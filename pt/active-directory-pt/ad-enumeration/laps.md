# LAPS

The "**Local Administrator Password Solution**" (**LAPS**) provides management of local account passwords of domain joined computers. Passwords are stored in Active Directory (AD) and protected by ACL, so only eligible users can read it or request its reset.

## Enumeration

### AdmPwd.PS

{% embed url="https://github.com/ztrhgf/LAPS/tree/master/AdmPwd.PS" %}

```powershell
# Import module
Import-Module AdmPwd.PS

# Find the OUs that can read LAPS passwords
Find-AdmPwdExtendedRights -Identity <OU>

# Once we have compromised a user that can read LAPS
Get-AdmPwdPassword -ComputerName <Hostname>
```





### Metasploit

```bash
use post/windows/gather/credentials/enum_laps
```







### &#x20; <a href="#enumeration" id="enumeration"></a>

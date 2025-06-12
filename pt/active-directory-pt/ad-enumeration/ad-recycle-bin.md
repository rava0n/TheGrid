# AD Recycle Bin

The **Active Directory (AD) Recycle Bin** is a feature that allows administrators to **recover deleted Active Directory objects**. It preserves deleted objects and their attributes for a period of time, typically 60 or 180 days. This feature is **not enabled by default** and **requires an irreversible action to enable**.



## Check if AD Recycle bin is enabled

{% code title="PowerView.ps1" %}
```powershell
. .\PowerView.ps1

Get-ADOptionalFeature -Filter * | ? {$_.Name -match "Recycle Bin"}
```
{% endcode %}



## Get ADObject from Recycle Bin with Powershell

```powershell
Get-ADObject -IncludeDeletedObjects -Filter {Isdeleted -eq $true}
```

{% code overflow="wrap" %}
```powershell
Get-ADObject -Filter "isDeleted -eq $true" -IncludeDeletedObjects -Property * | Format-List Name,ObjectGUID,Deleted,DistinguishedName
```
{% endcode %}

{% code title="PowerView.ps1" overflow="wrap" %}
```powershell
Get-DomainObject -SearchBase "CN=Recycle Bin Feature,CN=Optional Features,CN=Directory Service,CN=Windows NT,CN=Services,CN=Configuration,DC=tombwatcher,DC=htb" -Properties *
```
{% endcode %}



## Restoring Objects

```powershell
Restore-ADObject -Identity <ObjectGUID>
```


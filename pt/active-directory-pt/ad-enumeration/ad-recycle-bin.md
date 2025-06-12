# AD Recycle Bin

The **Active Directory (AD) Recycle Bin** is a feature that allows administrators to **recover deleted Active Directory objects**. It preserves deleted objects and their attributes for a period of time, typically 60 or 180 days. This feature is **not enabled by default** and **requires an irreversible action to enable**.



## Get ADObject from Recycle Bin with Powershell

{% code overflow="wrap" %}
```powershell
Get-ADObject -Filter "isDeleted -eq $true" -IncludeDeletedObjects -Property * | Format-List Name,ObjectGUID,Deleted,DistinguishedName
```
{% endcode %}



## Restoring Objects

```powershell
Restore-ADObject -Identity <ObjectGUID>
```

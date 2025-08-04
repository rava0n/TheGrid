# Guide

## Rule of Engagement

Read very well the RoE for this section



## Workstation part

### Initial Access

**Port scanning on the workstation will reveal the initial access path you will be using**, no rabbit hole here and pretty straightforward.



### PrivEsc

{% content-ref url="../../../pt/executive-pt/post-exploitation/windows-post-exploitation/windows-privilege-escalation/" %}
[windows-privilege-escalation](../../../pt/executive-pt/post-exploitation/windows-post-exploitation/windows-privilege-escalation/)
{% endcontent-ref %}

Once we are Local Admin we have to dump all credentials with mimikatz or impacket

## Pivoting

Ligolo-ng pivoting



## Domain Controller

To find a way to get into the domain controller, try to find the valid domain user first but do not use `net user /dom`, I have warned you.



Once we have a domain user:

* Try AS-REP roasting or if it can authenticate to the domain controller then try Kerberoasting attack with`nxc ldap $DC_IP -u 'validuser' -p 'valid_pass' --kerberoasting out.txt` or `impacket-GetNPUsers`
* fire up your bloodhound and use `bloodhound-python` to collect domain information.


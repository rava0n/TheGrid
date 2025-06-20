# Step-by-Step Windows Machine

## Without Credential

* SMB anonymous login
* LDAP anonymous informations
* Web Page&#x20;



## With Credentials

Enumeration:

* Enum4Linux&#x20;

```bash
enum4linux-ng -u USER -p PASS IP
```

* SMB Shares
* Bloodhound ( or Find **Kerberostable**, **AS-Roastable** accounts)
  * Check the information from "Node Info" of initial user.
* Impacket-secretsdump
  * Pass-the-Hash / Pass-the-Password
* WinRM connection
  * evil-winrm
  * Create persistence shell&#x20;

{% content-ref url="executive-pt/post-exploitation/payload-development.md" %}
[payload-development.md](executive-pt/post-exploitation/payload-development.md)
{% endcontent-ref %}

* FTP connection
* SSH connection



## PrivEsc:

* WinPeas
* Privs (whoami /priv)
  * Access Token Impersonation
* Check All AD user (deleted, disable, active)
* Mimikatz&#x20;
* Certipy (more if there is a CA\_SVC user)



## Dump

* impacket-secretsdump
* Mimikatz

When we have a hash we can:

* Do Pass-The-Hash
* Use `Hash-Identify` to get the type and crack it



## Crack Hash

* Hashcat & John


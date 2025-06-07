# Windows Lateral Movement

## Remote Management Protocols - Auth RCE

Remote Code Execution for lateral movement involve running commands or code on a remote system, allowing attackets to move laterally across a network.

Methodologies:

* RDP
* WMI

{% content-ref url="../../active-directory-pt/ad-lateral-movement/wmi.md" %}
[wmi.md](../../active-directory-pt/ad-lateral-movement/wmi.md)
{% endcontent-ref %}

* PowerShell Remoting (Invoke-Command, Enter-PSSession)

{% content-ref url="../../active-directory-pt/ad-lateral-movement/pssession.md" %}
[pssession.md](../../active-directory-pt/ad-lateral-movement/pssession.md)
{% endcontent-ref %}

* PsExec and Similar Tools (PsExec, Impacket psexec.py, CrackMapExec)

{% content-ref url="../../../tools/tools/psexec.md" %}
[psexec.md](../../../tools/tools/psexec.md)
{% endcontent-ref %}

{% content-ref url="../../../tools/tools/crackmapexec.md" %}
[crackmapexec.md](../../../tools/tools/crackmapexec.md)
{% endcontent-ref %}



* SMBExec

{% content-ref url="../enumeration-exploitation/windows-enum-exploitation/port-445-smb.md" %}
[port-445-smb.md](../enumeration-exploitation/windows-enum-exploitation/port-445-smb.md)
{% endcontent-ref %}



## Credential-Based Lateral Movement Techniques

### Pass-the-Hash

{% content-ref url="../../active-directory-pt/ad-lateral-movement/pass-the-hash.md" %}
[pass-the-hash.md](../../active-directory-pt/ad-lateral-movement/pass-the-hash.md)
{% endcontent-ref %}

### Pass-the-Ticket

{% content-ref url="../../active-directory-pt/ad-lateral-movement/pass-the-ticket.md" %}
[pass-the-ticket.md](../../active-directory-pt/ad-lateral-movement/pass-the-ticket.md)
{% endcontent-ref %}

### Credential Reuse

{% content-ref url="../../active-directory-pt/ad-enumeration/password-sprayng.md" %}
[password-sprayng.md](../../active-directory-pt/ad-enumeration/password-sprayng.md)
{% endcontent-ref %}

### Golden/Silver Ticket

{% content-ref url="../../active-directory-pt/ad-persistence/silver-ticket.md" %}
[silver-ticket.md](../../active-directory-pt/ad-persistence/silver-ticket.md)
{% endcontent-ref %}

{% content-ref url="../../active-directory-pt/ad-persistence/golden-ticket.md" %}
[golden-ticket.md](../../active-directory-pt/ad-persistence/golden-ticket.md)
{% endcontent-ref %}



## Windows Remote Managements Protocols

### RDP

{% content-ref url="../enumeration-exploitation/windows-enum-exploitation/port-3389-rdp.md" %}
[port-3389-rdp.md](../enumeration-exploitation/windows-enum-exploitation/port-3389-rdp.md)
{% endcontent-ref %}

### WinRm

{% content-ref url="../enumeration-exploitation/windows-enum-exploitation/port-5985-5986-winrm.md" %}
[port-5985-5986-winrm.md](../enumeration-exploitation/windows-enum-exploitation/port-5985-5986-winrm.md)
{% endcontent-ref %}

### WMI

{% content-ref url="../../active-directory-pt/ad-lateral-movement/wmi.md" %}
[wmi.md](../../active-directory-pt/ad-lateral-movement/wmi.md)
{% endcontent-ref %}




























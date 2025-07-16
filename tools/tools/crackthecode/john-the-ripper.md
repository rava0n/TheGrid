# John the Ripper

John the Ripper is a fast **password cracker**, currently available for many flavors of Unix, macOS, Windows, DOS, BeOS, and OpenVMS (the latter requires a contributed patch).



## NTLM

```bash
john --format=NT file.txt --wordlist=/PATH/TO/rockyou.txt
```

## zip2john

zip2john processes input ZIP files into a format suitable for use with John the Ripper.

#### Usage

```bash
zip2john /PATH/TO/file.zip > zip.john
```

Ready to crack it with JtR

```bash
john zip.john -wordlist:/usr/share/wordlists/rockyou.txt
```



## pfx2john

**pfx2john** is a python2 script to excract password hash of a .pfx file.

```bash
python2 /usr/share/john/pfx2john.py file.pfx > pfx.john
```

{% hint style="info" %}
For people that use **Python3**, that we remove `b'` and `'` from the output of the Python script to ensure that John properly reads the hash.
{% endhint %}

Ready to crack it with JrR

```bash
john pfx.john -wordlist:/usr/share/wordlists/rockyou.txt
```



## keepass2john

Convert your KeePass databases (.kdbx) to John the Ripper's format effortlessly using this conversion tool.

```bash
keepass2jogn file.kdbx
```

If the file is saved with Keepass version ≥ 2.36, the tool above won't work. Use this script to extract the hash.

{% embed url="https://github.com/r3nt0n/keepass4brute" %}

{% code title="Installation" overflow="wrap" %}
```bash
sudo apt install keepassxc
wget https://raw.githubusercontent.com/r3nt0n/keepass4brute/refs/heads/master/keepass4brute.sh

chmod +x keepass4brute.sh
```
{% endcode %}

```bash
./keepass4brute.sh <kdbx-file> /usr/share/wordlists/rockyou.txt
```

# hashcat

**Hashcat** supports five unique modes of attack for over 300 highly-optimized hashing algorithms. hashcat currently supports CPUs, GPUs, and other hardware accelerators on Linux, and has facilities to help enable distributed password cracking.

```bash
hashcat -m 1800 hash.txt /usr/share/wordlists/rockyou.txt
```

## Hashcat example

To find the hashcat code for a specific hash type search in this link :

{% embed url="https://hashcat.net/wiki/doku.php?id=example_hashes" %}

## Crack NTLM

### NTLM

Copy the NTLM inside a file and crack it with hashcat.

```bash
# crack NTML
hashcat -a 3 -m 1000 file.txt /PATH/TO/rockyou.txt
```

### NTLMv2

Copy the NTLMv2 inside a file

{% code title="ntlmv2.txt" %}
```
p.agila::FLUFFY:aaaaaaaaaaaaaaaa:078fa02d18e0c3b3014e84bfdf2c8fcc:0101000000000000003dc95a51cfdb0175f0bd3186a398af00000000010010007200790075004500790075006d006300030010007200790075004500790075006d0063000200100044006100710050006e007400750071000400100044006100710050006e0074007500710007000800003dc95a51cfdb0106000400020000000800300030000000000000000100000000200000a201a7207c75e3c4363868b48dd83c90e51c555356e1790a59b3a1da25e0982f0a001000000000000000000000000000000000000900220063006900660073002f00310030002e00310030002e00310034002e003100330038000000000000000000
```
{% endcode %}

Try to crack the file with the correct hashcat code

```bash
hashcat -m 5600 ntlmv2.txt wordlist.txt
```



## Crack Gitea Password

Sometimes some products use a custom hash to stored their passwords. Hashcat has some script to make easy cracking hash.

Some of these there are in official github repo:

[https://github.com/hashcat/hashcat/tree/master/tools](https://github.com/hashcat/hashcat/tree/master/tools)

Other scripts there are not in the official repo., but we can search them on google.

### gitea2hashcat

{% embed url="https://gist.github.com/h4rithd/0c5da36a0274904cafb84871cf14e271" %}

This script extract user and password from database file, trasform them in hashcat format and print: **Username**, **Algorithm**, **iterations**, **salt** ans **password hash**.

```bash
python3 gitea2hashcat.py gitea.db
```

Crack with new format

```bash
hashcat -m 10900 hashs /usr/share/wordlists/rockyou.txt
```




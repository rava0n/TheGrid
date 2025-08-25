# Gophish Configuration

## Machine Creation

Create a Ubuntu Machine where we will install in the GoPhish service.

{% hint style="info" %}
It's raccomended to use a AWS EC2 server
{% endhint %}



## GoPhish Setup

{% embed url="https://github.com/gophish/gophish" %}

How to Setup:

* Update our server: `sudo apt update`
* Update our server: `sudo apt upgrade`
* Install Golang: `sudo apt install go`
* Clone Gohish: `git clone https://github.com/gophish/gophish.git`
* Change to GoPhish directory: `cd gophish/`
* Build gophish binary: `go build`

### Configuring Gophish as a System Service <a href="#lecture_heading" id="lecture_heading"></a>

To configure gophish as a System service follow these steps:

1. Edit the `config.json` file changing the `listen_url` field

```bash
nano config.json

"listen_url": "0.0.0.0:3333" # set all interfaces
```

2. Create a service file to run gophish as a system service

```bash
sudo nano /etc/systemd/system/gophish.service
```

Add the following lines

```
[Unit]

Description=gophish-service

[Service]

Type=simple

WorkingDirectory=/home/ubuntu/gophish/

ExecStart=/home/ubuntu/gophish/gophish

[Install]

WantedBy=multi-user.target
```

Enable the gophish service:

```bash
sudo systemctl enable gophish.service
```

3. Start the gophish service

```bash
sudo systemctl start gophish.service
```

Check the status of the service

```bash
sudo systemctl status gophish.service
```



4. Get the default password in the gophish logs running this command

```bash
sudo ./gophish
```

Copy the admin default password



5. Limit the 3333 and 22 port only to our IP.
6. Login into the GoPhish Portal: `https://UBUNTU_IP_MACHINE:3333/` and reset password.



## Configuring TLS Certificates

### Create DNS A Record for landin page

For the landing page (default is port 80 of the gophish ubuntu server) we create a DNS A record to set that when a user go to the landin page with the Gophish server IP, he will be redirect to the our registrated phishing domain.

#### AWS Route 53

Create new record A and insert the Gophish public IP in the value field

<figure><img src="../../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>



### Create and assign the TLS certificate

Install `certbot` on our Ubuntu machine

```bash
sudo apt instasll certbot
```

And run this command to generate TLS certificate:

```bash
sudo certbot certonly -d <domain> --manual --preferred-challenges dns
```

Now we have to follow to instruction from certbot output creating new DNS TXT record for the domain with values given in the certbot output.

<figure><img src="../../../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>



Press Enter after the certbot command, and it give us the 2 path for the certificates.

**Copy certificates to the gophish directory**

```bash
sudo cp /etc/letsencrypt/live/mydomain.com/privkey.pem mydomain_privkey.pem
sudo cp /etc/letsencrypt/live/mydomain.com/fullchain.pem mydomain_fullchain.pem
```

**Edit the config.json in gophish directory to point to port 443**

```bash
nano /home/ubuntu/gophish/config.json
```

* Change the phishing server port from `80` to port `443`
* Change the privkey and fullchain to your newly copied certificates

```json
[..snip..]
"cert_path": "mycert_fullchain.pem"
"key_path": "mycert_privkey.pem"
[..snip..]
```

* Change `use_tls` to `true`



**Stop and Restart Gophish Service**

```bash
sudo systemctl stop gophish.service
sudo systemctl start gophish.service
```


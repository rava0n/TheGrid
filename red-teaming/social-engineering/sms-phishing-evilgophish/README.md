# SMS Phishing - Evilgophish

## Evilgophish Setup

{% embed url="https://github.com/fin3ss3g0d/evilgophish" %}

Combination of [evilginx3](https://github.com/kgretzky/evilginx2) and [GoPhish](https://github.com/gophish/gophish).

#### Installation

```bash
git clone https://github.com/fin3ss3g0d/evilgophish.git

cd evilgophish

chmod +x ./setup.sh
```



#### Start evilgophish

{% code overflow="wrap" %}
```bash
sudo ./setup.sh mydomain.com "subdomains(s)" false <redirectURL> true <ridReplacement> false
```
{% endcode %}



#### Configuration

**\*\* In a new SSH session execute: (fill in mydomain.com with your domain) or copy from the evilgophish output \*\***

{% code overflow="wrap" %}
```bash
sudo certbot certonly --manual --preferred-challenges=dns --email admin@mydomain.com --server https://acme-v02.api.letsencrypt.org/directory --agree-tos -d '*.mydomain.com' -d 'mydomain.com'
```
{% endcode %}

<figure><img src="../../../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

**Populate a new \_acme-challenge TXT record in AWS Route 53 with the record created by evilgophish. When add press Enter and copy the cert paths.**

<figure><img src="../../../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

**Continue the setup adding the path to the running evilgophish binary :**&#x20;

<figure><img src="../../../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>

Lastly restart the apache service

```bash
sudo systemctl restart apache2.service
```

## Admin Setup

Change the `listen_url` parameter on `config.json` file in the `evilgophish/gophish` directory from `127.0.0.1` to `0.0.0.0`&#x20;

```json
nano config.json

[..snip..]
"admin_server": {
    "listen_url" : "0.0.0.0:3333"
[..snip..]
```



**Edit the gophish.service config file:**

```bash
sudo nano /etc/systemd/system/gophish.service
```

Replace with this content:

{% code title="gophish.service" %}
```
File contents:

[Unit]

Description=gophish-service

[Service]

Type=simple

WorkingDirectory=/home/ubuntu/evilgophish/gophish/

ExecStart=/home/ubuntu/evilgophish/gophish/gophish

[Install]

WantedBy=multi-user.target
```
{% endcode %}

Reload the deamon and restart the gophish service

```bash
sudo systemctl daemon-reload

sudo systemctl stop gophish.service

sudo systemctl restart gophish.service

sudo systemctl status gophish.service
```

Grab the new username and password for the admin.

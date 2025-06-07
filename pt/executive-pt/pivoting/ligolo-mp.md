---
layout:
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# ligolo-mp

> Ligolo-mp is a **pivoting solution** that tunnels traffic using a TUN interface, enabling seamless access to the target network, as if you are directly connected to it. Is has both simple single-user environment and can also be a collaborative platform for the whole redteam. It allows multiple remote operators to handle unlimited concurrent pivots while automatically managing shared TUNs required for seamless networking. It also provides a dashboard that allows to assess the current networking status at a glance and helps onboarding new operators to the environment.



## Installation & Start

```bash
git clone https://github.com/ttpreport/ligolo-mp
cd ligolo-mp
sudo make install
```

Once we have installed the tool, we can run it:

```bash
sudo ligolo-mp
```

If we can start the service use the command bellow:

```bash
sudo systemctl start ligolo-mp.service
```



## Credentials Page

The page bellow is the first page named "Credentials". This page has all access to ligolo servers where you have access.&#x20;

<figure><img src="../../../.gitbook/assets/image (210).png" alt=""><figcaption></figcaption></figure>

### Add new Server Connection

Press `CTRL + N`:

<figure><img src="../../../.gitbook/assets/image (211).png" alt=""><figcaption></figcaption></figure>

Select the cred file and press `ENTER`. (How to create cred file --> [#create-creds-for-new-operator](ligolo-mp.md#create-creds-for-new-operator "mention"))

## Dashboard Page

Once we are connected to a server, we will see this screen:

<figure><img src="../../../.gitbook/assets/image (212).png" alt=""><figcaption></figcaption></figure>

### Craft the payload

Press `CTRL+N` to open the "Generate agent" window.

<figure><img src="../../../.gitbook/assets/image (216).png" alt=""><figcaption></figcaption></figure>

There are several options that you can tweak for your implant:

<table data-full-width="true"><thead><tr><th>Option</th><th>Description</th></tr></thead><tbody><tr><td>Save to</td><td>Full file path (incl. filename), where the implant will be stored</td></tr><tr><td>Server</td><td>A list (one per line) of servers in a format of <code>ip:port</code> which will be used in round-robin fashion to try to establish a session</td></tr><tr><td>Proxy</td><td>A proxy to be used for connection, in a format <code>protocol://host:port</code>. Supported protocols: HTTP, SOCKS5. If this option is configured, it will use only this proxy, ignoring any environment-based configuration the system might have</td></tr><tr><td>Ignore env proxy</td><td>This can be set to ignore system's environment-based proxy configuration and try to reach the server directly. This is only effective if you leave the proxy option empty</td></tr><tr><td>OS</td><td>Target OS for implant to run on</td></tr><tr><td>Arch</td><td>Target architecture for implant to run on</td></tr><tr><td>Obfuscate</td><td>This can be set to produce obfuscated binary. This uses <a href="https://github.com/moloch--/garble">Garble</a></td></tr></tbody></table>

\
Once we have created the payload, transfer it on the target machine, and execute it in base the target OS.&#x20;

When the binary will be executed, a session will appear in the Dashboard.

<figure><img src="../../../.gitbook/assets/image (207).png" alt=""><figcaption></figcaption></figure>

Then, Start the Relay to view all machine interfaces.

<figure><img src="../../../.gitbook/assets/image (202).png" alt=""><figcaption></figcaption></figure>

### Create a Route

If machine with active session has 2 or more interface we can create a route to reach the another network.

To create it, we have to select the session and then press `ENTER`. Once the windows will appear select the "Add route" section.

<figure><img src="../../../.gitbook/assets/image (208).png" alt=""><figcaption></figcaption></figure>

Then, insert the new network of other interface and press SUBMIT button

<figure><img src="../../../.gitbook/assets/image (209).png" alt=""><figcaption></figcaption></figure>

Now the new network is reachable.

> To verify if the routes has been created write the command `routel` into our terminal.

### Create a Redirector

TODO:



## Admin Panel



### Create Cred for New Operator

Press `CTRL+N` to open the window

<figure><img src="../../../.gitbook/assets/image (213).png" alt=""><figcaption></figcaption></figure>

Insert the _Name_, _Server_ with _IP:PORT_ of ligolo-mp Server, and chose if the operator will be a admin.

To save the connection file, press ENTER on the new operator and Select the "EXPORT".

<figure><img src="../../../.gitbook/assets/image (214).png" alt=""><figcaption></figcaption></figure>

Then write the path where the file will saves.

<figure><img src="../../../.gitbook/assets/image (215).png" alt=""><figcaption></figcaption></figure>

## Official wiki




# Starter Kit

## Reverse Shell/ Payload

{% code title="Nishang" %}
```bash
# folder with shell and payload for PowerShell shell
sudo apt install nishang
```
{% endcode %}

{% code title="Penelope" %}
```bash
# Tool for simplify revshell management
pipx install git+https://github.com/brightio/penelope 
```
{% endcode %}

## Wordlists

<pre class="language-bash"><code class="lang-bash"><strong>cd /usr/share/wordlists
</strong><strong>git clone https://github.com/danielmiessler/SecLists.git
</strong></code></pre>

## PrivEsc

{% code title="Peas Kit" %}
```bash
# tool for priv esc win/lin
sudo apt install peass
```
{% endcode %}

## AD Tools

<pre class="language-bash" data-title="SharpCollection"><code class="lang-bash"># Folder with a lot of executable uses in AD enumeration
cd /opt &#x26;&#x26; sudo git clone https://github.com/Flangvik/SharpCollection
<strong>sudo chown $USER:$USER SharpCollection
</strong></code></pre>






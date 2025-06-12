# LDAP

The **Lightweight Directory Access Protocol (LDAP)** is an open, industry-standard application protocol used to access and manage directory information over a network. A directory in this context is a specialized database optimized for reading rather than writing, commonly used to store structured information like user accounts, groups, permissions, and network resources.

During a penetration test, LDAP can serve as a valuable target for enumeration due to its role in centralizing sensitive information within an organization. If improperly secured or misconfigured, LDAP servers may allow unauthorized users to query the directory, exposing a wealth of information that can assist in further exploitation.





## Techniques

### Bypass TLS SNI check

Put in _/etc/hosts_ file, the line "IP\_SRV    company.com". The company.com is a random domain to bypass the TLS SNI check of the LDAP server. This way we can get all publicly avaible data from LDAP server if the anonymoud Binds is enabled.

```bash
sudo echo "IP    company.com" >> /etc/hosts

ldapsearch -H ldaps://company.com:389/ -x -s base -b '' "(objectClass=*)" "*" +
```

### Anonymous binds enumeration

Test if anonymous access to the LDAP directory is allowed.

{% code title="ldapsearch" %}
```bash
ldapsearch -x -H ldap://company.com
ldapsearch -x -H ldap://company.com -s base namingcontexts
```
{% endcode %}

{% code title="NMAP" %}
```bash
nmap -p 389 --script ldap-search $DC_IP
```
{% endcode %}

Anonymous bind could be limited. To check what we can do, look this params:

```bash
ldapsearch -x -H ldap://company.com -b "" -s base "(objectClass=*)"
```

{% code title="Command output" overflow="wrap" %}
```
namingContexts: DC=company,DC=com
namingContexts: CN=Configuration,DC=company,DC=com
namingContexts: CN=Schema,CN=Configuration,DC=company,DC=com
namingContexts: DC=DomainDnsZones,DC=company,DC=com

```
{% endcode %}

### General Enumeration

Get general data from LDAP service without and within credentials

{% code title="ldapsearch" %}
```bash
# get domain name
ldapsearch -H ldap://company.com -x -s base namingcontexts
ldapsearch -H $DC_IP -D '$USER' -w '$PASSWORD' -s base namingcontexts

# enumerate from domain
ldapsearch -H ldap://company.com -x -b "DC=htb,DC=local"
ldapsearch -H $DC_IP -D '$USER' -w '$PASSWORD' -b "DC=htb"

```
{% endcode %}

<pre class="language-bash" data-title="windapsearch"><code class="lang-bash"><strong>./windapsearch.py -d $DOMAIN --dc-ip $DC_IP 
</strong></code></pre>



{% code title="ldapdomaindump" overflow="wrap" %}
```bash
sudo ldapdomaindump ldaps://DC_IP -u 'DOMAIN\USER' -p 'PASS' -o DIRECTORY
# this command will creates some files with domain informations:
# domain_users: a file with domain users info
# domain_policy: a file with domain policies info
# domain_computers: a file with domain computers info
# domain_groups: a file with domain groups info
# domain_trusts: a file with domain trusts info
```
{% endcode %}



### Kerberostable Users

Identify user accounts with SPN (Service Principal Name) configured, exploitable via Kerberoasting.

{% code title="Impacket's GetNPUsers.py" %}
```bash
./GetNPUUsers.py $DOMAIN/ -dc-ip $DC_IP 
```
{% endcode %}

{% code title="ldapsearch" overflow="wrap" %}
```bash
ldapsearch -H ldap://$DC_IP -D "user@domain.com" -w $PASSWORD -b "dc=domain,dc=com" "(servicePrincipalName=*)"
```
{% endcode %}

All the user in the output may be vulnerable to **AS-REP Reasting**.

{% content-ref url="../ad-post-compromise-attacks/as-rep-roasting.md" %}
[as-rep-roasting.md](../ad-post-compromise-attacks/as-rep-roasting.md)
{% endcontent-ref %}



### Get Objs from AD Recycle Bin

Using this commands we can get (if is enable) the deleted obj from AD recycle bin.



{% code title="ldapsearch" overflow="wrap" %}
```bash
ldapsearch -x -H ldap://yourdomaincontroller.example.com -b 'CN=Deleted Objects,DC=yourdomain,DC=com' -s sub '(objectClass=user)(name=YourUserName)' distinguishedName -D '$USER' -w '$PASSWORD'
```
{% endcode %}



### Group Membership Enumeration

Identify group memberships and privileged users.

<pre class="language-bash" data-title="ldapsearch" data-overflow="wrap"><code class="lang-bash">ldapsearch -H ldap://$DC_IP -x -b "dc=domain,dc=com" "(objectClass=group)"
<strong>ldapsearch -H ldap://$DC_IP -D "user@domain.com" -w $PASSWORD -b "dc=domain,dc=com" "(objectClass=group)"
</strong></code></pre>

{% code title="windapsearch" %}
```bash
python3 windapsearch.py --dc-ip $DC_IP -u user@domain.local -p password --groups
```
{% endcode %}

{% code title="ldapdomaindump" %}
```bash
ldapdomaindump -u user@domain.com -p password -d domain.com
```
{% endcode %}

### Password Policy Enumeration

Extract domain password policies and account lockout thresholds.

{% code title="ldapsearch" overflow="wrap" %}
```bash
ldapsearch -H ldap://$DC_IP -D "user@domain.com" -w password -b "dc=domain,dc=com" "(objectClass=domain)"
```
{% endcode %}

### User Enumeration

Enumerate domain users and their attributes without and within credentials.

{% code title="Impacket's script" %}
```bash
python GetADUsers.py domain.com/user:<password>
```
{% endcode %}

{% code title="ldapsearch" overflow="wrap" %}
```bash
ldapsearch -x -H ldap://company.com -b "CN=Users,DC=<1_SUBDOMAIN>,DC=<TLD>"

ldapsearch -H ldap://<DC_IP> -D "user@domain.com" -w <password> -b "dc=domain,dc=com" "(objectClass=user)" | grep "sAMAccountName: " | awk -F'sAMAccountName: ' '{print $2}' > users.txt
```
{% endcode %}

{% code title="windapsearch" %}
```bash
python3 windapsearch.py --dc-ip $IP_SRV -u $USR@$DOMAIN_NAME.local -p $PSWD --da
python3 windapsearch.py -d $DOMAIN.local --dc-ip $IP -U
```
{% endcode %}

### Computer Enumeration

Identify computers and their roles in the domain.

{% code title="ldapsearch" overflow="wrap" %}
```bash
ldapsearch -x -H ldap://company.com -b "CN=Computers,DC=<1_SUBDOMAIN>,DC=<TLD>"
ldapsearch -H ldap://<DC_IP> -D "user@domain.com" -w <password> -b "dc=domain,dc=com" "(objectClass=computer)"
```
{% endcode %}

{% code title="windapsearch" overflow="wrap" %}
```bash
python3 windapsearch.py --dc-ip 10.10.10.10 -u john@domain.local -p password --computers
```
{% endcode %}

### GPO Enumeration

Enumerate Group Policy Objects (GPOs) applied to users and systems.

{% code title="ldapsearch" overflow="wrap" %}
```bash
ldapsearch -H ldap://<DC_IP> -D "user@domain.com" -w <password> -b "CN=Policies,CN=System,DC=domain,DC=com"
```
{% endcode %}

### Another Enumerations

Extract **my info**:

{% code title="ldapsearch" overflow="wrap" %}
```bash
ldapsearch -x -H ldap://company.com -b "CN=<MY NAME>,CN=Users,DC=<1_SUBDOMAIN>,DC=<TLD>"
ldapsearch -H ldap://company.com -b "CN=<MY NAME>,CN=Users,DC=<1_SUBDOMAIN>,DC=<TLD>" -D "user@domain.com" -w <password>
```
{% endcode %}

Extract **Domain Admins**:

{% code title="ldapsearch" overflow="wrap" %}
```bash
ldapsearch -x -H ldap://company.com -b "CN=Domain Admins,CN=Users,DC=<1_SUBDOMAIN>,DC=<TLD>"
ldapsearch -H ldap://company.com -b "CN=Domain Admins,CN=Users,DC=<1_SUBDOMAIN>,DC=<TLD>" -D "user@domain.com" -w <password>
```
{% endcode %}

{% code title="windapsearch" overflow="wrap" %}
```bash
python3 windapsearch.py --dc-ip 10.10.10.10 -u john@domain.local -p password --da
```
{% endcode %}

Extract **Domain Users**:

{% code title="ldapsearch" overflow="wrap" %}
```bash
ldapsearch -x -H ldap://company.com -b "CN=Domain Users,CN=Users,DC=<1_SUBDOMAIN>,DC=<TLD>"
ldapsearch -H ldap://company.com -b "CN=Domain Users,CN=Users,DC=<1_SUBDOMAIN>,DC=<TLD>" -D "user@domain.com" -w <password>
```
{% endcode %}

Extract **Enterprise Admins**:

{% code title="ldapsearch" overflow="wrap" %}
```bash
ldapsearch -x -H ldap://company.com -b "CN=Enterprise Admins,CN=Users,DC=<1_SUBDOMAIN>,DC=<TLD>"
ldapsearch -H ldap://company.com -b "CN=Enterprise Admins,CN=Users,DC=<1_SUBDOMAIN>,DC=<TLD>" -D "user@domain.com" -w <password>
```
{% endcode %}

Extract **Administrators**:

{% code title="ldapsearch" overflow="wrap" %}
```bash
ldapsearch -x -H ldap://company.com -b "CN=Administrators,CN=Builtin,DC=<1_SUBDOMAIN>,DC=<TLD>"
ldapsearch -H ldap://company.com -b "CN=Administrators,CN=Builtin,DC=<1_SUBDOMAIN>,DC=<TLD>" -D "user@domain.com" -w <password>
```
{% endcode %}

Extract **Privilege Users**:

{% code title="windapsearch" overflow="wrap" %}
```bash
python3 windapsearch.py --dc-ip 10.10.10.10 -u john@domain.local -p password --privileged-users
```
{% endcode %}

Extract **Remote Desktop Group**:

{% code title="ldapsearch" overflow="wrap" %}
```bash
ldapsearch -x -H ldap://company.com -b "CN=Remote Desktop Users,CN=Builtin,DC=<1_SUBDOMAIN>,DC=<TLD>"
ldapsearch -x -H ldap://company.com -b "CN=Remote Desktop Users,CN=Builtin,DC=<1_SUBDOMAIN>,DC=<TLD>" -D "user@domain.com" -w <password>
```
{% endcode %}



## Tools

### ldapsearch

Pre-installed in kali linux. Allow us to enumerate a Server with LDAP service open.

```bash
man ldapsearch # information about tool
sudo apt install ldap-utils # to install in kali
```

### windapsearch

&#x20;[Windapsearch](https://github.com/ropnop/windapsearch) is a Python script useful to **enumerate users, groups, and computers from a Windows** domain by utilizing LDAP queries.

{% embed url="https://github.com/ropnop/windapsearch" %}

```bash
git clone https://github.com/ropnop/windapsearch.git && windapsearch
sudo apt-get install python3-ldap
./windapsearch.py ...
```

### ldapdomaindump

This package contains an Active Directory information dumper via LDAP.

{% code title="Installation" %}
```bash
sudo apt install python3-ldapdomaindump
```
{% endcode %}

{% embed url="https://github.com/dirkjanm/ldapdomaindump" %}

### NMAP

The NMAP tools has multiple script to identify LDAP services and perform scripted enumerations.

```bash
ls /usr/share/nmap/scripts | grep ldap # list of enable script
nmap --script-help=$SCRIPT_NAME # script info
```

### Impacket's scripts

Impacket is a collection of Python classes for working with network protocols.

{% embed url="https://github.com/fortra/impacket" %}

```bash
# kali default location
cd /usr/share/doc/python3-impacket/examples
```

Following the installations of scripts used for LDAP enumeration:

#### GetNPUsers.py

{% embed url="https://github.com/fortra/impacket/blob/master/examples/GetNPUsers.py" %}

```bash
cp /usr/share/doc/python3-impacket/examples/GetNPUsers.py .
chmod +x GetNPUsers.py
./GetNPUsers.py ...
```




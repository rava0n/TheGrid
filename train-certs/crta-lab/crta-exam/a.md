---
hidden: true
---

# A

## To add in wiki

```bash
#  find db in linux machine 
ps aux
ps aux | grep db
ps aux | grep 
```

```bash
# find other IP into log files
cd /var/log
grep -rEo '([0-9]{1,3}\.){3}[0-9]{1,3}' . | sort | uniq

# Check if the user connected out to another machine:
cat ~/.ssh/known_hosts
cat ~/.ssh/config
cat ~/.bash_history | grep ssh

# This might reveal other machines the current host connects to:
netstat -tunp
ss -tunap

# Applications might be configured to connect to internal servers:
grep -rEo '([0-9]{1,3}\.){3}[0-9]{1,3}' /etc/*


```



<figure><img src="../../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

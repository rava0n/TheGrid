# Kerbrute

Kerberos is a key authentication service within Active Directory. With this port open, we can use a tool called [Kerbrute](https://github.com/ropnop/kerbrute/releases) to brute force discovery of users, passwords and even password spray!

Unlike traditional brute force attacks that flood login portals and generate excessive failed login attempts, Kerbrute operates directly against the Kerberos service (KDC), often bypassing basic account lockout policies and avoiding detection by traditional security controls.





## Installation

Download pre-compiled binaries for Linux from this Link.

{% embed url="https://github.com/ropnop/kerbrute/releases" %}

```bash
# change mod
chmod +x kerbrute_linux_amd64
```



## Using kebrute

### UserEnum

```bash
./kerbrute_linux_amd64 userenum -d DOMAIN_NAME --dc DC_IP USERLIST.txt
```



### UserCheck

Verify a list of possible AD user into domain.

```bash
./kerbrute_linux_amd64 userenum users.txt --domain DOMAIN.local --dc <IP>
```


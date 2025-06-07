# PsExec

**PsExec** is a tool that using valid credentials to execute remotely code on other system **via Server Message Block (SMB).**&#x20;



## How PsExec works

### 1)  Connection Over SMB

* PsExec establishes a connection to a remote system using Server Message Block (SMB).
* It typically requires credentials for authentication, either as plaintext passwords or NTLM hashes.

### 2)  Named Pipe

* PsExec creates a named pipe on remote system to facilitate communication between the local PsExec client and the remote service.

### 3)  Temporary Service

* To execute commands on the remote system, PsExec creates a temporary Windows service.
* This service runs with elevated privileges, allowing it to execute commands or scripts as a system administrator.

### 4)  Execution and Cleanup

* Once the command or script is executed, PsExec cleans up by removing the temporary service. However, traces like logs or artifacts may remain, providing a trail of activity.



## Permissions & Privileges Requireds

A user to execute PsExec must have following permissions:

* Create and Start a service on the remote system.
* Access the IPC$ share, which is used to establish the SMB connection
* Read and write to certain directories or system areas.
* Local Users and Domain Users (In domain env, domain user with privileges may have broader access o remote systems)



## Impacket PsExec

This script in Impacket mimics the funtionality of PsExec, allowing remote execution of commands on Windows systems.&#x20;

{% embed url="https://github.com/fortra/impacket/blob/master/examples/psexec.py" %}

## Metasploit Module

```bash
use exploit/windows/smb/psexec

set RHOSTS IP
set SMBDomain DOMAIN.COM
set SMBUser USER
set SMBPass Pass/NTLM hash

exploit
```


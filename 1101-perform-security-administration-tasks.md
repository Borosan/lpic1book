# 110.1. Perform security administration tasks

## **110.1 Perform security administration tasks**

**Weight:** 3

**Description:** Candidates should know how to review system configuration to ensure host security in accordance with local security policies.

**Key Knowledge Areas:**

* Audit a system to find files with the suid/sgid bit set
* Set or change user passwords and password aging information
* Being able to use nmap and netstat to discover open ports on a system
* Set up limits on user logins, processes and memory usage
* Determine which users have logged in to the system or are currently logged in
* Basic sudo configuration and usage

**Terms and Utilities:**

* find
* passwd
* fuser
* lsof
* nmap
* chage
* netstat
* sudo
* /etc/sudoers
* su
* usermod
* ulimit
* who, w, last

In this lesson we just take a look at basic security audits. First we review several commands we have learned from the  security perspective and then get introduced to some other new commands.

### find suid/guid 

We have learned about suid/guid  when we talked about managing file permissions and owner ship, as a quick review see table bellow:

| access mode |  **on file** | **on directory** |
| :--- | :--- | :--- |
| **SUID** | executes with permissions of file owner | nothing |
| **GUID** | executes with the permissions of group | new files have group membership of directory |
| **Sticky Bit** | nothing | only owner can delete files |

There are some  security concern while using suid/guid  such as, what will happen if a destructive program has suid/guid permission set on it?  Why should dangerous programs such as rm has suid permission? To search for all suid/guid files we use find command:

> sudo find / -perm -u+s
>
> sudo find / -perm -g+s

```text
root@ubuntu16-1:~# find / -perm -u+s
/bin/ping
/bin/fusermount
/bin/ping6
/bin/mount
/bin/su
/bin/ntfs-3g
/bin/umount
find: ‘/run/user/1001/gvfs’: Permission denied
/usr/bin/chsh
/usr/bin/passwd
/usr/bin/gpasswd
/usr/bin/pkexec
/usr/bin/newgrp
/usr/bin/sudo
/usr/bin/chfn
/usr/lib/x86_64-linux-gnu/oxide-qt/chrome-sandbox
/usr/lib/snapd/snap-confine
/usr/lib/policykit-1/polkit-agent-helper-1
/usr/lib/eject/dmcrypt-get-device
/usr/lib/vmware-tools/bin64/vmware-user-suid-wrapper
/usr/lib/vmware-tools/bin32/vmware-user-suid-wrapper
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/openssh/ssh-keysign
/usr/lib/xorg/Xorg.wrap
/usr/sbin/pppd
...

root@ubuntu16-1:~# find / -perm -g+s
find: ‘/run/user/1001/gvfs’: Permission denied
/run/log/journal
/run/log/journal/b4f9fc6cf1ca4724b56e6e4235c77155
/usr/bin/wall
/usr/bin/crontab
/usr/bin/bsd-write
/usr/bin/chage
/usr/bin/expiry
/usr/bin/ssh-agent
/usr/bin/mlocate
/usr/share/ppd/custom
...
```

obviously going to each of these files and finding out what they do is beyond the scope of this course, but we should keep our eyes open to find if any of these don't make sense, like thing might be find in home directory of users.

#### looking for open ports

It is important to verify which ports are listening on the server’s network interfaces. Below are the different categories of ports:

1. **0-1023** – the Well Known Ports, also referred to as System Ports.
2. **1024-49151** – the Registered Ports, also known as User Ports.
3. **49152-65535** – the Dynamic Ports, also referred to as the Private Ports.

 We need to pay attention to open ports to detect an intrusion. Apart from an intrusion, for troubleshooting purposes, it may be necessary to check if a port is already in use by a different application on our servers. For example, we may install Apache and Nginx server on the same system!

 This section provides steps to use the netstat, lsof and nmap command to check the ports in use and view the application that is utilizing the port.

### netstat

 One of **netstat** command line tool usage is  for monitoring network  incoming and outgoing connections. By default, netstat displays a list of open sockets which is not very usefull so we usually use it along with `-tuna` switches.

| netstat switch | usage |
| :--- | :--- |
| -t | show tcp ports |
| -u  | show udp ports |
| -n | Show numerical addresses instead of trying to determine symbolic host, port or user names |
| -a | Show both listening and non-listening \(for TCP this means established connections\) |

```text
root@ubuntu16-1:~# netstat -tuna
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State      
tcp        0      0 127.0.1.1:53            0.0.0.0:*               LISTEN     
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN     
tcp        0      0 127.0.0.1:631           0.0.0.0:*               LISTEN     
tcp        0      0 0.0.0.0:445             0.0.0.0:*               LISTEN     
tcp        0      0 127.0.0.1:3306          0.0.0.0:*               LISTEN     
tcp        0      0 0.0.0.0:139             0.0.0.0:*               LISTEN     
tcp        0      0 192.168.52.144:41270    172.217.0.227:80        ESTABLISHED
tcp        0      0 192.168.52.144:43418    72.21.91.29:80          TIME_WAIT  
tcp        0      0 192.168.52.144:43416    72.21.91.29:80          TIME_WAIT  
tcp        0      0 127.0.0.1:631           127.0.0.1:52720         ESTABLISHED
...
udp        0      0 0.0.0.0:45821           0.0.0.0:*                          
udp    14400      0 0.0.0.0:51238           0.0.0.0:*                          
udp    11520      0 127.0.1.1:53            0.0.0.0:*                          
udp    10880      0 0.0.0.0:68              0.0.0.0:*                          
udp        0      0 0.0.0.0:631             0.0.0.0:*                          
udp    24576      0 192.168.52.255:137      0.0.0.0:*                          
udp        0      0 192.168.52.144:137      0.0.0.0:*                          
udp    52224      0 0.0.0.0:137             0.0.0.0:*                          
udp    34560      0 192.168.52.255:138      0.0.0.0:*                          
udp        0      0 192.168.52.144:138      0.0.0.0:*                          
udp     7680      0 0.0.0.0:138             0.0.0.0:*                          
udp     6144      0 0.0.0.0:5353            0.0.0.0:*                          
...
```

 Before a TCP connection can be opened, we need to have a **server** with a listener. The listener will listen on incoming connections on a specific port. This state is represented as `LISTEN`.  If everything worked properly,  the connection is marked as `ESTABLISHED` on both end-point.  In these tables `0.0.0.0` dictates _any address_ or _any interface_.

### lsof

 **lsof** meaning **‘LiSt Open Files’** is used to find out which files are open by which process. As we know that in Linux everything is a file, so we can even check the files that are opened by some network connections in the system using lsof command with -i switch, -i list all network connections:

```text
root@ubuntu16-1:~# lsof -i
COMMAND     PID   USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
avahi-dae   791  avahi   12u  IPv4  24139      0t0  UDP *:mdns 
avahi-dae   791  avahi   13u  IPv6  24140      0t0  UDP *:mdns 
avahi-dae   791  avahi   14u  IPv4  24141      0t0  UDP *:45821 
avahi-dae   791  avahi   15u  IPv6  24142      0t0  UDP *:57596 
mysqld      967  mysql   27u  IPv4  27331      0t0  TCP localhost:mysql (LISTEN)
sshd        979   root    3u  IPv4 468063      0t0  TCP *:ssh (LISTEN)
sshd        979   root    4u  IPv6 468070      0t0  TCP *:ssh (LISTEN)
nmbd       1745   root   16u  IPv4  31466      0t0  UDP *:netbios-ns 
nmbd       1745   root   17u  IPv4  31467      0t0  UDP *:netbios-dgm 
nmbd       1745   root   21u  IPv4 468314      0t0  UDP 192.168.52.136:netbios-ns 
nmbd       1745   root   24u  IPv4 468315      0t0  UDP 192.168.52.255:netbios-ns 
nmbd       1745   root   25u  IPv4 468316      0t0  UDP 192.168.52.136:netbios-dgm 
nmbd       1745   root   26u  IPv4 468317      0t0  UDP 192.168.52.255:netbios-dgm 
smbd       1761   root   34u  IPv6  31629      0t0  TCP *:microsoft-ds (LISTEN)
smbd       1761   root   35u  IPv6  31630      0t0  TCP *:netbios-ssn (LISTEN)
smbd       1761   root   36u  IPv4  31631      0t0  TCP *:microsoft-ds (LISTEN)
smbd       1761   root   37u  IPv4  31632      0t0  TCP *:netbios-ssn (LISTEN)
cupsd      3683   root   10u  IPv6  41942      0t0  TCP ip6-localhost:ipp (LISTEN)
cupsd      3683   root   11u  IPv4  41943      0t0  TCP localhost:ipp (LISTEN)
cups-brow  3685   root    8u  IPv4  41958      0t0  UDP *:ipp 
gvfsd-smb 14071  user1   13u  IPv4 465267      0t0  TCP 192.168.52.144:60122->192.168.52.144:netbios-ssn (ESTABLISHED)
gvfsd-smb 14071  user1   14u  IPv4 465449      0t0  TCP 192.168.52.144:60124->192.168.52.144:netbios-ssn (ESTABLISHED)
dnsmasq   14148 nobody    4u  IPv4 466564      0t0  UDP ubuntu:domain 
dnsmasq   14148 nobody    5u  IPv4 466565      0t0  TCP ubuntu:domain (LISTEN)
dnsmasq   14148 nobody   11u  IPv4 466600      0t0  UDP *:44999 
dhclient  14166   root    6u  IPv4 466685      0t0  UDP *:bootpc 
```

this command shows the command, PID, user running it and source and destination IP and tells of if this is a LISTENING or STABLISHED connection.

| lsof switch | usage |
| :--- | :--- |
| -iTCP _or_ -iUDP | just show TCP or UDP Connections |
| -i 4 _or_ -i 6 | you can have IPv4 and IPv6 files displayed separately |
| -n  | Do not use DNS name |
| -P | do not convert port numbers to port names |

 If we want  to check which process is using specific port , we can grep the output of any above commands or simply use the `fuser` command.

### fuser

The fuser command is a very smart utility used to find which process is using a file, a directory or a socket. 

The following command creates a tcp listener on port 8080:

```text
root@ubuntu16-1:~# nc -l -p 8080

```

Since a tcp server is listening on port 8080, the fuser utility can be used to find the process which is using the server’s socket. The `-v` option is used to put the fuser utility in verbose mode and the `-n` option is used to select the tcp protocol as a name space:

```text
root@ubuntu16-1:~# fuser -v -n tcp 8080
                     USER        PID ACCESS COMMAND
8080/tcp:            root      15663 F.... nc
```

By default, the fuser tool will look in both IPv6 and IPv4 sockets, but the default option can be changed with the -4 and -6 options. 

### nmap  

 The **Nmap** aka **Network Mapper** is an open source and a very versatile tool for Linux system/network administrators. **Nmap** is used for **exploring networks**, **perform security scans**, **network audit** and **finding open ports** on local or  remote machine.

```text
root@ubuntu16-1:~# nmap localhost

Starting Nmap 7.01 ( https://nmap.org ) at 2020-03-18 00:47 +0330
Nmap scan report for localhost (127.0.0.1)
Host is up (0.000025s latency).
Not shown: 995 closed ports
PORT     STATE SERVICE
22/tcp   open  ssh
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
631/tcp  open  ipp
3306/tcp open  mysql

Nmap done: 1 IP address (1 host up) scanned in 1.62 seconds
```

By **default**, **Nmap** scans the most common 1,000 ports for each protocol.

examine sudo configuration 

.

.

.

[https://www.cyberciti.biz/faq/unix-linux-check-if-port-is-in-use-command/](https://www.cyberciti.biz/faq/unix-linux-check-if-port-is-in-use-command/)

[https://www.tecmint.com/find-open-ports-in-linux/](https://www.tecmint.com/find-open-ports-in-linux/)

[https://blog.confirm.ch/tcp-connection-states/](https://blog.confirm.ch/tcp-connection-states/) \(tcp 3-way handshake\)

[https://jadi.gitbooks.io/lpic1/content/1101\_perform\_security\_administration\_tasks.html](https://jadi.gitbooks.io/lpic1/content/1101_perform_security_administration_tasks.html)

[https://www.geeksforgeeks.org/lsof-command-in-linux-with-examples/](https://www.geeksforgeeks.org/lsof-command-in-linux-with-examples/)

[https://www.cyberciti.biz/faq/how-to-check-open-ports-in-linux-using-the-cli/](https://www.cyberciti.biz/faq/how-to-check-open-ports-in-linux-using-the-cli/)

[https://linux.die.net/man/8/lsof](https://linux.die.net/man/8/lsof)

[https://www.digitalocean.com/community/tutorials/how-to-use-the-linux-fuser-command](https://www.digitalocean.com/community/tutorials/how-to-use-the-linux-fuser-command)

[https://www.tecmint.com/nmap-command-examples/](https://www.tecmint.com/nmap-command-examples/)

.


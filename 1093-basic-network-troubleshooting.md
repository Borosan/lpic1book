# 109.3. Basic network troubleshooting

**Weight:** 4

Description: Candidates should be able to troubleshoot networking issues on client hosts.

**Key Knowledge Areas:**

* Manually and automatically configure network interfaces and routing tables to include adding, starting, stopping, restarting, deleting or reconfiguring network interfaces
* Change, view, or configure the routing table and correct an improperly set default route manually
* Debug problems associated with the network configuration

**Terms and Utilities:**

* ifconfig
* ip
* ifup
* ifdown
* route
* host
* hostname
* dig
* netstat
* ping
* ping6
* traceroute
* traceroute6
* tracepath
* tracepath6
* netcat

Till now we have learned about fundamentals of internet protocols and we have get familiar with some of network configuration files and utilities. The truth is that some times things doesn't work as we expected and need troubleshooting. In this section  we try to show some steps to resolve the problem, additionally some new commands will be introduced.

### ifconfig & ip (interface or ip address problems)

The first command we have learned is ifconfig . Some times there might be an inactive interface which doesn't appear in results:

```
root@ubuntu16-1:~# ifconfig
lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:6 errors:0 dropped:0 overruns:0 frame:0
          TX packets:6 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:318 (318.0 B)  TX bytes:318 (318.0 B)
```

use -a with ifconfig or ip command instead:

```
root@ubuntu16-1:~# ifconfig -a
ens33     Link encap:Ethernet  HWaddr 00:0c:29:e2:1b:3e  
          BROADCAST MULTICAST  MTU:1500  Metric:1
          RX packets:44 errors:0 dropped:0 overruns:0 frame:0
          TX packets:113 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:5169 (5.1 KB)  TX bytes:12521 (12.5 KB)

lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:6 errors:0 dropped:0 overruns:0 frame:0
          TX packets:6 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:318 (318.0 B)  TX bytes:318 (318.0 B)

root@ubuntu16-1:~# ip a s
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: ens33: <BROADCAST,MULTICAST> mtu 1500 qdisc pfifo_fast state DOWN group default qlen 1000
    link/ether 00:0c:29:e2:1b:3e brd ff:ff:ff:ff:ff:ff

```

Bring up the interface with` ifup ens33` or` ifconfig ens33` up , and next check for your ip address.  

```
root@ubuntu16-1:~# ifup ens33
```

> You can check your ip address either from GUI or trough config files.If you are on Automatic ip assignment use`  dhclient -r  `and `dhclient` to release and renew your ip address.

### ping (detecting the problem)

ping is our best friend when we are troubleshooting network problems.

Check whether you can ping another computer in the same network? 

```
root@ubuntu16-1:~# ping 172.16.43.127 -c 2
PING 172.16.43.127 (172.16.43.127) 56(84) bytes of data.
64 bytes from 172.16.43.127: icmp_seq=1 ttl=64 time=0.748 ms
64 bytes from 172.16.43.127: icmp_seq=2 ttl=64 time=0.755 ms

--- 172.16.43.127 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1001ms
rtt min/avg/max/mdev = 0.748/0.751/0.755/0.027 ms
```

from a simple ping command we can determine whether the target is up and running or not. Also there might be a firewall in your network, which filters out ICMP packets, check for host firewall first and then hardware firewall if there are any. 

```
root@ubuntu16-1:~# ping 172.16.43.126 -c 2
PING 172.16.43.126 (172.16.43.126) 56(84) bytes of data.
From 172.16.43.135 icmp_seq=1 Destination Host Unreachable
From 172.16.43.135 icmp_seq=2 Destination Host Unreachable

--- 172.16.43.126 ping statistics ---
2 packets transmitted, 0 received, +2 errors, 100% packet loss, time 1007ms
pipe 2
```

Some times you might ping a wrong ip address or the server might have two interfaces or two ip adresses.

### ping6

Regular ping command only works with IPv4 address. Use ping6 command to send ICMPv6 ECHO_REQUEST packets to network hosts from a host or gateway.

### route (gateway and routing problems)

If you cant reach any network except computers you are in the same subnet with, you should doubt about you gateway.

```
root@ubuntu16-1:~# route
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
default         172.16.43.2     0.0.0.0         UG    0      0        0 ens33
link-local      *               255.255.0.0     U     1000   0        0 ens33
172.16.43.0     *               255.255.255.0   U     0      0        0 ens33
```

we can also use netstat -rn command to see current gateway:

```
root@ubuntu16-1:~# netstat -rn
Kernel IP routing table
Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
0.0.0.0         172.16.43.2     0.0.0.0         UG        0 0          0 ens33
169.254.0.0     0.0.0.0         255.255.0.0     U         0 0          0 ens33
172.16.43.0     0.0.0.0         255.255.255.0   U         0 0          0 ens33
```

If there were no default gate way, you should use `route add default gw x.x.x.x` to  add a default gateway. Next check the gate way, and make sure packets are going out from the right interface:

```
root@ubuntu16-1:~# ping -c 3 172.16.43.2
PING 172.16.43.2 (172.16.43.2) 56(84) bytes of data.
64 bytes from 172.16.43.2: icmp_seq=1 ttl=128 time=0.434 ms
64 bytes from 172.16.43.2: icmp_seq=2 ttl=128 time=0.379 ms
64 bytes from 172.16.43.2: icmp_seq=3 ttl=128 time=0.166 ms

--- 172.16.43.2 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2025ms
rtt min/avg/max/mdev = 0.166/0.326/0.434/0.116 ms
```

Every thing seems okey, but you cant reach specific ip address in another building. Hmm there might be routing problems in physical routers! how to check that?

### traceroute

The traceroute command maps the journey that a packet of information undertakes from its source to its destination.  This tool uses **ICMP** messages, but unlike _ping_, identifies every router in the path. **traceroute** is useful when troubleshooting network problems because it can help you to localize problems in network connectivity (you might need to install it `apt install traceroute`):

```
root@ubuntu16-1:~# traceroute google.com
traceroute to google.com (172.217.18.142), 30 hops max, 60 byte packets
 1  172.16.43.2 (172.16.43.2)  0.272 ms  0.329 ms  0.172 ms
 2  172.16.130.1 (172.16.130.1)  0.969 ms  1.183 ms  1.128 ms
 3  192.168.1.66 (192.168.1.66)  0.448 ms  0.574 ms  0.655 ms
 4  192.168.66.41 (192.168.66.41)  5.123 ms  4.924 ms  7.027 ms
 5  * * *
 6  * * *
 7  10.10.53.93 (10.10.53.93)  14.334 ms  14.304 ms  14.256 ms
 8  10.201.147.214 (10.201.147.214)  19.629 ms  19.580 ms  21.650 ms
 9  10.21.21.22 (10.21.21.22)  19.445 ms  16.203 ms  16.131 ms
10  10.21.21.22 (10.21.21.22)  13.911 ms  13.793 ms  13.723 ms
11  213.202.4.172 (213.202.4.172)  48.527 ms  48.476 ms  48.416 ms
12  134.0.220.62 (134.0.220.62)  48.361 ms  48.285 ms  47.198 ms
13  108.170.246.113 (108.170.246.113)  49.444 ms  49.409 ms 108.170.240.49 (108.170.240.49)  49.677 ms
14  172.253.51.137 (172.253.51.137)  52.925 ms  46.305 ms  45.917 ms
15  arn02s05-in-f142.1e100.net (172.217.18.142)  45.783 ms  45.708 ms  4
```

> use -i to Specifies the interface through which traceroute should send packets.

### traceroute6

traceroute with option `-6` supports ipv6, instead we can use traceroute6 command.

{% hint style="info" %}
What is MTU ? , the maximum transmission unit is the size of the largest protocol data unit that can be communicated in a single network layer transaction.
{% endhint %}

### tracepath

Tracepath traces a path to a designated network address, reporting on the "time to live" or TTL lag and maximum transmission units (MTU) along the way. This command can be run by any user other with access to the command line prompt.

```
root@ubuntu16-1:~# tracepath google.com
 1?: [LOCALHOST]                                         pmtu 1500
 1:  172.16.43.2                                           0.148ms 
 1:  172.16.43.2                                           0.139ms 
 2:  172.16.130.1                                          4.944ms asymm  1 
 3:  192.168.1.66                                          1.224ms asymm  1 
 4:  192.168.66.41                                         8.198ms asymm  1 
 5:  192.168.198.169                                       5.567ms asymm  1 
 6:  192.168.0.254                                         5.923ms asymm  1 
 7:  10.10.53.93                                          10.018ms asymm  1 
 8:  10.201.147.214                                       11.534ms asymm  1 
 9:  10.21.21.22                                          15.286ms asymm  1 
10:  10.21.21.22                                          11.588ms asymm  1 
11:  213.202.4.172                                        43.501ms asymm  1 
12:  no reply
...
30:  no reply
     Too many hops: pmtu 1500
     Resume: pmtu 1500 

```

> Traceroute is essentially the same as Tracepath except that by default, it will only give the TTL value.

### tracepath6

 **tracepath6** is good replacement for **traceroute6.**

### dig

 **Dig** stands for (**Domain Information Groper**) is a network administration command-line tool for querying **Domain Name System** (**DNS**) name servers. It is useful for verifying and troubleshooting **DNS** problems and also to perform **DNS** lookups and displays the answers that are returned from the name server that were queried.

> By default, dig sends the DNS query to name servers listed in the resolver(/etc/resolv.conf) unless it is asked to query a specific name server.

```
root@ubuntu16-1:~# dig aol.com

; <<>> DiG 9.10.3-P4-Ubuntu <<>> aol.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 51870
;; flags: qr rd ra; QUERY: 1, ANSWER: 5, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 512
;; QUESTION SECTION:
;aol.com.			IN	A

;; ANSWER SECTION:
aol.com.		587	IN	A	188.125.72.165
aol.com.		587	IN	A	66.218.87.12
aol.com.		587	IN	A	67.195.231.10
aol.com.		587	IN	A	124.108.115.87
aol.com.		587	IN	A	106.10.218.150

;; Query time: 46 msec
;; SERVER: 8.8.8.8#53(8.8.8.8)
;; WHEN: Mon Feb 24 16:12:46 +0330 2020
;; MSG SIZE  rcvd: 116
```

The dig command output has several sections sections , to have just Answer section use +short switch :

```
root@ubuntu16-1:~# dig aol.com +short
124.108.115.87
66.218.87.12
188.125.72.165
106.10.218.150
67.195.231.10
```

to query specific  Name server use `@NameServerIP` :

```
root@ubuntu16-1:~# dig aol.com  @64.6.65.6 +short
106.10.218.150
188.125.72.165
66.218.87.12
124.108.115.87
67.195.231.10
```

### netstat

  **netstat** (network statistics) is a command-line tool that displays network connections (both incoming and outgoing), routing tables,  number of network interface and even network protocol statistics.

> By default, netstat displays a list of open sockets .( A socket is one end-point of a two-way communication link between two programs running on the network.) as an example X11:

```
root@ubuntu16-1:~# netstat  | grep X11 | head -n3
unix  3      [ ]         STREAM     CONNECTED     34942    @/tmp/.X11-unix/X0
unix  3      [ ]         STREAM     CONNECTED     33525    @/tmp/.X11-unix/X0
unix  3      [ ]         STREAM     CONNECTED     32753    @/tmp/.X11-unix/X0

```

So we usually use a combination of switches with netstat :

| netstat command example  | usage                                                      |
| ------------------------ | ---------------------------------------------------------- |
| netstat -a               | Listing all the LISTENING Ports of TCP and UDP connections |
| netstat -na              | all LISTENING  ports, but shows numerical addresses        |
| netstat -at              | Listing TCP Ports connections                              |
| netstat -au              | Listing UDP Ports connections                              |
| netstat -l               | Listing all LISTENING(TCP\&UDP) Connections                |
| netstat -s               | Showing Statistics by Protocol(TCP\&UDP&...)               |
| netstat -tp              | Displaying Service name with PID                           |
| netstat -rn              | Displaying Kernel IP routing                               |

> use netstat in conjunction with grep to get a better results.

### netcat

The `nc `(or netcat) utility is used for just about anything under the sun involving TCP or UDP. It can open TCP connections, send UDP packets, listen on arbitrary TCP and UDP ports, do port scanning, and deal with both IPv4 and IPv6. Unlike telnet, nc scripts nicely, and separates error messages onto standard error instead of sending them to standard output, as telnet does with some.

```
root@ubuntu16-1:~# netcat -l 8888
```

The -l parameter means that netcat is in listen (server) mode, and 8888 is the port it listens to; netcat will create a socket server and wait for connections on port 8888 . The terminal will remain on hold for a client to connect to the open server with netcat. We can verify that a host service listens on port 8888.We need to open a new terminal to the host station and run the command:

```
root@ubuntu16-1:~# netstat -na | grep 8888
tcp        0      0 0.0.0.0:8888            0.0.0.0:*               LISTEN 
```





.

.

.

[https://www.cyberciti.biz/faq/howto-test-ipv6-network-with-ping6-command/](https://www.cyberciti.biz/faq/howto-test-ipv6-network-with-ping6-command/)

[https://www.lifewire.com/traceroute-linux-command-4092586](https://www.lifewire.com/traceroute-linux-command-4092586)

[https://geek-university.com/linux/traceroute-command/](https://geek-university.com/linux/traceroute-command/)

[https://www.techwalla.com/articles/differences-between-traceroute-tracepath](https://www.techwalla.com/articles/differences-between-traceroute-tracepath)

[http://netstat.net/](http://netstat.net)

[http://journals.ecs.soton.ac.uk/java/tutorial/networking/sockets/definition.html](http://journals.ecs.soton.ac.uk/java/tutorial/networking/sockets/definition.html)

[https://www.geeksforgeeks.org/netstat-command-linux/](https://www.geeksforgeeks.org/netstat-command-linux/)

[https://www.tecmint.com/20-netstat-commands-for-linux-network-management/](https://www.tecmint.com/20-netstat-commands-for-linux-network-management/)

[https://linux.die.net/man/1/nc](https://linux.die.net/man/1/nc)

[https://www.mvps.net/docs/what-is-netcat-and-how-to-use-it/](https://www.mvps.net/docs/what-is-netcat-and-how-to-use-it/)

.

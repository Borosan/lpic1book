# 109.2. Basic network configuration

**Weight:** 4

**Description:** Candidates should be able to view, change and verify configuration settings on client hosts.

**Key Knowledge Areas:**

* Manually and automatically configure network interfaces
* Basic TCP/IP host configuration
* Setting a default route

**Terms and Utilities:**

* /etc/hostname
* /etc/hosts
* /etc/nsswitch.conf
* ifconfig
* ifup
* ifdown
* ip
* route
* ping

In this tutorial, we learn hat make network configurations persistent in linux.

### ifconfig

 **ifconfig** ( **interface configuration)** utility is used to configure, or view the configuration of, a network interface via command line interface(CentOS6).

```
ifconfig [...OPTIONS] [INTERFACE]
```

 The “**ifconfig**” command with no option, displays  current network configuration information:

```
[root@centos6-1 ~]# 
[root@centos6-1 ~]# ifconfig
eth0      Link encap:Ethernet  HWaddr 00:0C:29:6D:D2:C5  
          inet addr:172.16.43.137  Bcast:172.16.43.255  Mask:255.255.255.0
          inet6 addr: fe80::20c:29ff:fe6d:d2c5/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:235444 errors:0 dropped:0 overruns:0 frame:0
          TX packets:81562 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:309940884 (295.5 MiB)  TX bytes:5027974 (4.7 MiB)

lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:2964 errors:0 dropped:0 overruns:0 frame:0
          TX packets:2964 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0 
          RX bytes:386271 (377.2 KiB)  TX bytes:386271 (377.2 KiB)
```

> the lo is the loopback adapter , with ip address 127.0.0.1 which is used by operating system for its own internal communications.

| ifconfig option | description                                                  |
| --------------- | ------------------------------------------------------------ |
| -a              | display all the interfaces available, even if they are down. |
| -s              | display a short list (like netstat -i)                       |
| -v              | be more verbose for some error conditions                    |

We can use ifconfig to change network configurations, but this will require root access:

| ifconfig command                                    | description          |
| --------------------------------------------------- | -------------------- |
| ifconfig eth0 172.16.43.155                         | Assign a IP Address  |
| ifconfig eth0 netmask 255.255.255.224               | Assign a Netmask     |
| ifconfig eth0 172.16.43.155 netmask 255.255.255.224 | Assign a IP, Netmask |

ifconfig can be used to  turn an interface up and down ,again  this will need root access:

* ifconfig _interface_ up:  used to activate the driver for the given interface
* ifconfig _interface _down:   used to deactivate the driver for the given interface

{% hint style="danger" %}
If you're still using ifconfig, you're living in the past! ifconfig command  is deprecated and replaced by ip command.
{% endhint %}

### ifup , ifdown

Same as previous commands, ifup and ifdown are used to enable and disable an interface.

```
[root@centos6-1 ~]# ifdown eth0
Device state: 3 (disconnected)
[root@centos6-1 ~]# ifup eth0
Active connection state: activating
Active connection path: /org/freedesktop/NetworkManager/ActiveConnection/10
state: activated
Connection activated
```

### gateway

One benefit of chopping ip addresses into classes and subnets is controlling broad casts.This happens using subnetmask.  But what is two compuyers from two different networks wants to communicate with each other?

A **gateway **is a node or a router that acts as an access point to passes network data from local networks to remote networks.

the address which is used as a gateway to reach to other networks outside our local network is called **default gateway**. There are different ways to set gateway.

### Network Configuration files

The bad news is that all configurations that we have done using ifconfig command are not persistent and got vanished whe system restarts ot the interface turns up and down. So we should find out a way to make our setting persistent and the only way we can achieve that by using network configuration files.

Unfortunately the network configuration files in linux are placed in different places and it depend on the distribution which we are talking about.

#### RedHat Based systems 

In Redhat , CentOS, and fedora the files are located at  `/etc/sysconfig/network-scripts/` . 

```
[root@server1 ~]# ls /etc/sysconfig/network-scripts/
ifcfg-eth0   ifdown-ippp    ifdown-sit     ifup-ib     ifup-post      init.ipv6-global
ifcfg-lo     ifdown-ipv6    ifdown-tunnel  ifup-ippp   ifup-ppp       net.hotplug
ifdown       ifdown-isdn    ifup           ifup-ipv6   ifup-routes    network-functions
ifdown-bnep  ifdown-post    ifup-aliases   ifup-isdn   ifup-sit       network-functions-ipv6
ifdown-eth   ifdown-ppp     ifup-bnep      ifup-plip   ifup-tunnel
ifdown-ib    ifdown-routes  ifup-eth       ifup-plusb  ifup-wireless


```

and the default gateway is configured via file:  `/etc/sysconfig/network`

```
[root@server1 Desktop]# cat /etc/sysconfig/network
NETWORKING=yes
HOSTNAME=server1
GATEWAY=172.16.43.2
```

lets take a look at  eth0 configuration file:

```
[root@server1 ~]# cat  /etc/sysconfig/network-scripts/ifcfg-eth0
DEVICE="eth0"
BOOTPROTO=none
IPV6INIT="yes"
NM_CONTROLLED="yes"
ONBOOT="yes"
TYPE="Ethernet"
UUID="8f5774e8-607e-4629-8d54-a9cc4d12e851"
IPADDR=172.16.43.127
PREFIX=24
GATEWAY=172.16.43.2
DEFROUTE=yes
IPV4_FAILURE_FATAL=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
NAME="System eth0"
HWADDR=00:0C:29:6D:D2:C5
DNS1=8.8.8.8
IPV6_PEERDNS=yes
IPV6_PEERROUTES=yes
LAST_CONNECT=1582381005
```

#### Debian Based Systems

In debian based systems line Ubuntu , ... the main network configuration file location is  `/etc/network/interfaces` and there is no separated file for gateway configuration :

```
auto lo
iface lo inte loopback

auto eth0
#ifconfig eth0 inet dhcp
iface eth0 inet static 
address 172.16.43.135
netmask 255.255.255.0
gateway 172.16.43.2
dns-nameservers 8.8.8.8
```

there might be `/etc/network/interfaces.d `directory for configuration files.

> ifdown and ifup commands use this configuration file.

#### DNS configuration  file

### /etc/resolv.conf:

As you have noticed DNS configuration is in the same file that interface configuration is located but there is another place in linux which contains DNS information `/etc/resolv.conf` :

```
[root@server1 Desktop]# cat /etc/resolv.conf 
# Generated by NetworkManager
nameserver 8.8.8.8
```

Again setting in this file are not permanent and it is not recommended to change this file by hand , except for temporary  tests.

### hostname

 **Hostname** is the program that is used to either set or display the current host, domain or node name of the system (we are usiong centOS).

```
[root@server1 Desktop]# hostname
server1
[root@server1 Desktop]# hostname centos6-1
[root@server1 Desktop]# hostname
centos6-1
```

The new hostname will appear if you open a new terminal but get vanished if you restart the system. To configure hostname permanently there are a couple of other places which should be changed

1. `/etc/hostname `(Ubuntu) OR` /etc/sysconfig/network` (CentOS)
2. `/etc/hosts` (both Ubuntu , CentOS)

### /etc/hostname

/etc/hostname contains name of the machine and is one of the configuration files that should be modified  in order to make a new hostname persistent in Debian based systems.(ubuntu16 here)

```
root@ubuntu16-1:~# cat /etc/hostname 
ubuntu16-1
```

### /etc/sysconfig/network

Another place which contains hostname and should be changed in RedHat based systems to have persistent hostname(CentOS6)

```
[root@server1 Desktop]# cat /etc/sysconfig/network
NETWORKING=yes
HOSTNAME=server1   ###<---- change it to centos6-1 in our ex
GATEWAY=172.16.43.2
```

### /etc/hosts

 The **/etc/hosts** is an operating system file that translate hostnames or domain names to IP addresses , it do the same thing that DNS do. We can using for testing purposes or when DNS server is absent. Do not forget that it has a highr priority than DNS, (means that operating system first look inside /etc/hosts file to gain the ip address of a host, if it wasn't successful then it would query DNS Server).

```
[root@server1 Desktop]# cat /etc/hosts
127.0.0.1	centos6-1 localhost localhost.localdomain localhost4 localhost4.localdomain4
::1	server1	localhost localhost.localdomain localhost6 localhost6.localdomain6

```

For making new hostname permanent it is another file which should be modified.

### route

All network devices, whether they are hosts, routers, or other types of network nodes such as network attached printers, need to make decisions about where to route TCP/IP data packets. The routing table provides the configuration information required to make those decisions. the **route **command is used to view and make changes to the kernel routing table. 

{% hint style="danger" %}
route command make temporary setting, use config files instead!
{% endhint %}

 Running **route**  command  without any options displays the routing table entries:

```
[root@server1 Desktop]# route
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
172.16.43.0     *               255.255.255.0   U     1      0        0 eth0
default         172.16.43.2     0.0.0.0         UG    0      0        0 eth0
```

> This shows us how the system is currently configured. If a packet comes into the system and has a destination in the range **172.16.43.0 **through **172.16.43.255**, then it is forwarded to the gateway **\***, which is **0.0.0.0** — a special address which represents an invalid or non-existant destination. So, in this case, our system will not route these packets.
>
> If the destination is not in this IP address range, it is forwarded to the default gateway (in this case, **172.16.43.2**, and that system will determine how to forward the traffic on to the next step towards its destination.

By default route command displays the host name in its output. We can request it to display the numerical IP address using `-n `option:

```
[root@server1 Desktop]# route -n
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
172.16.43.0     0.0.0.0         255.255.255.0   U     1      0        0 eth0
0.0.0.0         172.16.43.2     0.0.0.0         UG    0      0        0 eth0
```

 The following route add command will set the default gateway as 172.16.43.2:

```
[root@server1 ~]# route add default gw 172.16.43.1
[root@server1 ~]# route
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
172.16.43.0     *               255.255.255.0   U     0      0        0 eth0
link-local      *               255.255.0.0     U     1002   0        0 eth0
default         172.16.43.1     0.0.0.0         UG    0      0        0 eth0
default         172.16.43.2     0.0.0.0         UG    0      0        0 eth0
```

use route del for deleting:

```
[root@server1 ~]# route del  default gw 172.16.43.1
[root@server1 ~]# route
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
172.16.43.0     *               255.255.255.0   U     0      0        0 eth0
link-local      *               255.255.0.0     U     1002   0        0 eth0
default         172.16.43.2     0.0.0.0         UG    0      0        0 eth0
```

> Kernel maintains the routing cache information to route the packets faster. We can list the kernel’s routing cache information by using the -C flag.

> `netstat -rn` also shows routing table.

### ip

This command replaces old good and now deprecated ifconfig command, however, **ifconfig ** command is still works and available for most of the Linux distributions.

It can be used to assign and remove addresses , bring interfaces up or down,  manipulate routing,  and many more things.

```
ip [ OPTIONS ] OBJECT { COMMAND | help }
```

| ip command example                                     | description                                             |
| ------------------------------------------------------ | ------------------------------------------------------- |
| ip address show                                        | show all IP addresses associated on all network devices |
| ip address show eth0                                   | view the information of any particular interface        |
| ip addr add 192.168.50.5/24 dev eth0                   | Assign a IP Address to Specific Interface               |
| ip addr del 192.168.50.5/24 dev eth0                   | Remove an IP Address                                    |
| ip link show                                           | Display Network Interface(s)                            |
| ip link set eth0 up                                    | Enable Network Interface                                |
| ip link set eth0 down                                  | Disable Network Interface                               |
| ip route show                                          | Show routing table information                          |
| ip route add 10.10.20.0/24 via 192.168.50.100 dev eth0 | Add static route                                        |
| ip route del 10.10.20.0/24                             | Remove static route                                     |

> All the above settings  will be lost after a system restart. use config files instead.

### ping 

The `ping` command is one of the most used utilities for troubleshooting, testing, and diagnosing network connectivity issues.

Ping works by sending one or more ICMP (Internet Control Message Protocol) Echo Request packages to a specified destination IP on the network and waits for a reply. When the destination receives the package, it will respond back with an ICMP echo reply.

```
[root@server1 ~]# ping 8.8.8.8 -c3
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=128 time=45.0 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=128 time=47.0 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=128 time=40.2 ms

--- 8.8.8.8 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2043ms
rtt min/avg/max/mdev = 40.262/44.107/47.040/2.851 ms
```

With the `ping` command, we can determine whether a remote destination IP is active or inactive. You can also find the round-trip delay in communicating with the destination and check whether there is a packet loss.

| ping command switch | description                                        |
| ------------------- | -------------------------------------------------- |
|  **-n**             | Numeric output only.do not try to resolve hostname |
|  **-i interval**    | Wait interval seconds between sending each packet  |
|  **-I interface**   | Set source address to specified interface address  |
| **-a**              | Audible ping                                       |

> for ipv6 environment use ping6 command.

### /etc/nsswitch

This file determines where the system finds things like host names, passwords, and protocol numbers:

Here’s a snippet from a sample /etc/nsswitch.conf file

```
passwd:   files nis
group:    files nis

hosts:    files dns myhostname
```

In this example, user information (the passwd and group services) come first from “files” (like /etc/passwd or /etc/group), and if no entries are found there, a query to an NIS server (configured elsewhere) will be used. 

Host information first comes from /etc/hosts (files), then a DNS server (dns), and if neither of those work, at least a fallback of “myhostname” so that the local machine has _some_ name.

> The non-complexity comes in the “and if that doesn’t work” rule. When multiple services are listed, they’re tried in order, and a sevice either succeeds or fails. If it fails, the next is tried, etc.

that's all.

.

.

.

{% hint style="info" %}
### Consistent network device naming

 Red Hat Enterprise Linux provides methods for consistent and predictable network device naming for network interfaces. These features change the name of network interfaces on a system in order to make locating and differentiating the interfaces easier.

Traditionally, network interfaces in Linux are enumerated as `eth[0123…]`, but these names do not necessarily correspond to actual labels on the chassis. Modern server platforms with multiple network adapters can encounter non-deterministic and counter-intuitive naming of these interfaces. This affects both network adapters embedded on the motherboard (_Lan-on-Motherboard_, or _LOM_) and add-in (single and multiport) adapters.

In Red Hat Enterprise Linux, **udev** supports a number of different naming schemes. The default is to assign fixed names based on firmware, topology, and location information. This has the advantage that the names are fully automatic, fully predictable, that they stay fixed even if hardware is added or removed (no re-enumeration takes place), and that broken hardware can be replaced seamlessly. The disadvantage is that they are sometimes harder to read than the eth0 or wlan0 names traditionally used. For example: enp5s0.

* for disabling that (how ever it is not recommanded Add both `net.ifnames=0` and `biosdevname=0` as kernel parameter values to the `GRUB_CMDLINE_LINUX` variable  )
{% endhint %}



.

.

[https://developer.ibm.com/tutorials/l-lpic1-109-2/](https://developer.ibm.com/tutorials/l-lpic1-109-2/)

[https://www.computerhope.com/unix/uifconfi.htm](https://www.computerhope.com/unix/uifconfi.htm)

[https://www.tecmint.com/ifconfig-command-examples/](https://www.tecmint.com/ifconfig-command-examples/)

[https://www.geeksforgeeks.org/ifconfig-command-in-linux-with-examples/](https://www.geeksforgeeks.org/ifconfig-command-in-linux-with-examples/)

[https://www.unixmen.com/how-to-find-default-gateway-in-linux/](https://www.unixmen.com/how-to-find-default-gateway-in-linux/)

[https://jadi.gitbooks.io/lpic1/content/1092\_basic_network_configuration.html](https://jadi.gitbooks.io/lpic1/content/1092\_basic_network_configuration.html)

[https://www.tecmint.com/setup-local-dns-using-etc-hosts-file-in-linux/](https://www.tecmint.com/setup-local-dns-using-etc-hosts-file-in-linux/)

[https://opensource.com/business/16/8/introduction-linux-network-routing](https://opensource.com/business/16/8/introduction-linux-network-routing)

[https://www.computerhope.com/unix/route.htm](https://www.computerhope.com/unix/route.htm)

[https://www.thegeekstuff.com/2012/04/route-examples/](https://www.thegeekstuff.com/2012/04/route-examples/)

[https://linuxize.com/post/linux-ip-command/](https://linuxize.com/post/linux-ip-command/)

[https://www.geeksforgeeks.org/ip-command-in-linux-with-examples/](https://www.geeksforgeeks.org/ip-command-in-linux-with-examples/)

[https://www.tecmint.com/ip-command-examples/](https://www.tecmint.com/ip-command-examples/)

[https://linuxize.com/post/linux-ping-command/](https://linuxize.com/post/linux-ping-command/)

[https://linux.die.net/man/8/ping](https://linux.die.net/man/8/ping)

[https://developers.redhat.com/blog/2018/11/26/etc-nsswitch-conf-non-complexity/](https://developers.redhat.com/blog/2018/11/26/etc-nsswitch-conf-non-complexity/)

.

[https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/networking_guide/ch-consistent_network_device_naming](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/networking_guide/ch-consistent_network_device_naming)

.

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

 **ifconfig** \( **interface configuration\)** utility is used to configure, or view the configuration of, a network interface via command line interface.

```text
ifconfig [...OPTIONS] [INTERFACE]
```

 The “**ifconfig**” command with no option, displays  current network configuration information:

```text
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

| ifconfig option | description |
| :--- | :--- |
| -a | display all the interfaces available, even if they are down. |
| -s | display a short list \(like netstat -i\) |
| -v  | be more verbose for some error conditions |

We can use ifconfig to change network configurations, but this will require root access:

| ifconfig command | description |
| :--- | :--- |
| ifconfig eth0 172.16.43.155 | Assign a IP Address |
| ifconfig eth0 netmask 255.255.255.224 | Assign a Netmask   |
| ifconfig eth0 172.16.43.155 netmask 255.255.255.224 | Assign a IP, Netmask |

ifconfig can be used to  turn an interface up and down ,again  this will need root access:

* ifconfig _interface_ up:  used to activate the driver for the given interface
* ifconfig _interface_ down:   used to deactivate the driver for the given interface

{% hint style="danger" %}
If you're still using ifconfig, you're living in the past! ifconfig command  is deprecated and replaced by ip command.
{% endhint %}

### ifup , ifdown

Same as previous commands, ifup and ifdown are used to enable and disable an interface.

```text
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

A **gateway** is a node or a router that acts as an access point to passes network data from local networks to remote networks.

the address which is used as a gateway to reach to other networks outside our local network is called **default gateway**. There are different ways to set gateway.

### Network Configuration files

The bad news is that all configurations that we have done using ifconfig command are not persistent and got vanished whe system restarts ot the interface turns up and down. So we should find out a way to make our setting persistent and the only way we can achieve that by using network configuration files.

Unfortunately the network configuration files in linux are placed in different places and it depend on the distribution which we are talking about.

#### RedHat Based systems 

In Redhat , CentOS, and fedora the files are located at  `/etc/sysconfig/network-scripts/` . 

```text
[root@centos6-1 ~]# ifconfig
eth0      Link encap:Ethernet  HWaddr 00:0C:29:6D:D2:C5  
          inet addr:172.16.43.127  Bcast:172.16.43.255  Mask:255.255.255.0
          inet6 addr: fe80::20c:29ff:fe6d:d2c5/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:235756 errors:0 dropped:0 overruns:0 frame:0
          TX packets:81964 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:309991277 (295.6 MiB)  TX bytes:5063448 (4.8 MiB)

lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:3120 errors:0 dropped:0 overruns:0 frame:0
          TX packets:3120 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0 
          RX bytes:404801 (395.3 KiB)  TX bytes:404801 (395.3 KiB)
```

and the default gateway is configured via file:  `/etc/sysconfig/network`

```text
[root@server1 Desktop]# cat /etc/sysconfig/network
NETWORKING=yes
HOSTNAME=server1
GATEWAY=172.16.43.2
```

#### Debian Based Systems

In debian based systems line Ubuntu , ... the main network configuration file location is  `/etc/network/interfaces` and there is no separated file for gateway configuration :

```text
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

> ifdown and ifup commands use this configuration file.

#### DNS configuration  file

As you have noticed DNS configuration is in the same file that interface configuration is located but there is another place in linux which contains DNS information `/etc/resolv.conf` :

```text
[root@server1 Desktop]# cat /etc/resolv.conf 
# Generated by NetworkManager
nameserver 8.8.8.8
```

Again setting in this file are not permanent and it is not recommended to change this file by hand , except for temporary  tests.

hostname

/etc/hostanme

hosts

route

ping

\(ip\)

\(nsswitch\)

.

.

.

[https://developer.ibm.com/tutorials/l-lpic1-109-2/](https://developer.ibm.com/tutorials/l-lpic1-109-2/)

[https://www.computerhope.com/unix/uifconfi.htm](https://www.computerhope.com/unix/uifconfi.htm)

[https://www.tecmint.com/ifconfig-command-examples/](https://www.tecmint.com/ifconfig-command-examples/)

[https://www.geeksforgeeks.org/ifconfig-command-in-linux-with-examples/](https://www.geeksforgeeks.org/ifconfig-command-in-linux-with-examples/)

[https://www.unixmen.com/how-to-find-default-gateway-in-linux/](https://www.unixmen.com/how-to-find-default-gateway-in-linux/)

[https://jadi.gitbooks.io/lpic1/content/1092\_basic\_network\_configuration.html](https://jadi.gitbooks.io/lpic1/content/1092_basic_network_configuration.html)

.


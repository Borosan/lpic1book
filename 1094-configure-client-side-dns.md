# 109.4. Configure client side DNS

**Weight**: 2

**Description:** Candidates should be able to configure DNS on a client host.

**Key Knowledge Areas:**

* Query remote DNS servers
* Configure local name resolution and use remote DNS servers
* Modify the order in which name resolution is done

**Terms and Utilities:**

* /etc/hosts
* /etc/resolv.conf
* /etc/nsswitch.conf
* host
* dig
* getent

We have seen all of these topics in previous lessons, so in this lesson first  we take a quick look at them and then we will talk about steps of Name Resolution on client side.

#### DNS

The DNS \(Domain Name System\) resolves the names of internet sites with their underlying IP addresses .

![](.gitbook/assets/clientdns-howdns.jpg)

#### Client Name Resolution 

When client  wants to access any other computers in the  network, first it needs to know about target  ip address. There are different places inside os which keeps information:

/etc/host

there are some steps which client traverses to gain  destination ip address:

1. first it looks at /etc/nsswitch , nsswitch file define the sequence and places which client should check them for ip address.



.

.

.

[https://www.networkworld.com/article/3268449/what-is-dns-and-how-does-it-work.html](https://www.networkworld.com/article/3268449/what-is-dns-and-how-does-it-work.html)

[https://computer.howstuffworks.com/dns3.htm](https://computer.howstuffworks.com/dns3.htm)

.


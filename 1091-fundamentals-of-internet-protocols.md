# 109.1. Fundamentals of internet protocols

## **109.1 Fundamentals of internet protocols**

**Weight:** 4

**Description:** Candidates should demonstrate a proper understanding of TCP/IP network fundamentals.

**Key Knowledge Areas:**

* Demonstrate an understanding of network masks and CIDR notation
* Knowledge of the differences between private and public “dotted quad” IP addresses
* Knowledge about common TCP and UDP ports and services \(20, 21, 22, 23, 25, 53, 80, 110, 123, 139, 143, 161, 162, 389, 443, 465, 514, 636, 993, 995\)
* Knowledge about the differences and major features of UDP, TCP and ICMP
* Knowledge of the major differences between IPv4 and IPv6
* Knowledge of the basic features of IPv6

**Terms and Utilities:**

* /etc/services
* IPv4, IPv6
* Subnetting
* TCP, UDP, ICMP

#### IP 

The IP \(Internet Protocol\) is the fundamental protocol for communications on the Internet. It specifies the way information is packetized, addressed, transferred, routed, and received by networked devices.

#### IP Address

 An **IP address** is a number identifying of a computer or another device on the Internet. It is similar to a mailing address, which identifies where postal mail comes from and where it should be delivered. IP addresses uniquely identify the source and destination of data transmitted with the Internet Protocol.

#### IPv4 and IPv6 addresses

IPv4 addresses are 32 bits long \(four bytes\). An example of an IPv4 address is 216.58.216.164, which is the front page of Google.com.

{% hint style="info" %}
 **IP address breakdown**

The address is made up of 32 binary bits.

![](.gitbook/assets/fundamentalip-ipv4oct.jpg)

The 32 binary bits are broken into four octets \(1 octet = 8 bits\). Each octet is converted to decimal and separated by a period \(dot\). For this reason, an IP address is said to be expressed in dotted decimal format.

**Here is how binary octets convert to decimal:** The right most bit, or least significant bit, of an octet holds a value of 2^0. The bit just to the left of that holds a value of 2^1. This continues until the left-most bit, or most significant bit, which holds a value of 2^7. So if all binary bits are a one, the decimal equivalent would be 255 as shown here:

> ```text
>     1  1  1  1 1 1 1 1
>   128 64 32 16 8 4 2 1 (128+64+32+16+8+4+2+1=255)
> ```

Here is a sample octet conversion when not all of the bits are set to 1.

> ```text
>   0  1 0 0 0 0 0 1
>   0 64 0 0 0 0 0 1 (0+64+0+0+0+0+0+1=65)
> ```

And this sample shows an IP address represented in both binary and decimal.

> ```text
>         10.       1.      23.      19 (decimal)
>   00001010.00000001.00010111.00010011 (binary)
> ```
{% endhint %}

The maximum value of a 32-bit number is 232, or 4,294,967,296. So the maximum number of IPv4 addresses, which is called its address space, is about 4.3 billion. In the 1980s, this was sufficient to address every networked device, but scientists knew that this space would quickly become exhausted. 

> Technologies such as NAT have delayed the problem by allowing many devices to use a single IP address, but a larger address space is needed to serve the modern Internet.

A major advantage of IPv6 is that it uses 128 bits of data to store an address, permitting 2128 unique addresses, or 340,282,366,920,938,463,463,374,607,431,768,211,456. The size of IPv6's address space — 340 duodecillion — is much, much larger than IPv4.

#### subnetmask

Each IP class is equipped with its own default subnet mask \(netmask\). netmask  is a 32-bit binary which bounds that IP class to have prefixed number of Networks and prefixed number of Hosts per network.

* Netid: The part of an IP address that identifies the network. 
* Hostid: The part of an IP address that identifies a host in a network.

The netid and hostid are of varying lengths, depending on the class of the address.

#### IP address classes

With an IPv4 IP address, there are five classes of available IP ranges: Class A, Class B, Class C, Class D and Class E, while only A, B, and C are commonly used.

 Each class allows for a range of valid IP addresses, shown in the following table.

| Class | Address range | subnetmask | Supports |
| :--- | :--- | :--- | :--- |
| **Class A** | 1.0.0.1 to 126.255.255.254 | 255.0.0.0 | Supports 16 million hosts on each of 127 networks. |
| **Class B** | 128.1.0.1 to 191.255.255.254 | 255.255.0.0 | Supports 65,000 hosts on each of 16,000 networks. |
| **Class C** | 192.0.1.1 to 223.255.254.254 | 255.255.255.0 | Supports 254 hosts on each of 2 million networks. |
| **Class D** | 224.0.0.0 to 239.255.255.255 | N/A | Reserved for multicast groups. |
| **Class E** | 240.0.0.0 to 254.255.255.254 | N/A | Reserved for future use, or research and development purposes. |

> Ranges 127.x.x.x are reserved for the loopback or localhost, for example, **127.0.0.1** is the loopback address. Range **255.255.255.255** broadcasts to all hosts on the local network.



Classful IP addressing does not provide any flexibility of having less number of Hosts per Network or more Networks per IP Class, where subnetting comes to play.

#### subnetting

The process of  deviding an IP Class into  smaller blocks, or groups of IPs, known as subnetting. 

Subnetting can improve security and help to balance overall network traffic. 

{% hint style="info" %}
**CIDR**

CIDR **** or **Classless Inter Domain Routing** is based on subnetting concept.CIDR and subnetting are virtually the same thing. The term Subnetting is generally used when you use it at the organizational level. CIDR is generally used when you it at the ISP level or higher.
{% endhint %}

**How subnetting works ?**subnetting is a bitwise operation on a network of ip addresses which take place using netmask \(subnetmask\).

 it provides the flexibility of borrowing bits of Host part of the IP address and using them as Network in Network, called Subnet. By using subnetting, one single Class A IP address can be used to have smaller sub-networks which provides better network management capabilities.

* **Class A Subnets**

  In Class A, only the first octet is used as Network identifier and rest of three octets are used to be assigned to Hosts \(i.e. 16777214 Hosts per Network\). To make more subnet in Class A, bits from Host part are borrowed and the subnet mask is changed accordingly.

![](.gitbook/assets/fundamentalip-classasub.jpg)

* **Class B Subnets**

  By default, using Classful Networking, 14 bits are used as Network bits providing \(2^14\) 16384 Networks and \(\(2^16\)-2\) 65534 Hosts. Class B IP Addresses can be subnetted the same way as Class A addresses, by borrowing bits from Host bits. Below is given all possible combination of Class B subnetting.

![](.gitbook/assets/fundamentalip-classbsub.jpg)

* **Class C Subnets**

  Class C IP addresses are normally assigned to a very small size network because it can only have 254 hosts in a network. Given below is a list of all possible combination of subnetted Class B IP address

![](.gitbook/assets/fundamentalip-classcsub.jpg)

#### Communication Protocols

tcp

udp

icmp

ports

 





.

.

.

[https://www.computerhope.com/jargon/i/ip.htm](https://www.computerhope.com/jargon/i/ip.htm)

[https://www.cisco.com/c/en/us/support/docs/ip/routing-information-protocol-rip/13788-3.html](https://www.cisco.com/c/en/us/support/docs/ip/routing-information-protocol-rip/13788-3.html)

[https://www.computerhope.com/jargon/n/netmask.htm](https://www.computerhope.com/jargon/n/netmask.htm)

[http://compunetworx.blogspot.com/2013/01/difference-between-hostid-and-netid-in.html](http://compunetworx.blogspot.com/2013/01/difference-between-hostid-and-netid-in.html)

[https://www.computerhope.com/jargon/s/subnetma.htm](https://www.computerhope.com/jargon/s/subnetma.htm)

[https://www.tutorialspoint.com/ipv4/ipv4\_subnetting.htm](https://www.tutorialspoint.com/ipv4/ipv4_subnetting.htm)

[http://www.itgeared.com/articles/1347-cidr-and-subnetting-tutorial/](http://www.itgeared.com/articles/1347-cidr-and-subnetting-tutorial/)

.


# CCNA 200-301 Chapter 22

## Introduction to IPv6

IP version 6 (IPv6) serves as the replacement protocol for IP version 4 (IPv4) as there is a limited amount of IPv4 address, we have exhausted all the routable  address back in 2015. IPv6 was made as the newer version of IPv4. IPv6 is 128-bit based versus the 32-bit IPv4 was. It is wiremen in 32 hex digits. versus the 4 numbers of the IPv6 address.

Many proctols had to be updated for exmaple:
OPSFv2 didnt not support IPv6 and had to be updated to V3 that has support for both V4 and V6
ICMP, and ARP had both needed to be updated to support the new IP format.

Just like IPv4, IPv6 defines a header, it makes no changes expect making the Source and Destination spaces bigger to accommodate the nigger IPv6 address. 

### IPv6 Routing

IPv6 mostly follow in suit with its older brother IPv4: 

- To be able to make and send IPv6 packets the end user device needs an IPv6 address on its interface
- End user Devices need to know the router (default gateway IPv6 address.)
- IPv6 routers de-encapsulate and re-encapsulate each IPv6 packet when routing the packet
- IPv6 routers make routing decisions based on IPv6 destination address by comparing it to his IPv6 Routing table. To see where the packet should go to next

​	

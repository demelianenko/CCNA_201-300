# CCNA 200-301 Chapter 11

## Analyze Subnetting And Addressing Needs

1. Which hosts should be grouped together?
2. How many subnets does this internetwork requires
3. How many host IP address does each subnet require?
4. Will we use a single subnet for simplicity or not?

### Rules About Which Hosts are In Which Subnet

- Address in the same subnet are not separated by a router
- Address in different subnets are separated by a least one router

A router would need an IP in the Subnet do forward packets to that subnet.

### Determining the Number of Subnets

You should plan for at least one subnet for each type of link:

- VLAN (One subnet per vlan)
- Point to Point (Router links)
- Ethernet Wan

### Determining the number of hosts per Subnet

Each device that connect to a subnet would need an IP address. When thinking about the size of each subnet you need to take in to account potential growth. If you are implementing a new site use the old one as reference and do not forget the link between routers would need its own subnet. Making a subnet bigger is always better than making it too small.

### One size Fit all --- Or Not

The final choice after how many and how many in, would be do we use this size for all subnets? (I.e we found we only need 50 per subnet do we keep all subnets to 50?) or use different sizes for each subnet. With pros and cons on both sides.

#### Defining the size of a subnet

The network engineer assigns a network mask, and that mask among other things defines the size of the subnet mask. How many hosts will be able to fit in this subnet. The mask sets a side the number of hosts bit that will be used to determine the number of hosts in that subnet. 
$$
2^H - 2
$$
H would be the number of hosts bits left by the subnet mask. We take 2 for the network address and the broadcast address. Two to the power of the host bits minus 2. 

The 32 bit IP address is broken to 3 parts
Network - Subnet - Host 
(The Subnet would be part of the network part)

### One Size subnet Fits All

To choose to use a single-size subnet in an enterprise network, you must use the same network mask for all of the subnets. That means you have to pick one that could accommodate the biggest subnetwork you have. Even if the other subnets would need only 50 hosts and one subnet would need 200. All the subnets would have the 200 mask if one size is chosen.

The biggest advantage of using one size for all would be the simplicity of the setup. The biggest disadvantage of using single size would be the potential waste of IP space. The wasted IP address are not a big issue as most companies would use Class A or B private networks that can supply many ip address.

### Multiple Subnet Size 

To create multiple sized subnet masks using a single Class A or B network address. Multiple subnet masks would need to be used. Insted of using the same big address we would use smaller address to waste less IP address. Instead of using 254 we can use 62. But we are still locked in our single bits. So some address are still wasted. Instead of using /24 we can use /22 to waste less address.

### One mask for all Subnets, or More than one

A network that uses more than one subnet mask is called a variable length subnet mask (VLSM) for example using a /24 for all the host subnetworks and /30 for the router links.

## Make a Design Choice

- Choose Network
- Choose 1 Mask
- List all Subnets

### Choose a Classful Network 

In the original design of the Internet companies used  to register public classful IP networks when implementing TCP/IP outside of their network. In the mid 90's an alternative became popular. Private IP networks

### Public IP networks

The original design of the Internet required companies to register for a Public IP Networks. That included describing the current strictures how many hosts they have and their projected growth. After submitting the paper work the company will get a class A, B or C classfull  network address. Public IP's and the their administration ensured that all companies that connected to the Internet were using unique address. After the company has received it network address no other company is to use that address.

### Growth Exhausted the Public IP Address Space

In the 1990's the world was running out of IPV4 address to give to companies, the very fast growth of the Internet has exhausted the number of public IP address. There were no more class A, B or C address to be given to companies. The Internet worked hard in the 1990 to solve that problem and came up with a few solutions:

* A new version of IP (IPv6), which has muc larger number of address (128 bit v the original 32 bit of IPv4)
* Assignment of subsets on the public IPV4 address to each company would get a part of the network, using CIDR
* Network Address Translation (NAT) which allows the use of private IP networks

NAT allows companies to use the same private address space like 10.0.0 but to use a unique public address at the same time. Lowring the amount of address needed for a company drastically. 

### Private IP Networks

When using NAT and every organization that connect to the Internet uses NAT and simply uses one or more private network address behind its public IP. The RFC 1918 defines the list of available private IP networks.

| Class Of Networks | Private IP Networks         | Number Of Networks |
| ----------------- | --------------------------- | ------------------ |
| A                 | 10.0.0.0                    | 1                  |
| B                 | 172.16.0.0 - 172.31.0.0     | 16                 |
| C                 | 192.168.0.0 - 192.168.255.0 | 256                |

* **Avoiding Using Another Organization Public Address Range for Private Networks:**
  Some organizations have part of their network that needs zero Internet access. The hosts in that part of the network still need an IP address. RFC 1918 suggest that truly private network that need no Internet access use the networks from the RFC 1918.
* **Avoiding/Delaying IPv4 Address Exhaustion:** 
  To delay the day in which all IPv4 address were assigned to organizations as public IP address. RFC 1918 calls to use NAT along with the private networks for the address internal to an organization. 
* **Reducing Internet Routers Routing Table Size:**
  The usage of private Internet address plus NAT helps to make the routing tables for routers much smaller as they don't need to route to the private address

#### Choosing an IP Network during Design Phase

In todays ages most new companies will use NAT plus private network address. Some companies who had their IP address before the 1990 are allowed to keep and use them. When choosing a private network address you are allowed to choose any of the Network mentioned in  RFC 1918. It might looks waste full to choose a network like 10.0.0.0 with 16 million IP address but there is no harm it in and no downside. 

### Choose the Mask

The mask would depends on a few factors:

* The number of subnets required
* The number of hosts/subnet required
* That a choice was made to use only one mask for all subnet so that all subnets are the same size
* The classful IP network number that will be subnetted

#### Classful IP Networks Before Subnetting

Before the network is sliced to sub networks it is a simple group of address. When thinking about an unsubnnted network classful network only has 2 parts to it the network part and the host part. 

* The address have the same value in the network part
* The address have the same value in the host part

The actual size of a network can be easily figured out

* Class A, Uses 8 bits for the network and 24 for the host. Resulting in: $2^{24}-2 = 16,777,214$ hosts
  
* Class B, uses 16 bits for the network and 16 for the hosts. Resulting $2^{16}-2=65,534$ usable hosts 
  
* Class C has only 8 bits for the host and 24 for the network: $2^8-2=254$ usable hosts.

#### Borrowing Host bits to Create Subnet Bits

In order to create a subnet we must borrow some bits from somewhere,  we cannot change the network bits of the address. for example if we use a Class C address there are 24 bits for the network that we cannot modify or use. That leaves us with 8 bits of the host portion for us to use in the subnet part of the address. The total length of the address of an IPv4 would always be 32 bits

#### Choosing Enough Subnet and Host bits

In order to detriment how many bits we need to borrow form the host part of the address. We need to think about two things we had in the planing phase.

* Number of subnets required
* Number of hosts/subnet

Each bit we take of the host part of the address times the amount of subnets we can have. But in the same time lowers the amount of hosts per subnet for example:
We have a class C address  with 8 host bits, if we take 1 host bit $2^1=2$ equals to 2 subnets each with $2^7-2=126$ Hosts per subnet. 

#### Example Design: 172.16.0.0 200 Subnets, 200 Hosts

* Use a single mask for all subnets
* Plan for 200 Subnets
* Plan for 200 hosts
* Use the Private class B network address 172.16.0.0

The closes number to 200 is 256 to get 256 we would need to borrow 8 bits from the hosts address, Class B has 16 bits of network and 16 bits of host. In this case 8 bits for the subnets and 8 bits for the hosts would be the right amount for 200 subnets and 200 hosts per subnet.

### Masks And Mask Format

The Subnet mask is 32 bit binary number with 1's on the left side (network side) and 0's on the right side (host side). Because the subnet mask always starts with the network portion it will always start with 1's and cant ever start with 0's. The 1's will always refer to the network/subnetwork part of the network address and 0 will always reference the host bit in the mask. 

Looking at the example above if we take the class B address 
The network part is: and the subnet part is while the host part would be all 0's
11111111 11111111 11111111 00000000

Apart from Binary we can write the subnet mask in two other methods in the dotted decimal notation: 255.255.255.0 or the prefix /24 the ddt (dotted decimal notation) is the sum of each octane (8 bits) of the address while the prefix is the sum of bits ($8*3=24$)

#### Build a List of All Subnets

Each subnet just like a network address reserves a few of its address for certain things like the:

* **Subnet number (Address):**
  Also called the subnet ID or subnet address. its is the smallest ip address in the subnet and cannot be used as an IP address as its the network address of that particular subnet(when all the host bits are 0).
* **Subnet Broadcast:**
  Also called the subnet broadcast address or directed broadcast address. is the last and biggest IP address in each subnet (when all the host bits are 1) and it also cannot be used as an IP address.
* **IP addresses:**
  All numbers between the subnet address and the broadcast address in the subnet. Can be used as an IP address for hosts

Following the example in this chapter (172.16.0.0 with mask of 255.255.255.0):

| Subnet Number (Address) | IP Address                  | Broadcast Address |
| ----------------------- | --------------------------- | ----------------- |
| 172.16.0.0              | 172.16.0.1-172.16.0.254     | 172.16.0.255      |
| 172.16.1.0              | 172.16.1.1-172.16.1.254     | 172.16.1.255      |
| 172.16.2.0              | 172.16.2.1-172.16.254       | 172.16.2.255      |
| 172.16.3.0              | 172.16.3.1-172.16.254       | 172.16.3.255      |
| 172.16.4.0              | 172.16.4.1-172.16.254       | 172.16.4.255      |
| 172.16.5.0              | 172.16.5.1-172.16.254       | 172.16.5.255      |
| 172.16.6.0              | 172.16.6.1-172.16.254       | 172.16.6.255      |
| 172.16.7.0              | 172.16.7.1-172.16.254       | 172.16.7.255      |
| 172.16.8.0              | 172.16.8.1-172.16.254       | 172.16.8.255      |
| 172.16.9.0              | 172.16.9.1-172.16.254       | 172.16.9.255      |
| Etc..                   | Etc..                       | Etc..             |
| 172.16.255.0            | 172.16.255.1-172.16.255.254 | 172.16.255.255    |

## Plan the Implementation

This is the last before deploying the network, the engineer has to choose how each subnet would be used, per department? per city? the Range of each subnet should be configured inside the DHCP server to be dynamically leased to hosts for use as their IP address.  

### Assigning Subnet to Different Locations 

The idea here is very simple, look at your network diagram and pick a subnet from the list we have made to fit that location. Making sure to record the subnet in a spreed sheet or a network tracker. You can pick any method you would like, using lower number subnet for LAN and higher for WAN for example. Another option would be to spread the subnet by their geographic location. You might want to reserve range of subnets for each location (some for US some for EU). 

### Choosing Static and Dynamic Range per Subnet

There are two ways a device in a network can get thier IP address.

- Statically assigned IP address
- Dynamically assigned IP address

The first method involves configuring the device manually and assigning all the information on the device itself.
The second and best practice method would be to use the DHCP server and protocol and give address dynamically, setting the device to DHCP (default on most) and the host will get their IP address from the DHCP server.  Some devices would still need or require a static IP address. Most places make a separation making the static IP address on the lower end and the dynamic on the higher end of the network.


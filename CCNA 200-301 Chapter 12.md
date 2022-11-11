# CCNA 200-301 Chapter 12

## IPv4 Network Classes and Related Facts

Classful IPv4 address consist of Consist of five classes: A,B,C,D,E. The first 3 classes consist of unicast address. Unicast are address are a single address meant to be sent or receive and be given to a single host. Class D address serve as a multi cast address being used to send data to multiple hosts (with address from A,B or C address) Class E is reserved for special future use case.

| Class | First Octet Values | Purpose                          |
| ----- | ------------------ | -------------------------------- |
| A     | 1-126              | Unicast (Large Networks)         |
| B     | 127-191            | Unicast (Medium Networks)        |
| C     | 192-223            | Unicast (Small Networks)         |
| D     | 224-239            | Multicast                        |
| E     | 240-255            | Reserved (Formally Experimental) |

The Class of the network can tell may more details than only its starting octet

|                                 | Class A               | Class B               | Class C                 |
| ------------------------------- | --------------------- | --------------------- | ----------------------- |
| First  Bits (that never change) | 0                     | 10                    | 110                     |
| First octet range               | 1-126                 | 172-191               | 192-223                 |
| Valid network address           | 1.0.0.0-126.0.0.0     | 172.0.0.0-191.255.0.0 | 192.0.0.0-223.255.255.0 |
| Total networks                  | $2^7-2=126$           | $2^{14}=16,384$       | $2^{21}=2,097,152$      |
| Hosts per network               | $2^{24}-2=16,777,214$ | $2^{16}-2=65,534$     | $2^8-2=254$             |
| Octets (bits) in network part   | 1 (8)                 | 2 (16)                | 3 (24)                  |
| Octets (bits) in the host part  | 3 (24)                | 2 (16)                | 1 (8)                   |
| Default mask                    | 255.0.0.0 (/8)        | 255.255.0.0 (/16)     | 255.255.255.0 (/24)     |

- All address range that begin with 0 and 127 are reserved. The reason why we take 2 away in the Total network parts of the class A.
- The reason class A is $2^7$ and not $2^8$ is because the first bit is always the same in a class A network. Same thing result in class B and C. Because we are looking for the total network amount. 

### Address Formats

When comparing two IP address in the same network, a few observation can be made:

- Address in the same network have the same values in the network portion of the address
- Address in the same network will always have a different host values in the host portion of the address

Before being subneted each network type will have it own part of the address reserved for the network and hosts

-  Class A: First 8 bits are reserved for the network, the next 24 bits are used for the hosts
- Class B First 16 bits are reserved for the network and the next 16 bits are used for the hosts
- Class C First 24 bits are reserved for the network and the next 8 are used for the hosts

### Default Masks

Default masks of the classful networks are the representation of the network parts, where 1 is the network part and 0 is the host part.

- Class A default mask would be 255.0.0.0 or 11111111 00000000 00000000 00000000 In binary or /8 in prefix
- Class B default mask would be 255.255.0.0 or 11111111 11111111 00000000 00000000 In binary or /16 in prefix
- Class C default mask would be 255.255.255.0 11111111 11111111 11111111 00000000 In binary or /24 in prefix

### Number of Hosts per Network

As discussed before to determined the number of hosts per network we would use the following math formula
$$
2^H-2
$$
H would be equal to the amount of hosts bits we have in the network, for example in class C unsubnetted network
$2^8-2=254$ we would have 254 valid hosts.

### Deriving the Network ID and Related Numbers

Each classful network has four key numbers describing the network

- Network number (Network Address)
- Usable hosts Range (Lowest to Highest) (two numbers)
- Network Broadcast Address 

The lowest number in a network is always the network ID (Network Address) the network ID cannot be used as an IP address for a host. The broadcast address would be the biggest number in the host partition of the address it also cannot be used as an IP address for a host. Any address in between the network address and the broadcast address is a valid IP address that can be given to a host. 

You should be able to find with ease all the information following the next steps:

1. Determine the class (A, B or C) based on the first octet
2. Mentality dived the network and host octet based on the class
3. To find the network address (Network ID) change the IP address host octet to 0
4. To find the first address, add a 1 to the last octet of the network address.
5. to find the broadcast address change the network address host octet to be all 1 (255)
6. to find the last valid host take 1 off the broadcast address 

### Unusual Network Address and Network Broadcast Address

For class A the first odd thing is, we don't use the network 0.0.0.0 and 127.0.0.0 as they are both reserved so we skip them. the 127.0.0.1 is a special address called a loop back address. 

For class B its important to make sure we remember class B start from 128 and we don't confuse it with class A same for 191 the last number of the class B address. Same goes for class C as for example 223.255.255.0 is a valid class C address. 

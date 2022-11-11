# CCNA 200-301 Chapter 13

## Subnet Mask Conversion

### Three Mask Formats

Subnet mask can be written using 3 formats. Binary, 32 bits of 1's or 0's where the 1 represents a network or subnet and the 0 represents the hosts part.  The 0's always have to be on the left of the 1's. 

11111111 11111111 0000000 0000000

The second methods would be dotted decimal notation (like IPv4) it is still 32 bits but this time in octets of 8 bits with a maximum value of 255 per octet.

255.255.0.0

The last method would be the prefix or prefix mask, the term CIDR mask or slash mask can be used as well. The prefix would just be the number of 1's that are in binary. so if we have a 255.255.0.0 that is equal to  11111111 11111111 0000000 0000000 our prefix would be /16 because we count the amount of time we have a 1 bit. 

/16 

```
11111111 11111111 0000000 0000000 == 255.255.0.0 == /16
```

### Converting Between Binary and Prefix Masks

**Binary to prefix: ** Count the number of 1's to a sum. and write the total as a decimal number with a / before the number (/16 /30)
**Prefix to Binary** The number of the prefix is the number of 1 there are in binary. write the 1's from left to right in groups of 8 (11111111 11111111 0000000000 0000000000)

### Converting Between Binary and DDN Mask

The easiest way it to memorize the following chart:

| Binary Mask Octet | Decimal Equivalent | Number of Binary one's |
| ----------------- | ------------------ | ---------------------- |
| 00000000          | 0                  | 0                      |
| 10000000          | 128                | 1                      |
| 11000000          | 192                | 2                      |
| 11100000          | 224                | 3                      |
| 11110000          | 240                | 4                      |
| 11111000          | 248                | 5                      |
| 11111100          | 252                | 6                      |
| 11111110          | 254                | 7                      |
| 11111111          | 255                | 8                      |

A very easy to remember it, The first 4 bits from the left are equal to 240 and the first 4 bits from the right equal to 15. If we split the octet in the middle
128 64 32 16 8 4 2 1
$128+64+32+16=240$
$8+4+2+1$ = 15

### Converting Between Prefix and DDN Masks

This one might not be as easy, as we are looking to convert /30 to 255.255.255.252. The easiest way would be to find the closest *8 and fill in the rest. For example /20 is $8+8+2=20$ so the DDN would be 255.255.192.0 this might take some time. Another way would be to do prefix to binary and than to DDT but it takes much longer. 

### Identifying Subnet Design Choices Using Masks

Subnet masks can provide information about the network it is used in:

- Define the size of prefix part of the address in a subnet (Network and subnet combined)
- Define the host portion of the subnet 
- Can be used to calculate the number of hosts in the subnet
- An easy way to communicate, the size of the network, the number of hosts bits given to the subnet.
- Used to calculate the number of subnets in an entire classful network
- Can be used to calculate the network address and the broadcast address.

### Masks Divide the subnets Addresses in to Two Parts

The subnet mask subdivides the IP address that resides in the same subnet.
**Prefix(subnet) part:** Equal in all address in the same subnet
**Host part:** Different in all address in the same subnet 

The prefix would be combination of the network address and the subnet. the subnet combined with the network make a new network address.

### Masks and Class Divide Addresses into Three Parts

Another way to look at an IP address would be to split it to three parts, Network, Subnet and Host. Because we know the network part will always be the same depending on the class of the network. The combined network and subnet part acts as the prefix because the new network address has to be the same per subnet but the host part can be different. 

### Classless and Classful Addressing 

**Classless Addressing:** The concept that an IPv4 has two parts - the prefix part plus the host part as defined by the mask. with no consideration of the class A, B or C
**Classful Addressing:** The concept the IPv4 has three parts to it network, subnet and hosts as defined by the Class A, B or C

### Calculations Based on the IPv4 Address Format

For every subnet we know the hosts bits for we can calculate how many total hosts there are. If we know the number of subnet bits we can also calculate the number of subnets in the network.
**Hosts in the subnet:** $2^H-2$ where H is the number of host bits
**Subnet in the network:** $2^s$ Where S is the number of subnet bits (Only use this formula if only one mask is used thought the network)
For IPv4 the total is always 32 bit, so the prefix (network+subnet) and hosts or Network + subnet + hosts should always equal to 32bits

### Practice

P = Prefix N = Network S= Subnet H= Hosts

1. 8.1.4.5 255.255.254.0
   P=23 (8+8+7)
   N= 8.0.0.0 (8 bits for network)
   S=$8+7=15$     $2^{15}=32768$
   H=$2^9-2=510$

2. 130.4.102.1 255.255.255.0

   P = /24 (8+8+8)
   N = 16 (We know calss B network part is 16 bits)
   S= $2^8-2=254$ (its is 8 because we take the N part and minus our over all prefix that is 24)
   H = $2^8-2=254$ (32[overall bits]-24[our prefix]=8)

3. 199.1.1.100 255.255.255.0
   P = /24
   N = 24
   S= 0
   H = $2^8-2=254$ 

4. 130.4.102.1 255.255.252
   P= /22
   N= 16
   S = $2^6=64$
   H= $32-22=10$ , $2^{10}-2=1022$ 

5.  199.1.1.100 255.255.255.224
   P /27
   N= 24 (Class C)
   S= $2^3$ 
   H $32-27= 5$   $2^5-2=30$ 

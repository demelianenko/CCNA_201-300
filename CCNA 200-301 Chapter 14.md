# CCNA 200-301 Chapter 14

## Defining a Subnet

A IP subnet is a **subset** of a classful network. It is the cat of making more network address out of a bigger network address.
But it most follow the following rules:

- The subnet contains a set of consecutive numbers (1..2..3)
- The subnet holds $2^H$ number of subnets, H is number of the host part that were taken to be used in the subnet part.
- Two special numbers in the range cannot be used as regular IP address:
  -  The first (lowest number in each network) address, is reserved for the network address of the subnet
  - The last (highest number of the network[The next network address - 1]) is reserved for the broadcast address of that subnet. 
- The reaming IP address between the network address and the broadcast address can be used as unicast IP address and be given to hosts

### An Example with Network 172.16.0.0 and Four subnets

You are given the network 172.16.150.41 255.255.192.0
We know the 172.16.150.41 was originally a class B address so its subnet mask was /16 255.255.192 = /18 ($128+64=192$) Our networks would be in jumps of 64
Know that we know the 172.16.150.41 is an IP in the 172.16.128.0 network because the next network address would be 192. 

The first IP address would be the network + 1 = 172.16.128.1 and the last address would be the broadcast address - 1. 172.16.191.255 (minus one on the last network part)

The total number of usable IP address would be our 32 - our prefix to the power of 2
$32-18=14$     $2^{14}-2=16,382$  ==   $2^{10}=1024$ * $2^4=16$    $1024*16-2=16,382$ 

### Subnet ID Concept

| Key                 | Fact                                                   |
| ------------------- | ------------------------------------------------------ |
| Definition          | Number that represent the mask                         |
| Numeric Value       | First (smallest) number in the subnet                  |
| Literal Synonyms    | Subnet number, subnet address, prefix, resident number |
| Common-Use synonyms | Network, network ID, network number, network address   |
| Typically seen in   | Routing tables                                         |

### Subnet Broadcast Address

The broadcast address has two uses, The first its the address used to send packet to all the host in the subnet. It is also used to find the highest IP address in that subnet. 

| Key                  | Fact                                                         |
| -------------------- | ------------------------------------------------------------ |
| Definition           | A reserved number in each subnet that, when used as the destination address of a packet, causes the device to ward the packet to all other hosts on the subnet. |
| Numeric Value        | The last IP address in each subnet. The highest number       |
| Literal Synonym      | Directed to broadcast the packet to all other hosts in the subnet |
| Broder-Use synonym   | Network broadcast                                            |
| Typically used in,,, | In calculations of the range of the address                  |

### Range of the Usable Addresses

To find the range of unicast IP address in a subnet all we need is the network address and the subnetmask. The mask tells us what is the size of the subnets. The next subnet address -2 would give us the last usable unicast address.

## Analyzing Existing Subnet Binary

### Finding the subnet ID: Binary

The Subnet ID is always the lowest value in that subnet, All the numbers that are in the network part (subnet or classful) are the same. To find the Subnt ID of a subnet in binary all we need to do is covert all the 1's in the host portion to 0's

### Finding the Subnet Broadcast address: Binary

To find the broadcast address in binary all we need to do is set all the host bits to 1's. Another way would be to take the address of the next subnet and subtract 1 from it.

### Binary Practice problems

Steps:

1. Convert the mask to prefix format, to find how many bits are for the host
   32 - prefix = Host bits
2. Convert the IP address to binary
3. Copy the prefix bits of the IP address (the mask)
4. Write down the 0's for host
5. Convert th binary to 8 bits in a time back to decimal.

### Shortcut for the Binary process

We know that if the mask is 255 it means that number stays the same and cant be chanced as it part of the network. Because of that we don't have to convert that number to binary.

1.  If the mask = 255, copy the number as is
2. If the mask = 0, write down th number 0
3. If the mask is between 0 and 255 we have to convert the number and make all the 1' in the host part 0's

## Analyzing Existing Subnets decimal

### Analysis with Easy Masks

When easy msaks are used:
255.255.255.0
255.255.0.0
255.0.0.0
We can easily find the subnet ID and mask as we don't need to find any number only set the number to 0 or keep it. depending on the if the number is in the 255 octet or not.

### Predictability in the Interesting Octet

Because we know that the same size of subnet is used when we do subneting questions we can predict a few things.
mask 255.255.128 creates 2 subnets with 128 ips in them for exmaple.

### Finding the subnet ID: Difficult Masks

1. If the mask is 255, keep the number as is
2. If the mask is 0, the number would be 0 as well
3. Magic number 256 - the mask octet
   1. Set the mask ID to a closest multiple of the magic number

### Subnet Practice

1. 10.77.55.3 255.248
   10.72.0.0 - Network address
2. 172.30.99.4 255.255.192
   172.30.64.0
3. 192.168.6.54 255.255.255.252
   192.168.6.52
4. 10.77.3.14 255.255.128.0
   10.77.0.0
5. 172.22.55.77 255.255.254.0
   172.22.54.0
6. 1.99.53.76 255.255.255.248
   1.99.53.72

### Finding the Broadcast Address: Difficult masks

The easiest way would be to find the next network address using the magic number and minus one it we look at it logically the brodcast address would be when all hosts are 1's. 

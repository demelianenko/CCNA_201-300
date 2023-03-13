# CCNA 200-301 Chapter 18

## Problem Isolation Using the ping Command

### Ping Command Basics

The ping command is used to test connectivity in a TCP/IP network, The ping command sends packets that say if these packets were have your address on them send a reply back. We can use this command to check for connectivity to a host.

The ping command uses the ICMP protocol, ICMP stands for Internet Control Message Protocol, when we use the ping command we send an echo request to a host and we expect a echo reply back. If we do not receive a echo reply back there is an issue most likely on the layer 3 with that device or reaching to that device via layer 3. ICMP is its own layer 3 protocol and does not relay on TCP or UDP. 

The command is widely supported on many operating systems and devices, when we use the ping command we can give it a host name or an IP address we can also tell it how many echo request to send, as well as how long should we wait for a echo reply. We an also tell it how big to make the packet.

### Strategies and Results When Testing with the ping Command

Using a ping command from a router would not net us the same result as using the ping command from the node that has the issue we are troubleshooting. We can use different ping command to try to replicate the ping we would have done from the node that has the issue.

#### Testing Longer Routes from Near the Source of the Problem

When we face an issue where we have host x cannot communicate with host y a ping command is a good first step to trouble shoot the issue. Pining host Y from host X to see if we can get a response should be the first thing we try. If we don't have access to either host pining from the closest router to host X should be our first step. 

By default on IOS it will send 5 echo messages with a timeout of 2 seconds. If the ISO router/switch is not recived a reply back in two seconds it will mark the request is failed. 

the first ping request, very commonly failed as a result of the ARP request happing, when the hosts that send the request dosent have the mac address of the destination. 

The result of a successful echo request tells us a few things:

- The next hop from the host is able to receive the echo request
- The router/switch was able to forward the packet out of its out going interface.
- The other host was able to send the reply back to the other host
- If the hosts are separated by a router, we know the routes are configured correctly 

#### Using Extended Ping to Test Revers Route

When we ping from a router instead of the host we are losing the part of the path to the other host, if there is another switch and and another link before the router comes in to play and most likely will be sent from the closer interface. We would like to get the echo request sent from as close as possible to the host we cant access.

If we use the ping command with no parameters but just by itself we are able to pick from what interface we will send the echo request on the router. By doing so we can pick the closet interface to the host we cant access to get the most accurate picture. When we use the ping command we need to say yes when we are prompted for the extended option of the ping command.

Using an extended ping command and picking the interface that is on the same network as the host we are trying to fix would be the closet we can test with out send the request from the host itself. But even than it is not as perfect as send the request from the host itself as the request is not using the IP address of the host that is having the issue and we can run in to the following problems:

- IP ACL's that discard the packets with the source IP address of the host that has the issue. But don't discard the router interface packets.
- Lan switch port security that filters the frames the host sends based on its mac address
- IP routes that happen to match the IP address of the Host but not the IP of the router interfaces
- Problems with the default gateway settings on the host itself.

#### Testing LAN Neighbors with Standard Ping

Testing by pining another host on a lan network helps us rules out many things if the host replies.

- The host with the address we have pinged can be reached using the IP address we used
- The lan can pass unicast frames from one host to another and back (echo reply)
- We can reasonably assume the switch was able to learn the mac address of both hosts
- We can assume that the ARP process was successful

If the Ping from one host to another on the same lan network has failed it can tell us a few things:

- **IP Addressing problem:** The host we are trying to each might have a static IP address set to it that is not the same IP address we are pining.
- **DHCP Problem:** There might be a DHCP misconfiguration where the IP, default getaway or the mask are wrong
- **VLAN Trunking:** The router might be configured to use Dot1Q while the switch is not.
- **Lan Problems:** There is a big verities of issues that might result in layer 2 frames not being able to be sent over the lan.

#### Testing Lan Neighbors with Extended Ping

A standard ping from a router to a host, will not test the hosts default router settings, while an extended ping will be able to test the router settings of the host.

Using the extended ping we can choose what interface to send the ping form our router, using the extended ping and sending the echo request from an interface from another network will prompt the host to use its default gateway settings, and if the settings are wrong the echo reply will never be received. If an extended ping fails (and is sent from another network) that might suggest the default gateway settings on the host are not correct, while the same normal ping might work as the IP interface that send the request is in the same network so the host does not need to use its default getaway settings. 

#### Testing Wan Neighbors with Standard Ping 

Routers connected via a WAN serial/Ethernet link should be able to ping each other to confirm that IPv4 packet can be sent over the link. Just like hosts on a TCP/IP network. 
A successful ping between two routers that are on each end of a wan link tells us a few things:

- Both routers Wan interfaces are in up/up state
- The layer 1 and 2 features of the link work
- The routers believe that the neighboring routers IP address is in the same subnet
- Inbound ACL on both routers do not filter the incoming packets for that router IP addess
- The remote router is configured with the IP address we expect

### Using Ping with Names and with IP address

We can use the ping command with IP address or host names. Allowing us to test the DNS settings as well as the network connectivity. Pining both by host name and IP can help us isolate the problem. Is it a DNS problem or a networking layer problem?

### Problem Isolation Using the trace route Command

Like ping the traceroute command helps us isolate the problem Both commands:

- Both Send messages in the network to test connectivity
- Both rely on the other device to send a reply back
- Both have a wide support on many different operating systems
- Both can use a host name or an IP address
- On routers both have standard version and an extended version

The biggest difference between the two would be that traceroute has a more detailed output about its hops and the response time it took each hop to forward the request.

#### traceroute Basics

The traceroute command systematically helps pinpoint routing problems by showing how far a packet goes through an IP network before getting discarded. 

The traceroute command identifies router in the path from source to destination. The command lists each hop the traceroute has to take. 

#### How the traceroute Command Works

The traceroute command gathers the IP address of each hop by generating error messages where the TTL has reached the its minimum (64 is the default these days) The error message is an ICMP TTL exceeded message. The traceroute command sends packets with TTL = 1 to the hops in path of the traceroute command that prompts the routers to send a TTL exceeds to the source of the traceroute command. In that way we can see where the TTL packets have stopped and where they are being forwarded. Each time a hop answers with a TTL exceeded trace routes will add +1 to the TTL and send the packet out again. In the end we end up with an output with the IP address of each hop in the chain to the destination. Or we see where the traceroute didn't get a response.

As a result of the nature of traceroute to use the TTL to find the IP address of the hops, the IP address that the hop sends back to the traceroute host is the same IP address that the packet came on.

#### Standard and Extended traceroute

The extended traceroute command has the same options as the extended ping command. we can select out of what interface we would like to send the trace route command if we run it on a router. 

## Telnet and SSH

We can use Telnet and SSH to remote in to a device (router,switch) to check on its status and its settings.

### Common Reasons to Use the IOS Telnet and SSH Client

Common use for SSHing or Telning in to the router itself would be a routing error where we try to remote to a remote router and we are unable so we can use the closer router to use and hop from router to router if they are directly connected. If both interfaces on the same network the routes will be connected, and we can hop from router to router to the remote touter to fix the routing issues.

### IOS Telnet and SSH Examples

Using the telnet command in ISO is very simple just like in any other system

```
telnet [ip-address]
telnet [hostname]
```

We can use the commands:

```
exit
quit
```

to end the Telnet session.

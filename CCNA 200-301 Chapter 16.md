# CCNA 200-301 Chapter 16

## IP Routing

IP routing deliverers IP packets from the sending host to the destination host.

The complete end to end routing process relies on the network layer logic. The sending host uses layer 3 concepts to create an IP packet forwards the packet to its default gateway(default router). The router compares the destination address to their routing table and decided based on it where to forward the packet next. 

The routing process also relies on data-link and physical details of each link. IP routing relies on:

- Serial WAN Links
- Ethernet WAN links
- Ethernet Lan Links
- Wireless Lan's 
- And many others network implementation

These lower layer devices move the IP packets over the TCP/IP network's by encapsulating and transmitting the packets inside the data-link layer frames.

### IPv4 Routing Process Reference

The routing process starts with the host creating an IPv4 packet:

1.  If the destination is local, send directly:

   1. Find the destination host's MAC Address, use the already known IP address in combination with ARP (Address resolution protocol) If the address is not already in the hosts ARP table.
   2. Encapsulate the IP packet in a data-link frame, with the destination data-link address of the destination host.

2. If the destination is not local, send to the default gateway

   1. Find the default gateway's MAC address using ARP, if the address is not found in the ARP table.
   2. Encapsulate the IP packet with in the data-link frame, with the destination data-link address of the default gateway. 

   #### Routers routing logic

   1. For each received data-link frame, choose if to process the frame or not
      Process if:
      1. The frame has no errors, (per the data link trailer frame check[FCS])
      2. The frames destination data-link address (layer 2) is the address of the router, (or an multi cast address)
   2. If choosing to process the frame in step 1, de-encapsulate the packet from inside the data link frame
   3. Make a routing decision, in order to make the decision compare the packet destination address to your routing table. The outgoing interface and the next hop IP address.
   4. Encapsulate the packet back in to a data frame, look at your ARP address to find the address of the next hop data link address
   5. Transmit the frame out of the outgoing interface, as listed in the matched IP address
   
   The whole process can be summed up as:
   The router receives a frame, removes the packet from with in the frame. decides where to forward the packet to, the router puts the packet back to a new frame with a new mac address.
   
   #### Routers Routing Logic Summary
   
   1. Router R1 checks that the incoming frame has passed the FCS check, and that the destination of the frame is the mac address of R1. If both are correct the router will start processing the frame.
   2. R1 de-encapsulates the IP packet from the frame.
   3. R1 compares the IP packets destination address to its routing table
   4. R1 encapsulates the IP packets inside a new data-link frame.
   5. R1 transmits the IP packet inside its new data-link frame(header and trailer) out to the next hop 

### IP routing Exmaple

#### Host forwards the IP packet to the default gateway

- PC examines its IP address versus the destination IP address
- If the destination IP address is in another network the sender has to send the frame to the default gateway 
- The sender encapsulates the packet in to a frame with the mac address of the default gate way

The Packet would be de-encapsulated and re-encapsulated with a the next hop mac address until it reaches the destination.

#### Routing Step 1: Decide whether to Process the Incoming Frame

Routers handle many frames over their lan interfaces. The first step each frame goes through would be the integrity check. The router checks if the frame has any errors using the FCS field to check if errors were made during transmission. If errors occurred the router will discard the frames. The router does no error recovery, the router simply asks the sender to resend the frames that had errors.

After the frames have passed the error check the router checks to see the mac address that is listed in the frame header and trailer to see if the packet is intended for the router itself. The router can also be the end destination of the frame but most likely a host send the frame to its default gateway.

Routers will some times receive broadcast frames that are not intended for the router, when a switch docent have a mac address of a certain host it will broadcast to see if it will answer with it mac address. In that case the router will ignore such frames.

#### Routing Step 2: De-encapsulation of the IP packet

After the router knows it can process the frame, it de-encapsulates the IP packet out of the frame. The router no longer needs the header and trailer of the original frame so it discards them (the destination IP address in the packet stays the same).

#### Routing Step 3: Choosing where to Forward the Packet

This step involves the most logic for the router. The decision needs to be made to where to forward the packet. The router would have to match the destination IP address to its routing table. When looking at a routing table there are multiple routes and matching information. When matching the IP address the router looks at the destination IP address and compares it to each IP range it has in its routing table (the table has the network ID and the subnet mask of all the routes the router knows) After determining the correct network the router will encapsulate the packet to a new data frame with the mac address of the next hope router. Keeping the original IP address of the IP packet the same. And forward the packet by the instruction it matched in the routing table.

#### Routing Step 4: Encapsulating the Packet In a New Frame

The router now know where and to who he needs to forward the packet. The next step would be to wrap the packet in a frame header and trailer so it can be sent over a serial link(in this example). Because we are going to forward the packet over a serial link there are only 2 protocols that can be used HDLC(the deafult) or PPP. Becouse serial links are point to point we dont need to bother with any addressing. In this exmaple the router will encapsulate the IP packet in HDLC header and sent it over its S0/0/0 serial link.

If we were sending the frame over an Ethernet port we would need to include the next hop MAC address in the Ethernet header. The Mac address will change with each hop but the original IP address in the packet will stay the same. 

#### Step 5: Transmitting the Frame

After receiving the frame checking it, deciding where to forward it to. And packing it back to a frame we would need to simply send the frame over the routing instruction we have in our routing table.

## Configuring IP Addresses and Connected Routes

Cisco router have IPv4 routing enabled by default. In order to make the router routing able. We would need at least one interface with an IP address and for it to be UP/UP on its line and protocols status (layer 1 and 2 are good) Only after that can the router start routing IP packets in and out.

After the router is ready to route with one or more interfaces. The router would need routes to route the packets with.

- **Connected routes:** Are added to the router after an interface is configured with its IP address and is in the state of up/up. And are the route to the local router interface of the router itself. the routes are added as a result of the 

  ```
  ip address
  ```

  Command when it is used on an interface

- **Static Routes:** Are added when a network engineer adds them manually using the

  ```
  ip route
  ```

  Command on the local router

- **Routing Protocols:** Are added as a result of dynamic routing protocols. The routes are added as a result of the dynamic protocols sharing the routes between the routers.

### Connected Routes and the ip address Command

Cisco router automatically add its directly connected interfaces routes to it routing table. Assuming that they follow the following facts:

- The interface is up/up and working

- The IP interface has a valid IP address, that was given it using the:

  ```
  ip address [interfacenumber]
  ```

  subcommand

The router calculates the network ID from the subnet and the IP address given to the interface. The router needs to know the directly connected networks to it to route via them. The router only adds the connected route if the interface is in up/up state.Connected routs are represented via a **C** in the routing table. (The routing table can be displayed the show ip route command)

The router also makes a local route to each single IP interface. with a /32 prefix (subnet) that is the route directly to the router interface.
Afew more thing the show route command show us are:

- **Routing protocol:** Each routing proctols, if the route was added using one. Has a letter:
  	C - Connected, L - Local, S - Static, O - OSPF
- **Prefix:** the words prefix is just another name for netowrk ID
- **Mask:** each route has to have a network ID and a Mask

### The ARP Table on a Cisco Router

After the router adds its connected routes, the router can route IPv4 packets out of the connected routes/interfaces. To do so the router uses its IP ARP table, where there is a list of IPv4 address and their corresponding mac address on th routers networks. When routing a packet to a host on the same network as the router the router uses the mac address that is listed in the ARP as the layer 2 mac address in its data link frame. If the router does not have the mac address of the host its looking to forward the packet to, the router will send an ARP request to get the mac address of the host its looking for.

Dynamically learned mac address entries have a life of 35 minutes and than they get deleted. by default Cisco ISO will delete the entry after 240 minutes. if an entry has the time of **-** it means it wont expire. you can clear an mac address learned mac address using the command:

```
 clear ip arp [ip address]
```

1. The router looks in its ARP table for the ip address
2. The router encapsulates the IP packet in to a data link frame with the mac address of the destination host as the destination mac address.
3. The router transmits the frame.

## Configuring Static Routes

Static routes are routes that are added manually to the router. Unlike the connected or local or dynamic the static routes need to be manually added to each router.

### Static Network Routes

Static routes can be added to Cisco router using the:

```
ip route
```

Command. It is a global configuration command, the command would involve the network that should be sent over to reach this route and the means it should done via, a interface the next hop ip address.

A static route is considered a network route and would involve directing a whole class like A, B, or C or a whole network. Unlike the default route that would match any route that wasn't matched by any other route or a host route that routes only to a single host IP.

Examples of the IP route command, the first would use the connected to the interface of the router and the second would be the connected interface of the adjacent router interface IP.

```
ip route 172.16.2.0 255.255.255.0 S0/0/0
ip route 172.16.3.0 255.255.255.0 172.16.5.3
```

The first command reference's the outgoing interface of the sending router as the forwarding instructions and the second command uses the IP address of the receiving interface on the adjacent router. Both methods are valid. 

The command that uses the interface would show up as connected on the show ip route command its is just a qurick of the IOS.
IOS would reomove and add back the static routes depending if the interface is up or down. If an interface went down it will remove the static route and add it back when the interface is back up.

Static routes that are connected to Ethernet interfaces should use the next hop Interface IP address, because they packet might need to be forwarded and when we use the IP address of the next hop router we identify the next hop while when use the interface we do not.

### Static Host Routes 

Static host routes are mostly used to force a packet to certain host use a certain route. Because routers when matching and decided where to route a packet will always pick the most detailed subnet mask route. The longest prefix. As it being the most specif. Because /32 or 255.255.255.255 is the longest prefix we can choosing  it will always taker priority over the other routes even if packet matches other routes in the routing table. That way if we wanted a certain subnet to route via one interface but wanted one of the hosts to go troughs another host we could do so with the host route and /32 subnet mask.

### Floating Static Routes

When a static route is the same length as the other routes and the packets matches multiple routes the router will take in to account the administrative distance. Each route depending on how it was added to the routing table has administrative distance. by default static routes have the lowest administrative distance of 1 and OSFP has the distance of 110. The lower the administrative the better the route is in the eyes of the router. Some times you would prefer the static route to be a back up (a back up T1 serial link for emxaple, slow but cheap) such a thing would be called a Floating static route. The router will ignore the static route unless there is no route with lower distance.
For exmaple if we wanted our T1 backup static route to not be the priporety we can use the following command

```
ip route 172.16.2.0 255.255.255.0 172.16.5.3 130
```

The command would set the static route for the 172.16.2.0 /24 via the 172.16.5.3 interface but we give it the administrative distance of  130 so it wont take propriety over our 110 OSPF route. by default this route would have had the priority of 1 and would have been a priority over our OSPF route.

If we use the

```
show ip route [network id]
```

We can see the distance the route for that network is. while listing all routes wont display their distance.

### Static Default Routes

Some times the packet destination matches none of routing rules in the routing table. In that scenario the router will send the packet do its default route. If not configured the router will just discard the packet. The static route can be either statically made or learned dynamically. The default route will match all packets and that the reason that if no better match is seen in the routing table it will be sent via the default route.  

An example of that would be for a company with many remote location where the smaller location have limited bandwidth and would just use a default route to send all the packet to the central location that has much more equipment and bandwidth. 

IOS allows to create a deafult route using a special ip address that looks like the following 

```
ip route 0.0.0.0 0.0.0.0 [interface-name/target-ip-interface]
```

this command will forward all packets that didn't match any routes using the interface name or target ip interface.
We can identify the default route when using the **show ip route** command by looking at the ***** symbol.

### Troubleshooting Static Routes

Some thing that could cause issues when using static routes:

- The route is in the routing table but has the wrong values
- The route is not in the routing table
- The route is in the routing table and the route has the correct values, but the end host cant receive the packets.

#### Troubleshooting Incorrect Static Routes That Appear in the IP Routing Table

ISO is able to make a syntax check to make sure the correct commands are used when a static route or any other command is used. If the values the network engineer are poorly picked or incorrect the router would still add the route if the syntax is valid.
When looking a the show ip route and looking at static routes remember to check the following:

- Is there a subnetting error in the subnet mask or the network ID?
- Is the next hope IP address is correct?
- Does the next hope IP interface is on the correct router?
- Is the outgoing (if used) is correct?

#### The Static Routes Does Not Appear in the IP Routing Table

After a route is added to the router using a **ip route** command, the router might not add the static route. if one of the following is true:

* If the interface that has been mentioned in the command is not in the up/up state. The route wont be added.
* If the ip route has used the next hop ip address  as the target, if the router has no route to that interface the the route wont be added   (directly attached interfaces are added as connected)

The router needs an existing route to another router or a default gateway to be able to forward the packet. Like the connected routes. We can configure the router to ignore the basic check and always add the route no matter if the interface or next hop IP address are working. we would need to use the keyword permanent

```
ip route 172.16.3.0 255.255.255.0 172.16.5.3 permanent
```

Would add the route regardless if the router has a route to 172.16.5.3. if the interface that has the IP address 172.167.5.3 is down the route wont go away from the routing table because of the permanent keyword.

### The correct Static Route Appears but Works Poorly

Static routes are perfectly fine solution for routing, an incorrect static route is one of many thing that can effect the ability from one host to send packets to another one. as a result of th keyword permanent some routes will stay in the route table and match packets even if they cant be actually used as a result of the key word.

## IP Forwarding with the Longest Prefix Match

A routers routing process requires the router to match the IP destination of each packet to the routers routing table to decide where to forward the packet to. Often time only one route will match the packet destination and there is no question about where the packet should be sent as there is only one answer. 

But some times multiple routes can match the packets destination address, how does the router pick which route to send the packet when the packet matches multiple routes. When a packets destination is matched by more than one route the router will pick the most specific route (the biggest subnet mask [32 being the largest]) or in other words the route with the longest prefix.

### Using show ip route to Find the Best Route

The easiest way for us to identify what route the router will use for a certain IP address would be to use the 

```
show ip route
```

Command. For us to figure out what route a packet will take we need 3 pieces of information

1. The packet destination ip address
2. the network ID of the route
3. Mask of the route

/0 being the smallest mask (used in default route ) /32 being the largest and being used in host routes. the router will always pick the route with the largest mask (most detailed) route if multiple routes are overlapping and matching the same destination.

### Using show ip route address to Find the Best Route

Another way to find the best route for a ip packet destination address would be to use the 

```
show ip route [address]
```

The last part [address] would be the ip address of the destination packet we are looking to find the best route for.
This command is much quicker and requires no subnetting.

### Interpreting the IP Routing Table

Knowing how to read and use the

```
show ip route
```

Is a big part of troubleshooting routing.

![image-20221118175021184](/home/d/CCNA_201-300/Chapter-16-show-ip-route)

#### **show ip route reference table:**

 

| Item | Idea                     | Value in the figure | Description                                                  |
| ---- | ------------------------ | ------------------- | ------------------------------------------------------------ |
| 1    | Classful network         | 10.0.0.0/8          | The routing table has organized by classful networks. This line is the heading line for the network 10.0.0.0 as a class A its subnet mask is /8 |
| 2    | Number of subnets        | 13 subnets          | The number of routes for subnets of the classful network known to this router, from all sources. including local routes - the /32 routes that match each router interface IP address |
| 3    | Number of masks          | 5 masks             | The number of different masks used in all routes known to this router inside this classful network. |
| 4    | Legend Code              | C, L, O             | A short code that identifies the source of the routing information. O is for OSFP, D for EIGRP, C for Connected L for Local. |
| 5    | Prefix (Network Address) | 10.2.2.0            | The Subnet number of this particular route.                  |
| 6    | Prefix Length (Mask)     | /30                 | The prefix mask used with subnet.                            |
| 7    | Administrative Distance  | 110                 | If a router learns routes for the listed subnet from one or more sources of routing information. the router uses source with the lowest administrative distance. |
| 8    | Metric                   | 128                 | The metric for this route                                    |
| 9    | next-hop router          | 10.2.2.5            | The IP address where the router will send the packets that match the route. |
| 10   | Timer                    | 14:31:52            | For OSPF and EIGRP routes. this is the time since route was first learned. |
| 11   | Outgoing interfaces      | Serial0/0/1         | For packets matching this route, the interface out which the packet should be forwarded. |




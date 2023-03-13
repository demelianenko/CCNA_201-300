# CCNA 200-301 Chapter 18.5

## Not in the Book but might be on the exam

## Dynamic Routing & RIP, EIGRP

### Dynamic Routing

Unlike static routing, dynamic routing is not done by hand like static routing is. We configure a dynamic routing like RIP, EGRP or OSFP.

 Dynamic routing protocols share their network routes with each other. A network route is a route in the networking table that has a mask less than /32 on the other side a host route would be a route to a single host and would be any route with a /32

Routers can use dynamic routing protocol to advertise information about the routes they know to other adjective/neighboring routers. And the other routers will share the route to their adjacent routers. If multiple routes to a destination are learned, the router will determine the better route by using metric.

If a link goes down the routers will adapt and remove the route from the routing table.
If we were using static routes and a link went down, the router will keep sending packets over that route.

### Types of Dynamic Routing Protocols

There are two types of Dynamic Routing protocols:

1. IGP - Interior Gateway Protocol
2. EGP Exterior Gateway Protocol

IGP is used to share routing within a single Autonomous system, which is a single organization (company). 
IGP Has two Alogorithm types:

- Distance Vector:
  - RIP (Routing Protocol)
  - EIGRP (Enhanced Interior Gateway Routing)
- Link State:
  - OSPF (Open shortest Path First)
  - IS-IS(Intermediate System to Intermediate System)

EGP is used to share routes between different autonomous systems.

EGP only has one Algorithm type and only has one EGP that is in use today. its BGP (Border Gateway Protocol)

### Distance Vector Routing Protocols 

Early examples of Vector protocols are RIPv1 and IGRP.
Distance vectors work by sending the following to their directly connected neighboring routers.

- Their known destination networks
- Their Metric to reach their known destination networks

This method is often called sharing by rumor. Because the router does not know the networks beyond its connected networks. And only learns the distance (metric) and the Vector (Direction of where to send the traffic for that network) for each route.

### Link State Routing Protocols

When using a link state touting protocol, every routers creates its own connectivity of the network. To be able to do so each router advertises information about its interfaces to its neighbors.  These advertisements are passed along to other routers. until all routers in the network end up with the same map. Each router independently uses the map to calculate the best routes to each destination. Link state protocols use more resources than vector distance. 

Link state protocols end to be faster in reacting to a network change where a link might go down.

### Administrative distance 

A routers routing table contains best routes to each destination network the router know about. If a router uses a dynamic routing protocol and learns two different routes to the same destination  it uses the metric value of the routes to determined which is best. A lower metric is better

Each routing protocol uses a different metric to determine which routes are best. 

If a router learns two routes via the same routing protocol to the same destination with the same metric cost for both routes. It will load balanced the traffic over both of the routes.

### Dynamic Routing Protocol Metric 

| IGP   | Metric                         | Explanation                                                  |
| ----- | ------------------------------ | ------------------------------------------------------------ |
| RIP   | Hop Count                      | The total metric is the total number of hops to the destination. Links of all speeds are equal |
| EIGRP | Metric Based Bandwidth & Delay | By default the bandwidth of the slowest link is taken and the total delay of all the links |
| OSPF  | Cost                           | The cost of each link is calculated based on its bandwidth. The total cost is cost of all the links added |
| IS-IS | Cost                           | The cost of all links, by default each link cost is set to 10 |



### Floating Static Routes

By changing the AD of a static route you can make it less preferred than dynamicallys learned routes and to use the static route as fail safe, last resort route. If all other routes fail the router will use the floating static route.

The route will be inactive unless there are no other better routes.  



### Administrative distance 

| Route Protocol/type   | AD   |
| --------------------- | ---- |
| Directly connected    | 0    |
| Static                | 1    |
| External BGP (eBGP)   | 20   |
| EIGRP                 | 90   |
| IGRP                  | 100  |
| OSPF                  | 110  |
| IS-IS                 | 115  |
| RIP                   | 120  |
| EIGRP (external IGRP) | 170  |
| Internal BGP (iBGP)   | 200  |
| Unusable route        | 255  |

## RIP & EGIRP

### RIP - Routing Information protocol

RIP is a distance vector IGP (internal gateway protocol) that use routing by rumor logic to learn/share routes. RIP uses hop count as a metric. One router= one hope. The maximum hop count is 15 (anything pass 15 hops wont be reachable and wont be added to the routing table)

RIP has 3 versions

1. RIPv1 and RIPv2 used for IPV4
2. RIPpng (RIP next generation) used for IPv6

RIP uses two types of messages:

- Request: used to ask RIP-enabled neighbor routers to send their routing table
- Response: used to send local routers routing table to neghboring routers.

By default RIP enabled routes will share their routes every 30 seconds. 

### RIPv1 V RIPv2

#### RIPv1

RIPv1 only advertises classful address and only classful address and wont support VLSM and CIDR.

RIPv1 will ignore any subnet mask and will only look at the classful first few bits making every 10.0.0.0 network a class A network. 

#### RIPv2

RIPv2 broadcasts its messages to 255.255.255.255

RIPv2 support VLSM and CIDR, by doing so includes the subnet mask in its advertisements.

Sends its messages to a multi cast address 244.0.0.9

### Basic RIP configuration

First command would enable RIP on the router

```
router rip
```

Afterwards we will tell the router what version to use 

```
version 2
```

We need to disable auto summary, because auto summary will convert the network to classful networks

```
no auto-summary
```

The network command tells the router to

- look for the interfaces with an IP address that is in the specified  range. It will active RIP if it find interface with the given range. The network command is a classful command so when looking for interfaes it will look at classful address.
- It will form adjacencies with connected RIP neighbors
- It will advertise the prefix of the interface (not the classful prefix of the network command, the real prefix) OSPF and EIGRP network commands works in a very simliar way

#### Example of Network command:

We will use the following command as an example:

```
network 10.0.0.0
```

- Because the network command is classful command it assumes the network address is a /8
- The router will look for interfaces with IP address in the range of 10.0.0.0 /8 it will match all address with the same 8 bits
- The router will than form adjacencies with its neighbors 
- The router will advertise the real prefix of the interfaces and not the /8 when sharing its route for example /30

The network command only tells the router what interfaces to enable RIP on (use classful address) and than the router will share its routes with its neighbors with the real prefix of the interfaces 

If you enable RIP on a hot facing interface (not connected to another router) it will still broadcast RIP messages on that link. We can fix it by making the interface passive. We can do so using the following command in RIP configuration mode:

```
passive-interface g2/0
```

The command will only stop the router of sending RIP advertisements on the link. The route will still be advertised to its neighbors. 



#### Advertise default route using RIP

If we have a static default route we want to advertise using RIP we can do so. By using the following commands in RIP config mode.

```
deafult-information originate
```

#### Show IP Protocol

The show IP protocol is a useful command that can help us with dynamic routing protocols.

#### Maximum same metric routes

By default RIP will only add up to 4 routes of the same metric and will load balance over them. We can adjust the number using the following command:

```
maximum-paths #number
```

We can change the AD of the RIP using the distance command

```
distance #number
```



## EIGRP

stands for Enhanced Interior Gateway Routing Protocol, It is Cisco propitiatory. It is considered an advanced hybrid distance vector routing protocol. It is much faster than RIP in reacting to changes in the network. Does not have 15 hop count limit like RIP.

EIGRP sends its message using the multicast address 244.0.0.10

It is the only IGP that can do uneuqal cost load balancing.

#### EIGRP Confiugration

Enable EIGRP

```
router egirp #AS number
```

The AS number has to match between all routers or they wont share their routes.

Afterwards we follow it up with no auto summary to make the commands not classful

```
no auto-summary
```

We follow it up with a passive interface command, we would use the passive if the port is host facing and not router facing. To stop it from broadcasting on that port. 

```
passive-interface #interface-number
```

We will follow it up with the same network command we used in RIP. This will tell the router to enable EIGRP on the interfaces with the matching classful network we provide it.

```
network #network-ip-address
```

We can use  a mask with EIGRP but if we dont it will  assume its a classfull IP address.

```
network 172.16.1.0 0.0.0.15
```

#### Wilcard Mask

EIGRP will use a wild card mask, is just an inverted subnet mask, we put 0 where a 1 would be.
255.255.255.0 would be 0.0.0.255. The easiest way would be to subtract 255 from each octet in the mask.

A 0 in the wildcard mask means the bit must match. A 1 means the bit does not have to match.

### EIGRP Protocol information

When using the show ip protocol we can see the following information.

- The router ID: In EIGRP the router has a unique router ID, that is used to recognize it in a AS. The router ID can be manually configured, if its not. It will be the highest loop back interface. If there are no loop back interfaces. It will be the highest IP address of physical interface. Not its not actually a IP Address but just a string to identify the router.

### EIGRP Commands

we can use the following command to see the neighboring eigrp

```
show ip eigrp neighbors
```

Will show us the eigrp neighbors and what interfaces.

We can also use the following command to see the whole eigrp topology

```
do show eigrp topology
```

We can also filter the **ip route** comaand to only show us eigrp

```
show ip route eigrp
```

### EIGRP Metric

by default EIGRP will use the bandwidth and delay to calculate the metric of each link.
The metric formula will look like this: Bandwidth (of the slowest link in the chain) + delay (of all links)



### EIGRP Terminology 

- Feasible Distance: The routers metric value to the destination (this routers metric value for that destination)
- Reported Distance: (Aka Advertised Distance) The neighbors  routers metric value to the routes (the routers that shared the route metric)
- Successor: the route with the lowest metric to the destination (best route) (can be multiple if it has the same cost)
- Feasible Successor: an alternate route to the destination (not the best route) which meets the feasibility condition (Feasibility condition:
  A route is considered a feasible successor if its reported distance is lower than the successor routes feasible distance )

### EIGRP Unequal Cost Load Balancing

We can use the variance option in egrip that is used, in load balancing. The variance variable is a multilayer that by default is so to 1. Meaning EIGRP will only load blance routes with the same cost. If we change it to 2 using

```
variance 2
```

Now EIGRP can load balance routes with up to 2 times of the best costed route.

EIGRP will only do unequal load balance if the other rotue is a feasible successor. It will never be able to do so if the second route is not a feasible successor.

As the feasible successor is a loop prevention method, and the router cant ignore it.

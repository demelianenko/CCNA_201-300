# CCNA 200-301 Chapter 19

## Foundation Topics

### Comparing Dynamic Routing Protocol Features

Routers can add routes to their routing tables in 3 methods connected routes, static routes and dynamic routes.

**Routing protocol:** A set of rules and protocols that routes use to build routing tables dynamically. To choose the best route to the destination. Examples include:  RIP,EIGRP, OSFP
**Routed protocol and routable protocol:** Both terms refer to a protocol that defines a packet structure and logical addressing.
allowing routers to forward packets, routers forward packets defined by routed routable protocols like IPv4 and IPv6

### Routing Protocol Functions

Cisco IOS software support several IP routing protocols, preforming the same general functions:

1. Learn routing information about IP subnet from neighboring routers.
2. Advertise routing information about IP subnet to neighboring routers.
3. If more than one possible route to the same destination exists, pick the best one.
4. Adjust to any topology changed in the network, if a link fails and there is another route. The none working route will be dropped and the working one placed instead of it.

### Interior and Exterior Routing Protocols

IP routing protocols fall in to two  major categories interior gateway protocols and exterior gateway protocols.
**IGP:** A routing protocols that is designed to be used inside a single autonomous system 
**EGP:** A routing protocols that was designed to be used between autonomous systems

An AS (autonomous system) is a network under the administration of a single organization. For example a network created and paid for by a single company. Each ISP is typically its on AS. Some routing protocols work best inside a single AS so these routing protocols are called IGP. While on the other hand routing protocols that are made to be used between AS's are called EGP in todays age BGP (Border gateway protocol) is the only EGP that is used.

Each AS can be assigned an AS number, like public IP adders the AS number cam be given out by the IANA it delegates that authority to other organizations in other parts of the world. Usually the same one that would give out IP address in that part of the world.

### Comparing IGP's  

#### IGP Routing Protocol Algorithm

A routing protocol's underlaying algorithm determines  how the routing protocol does it job. The term routing protocol algorithm simply refers to  the logic and process used by the different routing protocols to solve the problem of learning all routes and choosing the best route to each subnet. Three main branches of IGP routing protocols exist:

- Distance vector (Bellman-Ford)
- Advanced distance vector (balanced hybrid)
- Link-State

### Metrics

Routing protocols choose the best route to reach a subnet by choosing the route with the lowest metric. 
IGP Metrics:

| IGP   | Metric          | Description                                                  |
| ----- | --------------- | ------------------------------------------------------------ |
| RIPv2 | Hop Count       | The number of routers (hops) between the router and its destination |
| OSPF  | Cost            | The sum of all interface cost settings for all links in the route, with the cost by default being the bandwidth |
| EIGRP | Bandwidth&Delay | Calculated based on the routes slowest link and the sum of all the delays in each interface along the route |

### Other IGP comparison 

| Feature                                             | RIPv2 | EIGRP       | OSPF |
| --------------------------------------------------- | ----- | ----------- | ---- |
| Classless/ send mask in updates & support VLSM      | Yes   | Yes         | Yes  |
| Algorithm                                           | DV    | Advanced DV | LS   |
| Support manual summraztion                          | Yes   | Yes         | Yes  |
| Cisco proprietary                                   | No    | Yes         | No   |
| Routing updates are sent to a multi cast IP address | Yes   | Yes         | Yes  |
| Convergence                                         | Slow  | Fast        | Fast |

*CIsco did publish EIGRP in 2013 allowing other vendors to implement it 

 Routing protocols can be considered to be classless routing protocol or classful routing protocol. Classless routing protocols support variable length subnet masks (VLSM)  as well as manual summarization by sending routing protocol messages that include the subnet mask in the message. The older routing protocols like RIPv1 and IGRP are classful protocols. As well as being classful they send their messages as broadcasts and not multi cast.

### Administrative Distance  

Most companies usually use one routing protocol internally, when a companies connects to another company network and the two companies use different routing protocols. If for example company A uses OSFP and company B uses EIGRP they routes from company B would need to be advertise to company A and used with OSFP in a process called route redistribution.

Sometimes the 2 routing protocols will learn route to the same subnet but via two different routes. The metric will tell the router which route is the best one. However if the same route is been discovered by two different routing protocols with two different metrics the router cant decide which one is better as its only using one of the protocols. There is simply no comparison base for learning the same route from 2 protocols. 

When the router has to pick a route that has been discovered  by two different routing protocols, the router uses administrative distance.  AD (administrative distance.) is the value given to each routing protocol, the lower the value the more the router trust that routing protocol and will prefer routes from it if available. For example RIP has an AD of 120 and OSFP of 110 if a router got the same route from both it will pick the one from OSPF  because of its lower AD. The AD values are not exchanged between the routers and can be changed on each router as well.

| Route Type       | Administrative Distance |
| ---------------- | ----------------------- |
| Connected        | 0                       |
| Static           | 1                       |
| BGP              | 20                      |
| EIGRP (internal) | 90                      |
| IGRP             | 100                     |
| OSFP             | 110                     |
| IS-IS            | 115                     |
| RIP              | 120                     |
| EIGRP (external) | 170                     |
| iBGP             | 200                     |
| DHCP             | 254                     |
| Unusable         | 255                     |

### OSFP Concept and Operations

####  OSFP overview

Link state protocols build IP routes with a couple of major steps. First, the routers together build a lot of information about the network. Routers, Links, IP address, status information. The routers flood the information so the all the routers have the same information. At that point the each router can calculate routes to all subnets. but from each own router perspective. 

#### Topology Information and LSA's

Routers using link state routing protocols need to share all the information about the internetwork they know to one another. So each can build a full network map. The routers flood the information to one another resulting in each router having the same information.

OSPF is the most popular link state protocol it organizes topology information using LSA's and the link state data base. The link state data base(LSDB) is made out of link-state advertisement (LSA) that each holds data structure with some information about the network topology. the LSDB is simply the collection of all the LSA's know to a router.

The LSA is being flooded in a circle from router to router before sending the LSA the router will ask the other one if they already have received this LSA, if they didn't it will send it to them. Routers will re flood an LSA if some information about the topology has changed. If a link goes down or up.

#### Applying Dijkstra Math to Find the Best Routes

Sharing and holding the information about the topology still doesn't tell us what routes to add to the routing table. To find out the best route each router will need to apply some math to the information has in its LSDB. Links state protocols like OSFP use the Dijkstra Shortest Path FIRST (SPF) algorithm. To prcoess the LSBD. That algorithm analyzes with math the LSBD and builds the rotues that the local router should add to its IP routing table. Routes that list a subnet number and mask, out going interface and next hop router address.

Now lets see the three main phases of how OSPF router exchange LSA's and calculating routes. 

- Becoming neighbors: A relationship between two routers that are connected via link. Created so the routers can exchange information. two routers must first be neighbors and only then, can they become adjacent.
- Exchanging Data Bases: The process of each router independently sending LSA's to neighboring so all routers learn the same LSA's
- Adding the best route: The process of each router independently running SPF (math algorithm) on their local copy of the LSDB calculating the best routes, adding thoes routes to the IPv4 routing table.

### Becoming OSPF Neighbors

#### The Basics of OSPF Neighbors

OSPF neighbors are two routers that both use OSPF and both are physically connected via a data link. Two routers can become OSPF neighbors if they are connected to the same VLAN, or same serial link, or same Ethernet WAN link. Neighbors are the routers that are directly accessible through directly connected interface.

After being conncted to the same link, the routers have to send OSPF messages and agree to become OSPF neighbors. To do so the router will send OSPF hello messages, assuming both the routers have compatible OSPF settings they will become neighbors. And the result will show using the command

```
show ip ospf neghbors
```

The OSPF neighbor relationship also allows router to know when its not a good option to send the packets via their neighboring routers. If a router notices that its neighboring router relationship has failed this makes the router react resulting in re-flooding of LSA's impacted by the failed link and recalculates the routes on its own routes. 

The OSPF neighbor model allows new routers to dynamically be discovered. We can add a new router to the OSPF network with out needing to configure each existing router. The OSPF router listen to the OSPF hello messages from new routers and react to thoes messages. attempting to become  neighbors.

#### Meeting Neighbors and Learning Their Router ID

The process of becoming neighbors start with messages called, OSPF hello messages. The hello messages contain the routers router ID (RID) that serves as each router unique identifier. OSPF makes several checks of the hello messages and the information inside them, to make sure the routers can become neighbors.

The OSPF RID are 32-bit numbers, by default IOS chooses one of the router interfaces IPv4 address to use as its RID. However the RID can also be manually configured.

As soon as the router has its RID and has interfaces up it is up and ready to start making OSPF neighbors, OSPF routers can becomes neighbors if they are connected to the same subnet. The router will send multi cast hello messages on its link and hope to get back OPSF hello messages back on the same links.

The routers keep sending the OSPF hello messages on a a regular interval, based on their hello timer settings. The messages have the following features:

- The Hello messages follows the IP packet header, with IP protocol type 89
- Hello packets are sent to a multi-cast IP address 224.0.0.5, a multi-cast IP address intended for all OSPF speaking routers
- OSPF routers listen for packets sent to IP multi-cast address 224.0.0.5. In part hoping to receive Hello packets and learn of new neighbors.  

##### Early Neighbor States

The process of becoming neighbors start with both links being down and both router not being aware of each other. When the links comes up the router will send Hello packets to the other side of the link. The router that got the hello packet learns of the existence of the sender router. At this point the router that got the hello packet lists the sender as neighbor with in an interim beginning state of init.

At this point the router that first got the hello packet and discovered the existence of the other sender router and listed the sender in its neighbor with in an interim beginning state of init. Will send a hello packet back to the sender. Telling the sender that the receiver exists. After both routers have moved each other to their init state, they will move to the 2 way state.

**Link comes online -> receives hello packet -> put the sender router in its init list -> send hello packet -> ordinal sender now puts the second router in its init list -> 2 way state can begin.**

After both routers have seen each other and put each other in neighbor list in init state the following are true:

- The router received a Hello packet from its neighbor with its own RID listed as being seen by the neighbor.
- The router has checked all the parameters in the Hello it received from its neighbor and found no issues. The router is willing to become a OSPF neighbor.
- If both router reach a 2 way state with each other, it means both routers meet all the requirements to become OSPF neighbors. At this point the router will establish their relationship and become neighbors and start exchanging LSBD's with each other.

### Exchanging the LSDB Between Neighbors      

One of the main purposes of the Neighboring relationship is for the two neighboring router to exchange their data bases. 

#### Fully Exchanging LSA's with Neighbors

The 2 way state means the router is ready to exchange its database with its neighbor. Once 2 routers  move to a 2 way they can immediately start exchanging data bases (LSDB's)

After two router decide they will exchange their databases. They do not simply start sending streams of the databases to each other. The router first send each other a list with all the LSA's they have, each router reviews the List and only picks the LAS's its missing. Than the router will reuqest the missing LSA's and not the whole data base. 

When router update each other with the entries the other router is missing from the LSDB, that message is called a Link state update (LSU) in LSU has LSA (link state advertisement) in it. The LSA is not a packet but a data structure contains the network information.

-  Router exchange LSA's that are inside LSU packets.
- When the router are finished exchanging the LSU's they reach a full state, meaning the have fully exchanged their respective LSDB's and currently hold the same "map" of the network.

#### Maintaining Neighbors and the LSDB

Once the neighbors reach full state they have done all the initial work of becoming OSPF neighbors. But now in order to maintain the relationship. They most keep doing small ongoing tasks to keep the  relationship going.

Routers monitor each neighbors relationship using hello messages, and two timers. The hello interval and the dead interval. Routers send out hello messages at the hello interval and if the router doesn't respond by the dead interval that usually 4 times the hello interval the relationship is broken.

routers must react when the network topology changes. When a topology change happen the router will flood out an LSA to its neighbors letting them know of the change that happened. when a change happens to the local router like a link its directly to goes down the router will flood LSA's to its neighbors about the link going down and they will flood to heir neighbors updating all the routers in the network about the change.

A third maintenance task done by neighbor is to re-flood LSA occasionally even if a network change did not happen. By default each router that creates an LSA has the responsibility to re-flood it every 30 minutes (since its creation) by default. Even if no change has occurred to that record. 

The three maintenance tasks summarized are:

- Maintain neighbor state by sending hello messages, based on the hello interval and listening for Hello before the dead interval expires
- Flood any changed LSA's to each neighbor
- Re-flood unchanged LSA's  as their life time expires (default 30 minutes)

#### Using Designated Routers on Ethernet links

OSPF behaves differently depending on the interface it is used on. OSPF has per-interface setting called OSPF network type. On Ethernet links OSPF defaults to use a broadcast network type. Which causes OSPF to elect one router as the designated router. The designated router plays a key role in the exchange of LSA's with different rules from a point to point links.

The routers elect one designated router and one backup designated router. When OSPF is in the broadcast network type routers to not exchange their LSA's directly with their neighbors but send the LSA's to the DR (designated router) and he sends them out to the other OSPF routers. Because the DR is so important OSPF has the BDR (backup designated router) in case the DR fails. as the network most keep sending LSA's in order to keep its network map up to date. The BDR watches over the status of the DR and takes over if the DR ever goes down. And a new BDR will be elected in the place of the BDR that took the place of the DR making sure there is always one DR and one BDR.

The use of DR&BDR in combination with multi-cast address. makes the exchange of LSA's very efficient on networks that allow more than two routers on the same link. The DR can send packets to all the OSPF routers in the subnet by using a multi cast address, rather than sending out the packets to one router at a time. Each router that needs to send messages to the DR can send them to the BDR as well so the BDR is ready to take over using a multi cast address.

Send to all OSPF routers - 224.0.0.5
Send to all DR and BDR - 224.0.0.6

When a router will send it message to the 224.0.0.6 multi cast address both the DR and BDR will get the message.
Because of the structure of OSPF on Ethernet links only the DR and BDR  will get to a full neighbor state with all the routers. Any router that is not a DR or BDR (called DROther) will never reach full state because they do not exchange LSDB's with the other routers only with the DR and BDR. 

As a result when we use the:

```
show ip ospf neighbor
```

on routers that are DRother (not DR or BDR) we should see some neighbors in a 2 way state. The BR and BDR will shows as fully adjacent neighbors while all the other should show in 2 way state.

OSPF requires to have a name for all neighbors even those who do not reach full state. All OSPF routers on the same link (connected via a link) can reach 2 way state. As a result of sending hello messages and having matching parameters and are called neighbors. The neighbors that continue pass the 2 way state are called adjacent neighbors. The term neighbor refers to OSPF neighbors while adjacent refers to full state. 

| Neighbor State | Term for Neighbor                               | Term For Relationship |
| -------------- | ----------------------------------------------- | --------------------- |
| 2-way          | Neighbor                                        | Neighbor Relationship |
| Full           | Adjacent Neighbor<br /> Fully Adjacent Neighbor | Adjacency             |



### Calculating the best Route with SPF

The LSA's that contain valuable information about the network topology but the router cant just use that information and plug it in its routing table. The router must calculate the best routes using math from its own perspective and location on the network. After applying SPF (shortest Path First) math to the LSBD the router will add to its routing table a route with subnet number and a mask, an outgoing interface and next hop router IP. for each subnet.

We do not need to know exactly how the match works but we do need to know what route is most likely to get added.
The SPF algorithm will calculate the paths to each subnet, if there are multiple viable paths the router will compare the metric of each route and pick the best one (lowest metric). 

Once the SPF has identified a route, OSPF calculates the metric as follows:

- The Sum of the OSPF interface cost for all outgoing interfaces in the route. (OSPF only considers the out going not incoming interface cost)

A network engineer with a network diagram should be able to tell what interface OSPF will pick. 

## OSPF Areas and LSA's

 If one so wishes OSPF can be very easily used, enable OSPF on all router interfaces add all the routers to the same Area and it just works.

The larger the network the more it will suffer being in a single area. A network with 900 routers and thousands of subnet will turn up the cpu usage as wells the RAM usage to the point that it might run out. On top of OSPF convergences time being very slow from all the changes it will need to react to. Large single area disgn will also:

- Require more memory for reach router to hold the larger toplogy
- The SPF algorithm require exponentially more CPU power the bigger the network is in the single area.
- A single interface going down will force each router to recalculate the path to that subnet.

The solution to this issue is to take the one massive OSPF area and to break it in to smaller chunks. SPF does its math on the topology inside a area and that area topology only. So SPF will only do the match one toplogy per area. Network with a few dozen routers will already see benefit in using multiple areas. But there is no real number as it will all depend on the CPU and RAM the router have.

### OSPF Areas

OSPF area design follows basic rules

- Put all interfaces connected to the same subnet inside the same area
- An area should be made of routers next to each other (contiguous)
- Some routers may be internal to an area with all interfaces inside the area.
- Some routers may be  Area Border Routers (ABR) because some interfaces connect to the backbone area and some must connect to the none backbone area.
- All non-backbone areas must have a path to reach the backbone area (0 by default) by having an ABR connected to their area.

#### OSPF Design Terminology

| Term                     | Description                                                  |
| ------------------------ | ------------------------------------------------------------ |
| Area Border Router (ABR) | An OSPF router that has at least one interface connected to the backbone area and at least one that is connected to a non-backbone area. |
| Backbone Router          | A router that is connected to the backbone area (includes ABR) |
| Internal Router          | A router that is only in one area (excluding backbone area)  |
| Area                     | A set of routers and links that share the same detailed LSDB information, but not with routers in other areas, for better efficiency. |
| Backbone area            | A special OSPF area that each other area has to connect to via a ABR. By default area 0 |
| Intra-area route         | A route to a subnet inside the same area as the router       |
| Inter-area route         | A route to a subnet to which the router is not part of.      |

### How Areas Reduce SPF Calculation Cost Time

SPF spends most of its processing time working through all the topology details, routers and links that connect routers. Having more than one Area reduces SPF work load because the router will only have to calculate the topology in that given area and wont do it for the rest of the network that might be in other area's. 

While the LSBD will have less information when multiple areas are used, it still needs to have information about all the subnet's in all the area's. So that each router can make its IPv4 routes to each subnet, OSPFv2 will briefly summarize the information about subnets in other area's. This summaries do not include topology information about other area's but only the Subnet ID, Mask of the subnets that are not in its area. LSA's do not require much SPF processing at all instead all of the paths that are outside the router area will look like subnets connected to the ABR (Area Border Router). 

Using multiple area's improves the OSPF operations especially in larger networks. The following highlights the key points of the adventages of using multiple areas in larger OSPF networks:

- Routers require fewer CPU cycles to process the smaller per-area LSDB with SPF algorithm, reducing CPU overhead and improving converges times.
- The smaller per-area LSDB require less memeory.
- Changes in the network require SPF calculations only on routers in the area where the link has changed state. Reducing the number of routers that must rerun SPF as a result of a change.
- Less information must be advertised between areas, reducing the bandwidth required to send LSA's   

### OSPFv2 Link-State Advertisements 

LSDB for an area would consist of information about routers and the links between these routers. OSPF defines the first two type of LSA's with those exact parameters:

- One router LSA for each router in the area
- One network LSA for each network that has a DR plus one neighbor of the DR (designated router)

The ABR creates summary information about each subnet in one area to advertises to the other area routers. The inforamtion being only subnet ID and mask.

- One summary LSA for each subnet ID that exists in a different area

#### The Three OSPFv2 LSA types seen with Multiarea OSPF Design

| LSA Name | LSA Type | Primary Purpose                   | Contents of LSA                                              |
| -------- | -------- | --------------------------------- | ------------------------------------------------------------ |
| Router   | 1        | Describes a router                | RID, interfaces, IP address/mask's current interface state (status) |
| Network  | 2        | Describe a network that has a DR  | DR and BDR IP address, Subnet ID, Mask                       |
| Summary  | 3        | Describe a subnet in another area | Subnet ID, Mask, RID of ABR that advertises the LSA          |

### Router LSA's Build most of the Intra-Area Topology

In order for OSPF to work it needs to know detailed topology information about each area. The routers in each area need to know all th details about the topology in their area. The Mechanism to give the routers this information is to create and flood the type 1 LSA the router LSA and network LSA type 2. About the routers and the links in the area.

Router LSA, also known as Type 1 LSA describe the router in detail. Each Router LSA has in it Router ID, Interfaces, IPv4 Address and masks, the state of the interfaces and what neighbors the router know about each of its interfaces. 

### Network LSA's Complete the Intra-Area Topology

Router LSA's define most of the intra area topology, network LSA define the rest of it. When OSPF elects a DR and that DR has a neighbor OSPF treats that subnet as another node in its mathematical model of the network. To represent the network, the DR will create and flood Type 2 LSA network LSA's for that network (subnet)

Neighbors that are connected to the DR are advertised as network LSA and not as router LSA by the DR. 

In a single area design no Type 3 exist as they are LSA representing other area's.

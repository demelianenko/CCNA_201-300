# CCNA 200-301 Chapter 21

## OSPF Network Types

| Network Type Word | Dynamically Discovers Neighbors | Uses a DR/BDR |
| ----------------- | ------------------------------- | ------------- |
| broadcast         | Yes                             | Yes           |
| point-to-point    | Yes                             | No            |

### The OSPF broadcast Network Type

OSPF default to use broadcast network type on all Ethernet network interfaces. 

#### Verifying Operations with Network type Broadcast

All discovered routers on the link should become neighbors, in the least they would reach 2 way state for none DR/BDR and full with the DR and BDR. 

Using the:

```
show ip ospf interface brief
```

we can see the amount of neighbors the router has and the local router roles on each interface that OSPF is enabled on. The state would indicate the role of the router and NBR F would indicate the full neighbors the router has while NBRs C indicate the total number.

We can see what type of OSPF network a interface uses with the following command:

```
show ip ospf interface [interface-id]
```

We would see it in the Network type field. The command will also show us the state and neighbors of that interface.

In case we need to manually set the network type to broadcast we can do so with the following command:

```
ip opsf network brodcast
```

#### Configure to Influence the DR/BDR Election 

Once the DR and BDR have been elected, the following applies:

- If the DR fails, the BDR becomes the DR and a new BDR is elected
- When a better router enters the subnet, no preemption of the existing DR or BDR occurs.

as a result of these rules, even if we configure the a router to be DR he wont be the DR until the current DR and BDR fail. Some times we would want to influnce the election of the DR and BDR we can do so with two configurations:

-  The Highest OSPF interface priority: The highest value wins during an election, with values ranging from 0 to 255
- The Highest OSPF Router ID: If the priority ties, the elections chooses the router with the highest router ID 

In the scenario of a power outage where all the router comes back online and all have default OSPF settings (100mb reference) they will all have interfaces with cost of 1, and than the router with the highest router ID will win.

In order to influence the election process we could set the a highest RID to the router we would want to be picked, or we could set the interface propriety to higher than 1, for example 99.  Using:

```
ip ospf priority 99
```

sub command.

Even after doing so we would need to reboot the router to make them redo the election process. afterwards when the new election happens our router will win as it has the highest port priority.

### The OSPF Point to Point Network Type

The point to point network type works for all the data link that by nature only have 2 routers on them. For example serial links or wan links. the data link protocols most often use HDLC and PPP do not support broadcast. Many Wan links are also point to point, there is no point in using DR/BDR on a network that only has 2 routers.

We can set the ospf network type using the following command:

```
ip ospf network pooint-to-point
```

We would need to run the command on both side of the network on both interfaces.  as the command is a sub interface command.

The 

```
show ip ospf neighbor
```

should list only one neighbor and it being in a full state (if all went well) and no mention of DR/BDR.
The

```
show ip ospf inerface brief
```

Would show us the state of the interface to be P2P and the number of neighbors and total neighbors to be 1.

When using Ethernet wan links that behave as point to point we should consider to use the point to point network type instead of the default broadcast.

## OSPF Neighbor Relationships

 After OSPF has been enabled on an interface, OSPF will try to find neighbors on the same interface by sending and listing to hello messages. However after discovering another OSPF router on the link, the router might not be able to become neighbors. Certain settings have to match for the OSPF routers to become neighbors. 

#### OSPF Neighbor Requirements 

After the router receives a hello packet from its neighboring router, OSPF examines the hello message to see if all the settings match between the two routers, if the settings don't match the 2 routers cannot become neighbors. 

##### Neighbor Requirements for OSPF

| Requirements                                                 | Required for OSPF | Neighbor Missing if Incorrect |
| ------------------------------------------------------------ | ----------------- | ----------------------------- |
| Interface must be in an up/up state*                         | Yes               | Yes                           |
| Access control list (ACL) must not filter routing protocol messages* | Yes               | Yes                           |
| Interfaces must be in the same subnet                        | Yes               | Yes                           |
| They must pass routing protocol neighbor authentication      | Yes               | Yes                           |
| Hello and hold/dead timers must match                        | Yes               | Yes                           |
| Router ID's (RID) must be unique                             | Yes               | Yes                           |
| They must be in the same area                                | Yes               | Yes                           |
| OSPF process must not be shutdown                            | Yes               | Yes                           |
| Neighboring interface must use the same MTU settings&        | Yes               | No                            |
| Neighboring interface must use same OSPF network type&       | Yes               | No                            |

The column labeled as Required for OSPF means all the requirements are required to form the neighboring relationship if any of them do not match the relationship cant be made. The second column Neighbor Missing if Incorrect if any of them requirmets that are Yes are not correct the neighbor wont show in commands like:

```
ip ospf neighbor
```

In conclusion all the requirements are needed for OSPF to work but when most of the requirements do not match the routers wont show the neighbor output, the last two MTU and network type the router can still be in a relationship  but the OSPF relationship wont work and wont exchange LSA's. But OSPF will show the neighbor in the show ospf neighbor command.

##### OSPF Neighbor Requirements and Best show/debug commands

| Requirements                               | Best show Command                        |
| ------------------------------------------ | ---------------------------------------- |
| Hello and dead timers must match           | **show ip ospf interface**               |
| They must be in the same area              | **show ip ospf interface brief**         |
| RID must be unique                         | **show ip ospf**                         |
| They must pass any neighbor authentication | **show ip ospf interface **              |
| OSPF process must not be shutdown          | **show ip ospf, show ip ospf interface** |

### Issues That prevent Neighbor Adjacencies

#### Finding Area Mismatches

This issue happens when an interface is put in to the wrong area, when setting up OSPF. When an area miss match happens the neighbor wont be show in the **show ip ospf neighbor** command, as a result of seeing nothing in the OSPF table to fix this issue we will need to find the area of each interface on the router that has OSPF enabled on it.

We can do so following the next steps:

- Check the output of the **show running-config** to look for:
  - **Ip ospf [proccess-id] area [area-id]** interface subcommand
  - **network** commands in the OSPF configuration mode
- Use the **show ip opsf interface [int-id]** command to list the area number

#### Finding Duplicate OSPF router ID's

We can use the **show ip ospf ** command to list the router RID, the route should also display a message saying it has seen a RID duplicate. All we need to do is change the router RID using **router-id [id]** OSPF subcommand. And restart ospf using **clear ip ospf process**

#### Finding OSPF Hello and Dead Timer mismatches

- Hello interval/timer: The per-interface timer that tells router how often to send OSPF hello messages on an interface.
- Dead intervals/timer: The per-interface timer that tells the router how long to wait with out having received a Hello form a neighbor before declaring the neighbor has failed (default is 4 time the hello timer)

If a router has a mismatch of hello timer and dead timer the other routers wont establish relationship with it, as a result of the miss match .The easiest way to find the hello timer and dead timer is to use the 

```
show ip ospf interface [interface-id]
```

#### Shutting Down the OSPF Process

When the OSPF process is shutdown it result in the following:

- Brings down all neighbor relationships
- Clears the LSDB
- Clears the IP routing table of any OSPF learned routes

The following do stay intact and are not removed when OSPF is shutdown:

- IOS retains all the OSPF configuration 
- IOS still lists all OSPF-enabled interfaces in the OSPF interface list (**show ip ospf interface**) but in the down state

The result in shutting down the OSPF results in the router not using OSPF anymore but still keeps all the settings for the next time we enable the ospf process.

### Issues That Allow Adjacencies but Prevent IP Routes

The next two issues would result in the neighbor showing in the output of the **ip ospf neighbor** but not not have a full relationship with them and wont add their routes to the routing table.

#### Mismatched MTU settings

The MTU is defined per interlace, and indicates what is the buggiest packet the router can forward without breaking it in to parts. the default would be 1500 bytes. For Ipv4 pakcets.

Routers that have different MTUS can become neighbors and list each other in the neighbor list  and even reach 2 way state, however they will fail to exchange LSDB's afterward the status will change to failed. We can see the MTU size in the running config as well as 
using the 

```
show interfaces
```

#### Mismatched OSPF Network Types

If the two routers have a mismatch with the network types, one uses point to point and the other uses broadcast. The following would occur:

- The two routers become fully adjacent neighbors, they reach full state.
- They exchange their LSBD's
- They do not add IP routes to the IP routing table

The reason for not adding the LSA's to the routing table would be as a result of the use of a DR, and the structure of the LSA when using a broadcast network type. The router get a different layout and details in the LSA than they expect and do not trust the LSA and as a result wont add the LSA's to the routing table.

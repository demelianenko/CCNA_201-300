# CCNA 200-301 Chapter 20

## Implementing Single-Area OSPFv2

Summary of the steps:

1. Use the

   ```
   router ospf [process-id]
   ```

   to enter the OSPF configuration mode 

2. This step is optional, configure the OSPF router ID by doing the following:

   -  Use the 

     ```
     router-id [id-value]
     ```

     router sub command

   - The second method is to give our loop back address an ip address. Use the

     ```
     interface loopback [number]
     ```

     Along with giving the loop-back an IP address. OSPF will pick the highest IP address of the loop back interfaces as it router-ID if its was not manually configured 

3. Use one or more

   ```
   network [ip-address] [wildcard-mask] area [area-id]
   ```

   Subcommand to enable OSPF on the interfaces we wish to enable them on.

   The network command tells the router on what OSPF interface to enable OSPF and it dose not share the network address we put in.

### OSPF Single-Area Configuration

A single area would be where all links and routers are in the same area usually area 0. When we begin we begin with the command
**router ospf [process-id]** this command will put us in the OSPF configuration mode. The process ID is just a number given to the this OSPF instance we will run, it would be unique per router and lets us run multiple OSPF process on a single router. The process ID can be any number from 1 - 65,535 

The command inside the OSPF configuration mode the **network** tells the router on which interfaces to enable OSPF on. For each matched interface the router enables OSPF on those interfaces, discovers interfaces, creates neighbors relationship

### Wildcard Matching with network Command

The network command in OSPF compares the network ip we give it to each interface on the local router and seeing if it matches. Instead of comparing the whole number the router only compares the subnet based on a wild car address we give it.

- **Wildcard 0.0.0.0** : Compare all four octets, all four octets must match exactly to the bit
- **Wildcard 0.0.0.255** : Compare only the first three octets and match them. Ignore the last octet.
- **Wildcard 0.0.255.255** : Compare only the first two octets and ignore the last two.
- **Wildcard 0.255.255.255** : Compare only the first octet and ignore the rest.
- **Wildcard 255.255.255.255** : Compare nothing, this wild card will match any IP address network we give it in the first part of the network command

A wildcard mask value of 0 in the octet tell IOS to compare to see if the numbers match. And a value of 255 tells IOS to ignore that octet. A wildcard mask gives the router the rules for matching its own interfaces 

### Verifying OSPF Operation

In order for OPSF to add its routes to the routing table it must first, create neighbor relationships, Then they must flood LSA's between the two neighbors so each router has the same LAS's in its LSDB. finally each router independently calculates its own IP routing from the information in the LSDB using the SPF algorithm. 

To see information about the OSPF we can use the following command:

```
show ip ospf neighbor
show ip ospf database
show ip route
```

  There are many commands we can use to verify all the way from the start to the bottom.

Commands to check our global config where our network command and IP ospf commands are:

- ```
  show running-config
  ```

- ```
  show ip protocols
  ```

Commands to inspect our interfaces to see our enabled interfaces:

- ```
  show ip ospf interface
  ```

- ```
  show ip ospf interface [interface-id]
  ```

- ```
  show ospf interface breif
  ```

To view our neighbors status we can use the following: 

- ```
  show ip ospf neighbor
  ```

- ```
  show ip ospf neighbor [neighbor-id]
  ```

To view our LSA's and LSDB:

- ```
  show ip ospf database
  ```

To view our SPF math:

- ```
  show ip ospf rib
  ```

To view our admin distance:

- ```
  show ip route
  ```

- ```
  show ip route ospf
  ```

- ```
  show ip route [subnet-mask]
  ```

- ```
  show ip route | section [subnet]
  ```


Using the **show ip ospf neighbor** command shows us a few very important details:

- Interface: This is the local routers interface connected to the ospf neighbor. 
- Address: This is the neighbors IP address on that link. The IP address of the interface that our local router is connected to.
- State: the state of the ospf interface if its Full,2-way
- Neighbor-ID: This is the router ID of the neighbor

Once the OSPF convergence has been completed, a router should list each neighbor it has made relationship with. On links that use a designated router the state will also list the role of the neighboring router after the "/"

- **FULL/ -** : The neighboring router state is full, with the "-" instead of letters meaning that the link does not use a DR/BDR
- **FULL/DR** : the neighbor state is full and the neighbor is a DR
- **FULL/BDR** : The neighbor state is full and the neighbor is a BDR 
- **FULL/DROTHER** : The neighbor state is full and the neighbor is neither the DR nor BDR. Meaning that the local router is the DR/BDR because the state is full.
- **2WAY/DROTHER** : The neighbor state is 2-way and the neighbor is not the DR/BDR, the local router is not the DR/BDR and it is connected to another router that is not the DR/BDR. Resulting in the 2-way state.

Once the router OSPF neighboring process is complete the routers exchange relationship, The router exchange the the content of their LSDB. Either directly or via the DR.  Routers in a single area would have all have the same content in thier LSBD after they are done exchanging the LSA's.

We can see the content of the LSBD using the:

```
show ip ospf database
```

Using the:

```
show ip route
```

will shows us the routes that were added to the routing table via OSPF, the routes that were added would have an O at the very left. In small network we can count the number of subnets and see how if we have routes to all of them in the routing table. We can also see the AD (administrives distance ) and metric in the routing table. 110 would be the default AD for ospf, and the number after would be the route metric. 

### Verifying OSPF Configuration

If our OSPF configuration does not work we have to first check our configuration. Using the:

- If we have access to enable mode, using the **show running-config** command to examine the running config.
- If we have only user mode access using the **show ip protocols** command to recreate the OSPF config.
- Using the **show ip ospf interface brief** command to determine whether the router enabled OSPF on the correct interfaces or not based on the command.

The best way to check out configuration would be to use the **show running-config** command, however the **show ip protocols** repeats the details of the OSPFv2 configuration and does not require enable access. The ip protocols command shows us the router ID's and the parameters that were used with the **router ospf** command. We can also see the networks we configured using the **network** command in the same show ip protocols command. 

We also need to make sure IOS has started OSPF on the interfaces that we meant it to do. We can check the interfaces using the **show ip opsf brief** which will list all the interfaces that OSPF is enabled on.

### Configuring the OSPF Router ID

Each router in OSPF must have a Router ID by default the router ID if not manually configured would be the highest IP on the loop back interface. If there is no loop back interfaces OSPF will pick the highest IP on a physical interface.

ISO routers choose the Router ID by the following method:

1. If the router-id command was used, the router will use that value
2. If the router-id command was not used the router will pick the highest IP of the up/up loop back interfaces
3. Lastlely if the router-id command was not used and there are no loop back interfeaces the router will pick the highest IP of the physical interfaces that are up. 

Loop back interface is a virtual interface that can be always in an up/up state. We can configure a loop back interface using the

```
interface loopback[id]
```

We can also assgin to it an IP address just like a normal interface. 

The router will set its Router ID each time it boots and the ospf process start, we can restart the process and adjust the ID if we picked a new once using the:

```
clear ip ospf process
```

command.



### Implementing Multiarea OSPF

The only difference configuring single area v multi is the usage of more than one area. The commands are all the same. As well needing one router to have an interface in each area. 

## Using OSPFv2 interface Subcommands 

We have more than one way to enable OSPF on a router interface, we can use the network command that we have used thous far. There is another option using the 

```
ip ospf
```

sub-command. Instead of globally enabling ospf per the network we can enable ospf with a subnetwork interface, enabling ospf per interface.

### OSPF Interface Configuration Example

In order to use the ospf sub command, we first most get rid of the network command on a global level.

```
no network [network-id] area [area-id]
```

afterwards we can head over to the interface configuration and use the following:

```
ip ospf [process-id] area [area-id]
```

For exmaple:

```
interface g0/0
ip ospf 1 area 0
```

Would enable OSPF with process one and area 0 on gig0/0

### Verifying OSPF Interface Configuration

Using either command the global network or the sub interface would give us the exact same OSPF configuration. We do see some changes.

When we use the **show ip protocols** command we can see the output of Interface configured explicitly [area-id]  if we configured OSPF via the newer sub interface command. Another difference when we use the newer method would show in the output of the **show ip ospf interface** the difference would be in the wording, with the newer method it would be "Attached via Interface Enable" while if we used the network command it would be "Attached via Network Statement"

Other than these difference all the other commands show exactly the same thing.

### Additional OSPFv2 Features

#### OSPF Passive Interfaces

After enabling OSPF on an interface OSPF will send hello messages on the interface in hope of making a neighbor relationship, as well as listin to other hello messages. Some times there is no router on the other end of the interface and we would not need the constant messages on the link. To prevent the messages we can make the interface passive. Doing so would result in:

- OSPF would still continue to advertise the subnet that is connected to the interface.
- OSPF no longer would send hello messages over the link
- OSPF wont listing to any hello messages on this link 

This result in OSPF still advertising the network of the interface, but not trying to make relationships on this link.

To configure an interface as passive we would need to do the following: 
we would the following command in OSPF configuration

```
passive-interface [interface-id]
```

 We can also make the default setting of all interface to be passive using the following, we can make a interface active again using the no command

```
passive-interface default
```

to make an interface active with the default set to passive:

```
no passive-interface [interface-id]
```

The default setting can be very handy if most of our interfaces are going to be passive, this would result in less commands to be need to be typed.

The **show ip ospf interface brief** command lists all interfaces on which OSPF is enabled, including passive interfaces
The **show ip interface** command lists single line that mentions that the interface is passive.

#### OSPF Default Routes

The mos classic case for using the default routes feature would be to advertise the connection to the Internet/wan-link/default-gateway.
The most common configuration would be:

- All routers learn specific (non-default) routes to subnets inside the network.
- One routers connects to the Internet and it has a default route that points forwards the Internet
- All routers should dynamically learn about the default route, that would be used for all traffic that would go out to the Internet.

Our router that has the Internet link would have the route set as its default route (destination 0.0.0.0 and mask 0.0.0.0.) and by using the following command it would advertise to the rest of the routers on the network the its default route. 

```
default-information originate
```

Would make the router advertise its default route using OSPF to the rest of the routers in the network. The command tells the router to advertise it default route when its working and to stop if its not working anymore.

We can use the 

```
default-information originate always
```

to tell the router to always advertise the route regardless of it status.

The route would show under O*E2 in the routing table, for default routes that were learned via OSPF.

### OSPF Metrics (Cost)

Cisco router allows three different ways to change the OSPF interface cost

- Directly, using the interface subcommand

  ```
  ip ospf cost [valeu]
  ```

- Using the default calculation per interface, and changing  the **interface bandwidth** settings, which changes the calculated values

- Using the default calculation per interface, and changing the OSPF **reference bandwidth** settings which would the calculated value

#### Setting the Cost Directly

We can set the cost of each interface directly with the sub interface command

```
ip ospf cost [value]
```

We can confirm the cost with the following command:

```
show ip ospf interface brief
```

#### Setting the Cost Based on Interface and Reference Bandwidth

Routers use per-interface bandwidth settings to describe the speed of its interfaces. The interface bandwidth settings does not change the actual speed of the interface, but only acts as a configurable setting to represent the speed of the interface. 

We can configure this option using a subinterface command:

```
bandwidth [value]
```

This wont change the actual speed of the interface but only how it is presented to the router and OSPF. By default this bandwidth would match the speed of the interface. 

OSPF uses the interface bandwidth to make its decisions  on the best route to a destination. OSPF will use the bandwidth in its cost calculation if the cost wasn't manually set. OSPF puts the interface bandwidth in a denominator (Fraction) called the refrence band-width in the numerator

Reference_bandwidth / Interface_bandwidth

We could in theory change both the reference and interface, but many other IOS features relay on the interface bandwidth and we should not change it. 

Many enterprises would use the default cost settings and just change the reference bandwidth of the interface. To influence the cost of the interface. While leaving the interface bandwidth with the real bandwidth. OSPF default reference bandwidth was made many years ago when links were much slower resulting in any link faster than 100 Mbps being cost of 1. When using the default cost calculation of OSPF it helps to configure the reference bandwidth to another value.

The default reference cost would be the bandwidth cost that is given the priority of 1. It is recommended to set the reference bandwidth to the bandwidth of the fastest link. by default any link with bandwidth that is equal or bigger of 100mbp's would be given the priority of 1. 

Deafult OSPF cost

| Interface            | Interface Default Bandwidth (Kbps) | Formula (Kbps)        | OSPF Cost |
| -------------------- | ---------------------------------- | --------------------- | --------- |
| Serial               | 1544 Kbps                          | 100,000/1544          | 64        |
| Ethernet             | 10,000 Kbps                        | 100,000 / 10,000      | 10        |
| Fast Ethernet        | 100,000 Kbps                       | 100,000 / 100,000     | 1         |
| Gigabit Ethernet     | 1,000,000 Kbps                     | 100,000 / 1,000,000   | 1         |
| 10 Gigabit Ethernet  | 10,000,000 Kbps                    | 100,000 / 10,000,000  | 1         |
| 100 Gigabit Ethernet | 100,000,000 Kbps                   | 100,000 / 100,000,000 | 1         |

As we can see from the table, any link that is 100mpb or higher will have the cost of one, if we have 10gig links and 100mbps link they will have the same cost. 

We can use the 

```
auto-cost refrence-bandwidth [speed-in-Mbps]
```

To set a new reference bandwidth, it is recomnded to set it go the fastest/highest bandwidth link in the network, or even higher.

For example if we have a enterprise network with the fastest link being 10 gig. We can set the reference to 10,000 Mbps (10gig)

```
auto-cost refrence-bandwidth 10000
```

That would mean that the 10 gig interfaces will have a cost of 1 and 1gig interfaces cost of 10.

We can also set it to 100gig in anticipation of higher links in the future. It is recommended to keep the reference bandwidth the same on all routers. 

Here are all the ways we can manipliaute the cost of interfaces in OSPF

1. Set the cost per interface manually - using **ip ospf cost [vlaue]** in the interface as a subcommand we can give each interface a value from 1 - 65,535
2. (This method should be avoided is possible) We can change the interface bandwidth with the **bandwidth [speed]** command speed being the value in kilobits per second. (it wont change the speed of the interface but only its bandwidth indication to the router.)
3. Change the reference bandwidth, using the router OSPF subcommand. **auto-cost reference-bandwidth [value-in-Mbp's]** 

### OSPF Load Balancing

When an OSPF router has two routes with the same cost, it can load balance the traffic over both the routes as them being of equal cost. Both routes would be added to the route table and the traffic would be load balanced over them. The router will add the equal cost routes based on the 

```
maximum-paths [number]
```

This command would set the maximum amount of paths OSPF would load balance over the traffic. 

The router could load balance its traffic per packet basis send each new packet over a new route. Packet one over path one packet two to path to. If all the packets are destined to the same subnet. The other method would be per destination IP.

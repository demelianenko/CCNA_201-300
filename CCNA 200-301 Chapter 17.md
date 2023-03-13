# CCNA 200-301 Chapter 17

## Vlan Routing with Router 802.1Q Trunks

All enterprise network use vlans, either a router or a switch need to have an IP address in each subnet. And have routes to the other networks. Only than can that router or switch can be used as default gateway.
Our four LAN routing options are:

- Use a router, with one router LAN interface and cable connected to a switch for each and every VLAN. (Physical Vlans where each switch is another lan)
- Use a router, with a VLAN trunk connecting to a LAN switch. (Known as a router on a stick)
- Use layer 3 switch with switched virtual interfaces (SVI)
- Use a Layer 3 switch with routed interfaces

The first item, is doable but not practical and is not used widely, needing far to many interfaces.

### Configuring Router On a Stick (ROAS)

Router on a stick uses VLAN trunking configuration to give a router a logical router interface connected to each VLAN. As a result the Router will have an interface in each VLAN, more so each logical interface can have it own IP address. The router uses sub-interfaces as means to have logical interfaces, to have a interface in each VLAN. The router needs an IP Address and a mask associated with each VLAN. How over the router physically has only one physical interface connected. Cisco solves this issue by using sub interfaces, creating a virtual interface on the same psychical interface. One logical interface per VLAN.

Most Cisco switches do not negotiate trunking, it needs to be manually configured.   Using the command:

```
switchport mode trunk
```

Router on a stick involves making logical interfaces on the router physical interface on order for a single physical interface to act as many logical interfaces for each vlan.

#### Steps to configure 802.1Q trunking:

1. use the **interface [port type].[number]**

   ```
   interface gigabitetherent 0/0.10 
   ```

2. Use the **encapsulation dot1q [vland_id]** command inside the subinterface

   ```
   encapsulation dot1q 10
   ```

3. Use the **ip address [address] [mask]** 

   ```
   ip address 10.1.10.1 255.255.255.0
   ```

The sub interfaces always start with a dot. The number has to be unique for each sub-interface. The sub interface number does not have to match the subnet ID but most places do so. 
Each sub interface has two command attached to it, the encapsulation method and the IP address given to the interface. The router treats the sub-interfaces like regular physical interfaces.

The native VLAN can be configured on the physical interface or on a sub-interface or ignored completely. Each Trunk has one native VLAN, than the router needs some configuration to make the native VLAN work. The two options for configuring a native VLAN are:

1. Configuring the  command **ip address** on a physical interface. but with out any encapsulation. The router considers this physical interface to be using the native VLAN. Using the Physical IP address and VLAN as the Native VLAN

2. Configuring the **ip address** command on a sub interface, and to use the encapsulation dot1q [vlan_ID]  sub command. to tell the router VLAN ID and the fact its a native VLAN, USING one of the sub interfaces as the native vlan.

   Using the commands

   

   1. we have no need to declare the vlan as the VLAN 1 by default will be used

      ```
      interface gigabitetherent 0/0
      ip address 10.1.10.1 255.255.255.0
      
      interface gigabitetherent 0/0.20
      encapsulation dot1q 20
      ip address 10.1.20.1 255.255.255.0
      ```

      Second part will add the 0.20 interface to the 20 vlan and not the default 1 (or if another one was declared )

   2. In this example we will make the we will change the default native VLAN to VLAN 10

      ```
      interface gigabitetherent 0/0.10
      encapsulation dot1q 10 native
      ip address 10.1.10.1 255.255.255.0
      
      interface gigabitetherent 0/0.20
      encapsulation dot1q 20
      ip address 10.1.20.1 255.255.255.0
      ```

      If we did say 20 on that line, it would have been added to vlan 10

   

   ### Verifying Router On a Stick

   A few commands we can use to verify the ROAS:

   - ```
     Show running-config
     ```

   - ```
     show ip route conncted
     ```

   - ```
     show vlans
     ```

The virtual sub interfaces are depended on the physical interface. If the physical interface isn't working the virtual sub interfaces wont work either. As with all routers when the interface is up and has an IP it will add the connected routes in to the table and the router will do the same with the sub interfaces. But more so the sub interfaces can be individually shutdown or brought up like physical interfaces. 

The **show vlan** command tells us which trunk interfaces use which vlans. 

### Troubleshooting Router On a Stick

The issue with trunking is, because it is manually done. If there is a misconfiguration the switch or the router have no way to tell if they are. If we check both the routes on the router sub interfaces or the vlans we cant find the issue. The first thing we need to check are the configuration of both trunks on both sides.

Checking the configuration on the router side:

1. Is each non-native VLAN configured on the router with an encapsulation dot1q [vlan-id] command on a sub interface?
2. Do those same vlans exists on the other side of the trunk connected switch and are they in the allowed list. Not TVP pruned. and not STP blocked
3. Does each router ROAS subinterface have an IP address/mask configured per planned configuration?
4. If the native VLAN is being used, is it configured correctly? either on the physical interface or on a sub interface. 
5. Is the same native VLAN is configured on the neighboring switch trunk?
6. Are the interface or subinterfaces are configured with the shutdown command?

## VLAN Routing with Layer 3 Switch SVIs 	

A layer 3 switch, called a multi layer switch is a router and a switch 2 in 1. He is able to do layer 2 networking as well as layer 3 routing. You would need to enable the routing feature as its not the default of the switch. An SVI would be a logical layer 3 port that is depended on the layer 2 VLAN, while a routed port would be an independent layer 3 port not depending on any VLAN to work. an SVI more commonly would be used when connection to access switch or to the hosts. In order to allow the switch to do inter vlan routing. While the routed port will act as  router port.

### Configure Routing Using Switch SVI's

The switch needs a virtual interface in order to connect to each LAN. These Virtual interfaces act as router interfaces and allow the switch to make layer 3 routing. The multi layer switch would still need a port per VLAN. In this case a virtual interface per VLAN. On some switch the routing function would need to be enabled as well as a reload of the whole switch. 

1. use the 

   ```
   sdm prefer lanbase-routing
   ```

   Or another command to configure the switch to switch forwarding ASIC settings to make space for IPv4 routes at the next reload.

2. Use the 

   ```
   reload
   ```

   command to restart the switch so the command will take

3. Once reloaded use the

   ```
   ip routing
   ```

   command in global configuration mode to enable the IPv4 routing function.

Now to that we have enabled the routing on the multi switch. We can configure the virtual interfaces

1. Use the 

   ```
   interface vlan [vlan_id]
   ```

   command in global configuration mode to create a VLAN interface and give the switch routing logic, A layer 3 interface connected in to the VLAN of the same number.

2. Use the 

   ```
   ip address [address] [mask]
   ```

   command in VLAN interface configuration to configure the IPv4 on the Vlan interface enabling Layer 3 routing

3. Use the 

   ```
   no shutdown
   ```

     command in interface configuration to bring the interface to an up/up state.
   
   After thing configuration the switch should be able to route to all the vlans that are added in the steps. 

### Verifying Routing with SVI's

After enabling routing you can use the same command you use on a router like

```
show ip route
```

After enabling layer 3 routing, the switch acts like a router and can even use OSFP as well as all thr routing method a router can. 

### Troubleshooting  Routing with SVI's 

There are two main things to check when troubleshooting SVI's on a switch. The first thing to check would be to makes sure routing is enabled on the switch. The second thing would be to make sure each VLAN that is associated with the switch is known and active on the local switch. 

Some switch will default to have layer 3 switch on by default some do not. To make sure the switch support layer 3 would be to try and use the **sdm perfer** **ip routing** commands. The sdm prefer changes how the switch allocates it memory and is require to enable layer 3 routing on many switch. the usages of the command lets the switch know we need to allocate memory to layer 3 routing tables now. following the sdm command and a reload. The **ip routing** command enables the layer 3 routing on the ISO level. Both commands are needed to enable layer 3 routing on a switch.

The ip routing command cant be used until the sdm prefer command and a reload of the switch was done, in some switches. After the sdm command has been used a reload was preformed the ip routing command exists and can be used.

The second thing to check when troubleshooting SVI's on a layer 3 switch would be the VLAN's The SVI state is tied to the Satet of its VLAN. 

1. The VLAN must be defined locally or discovered via VTP
2. The switch must have at least one physical interface with up/up state in that VLAN, either/both
   1. An up/up access interface assigned to that VLAN
   2. a trunk interface for which the VLAN is in the allowed list, is STP forwarding and is not VTP prune
3. The VLAN must be administratively enabled (the vlan itself not the port)
4. The VLAN interface must be administratively enabled

Its good to keep in mind that the VLAN and the VLAN interface are two different things they are connected but configured in two different places. The VLAN interface is a layer 3 interface connected to the VLAN. If you require to route packets to VLAN's 11,12,13 you would require the Layer 3 to be in the VLAN they are going to forward the packet to. Both the VLAN interface and the VLAN can be disabled. 

A few scenarios that can cause issues:

1. The access interface for VLAN 10 is shutdown so ISO show down the VLAN interface for vlan 10. 
2. VLAN 20 is deleted, resulting in IOS then brining down down the VLAN 20 interface.
3. VLAN 30 is shutdown resulting in IOS brining the VLAN 30 interface down.

## VLAN Routing with Layer 3 Switch Routed Ports

 When layer 3 switches use SVI's the physical interfaces still act as layer 2 ports and do no layer 3 logic routing. a Port can be made in to a routed port and be converted to a routed port that can prefigure layer 3 logic routing. An SVI cam route between vlans but is not a full layer 3 port like a router will have.

After making the port in to a routed port the port will stop doing layer 2 switching and will only do layer 3 that includes:

- Stripping off the incoming Ethernet data-link header/tralier
- Making a Layer 3 forwarding decisions
- Adding a new Ethernet data link-link header tralier to the packet. 
- Forwarding the packet, encapsulated in a new frame

### Implementing Routed interfaces on Switches

When a Layer 3 switch needs a Layer 3 interface connected to a subnet and only has one physical interface. You can choose to use a routed interface or a SVI. If you have many interfaces connected to that subnet you should use an SVI, because after making a interface in to a routed interface it cant forward the frames to the inter vlan routing and can only preform Layer 3 routing like a router.  The SVI will forward the traffic intreanlly to the vlan, so layer 2 logic can forward the frame. a Routed port cannot do it.

Converting the port will stop it from working like a switch port and will make it to layer 3 port like a router will have. The port usually is connected to the default gate way or to another layer 3 router to route layer 3 traffic. a routed interface wont be connected to hosts.
![](/home/d/CCNA_201-300/Images/Chapter 17/image-20221130175513131.png)

The following pictures shows well, when to use SVI and when to use routed interface. 

Enabling a switch interface to be a routed interface, instead of a switch interface is a simple process. Simple go to the interface configuration mode and use the command on the physical interface:

```
no siwtchport
```

Once the switch port has been made in to a routed interface, it will be a router interface but on a switch. It will need an IP address on the physical interface (unllike the SVI). After being set to a routed interface the interface will not show as the other switch interfaces.

When using the **show interface:** command on a router the output will display the IP address 

The **show interface status** command under where the VLAN heading instead of saying the access vlan or the word trunk it will say Routed indicating its a routed interface and not a layer 2 interface.

**show ip route** lists the routed port as an outgoing interface in routes

**show interface [type number] switchport** If the port number is a routed port the command will output the port is not a switch port

#### When to use which

We have two options, SVI's and routed ports. when do we should we use each of them? when we have point to point link between two devices that route,  it is preferred to use a routed interface. while using the SVI's between end hosts and other switches that do not route. Having the routed ports between the core switches and SVI's between the distribution switches.
Routed interfaces should be used between layer 3 switches and or routers. 

### Implementing Layer 3 EtherChannel

In most design network we would have more than one link connecting the layer 3 switches. While each port can be treated as sepreate routed port it is better to combine the port in to layer 3 ether channel. Leaving multiple parallel link between switches results in each switch that has multiple parallel link to it to learn two ip routes with the same neighboring switch as the next hop. Using a layer 3 ether channel lets us maintenance the link between the switch as a single logical link over multiple physical links resulting in a single next hop.  Each switch will learn a single link instead of many. ISO will than balance the traffic over the multiple psychical links. 

#### Steps to make a Layer 3 ether channel:

1. Configure the phyiscal interface as following:

   - Add the 

     ```
     channel-group [number] mode on
     ```

     command to add the interface to the channel, Use the same number for all the interfaces you want in the same switch.

   - Add the 

     ```
     no switchport
     ```

     command to make each phyiscal port a routed port

2. Configure the PortChannel interface:

   - Use the 

     ```
     interface port-channel [number]
     ```

     command to move to port channel configuration mode for the same channel number we have configured on the physical interface.

   - Add the 

     ```
     no siwcthport
     ```

     command to make sure the port-channel interface act as routed interfaces

   - Use the 

     ```
     ip address [address] [mask]
     ```

     command to configure the address and mask

One important thing to note, even though the physical interface is routed the ip address is configured on the ether channel. When the no swicthport command is used IOS adds the no ip address command to the interface If the port is part of ethercahnnel. 

Once we have configured the ether channel it will show up in various  commands that refer to IP address and routes mention the PortChannel. As well as the 

```
show interace status
```

command lists that fact that ports is a routed port.

Another way to check the EtherChannel using the 

```
show etherchannel summary
```

  

### Troubleshooting Layer 3  EtherChannels

There are two main area's to consider when troubleshooting Layer 3 EtherChannels, we first need to take a look in to the             **channel-group** command. which enables an interface for an EtherChannel. Secondly we need to make sure a list of settings must match on the interface for a layer 3 etherchannel to work correctly. 

For the **channel-group** command, this command enables EtherChannel statically or dynamically. If the command is used dynamically the commands key word would imply either Port Aggregation Protocol (PaGP) or link Aggregation Control Protocol(LACP). As the protocol to negotiate between the neighboring switches, whether they will include their ports in the channel. 

<u>The Layer 3 EtherChannel works exactly the same as the layer 2 one!</u>

Additionally to the **channel-group** command a few conditions have to be met on layer 1 and 2 of the ports to be included in the channel.

- **no switch port:** The port must be configured with no switch port and so most the physical interface. If the physical interface is not also configured with no switch port. The port wont be operational in the channel. 
- **Speed:** The physical ports in the channel must use the same speed. 
- **Duplex:** The ports in the channel must have the same duplex settings.

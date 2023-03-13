# CCNA 200-301 Chapter 10

## Understanding RSTP Through Configuration

Most modern switches will default to using RSTP, No configuration is needed for RSTP to do its job by default. you can buy a new switch plug it to the network and RSTP will work to block the needed port to prevent loops.
While this might make sense in small networks in mid to large (enterprise networks) benefit from manual configuration of RSTP\STP  to better suit larger topologys.
In larger networks switches are categorized based on what they are going to be connected to:

- **Switches that connect to end points (computers,servers): Access Switches**

- **Switches that connect to Access switches: Distribution switches**

Generally the root switches in a network will be a Distribution switch.

### The Need For Multiple Spanning Trees

STP was originally made before vlan were invented, so when Vlans became mainstream and were being used. the solution was to create an STP instance per vlan and block different ports on different vlans.

### STP Mods And Standards

| Name        | Based on STP or RSTP | Trees                         | Original IEEE Standard | Config Parmater |
| ----------- | -------------------- | ----------------------------- | ---------------------- | --------------- |
| STP         | STP                  | 1(cat) 1 for the whole switch | 802.1D                 | N/A             |
| PVST+       | STP                  | 1 per vlan                    | 802.1D                 | pvst            |
| RSTP        | RSTP                 | 1(cat) 1 for the whole switch | 802.1W                 | N/A             |
| RApid PVST+ | RSTP                 | 1 per vlan                    | 802.1W                 | rapid-pvst      |
| MSTP        | RSTP                 | 1 or more                     | 802.1s                 | mst             |

(MSTP allows for as many instances of stp as the network designer wants, and dose not require 1 per vlan  )
In today's age CISCO switches have only the option to use:

- Cisco propriety STP based PVST+ or RSTP based RPVST+

- IEEE standard MSTP
  
  Modern switch do not have the option to do a single instance for the whole switch like the original RSTP or STP did. 

### The Bridge ID and System ID Extension

TO support Spanning tree per VLAN or multiple spanning tree instances on the same switch as dose MSTP. the protocol must consider tagging (vlan tab) and trunking and why RSTP and MSTP now exist as part of the 802.1Q standard.
For that purpose the Bridge ID was updated to enable tagging 

##### Original Format Bridge ID:

Had only 2 parts 

| Priority           | System ID             |
| ------------------ | --------------------- |
| contained 0-65,535 | contained Mac address |
| 2 Bytes in Size    | 6 bytes In Size       |

##### Extended Bridge ID

Updated Bridge ID for tagging
priority field was reduced in its place now holds the vlan ID of the packet.
Priority became Multiples of 4096
and the system ID stayed the same

| Priority                    | system ID extension | System ID             |
| --------------------------- | ------------------- | --------------------- |
| contained Multiples of 4096 | contained Vlan ID   | contained Mac address |
| 4 Bits in Size              | 12 Bits in Size     | 6 Bytes in Size       |

The only part that can be modified in the Bridge ID is the Priority Both the vlan and the mac address are put there by the switch. But the Priority has to be a Multiples of 4096. 0 being the lowest and 61440 being the highest.
The priortiy can be set using the command:

```
spanning-tree vlan 1 priority 4096
```

In most Lans only a few switches will ever be good candidates to be root switches so instead of thinking in Multiples of 4096 we could use the following commands to set a primary switch and the second best secondary switch 
`spanning-tree vlan 1 root primary`

```
spanning-tree vlan 1 root secondry
```

This will set the switch to be the primary switch in that vlan
and the secondary as well. When using the command root primary the switch will decide to either select 4096 or 24576 as its priority while secondary will always choose 28672. because of the assumption the default will be 32,768.

### How Switches Use the Priority And System ID Extension

The priority is broken in to 2 parts the vlan id takes the first 12 bits and the priority take the last 4

in bits it will looks like this last 4 are priorty first 12 are the vlan id 
1000 0000 0000 1001
with the first 4 being vlan 9. 

The root swicth will use the priority 24,576 plus 9 (vlan ID) = 24585
while the local swicth got the priority 32,768 plus 9(vlan ID) =  32777

### RSTP Methods to Support Multiple Spanning Trees

RPVST+ the Cisco implantation of RSTP is able to span an instance of RSTP per vlan that exists on the switch.

- RSTP creates one tree - the common spanning tree (CST) creates a single spanning tree per physical switch, while RPVST+ creates one for each vlan

- RPVST sends one set of BPDU's per vlan. while the common RSTP will send 1 set of BPDUS's no matter how many vlans the switch has.

- RPVST+ uses mac multicast address 0100.0CCC.CCCD(Defined by Cisco) while RSTP uses 0180.C200.0000 (Defined by IEEE)

- When RSTP sends a message it dose not use a vlan header and will only send the message on the native vlan (vlan1). Whole RPVST+ sends each vlan its message in the vlan itself with the vlan header in the packets (802.1Q)

- RPVST+ adds an extra type length value to the BPDU that identifies the vlan id while RSTP dosent as it dosent care about Vlans

- Both RPVST+ and RSTP view the 16-bit priority as having only 12 bits System ID extension. with RSTP setting the vlan value to all 0 meaning no vlan

RSTP behaves at if vlans do not exist while RPVST+ read and understand the vlan information. 

## Other RSTP Configuration Options

- **Switch Priority**: The global Command 

  ```
  spanning tree vlan "X" root Primary "Y"
  ```

  let the network engineer to set the switches priority in that Lan

- **Primary And Secondary Root Switches**: The global command 

  ```
  spanning-tree vlan "X" root Primary|Secondry
  ```

  Also lets you set the priority, but the switch decides on what value to make that switch likely to be the primary root switch  or the secondary root switch. instead of using number of priority we can just use primary and secondary.

- **Port Cost**: The interface subcommand, 

  ```
  spanning-tree vlan "X"
  cost "Y"
  ```

  Let us  set the switch's RSTP/STP cost on that port, for either all vlans or just a single. Changing those port costs for some switches and that can impact the election of root ports and designated ports.

  
  
  ## Configuring Layer 2 EtherChannel
  
  Ether Channel allows switches that have more than link between them that are parallel to treat them as one link. Helping with redundancy. The switch will treat such links with EtherChannel as one logical link but many hardware links. WIth out EtherChannel such links will be blocked by STP/RSTP.
  
  EtherChannel == PortChannel == Channel group are all the same.
  
  ### Configuring A Manual Layer 2 EtherChannel
  
  To configure EtherChannel that each port always attempts to be part of the channel we can add the 
  
  ```
  channel-group
  ```
  
  configuration command to each physical interface. The On keyword tells that switch to put the ports in EtherChannel
  
  to confiure it manually
  
  1. Add the 
  
     ```
     channel-group "Number" mode on
     ```
  
     in the interface configuration mode under each physical interface 
  
  2. Use the same number for all commands on the same switch, but the channel number on the switches can be different 
  
  3. you can use the 
  
     ``` 
     show etherchaneel summary
     ```
  
     to view the etherchannel info.



### Configuring Dynamic EtherChannels

In addition to the manual way of configuring EtherChannels we can also use the dynamic way. The switch can negotiate if a set of links can become part of the channel or not. Afterwards the switch can also send messages to directly connected switch's to discover their settings for EtherChannel. If the physical interfaces pass the checks they are added to the channel and used if not they are put in a down state. Until all the configuration inconsistency is resolved. 

Most Cisco switches support both the propriety Cisco PAgP as well as the IEEE LACP both protocols achieve the same results.  They both achive the same thing only adding links that have passed all the checks in to the channel. One major diffrance would be that LCAP support up to 16 links while PAgp support only up to 8. With the anymore being set in reserve if a link fails in a down state.

To configure either protocol you would need to use the 

```
channel-group
```

Command either by using the Desiarble and Auto for PAgp or active and passive for LACP. With at least one option on one side being desirable or Active.

At least one side has to have the "Active" option (desiable for PAgP) if both side are auto or passive no link will be made. If both sides are active or desirable a link will be made.

### Physical Interface Configuration and EtherChannels

Even when ports have been added to the channel other configurations could prevent the ports from joining the channel in an active mode. When looking at adding the ports the new physical ports should have the same configuration as the already existing port in the channel. other wise the switch wont be able to add the ports to the channel.

The list of items the swicth checks before adding the port to the channel:

1. Speed
2. Duplex
3. Trunking state, access or trunk
4. If the port is access what vlan does it have access to
5. If the port is trunk what vlans is it allowed on
6. If a trunk the native Vlan
7. STP interface settings

The switch also checks the settings on neighboring switch's to do the siwtch will use PAgp or LACP or CDP. When checking Neighboring ports have to have all the settings the same as the other switch expect the STP settings. 

If the channel ports have been put in to err-disabled as a result of miss matching settings you most shudown the ports and no shutdown to take them out of the err disabled state. 

### Ether Channel Load Distribution 

When using layer 2 etherchannel Switches mac address learning process assotices the mac address with the channel interface and not the physical ports as the channel present it self as a single physical port. Later when the switch has to make a forwarding decision it most decide what real physical port to send the frame out of. 

### Configuring Options for EtherChannel Load Distribution

The load Distribution makes the decision for each frame based on various numeric values found in layer 2,3 and 4 headers. The process uses one configuration settings as input.

```
por-channel load-balance "Method"
```

Each switch may support different methods.

#### Ether Channel Load distribution methods:

| Configuration Key Word | Math Uses                                   | Layer |
| ---------------------- | ------------------------------------------- | ----- |
| src-mac                | Source mac address                          | 2     |
| dst-mac                | Detestation mac address                     | 2     |
| src-dst-mac            | Source and destination mac address          | 2     |
| src-ip                 | Source Ip address                           | 3     |
| dst-ip                 | Detestation Ip address                      | 3     |
| src-dst-ip             | Source and destination Ip address           | 3     |
| dst-port               | Destination TCP or UDP port                 | 4     |
| src-dst-port           | Both source and Destination TCP or UDP port | 4     |

Different use cases for the different methods:

* To cause all messages in a single application to flow use the same link in the channel, rather being sent over two different links. One of which can be busy and delaying the packets.
* When using the forwarding logic on the switch it is using ASIC that has been built for this purpose and is making the decision in much quicker way versus software.
* To use all the active links in the EtherChannel adjusting to addition and removel of the active links over time.
* Within the constraint of the other goals balance the traffic across thoes active links. 

In summary, the algorithms first intend to avoid re ordering messages as well as making use of the ASIC and use all the active links. However the algorithms does not attempt send the exact same number of bits over each link. The algorithm does try to balance the traffic, but always within the constraint of the goals (Math uses in the table)
The algorithm identifies the fields it needs in order to make the decision to what port to send the packets in the message header regardless of what algorithm was selected. All packets that corresponds to the same method and variable (source mac address or destination mac address for example) will be forwarded over the same physical link. 

For example when a suer connect to a website there can be thousands of packets sent over the time of the connection. All the packets will be forwarded over the same physical link. Another example would be source mac address all the packets from a mac address will be sent over the same physical link. 

The algorithms focus on low order bit instead of high order bits. Because the low order bits usually differ the most in real network.
achieving better traffic over the links. Low order bit being the bits that are lower in binary bit 0 versus 128. In real network the bits that are lower are more likely to be different versus the higher bits that usually are the same.

### The Effect of the EtherChannel load Distribution Algorithm

The 

```
test etherchannel load-balance
```

Can be used to test the settings and see where the switch will send a packet using a certain algorithm.
For exmaple:

```
test etherchannel load-balance interface po1 mac 0200.0000.00001 0200.1111.1111

Would select Gi1/0/22 of Po1

test etherchannel load-balance interface po1 mac 0200.0000.00001 0200.1111.1112

Would select Gi1/0/22 of Po1

test etherchannel load-balance interface po1 mac 0200.0000.00001 0200.1111.1113

Would select Gi1/0/22 of Po1
```

 we select what EtherChannel interface after the test command and than we select the source and destination mac address. The same link is selected because the method is source mac address. If the source mac address would be diffrent another link would be selected:

```
test etherchannel load-balance interface po1 mac 0200.0000.00002 0200.1111.1111

Would select Gi/0/24
```

If both source and destination are selected if any of them changes another link wold be selected if the lower bit of the mac address are different.


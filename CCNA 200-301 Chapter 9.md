# CCNA 200-301 Chapter 9

The 2 goals STP and RSTP have are :

- all devices in a vlan can send frames to all other devices 
- frames have a short life and do not loop around the network for infinity, by blocking redundant  links between switches.

## The 3 problems

STP/RSTP prevent 3 common problems in LANs

| Problem                     | Description                                                  |
| --------------------------- | ------------------------------------------------------------ |
| Broadcast storm             | the looping of a frame to infinity unless something breaks the loop |
| Mac table instability       | as a result of the looping the mac adders table with incorrect frames source because of the looping. |
| Multiple frame transmission | as a result of so many copies being delivered to the receiver confusing him |



## What dose Spanning tree dose 

STP/RSTP prevents loops by placing each interface in either forwarding mode or blocking mode. Interfaces in blocking mode do not process any incoming packets except STP/RSTP frames. 

STP convergences refers to when the network topology changes and the switch needs to reconsider what ports are blocked and what port are forwarding.


## How STP Works

- STP elects a root switch. STP enables all the interfaces on the root switch to forward.
- each nonroot switch considers one of its ports to have the least administrator cost between itself and the root switch
- the interface with the least root cost will be changed to the forwarding and be designated port 

| Port                                               | STP state  | Description                                                  |
| -------------------------------------------------- | ---------- | ------------------------------------------------------------ |
| All root switches ports                            | Forwarding | All the root switches ports will be set to forwarding        |
| each other switch that not root switches root port | Forwarding | The port with the least root cost (lowest) to reach the root switch |
| Each none switch   designated port                 | Forwarding | All ports not connected to other switches and facing endpoints |
| All other working ports                            | Blocking   | All other ports are blocked and wont process ant other frames except STP frames |

So at the end of the day we want to get rid of redundant links while still having them for if and when one fails.

STP only considers working interfaces, meaning interfaces that are up and have a cable connected to them 

## STP Bridge ID & Hello BPDU

Fields in the Hello BPDU


| Field                          | Description                                                  |
| ------------------------------ | ------------------------------------------------------------ |
| Root Bridge ID                 | The Bridge ID of the switch that the sender of this hello packet think is the current root switch |
| Senders ID                     | The Bridge ID of the sender                                  |
| Senders root cost              | The STP cost between the sender switch to the root switch    |
| Timer Value on the root switch | Includes the hello timer, MaxAge timer and forward delay     |



## Electing Root Switch

The election start with each switch thinking it the root switch and advertising itself as the root.
With the winner being the one with the smallest BID number  the BID is a 8-byte and is made of a 2 byte priority number and the mac address of each switch.
If the Priority number is the same than the tie breaker is the switch with the lowest mac address.

- Lowest Priority (same by default) (2 byte field in the Bridge-ID (BID))
- If a tie than lowest mac address



## Choosing Root Port

This will only occur on noon root switch as the root switch will have all of its ports set to be root switches and to forward.
Each nonroot switch will have only one root switch.

The root port would be the port with the least root cost, meaning the port who can reach the root switch faster and with the least hops.
Each time the link has to go trough another none root switch the cost will increase.

If a tie in root cost accurse the tie breaker would be:

- Lowest Bridge ID
- Lowest Port Priority on the neighbor switch (not the one we look but the one connected to it)
- Lowest Internal port number neighbor switch (not the one we look but the one connected to it)



## Choosing Designated port

The port with the lowest root cost on each Lan segments gets to be the designed port.
if the cost is tied the between the 2 switches than the BID would be the tie breaker.

Switch ports connected to end  points (computers etc..) would always be designated ports and should forward.

each collision domain (each link ) will have at least one interface forwarding 

If the port is connect to another non root switch's root port then the port will be designated.
The other side of root port will always be Designated  or root.



## Configuring STP

One of the few tools network engineers have to configure STP on their network is to change the BID (bridge ID) and to change the cost of ports.
if we want to rig the stp election and to make a certain switch the root switch we can just give it a lower priority.
same with the ports if we change the cost we can rig which one would act as dp(designated port ) 

the lower the speed of the port the higher is the cost of him (lower cost better)



## Commands

Show spanning tree

```
show spanning-tree vlan 1
```

show spanning tree detail

```
show spanning-tree detail
```

Show Summry

```
show spanning-tree summry
```

# STP Not RSTP

most of the difference between stp and rstp is after the initial setup up and comes more in to play when a change in the topology occurs.

While no change occurs by default the root switch will send out hello packets every 2 seconds.
each nonroot switch in turn will forward the hello packet after adding the root cost and setting the senders Bridge ID 

- The root switch creates a hello packet every 2 seconds with root cost of 0 out all of its interfaces
- the nonroot switch receives the hello packets changes the root value according to their ports and assign their own Bridge id to the hello packet
- Rinse and repeat

the switch knows that as long as it receives hello packets all is well and done. but the moment he stops receiving the packets something has accord.

## STP  Timers




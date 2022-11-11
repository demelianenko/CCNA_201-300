# CCNA 200-301 Chapter 9

The 2 goals STP and RSTP have are :

- all devices in a vlan can send frames to all other devices 
- frames have a short life and do not loop around the network for infinity, by blocking redundant  links between switches.

## The 3 problems

STP/RSTP prevent 3 common problems in LANs

| Problem                     | Description                                                                                          |
| --------------------------- | ---------------------------------------------------------------------------------------------------- |
| Broadcast storm             | the looping of a frame to infinity unless something breaks the loop                                  |
| Mac table instability       | as a result of the looping the mac adders table with incorrect frames source because of the looping. |
| Multiple frame transmission | as a result of so many copies being delivered to the receiver confusing him                          |

## What dose Spanning tree dose

STP/RSTP prevents loops by placing each interface in either forwarding mode or blocking mode. Interfaces in blocking mode do not process any incoming packets except STP/RSTP frames. 

STP convergences refers to when the network topology changes and the switch needs to reconsider what ports are blocked and what port are forwarding.

## How STP Works

- STP elects a root switch. STP enables all the interfaces on the root switch to forward.
- each nonroot switch considers one of its ports to have the least administrator cost between itself and the root switch
- the interface with the least root cost will be changed to the forwarding and be designated port 

| Port                                               | STP state  | Description                                                                     |
| -------------------------------------------------- | ---------- | ------------------------------------------------------------------------------- |
| All root switches ports                            | Forwarding | All the root switches ports will be set to forwarding                           |
| each other switch that not root switches root port | Forwarding | The port with the least root cost (lowest) to reach the root switch             |
| Each none switch   designated port                 | Forwarding | All ports not connected to other switches and facing endpoints                  |
| All other working ports                            | Blocking   | All other ports are blocked and wont process ant other frames except STP frames |

So at the end of the day we want to get rid of redundant links while still having them for if and when one fails.

STP only considers working interfaces, meaning interfaces that are up and have a cable connected to them 

## STP Bridge ID & Hello BPDU

Fields in the Hello BPDU

| Field                          | Description                                                                                       |
| ------------------------------ | ------------------------------------------------------------------------------------------------- |
| Root Bridge ID                 | The Bridge ID of the switch that the sender of this hello packet think is the current root switch |
| Senders ID                     | The Bridge ID of the sender                                                                       |
| Senders root cost              | The STP cost between the sender switch to the root switch                                         |
| Timer Value on the root switch | Includes the hello timer, MaxAge timer and forward delay                                          |

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
each non-root switch in turn will forward the hello packet after adding the root cost and setting the senders Bridge ID 

- The root switch creates a hello packet every 2 seconds with root cost of 0 out all of its interfaces
- the non-root switch receives the hello packets changes the root value according to their ports and assign their own Bridge id to the hello packet
- Rinse and repeat

## STP  Timers

| Timers        | Default Value  | Description                                                                                                                                                                            |
|:------------- | -------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Hello         | 2 seconds      | default packet time for the hello packet                                                                                                                                               |
| Max-Age       | 10 times Hello | The amount of time the switch will wait before trying to change the topology.                                                                                                          |
| Forward Delay | 15 seconds     | The delay time the port will be in between blocking and forwarding state. the stage called listening stage and the port will stay in it for the value of time assigned. 15 by default. |

the switch knows that as long as it receives hello packets all is well and done. but the moment he stops receiving the packets something has accord. after the switch dose not receive the hello bpdu for the set time (default 20 seconds) the switch will begin the whole process from the beginning of electing a root switch

## Changing interface state with STP

Switches that use STP can move the state of ports from forwarding to blocking with no other steps in between.
But for the switch to change the state of a port from blocking to forwarding the port must first enter 2 transition states :

- Listening - the port dose not forward frames (just like blocking) the switch removes all old unused mac address on this interface, the entry's could be the result of temporary loops.

- Learning -   in this state the port will still not forward frames but unlike Listening it will learn new mac address and receive frames on the interface
  
  STP moves ports from blocking -> Listening -> Learning -> forwarding.  
  each time STP moves the port to a different state it leave it the port in that state for the length of the forward delay timer (deafult is 15 seconds)

IEEE STP States

| State      | Forwards Frames? | Learns Mac address based on received frames? | Transitory or State Stable |
| ---------- | ---------------- | -------------------------------------------- | -------------------------- |
| Blocking   | No               | No                                           | Stable                     |
| Listening  | No               | No                                           | Transitory                 |
| Learning   | No               | Yes                                          | Transitory                 |
| Forwarding | Yes              | Yes                                          | Stable                     |
| Disabled   | no               | no                                           | Stable                     |

## Rapid STP (RSTP) Concepts

RSTP is the new version of STP that suited more modern networks. RSTP standard number is  802.1w was the original but as of today the standard is 802.1Q

## Comparing STP To RSTP

RSTP has all the same features of STP and works largely the same.

- RSTP and STP elect a root switch using the same rules and tie breakers

- RSTP and STP switch selects their root port  with the same rules and tie-breakers.

- RSTP and  STP place each port in either forwarding or blocking state, although

STP and RSTP switches are so a like they can be both deployed on the same network with 0 issues.

the main reason for the creation of the RSTP even thought the difference between STP and RSTP is minimal is the time it takes STP to switch between stages.

RSTP changes and adds to STP in away to avoid waiting for the long STP timers.

- RSTP adds a mechanism by which a switch replace its root port without waiting to reach the forwarding state (in some conditions)
- RSTP adds a new mechanism to replace a designated port without any delay 
- RSTP lowers waiting time for cases in which STP must wait for a timer

As an Example imagine a failure case in which a link remains up. but for some reason, a non-root switch stops hearing the Hello BPDUs. Because of the failed link that is still ramins up. STP requires switch to wait for MaxAge seconds, which STP defines based on 10 times the hello timer, or 20 seconds by default. 
RSTP shortens this timer defining MaxAge as three times the hello timer. Additionally RSTP can send messages to the neighboring switch to inquire whether a problem has occurred rather than wait for failure and than wait for the timers.

RSTP uses the term alternate port to refer to ports on a switch that could be used as the root port if the root port ever fails. 

#### Port Roles in RSTP:

| **Function**                                                    | **Port Role**   |
| --------------------------------------------------------------- | --------------- |
| Port that begins a non-root switches best path to the root      | Root port       |
| Port that will replace the root port if he fails                | Alternate port  |
| Switch port that is designated to forward to a collision domain | Designated Port |
| Port that will replace the designated port if he fails          | Backup port     |
| Port that is administratively  down                             | Disabled port   |

RSTP differs from STP in a few other ways as well. For example with STP the root switch creates the hello packets with all other switches and forwards them to the other switches. In RSTP each switch creates his own hello packets more so RSTP allows to switches to inquire between neighboring switches rather than wait for one to fail and wait for the timer to kick in.

### RSTP and the alternate (Root) Port Role

In STP each non-root switch will assign 1 port to be its root port (The lowest cost to the root switch) RSTP dose the same thing as STP when it comes to root port on noon-root switches.
RSTP adds more to it RSTP will identify the next best port to become the backup root port (alternate port). Alternate port is the second best option to be the root port. So there is no downtime if the root port stops receiving hello BPDU's the  alternate switch will become the new root port. The switch will put the failed root port (the original) in disabled state 

and assign the role of the root port to the alternate port. the new root port has no transition time in other roles or state it becomes the new root port and receives packets as soon as the switch gives it the role.

### RSTP states and Processes

Both STP and RSTP use port states, but not in the same way. Both RSTP and STP have the learning and forwarding states but unlike STP RSTP dose not have the listening state. RSTP renamed the blocking state to discarding.

STP has 2 state disabled and blocking but RSTP makes them in to one discarding.
RSTP combines both the state of blocking and disabled in to discarding.
Blocking state is when the physical interface works but STP blocks it to avoid a loop.
disabled state means that the administrator disabled the interface manually.
RSTP makes both states in to 1.

#### RSTP and STP port roles

| Function                                                                      | STP State  | RSTP state |
| ----------------------------------------------------------------------------- | ---------- | ---------- |
| Port is administratively disabled                                             | Disabled   | Discarding |
| Stable state when the port is blocking data packets in order to a void a loop | Blocking   | Discarding |
| Interim state without mac address learning and not packet forwarding          | Listening  | Not Used   |
| Interim state with mac address learning and no forwarding                     | Learning   | Learning   |
| Stable state that allows MAC learning and packet forwarding                   | Forwarding | Forwarding |

RSTP not only changes the states but the process as well in order to speed up the time between interim states. In both listening and learning state STP has to wait the forward delay time. When a change in the topology happens the switch tell the port to drop its mac address table as it can cause loops now that its not accurate anymore.
On top of having to send BPDU's to all the other switches in the network to tell them that a change in the topology has happened.

RSTP in the same scenario switches between the port state much faster. RSTP switches tell each neighboring switches to flush their mac address tables to avoid any loops. without relying on timers (forward delay that STP has to use on each state) ports that have flushed their mac address table can change state with out timers.
The biggest change is RSTP dose not rely on timer and is more efficient as a result.

### RSTP and the backup (designated) Port Role

RSTP backup port role is a new role in RSTP versus STP. RSTP back up port or designated port, allows RSTP to quickly replace the root port of a switch if the designated port ever stops working.

 But the backup port can only be used with hubs and so is unlikely used today. because for the backup port to switch with 0 delay the switch needs to connect 2 port to the same collision domain (the hub). as a hub is needed its is not a role we see in this day and age.

### RSTP port Types

in RSTP a point between 2 switches is called a point to point links and the ports are called ports because they connect exactly 2 devices to each other.

in RSTP point to point ports have 2 categories, ports that connect switches to each other are called just point to point ports. but ports that connect to an edge device (computer,server..) are called point to point edge ports.

the last port type RSTP possess is shared port, those port that are connected to a hub, the term shared  comes from the fact that the port is connected to a hub creating a shred Ethernet. (hubs repeat the packets that are sent to them out of all ports expect the incoming) RSTP assumes all ports connected to hubs are half duplex and making coverage slower on shared ports. RSTP treats all shared ports as half duplex.

## Optional STP Features

#### EtherChannel

Combines multiple  same speed cables in to one logical cable to show to STP so if 1 cable out of three fails for STP it is as the cable never failed as there are 2 more that work.
This makes it so STP has 0 convergences time because it dose not happen. as there is more than 1 physical cable. with out EtherChannel STP will block all the parallel cables and will take time changing states.

#### PortFast

Port fast changes states from blocking to forwarding skipping both listening and learning. Making it perfect for edge devices like computers and server. where the port cannot cause a loop. Making the port going to forwarding as soon as the client NIC on the other side is awake. other wise the client will have to wait for the full convergences time. (RSTP point to point edge acts in the same way)

#### BPDU Guard

BPDU Guard disables a port if the port receives BPUDS this can happen for exmaple when a user brings a dumb switch and plugs its network cable in to the switch.
The combination of FastPort and BPDU Guard on edge port that serve the user is the prefect combo for security and fast performance.

Another example of when BPDU guard can help are:

Attacker could connect switch to another switch the attackers switch can have a lower priority value. Becoming the root switch lowering the performance of the whole network. Or even worse the attacker could be copying all the data passing in his evil root switch.

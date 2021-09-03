# CCNA 200-301 Chapter 7




## Commands to config a switch


In few commands to configure the duplex and speed of a switch

```
config t
interface FastEthernet 0/1
duplex full
speed 100
description "a port"
```

you can use a range with 1-7 to configure multiple ports in the same time.

### No key word

you can use no to nullify the content of a command to

```
no speed
no duplex
no descreption
no shutdown
```



## Interface Status codes

Command to show the interface code status:

```
show interfaces
show interfaces description
```

there are 2 code 
line status and protocol status. Line status generally refers to whether Layer 1 is working.
While the protocol status refers to layer 2.

### Codes:

| Line Status           | Protocol Status    | Interface Status | Typical Root Cause                                           |
| --------------------- | ------------------ | ---------------- | ------------------------------------------------------------ |
| administratively down | down               | disabled         | The shutdown command was configured on the interface (opposite of no shutdown) |
| down                  | down               | notconnect       | No Cable,Bad Cable, Wrong cable pinout, Speed mismatch (physical cable issue most likely) |
| up                    | down               | notconnect       | Not expected on LAN switch physical interfaces               |
| down                  | down(err-disabled) | err-disabled     | Port Security has disabled the interface                     |
| up                    | up                 | connected        | The interface is Working                                     |

### Duplex

to see the speed and duplex setting we would use the command:

```
show interface status
show interfaces fa0/13
```

### Duplex miss match

the speed of 10mb or 100 by default uses half duplex 
if for some reason auto negation is not enabled on both interfaces the link will default to the switches lowest speed and duplex.

So if one side of a gig connection is set to use 100mb the other side that uses auto negations would use 100mb and half duplex.
As a result when a interfaces fails to auto negotiate it will fall back on to its defaults resulting in a chance to miss match duplex.

Unlike with a  speed miss match (if you set one side to 100 and the other to 10) duplex miss match will still let the link work (up/up)

## Interface Counters

| counter name    | reason                                                       | Cause                      |
| --------------- | ------------------------------------------------------------ | -------------------------- |
| Runts           | did not meet the minimum frame size requirements  (64 bytes) | can be collisions          |
| Giants          | exceeds the maximum frame size (1518 bytes)                  |                            |
| Input Errors    | a total of all the error counters (runts,giants,no buffer,CRC,frame,overrun,) | when an error is detected  |
| CRC             | did not pass the FCS check                                   | can be collisions          |
| Frame           | received frames have an illegal format                       | can be collisions          |
| Packet output   | Total number of packets forwarded out of the interface       | when a packet is forwarded |
| Collisions      | a collisions occurs when the interface try's to transmits the frame |                            |
| Late Collisions | the collisions happens after the 64th byte of the frame has been transmitted (properly working lan it should occur before the 64th byte) | duplex mismatch            |

* if CRC grow but the collisions don't it probably is a physical cable problem
* if Collisions grow on a half duplex interface it is most likely to a duplex miss match as one side of the cable sends after the 64th byte as its full duplex.

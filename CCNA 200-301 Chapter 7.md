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

the speed of 10mb or 100 can only use half duplex any higher will use full duplex


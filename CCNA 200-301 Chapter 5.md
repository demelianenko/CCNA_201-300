



# CCNA 200-301 Chapter 5



##  Switching Logic

Lan switching work on a mac source and destination logic as switches are layer 2 devices (usually).

Switch logic follow as:

- deciding if to forward the frame or drop it, using the destination mac address to decide.
- adding the source mac address and the port it originated from to the MAC table.
- forward only one copy of the frame in a loop free environment using sftp

### Content of the frame header

- preamble (7 bytes)

- SFD (1 byte)

- Destination Mac address (6 bytes)

- Source Mac address (6 bytes)

- type v4/v6 (2 bytes)

  Trailer 

  - FCS (4 bytes)

### Leaning Mac Addresses

The switches build their mac table by looking at the source mac address on each port and accosting that port with that mac address.
If a siwtch dose not know where on which port a mac address living it will flood to all its ports and ask where is that host with that mask address is.



When a switch looks for a mac address he flood the requests on all port, if more switches are connected we can be stuck in an endless loop.
For that we have STP.

### STP

Spanning tree protocol is a loop prevention.

### Step by Step Switch Logic 

- receive a frame if its a broadcast flood the port/ports
- if its not compare the destination of the mac address to the mac address table.
- record the source mac address in to the mac address table if its not there.
- send the packet to the known mac address port, if not send broadcast to find the port that has that mac address on.

### Switching commands and need to know

Showing mac table static

```
show mac address-table
```

to show dynamic mac adress use:

```
show mac address-table dynamic
```

in order to find a mac address you would add the mac address after the command above. ie:

```
show mac address-table dynamic address 0200.1111.1111
```

In order to see all the mac address learnt on a port or vlan.

```
show mac address-table dynamic interface Gig0/1
```

```
show mac address-table dynamic vlan1
```

### Managing ma address table

When a frame that the switch knows its source mac address comes in the time to live of that entry on the mac address table, gets reset back to 0.
by default the time to live of an entry in the mac table is 300 seconds.

you can see  the timer by using the command:

```
show mac address-table aging-time
```

if the mac table is full and the switch comes with a new mac address he will drop the oldest known entry in the list.

you can clear the list using the command:

```
clear mac table-address dynamic vlan		vlan1
clear mac table-address dynamic interface	fa0/1
clear mac table-address dynamic address 	0200.1111
```



#### Step to factory default your switch 

- ```
  write erase
  ```

- ```
  delete vlan.dat
  ```

- ```
  relaod
  ```


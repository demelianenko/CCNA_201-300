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

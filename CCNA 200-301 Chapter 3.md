# CCNA 200-301 Chapter 3

## WAN NETWORKS

### Leaded-Line Wans

is a line between 2 building that would be leased from an ISP for the 2 places to be on the same network.
Most popular layer 1 protocols on leased line are PPP and HDLC

### Address Resolution Protocol 

I used to discover the mac address of an destination in order to make the frame to be sent.
The ARP requests is a packet with that asks the host with a certain IP address to send his mac address back to sender. 
The client than sends back the his mac address so the sender.
TO see the arp table use the

```
arp -a
```

command

Arp is used on layer 2 as its mac address based and is used for local network communications, while the IP is used to locate the node the mac is used to talk on it will broadcast the ARP requests asking what is the mac address of the this IP address. in order to send a packet on the local LAN as it needs to become a frame and frame are layer 2 and use mac address addressing as do most switches. it will than be stored in ARP cache.

In order to send any frames the computer needs to know the mac address destination as its the layer 2 address. when send it to the switch. mac is the ip address of the layer 2. cant send traffic without arp and without mac address. because we go down the osi module the frames are the last thing before coming bits .

The switch will than look at its mac address table and forward the packet accordingly but with out arp the host wont know what the destination mac address and the switch wont be able to route the frame accordingly 


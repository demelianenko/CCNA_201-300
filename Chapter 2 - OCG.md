# Chapter 2 - OCG

------

## Overview of LAN's

The term LANs refers to family of standard that together define the physical and data-link layers.

### Typical SOHO LAN's

SOHO stand for small office/home office. A SOHO LAN would consist of a switch and router to be a network and of course clients. Many network this days combine the switch and router in one for example an ISP box that combines WIFI access point, router, switch, modem all in one box. But as a result if that box goes down all the network will go down with it.

### Typical Enterprise LAN's

The Enterprise LANs are much like the SOHO LAN's but on much bigger scale and most Enterprise network would have device redundancy and would not use one is all devices in order to make sure that even if the modem is down the network can still function. Traditionally  each floor would have a switch and all the switches would be connected to a core switch that would go to a fire wall and in to a modem and out to the internet. 

### The variety of Ethernet Physical Layer Standards

All the LAN standards come from the IEEE and include the number 802.3 in the beginning of the standard code.

| Speed     | Common name      | IEEE Name   | Standard IEEE Name | Type & max Length |
| --------- | ---------------- | ----------- | ------------------ | ----------------- |
| 10 Mbps   | Ethernet         | 10BASE-T    | 802.3              | Copper, 100 m     |
| 100 Mbps  | Fast Ethernet    | 100BASE-T   | 802.3u             | Copper, 100 m     |
| 1000 Mbps | Gigabit Ethernet | 1000BASE-LX | 802.3z             | Fiber, 5000 m     |
| 1000 Mbps | Gigabit Ethernet | 1000BASE-T  | 802.3ab            | Copper, 100 m     |
| 10 Gbps   | 10 Gig Ethernet  | 10GBASE-T   | 802.3an            | Copper, 100 m     |

## UTP Cabling pinout

### Straight-Through Cable pinout 

10 Mbps and 100 Mbps connections only use 2 pairs of cables.
The computer would transmit on pairs (1 and 2) and will receives on par (3 and 6) the NIC will transmit on pair 1 and 2 the switch knowing it will receive on this pair and transmit on the bottom pair 3 and 6 while the computer receives on 3 and 6.

For this connection to work we will need both ends of the cable to be mapped correctly 1 to 1 2 to 2 3 to 3 and 6 to 6
we use 2 pairs to transmit and 2 pairs to receives on each devices. the ends would be opposite if one end transmits the other receives and both pairs transmit or receives.

This will only work if the devices know before hand that on what end they will send and receive so for PC and switch this will work but for PC and PC this will not  and for that we will need a crossover cable.

Where unlike Straight-Through the pines are opposite so  1 to 3 and 2 to 6.

### Choosing the right cable pinout

Crossover: if the end points transmit on the same pin pair
Straight-Through: if the end points transmit  on opposite pin pairs

 

| Transmits on Pins 1,2  | Transmits on Pins 3,6 |
| ---------------------- | --------------------- |
| PC                     | Hubs                  |
| Routers                | Switches              |
| Wireless access points |                       |

For devices in the same column you would use crossover and for devices in different columns you would use Straight-Through

### UTP Cabling pinouts For  1000BASE-T (1Gib)

1Gib unlike base 10 and base 100 uses 4 pairs for transmitting and 4 pairs for receiving
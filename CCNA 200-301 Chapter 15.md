# CCNA 200-301 Chapter 15

## Installing Cisco Routers

Routers forward packet by connecting various physical network together using network links like Ethernet LAN and Ethernet WAN.

### Installing Enterprise Routers

Most Enterprise network would have many locations, most locations would have at least one switch to connect local devices to a local network. At least one router would be used to connect to the same switch to allow the local network and communicate with remote network. If the remote network are another site of the same company or completely other networks out on the Internet. All locations may also be connected to the central location using their WAN connection that connect to their router.

Serial link can be used to connect a router to External CSU/DSU. the SCU/DSU can be a stand alone device or baked in to the router. The CSU/DSU is used when using leased link between two locations It is needed to translate the packets back to the TCP/IP network.

### Cisco Integrated Service Routers

In todays age routers are used not just to forward packets they provide many more network services. In todays age alot of routers can act as switch as well as A wireless access points bundling all the feature together with the routing capabilities to make one device that is able to provide all the needs of a small office or branch. Cisco also commonly makes one of its Ethernet support SFP modulus so it can be used with a Fiber SFP. 

### Physical installation

1. For any Ethernet Lan interface, connect the Rj45 connector to a Lan switch.
2. For any Serial WAN port
   1. If using an External CSU/DSU, connect the router serial interface to the CSU/DSU and the CSU/DSU to the line from the telco company
   2. If using an internal CSU/DSU, connect the router serial interface to the line from the teleco line
3. For any Ethernet WAN Ports
   1. When ordering the Ethernet plan, make sure to know what type of connector the provider uses. And all the SFP requirements
   2. Install the SFP in to the routers and connect to the provider line
4. Connect the router console port to a PC as needed to be able to configure the router
5. Connect a power cable from the outlet to the power port on the router
6. Power on the router (Cisco routers usually have an on off switch unlike switches)

### Installing SOHO Routers

The line between Enterprise and SOHO (small office home) is blurry, in general a entrprise router would be installed in a permanent company location, while a SOHO router would be commonly found in a users home or a small location. 
SOHO routers would typically have two features that enterprise routers wont:

- SOHO routers almost always use the Internet and a VPN for thier wan connection to send packets back and fourth over multiple location. Unlike the leased teleco line an enterprise router would use.
- SOHO routers are almost a multi purpose device that does routing, Lan switching, VPN, Wireless and other features.

For en example a enterprise network can be made with Routers Ethernet switches, Wireless access points. While A SOHO multi service router could do the same as single device for a small amount of users. 

## Enabling IPv4 on Cisco Router Interface

### Accessing the router CLI

Accessing the router CLI works exactly the same as using the switch CLI. The Cisco switch and routers share many of the commands used in both:

- User and Privileged mode (Enable)

- Entering and exiting config mode, using the config t, end and exit

- Configuring of console, Telnet(vty) and enable secrets

- Configuring of Secure shell (SSH) encryption keys and username/passwords login credentials 

- Configuring the host name and interface desription

- Configuring the Ethernet interfaces that can negotiate speed using:

  ```
  Speed
  Duplex
  ```

- Configuring an interface to be down or up using:

  ```
  no shutdown
  shutdown
  ```

- Navigation of the different configuration mods:

  ```
  line console
  interface g0/0
  ```

- CLI help, command editing, and command recall features

- The meaning and use of startup-config(VRAM) running config (RAM) and external servers like the TFTP along with how to use the:

  ```
  copy
  ```

Some thing do work differently in routers compared to switches.

- Switches have IP address configured on their Vlan ports while Routers have the IP address configured directly on the physical ports.

- Many of Cisco's routers have an AUX port used for the router to be connected to a modem, for users to be able to dial in to the modem and use the cli. Switches do not have that feature. 

- Router ISO by default disallow both telnet and ssh, because of the typical router default settings:

  ```
  transport input none
  ```

  in the vty settings, unlike router switch deafult to allow both ssh and tty. to enable both or just tel net or ssh

  ```
  transport input telnet/ssh/all
  ```

The router CLI differs from the switch CLI commands

- Cisco layer two switch support the command:

  ```
  show mac address-table
  ```

  while Cisco routers do not

- Cisco routers supprot

  ```
  show ip route
  ```

  while layer two switches to not.

- Cisco layer 2 switch's use the

  ```
  show interface status
  ```

  command to list one line of the output per interface and routers do not. While routers use the

  ```
  show ip interface breif
  ```

  command to list similar information, bur siwtch use the command above.

### Router Interfaces

One big difference between router and switch is that routers support a much wider selection of interfaces. Lan switches support Ethernet    Lan interfaces of various speeds while router support serial, cable TV, DSL, 3G/4G wireless and many more. Most Cisco routers support at least  one Lan Ethernet  at various speeds as well as auto negotiations. For consistency the router ISO refers to all interfaces by their  maximum speed.

| Interface speed in Mbps | Interface name (type) | CLI command                              |
| ----------------------- | --------------------- | ---------------------------------------- |
| 10                      | ethernet              | interface ethernet PortNumberHere        |
| 10/100                  | fastethernet          | interface fastethernet PortNumberHere    |
| 10/100/1000             | gigabitethernet       | interface gigabitethernet PortNumberHere |

Many routers comes with a serial port that can be used to connect the router to a serial link, Each a point to point serial link can use either High Level Data Control (HDLC) the default or Point to Point protocol (PPP)
The format in which the router will refer to an interface is, the first will always be the command/word interface followed by the type (Fast/Gig/Serial) followed by the port number. 
The 2 most common command to display the interface information would be:

```
show ip interface berif
# or the shoerther version show ip int br
```

Would Display list with each interface on its own line with some basic information about the interface. like the IP address and the status

and

```
show interfaces
```

Would display large amount of information about the interfaces. As well as the description of the interface if it has one.

### Interface Status Codes

Each interface has two status codes, to be usable the interface status codes have to be both up. The first status code refers to layer 1, and the second refers to layer 2. 

| Name            | Location           | General Meaning                                              |
| --------------- | ------------------ | ------------------------------------------------------------ |
| Line status     | First Status code  | Refers to the Layer 1 status. For example if the cable is missing |
| Protocol status | Second status code | Refers generally to the data link (Layer 2) status. Its always down if the line status is down. If the Line status is up and protocol is down it usually means Data link layer configuration miss match. |

Several Combination of interface status code exist:

| Line Status           | Protocol Status | Typical Reasons                                              |
| --------------------- | --------------- | ------------------------------------------------------------ |
| Administratively down | Down            | the interface has been configured with the **Shutdown** command |
| Down                  | Down            | Unlike the Administratively down  this time the physical layer has an issue. for example the cable is not plugged in or broken. Or the other end of the cable is not plugged in. |
| Up                    | Down            | Almost always refers to a problem in the data link layer, most often a configuration problem. for example on a serial link one router has been configured with a HDLC and the other one with PPP |
| Up                    | Up              | Layer 1 and Layer 2 of this interface have no issue and work. |

### Router Interface IP Address

Cisco router needs some configuration before they are able to preform their duty. Unlike switch that can work out of the box. Before the router can start routing packets, it would need at least one interface that is enabled and have an IP address assigned to it.

- Most Cisco router interfaces default to being shutdown(off), and would need the 

  ```
  no shutdown
  ```

  Command used on them to enable them.

- Cisco Routers to not route packets out of the interface until the interface IP address and mask have been set. By default no IP address or mask are set to any interface

- Cisco Routers try to route IP packets to any interface that is up/up (Line status and Protocol status) and have their IP address and mask set.

| Command                      | Line of output per interface | IP configuration Listed | Interface Status Listed? |
| ---------------------------- | ---------------------------- | ----------------------- | ------------------------ |
| show ip interface            | 1                            | Address                 | Yes                      |
| show protocols [type number] | 1 or 2                       | Address/mask            | Yes                      |
| show interfaces              | Many                         | Address/mask            | Yes                      |

### Router Auxiliary Port

Both routers and switches have a console port that would allow to connect to them and configure them even without any interfaces or vlan's set with IP address. Most Cisco router also have a Aux Port, typically means to serve to make a phone call and connect to the router to issue commands. As an alternative to the Console port or ssh or tel net.

It works exactly like the console port but, the port will be connected to a modem and the modem will be connected to phone line. Allowing a network engineer to use a terminal emulator and a modem to call and access the router CLI over the AUX port. 

To configure a Aux port use the

```
line aux 0 #or another numebr
```

Just like the console port or SSH login and password can be used.

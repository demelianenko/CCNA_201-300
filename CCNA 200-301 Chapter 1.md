# CCNA 200-301 Notes Chapter 1

------

## TCP/IP Networking Model

### Overview of the TCP/IP Networking Model

The TCP/IP model is a set of protocols and tools that help standardize how network devices communicatee with each other. Allowing us to buy any product and have a plug and play capabilities.

| TCP/IP Model |
|:------------:|
| Application  |
| Transport    |
| Network      |
| Data-Link    |
| Physical     |

Common TCP/IP protocols and the layers they function in:

| TCP/IP Layer        | Example Protocols      |
| ------------------- | ---------------------- |
| Application         | HTTP,POP3,SMTP         |
| Transport           | TCP,UDP                |
| Internet/network    | IP,ICMP                |
| Data-Link&Psychical | Ethernet,802.11 (WIFI) |

### Application layer

Provides an "interface" between software running on a computer and the network.
The most popular TCP/IP application now days is the web browser.

#### HTTP

Hyper text transfer protocol is the most widely used web protocol. Http allows our browser and the web server to have a standard way of talking.

- A web browser send a HTTP packet that has get in the header with the file name of the file it wants to get.
- the server send an HTTP packet back with a status code (200 for example) as well as the data if the status code is OK (200)
- After the initial HTTP header the server wont bother to send more HTTP header and only will serve data from now on.

### Transport Layer

The 2 most common Transport layer protocols are TCP and UDP.
The Transport Layer provides a services to The application layer the services is error recovery.
each layer provides some service to a the layer above it.

#### Error Recovery Basics

HTTP depends on the TCP error recovery services and connection establishment. 
You wouldn't want to load a website with parts of it missing.
TCP uses acknowledgment of receiving data that hast been corrupted or lost.
The packets will be numbered and sequenced in the packet header.
That allows the host to tell the server I didn't receive the packet please resend it. 

#### Same-Layer and Adjacent-layer Interactions

Adjacent layer refers to a protocol relaying on a function of feature that a protocol in a closer layer upper or lower provides to it.
Just like TCP provides error correction to http.
same layer interaction refers to protocols on one machine wanting to communicate to the same layer protocol but on another machine.
The 2 computers use headers to hold that information. For example if an TCP packet got lost the receiving computer will send a packet back with the number of the missing packet in the header of the new packet. Letting know the sender to resend it before sending any father packets.

| Concept                     | Description                                                                                 |
| --------------------------- | ------------------------------------------------------------------------------------------- |
| Same-Layer interactions     | When a computer is send packet to a remote computer on the same TCP layer.                  |
| Adjacent-Layer interactions | When a computer relays on a feature on of the lower layers can provide to the current layer |

### Network Layer

#### IP routing Basics

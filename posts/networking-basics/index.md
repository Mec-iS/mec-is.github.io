<!-- 
.. title: Networking Basics
.. slug: networking-basics
.. date: 2015-07-29 17:35:22 UTC+02:00
.. tags: Networking, DevOps
.. category: Internet
.. link: 
.. description: 
.. type: text
-->

Thanks to [Stanford Online](http://online.stanford.edu/course/introduction-computer-networking)<br/>

# Networks Layers (ISO-OSI)
![OSI 4 layers communication](/assets/images/posts/ISO-flow.png)

## Network Layer
Delivers packets to computers

![IP in OSI](/assets/images/posts/ISO-4.png)

### Internet Protocol: characteristics
- IP is a *datagram service*. Datagrams are self-contained: data and header (destination address + source address). Datagrams are delivered hop-by-hop (node-to-node, router-to-router). Routers have forwarding tables for different IP addresses, they know only the next hop for the datagram in the packet.
- IP is *unreliable*. Packets may get lost or dropped, or simply delivered out of sequence compared with the sending sequence.
- IP guarantees the *best effort*: packets will be dropped only when necessary (recovery these errors is on Transport, or Link).
- IP is *connectionless*. It maintains no knowledge at all of the state or sent datagrams 

<br/><br/>
The IP layer does not guarantee that packets will be delivered in order or always follow the same path through the network. Each packet is individually routed. Often, all the packets in a flow *do* follow the same path. But the path may change if a link fails and packets follow a new path. Or if a router load-balances packets, packet-by-packet over several different output links.

### Internet Protocol: model and features

- IP is a deliberately simple service, to work on different kinds of physical infrastructure
- IP tries to prevent pakets looping forever (usually due to updates in forwarding tables), using the hop-count field in the datagram (TTL field).
- IP limits packets size depending on the underlying Link (different networks have different max-size for packets, Network layers in routers fragments packets exceed max-size)
- IP uses checksums to check the header is not being corrupted and the destination is right

### IPv4 Datagram

![IP in OSI](/assets/images/posts/ipv4-datagram.png)<br/><br/>

You can experience the different hops walked by a packed with the *traceroute* command (`$> traceroute -w 1 www.google.com`)

## Transport Layer
delivers data to applications

###TCP Byte Stream

Client and server establish a communication using the three-way-handshake (SYN > SYN/ACK > ACK):

- client sends a "synchronize" (SYN)
- server responds with a "synchronize/acknowledge" (SYN/ACK)
- client responds with an "acknowledge" (ACK)

Everybody can interact and learn about this behaviour with software like (Wireshark)[https://www.wireshark.org/]


## Packets
Packet switching, Layering, Encapsulation

### Packet Switching

*Reminder*: packets are self-contained, independent chunks of communication. <br/><br/>
Flow: a collection of datagrams belonging to the same end-to-end communication (e.g. a TCP connection).<br/><br/>

Packet switching principle in a common router: *Independently, for each arriving packet, pick its outgoing link. If the link is free, send it. Else, hold the packet for later.*. <br/><br/>

This principle has important consequences:

- simple packet forwarding: each router has its own forwarding table, that defines the next hop to the destination defined in the packet.
- efficient sharing of links (lines between hops): many different packets from different users/connections can share the same link.
- considering that data traffic is bursty. packet switching allows to use all available link capacity and to share link capacity. This is called *statistical multiplexing*.


### Layering
Layering is a well-know structure for computer and network systems

![Layers](/assets/images/posts/layering1.png)<br/><br/>

Example of use layering outside networks:<br/>
![Layers in computer systems](/assets/images/posts/layering2.png)<br/><br/>

Five main reason for Layering:

- Modularity: It breaks down the system into smaller, more manageable modules
- Well defined services: Each layer provides a well defined service to the layer above
- Reuse: A layer above can rely on all the hard work put in by others to implement the layers below
- Separation of concerns: Each layer can focus on its own job, without having to worry about how other layers do theirs 
- Continous improvement (ex. cross-layer optimization)
- A 6th benefit is specific to layered communication systems, such as the Internet. That is peer to peer communications

### Encapsulation
When you send a TCP segment, for example, it’s inside an IP packet, which is inside an Ethernet frame. Encapsulation is how this works. Encapsulation is the principle by which you organize information in packets so that you can maintain layers, yet let them share the contents of your packets.<br/><br/>

The way this works is each protocol layer has some headers, followed by its payload.<br/><br/>

*Example*: <u>A HTTP GET request of a Wi-Fi Ethernet</u>: The HTTP GET request is the payload of a TCP segment. The TCP segment, encapsulating the HTTP GET, is the payload of an IP packet. This IP packet, encapsulating the TCP segment and the HTTP GET, is the payload of a Wi-Fi frame. <br/><br/>

![Encapsulation of a HTTP GET](/assets/images/posts/encapsulation1.png)<br/><br/>
*Image*: This is how WireShark represent the example above, each line represents the different layers' payloads (from bottom to top: the application layer with the HTTP request, the transport layer with the TCP segment, the IP layer with the datagram, and the Ethernet frame).<br/><br/>

*Example*: <u>VPN</u>: HTTP inside TCP inside IP inside TLS inside TCP inside IP inside Ethernet. In VPN the initial payloads are encypted in TLS and sent to the VPN gateway, that can decrypt them and route the decrypted payload inside the private network.

## Addres Resolution Protocol (ARP)

ARP is the mechanism by which the network layer can discover the link address associated with a network address it’s directly connected to. It's the algorithm that maps IP packets address to link address for the next hop in the link for a given packet. An IP address is a network-level address. It describes a host, a unique destination at the network
layer. A link address, in contrast, describes a particular network card, a unique device.

![Network and Link addresses](/assets/images/posts/ARP1.png)<br/><br/>

The fact that link layer and network layer addresses are decoupled logically but coupled in practice is in some ways a historical artifact. When the Internet started, there were many link layers, and it wanted to be able to run on top of all of them. <br/><br/>

ARP is a simple request-reply protocol. Every node keeps a cache of mappings from IP addresses on its network to link layer addresses. The requester can ask for a network-link address map so that every node hears the request, a node sends requests to a link layer broadcast address.
Every node in the network will hear the packet.
Furthermore, ARP is structured so that it contains redundant data. The request contains
the network and link layer address of the requestor. That way, when nodes hear a request
(since it’s broadcast), they can insert or refresh a mapping in their cache.

![ARP packet](/assets/images/posts/ARP2.png)<br/><br/>

If a node needs to send a packet to or through an IP address whose link layer address it does not have, it can request the address through ARP.
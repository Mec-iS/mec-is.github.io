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
delivers packets to computers

![IP in OSI](/assets/images/posts/ISO-4.png)

### Internet Protocol: characteristics
- IP is a *datagram service*. Datagrams are self-contained: data and header (destination address + source address). Datagrams are delivered hop-by-hop (node-to-node, router-to-router). Routers have forwarding tables for different IP addresses, they know only the next hop for the datagram in the packet.
- IP is *unreliable*. Packets may get lost or dropped, or simply delivered out of sequence compared with the sending sequence.
- IP guarantees the *best effort*: packets will be dropped only when necessary (recovery these errors is on Transport, or Link).
- IP is *connectionless*. It maintains no knowledge at all of the state or sent datagrams 

### Internet Protocol: model and features

- IP is a deliberately simple service, to work on different kinds of physical infrastructure
- IP tries to prevent pakets looping forever (usually due to updates in forwarding tables), using the hop-count field in the datagram (TTL field).
- IP limits packets size depending on the underlying Link (different networks have different max-size for packets, Network layers in routers fragments packets exceed max-size)
- IP uses checksums to check the header is not being corrupted and the destination is right

### IPv4 Datagram

![IP in OSI](/assets/images/posts/ipv4-datagram.png)<br/>

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

*Reminder*: packets are self-contained, independent chunks of communication. <br/>
Flow: a collection of datagrams belonging to the same end-to-end communication (e.g. a TCP connection).

Packet switching principle in a common router: *Independently, for each arriving packet, pick its outgoing link. If the link is free, send it. Else, hold the packet for later.*. 

This principle has important consequences: 
- simple packet forwarding: each router has its own forwarding table, that defines the next hop to the destination defined in the packet.
- efficient sharing of links (lines between hops): many different packets from different users/connections can share the same link.
- considering that data traffic is bursty. packet switching allows to use all available link capacity and to share link capacity. This is called *statistical multiplexing*.

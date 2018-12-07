---
layout:     post
title:      Network Note
excerpt:    Details about my intern project
date:       2018-09-19 10:00:00
author:     "Shane"
header-img: "img/bg-mac.jpg"
catalog: true
tags:
    - Note
    - Networking
---

FTP: contron connection 21, data connection 20 <br>
SSH: 22 <br>
Telnet: 23 <br>
SMTP: 25 <br>
HTTP: 80 <br>
DNS: 53 <br>
DHCP: server 67, client 68 <br>

# Chapter 1

Protocol: define format, order of messages sent and received among network entities, and action taken on message transmission, receipt<br>
ISP: internet service provider

Network Edge: hosts running app<br>
Network Core: packet switches(routers, link-layer switch), links<br>
Access Networks: phycial links that connect an end system to the first router.<br>
- broadband residential access: DSL (digital subscriber line), cabel modem (HFC, hybrid fiber coax)
    - DSL make use of telephone infra
    - HFC make use of cable television infra
- institutional access: LAN, e.g. Ethernet. PC->copper wire->Ethernet switch->Router->ISP
- mobile network: wide-area wireless access network, base station
Phycial Media: coax, fiber, radio<br>
Home network: combine wifi and broadband residential access. <br>
    wireless PC ->access pointer -> router -> cable modem -> Internet <br>
                   stationary PC -> <br>

packet: chunks of data<br>
store-and-forward transmission: the switch must receive the entire packet before transmitting the first bit of the packet onto the link.<br>
output buffer: stores packets which the switch is about to send into the link.<br>

circuit switching<br>
- reserved resources
- bandwidth division into pieces
- Pro: guatranteed constant rate due to the dedicated allocation of bandwidth
- Con: resource piece idle

packet switching<br>
- unreserved resources
- full bandwidth
- Pro: resources used as need
- Con: queue delay in congested network

delay<br>
- processing: examining header, determining where to direct the packet, checking bit-level error
- queueing: wait at output buffer for transmission
- transmission: push all of the packet's bits into the link
- propagation: time required to propage from beginning of the link to router
- traffic intensity: La/R. a denotes the average packet arrival rate

- Application: supporting network applications
- Transport: process-process data transfer
- Network: routing datagrams from one host to another
- Link: data transfer between neighboring network elements.
- Physical: bits on the wire, over the air.

Message, Segment, Datagram, Frame. Each layer provides its services by (1) taking certain actions within that layer and by (2) using the services of the layer directly below it. A packet contains has two types of fields: a head field and a payload field. The payload is typically a packet from the layer above.

# Chapter 2

Server: Permanent IP. Always-on. <br>
Client: Dynamic IP. Intermittently connected. <br>
P2P: No always-on server. Arbitrary hosts directly communicate. Self-scalability: new peer brings new service capacity as well as new service demands.

Socket: The process send messages into, and receive messages from a software interface. Socket is the API between the application layer and the transport layer. <br>
What of socket could controlled by developers on the transport-layer side?
- transport protocol: TCP or UDP
- some parameters: maximun buffer and segment.

App-layer Protocol Defines<br>
- Type of messages exchanged. e.g., Request, reponse
- Message syntax. What fields in message and how fields delineated.
- Message semantics. Meaning of fields.
- Rules. When and how process send and respond to messages.

Web page: consists of base HTML page and serveral referenced objects. Addressed by a URL. <br>
URL: host name and path name. <br>
HTTP: HyperText Transfer Protocol<br>
- stateless. Maintain no past client requests.
- non persistant: each request/response pair sent over a separate TCP connection. 2RTT+L/R to fetch each object. Each object transfer suffers from slow start. 
- persistant: all of rquests and responses sent over the same TCP connection. Clients send requests for all referenced objects as soon as it receives base HTML page.

HTTP request format<br>
- request line: method, url, version
- header lines
- blank line
- entity body

Http response message<br>
- status line: version, status, phrase
- header lines
- blank line
- entity body

FTP: File Transfer Protocol<br>
- Out-of-band: Seperate control connection and data connection. Close data connection every time a file is transferred.
- maintain state: current directory, ealier authorization.

Mail Server: outgoing queue, mailbox
SMTP: simple mail tranfer protocol, delivery/storage to receiver's server
- SMTP: push (sending server send pushes the file to receiving server); 7-bit ASCKII format; place all objects into one message.
- HTTP: pull (pull files from server at their convenience); not impose this restriction; encapsulate each object in its own HTTP response message.
Mail Access Protocol: retrieval from receiver's server<br>
- POP3: posf office protocol-version 3. Three phases: authorization, transaction(mark/remove deletion mark), update <br>
- IMAP: Internet Mail Access Protocol. 
    - organize message in folders
    - maintain user state. e.g., folder name, mapping between folders and messages.
    - obtain message component.

DNS: Domain Name System. (1) a distributed database implemented in a hierarchy of DNS servers. (2) an applicatin protocol that allow hosts to query the distributed database. <br>
- translate hostname to IP address
- host aliasing
- mail server aliasing
- load distribution. For replicated web servers, a set of IP address is associated with a canonical hostname. The server would reply the whole set but rotate the ordering. 

three classes of DNS servers
    - root server: contacted by local name server that cannot resolve name
    - top-level domain (TLD) server: responsible for top-level domains.
    - authoritative server: provide authoritative hostname to IP address mappings for organizations' named hosts.

local domain name server: Each ISP has a default domain name server. 
- Cache recent name-to-address translation pair.
- Cache IP adress of TLD servers, thus bypass the root server in a query chain.
- Act as proxy, forwards query into DNS server hierarchy.

Why DNS caching: improve delay performance, reduce DNS messages ricocheting around the Internet.

DNS query pattern: The query from the host to local domain name server is recursive, and the remaing queries are iterative. For iterative query, contacted server replies with name of server to contact. Drawback of recursive fashion: heavy load at upper layers. put burden of name resolution on contacted name server.

RR: resource record.
- (Name, Value, Type, TTL)
- Type = A, Name is a hostname, Value is the IP adrress for the hostname.
- Type = NS, Name is a domain, Value is the hostname of an authoritative DNS server that knows how to obtain IP addresses of hosts in that domain.
- Type = CNAME, Value is the canonical name for alias hostname Name.
- Type = MX, Value is the canonical name of a mail server having alias hostname Name.

# Chapter 3
MSS: Maximum Segment Size. The maximum amount of application-layer data, excluding header size. 1460.
MTU: Maximum Transmission Unit. The maxinum size of link-layer frame. 1500.

Logical communication between app layer processes running on diffrent hosts: From an application's perspective, it is as if hosts running processes were direct connected.

Multiplexing: Gathering data from multiple app layer processes, enveloping data with header
Demultiplexing: Delivering received segments to correct app layer process
Internal action between layers: in the network, messages from different applications will be in different packets.

UDP: user datagram protocol
- unreliable: segment may be lost or deliverded out of order to app
- connectionless: no handshaking between sender and receiver; each UDP segment is handled independently of others
- Pro: no connection establishment, no connection state, small header, no congestion control

UDP header: Src Port, Dest Port, Length(including header), Checksum (detect flipped bit)

UDP Checksum
- Cause:  by noise in the links or while stored in the router.
- Methedology
    -Sender: one's complement of the sum of all 16-bit word, with any overflow being wrappred around.
    -Receiver: one's complement sum. All 1 indicates no error.

TCP: Transimit Control Protocol, running only in the end system.
- connection-oriented: handshaking inits sender, receiver state (buffer size, init #seq, etc.) before data transfer.
- reliable, in-order byte steam
- full-duplex: bi-direction dataflow in the same connection
- point-to-point: one sender, one receiver

Reliable Data Transfer Service
- Goal: Data is delivered without error, loss or duplicate and in the proper order.
- sequence number: the byte-stream number of first byte in the segment
- acknowledgement number: the sequence number of next expecting byte
- cumultative acknowledgement: acknowledge byte up to first missing byte in the stream.
- How: timer, cumulative acknowledgement, retransmission
- Fast retransmit: If receive tripple duplicate ACKs for y, retransmit the missing segment y before timeout. Why? the timeout period is relatively long, forcing the sender to dealay resending the loss pocket.
- Why gaps in the receiver buffer: packet loss, reorder in the network.

TCP ACK Generation (No delayed ACK for this class)
- In-order segment arrival, with expected #seq. Send single cumulative ACK.
- Out-of-order segment arrival, with higher-than-expected #seq. Send duplicate ACK, indicating #seq of the lowest end of the gap.
- arrival of segment filling gap. Send ACK if segment starts at the lower end of gap.

Flow Control
- Sender won't overwhelm receiver
- rwnd = RcvdBuff - [LastByteRcvd-LastByteRead], spare room in the receiver buffer
- How: Receiver explicitly infroms the ammount of free buffer via rwnd. Sender keep the amount of transmitted but unACKed data is less than rwnd. i.e., LastByteSent-LastByteACK <= rwnd at the sender side.
- Corner Case: When rwnd=0, TCP requires sending segment with one data.

Congestion Control
- Definition: a source throttles its sending rate when the network is congested.
- How: The amount of unACKs is smaller than cwnd, LastByteSent-LastByteACK <= min{cwnd, rwnd}. Throughput = cwnd\*MSS/R. TCP uses ACKs to clock its increasing in cwnd size. 

Congesition Stage
- slow start: increase cwnd by 1MSS every time a transmitted segment is first acknowledged. In other words, cwnd grow exponentially every RTT. 
- congenstion avoidance: increase cwnd by 1MSS every RTT.
- fast recovery: increase cwnd by 1MSS for each dup Acks.
- TCP Taheo: Unconditionally cut down cwnd to 1MSS when eithor timeout or dup ACKs occurs.
- TCP Reno: incorporate fast recovery. Skip slow start stage while receiving duplicate ACKs.

# Chapter 4

## Network Service
- routing: determine 'good' path through network from source to destination
- forwarding: transfer a datagram from incoming link to outgoing link
- Virtual-circuit has the funciton of call setup thus provides connection service.
- Datagram network provides connectionless service.

## forwarding
- forwarding table: the entries of the pair of prefix and outgoing link interface
- prefix: network portion of CIDR's address, representing a range of IP address
- longest prefix (most specific) matching: The router finds the longest prefix that matches the destination address and then forwards the packet to the associated outgoinglink interface.

inside router
- input port: line termination -> data link processing -> lookup/fowarding (store a shadow copy of forwarding table, achieve complete processing at line speed without invoking router processor), queueing(datagrams arrivate from network faster than forwarding rate into switch fabric)
- switch fabric: memory, bus, crossbar
- output port: queueing (multiple inputs send to the same output) -> data link processing -> line termination

## IP Protocol
- forwarding and addressing.
- TTL: time-to-live, decrease by one each time the datagram is processed by one router. Avoid circulating forever.
- Identifier, flag, offset: IP fragmentation and reassembly. Identify and order related datagrams.
- Why IP fragmentation? An router may interconnect different types of links with different MTU.

IP Address: network portion and host portion. Associated with interface instead of hosts or routers. <br>
Subnet: device interfaces within the same network portion. Physically reach each other without intervening router.

CIDR: Classless Interdomain Routing. 
- Role: IP address assignment strategy.
- Characteristics: Network portion can have arbitrary length. Written in the form a.b.c.d/x. The x leftmost bits constitutes the network portion and are referred to as the prefix.
- An organization is assigned with a range of address with a common prefix. Routers outside the oranization's network consider prefix, since the single entry a.b.c.d/x is sufficient to forward packets into any destination within the organization. Thus, it reduces the fowawarding table size.
- The remaing 32-x bits can distinguish device interfaces withion the organization. These lower bits can have additional subnet structures.
- Address aggregation: use a single prefix to advertise multiple networks.
- What kicks off if the address is outside of the block? Longest prefix matching.
- Why CIDR? Reduce the fowawarding table size. Avoid address exhausition.

DHCP
- Dynamic Host Configuration Protocol. Host can obtain 5 configurations: host ip address, subnet mask, gateway router ip address and 2 dns server ip adress.
- DHCP has 4 steps in the process. discover, offer, request, ACK.
- UDP based. dst = 255.255.255.255, client src = 0.0.0.0. DHCP server port = 67, DHCP client port = 68.

NAT
- private IP Addr: 10.0.0.0 A, 172.16.0.0 B, 192.168.0.0 C.
- Why NAT? All device inside local network uses just one IP address, sufficient IP address use. Security. Reduce IP update traffic.
- NAT table: private host IP, port - NAT IP, new port
- Why include port? Datagrams arrive from WAN have same dst. Router use port number to identify which host the datagram should be sent to.

ipv6
- 128 bit
- fixed length header : 40
- don't include most of options IPV4 can incldue: no fragmentation or checksum.
- several fields are same in spirit
    - ipv6: traffic class, payload length, next header, hop limit
    - ipv4: service type, datagram length, up-layer protocol, time to live
- Dual Stack: some routers can translate between tow formats. Loss flow fields.
- Tunneling: IPv6 carried as payload in IPv4 datagram among routers.

ICMP: Internet Control Message Protocol.
- error reporting
- echo request/reply, used by *ping* and *traceroute*.

## Routing

Link-State
- Find single source shortest path. All routers have complete topology and all link cost.
- Cons: oscillations possible, e.g. cost = traffic

Distance-Vector
- wait for cost change (in links) or vector update (from neighbors)  -> recompute -> notify only if paths to any dst change.
- distributed and decentralized: Routers only know physically connected neighbors and directly attached link cost. Update its own distance vector only when it sees a cost change in directly attached links or receives a distance vector update from phycially connected neighbors.
- iterative and self-terminating: continues until no nodes exchange info. No 'signals' to stop.
- asynchronous: Needn't to exchange info or iterate in lock step.
- Cons: convergence time varies

Hiearachical Routing
- scalable and administrative autonomy.
- autonomous system: organize routers into regions.
- IGP: interior gateway protocol, intra-AS routing, RIP, OSPF, EIGRP. Determine routing paths for source-destination pairs that are internal to the AS.
- BGP: border gateway protocol, inter-AS routing, standard. (1) Obtain subnet reachability from neighboring ASs. (2) propogate subnet reachability to entire AS. (3) determine routing paths based on the reachability and policy.
- gateway router: responsible for forwarding packets to destinations outside AS

RIP: Routing Information Protocol.
- DV, UDP.
- Distance Diameter: 15
- Advertisement: route up to 25 dst
- Used by small organization.

OSPF: Open short path first
- LS, TCP
- DV exchange period: 30s
- router type: boundary, backbone, area border.
- two-level hierarchy: backbone and local area.
- local area: LS advertisements only in area.
- backbone: exactly one, contains all area border routers. route packets between different areas.
- relatively complicated, used by giant company.

OSPF Features not in RIP:
- multiple same-cost path
- multiple link cost metrics
- multicast support
- hierarcy in large domain
- security: TCP, authenticated msg

BGP
- DV, TCP.
- Most complicated.

How a hierarchical organiztion of the Internet has made scalability possible?<br>
Routers are organized in ASs. Within an AS, all routers run the same protocol. The problem of scale is solved since an router in an AS need only know routers within AS and subnets attaches to AS. To router accross ASs, the inter-AS is based on the AS graph and don't take interior routers into account.

Why hierarchy?
- save table size
- reduce update traffic

Why is different is IGP and BGP?
- IGP
    - Policy: Single admin, no policy decision needed
    - Performance: can focus on.
    - Scale: less concern. 
- BGP:
    - Policy: control how the traffic routes, who route throuth its network.
    - Performance: policy domain over performance
    - Scale: critical issue becasue it has to handle routing across large number of networks.

## Broadcast and Multicast
Broadcast
- send to all nodes
- flooding: Duplicate the received pkt and broadcast copies to all neighbors. Cons: cycles and broadcast storm
- controlled flooding: Broadcast only if the received pkt wasn't broadcasted before.
    - RPF: reversed path forwarding. A router broadcasts pkt only if pkt is on the link that is on its shortest unicast path back to the source.
    - Cons: redundant received pkt.
- spanning tree: Broadcast alone tree.
    - Pros: No redundant
    - How: Each node unicasts a join message addressed to the center node. Messages are forwarded util arrive at the center or at the node that already belongs to the spanning tree.

Multicast
- send to a subnet of nodes
- PIM: Protocol Indepedent Multicast
- PIM Sparse mode: center based. 
    - Group member explicitly join
    - Switch to source-specific tree, less concentration

# Chapter 5
## Link Service
- framing
- link access: Medium Access control protocol.
    - point-to-point: single sender and receiver. Send whenever the link is idle.
    - broadcast: coordinate the frame transmissions of many nodes over the broadcast link.
- reliable delivery: used in wireless network.
- flow control: pacing sender and receiver
- error detection and correction
    - parity checking: A parity value for each column and row. EDC contains i+j+1 bits. Basic idea.
    - checksum: used only in layer 4.
    - cyclic redundacy check: adapter: 
- full duplex and half duplex: nodes at both side may transmit packets at the same time. input and output in the same interface

## CSMA
- random access: allow and recover from collisions. Used by Ethernet. 'Taking turns' and channel partitioning is the other two broadcast access MAC protocol.
- CSMA: Carrier sense multiple access
    - sense channel before transmission.
    - busy channel: defer transmission
    - idle channel: transmit entire packet
    - retransmit
        - persistent: instable. retransmit immediatedly with probability when sense idel channel.
        - non persistent: retransmit after random intervel
    - Collision probability deponds on the propogation delay and distance.
    - Cons: channel waste, transmit entire package even though a collision happen.
- Ethernet CSMA/CD (Collision Detection)
    - Action when collision occurs
        - abort (cease/ refrain from) 
        - send jam signal: make sure all other transmitters aware of collision
        - Exponential backoff random wait: adapt retransmission attempt to esitimated current load
    - collision detection: easy in wired network.
    - Pros: reduce channel waste

## ARP
- MAC Address: 48 bit, burn in the adapter ROM, unique.
- Address Resolution Protocol: resolve IP to MAC only on the subnet
- ARP table entry's TTL: 20 min

## Switch and Hub
Hub
- one broadcast domain and one collision domain
- 10BaseT: 10 Mbps rate, T stands for twisted pair. 100 meters distance per hub. star topology.
- LAN segment: each connected LAN
- Repeaters repeat bit in both direction.
- CSMA/CD implemented in hub
- Hierarchy: backbone at top
- Pros: cheap; Multi-tier provides degragation
- Cons: single collision domain, collide with all nodes; cannot connect diff Ethernet types.

Switch
- One broadcast domain and dedicated collision domain for each interface
    - full duplex, outgoing buffer
    - don't need CRC and CSMA
- transparent: a node addresses a frame to another node (rather than addressing the frame to switch) and happily send the frame into a LAN, unaware that a switch will be receiving the frame and forwarding.
- Self learning: learn which hosts can be reached through which interface. When frame arrives, combine the incoming link and MAC address of sender.
- Filtering and forwarding: index switch table.
    - not found: flooding
    - found: drop if the frame comes from the destination segment (the interface associated with the destination MAC = the incoming interface); otherwise, forward to the associated interface.

Switch vs Router
- elimination of collisions: buffer frames and never transmit more than one frame on a LAN segment at any one time
- heterogeneous links.
- management: disconnect the malfunctioning (jarbbering) adapter.

Why Switch has buffer at outgoing link?
- arrivate rate exceed the link capacity
- to make sure never transmit more than one frame on a LAN segment at any one time to avoid collision on broadcast link.

VLAN: trunk prot
- why VLAN: avoid lay-2 broadcast traffic crossing entire LAN; security
- define multiple VLAN over single physical LAN infrastructure via tag
- Trunk port: carry frames between VLANS defined over multiple physical switches
- forward between different VLANs: router

Switch VS Router
- Both are store-and-forward
- Both have forwarding table: switch is self-learning while router's table is computed by routing algo.
- Both isolate traffic
- Swith: plug-and-play, relatively high filtering and switching (less per-packet processing time, process only up through layer-2). Restricted to a spanning tree 
- Router: control broadcast storm via TTL and firewall

## MPLS
- Multiprotocol Label Switching: use a fixed length label without even touching the IP header
- Speed up switching in the router. Longest Prefix matching too flow
- distribute traffic to the same destination through different path
- VPN, virtual private networks. isolate resources and addressing

## DataCenter Network

- border router: connect the data center network to the public Internet
- access router: subnet
- tier-1 switch: distribution switch, associated with load balancer
- load balancer: balance the load accross hosts as the function of their current load. NAT-like function
- tier-2 switch: same application, VLAN
- TOR switch: interconnects the hosts in the rack with each other and with other switches in the data center
- redundancy: TOR and tier-2
- fully connected: improve the rack throughput via multiple routing paths; reliability via redundancy

# Chapter 6

- SNR: signal-to-noise rate
- BER: bit error rate
- given SNR, higher throughput, higher BER
- larger SNR, easier to extract signal from noise. good
- given throughput, increase power, higher SNR, lower BER. good

# Other
LAN: a computer network concentrated in a geographical area

LAN Segment

CRC: Generator G divide into D\*2^r XOR R without remainder. G has r+1 bits. R = reaminder(D\*2^r/G)

buffer formula: RTT \* C/root(N), C represents link capacity, N denotes  flows

virtual circuit: guaranteed rate and dedicated bandwidth. expensive

DHCP: 20s, ARP: 20min

CSMA: half duplex and wireless AP

switch fabric: memory, bus, crossbar

What is call setup?

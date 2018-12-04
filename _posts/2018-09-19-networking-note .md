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
- forwarding table: the entries of pairs of prefix and outgoing link interface
- prefix: represents a range of IP address
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
- DHCP has 4 steps in the process. discover, offer, request, ACK. The first 3 messages is broadcast
- UDP based. Broadcast: dst = 255.255.255.255, client src = 0.0.0.0. DHCP server port = 67, DHCP client port = 68.

NAT
- private IP Addr: 10.0.0.0 A, 172.16.0.0 B, 192.168.0.0 C.
- Why NAT? All device inside local network uses just one IP address, sufficient IP address use. Security. Reduce IP update traffic.
- NAT table: private host IP, port - NAT IP, new port
- Why include port? Datagrams arrive from WAN have same dst. Router use port number to identify which host the datagram should be sent to.

ipv6
-no fragmentation or checksum. flow.
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
- DV exchange period: 30s
- Advertisement: route up to 25 dst
- Used by small organization.

OSPF: Open short path first
- LS, TCP
- router type: boundary, backbone, area border.
- two-level hierarchy: backbone and local area.
- local area: LS advertisements only in area.
- backbone: exactly one, contains all area border routers. route packets between different areas.
- relatively complicated, used by giant company.

BGP
- DV, TCP.
- Most complicated.

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
- full duplex and half duplex: nodes at both side may transmit packets at the same time.

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
    - Action when collision occurs: abort (cease/ refrain from) and send jam signal. Exponential backoff wait.
    - collision detection: easy in wired network.
    - Pros: reduce channel waste


## 4 scrapy
Switch 48 port

Router 不会传播broadcast

switch会

CRC

why linked layer relia: save resource

RJ45, Network layer view, Router 
Input: 0/1 DL forwarding table

    Output: buffer, DL, 0/1

PIM SPARSE 124

BGP: most complicated

rip: easy to config

ospf: large company.

is ospf not in rip: 95

how ospf orgnazie: go to area zero

Linked state and disvector any is done: 91. routing protocol has priority

stop exchange: 1 parameter is not the saen


Global: 

Intranet: who decide what protocol. network ministrater. atonogy

2 Character of routing algorithm Link-State OSPF and Distance-Vector RIP

BGP fallen 在 Internet.

Dual stack

more efficient use for IP space. classful addressing, classless addressing

one to one translation: NAT
one to one many translation: PAT
why 4 is better than 6

NAT: RFC 1912, What is the definition
PAT: 

private IP Addr: 10.0.0.0 A, 172.16.0.0 B, 192.168.0.0 C 

5 Info, DHCP: IP mask gw dns1 dns2

8 wires/4pairs

full duplex: input and output in the same interface.

interface: 3 layers. bit, MAC Addr,IP addr

buffer公式： RTT * C

2 router algo: BGP ERGARP linespeed: bandwidth

3 farctor in net work layer, Call set up, routing, forwarding

forwarding (inside router, look up forwaring table, send to the output interface) vs routing.

virtual channel: guranteed rate, dedicated bandwidth. dis: expensive

## 5
Transport: Logical end

Network: Router to Router

LinkedLayer : node (adapter, 网卡) to node, why. Collision.

synchronized: time order

100 meters

fully duplex, parital duplex

CSMA/CD

DHCP 返回5 20s

ARP 20minites

48 Mac: 24 manufacture

CRC 算法

Ethernet cheaper, openstandard

When Ethernet's CSMD: half duplicate

Each link is its own collision domain.

Swithcer - Switcher 不阻止 broadcast ，因为有store, 相连的s impact with each other
Router-Router. 阻止

switch 有点想ARP 可以自我学习

LAN segment

Switch: CPU memory

贵的swittch，4个port 对应 一个CPU. Console port可以连PC -> config IP addr, http. serialially

box 可以you trunk frame

Trunk的字段只有switch有 802.1Q frame。

Token passing不考

CSMD: half duplex involved in hub, wireless AP
full duplex, switch dont need

hub: 如果有一个collision， 所有人的都可以感到。 wireless
one broadcast 和 one collision domain

switch: 48 collision domain

Switch keep track of TIES MAC ADDRESS WITH INTERFACE, keep ARP TABLE

Why switch store and forward: check error

如果switch的摸个port是full dupelx，不需要check error

table 20 minites

hub每个端口的传输速录是一样的

switch: learning, VLAN

ethner cable: 8 wire, 4 pair. half duplex, 占有一个pair， t/r共享 有冲突

43-44 on test

boder router - core router - distribution swithers - access swithches

load balancer: 

TOR: top of rack

redundancy zai tier-2和tor

two options： pipe bigger

77
iGP：3
EGP：BGP
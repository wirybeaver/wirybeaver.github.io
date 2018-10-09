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

Common Port

ping traceroute 用的什么协议

FTP: command 21, data 20
SSH: 23
SMTP: 25

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

TCP: Transimit Control Protocol

Reliable Data Transfer Service
- Goal: Data is delivered without error, loss or duplicate and in the proper order.
- How: timer, cumulative acknowledgement, retransmission

Flow Control
- Sender won't overwhelm receiver


Congestion Control
- Definition: a source throttles its sending rate when the network is congested.
- How: The amount of unACKs is smaller than cwnd. TCP uses ACKs to clock its increasing in cwnd size. 
- Principle:

Congesition Stage
- slow start: increase cwnd by 1MSS every time a transmitted segment is first acknowledged. In other words, cwnd grow exponentially every RTT. Throughput = cwnd*MSS/R
- congenstion avoidance: increase cwnd by 1MSS every RTT.
- fast recovery: increase cwnd by 1MSS for each dup Acks.
- TCP Taheo: Unconditionally cut down cwnd to 1MSS when eithor timeout or dup ACKs occurs.
- TCP Reno: incorporate fast recovery. Skip slow start stage while receiving duplicate ACKs.
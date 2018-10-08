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

Protocol: define format, order of messages sent and received among network entities, and action taken on message transmission, receipt

ISP: internet service provider

Network Edge: hosts running app

Network Core: packet switches(routers, link-layer switch), links

Access Networks: phycial links that connect an end system to the first router.
- broadband residential access: DSL (digital subscriber line), cabel modem (HFC, hybrid fiber coax)
    - DSL make use of telephone infra
    - HFC make use of cable television infra
- institutional access: LAN, e.g. Ethernet. PC->copper wire->Ethernet switch->Router->ISP
- mobile network: wide-area wireless access network, base station

Phycial Media: coax, fiber, radio

Home network: combine wifi and broadband residential access. <br>
wireless PC ->access pointer -> router -> cable modem -> Internet <br>
               stationary PC -> <br>

packet: chunks of data<br>
store-and-forward transmission: the switch must receive the entire packet before transmitting the first bit of the packet onto the link.<br>
output buffer: stores packets which the switch is about to send into the link associated with that output buffer.<br>

circuit switching
- reserved resources
- bandwidth division into pieces
- Pro: guatranteed constant rate due to the dedicated allocation of bandwidth
- Con: resource piece idle

packet switching
- unreserved resources
- full bandwidth
- Pro: resources used as need
- Con: queue delay in congested network

Http: stateless, in-band

FTP: state, out-band. Seperate control connection and data connection. Close data connection after transferring one file.

SMTP:



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
- Definition: TCP throttles a sending process when the network is congested between sender and receiver.
- How: The amount of unACKs is smaller than cwnd. TCP uses ACKs to clock its increasing in cwnd size. 
- Principle:

Congesition Stage
- slow start: increase cwnd by 1MSS every time a transmitted segment is first acknowledged. In other words, cwnd grow exponentially every RTT. Throughput = cwnd*MSS/R
- congenstion avoidance: increase cwnd by 1MSS every RTT.
- fast recovery: increase cwnd by 1MSS for each dup Acks.
- TCP Taheo: Unconditionally cut down cwnd to 1MSS when eithor timeout or dup ACKs occurs.
- TCP Reno: incorporate fast recovery. Skip slow start stage while receiving duplicate ACKs.
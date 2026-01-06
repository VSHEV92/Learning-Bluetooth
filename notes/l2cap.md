# LOGICAL LINK CONTROL AND ADAPTATION PROTOCOL

------

## INTRODUCTION

![L2CAP](images/l2cap/l2cap-components.png)

- **Protocol/channel multiplexing** - L2CAP supports multiplexing over individual Controllers and across multiple Controllers. An L2CAP channel shall operate over one Controller at a time.
- **Segmentation and reassembly** - With the frame relay service offered by the Resource Manager, the length of transport frames is controlled by the individual applications running over L2CAP. Many multiplexed applications are better served if L2CAP has control over the PDU length
- **Flow control per L2CAP channel** - Controllers provide error and flow control for data going over the air and HCI flow control exists for data going over an HCI transport
- **Fragmentation and Recombination** - Some Controllers may have limited transmission capabilities and may require fragment sizes different from those created by L2CAP segmentation. Therefore layers below L2CAP may further fragment and recombine L2CAP PDUs to create fragments which fit each layer’s capabilities

------

## TERMINOLOGY

- **L2CAP channel** - the logical connection between two endpoints in peer devices, characterized by their Channel Identifiers (CID)
- **Service Data Unit (SDU)** - a packet of data that L2CAP exchanges with the upper layer and transports transparently over an L2CAP channel
- **Segment** - part of an SDU, as resulting from the Segmentation procedure
- **Protocol Data Unit (PDU)** - protocol Data Unit: a packet of data containing L2CAP protocol information fields, control information, and/or upper layer information data
- **Basic L2CAP header** - minimum L2CAP protocol information that is present in the beginning of each PDU
- **Fragment** - a part of a PDU, as resulting from a fragmentation operation. Fragments are used only in the delivery of data to and from the lower layer
- **Maximum Transmission Unit (MTU)** - the maximum size of payload data, in octets, that the upper layer entity is capable of accepting, i.e. the MTU corresponds to the **maximum SDU size**
- **Maximum PDU payload Size (MPS)** - the maximum size of payload data in octets that the L2CAP layer entity is capable of accepting, i.e. the MPS corresponds to the **maximum PDU payload size**
- **Signaling MTU (MTUsig)** - the maximum size of command information that the L2CAP layer entity is capable of accepting. The MTUsig, refers to the signaling channel only and corresponds to the **maximum size of a C-frame**
- **Connectionless MTU (MTUcnl)** - the maximum size of the connection packet information that the L2CAP layer entity is capable of accepting. The MTUcnl refers to the connectionless channel only an  corresponds to the **maximum G-frame**
- **MaxTransmit** - in Enhanced Retransmission mode and Retransmission mode, MaxTransmit controls the number of transmissions of a PDU that L2CAP is allowed to try before assuming that the PDU is lost

------

## GENERAL OPERATION

#### CHANNEL IDENTIFIERS

A channel identifier (CID) is the local name representing a logical channel endpoint on the device. 

The null identifier (0x0000) shall never be used as a destination endpoint. Identifiers from 0x0001 to 0x003F are reserved for specific L2CAP functions. 

Implementations are free to manage the remaining CIDs in a manner best suited for that particular implementation, with the provision that two simultaneously active L2CAP channels shall not share the same CID.

#### MODES OF OPERATION

L2CAP channels may operate in one of five different **modes** as selected for each L2CAP channel.

- **Basic L2CAP Mode** - the default mode, which is used when no other mode is agreed
- **Flow Control mode** - PDUs exchanged with a peer entity are numbered and acknowledged. No retransmissions take place, but missing PDUs are detected and can be reported as lost
- Retransmission mode - PDUs exchanged with a peer entity are numbered and acknowledged. A timer is used to ensure that all PDUs are delivered to the peer, by retransmitting PDUs as needed
- **Enhanced Retransmission mode** - PDUs exchanged with a peer entity are numbered and acknowledged. Similar to Retransmission mode. It adds the ability to set a POLL bit to solicit a response from a remote L2CAP entity, the SREJ S-frame to improve the efficiency of error recovery and adds an RNR S-frame to replace the R-bit for reporting a local busy condition
- **Streaming mode** - real-time isochronous traffic. PDUs are numbered but are not acknowledged. A finite flush timeout is set on the sending side to flush packets that are not sent in a timely manner. On the receiving side if the receive buffers are full when a new PDU is received then a previously received PDU is overwritten by the newly received PDU. Missing PDUs can be detected and reported as lost
- **LE Credit Based Flow Control Mode** - for LE L2CAP connection oriented channels for flow control using a credit based scheme for L2CAP data. This is the only mode that shall be used for LE L2CAP connection oriented channels

------

## DATA PACKET FORMAT

L2CAP is packet-based but follows a communication model based on **channels**. A channel represents a data flow between L2CAP entities in remote
devices. 

Channels may be **connection-oriented** or **connectionless**. Fixed channels other than the L2CAP connectionless channel (CID 0x0002) and the two L2CAP signaling channels (CIDs 0x0001 and 0x0005) are considered connection-oriented.  All channels with dynamically assigned CIDs are connection-oriented.



#### B-frame

In basic L2CAP mode, the L2CAP PDU on a connection-oriented channel is also referred to as a "**B-frame**"

![L2CAP](images/l2cap/B-frame.png)



#### G-frame

L2CAP PDU format within a connectionless data channel is referred to as a "**G-frame**". 

![L2CAP](images/l2cap/G-frame.png)

**Protocol/Service Multiplexer (PSM)** - identifier of higher-level Bluetooth protocol (like RFCOMM or GATT) or application, allowing multiple services to share a single L2CAP channel, similar to port numbers in TCP/IP.



#### I/S-frame

To support **flow control**, **retransmissions**, and **streaming**, L2CAP PDU types with protocol elements in addition to the Basic L2CAP header are defined. 

The information frames (**I-frames**) are used for information transfer between L2CAP entities. 

The supervisory frames (**S-frames**) are used to acknowledge I-frames and request retransmission of I-frames.

![L2CAP](images/l2cap/IS-frame.png)

The **Control Field** identifies the type of frame. 

There are three different Control Field formats: 

- **Standard Control Field** - shall be used for Retransmission mode and Flow Control mode
- **Enhanced Control Field** - shall be used for Enhanced Retransmission mode and Streaming mode
- **Extended Control Field** - may be used for Enhanced Retransmission mode and Streaming mode

The Control Field will contains:

- Send Sequence Number (TxSeq)
- Receive Sequence Number (ReqSeq)
- Retransmission Disable Bit (R)
- Segmentation and Reassembly (SAR) - start, end, middle of SDU
- Supervisory function (S) - only in S-frame: RR (Receiver Ready), REJ (Reject), RNR (Receiver Not Ready) and SREJ (Selective Reject)
-  Poll (P)
- Final (F)

The L2CAP SDU **Length Field** shall specify the total number of octets in the SDU, when an SDU spans more than one I-frame. The first I-frame in the sequence shall be identified by SAR=”Start of L2CAP SDU”. 

The **Frame Check Sequence** (FCS) is constructed using the generator polynomial g(D) = D^16 + D^15 + D^2 + 1. The 16 bit LFSR is initially loaded with the value 0x0000. The FCS covers the Basic L2CAP header, Control, L2CAP-SDU length and Information payload fields



#### LE-frame

In LE Credit Based Flow Control Mode, the L2CAP PDU on a connection-oriented channel is an LE information frame (**LE-frame**)

![L2CAP](images/l2cap/LE-frame.png)

The first LE-frame of the SDU shall contain the L2CAP SDU **Length field** that shall specify the total number of octets in the SDU. All subsequent LE-frames that are part of the same SDU shall not contain the L2CAP SDU Length field.

------

## SIGNALING PACKET FORMATS

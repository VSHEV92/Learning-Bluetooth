# LOGICAL LINK CONTROL AND ADAPTATION PROTOCOL

------

By using NimBLE L2CAP you can transmit raw SDU data using LE Credit Base Mode.



------

## Header Files



### nimble/host/include/host/ble_l2cap.h

This header file provides the public API for interacting with the L2CAP layer of the BLE (Bluetooth Low Energy) stack. L2CAP is responsible for managing logical channels between two connected BLE devices.

1. **Defines**:

   - Fixed CID values (Vol 3, Part A, 2.1, Table 2.1)

   - Signaling Packets opcodes  (Vol 3, Part A, 4, Table 4.2)

   - Command Reject packet error codes (Vol 3, Part A, 4.1, Table 4.3)

   - Connection Response packet error codes (Vol 3, Part A, 4.3, Table 4.6)

   - Other L2CAP Stack Define

2. **Structures**:

   - **ble_l2cap_sig_update_params** - Represents the signaling update in L2CAP (connection interval, slave latency, timeout multiplier)

   - **ble_l2cap_event** - Represents a L2CAP-related event. Union of structures for each event (connect, disconnect, accept, receive, tx_unstalled, reconfigured)

   - **ble_l2cap_chan_info** - Represents information about an L2CAP channel. (scid, dcid, mtu, psm)

3. **API**:

   - update connection parameters (interval, slave latency and so on)

   - create server, that accept incoming connection

   - connect, disconnect

   - send SDU

   - get channel info and ready status



### nimble/host/src/ble_l2cap_priv.h

Contain data and prototypes of function that are for internal L2CAP layer use.

1. **Structures**:

   - **ble_l2cap_chan** - Represents L2CAP channel (scid, dcid, mtu, rx buffer, rx callback, COC - psm, init credits, callback)

   - **ble_l2cap_hdr** - L2CAP packet header (cid, len) (Vol 3, Part A, 3.1, Figure 3.1)

   - **L2CAP statistic** - channel create/delete, update, signal packets rx/tx, secure manager packets rx/tx

2. **API**:

   - parse/prepare header

   - channel allocate/free

   - RX/TX

   - init



### nimble/host/src/ble_l2cap_sig_priv.h

Contain data and prototypes of function related to signaling packets that are for internal L2CAP layer use.

1. **Defines**:
   - MTU - default value is 100

2. **Structures**:
   - Structures that define L2CAP signaling packets (Vol 3, Part A, 4)

3. **API**:

   - transmit signal packet

   - parse signal packet (parse header, get cmd)

   - reject packet (reason, invalid cid)

   - COC function - connect/disconnect



### nimble/host/src/ble_l2cap_coc_priv.h

Contain data and prototypes of function related to channel creation that are for internal L2CAP layer use.

1. **Structures**:
   - **ble_l2cap_coc_endpoint** - L2CAP endpoint data structure (sdus buffer, mtu, credits)

2. **API**:

   - create server

   - create/free channel

   - send

   - update credit



------

## Source Files



### nimble/host/src/ble_l2cap.c

L2CAP layer API functions definitions. Most function are very simple - just set some date structures fields or allocate/free buffers. Some function are wrappers around COC and SIG function.  Two most interesting functions are:

1. **ble_l2cap_rx** - Processes an incoming L2CAP fragment
   - If fragment is first fragment of PDU:
     - Parse header
     - Check that SCID is valid
     - Check that SPU size is less then MPS
     - Remember channel and packet size for later fragments processing
   - If fragment is middle fragment of PDU:
     - If middle packet without start packet before, then discard it
   - Call **ble_l2cap_rx_payload** function:
     - Concatenate all fragments in single buffer
     - When all fragments are received, call channel callback function 
2. **ble_l2cap_tx** -  Transmits the L2CAP payload contained in the specified mbuf
   - Prepare packet header
   - Send data using HCI api (**ble_hs_hci_acl_tx**)



### nimble/host/src/ble_l2cap_sig_cmd.c

Define some function used for signaling packets. 

- **ble_l2cap_sig_tx** - transmit data using **ble_l2cap_tx** function 
- **ble_l2cap_sig_hdr_parse** - parse header and get opcode, identifier and length
- **ble_l2cap_sig_cmd_get** - create singnaling packet using opcode, identifier, length and data buffer
- **ble_l2cap_sig_reject_tx** - send Command Reject packet. Create packet using **ble_l2cap_sig_cmd_get** and send it using **ble_l2cap_sig_tx**
- **ble_l2cap_sig_reject_invalid_cid_tx** - send Command Reject packet with Invalid CID reason using **ble_l2cap_sig_reject_tx**

 

### nimble/host/src/ble_l2cap_sig.c

Define function used for signaling channel (CID = 5)

- Functions for signal channel creation
- Functions for channel connection
- Functions for channel parameters update
- Functions for channel disconnection
- Functions for channel credits processing



### nimble/host/src/ble_l2cap_coc.c

Define function used for processing connection-oriented channels

- Channel and server allocation function
- Server creation function
- Credit based receive function
- Credit based transmit function

 

------

## Examples

### ESP IDF BLE Central L2CAP COC Example

This example is designed to demonstrate the process of BLE service discovery, connection establishment, and data transfer using the L2CAP layer's connection-oriented channels. It begins by conducting a passive scan to identify nearby peripheral devices. If a device is found that both advertises its connectability and supports connection-oriented channels, the example proceeds to establish a connection with that device.



### ESP IDF BLE Peripheral L2CAP COC Example

This example illustrates the process of advertising data, accepting connection from a central device, and enabling connection-oriented channels. It accomplishes two key tasks with the designated peer i.e. establishing L2CAP connection-oriented channels and receiving data via these established channels. The primary goal of the example is to demonstrate the functionality of BLE service discovery, connection establishment, and data transmission over the L2CAP layer using connection-oriented channels.

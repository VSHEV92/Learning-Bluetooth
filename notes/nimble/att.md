# ATTRIBUTE PROTOCOL

------

## Header Files



### nimble/host/include/host/ble_att.h

This header file provides the public API for interacting with the ATT layer of the BLE (Bluetooth Low Energy) stack. ATT layer contain attribute table and define attribute access PDUs.

1. **Defines**:

   - Attribute Protocol (ATT) UUIDs (primary, secondary, include, characteristic)

   - Attribute Protocol (ATT) Error Codes (Vol 3, Part F, 3.4, Table 3.3)

   - Attribute Protocol (ATT) Operation Codes (Vol 3, Part F, 3.4, Table 3.37)
   - Attribute Protocol (ATT) Flags that define attribute permissions (Vol 3, Part F, 3.2, 3.2.5)
   - Default (23) and Maximum (527) ATT MTU

2. **API**:

   - Function prototypes for read and write attribute using handler
   - Function prototypes for get and set ATT MTU



### nimble/host/src/ble_att_priv.h

Contain data and prototypes of function that are for internal ATT layer use.

1. **Structures**:
   - **ble_att_svr_entry** - structure that define attribute. Contain UUID, flags, handler and access callback 
   - Structures that define data for response ATT PDUs (find, read, group read)
2. **API**:
   - Function prototypes for l2cap channel creation and channel finding by connection
   - Function prototypes for ATT MTU managment
   - Function prototypes for start and stop ATT server
   - Function prototype for handler finding by UUID
   - Function prototypes for callbacks for receiving ATT PDUs by server
   - Function prototypes for transmitting ATT PDUs by client from GATT (requests)
   - Function prototypes for receiving ATT PDUs by client from L2CAP (responses)



### nimble/host/src/ble_att_cmd_priv.h

Contain data and prototypes of function for ATT PDUs creation and processing

1. **Structures**:
   - **ble_att_hdr** - attribute PDU header
   - Structure that define data payload for all types of PDUs
2. **API**:
   - Function prototypes for prepare and parse ATT PDUs
   - Function prototypes for ATT PDUs transmission



------

## Source Files



### nimble/host/src/ble_att_cmd.c

- **ble_att_init_parse** - get ATT PDU, check opcode and return pointer to ATT PDU payload
- **functions with _parse suffix** - use **ble_att_init_parse**  to get data from ATT PDU and return structure of data payload for given PDU type (used only in tests)
- **ble_att_init_write** - get data, opcode and length and initialize byte array that corresponding to ATT PDU   
- **functions with _write suffix** - use **ble_att_init_write**  to set ATT PDU and return structure of given PDU type  (used only in tests)
- **ble_att_cmd_prepare** - allocate memory for ATT PDU and set opcode field 
- **ble_att_cmd_get** - allocate memory for HCI and L2CAP header and then use **ble_att_cmd_prepare** to chain ATT PDUs buffer to that memory
- **ble_att_tx_with_conn** - get LL connection, L2CAP channel and data buffer. Perform some checks and send data to L2CAP using **ble_l2cap_tx** call
- **ble_att_tx** - get LL connection handler, L2CAP CID and data buffer. Find LL connection by handler, L2CAP by CID and then call **ble_att_tx_with_conn**



### nimble/host/src/ble_att.c

- **ble_att_rx_dispatch_entry** - list of callbacks for received ATT PDUs by client and server 
- **ble_att_stats** - ATT PDUs statistics
- Helper function for ATT MTU value setting and getting 
- **ble_att_rx_extended** - get LL connection, L2CAP CID and data buffer. Get opcode from buffer, find callback from **ble_att_rx_dispatch_entry** and call it 
- **ble_att_rx** - get L2CAP channel. Find LL connection and L2CAP CID. Then call **ble_att_rx_extended**. This is L2CAP channel RX callback. 
- **ble_att_create_chan** - get LL connection and create fixed L2CAP channel for ATT protocol  



### nimble/host/src/ble_att_clt.c

- Define function for processing client received ATT PDUs. This functions have **ble_att_clt_rx** prefix. Used as callbacks from **ble_att_rx_dispatch_entry** for ATT response PDUs processing. Call function from **GATT** API.
- Define function for processing client transmitted ATT PDUs. This functions have **ble_att_clt_tx** prefix. Used to create ATT PDU using **ble_att_cmd_get**, and then send this PDU using **ble_att_tx**



### nimble/host/src/ble_att_srv.c

- Define functions for Attributes managment:
  - **ble_att_svr_entry_alloc** - allocate memory for new attribute table entry
  - **ble_att_svr_register** - initialize attribute table entry. Set UUID, flags, handler, callback for attribute events
  - Helper function for attribute table entry finding by handler, UUID and so on.
  - Helper functions for permissions and security checks

- Define functions for access to attribute table:

  - **ble_att_svr_pkt** - allocate memory for ATT request packet

  - **ble_att_svr_read** - check permissions and call attribute table entry callback 
  - **ble_att_svr_read_flat** - allcate memory for L2CAP packet using **ble_hs_mbuf_l2cap_pkt**, Then call **ble_att_svr_pkt** for get attribute data. Then copy this data to L2CAP packet buffer
  - **ble_att_svr_read_handle** - find entry by handler and call **ble_att_svr_read**
  - **ble_att_svr_write** - check permissions and call attribute table entry callback 
  - **ble_att_svr_write_handle** - find entry by handler and call **ble_att_svr_write**

- Define functions for ATT PDUs processing: 

  - Function for each ATT request type. This functions have **ble_att_srv_rx** prefix. Used as callbacks from **ble_att_rx_dispatch_entry** for ATT request PDUs processing by server. Create response PDU by **ble_att_svr_pkt** and  **ble_att_cmd_prepare**. Read or write attribute entry by **ble_att_svr_read_handle** or **ble_att_svr_write_handle**. Call **ble_att_svr_tx_rsp** to transmit response PDU.
  - ble_att_svr_tx_rsp - transmit response PDU by **ble_att_tx** if there is no errors. Or send error response by **ble_att_svr_tx_error_rsp**

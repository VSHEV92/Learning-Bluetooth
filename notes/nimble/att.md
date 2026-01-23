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

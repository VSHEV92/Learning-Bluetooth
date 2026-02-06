# SECURITY MANAGER

------

## Header Files



### nimble/host/include/host/ble_sm.h

This header file provides the public API for interacting with the SM layer of the BLE (Bluetooth Low Energy) stack. 

1. **Defines**:

   - SM PDU Errors (Vol 3, Part H, 3.5.5, Table 3.7)

   - SM Key Distribution Field (Vol 3, Part H, 3.6.1, Figure 3.11)

   - SM IO Capabilities, OOB, Authreq (Vol 3, Part H, 3.5.1, Table 3.4, 3.5, 3.6)

2. **Structures**:

   - **ble_sm_sc_oob_data** - OOB data

   - **ble_sm_io** - SM Data From IO

3. **API**:

   - **ble_sm_sc_oob_generate_data** - function that initialize **ble_sm_sc_oob_data** data

   - ble_sm_inject_io - function to send **ble_sm_io** data

     

### nimble/host/src/ble_sm_priv.h

Contain data and prototypes of function that are for internal SM layer use.

1. **Defines**:
   - SM PDU Types (Vol 3, Part H, 3.3, Table 3.3)
2. **Structures**:
   - Data structure for each SM PDU data type
   - **ble_sm_keys** - data structure, that contain keys
   - **ble_sm_proc** - data structure, that contain all needed data for SM procedures (connection handler, TK, other keys, random numbers, callbacks)
   - **ble_sm_result** - data structure, that contain result information about SM connection
3. **API**:
   - Function prototypes for each function from Cryptographic Toolbox (Vol 3, Part H, 2.2) 
   - Function prototypes for key and random numbers creation
   - Function prototypes for all SM actions - send PDU, receive PDU, confirm data and so on
   - **ble_sm_tx()** - transmit SM PDU





------

## Source Files



### nimble/host/src/ble_sm_alg.c

- Define helper functions, used in cryptographic algorithms. For example XOR, AES encrypt and so on
- Define cryptographic toolbox function
- Define function for keys generation DHKey, public and private keys, 



### nimble/host/src/ble_sm_cmd.c

- **ble_sm_cmd_get()** - create SM PDU from opcode, length and data
- **ble_sm_tx()** - send SM command through L2CAP using ble_l2cap_tx()



### nimble/host/src/ble_sm_lgcy.c

Contain helper function for Legacy Pairing 

- Define structures used in actions with response to IO Capabilities information
- **ble_sm_lgcy_io_action()** - function that select authentication algorithm (Vol 3, Part H, 2.3.5, Table 2.8) 
- **ble_sm_lgcy_confirm_exec()** - confirm values using **c1** function in legacy pairing (Vol 3, Part H, 2.3.5.5) 
- **ble_sm_gen_stk()** - generate STK
- Function for send and receive random values



### nimble/host/src/ble_sm_sc.c

Contain helper function for Secure Connection 

- Define structures used in actions with response to IO Capabilities information
- **ble_sm_sc_io_action()** - function that select authentication algorithm (Vol 3, Part H, 2.3.5, Table 2.8) 
- Define function for public/private keys generation and processing, values confirmation, random data generation and processing, DHKey processing, OOB data processing



### nimble/host/src/ble_sm.c

Contain functions for SM Procedures

- Define function for processing SM Commands. This functions have **ble_sm_<>_rx** prefix. Used as callbacks from **ble_sm_dispatch**.
- Define function for create and send SM Commands. This functions have **ble_sm_<>_exec** prefix. Used as callbacks from **ble_sm_state_dispatch**.
- Define helper functions for debug purpose (**ble_sm_dbg_set_next** prefix)
- Define  helper functions to generate random values, EDIV, LTK, ID rand, CRSK
- **ble_sm_start_encrypt_tx()** - send command to HCI to start encoding
- Define helper functions LTK processing, values confirm, pairing and encoding initialization and processing

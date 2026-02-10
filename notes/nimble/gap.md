# GENERIC ACCESS PROFILE

------

## Usage



------

## Header Files



### nimble/host/include/host/ble_gap.h

This header file provides the public API for interacting with the GAP layer of the BLE (Bluetooth Low Energy) stack. 

1. **Defines**:
   - Contain Macro for advertising and scan intervals, connection interval, scan window,  supervisor timeout 

   - Define 38 GAP Events - connect, notify, subscribe, pairing and so on

   - Other defines, like subscribe reason, repeat pairing and so on 
   
   - Defines for connection and discovery modes
   
2. **Structures**:
   - **ble_gap_sec_state** -  Connection security state (encrypted, bonded, key size and so on)
   - **ble_gap_adv_params** - Advertising parameters (connection mode, discovery mode, advertising interval, channel map, filter policy).
   - **ble_gap_conn_desc** - Connection descriptor (local and peer address, connection handler, connection interval and supervisor timeout, role).
   - **ble_gap_conn_params** - Connection parameters (connection interval and supervisor timeout, latency, scan interval and window).
   - **ble_gap_disc_params** , **ble_gap_ext_disc_params** - interval, window, passive/active, limited, policy.
   - **ble_gap_upd_params** -  Connection parameters update parameters (almost same as ble_gap_conn_params)
   - **ble_gap_passkey_params** - action, compare number
   - **ble_gap_disc_desc**, **ble_gap_ext_disc_desc** - advertising report (packet type, data length, data, address, RSSI, TX power)
   - **ble_gap_event** - Represents a GAP-related event.  When such an event occurs, the hostnotifies the application by passing an instance of this structure to anapplication-specified callback. Contain event type and its info (connect, link_estab, disconnect, adv_complete, notify_rx, notify_tx, subscribe, periodic events, scan request receive, pairing complete and so on).
   - **ble_gap_multi_conn_params** - multi connection parameters
3. **API**:
   - Function prototypes for finding connection using address and handler
   - **ble_gap_adv_start**, **ble_gap_adv_stop**  - This function configures and start/stop advertising procedure.
   - **ble_gap_adv_set_data**, **ble_gap_adv_rsp_set_data** - function to set advertising and scan response data 
   - Functions for setup **extended**/**periodic** advertising (configure, start, stop)
   - Functions for Constant Tone Extension (CTE)
   - **ble_gap_disc**, **ble_gap_ext_disc** - functions to start discovery procedure 
   - **ble_gap_connect**, **ble_gap_ext_connect** - initiate connection procedure
   - Functions for multi connection procedure
   - Function for LE Suggested Default Data Length processing
   - **ble_gap_pair_initiate**, **ble_gap_security_initiate** - functions for security and pairing initiation
   - **ble_gap_event_listener_register** - Registers listener for GAP events
   - Functions for power control
   - Function for design test mode processing (DTM)



### nimble/host/src/ble_gap_priv.h

This header file provides data structures and API for internal usage.

1. **Structures**:

   - **ble_gap_conn_complete** - data structure about completed connection

2. **API**:

   - Contain prototypes for GAP events callbacks

   

------

## Source Files

### nimble/host/src/ble_gap.c

This file contain API used to interact with GAP



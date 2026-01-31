# GENERIC ATTRIBUTE PROFILE

------

## Header Files



### nimble/host/include/host/ble_gatt.h

This header file provides the public API for interacting with the GATT layer of the BLE (Bluetooth Low Energy) stack. 

1. **Defines**:
   - GATT Service UUID (0x1801) and  CCCD UUID (0x2902)

   - GATT Characteristic Properties (Vol 3, Part G, 3.4, Table 3.5)

   - GATT Characteristic Format Types, Units, Name Space (From Assigned Numbers)
2. **Structures**:
   - Data structures for GATT Notify and Error SDU
   - Data structures for GATT Service (**ble_gatt_svc**), Characteristic (**ble_gatt_chr**), Attribute (**ble_gatt_att**), Descriptor (**ble_gatt_dsc**)
   - Data structures for GATT Definitions Service (**ble_gatt_svc_def**), Characteristic (**ble_gatt_chr_def**), Descriptor (**ble_gatt_dsc_def**), Value Format (**ble_gatt_cpfd**)
   - **ble_gatt_access_ctxt** - context information about access to characteristic, pass as argument to callback functions
   - **ble_gatt_register_ctxt** - context information about characteristic, that need to be registered within GATT
3. **API**:
   - Function prototypes for GATT Procedures and there callbacks (Vol 3, Part G, 4.13, Table 4.2) 
   - Function prototypes for helper functions, used to register service, find service or characteristic and so on
   - **ble_gatts_start** -  Makes all registered services available to peers. Called be NimBLE at startup



------

## Source Files



### nimble/host/src/ble_gatts_lcl.c

This file contain API used to print information about local GATT Server. Information includes: services, include services,  characteristics and declarators.



### nimble/host/src/ble_gatts.c

This file contain API used by server to interact with GATT. This function is used by NimBLE internally.

- Functions to register services, characteristics and declarators
- Functions for access to services, characteristics and declarators, used as callbacks by ATT server
- Functions for process CCCD



### nimble/host/src/ble_gattc.c

This file contain API used by client to interact with GATT

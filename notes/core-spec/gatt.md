# GENERIC ATTRIBUTE PROFILE

------

## INTRODUCTION

The **Generic Attribute Profile (GATT)** defines a service framework using the Attribute Protocol. This framework defines procedures and formats of services and their characteristics. The procedures defined include discovering, reading, writing, notifying and indicating characteristics, as well as configuring the broadcast of characteristics.

This profile can be used over any physical link, using the Attribute Protocol L2CAP channel, known as the **ATT Bearer**.

**Client**—This is the device that initiates commands and requests towards the server and can receive responses, indications and notifications sent by the server.

**Server**—This is the device that accepts incoming commands and requests from the client and sends responses, indications and notifications to a client.

The following scenarios are covered by GATT profile:

- Exchanging configuration

- Discovery of services and characteristics on a device
- Reading a characteristic value
- Writing a characteristic value
- Notification of a characteristic value
- Indication of a characteristic value

------

## ATT PROTOCOL OVERVIEW

The GATT Profile uses the Attribute Protocol to transport data in the form of commands, requests, responses, indications, notifications and confirmations between devices. This data is contained in Attribute Protocol PDUs.

![GATT](images/gatt/att-pdu.png)

- The **Opcode** contains the specific command, request, response, indication, notification or confirmation opcode and a flag for authentication. 
- The **Attribute Parameters** contain data for the specific command or request or the data
  returned in a response, indication or notification. 
- The **Authentication Signature** is optional.

Attribute Protocol commands and requests act on values stored in Attributes on the server device. An Attribute is composed of four parts: Attribute Handle, Attribute Type, Attribute Value, and Attribute Permissions.

![GATT](images/gatt/att-attribute.png)

- The **Attribute Handle** is an index corresponding to a specific Attribute. 
- The **Attribute Type** is a UUID that describes the Attribute Value. 
- The **Attribute Value** is the data described by the Attribute Type and indexed by the Attribute Handle.
- The **Attribute Permissions** is part of the Attribute that cannot be read from or written to using the Attribute Protocol. It is used by the server to determine whether read or write access is permitted for a given attribute.

**Attribute caching** is an optimization that allows the client to discover the Attribute information such as Attribute Handles used by the server once and use the same Attribute information across reconnections without rediscovery. The Attribute information that shall be cached by a client is the Attribute Handles of all server attributes and the GATT service characteristics values. Attribute Handles used by the server should not change over time. This means that once an Attribute Handle is discovered by a client the Attribute Handle for that Attribute should not be changed.

If GATT based services on the server cannot be changed during the usable lifetime of the device, the **Service Changed characteristic** shall not exist on the server and the client does not need to ever perform service discovery after the initial service discovery for that server.

Generic attribute profile defines **grouping** of attributes for three attribute types:

- «Primary Service»
- «Secondary Service» 
- «Characteristic»

------

## GATT PROFILE HIERARCHY

The top level of the hierarchy is a **profile**. A profile is composed of one or more **services** necessary to fulfill a use case. A service is composed of **characteristics** or references to other services. Each characteristic contains a **value** and may contain optional information about the value.

![GATT](images/gatt/gatt-hierarchy.png)

#### Service

A service is a collection of data and associated behaviors to accomplish a particular function or feature. In GATT, a service is defined by its service definition. A service definition may contain referenced services, mandatory characteristics and optional characteristics. There are two types of services: primary service and secondary service.

A **primary service** is a service that exposes the primary usable functionality of this device. A primary service can be included by another service. Primary services can be discovered using Primary Service Discovery procedures.

A **secondary service** is a service that is only intended to be referenced from a primary service or another secondary service or other higher layer specification. A secondary service is only relevant in the context of the entity that references it.

#### Included Services

An included service is a method to **reference** another service definition existing on the server into the service being defined. To include another service, an include definition is used at the beginning of the service definition. When a service definition uses an include definition to reference the included service, the entire included service definition becomes part of the new service definition. This includes all the included services and characteristics of the included service.

#### Characteristic

A **characteristic** is a value used in a service along with properties and configuration information about how the value is accessed and information
about how the value is displayed or represented. In GATT, a characteristic is defined by its characteristic definition. A characteristic definition contains a characteristic declaration, characteristic properties, and a value and may contain descriptors that describe the value or permit configuration of the server with respect to the characteristic.

------

## SERVICE INTEROPERABILITY REQUIREMENTS



### SERVICE DEFINITION

A **service definition** shall contain a service declaration and may contain include definitions and characteristic definitions. All include definitions and characteristic definitions contained within the service definition are considered to be part of the service. All include definitions shall immediately follow the service declaration and precede any characteristic definitions. All characteristic definitions shall be immediately following the last include definition or in the event of no include definitions, immediately following the service declaration.

A **service declaration** is an Attribute with the Attribute Type set to the UUID for «Primary Service» (0x2800 UUID) or «Secondary Service»  (0x2801 UUID). The Attribute Value shall be the 16-bit Bluetooth UUID or 128-bit UUID for the service, known as the **service UUID**. The Attribute Permissions shall be read-only and shall not require authentication or authorization.



### INCLUDE DEFINITION

An **include definition** shall contain only one include declaration.

The **include declaration** is an Attribute with the Attribute Type set to the UUID for «Include» (0x2802 UUID). The Attribute Value shall be set to the included service Attribute Handle, the End Group Handle, and the service UUID. The Service UUID shall only be present when the UUID is a 16-bit Bluetooth UUID. The Attribute Permissions shall be read only and not require authentication or authorization.



### CHARACTERISTIC DEFINITION

A **characteristic definition** shall contain a characteristic declaration, a Characteristic Value declaration and may contain characteristic descriptor declarations.

Each declaration above is contained in a separate Attribute. The two required declarations are the **characteristic declaration** and the **Characteristic Value declaration**. The Characteristic Value declaration shall exist immediately following the characteristic declaration. Any optional characteristic descriptor declarations are placed after the Characteristic Value declaration.



#### Characteristic Declaration

A **characteristic declaration** is an Attribute with the Attribute Type set to the UUID for «Characteristic» (0x2803) and Attribute Value set to the Characteristic Properties, Characteristic Value Attribute Handle and Characteristic UUID. The Attribute Permissions shall be readable and not require authentication or authorization.

![GATT](images/gatt/characteristic-declaration.png)

- **Characteristic Properties** - bit field determines how the Characteristic Value can be used, or how the characteristic descriptors can be accessed. Use example is broadcast, read, write , notify, indicate and so on. Look at Vol 3, Part G, 3.3.1.1, Table 3.5
- **Characteristic Value Attribute Handle** - field is the Attribute Handle of the Attribute that contains the Characteristic Value
- **Characteristic UUID** - field is a 16-bit Bluetooth UUID or 128-bit UUID that describes the type of Characteristic Value



#### Characteristic Value Declaration

The **Characteristic Value declaration** contains the value of the characteristic. It is the first Attribute after the characteristic declaration. All characteristic definitions shall have a Characteristic Value declaration.

A Characteristic Value declaration is an Attribute with the **Attribute Typ**e set to the 16-bit Bluetooth or 128-bit UUID for the Characteristic Value used in the characteristic declaration. The **Attribute Value** is set to the Characteristic Value. The **Attribute Permissions** are specified by the service or may be implementation specific if not specified otherwise.



#### Characteristic Descriptor Declarations

**Characteristic descriptors** are used to contain related information about the Characteristic Value. Each characteristic descriptor is identified by the characteristic descriptor UUID. Characteristic descriptors if present within a characteristic definition shall follow the Characteristic Value declaration.

The GATT profile defines a standard set of characteristic descriptors that can be used by higher layer profiles. Higher layer profiles may define additional characteristic descriptors that are profile specific. 

- **Characteristic Extended Properties** - descriptor that defines additional Characteristic Properties. The Characteristic Extended Properties bit field describes additional properties on how the Characteristic Value can be used, or how the characteristic descriptors can be accessed. Look at Vol 3, Part G, 3.3.1.1, Table 3.8
- **Characteristic User Description** - defines a UTF-8 string of variable size that is a user textual description of the Characteristic Value.
- **Client Characteristic Configuration** - defines how the characteristic may be configured by a specific client. The characteristic descriptor value is a bit field. When a bit is set, that action (notify, indicate) shall be enabled, otherwise it will not be used. A client may write this configuration descriptor to control the configuration of this characteristic on the server for the client. Each client has **its own instantiation** of the Client Characteristic Configuration. Reads of the Client Characteristic Configuration only shows the configuration for that client and writes only affect the configuration of that client.
- **Server Characteristic Configuration** - defines how the characteristic may be configured for the server. The characteristic descriptor value is a bit field. When a bit is set, that action (broadcast) shall be enabled, otherwise it will not be used. A client may write this configuration descriptor to control the configuration of this characteristic on the server for all clients. There is a single instantiation of the Server Characteristic Configuration for all clients. Reads of the Server Characteristic Configuration shows the configuration all clients and writes affect the configuration for all clients.
- **Characteristic Presentation Format** - defines the format of the Characteristic Value. If more than one Characteristic Presentation Format declarations exist, in a characteristic definition, then a Characteristic Aggregate Format declaration shall exist as part of the characteristic definition. The characteristic format value is composed of five parts: 
  - format  - uint8, int32, float and so on
  - exponent - multiply characteristic value by 10 powered exponent
  - unit - UUID of physical units (meters, seconds, volts and so on)
  - name space - used to identify the organization (Bluetooth SIG is only no)
  - description - enumerated value as defined in the Assigned Numbers document (left, right, bottom, top, inside, outside and so on)
- **Characteristic Aggregate Format** - defines the format of an aggregated Characteristic Value. The Characteristic Aggregate Format value is composed of a list of Attribute Handles of Characteristic Presentation Format declarations, where each Attribute Handle points to a Characteristic Presentation Format declaration.



### SUMMARY OF GATT PROFILE ATTRIBUTE TYPES

![GATT](images/gatt/gatt-attribute-types.png)

------



## GATT FEATURE REQUIREMENTS

There are 11 features defined in the GATT Profile:
1. Server Configuration
2. Primary Service Discovery
3. Relationship Discovery
4. Characteristic Discovery
5. Characteristic Descriptor Discovery
6. Reading a Characteristic Value
7. Writing a Characteristic Value
8. Notification of a Characteristic Value
9. Indication of a Characteristic Value
10. Reading a Characteristic Descriptor
11. Writing a Characteristic Descriptor



### SERVER CONFIGURATION

This procedure is used by the client to configure the Attribute Protocol.

- **Exchange MTU** - This sub-procedure is used by the client to set the ATT_MTU to the maximum possible value that can be supported by both devices when the client supports a value greater than the default ATT_MTU for the Attribute Protocol. The Attribute Protocol Exchange MTU Request is used by this sub-procedure. The Client Rx MTU parameter shall be set to the maximum MTU that this client can receive.



### PRIMARY SERVICE DISCOVERY

This procedure is used by a client to discover primary services on a server.

- **Discover All Primary Services** - This sub-procedure is used by a client to discover all the primary services on a server. The Attribute Protocol Read By Group Type Request shall be used with the Attribute Type parameter set to the UUID for «Primary Service». The Starting Handle shall be set to 0x0001 and the Ending Handle shall be set to 0xFFFF.
- **Discover Primary Service by Service UUID** - This sub-procedure is used by a client to discover a specific primary service on a server when only the Service UUID is known. The primary service being discovered is identified by the service UUID. The Attribute Protocol Find By Type Value Request shall be used with the Attribute Type parameter set to the UUID for «Primary Service» and the Attribute Value set to the 16-bit Bluetooth UUID or 128-bit UUID for the specific primary service. The Starting Handle shall be set to 0x0001 and the Ending Handle shall be set to 0xFFFF.



### RELATIONSHIP DISCOVERY 

This procedure is used by a client to discover service relationships to other services.

- **Find Included Services** - This sub-procedure is used by a client to find include service declarations within a service definition on a server. The service specified is identified by the service handle range. The Attribute Protocol Read By Type Request shall be used with the Attribute Type parameter set to the UUID for «Include» The Starting Handle shall be set to starting handle of the specified service and the Ending Handle shall be set to the ending handle of the specified service.



### CHARACTERISTIC DISCOVERY

This procedure is used by a client to discover service characteristics on a server. Once the characteristics are discovered additional information about the characteristics can be discovered or accessed using other procedures.

- **Discover All Characteristics of a Service** - This sub-procedure is used by a client to find all the characteristic declarations within a service definition on a server when only the service handle range is known. The Attribute Protocol Read By Type Request shall be used with the Attribute Type parameter set to the UUID for «Characteristic» The Starting Handle shall be set to starting handle of the specified service and the Ending Handle shall be set to the ending handle of the specified service.
- **Discover Characteristics by UUID** - This sub-procedure is used by a client to discover service characteristics on a server when only the service handle ranges are known and the characteristic UUID is known. The characteristic being discovered is identified by the characteristic UUID. The Attribute Protocol Read By Type Request is used to perform the beginning of the sub-procedure. The Attribute Type is set to the UUID for «Characteristic» and the Starting Handle and Ending Handle parameters shall be set to the service handle range.



### CHARACTERISTIC DESCRIPTOR DISCOVERY

This procedure is used by a client to discover characteristic descriptors of a characteristic. Once the characteristic descriptors are discovered additional information about the characteristic descriptors can be accessed using other procedures.

- **Discover All Characteristic Descriptors** - This sub-procedure is used by a client to find all the characteristic descriptor’s Attribute Handles and Attribute Types within a characteristic definition when only the characteristic handle range is known. The characteristic specified is identified by the characteristic handle range. The Attribute Protocol Find Information Request shall be used with the Starting Handle set to the handle of the specified characteristic value + 1 and the Ending Handle set to the ending handle of the specified characteristic.



### CHARACTERISTIC VALUE READ

This procedure is used to read a Characteristic Value from a server.

- **Read Characteristic Value** - This sub-procedure is used to read a Characteristic Value from a server when the client knows the Characteristic Value Handle. The Attribute Protocol Read Request is used with the Attribute Handle parameter set to the Characteristic Value Handle. The Read Response returns the Characteristic Value in the Attribute Value parameter.
- **Read Using Characteristic UUID** - This sub-procedure is used to read a Characteristic Value from a server when the client only knows the characteristic UUID and does not know the handle of the characteristic. The Attribute Protocol Read By Type Request is used to perform the sub- procedure. The Attribute Type is set to the known characteristic UUID and the Starting Handle and Ending Handle parameters shall be set to the range over which this read is to be performed. This is typically the handle range for the service in which the characteristic belongs.
- **Read Long Characteristic Values** - This sub-procedure is used to read a Characteristic Value from a server when the client knows the Characteristic Value Handle and the length of the Characteristic Value is longer than can be sent in a single Read Response Attribute Protocol message. The Attribute Protocol Read Blob Request is used to perform this sub-procedure. The Attribute Handle shall be set to the Characteristic Value Handle of the Characteristic Value to be read. The Value Offset parameter shall be the offset within the Characteristic Value to be read.
- **Read Multiple Characteristic Values** - This sub-procedure is used to read multiple Characteristic Values from a server when the client knows the Characteristic Value Handles. The Attribute Protocol Read Multiple Requests is used with the Set Of Handles parameter set to the Characteristic Value Handles. The Read Multiple Response returns the Characteristic Values in the Set Of Values parameter.



### CHARACTERISTIC VALUE WRITE

This procedure is used to write a Characteristic Value to a server.

- **Write Without Response** - This sub-procedure is used to write a Characteristic Value to a server when the client knows the Characteristic Value Handle and the client does not need an acknowledgment that the write was successfully performed. The Attribute Protocol Write Command is used for this sub-procedure. The Attribute Handle parameter shall be set to the Characteristic Value Handle. The Attribute Value parameter shall be set to the new Characteristic Value.
- **Signed Write Without Response** - This sub-procedure is used to write a Characteristic Value to a server when the client knows the Characteristic Value Handle and the ATT Bearer is not encrypted. This sub-procedure shall only be used if the Characteristic Properties authenticated bit is enabled and the client and server device share a bond. The Attribute Protocol Signed Write Command is used for this sub-procedure.
- **Write Characteristic Value** - This sub-procedure is used to write a Characteristic Value to a server when the client knows the Characteristic Value Handle. The Attribute Protocol Write Request is used to for this sub-procedure. The Attribute Handle parameter shall be set to the Characteristic Value Handle. The Attribute Value parameter shall be set to the new characteristic. A Write Response shall be sent by the server if the write of the Characteristic Value succeeded.
- **Write Long Characteristic Values** - This sub-procedure is used to write a Characteristic Value to a server when the client knows the Characteristic Value Handle but the length of the Characteristic Value is longer than can be sent in a single Write Request Attribute Protocol message. The Attribute Protocol Prepare Write Request and Execute Write Request are used to perform this sub-procedure.
- **Reliable Writes** - This sub-procedure is used to write a Characteristic Value to a server when the client knows the Characteristic Value Handle, and assurance is required that the correct Characteristic Value is going to be written by transferring the Characteristic Value to be written in both directions before the write is performed. The sub-procedure has two phases; the first phase prepares the Characteristic Values to be written. To do this, the client transfers the Characteristic Values to the server. The server checks the validity of the Characteristic Values. The client also checks each Characteristic Value to verify it was correctly received by the server using the server responses. Once this is complete, the second phase performs the execution of all of the prepared Characteristic Value writes on the server from this client.



### CHARACTERISTIC VALUE NOTIFICATION

This procedure is used to notify a client of the value of a Characteristic Value from a server. Notifications can be configured using the Client Characteristic Configuration descriptor.

- **Notifications** - This sub-procedure is used when a server is configured to notify a Characteristic Value to a client without expecting any Attribute Protocol layer acknowledgment that the notification was successfully received.



### CHARACTERISTIC VALUE INDICATIONS

This procedure is used to indicate the Characteristic Value from a server to a client. Indications can be configured using the Client Characteristic Configuration descriptor. A profile defines when to use Indications.

- **Indications** - This sub-procedure is used when a server is configured to indicate a Characteristic Value to a client and expects an Attribute Protocol layer acknowledgment that the indication was successfully received.



### CHARACTERISTIC DESCRIPTORS

This procedure is used to read and write characteristic descriptors on a server.

- **Read Characteristic Descriptors** - This sub-procedure is used to read a characteristic descriptor from a server when the client knows the characteristic descriptor declaration’s Attribute handle. The Attribute Protocol Read Request is used for this sub-procedure. The Read Request is used with the Attribute Handle parameter set to the characteristic descriptor handle. The Read Response returns the characteristic descriptor value in the Attribute Value parameter.
- **Read Long Characteristic Descriptors** - This sub-procedure is used to read a characteristic descriptor from a server when the client knows the characteristic descriptor declaration’s Attribute handle and the length of the characteristic descriptor declaration is longer than can be sent in a single Read Response Attribute Protocol message. The Attribute Protocol Read Blob Request is used to perform this sub-procedure.
- **Write Characteristic Descriptors** - This sub-procedure is used to write a characteristic descriptor value to a server when the client knows the characteristic descriptor handle. The Attribute Protocol Write Request is used for this sub-procedure. The Attribute Handle parameter shall be set to the characteristic descriptor handle. The Attribute Value parameter shall be set to the new characteristic descriptor value. A Write Response shall be sent by the server if the write of the characteristic descriptor value succeeded.
- **Write Long Characteristic Descriptors** - This sub-procedure is used to write a characteristic descriptor value to a server when the client knows the characteristic descriptor handle but the length of the characteristic descriptor value is longer than can be sent in a single Write Request Attribute Protocol message. The Attribute Protocol Prepare Write Request and Execute Write Request are used to perform this sub-procedure.



------

## L2CAP INTEROPERABILITY REQUIREMENTS

Over LE, the ATT Bearer is the Attribute L2CAP fixed channel. L2CAP fixed CID 0x0004 shall be used for the Attribute Protocol.

Both a GATT client and server implementations shall support an ATT_MTU not less than the default value (23).

The retransmission and flow control mode for this channel shall be Basic L2CAP mode.  The default parameters for the payload of the L2CAP B-frame shall be a single Attribute PDU.



------

## GENERIC ATTRIBUTE PROFILE SERVICE

All characteristics defined within this section shall be contained in a primary service with the service UUID set to «GATT Service».

- **Service Changed (0x2A05 UUID)** - is a control-point attribute that shall be used to indicate to connected devices that services have changed (i.e., added, removed or modified). This Characteristic Value shall be configured to be indicated, using the Client Characteristic Configuration descriptor by a client.

The **Service Changed Characteristic Value** is two 16-bit Attribute Handles concatenated together indicating the beginning and ending Attribute Handles affected by an addition, removal, or modification to a GATT-based service on the server.

If the list of GATT based services and the service definitions cannot change for the lifetime of the device then this characteristic **shall not exist**, otherwise this characteristic **shall exist**.



------

## SECURITY CONSIDERATIONS

The list of services and characteristics that a device supports is not considered private or confidential information, and therefore the Service and Characteristic Discovery procedures shall always be permitted.

If ATT a request is issued when the physical link is unauthenticated or unencrypted, the server shall send an Error Response. The client wanting to read or write this characteristic can then request that the physical link be authenticated using the GAP authentication procedure, and once this has been completed, send the request again.

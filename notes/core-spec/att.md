# ATTRIBUTE PROTOCOL

------

## INTRODUCTION

The attribute protocol allows a device referred to as the **server** to expose a set of **attributes** and their associated values to a peer device referred to as the **client**. These attributes exposed by the server can be discovered, read, and written by a client, and can be indicated and notified by the server.

An **attribute** is a discrete value that has the following three properties associated with it:

- attribute type, defined by a **UUID** - specifies what the attribute represents
- attribute handle - uniquely identifies an attribute on a server, allowing a client to reference the attribute in read or write requests
- a set of permissions - may be applied to an attribute to prevent applications from obtaining or altering an attribute’s value

There shall be **only one** instance of a server on each Bluetooth device; this implies that the attribute handles shall be identical for all supported bearers.

The attribute protocol has **notification** and **indication** capabilities that provide an efficient way of sending attribute values to a client without the need for them to be read.

------

## BASIC CONCEPTS

#### Attribute Type

A universally unique identifier (UUID) is used to identify every attribute type. A UUID is considered unique over all space and time. A UUID can be independently created by anybody and distributed or published as required.

#### Attribute Handle

An attribute handle is a 16-bit value that is assigned by each server to its own attributes to allow a client to reference those attributes. Attribute handles on any given server shall have unique, non-zero values. Attributes are ordered by attribute handle.

**Grouping** is defined by a specific attribute placed at the beginning of a range of other attributes that are grouped with that attribute, as defined by a higher layer specification. Clients can request the first and last handles associated with a group of attributes.

#### Attribute Value

An attribute value is an octet array that may be either fixed or variable length. For example, it can be a one octet value, or a four octet integer, or a variable length string. An attribute may contain a value that is too large to transmit in a single PDU and can be sent using multiple PDUs.

The maximum length of an attribute value shall be 512 octets.

#### Attribute Permissions

An attribute has a set of permission values associated with it. The permissions associated with an attribute specifies that it may be read and/or written. The permissions associated with the attribute specifies the security level required for read and/or write access, as well as notification and/or indication. Attribute permissions are a combination of access permissions, encryption permissions, authentication permissions and authorization permissions.

------

## ATTRIBUTE PDU

Attribute PDUs have one of six types:

- **Commands**—PDUs sent to a server by a client that do not invoke a response.
- **Requests**—PDUs sent to a server by a client that invoke a response.
- **Responses**—PDUs sent to a client by a server in response to a request.
- **Notifications**—Unsolicited PDUs sent to a client by a server that do not invoke a confirmation.
- **Indications**—Unsolicited PDUs sent to a client by a server that invoke a confirmation.
- **Confirmations**—PDUs sent to a server by a client to confirm receipt of an indication.

Attribute PDUs has the following **format**:

- **Attribute Opcode** - The attribute PDU operation code (Authentication Signature Flag, Command Flag, Method). The Method is a 6-bit value that determines the format and meaning of the Attribute Parameters. If the Command Flag of the Attribute Opcode is set to one, the PDU shall be considered to be a Command.
- **Attribute Parameters** - The attribute PDU parameters
- **Authentication Signature** - Optional authentication signature for the Attribute Opcode and Attribute Parameters

An attribute protocol request and response or indication-confirmation pair is considered a single **transaction**. A transaction shall always be performed on one **ATT Bearer**. A transaction not completed within **30 seconds** shall **time out**. Such a transaction shall be considered to have failed and the local higher layers shall be informed of this failure.

------

## ATTRIBUTE PROTOCOL PDUs

- **Error Response** - used to state that a given request cannot be performed, and to provide the reason
- **Exchange MTU Request** - used by the client to inform the server of the client’s maximum receive MTU size and request the server to respond with its maximum receive MTU size
- **Exchange MTU Response** - sent in reply to a received Exchange MTU Request
- **Find Information Request** - used to obtain the mapping of attribute handles with their associated types. This allows a client to discover the list of attributes and their types on a server. Only attributes with attribute handles between and including the Starting Handle parameter and the Ending Handle parameter will be returned. To read all attributes, the Starting Handle parameter shall be set to 0x0001, and the Ending Handle parameter shall be set to 0xFFFF
- **Find Information Response** - sent in reply to a received Find Information Request and contains information about this server. The Find Information Response shall have complete handle-UUID pairs. Such pairs shall not be split across response packets; this also implies that a handle- UUID pair shall fit into a single response packet
- **Find By Type Value Request** - used to obtain the handles of attributes that have a 16-bit UUID attribute type and attribute value. Only attributes with attribute handles between and including the Starting Handle parameter and the Ending Handle parameter that match the requested attribute type and the attribute value that have sufficient permissions to allow reading will be returned. Attribute values will be compared in terms of length and binary representation
- **Find By Type Value Response** - sent in reply to a received Find By Type Value Request and contains information about this server. For each handle that matches the attribute type and attribute value in the Find By Type Value Request a Handles Information shall be returned. The Found Attribute Handle shall be set to the handle of the attribute that has the exact attribute type and attribute value from the Find By Type Value Request
- **Read By Type Request** - used to obtain the values of attributes where the attribute type is known but the handle is not known. Only the attributes with attribute handles between and including the Starting Handle and the Ending Handle with the attribute type that is the same as the Attribute Type given will be returned. To search through all attributes, the starting handle shall be set to 0x0001 and the ending handle shall be set to 0xFFFF
- **Read By Type Response** - sent in reply to a received Read By Type Request and contains the handles and values of the attributes that have been read. The Read By Type Response shall contain complete handle-value pairs. Such pairs shall not be split across response packets. The handle-value pairs shall be returned sequentially based on the attribute handle. The **Read Blob Request** would be used to read the remaining octets of a long attribute value
- **Read Request** - used to request the server to read the value of an attribute and return its value in a Read Response. The attribute handle parameter shall be set to a valid handle. The server shall respond with a Read Response if the handle is valid and the attribute has sufficient permissions to allow reading
- **Read Response** - sent in reply to a received Read Request and contains the value of the attribute that has been read.  The attribute value shall be set to the value of the attribute identified by the attribute handle in the request. The Read Blob Request would be used to read the remaining octets of a long attribute value
- **Read Blob Request** - used to request the server to read part of the value of an attribute at a given offset and return a specific part of the value in a Read Blob Response. The attribute handle parameter shall be set to a valid handle. The value offset parameter is based from zero; the first value octet has an offset of zero, the second octet has a value offset of one, etc. Read Blob Request is the only way to read the additional octets of a long attribute
- **Read Blob Response** - sent in reply to a received Read Blob Request and contains part of the value of the attribute that has been read. The part attribute value shall be set to part of the value of the attribute identified by the attribute handle and the value offset in the request. If the value offset is equal to the length of the attribute value, then the length of the part attribute value shall be zero. If the attribute value is longer than (Value Offset + ATT_MTU-1) then (ATT_MTU-1) octets from Value Offset shall be included in this response.
- **Read Multiple Request** - used to request the server to read two or more values of a set of attributes and return their values in a Read Multiple Response. Only values that have a known fixed size can be read, with the exception of the last value that can have a variable length. The knowledge of whether attributes have a known fixed size is defined in a higher layer specification.
- **Read Multiple Response** - sent in reply to a received Read Multiple Request and contains the values of the attributes that have been read. The Set Of  Values parameter shall be a concatenation of attribute values for each of the attribute handles in the request in the order that they were requested
- **Read by Group Type Request** - used to obtain the values of attributes where the attribute type is known, the type of a grouping attribute as defined by a higher layer specification, but the handle is not known. Only the attributes with attribute handles between and including the Starting Handle and the Ending Handle with the attribute type that is the same as the Attribute Group Type given will be returned. To search through all attributes, the starting handle shall be set to 0x0001 and the ending handle shall be set to 0xFFFF
- **Read by Group Type Response** - sent in reply to a received Read By Group Type Request and contains the handles and values of the attributes that have been read. The Read By Group Type Response shall contain complete Attribute Data. An Attribute Data shall not be split across response packets. The Attribute Data List is ordered sequentially based on the attribute handles
- **Write Request** - used to request the server to write the value of an attribute and acknowledge that this has been achieved in a Write Response. The server shall respond with a Write Response if the handle is valid, the attribute has sufficient permissions to allow writing, and the attribute value has a valid size and format, and it is successful in writing the attribute
- **Write Response** - sent in reply to a valid Write Request and acknowledges that the attribute has been successfully written. The Write Response shall be sent after the attribute value is written
- **Write Command** - used to request the server to write the value of an attribute, typically into a control-point attribute. The attribute handle parameter shall be set to a valid handle. The attribute value parameter shall be set to the new value of the attribute
- **Signed Write Command** - used to request the server to write the value of an attribute with an authentication signature, typically into a control-point attribute. If the authentication signature verification fails, then the server shall ignore the command
- **Prepare Write Request** - used to request the server to prepare to write the value of an attribute. The server will respond to this request with a Prepare Write Response, so that the client can verify that the value was received correctly. A client may send more than one Prepare Write Request to a server, which will queue and send a response for each handle value pair. A server may limit the number of prepare write requests that it can accept. The Value Offset parameter shall be set to the offset of the first octet where the Part Attribute Value parameter is to be written within the attribute value. The server shall not change the value of the attribute until an Execute Write Request is received
- **Prepare Write Response** - sent in response to a received Prepare Write Request and acknowledges that the value has been successfully received and placed in the prepare write queue. The attribute handle shall be set to the same value as in the corresponding Prepare Write Request. The value offset and part attribute value shall be set to the same values as in the corresponding Prepare Write Request
- **Execute Write Request** - used to request the server to write or cancel the write of all the prepared values currently held in the prepare queue from this client. This request shall be handled by the server as an atomic operation. When the flags parameter is set to 0x01, values that were queued by the previous prepare write requests shall be written in the order they were received. When the flags parameter is set to 0x00 all pending prepare write values shall be discarded for this client
- **Execute Write Response** - sent in response to a received Execute Write Request. The Execute Write Response shall be sent after the attributes are written. In case an action is taken in response to the write, an indication may be used once the action is complete
- **Handle Value Notification** - send a notification of an attribute’s value at any time. The attribute handle shall be set to a valid handle. The attribute value shall be set to the current value of attribute identified by the attribute handle
- **Handle Value Indication** - send an indication of an attribute’s value. The client shall send a Handle Value Confirmation in response to a Handle Value Indication. No further indications to this client shall occur until the confirmation has been received by the server. If the attribute handle or the attribute value is invalid, the client shall send a handle value confirmation in response and shall discard the handle and value from the received indication
- **Handle Value Confirmation** - sent in response to a received Handle Value Indication and confirms that the client has received an indication of the given attribute

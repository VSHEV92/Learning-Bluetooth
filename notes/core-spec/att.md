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

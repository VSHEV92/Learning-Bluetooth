# GENERIC ACCESS PROFILE

------

## INTRODUCTION

![GAP](images/gap/stack.png)

This profile states the requirements on names, values and coding schemes used for names of parameters and procedures experienced on the user interface level.

This profile defines modes of operation that are not service- or profile-specific, but that are generic and can be used by profiles referring to this profile, and by devices implementing multiple profiles.

This profile defines the general procedures that can be used for discovering identities, names and basic capabilities of other Bluetooth devices that are in a mode where they can be discovered. Only procedures where no channel or connection establishment is used are specified.

This profile defines the general procedure for how to create bonds (i.e., dedicated exchange of link keys) between Bluetooth devices.

This profile describes the general procedures that can be used for establishing connections to other Bluetooth devices that are in a mode that allows them to accept connections and service requests.

The purpose of this profile is to describe:

- Profile roles
- Discoverability modes and procedures
- Connection modes and procedures
- Security modes and procedures



Roles when Operating over an LE Physical Transport:

- **Broadcaster Role** - A device operating in the Broadcaster role is a device that sends advertising
  events.
- **Observer Role** - A device operating in the Observer role is a device that receives advertising
  events.
- **Peripheral Role** - Any device that accepts the establishment of an LE active physical link using any of the connection establishment procedures is referred to as being in the Peripheral role. A device operating in the Peripheral role will be in the Slave role in the Link Layer Connection State. A device operating in the Peripheral role is referred to as a Peripheral.
- **Central Role** - A device that supports the Central role initiates the establishment of an LE active physical link. A device operating in the Central role will be in the Master role in the Link Layer Connection State. A device operating in the Central role is referred to as a Central.

------

## REPRESENTATION OF BLUETOOTH PARAMETERS

- **Bluetooth Device Address (BD_ADDR)** - A BD_ADDR is the address used by a Bluetooth device. It is received from a remote device during the device discovery procedure. When the Bluetooth address is referred to on UI level, the term **Bluetooth Device Address** should be used. On the baseband level the BD_ADDR is represented as 48 bits.
- **Bluetooth Device Name** - The Bluetooth device name is the user-friendly name that a Bluetooth device exposes to remote devices. For a device supporting the LE-only device type, the name is a character string held in the Device Name characteristic. When the Bluetooth device name is referred to on UI level, the term **Bluetooth Device Name** should be used.
- **Bluetooth Passkey (Bluetooth PIN)** - The Bluetooth passkey may be used to authenticate two Bluetooth devices with each other during the creation of a mutual link key via the pairing procedure. The passkey may be used in the pairing procedures to generate the initial link key. The PIN may be entered on the UI level but may also be stored in the device; e.g., in the case of a device without sufficient MMI for entering and displaying digits. When the Bluetooth PIN is referred to on UI level, the term **Bluetooth Passkey** should be used. For compatibility with devices with numeric keypads, fixed PINs shall be composed of only decimal digits, and variable PINs should be composed of only decimal digits.
- **Class of Device** - Class of device is a parameter received during the device discovery procedure on the BR/EDR physical transport, indicating the type of device. The Class of Device parameter is **only used on BR/EDR and BR/EDR/LE devices** using the BR/EDR physical transport.
- **Appearance Characteristic** - The Appearance characteristic contains a 16-bit number that can be mapped to an icon or string that describes the physical representation of the device during the device discovery procedure. It is a characteristic of the GAP service located on the device’s GATT server. The Appearance characteristic value should be mapped to an icon or string or something similar that conveys to the user a visual description of the device. This allows the user to determine which device is being discovered purely by visual appearance.

------

## OPERATIONAL MODES AND PROCEDURES – LE PHYSICAL TRANSPORT

Several different modes and procedures may be performed simultaneously over an LE physical transport. The following modes and procedures are defined for use over an LE physical transport:

- Broadcast mode and observation procedure
- Discovery modes and procedures
- Connection modes and procedures
- Bonding modes and procedures
- Periodic advertising modes and procedure



#### BROADCAST MODE AND OBSERVATION PROCEDURE

The broadcast mode and observation procedure allow two devices to communicate in a unidirectional connectionless manner using the advertising events.

The **broadcast mode** provides a method for a device to send connectionless data in advertising events. A device in the broadcast mode may send non-connectable and non- scannable undirected or nonconnectable and non-scannable directed advertising events anonymously by excluding the broadcaster's address.

The **observation procedure** provides a method for a device to receive connectionless data from a device that is sending advertising events. A device performing the observation procedure may use active scanning to also receive scan response data sent by any device in the broadcast mode that advertises using scannable advertising events.



#### DISCOVERY MODES AND PROCEDURES

All devices shall be in either non-discoverable mode or one of the discoverable modes. 

A device in the discoverable mode shall be in either the **general discoverable mode** or the limited discoverable mode. A device in the **non-discoverable mode** is not discoverable. 

Devices operating in either the general discoverable mode or the limited discoverable mode can be found by the discovering device.

- **Non-Discoverable Mode** - A device configured in non-discoverable mode will not be discovered by any device that is performing either the general discovery procedure or the limited discovery procedure.

- **Limited Discoverable Mode** - Devices configured in the limited discoverable mode are discoverable for a limited period of time by other devices performing the limited or general device discovery procedure. The limited discoverable mode is typically used when a user performs a specific action to make the device discoverable for a limited period of time. Devices shall remain in the limited discoverable mode no longer than **Tgap(lim_adv_timeout)**.

- **General Discoverable Mode** - Devices configured in general discoverable mode are intended to be discoverable by devices performing the general discovery procedure. The general discoverable mode is typically used when the device is intending to be discoverable for a long period of time.

  

- **Limited Discovery Procedure** - A device performing the limited discovery procedure receives the device address, advertising data and scan response data from devices in the limited discoverable mode only.

- **General Discovery Procedure** - A device performing the general discovery procedure receives the device address, advertising data and scan response data from devices in the limited discoverable mode or the general discoverable mode.

- **Name Discovery Procedure** - The name discovery procedure is used to obtain the Bluetooth Device Name of remote connectable device. If the complete device name is not acquired while performing either the limited discovery procedure or the general discovery procedure, then the name discovery procedure may be performed.

  

#### CONNECTION MODES AND PROCEDURES






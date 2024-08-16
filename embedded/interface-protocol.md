# Protocol or Interface

What are an interface and a protocol in the context of communication systems
and what's the difference between them?

> **NOTE:** The term interface is considered here as hardware interface.

**An interface** is a set of physical and logical information that facilitates
communication. It basically deals with the physical characteristics of the
transmission medium. The only purpose of an interface is to provide the
ability to send bits over a medium.

**A protocol**, on the other hand, works with data bits. It structures the bits
into data frames, handles errors in underlying communications, defines
request-response packet sequences and etc. In other words, it doesn't deal with
the physical representation of the signal and operates with data itself.

Briefly:
- *An interface* is based on physics.
- *A protocol* is based on an interface or another protocol.

## The real world

Unfortunately, in the real world, many interfaces define some features of a
protocol. For example, RS-232 defines a flow control mechanism (RTS/CTS), and
UART defines frame formad, even though both are regarded as interfaces.

People often use the terms "interface" or "protocol" for communication, to
reference specific parts of technologies.

## Examples

**What interfaces define in real life:**
- Voltage:
  - RS-232 (+-15V)
  - MLT-3
- Pinout:
  - DE-9 RS-232
  - TIA-568
- Connectors:
  - DB-25
  - DE-9
  - 8P8C (RJ45)
- Encoding:
  - PAM-4
  - 4B5B
  - NRZI
- Characteristics of cables:
  - 100BASE-TX (Twisted pairs)
  - 1000BASE-SX (Fiber-optic)
  - 10GBASE-CX4 (Twinaxial cable)
  - USB 3.0 (Twisted pairs)

**What protocols define in real life:**
- Frame structure:
  - Ethernet frame
  - WIFI frame
  - UART frame
- Error detection and correction:
  - Parity bit
  - CRC
  - TCP data retransmission
- Flow control:
  - RTS/CTS
  - Ethernet pause frame
- Packet sequences:
  - ARP request / responce
  - DHCP DORA
  - TCP handshake
- Data handling:
  - Recursive DNS request
  - HTTP GET request

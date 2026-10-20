# DTE and DCE RS-232 Concept Network
Initially, **DTE** and **DCE** were used as decvices that create a network by
different mediums.

**DTE**:
- Stands for **Data Terminal Equipment**.
- Is an end-user device where a human operates.

**DCE**:
- Stands for **Data Circuit-terminating Equipment**
- Is a device (e.g a modem) that facilitates data transmission through a
network.

Scheme:  
DTE --- DCE <---------> DCE --- DTE
- DTE and DCE devices are connected by RS-232
- DCE and DCE devices are connected by any other carrier (e.g phone network)

The reason for separation of DTE and DCE is flexibility. DTE devices should only
implement RS-232 and are able to communication to other DTE devices through any
medium, if the connect to suitable DCE devices.

In modern world we use a similar concept:
DTE / DCE network | Modern network
--                | --
DTE               | Computer (motherboard)
DCE               | NIC (Network interface card)
RS-232            | PCI, PCIe
DCE carrier       | Ethernet, Wi-Fi and Fibre Channel

## DTE
**A DTE** (Data terminal equipment) is one side of the RS-232 communication.

Originally, **terminals (or DTE devices)** were:
- A teletypewriter:
  - A keyboard with a mechanical typewriter.
- A video display terminal:
  - A computer that gave the ability to insert text input and was able to
    print text on the screen.

In the modern RS-232, devices that act as DTE:
- PCs with terminal emulators
- Printers
- Fax machines
- Computer mice

> **NOTE:** Some devices act as only input (computer mice) devices or output
(printers) devices.

## DCE
**A DCE** (Data circuit-terminating equipment) is another side of the RS-232
communication.

The term **circuit-terminating** denotes that this circuit (DCE device)
terminates (is placed at the end of) the current communication point (DTE + DCE).

Originally, **DCE devices** were modems. A modem is a device that is able to
communicate through a specific medium usually through great distance.

In the modern RS-232, devices that act as DCE:
- Modems
- Switches
- Routers
- GPS receivers

> **NOTE:** Data communication equipment (DCE) is a former denotation.

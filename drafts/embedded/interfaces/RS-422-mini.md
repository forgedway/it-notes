# RS-422
is an interface for serial, point-to-point (or unidirectional multidrop bus),
full-duplex (or half-duplex) communication.

It's often used as a point-to-point, full-duplex connection.

## Physical characteristics
- Cables are up to 1200m long.
- Baud rate is up to 10MBd.
- Voltages:
  - Logic 1: -6V - -2V
  - Logic 0: +2V - +6V

## Connection
Lines:
  - RX+, RX-
  - TX+, TX-
  - GND
  - Any subset of these where every signal is a differential signal:
    - DCD, DTR, DSR, RTS, CTS (from RS-232)

Connectors:
  - DE-9
  - Terminal block

Pinouts:
  - Manufacture-specific

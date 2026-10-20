# RS-232
is an interface for serial, point-to-point, full-duplex communication.

## Physical characteristics
- Data rate is usually up to 115200 bps.
- Cables mostly are less than 15 meters.
- Voltages:
  - Logic 1: -15V - -3V
  - Logic 0: +3V - +15V

## Connections
3 wire connection:
  - Tx, Rx, GND

5 wire connection:
  - Tx, Rx, GND
  - RTS, CTS

Connectors:
  - DE-9
  - DB-25

## Additional Information
RS-232 is an asymmetric interface. It uses DTE and DCE equipments for
connection. DE-9 plugs represent DTE and DE-9 sockets represent DCE.

For DTE-DTE connection it has:
  - null mode modems
  - null mode cables

It might use RTR (RTS) and CTS for flow control:
  - RTR (Ready to Receive) is used to allow transmission from DCE.
  - CTS (Clear to Send) is used to allow transmission from DTE.

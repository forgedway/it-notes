# UART
is a standard that partially defines an interface and a protocol.

## Interface part
- Serial:
  - Converts from parallel data input to serial impulses.
- Asynchronous:
  - It doesn't have a clock signal. It uses start and stop bits for synchronization.

## Protocol part
- Defines data frame:
  - Start bit (1)
  - Data bits (5-9)
  - Parity bit (0-1)
  - Stop bit(s) (1-2)
- It detects bit flipping.
- Transmits the least significant bit first.

## Additional Information
- It uses logic level signals.
- It is integrated into microcontroller or placed as a separate IC.

- It often uses RS-232, RS-422 or RS-485 drivers to transmit data.
- UART ICs might handle RS-232 signals like DCD, DTR, DSR, RTS, CTS.

Errors:
  - Overrun
  - Underrun
  - Frame error
  - Parity error
  - Break condition

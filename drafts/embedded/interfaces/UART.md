**UART (Universal asynchronous receiver-transmitter)** is a hardware interface
for asynchronous serial communication. It sends data bits one by one, from the
least significant to the most significant bit.

Parts of the UART that might be considered as interface:
  - Communication is serial:
    - Transmission is executed in bit by bit manner.
  - Communication is asynchronous:
    - Transmission doesn't have a common clock signal and uses start and stop
      bits for synchronization.

Parts of the UART that might be considered as protocol:
  - It defines data frame.
  - It detects errors (bit flipping) by the parity bit.
  - It transmits the least significant bit first.

A peripheral device that implements the UART interface might be referred as UART
too. An UART device might be an IC or might be integrated in a microcontroller
chip. The device is responsible for converting parallel data from a
microcontroller into a serial format for transmission and vice versa.

UART doesn't define voltage levels and uses logic level signals to transmit
data. UART circuits use different driver ICs to transmit data by external wires
such as RS-232, RS-422 and RS-485.


## Data frame
Bits       | Count of bits | Possible states | Description
--         | --            | --              | --
Idle       | 0-...         | 1               | The state when there's no data is being transmitted.
Start bit  | 1             | 0               | The start bit signals that some data is comming since the next bit.
Data bits  | 5-9           | 0 / 1           | Transmitting data.
Parity bit | 0-1           | 0 / 1           | It's used to detect bit flipping.
Stop bit   | 1-2           | 1               | The stop bit is used to synchronize sender and receiver clocks.

The most common settings is "8N1":
  - 8 data bits
  - No parity bit
  - 1 stop bit.


## Settings
The following settings must be the same on both communicating sides to work:
  - Data bit size:
    - 5-9 bits
  - Parity bit:
    - None (Don't use the parity bit)
    - Even (Count of logic highs must be even)
    - Odd (Count of logic highs must be odd)
    - Mark (Always logic high)
    - Space (Always logic low)
  - Stop bit:
    - 1 bit
    - 1.5 bits
    - 2 bits
  - Baud rate
    - The most common: 4800, 9600, 19200, 38400, 57600, 115200 bps
    - Any others bit rates
  - Voltage level:
    - Logic level signals
    - Interfaces RS-232, RS-422 or RS-485
    - Any other interfaces


## Special conditions
Name            | Communication side | Description
--              | --                 | --
Overrun error   | Receiver           | When there's no buffer space to hold received data.
Underrun error  | Transmitter        | When there's no data to send.
Framing error   | Receiver           | When a receiver doesn't see a stop bit at the expected place.
Parity error    | Receiver           | When a parity check failed.
Break condition | Receiver           | When an input is at the logic low (not in idle state) for some time.

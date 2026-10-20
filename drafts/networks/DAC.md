# DAC

DAC (Direct Attach Copper) is a full duplex cable that is designed for
high-speed data transmission in data centers and networking applications. Due
to cost efficiency, it became popular in 2013 for very-short-range high speed
differential signaling applications.

**Twinaxial vs Coaxial Cable:**
- **Coaxial Cable** has one inner conductors surrounded by a shield.
  - Two conductors in total.
  - The inner conductor is a data signal.
  - The shield is a ground signal.
- **Twinaxial Cable** has two inner conductors surrounded by a common shield.
  - Three conductors in total.
  - The inner conductors are a differential signal.
  - The shield is a ground signal.

**Passive vs Active twinax cable:**
- **Passive Twinax Cable** is usually shorter than 7 meters.
- **Active Twinax Cable** is longer than 7 meters and has active electronic components.

DAC cable has lower latency and is more power efficient than 10GBASE-T cables:

-                     | DAC cable | 10GBASE-T cables
--                    | --        | --
**Latency**           | 0.1 μs    | 1.5 to 2.5 μs
**Power Consumption** | 0.1 watts | 4-8 watts

## Speeds

Speed      | Twinaxial Cables | Cable Type
--         | --               | --
1 Gbit/s   | 1                | SFP DAC
10 Gbit/s  | 1                | SFP+ DAC
25 Gbit/s  | 1                | SFP28 DAC
50 Gbit/s  | 1                | SFP56 DAC
--         | --               | --
4 Gbit/s   | 4                | QSFP DAC
40 Gbit/s  | 4                | QSFP+ DAC
50 Gbit/s  | 2                | QSFP28 DAC
100 Gbit/s | 4                | QSFP28 DAC
200 Gbit/s | 4                | QSFP56 DAC

## Backward compatibility

Each SFP (or QSFP) port is backward compatible to slower versions ports. For example:
- SFP28 is backward compatible to SFP+ and SFP
- QSFP56 is backward compatible to QSFP28, QSFP+ and QSFP

Also QSFP/QSFP+/QSFP28/QSFP56 are designed to be electrically backward
compatible with SFP/SFP+/SFP28 or SFP56 respectively. Backward compatibility
might be exploited by a simple adapter.

> **NOTE:** The compatibility might depend on the specific hardware.

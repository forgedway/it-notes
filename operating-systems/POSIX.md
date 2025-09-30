# POSIX
POSIX (The Portable Operating System Interface) is a family of standards for
maintaining compatibility between operating systems. It means that if a
program uses the POSIX API, whether `C` or shell utilities, it will work on
any OS that is posix compliant.

It's divided into four volumes:
- Base Definitions (XBD). It defines **general ideas**:
  - Character set
  - POSIX Locale
  - Regular expressions
  - Utility conventions
  - C headers
  - ...
- System Interface (XSH). It defines C API:
  - Threads
  - Sockets
  - Filesystem
  - Interprocess communication
  - ...
- Shell and Utilities (XCU):
  - Shell command language (pipes, subshells and so on)
  - Utilities (`ls`, `grep`, `seq` and so on)
- Rationale (XRAT)


## Development Before 1997
Before 1997, it had been developed by IEEE.

POSIX comprised of different standards:
- `POSIX.1`: Core C API
- `POSIX.2`: Shell and Utilities
- `POSIX.1` extensions (C API):
  - `POSIX.1b`: Real-time extensions
  - `POSIX.1c`: Threads extensions
  - `POSIX.1d`: Additional real-time extensions
  - `POSIX.1g`: Networking APIs
  - `POSIX.1j`: Advanced real-time extensions
  - ...

So any OS might have been compliant to a specific standard. For example, an OS
could have been compliant to `POSIX.2` (shell and utilities), but not to
`POSIX.1` (`C` API). It changed after 1997.

## Development After 1997
After 1997, `POSIX` is developed and maintained by the Austin Group. Since the
time it unites all standards (`POSIX.1`, `POSIX.2`, `POSIX.1.b`, ...) under
one name. Despite being one standard, `POSIX` compliant OS must support base
part and can support or not support different extensions like realtime or
threads. To signal that an extension or a feature is supported the OS defines
corresponding constants (e.g. `_POSIX_THREADS` or `_POSIX_REALTIME_SIGNALS`).

The first united POSIX standard, `POSIX.1-2001`, was developed in
2001. Since then, all subsequent revisions have included the year of
development as a prefix.

POSIX revisions:
- `POSIX.1-2001`
- `POSIX.1-2008`
- `POSIX.1-2017`
- `POSIX.1-2024`
---

### The Austin Group

Members                   | Description
--                        | --
IEEE                      | An American professional association.
ISO/IEC JTC 1/SC 22/WG 15 | The International Organization for Standardization.
The Open Group            | A global consortium.
---

POSIX standard names example:
POSIX.1-2017 Standard Name                                     | Organization
--                                                             | --
IEEE Std 1003.1-2017                                           | IEEE
ISO/IEC/IEEE 9945:2009/Cor:2-2017                              | ISO/IEC
The Open Group Technical Standard Base Specifications, Issue 7 | The Open Group
---

The Open Group also:
- Certificates different products as POSIX-compliant.
- Publishes POSIX standard:
  - https://pubs.opengroup.org/onlinepubs/9799919799/

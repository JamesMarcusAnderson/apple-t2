# apple-t2

Notes from putting a 2020 MacBook Air (Apple T2) into DFU mode and enumerating it over USB.

![scope: enumeration only](https://img.shields.io/badge/scope-DFU%20enumeration%20only-8e8e93)
![hardware: Apple T2](https://img.shields.io/badge/hardware-Apple%20T2-lightgrey)

## Scope — read this first

This repo documents **DFU-mode USB enumeration only**: getting the T2 into DFU and reading back the USB descriptors the chip presents. That's the whole project.

It does **not** document, and I did **not** perform: fault injection, voltage glitching, SecureROM extraction, jailbreaking, or any exploit work against the T2. Anecdotal claims without captured terminal evidence were deliberately excluded from these notes.

## Setup

- **Host:** 2015 MacBook 12" (x86_64), macOS 10.15.7
- **Target:** 2020 MacBook Air with Apple T2
- **Cable:** "banana" J137 debug cable — USB-A + USB-C into the host (data + power), USB-C into the target's port 1

## Entering DFU

Apple's documented sequence for portable Macs: power the Mac fully off, connect the cable to the front-left Thunderbolt port, then hold **power + right Shift + left Control + Option** for ~3 seconds. The timing took several attempts to land (too long and the Mac just reboots into macOS). When it works, the target's screen stays black and the T2 enumerates on the host as a DFU device.

## What enumeration showed

`system_profiler SPUSBDataType` on the host, target in DFU:

```
Apple Mobile Device (DFU Mode):
    Product ID:  0x1227
    Vendor ID:   0x05ac (Apple Inc.)
    Version:     0.00
    Serial Number: CPID:8012 CPRV:10 CPFM:03 SCEP:01 BDID:3F ECID:[redacted]
                 IBFL:3C SRTG:[iBoot-3401.0.0.1.16]
```

Decoded identifiers:

| Field | Value | Meaning |
|-------|-------|---------|
| PID | `0x1227` | Apple's DFU-mode USB product ID |
| VID | `0x05ac` | Apple Inc. |
| CPID | `8012` | Chip ID — the Apple T2 |
| CPRV | `10` | Chip revision |
| CPFM | `03` | Chip fabrication variant |
| SCEP | `01` | Secure Enclave present |
| BDID | `3F` | Board ID |
| IBFL | `3C` | iBoot flags |
| SRTG | `iBoot-3401.0.0.1.16` | SecureROM/iBoot version string |
| ECID | `[redacted]` | Unique chip ID — redacted |

`ioreg -p IOUSB` corroborated the same serial string on the DFU device node.

In **normal (non-DFU) mode**, the same machine presents differently:

```
Apple T2 Controller:
    Product ID:  0x8600
    Vendor ID:   0x05ac (Apple Inc.)
```

The J137 cable itself enumerates as a "Potassium Hub" (`0x1635`) / "Potassium" (`0x1636`) device — that is the cable's hub, not the T2. Worth knowing before you go chasing it.

## Tooling attempts

- **`cfgutil` (Apple Configurator 2.15.1):** `cfgutil list` saw the target as `Type: iBridge2,15`. A `revive` attempt downloaded the image but failed: *"The required framework 'MobileDevice' is out of date. Please update macOS."* — the 10.15.7 host couldn't satisfy it.
- **`usbterm` (Apple RestoreTools):** found the DFU device (`PID 0x1227`, `CPID:8012` serial) but errored opening the USB interface (`0xe00002c7`) and kept waiting for the device.
- **`macvdmtool`:** attempted; ioreg confirmed the Potassium hub path but no T2 interaction resulted.
- **Serial:** connecting the cable exposed `/dev/cu.usbmodem1411202`. Output captured at default settings was not decodable (baud mismatch) — no T2 console output was captured.

## Layout

- `notes/dfu-enumeration.md` — the captured terminal sessions, redacted
- `SOURCES.md` — every claim traced to its source conversation

## License

Notes and documentation are mine; no Apple software, firmware, or internal tools are included or redistributed.

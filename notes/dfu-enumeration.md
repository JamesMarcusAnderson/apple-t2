# DFU enumeration — captured sessions (redacted)

All output below is pasted terminal output from the source conversations listed in
`SOURCES.md`. ECIDs, device serial numbers, and nonces are redacted; device-class
identifiers (CPID, BDID, iBoot version) are not unique to the device and are kept.

## Session 1 — 2025-11-02: first successful DFU enumeration

Host: 2015 MacBook 12", macOS 10.15.7. Target: 2020 MacBook Air (T2).
Banana J137 cable: USB-A + USB-C to host, USB-C to target port 1.

After several attempts at the key sequence (power + right Shift + left Control +
Option), the target entered DFU — black screen, and the host saw:

```
$ system_profiler SPUSBDataType | grep -i "dfu\|apple t2" -A 5

                Apple Mobile Device (DFU Mode):

                  Product ID: 0x1227
                  Vendor ID: 0x05ac (Apple Inc.)
                  Version: 0.00
                  Serial Number: CPID:8012 CPRV:10 CPFM:03 SCEP:01 BDID:3F ECID:[redacted] IBFL:3C SRTG:[iBoot-3401.0.0.1.16]
```

```
$ ioreg -p IOUSB -l -w 0 | grep -i -E "boardid|chipid|8012|serial" | head -20
    ...
    |   |   "USB Serial Number" = "[redacted]"
    ...
    |         "iSerialNumber" = 4
    |         "kUSBSerialNumberString" = "CPID:8012 CPRV:10 CPFM:03 SCEP:01 BDID:3F ECID:[redacted] IBFL:3C SRTG:[iBoot-3401.0.0.1.16]"
```

Same session, target in normal mode (for contrast):

```
$ system_profiler SPUSBDataType | grep -i "apple t2\|dfu" -A 3
                Apple T2 Controller:

                  Product ID: 0x8600
                  Vendor ID: 0x05ac (Apple Inc.)
```

And the cable's own hub, present regardless of target state:

```
            Potassium Hub:

              Product ID: 0x1635
              Vendor ID: 0x05ac (Apple Inc.)
              Version: 0.01
              Serial Number: [redacted]
```

(`0x1635` = hub, `0x1636` = "Potassium" device. Both belong to the J137 cable —
not the T2.)

## Session 2 — 2025-11-02: cfgutil

```
$ cfgutil list
Type: iBridge2,15	ECID: [redacted]	UDID: N/A Location: 0x14111000 Name: N/A
```

`cfgutil` addressed the target by ECID as `iBridge2,15` (the T2 board ID for this
machine). Property reads mostly returned "The device is not booted into the
System" (expected in DFU). A `revive` attempt:

```
$ cfgutil --ecid [redacted] revive

Waiting for the device [1/4] [*******************************************]  100%
Step 2 of 4: Downloading System [2/4] [**********************************]  100%
Step 3 of 4: Unzipping System [3/4] [************************************]  100%
cfgutil: error: The required framework "MobileDevice" is out of date. Please update macOS.
(Domain: ConfigurationUtilityKit.error Code: 401)

"revive" failed on Unknown device (ECID: [redacted]).
```

The 10.15.7 host could not satisfy the framework requirement. No restore was
performed.

## Session 3 — 2025-11-11: usbterm

Apple's `usbterm` (from RestoreTools) found the DFU device but could not open it:

```
$ sudo .../usbterm -pid 0x1227
=======================================
::
::	APPLE USB TERMINAL
::	@(#)PROGRAM:usbterm  PROJECT:usbterm-55
::
:: (User Authlisting enabled)
:: (Hit CTRL+C to quit)
=======================================

:: Device Found -
::	 Product ID    : 0x1227
::	 Serial Number : CPID:8012 CPRV:10 CPFM:03 SCEP:01 BDID:3F ECID:[redacted] IBFL:3C SRTG:[iBoot-3401.0.0.1.16]
::	 Location ID : 0x14310000
:: (Press ENTER to continue...)
usb_serial:find_interface - Error getting plugin interface for interface: 0xe00002c7
usb_serial:usb_device_added - Error finding interface 0 (0xfe, 0x1, 0x2), 0xe00002c7
:: Waiting for device with PID: 0x1227...
```

Device listing from the same tool confirmed the identifiers once more:

```
(a) 0x1227, 0x14310000, CPID:8012 CPRV:10 CPFM:03 SCEP:01 BDID:3F ECID:[redacted] IBFL:3C SRTG:[iBoot-3401.0.0.1.16] NONC:[redacted] SNON:[redacted]
```

## Session 4 — serial device appearance

Connecting the J137 cable exposed a USB serial device on the host:

```
$ ls /dev
...
cu.usbmodem1411202
...
tty.usbmodem1411202
...
```

Output read at default settings was not decodable (baud mismatch — framing
garbage). No T2 console output was captured.

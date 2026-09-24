# Sources

Every factual claim in this repo traces to James Anderson's own chat messages or
pasted terminal output in his DeepSeek archive
(`~/workspace/deepseek-archive/conversations.json`, 965 conversations).
Assistant replies were never used as evidence. Redactions: ECIDs, hardware serial
numbers, USB nonces.

## Evidence

| Conversation ID | Date | Title | What it evidences |
|---|---|---|---|
| `30cdc69f-93ee-408e-837b-c3b5066f1c45` | 2025-11-02 | Entering DFU Mode for T2 Diagnostics Guide | **Primary.** Setup (banana J137 cable: USB-A+USB-C to 2015 MacBook 12" host on macOS 10.15.7, USB-C to 2020 T2 MacBook Air port 1); DFU key-sequence attempts and timing; successful DFU enumeration via `system_profiler` (PID `0x1227`, serial `CPID:8012 CPRV:10 CPFM:03 SCEP:01 BDID:3F … SRTG:[iBoot-3401.0.0.1.16]`); `ioreg` corroboration; normal-mode "Apple T2 Controller" PID `0x8600`; Potassium Hub `0x1635` / Potassium `0x1636` belonging to the cable, with James's own note that Potassium "isnt the t2 chip"; `/dev/cu.usbmodem1411202` serial device appearance; `cfgutil list` → `iBridge2,15`; `cfgutil revive` failure ("MobileDevice framework out of date"); macvdmtool attempt |
| `352eb71f-441b-4cb1-a00d-781a15fc100e` | 2025-11-11 | User seeks macOS kernel protection bypass commands | `usbterm` found the DFU device (PID `0x1227`, `CPID:8012` serial) but failed to open the USB interface (`0xe00002c7` errors) |
| `102552e2-d185-41e3-9b6f-50f8992f6892` | 2025-11-11 | (DFU session) | Repeat DFU enumeration via `system_profiler`; `usbterm` device listing with identifiers |
| `29ae1177-68b6-4329-84ec-03c4a3944302` | 2025-11-09 | Troubleshooting Apple Service Utility Missing Framework | Repeat DFU enumeration via `system_profiler` |
| `8b493269-9e85-4d93-a478-f75ea258c459` | 2025-11-09 | Fixing macOS App Code Signature Issues | Repeat DFU enumeration via `system_profiler` |
| `9e9938b6-6a48-40d5-a9b1-03a3befd8997` | 2025-06-21 | Apple J137 DC Cable Technical Guide | James's own description of the "banana J137" / DCSD Alex cable used in the setup |

## Deliberately left out

- **Cable flash-dump analysis** (`602693b9-…`, 2025-10-30: `strings` on a `Potassium_…_Flash.bin` file). Out of scope — it concerns the debug cable's firmware, not T2 enumeration, and firmware is never redistributed here.
- **Pasted reference material** (Wikipedia T2/checkm8 text, iBoot-log and DFU-identification guides). Public reference James pasted for context; not his findings, not presented as such.
- **Anecdotal claims without captured terminal evidence.** Per the standing fact-check rule (own words + terminal output are the source of truth), stories that appear in chat but have no supporting captures are excluded from this repo. Details intentionally not repeated here.
- **Assistant speculation** from RESPONSE fragments — never treated as evidence.
- **Unrelated threads** that merely mention "T2" (jailbreak tooling for iPhones, Apple-internal software inventory, etc.).

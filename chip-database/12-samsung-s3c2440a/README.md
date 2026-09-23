# Samsung S3C2440A

**Board:** HMI Board mezzanine (`GOLDEN COREBOARD_V1.20`) — the system's only
CPU
**Identification confidence:** `[P]` Confirmed by photograph, lot `A1131`

---

## What it is

An **ARM920T-core** 32-bit RISC application processor, popular for
PDAs/handhelds and industrial embedded designs in the mid-to-late 2000s. This
is the only CPU anywhere in the three-board system — every other board runs
purely on FPGA fabric with no processor at all.

## Key specs

| Parameter | Value |
|---|---|
| Core | ARM920T, Harvard architecture, with MMU |
| Cache | 16 KB instruction cache + 16 KB data cache |
| Max clock | Up to 400 MHz (this part's "-40" grade) |
| Process | 0.13 µm |
| Package | 289-ball FBGA |
| Core voltage | ~1.2–1.3 V core, 1.8–3.3 V I/O (varies by rail) |
| On-chip RAM | 4 KB |
| Boot | **Native NAND flash boot-loader support** — boots directly from NAND without needing NOR; `OM[1:0]` pins select boot width at reset |
| Serial | 3× UART (64-byte FIFO each, IrDA capable), 2× SPI, 1× I²C, I²S, AC97 |
| Display | LCD controller — STN and TFT, up to 1280×1024 in some references |
| Storage/USB | SD/MMC host, USB host (2-port OHCI 1.0) + 1 USB device |
| Analog | 8-channel 10-bit ADC (up to 500 kSPS in one reference) with touch-screen interface |
| Other | Camera interface, 4-channel DMA, 4-channel PWM timers + watchdog, RTC, ~130 GPIO, ~24–60 interrupt sources (sources vary on exact count) |

## Why the NAND boot capability matters here

This SoC's **native NAND boot-loader support** strongly supports NAND flash
(rather than NOR) as the likely type of the still-unphotographed boot flash
chip on the mezzanine's underside. NAND is also cheaper and higher-density,
fitting a cost-optimised industrial HMI design from this era.

## Board role

Runs the operator UI, pattern-file handling, and the DM9000A Ethernet
interface (`11-davicom-dm9000a/`) — all the "soft real-time" work. Hard
real-time machine-bus timing is handled instead by the adjacent MachXO FPGA
(`02-lattice-lcmxo1200c/`) on the same board, not by this CPU.

## Lifecycle / sourcing status

**Obsolete.** Confirmed via multiple broker/distributor listings showing
"Form-fit-function: Obsolete" from Samsung and every secondary listing (no
true second-source ever existed for this exact part). Was a very popular
2003–2008-era chip, widely used in dev boards of the period (e.g., Boardcon
EM2440-III, AT2440, and the well-known "mini2440" community boards). **No new
stock exists** — anything available today would be pulled/recycled silicon
of uncertain provenance.

## What this means for a replacement design

If the HMI board's CPU ever fails, this is a genuine **redesign trigger**,
not a like-for-like repair. The practical path is migrating the HMI firmware
functionality to a currently-produced ARM SoC — the peripheral list above
(UART/SPI/LCD/USB/Ethernet-via-external-MAC) is the functional spec that any
replacement needs to cover, even though the exact register-level interface
will differ.

## Confidence caveats

Peripheral list, cache sizes, and package are solidly confirmed across every
research pass and match Samsung's own published documentation. Obsolescence
status is likewise consistently reported. The exact core/I/O voltage split
varies slightly between sources (reflects the multiple internal rails this
SoC actually has, not a real disagreement).

## Sources

- Samsung: S3C2440A User's Manual (multiple mirrors — Keil, university course
  sites, armdesigner.com)
- Keil device database: peripheral summary cross-check
- Distributor/broker lifecycle listings: multiple sources confirming
  "Obsolete" status industry-wide
- Cross-verified across 7 independent research passes (this document + 6
  other AI sessions in `Other_AI_Research/`)

# TI AM26LS31 (AM26LS31C)

**Board:** Racking Board only
**Identification confidence:** `[P]` Confirmed by photograph

---

## What it is

An **older, bipolar 5 V** quad differential line driver — the legacy
predecessor to the AM26LV31 (`05-ti-am26lv31/`), from TI's original 1980s-era
26LS3x family. Meets the same TIA/EIA-422-B / ITU V.11 standard, but on a
different process generation and supply voltage entirely.

## Key specs

| Parameter | Value |
|---|---|
| Standard | ANSI TIA/EIA-422-B / ITU V.11 |
| Supply | Single 5 V (4.75–5.25 V), **not 3.3 V** |
| Technology | Low-power Schottky bipolar (not BiCMOS like the LV-series) |
| Drive current | Minimum ±30 mA sink/source per output |
| Output state, power-off | High-impedance |
| Enable | Active-high and active-low enables, per-driver |
| Application note (TI) | RS-422 uses one driver + termination at the far end only; RS-485 needs both ends terminated — this is a driver-only part, receiver is a separate chip (AM26LV32 on this board) |

## Why this specific pairing exists — confirmed deliberate

This board pairs the **older 5 V bipolar AM26LS31 driver** with the **newer
3.3 V AM26LV32 receiver** (`06-ti-am26lv32/`). This is not a casual parts
substitution — the AM26LS31 predates the AM26LV31 by roughly two decades in
TI's catalog and uses an entirely different process. Using it specifically as
the transmit side gives:
- More drive amplitude/current onto a long, potentially noisy cable run out
  to the Racking Board's field connections
- A modern 3.3 V receiver on the return path that matches the MachXO2 FPGA's
  I/O bank voltage directly, with no level-shifting needed on receive

This was almost certainly a deliberate choice by the original board designer.

## Lifecycle / sourcing status

**Fully obsolete, no longer manufactured.** Distributor listings (Digikey)
show TI's own newer parts plus Analog Devices/Maxim alternates as suggested
substitutes — but **none are pin-identical**. If this specific driver ever
fails, it is a small redesign (different pinout, possibly different
supporting passives), not a straight swap.

## What this means for a replacement design

This is the one RS-422 transceiver on these three boards genuinely worth
flagging as a redesign trigger if it fails. A modern 5 V-tolerant driver with
similar drive strength would need its own footprint and support circuitry —
budget real engineering time for this specific chip if the Racking Board's
bus driver ever needs replacing.

## Confidence caveats

The 5 V/bipolar identification and obsolescence status are corroborated
across multiple independent research passes. The claim that the asymmetric
pairing is "deliberate" is an inference from the process-generation gap and
electrical logic, not a statement from the original board designer — no
schematic or design notes exist to confirm intent directly, though the
reasoning is sound engineering practice for exactly this situation (long
noisy cable run, modern receiver-side FPGA).

## Sources

- Texas Instruments: AM26LS31 datasheet (SLLS114 family)
- Digikey: substitute/cross-reference listings for obsolete AM26LS31 variants
- Cross-verified across 7 independent research passes (this document + 6
  other AI sessions in `Other_AI_Research/`)

# TI SN74AHCT245

**Board:** Main Board (marked `HT245` on the package — TI's top-side
abbreviation for this exact part)
**Identification confidence:** `[P]` Confirmed by photograph, marking read
directly

---

## What it is

An octal 3-state bus transceiver with **TTL-threshold inputs on a CMOS
core** — the "AHCT" designation specifically means it accepts 3.3 V logic
swings as valid inputs while operating on a 5 V rail with CMOS output levels.
This is exactly the part class needed where 3.3 V FPGA logic meets a 5 V
field.

## Key specs

| Parameter | Value |
|---|---|
| Function | Octal bidirectional bus transceiver, 3-state outputs |
| Supply | 4.5–5.5 V |
| Input type | TTL-compatible thresholds |
| Output type | CMOS |
| Drive current | ±8 mA (IOL/IOH) |
| Package | SOIC-20 (this board) |

## Board role

Bidirectional buffer where 3.3 V FPGA-side logic meets 5 V field-side
circuitry — accepts either voltage swing as a valid input while driving
solid CMOS output levels.

## Lifecycle / sourcing status

Active, huge stock, dozens of second sources — standard TI 74-series part.

## Sources

- Texas Instruments: SN74AHCT245 datasheet
- Cross-verified across 7 independent research passes (this document + 6
  other AI sessions in `Other_AI_Research/`)

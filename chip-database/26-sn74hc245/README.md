# TI SN74HC245

**Board:** Racking Board, designator `U9`
**Identification confidence:** `[P]` Confirmed by photograph

---

## What it is

An octal 3-state bus transceiver on standard CMOS thresholds — used where
both sides of the interface already share compatible voltage levels (unlike
the AHCT variant on the Main Board, which specifically bridges 3.3 V and
5 V — see `25-sn74ahct245/`).

## Key specs

| Parameter | Value |
|---|---|
| Function | Octal bidirectional bus transceiver, 3-state |
| Supply range | 2–6 V |
| Drive capability | Can drive up to 15 LSTTL loads |
| Drive current | ±6 mA at 5 V (IOL/IOH) |
| Propagation delay | ~12 ns typical |
| Temperature range | −40°C to 85°C (this grade) |
| Package | SOIC-20 |

## Board role

Bus transceiver on the Racking Board's local logic, likely bridging the
MachXO2 FPGA to some parallel-bus-facing section of the board.

## Lifecycle / sourcing status

Active, huge stock — standard 74-series part.

## Sources

- Texas Instruments: SN74HC245 datasheet
- Cross-verified across 7 independent research passes (this document + 6
  other AI sessions in `Other_AI_Research/`)

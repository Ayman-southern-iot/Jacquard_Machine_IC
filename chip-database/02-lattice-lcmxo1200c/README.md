# Lattice LCMXO1200C-3TN144C

**Board:** HMI Board, carrier board `U1` — machine-bus master + glue logic
**Identification confidence:** `[P]` Confirmed by photograph, lot `A3141RV9`

---

## What it is

A non-volatile FPGA/CPLD from Lattice's first-generation **MachXO family**.
Positioned by Lattice as combining FPGA-style LUT logic with CPLD-style
instant-on behavior — the "MachXO" name is literal: "machine XO" for
instant-on. Note this board's own `U1` is a different physical device from the
Main Board's `U28` — same designator name, different chip, different board.

## Key specs

| Parameter | Value |
|---|---|
| Logic capacity | ~1,200 LUT4s, ~150 LABs (logic array blocks) |
| Embedded memory | ~9.2 Kbit block RAM + distributed RAM; one source states up to 256 Kbit on-chip flash for the family overall |
| Instant-on | Yes — internal flash config array, no external boot ROM |
| Sleep mode | Static Icc drops to <100 µA in Sleep; wake-up ≤800 µs; logic state is **not** retained during sleep |
| Internal oscillator | On-chip CMOS oscillator, commonly cited around 18–26 MHz for one default tap — **flagged on validation**: MachXO's internal oscillator is typically a divided/tapped structure with several selectable output frequencies, so this range should be read as "one commonly cited operating point," not the device's full programmable range, until checked against the specific speed grade's oscillator table |
| Config interfaces | JTAG background programming; **TransFR** live-update (reprogram while running) |
| Package (this part) | TQFP-144 |
| Core voltage | 3.3 V ("C" grade parts) |

## Board role (inferred)

Sits between the ARM CPU (S3C2440A) and the RS-422 machine-bus transceivers —
shifts data in/out of the isolated bus on the CPU's behalf. The smallest of
the three FPGAs on these boards, consistent with doing less real-time
computation than the Main or Racking boards' FPGAs (the CPU absorbs the
soft-real-time work here).

## Lifecycle / sourcing status

**Obsolete**, confirmed across TN100/TN144/B256/FT256 package variants by
multiple distributor listings (Suntsu, IBS Electronics). Some new-old-stock
still circulates through Dasenic and LCSC-adjacent brokers, but there is no
current manufacturer production.

## What this means for a replacement design

Same situation as the Main Board's LFXP3C — no drop-in replacement exists.
A modern equivalent (MachXO2 or MachXO3) would need the design re-synthesized,
not swapped in.

## Configuration / JTAG notes

- `JTAG` header present on this board per photos, with surrounding silkscreen.
- Security architecture is the same family philosophy as the LFXP3C: "no
  bitstream to intercept" is Lattice's own description — readback via JTAG
  is the only route in, and can be permanently fused off.

## Confidence caveats

Family, LUT count, and instant-on/sleep behavior are well corroborated across
multiple independent passes. As with the other two FPGAs, **the actual pin
function assignments on this specific board are not schematic-confirmed** —
they are inferred from what's silkscreened and from adjacency to the RS-422
transceivers and CPU bus. Continuity tracing is the only way to convert this
from "plausible role" to "confirmed wiring."

## Sources

- Lattice Semiconductor: MachXO Family Data Sheet (DS1002)
- Distributor obsolescence listings: Suntsu, Dasenic, IBS Electronics,
  Octopart
- Cross-verified across 7 independent research passes (this document + 6
  other AI sessions in `Other_AI_Research/`)

# TI SN74LVC07A

**Board:** Main Board, Racking Board (`U3`, `U18` on Racking)
**Identification confidence:** `[P]` Confirmed by photograph

---

## What it is

A hex buffer/driver with **open-drain** outputs. The open-drain output stage
is the key design feature — it specifically enables safe level-shifting to a
*higher* rail via an external pull-up resistor, which is exactly why this
part appears at the 3.3 V→5 V level-shift points on both boards.

## Key specs

| Parameter | Value |
|---|---|
| Function | Hex buffer, open-drain outputs |
| Supply | 1.65–5.5 V |
| Input tolerance | Accepts input voltages up to 5.5 V regardless of VCC |
| Sink current | Up to 24 mA max |
| Propagation delay | ~3.6 ns typical at 5 V |
| Latch-up | Exceeds 250 mA per JESD 17 |
| Package | SOIC-14 |

## Note on package variant lifecycle

One research pass flagged that the **PWLE (TSSOP) package variant** of this
part specifically shows as obsolete on some distributor listings, with a
recommended pin-compatible replacement — check the exact package/suffix
fitted on these boards (SOIC, per the photos, not TSSOP) before assuming
sourcing status. The LVC family generally remains active.

## Board role

3.3 V to 5 V level shifting via open-drain output + external pull-up, at
points where FPGA logic needs to drive a higher-voltage field signal.

## Lifecycle / sourcing status

Active for the SOIC package variant used on these boards; check the exact
package/suffix before re-ordering.

## Sources

- Texas Instruments: SN74LVC07A datasheet (Rev. W, Nov 2015)
- Digikey: package-variant lifecycle cross-check (PWLE/TSSOP obsolescence
  flag)
- Cross-verified across 7 independent research passes (this document + 6
  other AI sessions in `Other_AI_Research/`)

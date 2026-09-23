# TI AM26LV31 (AM26LV31C)

**Board:** HMI Board ×3, Main Board ×1
**Identification confidence:** `[P]` Confirmed by photograph

---

## What it is

A quad differential line driver for RS-422-style signaling, built on a
low-voltage BiCMOS process so it runs from a 3.3 V supply rather than the
original 1980s-era 5 V spec. Pairs with the AM26LV32 receiver (see
`06-ti-am26lv32/`).

## Key specs

| Parameter | Value |
|---|---|
| Standard | TIA/EIA-422-B / ITU Recommendation V.11 compatible (reduced supply-voltage variant) |
| Supply | Single 3.3 V |
| Max switching rate | 32 MHz |
| Propagation delay | ~8 ns typical |
| Pulse skew | ~500 ps typical |
| Output drive | ±30 mA, high-current for twisted-pair transmission lines |
| Output state, power-off | High-impedance (safe to leave unpowered on a live bus) |
| Input tolerance | Accepts 5 V logic inputs even on a 3.3 V supply |
| Enable | Common enable across all 4 channels |
| Package | 16-pin SOIC (9.9 mm × 6 mm) |

## Pinout (16-pin SOIC)

| Pin | Signal | Pin | Signal |
|---|---|---|---|
| 1 | 1A (input) | 9 | 3A (input) |
| 2 | 1Y (output) | 10 | 3Y (output) |
| 3 | 1Z (output) | 11 | 3Z (output) |
| 4 | G (enable) | 12 | G (enable) |
| 5 | 2Z (output) | 13 | 4Z (output) |
| 6 | 2Y (output) | 14 | 4Y (output) |
| 7 | 2A (input) | 15 | 4A (input) |
| 8 | GND | 16 | VCC |

Each channel: one single-ended input (`nA`), two differential outputs
(`nY`/`nZ`). Two enable pins (4 and 12) both need to be active for outputs to
drive.

**Validation note on pins 8 and 16:** the signal pins (1–7, 9–15) were
directly quoted from a search result citing TI's own pinout diagram — high
confidence. **Pins 8 (GND) and 16 (VCC) were not explicitly stated in that
source** and are filled in here by process of elimination (16 total pins − 14
signal/enable pins = 2 power pins, placed at the conventional diagonal
corners for a part in this package class). This is a reasonable inference,
not a directly sourced fact — worth a two-second continuity check against
the board before treating it as gospel.

## Board role

Converts 3.3 V single-ended FPGA logic into RS-422 differential pairs for the
isolated machine bus — the outbound half of the link, paired with a 6N137
optocoupler for galvanic isolation before the pair leaves the board.

## Lifecycle / sourcing status

**Mixed by exact suffix.** TI's current product page shows this family active
for some packages, but the specific `AM26LV31CNSLE` variant is marked
obsolete with a recommended replacement (`AM26LV31CNSR`). TI's own e2e support
forum (2023 threads) recommends the newer "E"-suffix parts (AM26LV31E /
AM26LV32E, improved ESD rating) as the go-forward choice for new designs.
**Check the exact suffix on this board before assuming interchangeability.**

## What this means for a replacement design

A pin-compatible modern equivalent almost certainly exists (the E-suffix
parts share the SOIC-16 footprint and pinout), but don't order a generic
"AM26LV31" without checking the specific ordering code against TI's current
recommendation table.

## Confidence caveats

Electrical specs (32 MHz, 3.3 V, drive current) are consistent across every
research pass and directly from TI's datasheet — solid. The obsolescence
finding for the specific `NSLE` suffix came from one research pass
(Claude/prior session) via distributor lifecycle listings; this project has
not independently confirmed which exact suffix is populated on this board
(the photographed marking includes lot-code text, not necessarily the full
ordering suffix).

## Sources

- Texas Instruments: AM26LV31 datasheet (SLLS201 family), TI product page,
  TI e2e support forum (AM26LV31/DS26LV31 replacement recommendation threads)
- Cross-verified across 7 independent research passes (this document + 6
  other AI sessions in `Other_AI_Research/`)

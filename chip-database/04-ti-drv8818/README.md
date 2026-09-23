# TI DRV8818

**Board:** Main Board, ×6 (designators `U4` `U5` `U6` `U15` `U17` `U18` `U19`
region — seven sites visible, six populated)
**Identification confidence:** `[I]` **Inferred, not confirmed** — the laser
marking has never been legible in any photo taken so far. Everything below
assumes this identification is correct.

---

## What it is

A bipolar stepper-motor driver IC with a built-in microstep indexer — the
chip does its own current chopping and microstepping internally, so the
controlling FPGA only needs to supply a STEP pulse train and a DIR level.

## Key specs

| Parameter | Value |
|---|---|
| Motor supply range | 8–35 V (comfortably covers this system's 27 V rail) |
| Output current | Up to 2.5 A/winding, chopped (not continuous free-air rating) |
| Microstep resolution | Full, 1/2, 1/4, 1/8 step (`USM1`/`USM0` mode pins: `00`=full, `01`=half, `10`=quarter, `11`=eighth) |
| Control interface | STEP/DIR — indexer built in |
| Current-set formula | **`IFS = VREF / (8 × RSENSE)`** — TI's own datasheet example: `VREF=1.56V`, `RSENSE=0.1Ω` → `IFS=1.95A` |
| RDS(on) | ~0.37 Ω total (HS+LS combined), ~0.22 Ω HS + 0.15 Ω LS typical at 25°C |
| Decay modes | Fast / slow / mixed, selected by `DECAY` pin voltage (<0.21·VCC = fast, >0.6·VCC = slow, between = mixed) |
| Blanking / off-time | ~1.25 µs blanking; ~44 µs tOFF with 47 kΩ/1000 pF on the RC pin |
| Protection | UVLO (VM 6.7–7.5 V), OCP (3.5 A trip, 1.5 µs deglitch, 800 µs retry), TSD (150–160°C) |
| Package | HTSSOP-28 with PowerPAD (exposed thermal pad), 9.7 × 4.4 mm |
| Pin-to-pin lineage | Documented upgrade path from the older DRV8811 (lower RDS(on)) |

## Worked example for this board

Sense resistors observed on the board read **`R25`**, interpreted as
**0.25 Ω** direct-value marking. If correct:

```
IFS = VREF / (8 × 0.25) = VREF / 2
```

So VREF = 1.0 V → 0.5 A/phase; VREF = 1.25 V → 0.625 A/phase, etc. VREF on
this part is normally supplied by a DAC or trimpot — **this is the direct
functional link to the TLC5620 quad DAC** sitting nearby on the same board
(see `10-ti-tlc5620/`). The DAC's most likely job is generating VREF for these
six drivers.

## Strong corroborating evidence for the identification

TI's own datasheet (last revised as recently as **February 2025** at time of
research, so this is a chip TI still actively documents) explicitly lists
**"Textile Machinery"** as a named target application, alongside printers,
scanners, and factory automation. A stepper driver TI specifically markets for
textile machines, found in a Jacquard/flat-knitting machine controller, is a
meaningful independent signal that the identification guess is on the right
track — though it remains unconfirmed until a marking is actually read.

## Board verification checklist (to move this from `[I]` to `[P]`)

- [ ] Read the package marking under magnification / oblique light
- [ ] Identify the two motor-output pins per channel
- [ ] Confirm the two low-value current-sense resistors per channel
- [ ] Trace VREF back to its source (DAC channel? trimpot?)
- [ ] Identify FAULT, RESET, SLEEP, ENABLE, STEP, DIR nets
- [ ] Compare the exposed thermal pad pattern against the datasheet package
      drawing

## Lifecycle / sourcing status

**Active / current production.** Available new from TI, Mouser, Digikey.

## What this means for a replacement design

Because the chip handles microstepping internally, **a replacement only needs
to generate a STEP pulse train and a DIR level per axis** — a small,
well-understood piece of logic. This significantly de-risks the six-stepper
subsystem of any replacement controller; it does not require reverse
engineering any microstep waveform generation.

## Confidence caveats

Every electrical fact above (formula, decay modes, protection thresholds) is
directly from TI's datasheet and is not in dispute across any research pass.
**The only open question is whether this chip is actually installed here** —
that identification rests entirely on package outline (HTSSOP-28 with a large
thermal pad) and sense-resistor topology (two resistors per channel, matching
a dual H-bridge current-sense scheme), not on a read marking. Treat every
number in this file as conditional on that identification until it's
physically confirmed.

## Sources

- Texas Instruments: DRV8818 datasheet (SLVSAX9, multiple revisions through
  Feb 2025), DRV8818A datasheet, TI product page listing "Textile Machinery"
  as an application
- Cross-verified across 7 independent research passes (this document + 6
  other AI sessions in `Other_AI_Research/`)

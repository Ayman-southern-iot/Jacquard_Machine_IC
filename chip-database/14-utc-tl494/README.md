# UTC TL494

**Board:** HMI Board — PWM switching controller
**Identification confidence:** `[P]` Confirmed by photograph

---

## What it is

**Not a genuine Texas Instruments part** despite sharing the famous TL494
name — "UTC" is **Unisonic Technologies Co.** (Taiwan), and their TL494 is a
clone/second-source of TI's original TL494 architecture: same pinout, same
internal block diagram. This is one of the most widely cloned PWM controller
designs in the industry (also second-sourced by ON Semiconductor,
STMicroelectronics, and others).

## Key specs

| Parameter | Value |
|---|---|
| Function | Classic fixed-frequency voltage-mode PWM controller for switching supplies |
| Internal blocks | 2× error amplifiers, adjustable oscillator, dead-time control comparator, pulse-steering flip-flop, 5V ±5% reference (10 mA), output-control logic, 2× output transistors |
| Oscillator range | ~1 kHz – 300 kHz, set by external `RT`/`CT`: `fOSC ≈ 1.1/(RT×CT)` |
| Dead-time control | Built-in ~3–5% minimum, adjustable to 100% via the DTC pin (0–3.3V external control) |
| Output mode | Single-ended or push-pull (selectable via output-control pin) |
| Output current | 200 mA per output, open-collector |
| Supply | 7–40 V (42 V absolute max) |
| Error amp bandwidth | ~800 kHz unity-gain |
| Error amp CMRR | ~80 dB |
| Package | 16-pin DIP or SOP |

## Board role (inferred)

Almost certainly drives the CCFL backlight inverter's switching transformer —
the FU120N MOSFET (`17-fu120n/`) and AO4606 dual-MOSFET (`16-ao4606/`) sit
nearby, and the board carries a `DANGER HIGH VOLTAGE` silkscreen warning
consistent with a transformer-based DC-AC inverter for a fluorescent backlight
tube.

## Lifecycle / sourcing status

**Active, cheap, high-volume production.** DIP-16 and SOP-16 packages widely
available through Chinese distribution (LCSC-adjacent channels). This is one
of the easiest and cheapest chips in this entire inventory to re-source or
substitute — genuine interchangeable clones exist from multiple
manufacturers.

## What this means for a replacement design

Zero sourcing risk. If a replacement backlight/PWM circuit is needed, any
genuine TL494-family part (TI, ON Semi, ST, or a Chinese clone) will work
identically at the architecture level — this is a mature, extremely
well-understood reference design.

## Confidence caveats

Internal architecture and pin function are well documented and consistent
across every research pass — the TL494 architecture itself is one of the
most thoroughly reverse-engineered analog ICs in existence, so confidence
here is high by any measure. The specific claim that it drives the CCFL
inverter (rather than some other DC-DC function on the board) is inferred
from proximity and the high-voltage warning silkscreen, not confirmed by
tracing its output pins to the transformer primary.

## Sources

- UTC (Unisonic Technologies): TL494 datasheet
- Texas Instruments: original TL494 datasheet (SLVS074, architecture
  cross-check)
- ON Semiconductor: TL494 datasheet (further cross-check of clone-consistency)
- Cross-verified across 7 independent research passes (this document + 6
  other AI sessions in `Other_AI_Research/`)

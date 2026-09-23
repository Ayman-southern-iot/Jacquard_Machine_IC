# TI TLC5620 (TLC5620C)

**Board:** Main Board, designator `U_D5`
**Identification confidence:** `[P]` Confirmed by photograph

---

## What it is

A quad 8-bit voltage-output DAC with a simple 3-wire serial interface —
almost certainly the chip that sets the current reference for the six
DRV8818 stepper drivers on this board (see `04-ti-drv8818/`).

## Key specs

| Parameter | Value |
|---|---|
| Resolution | 8-bit, 4 independent channels |
| Interface | 3-wire serial (DATA, CLK, LOAD/LDAC), CMOS-compatible |
| Command word | **11 bits total**: 8 data bits + 2 DAC-select bits + 1 range bit |
| Range bit | Selects ×1 or ×2 output multiplier against the reference |
| Data clocking | Shifted in on the **falling edge** of CLK, MSB first |
| Max clock rate | ~1 MHz |
| Update mechanism | **Double-buffered** — `LOAD` clocks data into the input register; `LDAC` (separate pin) then latches all 4 DAC outputs **simultaneously** |
| Output formula | `VO = REF × (CODE/256) × (1 + RNG)` |
| Settling time | 10 µs to within ±0.5 LSB |
| Supply | 5 V single supply |
| Reference input | Buffered, high-impedance |
| Power-on behavior | Resets to code 0 automatically |
| Package | 14-pin SOIC or 14-pin PDIP |
| Linearity | ±1 LSB INL |

## Pinout (14-pin, SOIC/PDIP)

Exact pin *numbers* vary slightly by exact datasheet revision, but the
functional pin set is: `DATA` (serial data in), `CLK` (serial clock),
`LOAD` (shifts data into the register), `LDAC` (simultaneous output latch),
`REF A`–`REF D` (four reference inputs, or one shared reference depending on
board wiring), `OUT A`–`OUT D` (four analog outputs), plus `VDD`/`GND`.

## Why this design detail matters

The 3-wire serial interface with an 11-bit command word is structurally
**the same shape** as the `xI`/`xO`/`xK` clocked-shift-register convention
found everywhere else on these boards (the machine bus signal naming). This
DAC uses the same kind of protocol as the bus itself, just locally rather
than over the isolated link.

The **double-buffered simultaneous update via `LDAC`** is functionally
important: it means all 4 (or 6, if time-multiplexed across the six stepper
channels via an analog mux) current references can update atomically —
avoiding a moment where one stepper phase sees a stale current setting while
another updates. This is good practice for balanced multi-axis motion and a
detail worth preserving in any replacement design.

## Board role (inferred)

Almost certainly generates `VREF` for some or all of the six DRV8818 stepper
drivers. With only 4 channels and 6 drivers, either an external analog mux
selects which driver each channel feeds, or two of the six drivers share a
fixed reference/trimpot instead of a DAC channel. This detail is not yet
confirmed by continuity tracing.

## Lifecycle / sourcing status

**Active.** TI's own product page confirms Active status, though TI suggests
a newer part (DAC43204) as the modern alternative for new designs. Still sold
new — LCSC stocks it under part number C36879, SOIC-14 package.

## What this means for a replacement design

Easy to re-source as-is if keeping the original design; if migrating away
from it, DAC43204 is TI's suggested modern equivalent (different interface —
would need re-verification of the serial protocol against a replacement
FPGA/MCU).

## Confidence caveats

The 11-bit command word structure and double-buffered LDAC mechanism are
confirmed directly from TI's datasheet and agree across every research pass.
The specific claim that this DAC sets stepper VREF (rather than some other
analog setpoint, e.g. take-down tension) is a strong but still-inferred
hypothesis based on board layout proximity to the stepper section — not
confirmed by tracing the DAC's output pins to a specific DRV8818's VREF pin.

## Sources

- Texas Instruments: TLC5620 datasheet (SLAS081, multiple revisions through
  Nov 2001), TI product page
- LCSC: current stock confirmation (part C36879)
- Cross-verified across 7 independent research passes (this document + 6
  other AI sessions in `Other_AI_Research/`)

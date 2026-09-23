# Everlight 6N137 / EL6N137

**Board:** HMI Board ×2, Racking Board ×8
**Identification confidence:** `[P]` Confirmed by photograph, lot `1119T1`
(HMI); marked `EL-6N137` (Racking)

---

## What it is

A high-speed **logic-gate** optocoupler — meaningfully different from a
plain phototransistor coupler (see `09-cosmo-357nt/` for the contrast). This
part has an internal IR LED optically coupled to an integrated photodetector
**with a logic gate stage built in**, giving a clean digital output edge
without needing an external comparator.

## Key specs

| Parameter | Value |
|---|---|
| Speed | **10 Mbit/s** guaranteed minimum |
| Isolation voltage | ~5,000 Vrms (some grades/variants up to higher) |
| Common-mode transient immunity | 10 kV/µs minimum (some grades 15 kV/µs) |
| Propagation delay | ~25–75 ns depending on exact variant/grade |
| Internal structure | IR LED + NMOS output transistor with leakage protection + logic gate; open-collector output, active-low typical |
| Strobe/enable | A strobable enable pin is present on some variants |
| Input threshold | ~5 mA LED drive current typical |
| Output sink current | ~13 mA minimum |
| Supply | 4.5–5.5 V |
| Operating temperature | −40°C to +85°C |
| Package | DIP-8 or SOIC-8 |

## Pinout (DIP-8 / SOIC-8)

| Pin | Signal |
|---|---|
| 1 | Anode (LED) |
| 2 | Cathode (LED) |
| 3 | NC |
| 4 | NC |
| 5 | GND |
| 6 | VO (output) |
| 7 | VE (enable/strobe) |
| 8 | VCC |

Needs a 0.1 µF bypass capacitor between pins 5 and 8, plus an external
pull-up resistor (330 Ω–4 kΩ range) on the output.

## Board role

The isolation stage on the machine bus — every RS-422 signal crossing the
board's galvanic isolation boundary passes through one of these. On the
Racking Board, 8 of them isolate the entire bus interface plus servo signal
returns; on the HMI Board, 2 of them isolate the bus master's connection out.

## Why this sets the bus-clock ceiling

This part's 10 Mbit/s rating is not a rough estimate — it is the guaranteed
datasheet minimum. **Wherever a 6N137 sits in a signal path, that path cannot
exceed 10 Mbit/s**, regardless of what the RS-422 transceivers on either side
of it are individually capable of (they're rated to 32 MHz — see
`05-ti-am26lv31/` and `06-ti-am26lv32/`). The optocoupler, not the
transceiver, is always the true bottleneck wherever one is present.

## Lifecycle / sourcing status

**Active, high-volume production.** Confirmed in stock at LCSC (tens of
thousands of units, roughly $0.13–0.56 depending on package variant — DIP-8,
SOP-8, SOP-8-2.54mm all available). Octopart lists this part "Production"
status with authorized distribution through at least 6 channels. **One of the
easiest and lowest-risk parts on either board to re-source.**

## What this means for a replacement design

No sourcing concern at all — this part will remain available for the
foreseeable future from multiple manufacturers (Everlight, Vishay, and
others all make compatible 6N137-class parts).

## Confidence caveats

Speed, isolation rating, and internal structure are solidly confirmed and
consistent across every research pass — this is a well-documented, widely
second-sourced industry-standard part. Minor variation between sources on
exact propagation delay and CMTI numbers reflects genuine differences between
manufacturer grades (Everlight vs. Vishay vs. Fairchild versions of "6N137"
are not bit-for-bit identical parts, just pin/function compatible).

## Sources

- Everlight Electronics: 6N137/EL6N137 datasheet
- Vishay: 6N137 datasheet (cross-reference for CMTI/isolation specs)
- LCSC: current stock and pricing confirmation
- Octopart: lifecycle status and distribution channel count
- Cross-verified across 7 independent research passes (this document + 6
  other AI sessions in `Other_AI_Research/`)

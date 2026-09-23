# ULN2804A

**Board:** Main Board, 2× DIP-18 (`U_D4`, `U_D6`)
**Identification confidence:** `[P]` Confirmed by photograph, both instances
read in situ

---

## What it is

An octal high-voltage, high-current Darlington transistor array — 8 NPN
Darlington pairs, common emitters, open-collector outputs. One of the most
widely second-sourced parts in the entire electronics industry (originally a
Sprague/Allegro/TI part, now made by ST, ON Semi, Toshiba, and many others).

## Key specs

| Parameter | Value |
|---|---|
| Channels | 8, common emitter (`COM` pin) |
| Off-state voltage | 50 V max per channel |
| Continuous current | 500 mA/channel (peak/surge rating ~600 mA) |
| Input | TTL/CMOS compatible; **the 2804 variant specifically has a ~10.5 kΩ input resistor**, suited for 6–15 V CMOS logic families (contrast with ULN2803, which has a lower input resistor for TTL) |
| Protection | Integral flyback/clamp diodes to `COM` — essential for driving inductive loads (relay coils, small solenoids) without external diodes |
| Logic sense | Non-inverting (ULN2804 is non-inverting; ULN2803 is the inverting sibling — easy to confuse) |
| Package | DIP-18 (also available SOIC-18) |

## Board role

16 total output channels across the two chips — driving valves, small
solenoids, or indicator lamps. The built-in clamp diodes make this an ideal
low-side driver for anything inductive without extra external protection
parts.

## Lifecycle / sourcing status

**Active, full production.** ST's own datasheet describes it as "information
on a product in full production" (most recent revision seen: June 2018).
Universal second-sourcing means this part will remain available indefinitely.

## What this means for a replacement design

Zero sourcing risk. Any manufacturer's ULN2804A is a safe substitute for any
other.

## Sources

- STMicroelectronics: ULN2804A datasheet (rev. June 2018)
- Cross-verified across 7 independent research passes (this document + 6
  other AI sessions in `Other_AI_Research/`)

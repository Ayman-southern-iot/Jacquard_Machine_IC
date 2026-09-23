# LM324

**Board:** Main Board — quad op-amp
**Identification confidence:** `[P]` Confirmed by photograph

---

## What it is

A classic quad general-purpose operational amplifier — one of the most
ubiquitous analog ICs ever made, produced by essentially every analog IC
manufacturer on Earth (TI, ON Semi, ST, Diodes Inc., and many others).

## Key specs

| Parameter | Value |
|---|---|
| Channels | 4 independent op-amps, internally frequency-compensated |
| Supply range | 3–32 V single supply, or ±1.5 to ±16 V dual supply |
| Supply current | ~0.8 mA typical, largely independent of supply voltage |
| Input bias current | ~45 nA |
| Input offset voltage | ~2 mV |
| Unity-gain bandwidth | ~1 MHz |
| Input common-mode | Includes ground — key trait enabling 0 V sensing on a single-supply rail |
| Package | SOIC-14 or DIP-14 |

## Board role

Sensor signal conditioning — buffering and scaling analog sensor signals
before they reach a comparator stage (LM339, see `21-lm339/`) for
threshold/limit detection. Ground-sensing input capability is exactly what's
needed for conditioning 0–24 V field sensor signals from a single rail.

## Lifecycle / sourcing status

No sourcing risk whatsoever — will remain in production for decades, made
by dozens of manufacturers.

## Sources

- Texas Instruments and general industry datasheets (LM324 is a
  textbook-standard part; specs consistent across every manufacturer)
- Cross-verified across 7 independent research passes (this document + 6
  other AI sessions in `Other_AI_Research/`)

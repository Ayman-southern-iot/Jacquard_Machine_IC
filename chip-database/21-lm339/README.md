# LM339

**Board:** Main Board, designator `U_Z2` — quad comparator
**Identification confidence:** `[P]` Confirmed by photograph

---

## What it is

A quad differential voltage comparator with open-collector outputs — the
comparator-family counterpart to the LM324 op-amp family.

## Key specs

| Parameter | Value |
|---|---|
| Channels | 4 independent comparators |
| Supply range | 2–36 V single supply |
| Input offset voltage | ~2 mV max (LM339B variant improves this to ~0.37 mV) |
| Supply current | ~200 µA per comparator |
| Response time | ~1.3 µs with 5 mV overdrive |
| Output | Open-collector — needs an external pull-up resistor |
| Package | SOIC-14 or DIP-14 |

## Board role

Limit/threshold detection — takes a conditioned analog signal (typically
from an LM324 stage) and converts it to a clean logic level against a
reference threshold. Sits inside the `S_IN`/`S_OUT`/`S_REF` silkscreen group
per board photos.

## Lifecycle / sourcing status

No sourcing risk — universally second-sourced comparator family.

## Sources

- Standard industry datasheets (textbook part)
- Cross-verified across 7 independent research passes (this document + 6
  other AI sessions in `Other_AI_Research/`)

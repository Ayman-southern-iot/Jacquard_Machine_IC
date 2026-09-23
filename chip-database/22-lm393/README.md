# LM393

**Board:** HMI Board — dual comparator
**Identification confidence:** `[P]` Confirmed by photograph

---

## What it is

A dual differential comparator — essentially half of an LM339
(`21-lm339/`) in a smaller package.

## Key specs

| Parameter | Value |
|---|---|
| Channels | 2 independent comparators |
| Supply range | 2–36 V |
| Input offset voltage | ~2.0 mV max |
| Supply current | ~0.45 mA total |
| Output | Open-collector, needs external pull-up |
| Package | SOIC-8 or DIP-8 |

## Board role

Threshold/limit detection on the HMI board, same functional family as the
LM339 on the Main Board.

## Lifecycle / sourcing status

No sourcing risk — universally second-sourced.

## Sources

- Standard industry datasheets (textbook part)
- Cross-verified across 7 independent research passes (this document + 6
  other AI sessions in `Other_AI_Research/`)

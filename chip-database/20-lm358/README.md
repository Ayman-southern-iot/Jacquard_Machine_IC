# LM358

**Board:** Main Board ×2 — dual op-amp
**Identification confidence:** `[P]` Confirmed by photograph, both instances

---

## What it is

A dual general-purpose operational amplifier — essentially half of an
LM324 (`19-lm324/`) in a smaller package. Same family characteristics, same
universal second-sourcing.

## Key specs

| Parameter | Value |
|---|---|
| Channels | 2 independent op-amps |
| Supply range | 3–32 V single supply (same family as LM324) |
| Supply current | ~500 µA typical |
| Unity-gain bandwidth | ~1 MHz |
| Package | SOIC-8 or DIP-8 |

## Board role

Same functional family as LM324 — sensor conditioning, but used where only 2
channels are needed rather than 4.

## Lifecycle / sourcing status

No sourcing risk — universally second-sourced, made by every major analog
IC manufacturer.

## Sources

- Standard industry datasheets (textbook part, consistent across
  manufacturers)
- Cross-verified across 7 independent research passes (this document + 6
  other AI sessions in `Other_AI_Research/`)

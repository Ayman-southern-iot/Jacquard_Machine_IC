# AMS1117

**Board:** HMI Board ×2 (`U21`, `U22`) — secondary LDO regulators
**Identification confidence:** `[P]` Confirmed by photograph. **Output-voltage
variant not readable from the marking** — this remains open.

---

## What it is

A ubiquitous 1 A linear low-dropout regulator. Worth noting: the original
AMS (Advanced Monolithic Systems) die source is murky/legacy — nearly all
AMS1117-marked parts on the market today are made by dozens of Chinese fabs
as "compatible" clones rather than genuine original-source silicon. This is
normal and not a quality concern in itself, just worth knowing.

## Key specs

| Parameter | Value |
|---|---|
| Output current | 1 A max |
| Dropout voltage | ~1.1–1.3 V at 800 mA–1 A load |
| Output accuracy | ±2% typical |
| Fixed variants | 1.2 V, 1.8 V, 2.5 V, 3.3 V, 5.0 V |
| Adjustable variant | Also available |
| Max input voltage | ~15 V |
| Package | SOT-223 (this board) |

## Board role

Secondary regulation on the HMI board, downstream of the main SP29302 LDO
(`13-sipex-sp29302/`).

## Open item

The specific output-voltage variant of the two AMS1117 chips on this board
has **not been read from the marking** — this is a "read-the-marking"
problem, not a datasheet-lookup problem (identifying which fixed-voltage
version is fitted requires a clearer close-up photo).

## Lifecycle / sourcing status

Zero sourcing risk — sub-$0.05 in volume, made by dozens of manufacturers,
will remain available indefinitely.

## Sources

- Standard industry datasheets (AMS1117 family; specs consistent across
  clone manufacturers)
- Cross-verified across 7 independent research passes (this document + 6
  other AI sessions in `Other_AI_Research/`)

# Sipex SP29302 (SPX29302)

**Board:** HMI Board, designator `U20` — main regulator
**Identification confidence:** `[P]` Confirmed by photograph. Filename in the
project's images reads "sidex"; the actual package logo is **Sipex**.

---

## What it is

A 3 A adjustable low-dropout linear regulator. This is the HMI board's main
power regulator, feeding the board's local logic rails from whatever incoming
DC voltage arrives at `J15`.

## Key specs

| Parameter | Value |
|---|---|
| Type | Adjustable LDO, 5-pin |
| Output current | 3.0 A full-load |
| Dropout voltage | ~450–600 mV typical at 3 A depending on exact datasheet revision (some cite 370 mV, others 450 mV, others 600 mV — see caveats below) |
| Adjustable range | Down to 1.25 V |
| Reference | VREF 1.24 V |
| Accuracy | ±1% (on-chip trimmed) |
| Package | TO-263-5 (D²PAK) or TO-220-5, this board uses TO-263-5 |
| Thermal resistance | ~31.2°C/W junction-to-ambient (TO-263-5) |
| Protection | Over-current, reverse-battery, thermal shutdown, transient protection |
| Ground current | ~30–40 mA at load |

## Corporate lineage (matters for sourcing)

Sipex Corporation was acquired by **Exar** in 2007. Exar's own datasheet for
the identical part exists under the same "SPX29302" naming. Exar was later
acquired by **MaxLinear** in 2017. So the current authoritative datasheet
source is MaxLinear, even though the board itself carries the original
Sipex-branded package.

## Quantified failure mode — why input voltage matters so much

This is a **linear** regulator — every volt of input-to-output difference
becomes heat dissipated in the package, not efficiency loss elsewhere.

```
P_DISS ≈ (VIN − VOUT) × ILOAD
```

Two worked examples using this board's actual numbers:

- **Correct input (~12 V) → 3.3 V output, ~3 A load:**
  `(12 − 3.3) × 3 ≈ 26 W`
- **Wrong input (24 V, if someone assumed the board's `+24V` rail applies
  directly) → same 3.3 V output, 3 A load:**
  `(24 − 3.3) × 3 ≈ 62 W`

62 W is well beyond what a TO-263-5 package can dissipate without a large
external heatsink — the part would thermally shut down or fail rapidly.
**This is exactly why determining the HMI board's actual `J15` input voltage
by tracing (rather than assuming) matters before any powered bench work.**

## Lifecycle / sourcing status

Shows up as **discontinued/limited-stock** at some distributors — one
listing (Farnell/Samm Turkey) showed "out of stock." Worth checking
MaxLinear's current LDO lineup for a modern equivalent before assuming fresh
stock of this exact part is available.

## What this means for a replacement design

If this regulator needs replacing, check MaxLinear's current catalog first
rather than searching for "Sipex SP29302" directly — the corporate lineage
means the modern part may carry a different model number entirely while
being electrically equivalent.

## Confidence caveats

Dropout voltage specifically varies across sources (370 mV, 450 mV, 600 mV
have all been cited) — this likely reflects genuine differences between
datasheet revisions (Sipex-era vs. Exar-era vs. MaxLinear-era documents for
what is nominally "the same part"), or different test conditions (load
current, junction temperature) rather than a real disagreement. Use the
`(VIN−VOUT)×ILOAD` formula with your own measured input voltage for any
safety-critical calculation rather than relying on a single cited dropout
number.

## Sources

- Sipex/Exar/MaxLinear: SPX29300/01/02/03 datasheet (multiple corporate-era
  revisions)
- MaxLinear.com: current official datasheet host
- Farnell/Samm Turkey: distributor stock status (discontinued/out-of-stock
  signal)
- Cross-verified across 7 independent research passes (this document + 6
  other AI sessions in `Other_AI_Research/`)

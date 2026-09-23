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

### Correction from senior-engineer validation pass — the "correct" scenario is not automatically safe either

The 26 W vs. 62 W comparison above correctly shows *relative* severity, but
taken on its own it can leave the impression that 26 W is a safe, tolerable
number. Running it through the package's own θJA (junction-to-ambient
thermal resistance, ~31.2°C/W for this TO-263-5 rating) shows it is not:

```
ΔTJ ≈ P_DISS × θJA
```

- At 26 W: `ΔTJ ≈ 26 × 31.2 ≈ 811°C` rise — physically impossible; the part
  would hit its ~150°C thermal-shutdown threshold almost immediately.
- Working backward, the **maximum sustainable dissipation** in free air
  (25°C ambient, 150°C junction limit) is only
  `(150 − 25) / 31.2 ≈ 4 W`.

**That 4 W ceiling assumes no heatsink and no extra PCB copper pour** — the
θJA figure is a bare-package, still-air rating. Two things follow:

1. **The board almost certainly does not draw 3 A of continuous load through
   this regulator** — 3 A was an illustrative worked-example number, not a
   measured board load. A more realistic HMI logic load (LCD controller,
   FPGA, small peripherals) is more likely in the 0.3–1 A range. At 1 A and a
   12 V input: `(12 − 3.3) × 1 ≈ 8.7 W` — still **more than double** the bare
   4 W free-air ceiling.
2. **This part therefore needs real heatsinking or substantial copper pour
   under the TO-263-5 tab to survive at *any* plausible input voltage**, not
   only the wrong-voltage failure case. The photographed board shows the
   part on "a large thermal land," which is consistent with the designer
   having already accounted for this — but it means the safety margin at the
   *correct* input voltage is genuinely tighter than the original 26 W vs.
   62 W framing suggested, not a comfortable margin.

**Practical implication:** measure the actual board load current before
assuming any input voltage is "safe" for this regulator — the load current
matters as much as the input voltage in this calculation, and the original
treatment held load current constant while varying only voltage.

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

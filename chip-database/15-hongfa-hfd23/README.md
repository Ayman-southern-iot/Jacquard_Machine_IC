# HongFa HFD23/005-1ZS

**Board:** Main Board — safety/enable relay
**Identification confidence:** `[P]` Confirmed by photograph

---

## What it is

A subminiature PCB-mount signal relay, manufactured by **Xiamen Hongfa
Electroacoustic Co.** The only electromechanical relay on the Main Board —
this is the machine's safety/enable interlock.

## Key specs

| Parameter | Value |
|---|---|
| Contact form | 1 Form C (SPDT) |
| Coil voltage | 5 VDC (the `005` in the part number) |
| Coil power | 150 mW (highly sensitive coil variant) |
| Coil resistance | ~125–167 Ω (sources vary slightly; ~30 mA coil current at 5V is the consistent implied figure) |
| Contact rating | 2 A switching; commonly cited as 1A/30VDC or 0.5A/125VAC per some datasheet tables, others cite 2A/125VAC or 2A/60VDC — **check the exact suffix table** |
| Contact material | AgNi, gold-plated on some variants |
| Operate time | ≤5 ms |
| Release time | ≤5 ms (some sources cite 3 ms — likely datasheet-revision or suffix variation) |
| Insulation resistance | 1,000 MΩ |
| Ambient range | −40°C to 70°C |
| Package | Sealed plastic, PCB mount, ~12.5 × 7.5 × 10 mm |
| Suffix meaning | `-1ZS` = sealed type (vs. `-1ZP` or `-1HS` for different sensitivity/contact variants) |

## Board role

Machine safety / enable interlock — likely tied to the `UPS-T` power-fail
signal or a similar orderly-shutdown/park function given its single-relay,
single-purpose placement on this board.

## Lifecycle / sourcing status

**Active, current production.** Confirmed via JLCPCB (part C399483). Note: a
related part number `C64002` on the same platform is separately marked "no
longer manufactured" — **check the exact suffix and date code before
ordering**, since HongFa's relay family has multiple similar-looking variants
with different lifecycle status. Priced roughly $0.55–1.10 depending on
quantity.

## What this means for a replacement design

Easy to re-source, but be precise about the suffix. `-1ZS` (sealed) vs.
`-1ZP` vs. `-1HS` are electrically/mechanically different variants (coil
sensitivity, sealing) even though they look nearly identical — match the
exact suffix rather than ordering the closest-looking part number.

## Confidence caveats

Contact rating numbers vary meaningfully between sources for this exact
part — this is a real point of disagreement across the research passes, not
just rounding. Likely explanation: HongFa's HFD23 series has multiple
sub-variants with genuinely different contact ratings, and different sources
pulled slightly different reference datasheets. **Before relying on a
specific current/voltage rating for a safety-relevant design decision, pull
the exact datasheet page for `HFD23/005-1ZS` specifically** rather than a
general HFD23-family summary.

## Sources

- HongFa (Xiamen Hongfa Electroacoustic): official HFD23 series datasheet
- JLCPCB parts library: current stock/lifecycle confirmation (C399483)
- Farnell, TME, Newark, element14, eeworld: distributor spec cross-checks
- Cross-verified across 7 independent research passes (this document + 6
  other AI sessions in `Other_AI_Research/`)

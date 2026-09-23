# FU120N (≡ IRFU120N family)

**Board:** HMI Board, designator `U_D7` — backlight switch MOSFET
**Identification confidence:** `[P]` Confirmed by photograph and marking —
identified this project (was a total unknown before this research effort)

---

## What it is

An N-channel power MOSFET. **Important naming resolution (new this
session):** "FU120N" and "IRFU120N" are **the same underlying device
family**, not two different chips:

- **IRFU120N** is International Rectifier's (now Infineon's) original part
  number — the "IR" prefix is the manufacturer code, "FU" indicates the
  TO-251/IPAK straight-lead through-hole package style, "120N" is the
  100V/N-channel class designation.
- **VBsemi Electronics** (a Chinese fab) produces a compatible/second-sourced
  version marked simply **"FU120N"** (without the "IR" manufacturer prefix),
  matching the IR/Infineon original's core electrical specs.

Both markings point to functionally equivalent devices. Related package
variant naming: **IRFR120N** is the surface-mount (SMD) version of the same
electrical part, for reference if a future photo shows that marking instead.

## Key specs

| Parameter | Value |
|---|---|
| Type | N-channel power MOSFET |
| VDS (breakdown) | **100 V** |
| ID (continuous) | ~9.4–12 A depending on exact source/manufacturer (VBsemi's own datasheet states 12A; IR/Infineon's original IRFU120N states 9.4A — likely reflects slightly different test conditions or manufacturer margin, not a real contradiction for practical design purposes) |
| RDS(on) @ VGS=10V | ~0.18–0.27 Ω across sources (VBsemi: 0.20Ω; IR original: ~0.21–0.27Ω) |
| VGS(th) | 2–4 V (sources vary) |
| Max gate charge (Qg) | 16 nC (VBsemi datasheet) |
| Package | TO-251 (IPAK) — through-hole, straight-lead |
| Power dissipation | Up to ~1.5 W typical in surface-mount-adjacent use, more with proper heatsinking of the TO-251 tab |
| Operating temp | Up to 175°C (VBsemi rating) |
| Halogen-free | Yes, per IEC 61249-2-21 (VBsemi's stated compliance) |

## Board role — confirmed by design logic

Sits between the DC power rail and connector `J3` (the CCFL backlight
inverter connection), gate driven by `Q2`/`Q3`/`Q4` (small-signal transistors,
SOT-23, marked `J3Y` = MMBT3904-class). Acting as the **backlight switch** —
the software brightness/blanking control for the CCFL tube. A 100V-rated
MOSFET switching a backlight inverter primary makes complete sense: CCFL
inverter transformers commonly see higher instantaneous voltages during
switching transients than the DC rail feeding them, and 100 V gives
comfortable margin over a typical 12–24 V input rail.

## Lifecycle / sourcing status

**Active, cheap, high stock.** VBsemi's version confirmed at LCSC, part
`C878805`, roughly $0.13/unit. VBsemi's catalog also lists related family
members — an "FR120N" and "FU9024N" — worth double-checking against a
closer photo if the board's marking is ever re-read, since these are
different parts with different current ratings and easy to misread from a
photo at distance.

## What this means for a replacement design

Low sourcing risk either way — buy genuine VBsemi FU120N (cheapest, highest
stock) or a genuine IR/Infineon IRFU120N if manufacturer traceability
matters more than cost. Either will perform equivalently in this backlight-
switch application.

## Confidence caveats

The IR/VBsemi cross-reference is corroborated by direct comparison of the
two manufacturers' datasheet specs (same VDS class, same package family,
same rough RDS(on) range) — this is a reasonable identification, but it's
worth noting the exact die may differ subtly between manufacturers even
where the marking convention overlaps. The minor numeric spread in ID and
RDS(on) between sources most likely reflects manufacturer-to-manufacturer
variation rather than measurement error.

## Sources

- VBsemi Electronics: official FU120N datasheet (via alldatasheet.com
  mirror), LCSC listing (C878805)
- International Rectifier / Infineon: IRFU120N datasheet (multiple
  alldatasheet mirrors)
- alltransistors.com: IRFU120N spec cross-reference and pinout
- Cross-verified across 7 independent research passes (this document + 6
  other AI sessions in `Other_AI_Research/`) — this specific IR/VBsemi
  naming resolution was confirmed via a dedicated search this session

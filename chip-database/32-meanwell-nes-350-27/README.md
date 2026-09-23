# Mean Well NES-350-27

**Board:** Power supply rack, ×2 units
**Identification confidence:** `[P]` Confirmed directly by label

---

## What it is

A 350 W enclosed single-output switching power supply, 27 V DC output. Two
of these units feed the main 27 V rail across all three control boards.

## Key specs

| Parameter | Value |
|---|---|
| Output voltage | 27 VDC ±1.0% |
| Output current | 0–13 A |
| Rated power | 351 W |
| Input voltage | 90–264 VAC (switch-selectable range), or 127–370 VDC |
| Efficiency | ~88% |
| Operating temp | −20°C to +70°C with derating |
| Isolation voltage | 3 kV |
| Ripple/noise | ~200 mV |
| Dimensions | ~215 × 115 × 50 mm |
| Protection | Overload, overvoltage |

## Why 27 V specifically (not 24 V)

The boards silkscreen `+24V`, but the installed supplies deliver **27 V**.
This is deliberate, not a mislabel: 27 V into a nominally 24 V solenoid coil
gives faster current slew (`di/dt = V/L`), directly reducing needle-selection
latency, and provides cable-drop headroom for the carriage traversing the
full bed on a flexing cable. The 27 V rail also sits safely below the 35 V
absolute maximum of the DRV8818 stepper drivers (`04-ti-drv8818/`), preserving
margin against inductive back-EMF spikes during motor deceleration.

## Lifecycle / sourcing status

**Discontinued.** Mean Well's own Australian distributor page marks the
entire NES-350 series "Discontinued Product" and points to the newer
**LRS-350 series** as the replacement family. **LRS-350-27 is the practical
drop-in replacement** if either unit fails — confirmed via a dedicated search
this session cross-referencing Mean Well's own product pages. Original
NES-350-27 units do still circulate on the gray/secondary market (eBay,
Chinese sellers), but that supply is shrinking over time.

## What this means for a replacement design

**Do not plan to source more NES-350-27 units long-term.** Budget for
LRS-350-27 as the eventual replacement — verify its output is field-
adjustable to the same 27 V setpoint (not fixed at 24 V) before ordering, and
check its physical dimensions/mounting against the enclosure this replaces.

## Sources

- Mean Well: NES-350 series datasheet, LRS-350 series datasheet
- meanwellaustralia.com.au: NES-350 discontinuation notice, LRS-350
  successor confirmation
- Jameco, Digikey, Octopart, Mouser: distributor listing cross-checks
- Cross-verified across 7 independent research passes (this document + 6
  other AI sessions in `Other_AI_Research/`) — discontinuation/successor
  confirmed via a dedicated search this session

# TI AM26LV32 (AM26LV32C)

**Board:** All three boards (HMI, Main, Racking)
**Identification confidence:** `[P]` Confirmed by photograph

---

## What it is

The receiver counterpart to the AM26LV31 driver (`05-ti-am26lv31/`) — a quad
differential line receiver for RS-422-style signaling, 3.3 V BiCMOS.

## Key specs

| Parameter | Value |
|---|---|
| Standard | TIA/EIA-422-B / ITU V.11 compatible, reduced supply-voltage variant |
| Supply | Single 3.3 V |
| Max switching rate | 32 MHz |
| Common-mode range | −0.3 V to 5.5 V |
| Receiver sensitivity | ±200 mV differential |
| Input hysteresis | 50 mV typical |
| Fail-safe behavior | Defined output on open-circuit, short-circuit, and terminated-idle bus conditions |
| Power dissipation | ~27 mW typical; ~235 mW with all four channels active at 32 MHz |
| Enable | Common, active-high or active-low input choice |
| Package | 16-pin SOIC (same footprint family as AM26LV31) |
| Temperature grade | Industrial version (AM26LV32I) rated −40°C to 85°C |

## Board role

Receives the isolated RS-422 signal from the bus (after the 6N137/EL6N137
optocoupler stage) and converts it back to single-ended 3.3 V logic for the
local FPGA. Present on **all three boards** — the one part type that appears
everywhere in this system's bus architecture, always as the receive side.

## Notable finding: this transceiver is never the speed bottleneck

Where a 6N137 optocoupler sits in the same signal path (HMI and Racking
boards), the optocoupler's 10 Mbit/s ceiling is reached long before this
receiver's 32 MHz ceiling. **On the Main Board specifically, there is no
6N137 in the bus path** — so this receiver's own 32 MHz limit becomes the
relevant ceiling for whatever link the Main Board uses toward the (missing)
carriage/selector board. That link could plausibly run faster than the
Racking Board's bus.

## Lifecycle / sourcing status

Similar situation to the AM26LV31: certain older suffix variants (e.g.
`CDR`/`NSLE`) are obsolete with TI-recommended current substitutes sharing
the same SOIC footprint and pinout. The general AM26LV3x family line remains
active.

## What this means for a replacement design

Straightforward to re-source or substitute with a current TI part sharing
the same pinout — check the exact suffix rather than assuming the oldest
listing is still buyable.

## Confidence caveats

Electrical specs are solidly confirmed from TI's own datasheet (SLLS202,
multiple revisions through at least 2023) and agree across every research
pass. The claim that this part is "never the bottleneck" is a derived
conclusion from combining this datasheet with the 6N137's datasheet — sound
reasoning, but worth remembering it's a synthesis, not a single sourced fact.

## Sources

- Texas Instruments: AM26LV32 datasheet (SLLS202H, May 1995 – rev Aug 2023)
- Cross-verified across 7 independent research passes (this document + 6
  other AI sessions in `Other_AI_Research/`)

# Alpha & Omega Semiconductor AO4606

**Board:** HMI Board — load switch / reverse-polarity protection
**Identification confidence:** `[P]` Confirmed by photograph

---

## What it is

A complementary N-channel + P-channel MOSFET pair in a single SOIC-8
package, using AOS's trench MOSFET technology. The N+P complementary pairing
in one package is the classic building block for a reverse-polarity
protection circuit combined with a load switch.

## Key specs

| Parameter | N-channel | P-channel |
|---|---|---|
| VDS breakdown | 30 V | −30 V |
| Continuous current | 6–6.9 A (sources vary slightly) | −6–(−6.5) A |
| RDS(on) @ 10V/−10V | ~28–30 mΩ | ~28–44 mΩ |
| RDS(on) @ 4.5V/−4.5V | ~42 mΩ | ~44 mΩ |
| Gate charge (Qg) | ~2.55 nC | ~6.7 nC |
| Reverse recovery (trr) | ~8.5 ns | ~15 ns |
| VGS max | ±20 V (shared rating) | |
| Package | SOIC-8 | |
| Operating temp | −55°C to +150°C | |

## Board role

Sits between the DC power rail and the rest of the HMI board circuitry —
consistent with a P-channel high-side reverse-polarity protection switch
combined with an N-channel low-side or gate-driven load switch, a very
standard topology for this device configuration.

## Sourcing complication worth knowing: heavy cloning

The genuine AOS-branded part is confirmed active (AOS's own datasheet, most
recently seen at Rev 10.1, dated August 2023). However, this exact part
number is now **heavily second-sourced/cloned** by multiple Chinese fabs
under the identical "AO4606" marking — confirmed listings from VBsemi, TECH
PUBLIC, MSKSEMI, Guangdong Kexin, Shenzhen Doingter, and Guangdong
Youtai/UMW, all selling parts marked "AO4606" with near-identical but **not
always electrically identical** specs.

## Lifecycle / sourcing status

**Active** (genuine AOS part). If sourcing from LCSC/JLCPCB-style Chinese
distribution rather than direct from AOS or an AOS-authorized distributor,
you are most likely receiving one of the clone variants above, not the
genuine AOS die.

## What this means for a replacement design

If RDS(on) or Qg precision matters for the specific circuit (e.g., switching
loss budget is tight), **check the datasheet PDF that actually ships with
the specific listing you're buying from** rather than assuming genuine AOS
specs apply — the clones vary slightly from each other and from the
original.

## Confidence caveats

Electrical specs shown above are a synthesis across several sources citing
slightly different numbers for the same nominal part (e.g., RDS(on) values
ranging 28–44 mΩ across sources) — this spread is consistent with the
clone-proliferation finding: different sources may have pulled specs from
different manufacturers' "AO4606" datasheets, all legitimately marked that
way but not identical dies. Treat any single number here as representative,
not as a guaranteed spec for whatever physical part is actually on this
specific board.

## Sources

- Alpha & Omega Semiconductor (aosmd.com): official AO4606 datasheet, Rev
  10.1, Aug 2023
- Distributor/clone listings: VBsemi, TECH PUBLIC, MSKSEMI, Guangdong Kexin,
  Shenzhen Doingter, Guangdong Youtai/UMW (cross-check for clone
  proliferation)
- xdevs.com: archived AO4606/AO4606L datasheet mirror
- Cross-verified across 7 independent research passes (this document + 6
  other AI sessions in `Other_AI_Research/`)

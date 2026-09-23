# Lattice LCMXO2-7000HC-4TG144C

**Board:** Racking Board — the node's entire control logic
**Identification confidence:** `[P]` Confirmed by photograph, lot `A6151R54`,
also transcribed in `IC Number.txt`

---

## What it is

A non-volatile FPGA from Lattice's **second-generation MachXO2 family** —
newer and larger than the Main and HMI boards' first-generation LatticeXP and
MachXO parts. Notably, this is the **only one of the three FPGAs still in
current production** (see Lifecycle below), which matters if this board's
control logic is preserved in a replacement design.

## Key specs

| Parameter | Value |
|---|---|
| Logic capacity | 6,864 logic elements (LUT-equivalent) — device name "7000" is Lattice's marketing/class number, not a literal LUT count |
| Embedded memory | ~240 Kbit sysMEM block RAM + ~54 Kbit distributed RAM, plus on-chip User Flash Memory (UFM) |
| Hardened blocks | **Embedded Function Block (EFB)** — hardened SPI controller, hardened I²C controller(s), timer/counter — saves 500+ LUTs vs. implementing the same in fabric |
| PLLs | 2 on-chip |
| Process | 65 nm |
| Package (this part) | TQFP-144, speed grade `-4` (one of the faster grades in the family) |
| I/O count | Up to ~115 user I/O in this package |
| Core voltage | HC = "High Capability" — internal regulator supporting 2.5/3.3 V external VCC |
| Config interfaces | JTAG, **Slave SPI**, I²C |
| Instant-on / dual-boot | Yes — non-volatile, TransFR live update, dual-boot supported |

## Why this is the biggest/newest of the three FPGAs

MachXO2 is architecturally newer than LatticeXP/MachXO, and 6,864 logic
elements is roughly 2× the Main Board's part and 5.7× the HMI's. Consistent
with this board doing the most computationally dense job of the three: 8
simultaneous isolated channels, two servo pulse-train generators, and the
machine-bus slave logic, all without help from a CPU.

## Board role (inferred)

`SIO` / `SK` / `SE` / `SO` silkscreened near the FPGA's edge is almost
certainly this device's **hardened EFB slave-SPI configuration port** —
Lattice's own hardened block is literally named for this signal pattern, and
exposing it next to the FPGA for reprogramming is extremely common MachXO2
board practice. Confidence on this specific claim: **moderate-to-high**, but
one research pass (Perplexity) correctly cautions these could instead be
generic board net names — only continuity tracing back to the FPGA's actual
EFB pins settles it definitively.

## Lifecycle / sourcing status

**Active / current production.** Digikey, Mouser, and Arrow all list this
part new with normal lead times (~16 weeks at time of research), priced
roughly $21–30 depending on exact package suffix. **This is the one FPGA on
these three boards that can genuinely be re-sourced as-is**, unlike its two
siblings.

## What this means for a replacement design

Because it's still available new, the Racking Board's original design (if
recovered via readback) could in principle be re-flashed onto a fresh part of
the same type rather than requiring a full re-target to a different family.

## Confidence caveats

Logic-element count, hardened EFB block presence, and active-production
status are corroborated across every independent research pass. The
speed-grade suffix (`-4`) is read directly from the part marking, so that
detail is solid. The functional claim about `SIO/SK/SE/SO` being the EFB port
specifically — as opposed to some other board-defined net — remains the one
item in this dossier that a schematic or continuity trace would upgrade from
"probable" to "confirmed."

## Sources

- Lattice Semiconductor: MachXO2 Family Data Sheet (DS1035), MachXO2
  Programming and Configuration User Guide
- Distributor listings: Digikey, Mouser, Arrow, Findchips/Verical
  (confirming active production and current pricing)
- Cross-verified across 7 independent research passes (this document + 6
  other AI sessions in `Other_AI_Research/`)

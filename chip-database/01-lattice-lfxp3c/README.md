# Lattice LFXP3C-3TN144C

**Board:** Main Board, designator `U28` — the board's central sequencer/controller
**Identification confidence:** `[P]` Confirmed by direct photograph of the top marking

---

## What it is

A non-volatile FPGA from Lattice's **LatticeXP family** (the original 2005-era
family — not LatticeXP2, a different and larger later family with a similar
name; this was double-checked against a search result that briefly suggested
otherwise). Combines SRAM-based logic cells with on-die flash configuration
storage, so the design loads instantly at power-up with no external
configuration ROM to intercept.

## Key specs

| Parameter | Value |
|---|---|
| Logic capacity | 3,000 LUT4 logic cells (some sources say 3,000–3,100 depending on how "logic elements" are counted) |
| Embedded memory | ~54 Kbit block RAM (sysMEM), plus distributed RAM |
| Process | 130 nm |
| Max internal clock | Up to ~320 MHz (speed-grade dependent; this is a ceiling, not a typical operating point) |
| PLLs | 2 on-chip |
| Package (this part) | TQFP-144 |
| I/O voltage | Bank-selectable 1.2 / 1.5 / 1.8 / 2.5 / 3.3 V |
| Config interfaces | JTAG (IEEE 1149.1, ispJTAG), Slave SPI, Master SPI |
| Security | On-chip security bit can permanently disable bitstream readback |

## Board role (inferred, not schematic-confirmed)

Central sequencer for the Main Board: decodes the machine bus, generates
STEP/DIR pulses for the six stepper drivers, scans the ~37-channel isolated
input bank, and drives the two Darlington output banks. No CPU is present on
this board — this FPGA **is** the controller.

## Lifecycle / sourcing status

**Obsolete.** Lattice discontinued the entire LatticeXP family years ago,
across every package variant. Distributor listings (Suntsu, Dasenic, IBS
Electronics) confirm "obsolete" status industry-wide, with gray-market/broker
stock at roughly $15–20/unit and one listing flagging counterfeit risk as high
as ~51% for this exact part. **There is no current-production drop-in
replacement.**

## What this means for a replacement design

If the Main Board's control logic is ever re-implemented, this device cannot
be re-sourced new. Two realistic paths:
1. Recover the bitstream via JTAG readback (if the security bit is clear) and
   re-target the design to a currently-shipping Lattice part (MachXO2 or
   MachXO3 family) — requires re-synthesis, not a pin-swap.
2. Design the replacement logic fresh, informed by the pin/signal map
   recovered through continuity tracing.

## Configuration / JTAG notes

- JTAG header on this board is `J1AG2` (per board photos).
- **Read the IDCODE first** before attempting any readback — confirms wiring
  and part identity without risking the security bit.
- If readback fails or the security bit is set, the design must be
  re-engineered rather than recovered — see the project's
  `docs/05-replacement-controller-plan.md` §10 for that path.

## Confidence caveats (per methodology cross-check)

Multiple independent research passes agreed on the LUT count, family, and
obsolescence status. One pass (Perplexity) correctly cautioned that the exact
ordering suffix (`-3TN144C` — speed grade `3`, package `TN144`, temp grade
`C`) is what's actually confirmed from the photograph, and that architectural
claims should be understood as "this device family" rather than "this exact
die revision has been individually verified against a schematic." No
schematic exists for this board, so **board-level pin function (which pin
does what) remains inferred from layout, not confirmed** — only continuity
tracing settles that.

## Sources

- Lattice Semiconductor: LatticeXP Family Data Sheet (DS1001), part-specific
  thermal/programming application notes
- Distributor lifecycle listings: Suntsu, Dasenic, IBS Electronics, Arrow,
  Octopart, Digikey (obsolescence + pricing cross-check)
- Cross-verified across 7 independent research passes this project (this
  document + 6 other AI research sessions in `Other_AI_Research/`)

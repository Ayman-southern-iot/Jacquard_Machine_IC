# Validation Report

**Scope:** all 32 identified-chip dossiers in `chip-database/`.
**Method:** senior-IC-engineer technical audit — every formula checked
dimensionally, every part-number suffix cross-checked against its claimed
speed/timing grade, every cross-file claim checked for internal consistency,
and every number checked for plausibility against the device class it
belongs to. This is a different pass from the original research: the
original pass asked "what does the datasheet say"; this pass asks "does
what's written actually hold up, and does it agree with itself."

**Verdict up front:** the knowledge base is **fundamentally sound**. Of 32
chip dossiers, **6 needed a correction or an added caveat**; none contained
an error serious enough to invalidate the chip's identification or its core
electrical role on the board. Two general strengths that held up well under
scrutiny: (1) every file already distinguished `[P]` photo-confirmed from
`[I]` inferred identification, and (2) every file already carried a
"Confidence caveats" section — that discipline is what made most of the
issues below easy to find and fix rather than hidden.

---

## Findings requiring correction (6) — all now fixed in the affected files

### 1. Lattice LFXP3C — "Master SPI" configuration mode (incorrect, removed)

**Severity: Moderate.** `01-lattice-lfxp3c/README.md` listed "Master SPI" as
a supported configuration interface. Master SPI mode means the FPGA acts as
bus master and reads its configuration from an *external* SPI flash at
boot — which directly contradicts the LatticeXP family's whole architecture
(self-configuring from **internal**, on-die flash, no external configuration
memory needed). This looks like a family-conflation error picked up
uncritically from one of the six cross-referenced AI reports, likely mixing
LatticeXP up with a later SRAM-configured Lattice family that genuinely does
support Master SPI boot. **Fixed:** removed the claim, added an explanation
of the error to the file so it doesn't get silently reintroduced later.

### 2. TI DRV8818 — DRV8811 "upgrade path" direction (unsupported, softened)

**Severity: Low-moderate.** `04-ti-drv8818/README.md` stated the DRV8818 is
"a documented upgrade path from the older DRV8811 (lower RDS(on))" — this
phrasing implies DRV8811 has the lower resistance, i.e., that DRV8818 is a
regression. That direction was never independently verified this session and
reads as very likely backward (TI generally introduces newer parts in a
family with equal-or-better RDS(on), not worse). **Fixed:** reworded to state
only what's actually confirmed — the two parts are pin-compatible siblings —
and flagged the direction of improvement as unconfirmed pending a check of
TI's actual migration table.

### 3. Sipex SP29302 — thermal example understated the real risk

**Severity: Moderate — this is the most substantive finding in the audit.**
The original worked example (`13-sipex-sp29302/README.md`) computed 26 W
dissipation at "correct" input voltage vs. 62 W at the wrong voltage, and
implied 26 W was the safe case. Running the package's own θJA
(31.2°C/W) against that power level gives `ΔTJ ≈ 811°C` — physically
impossible, meaning the part would hit thermal shutdown long before reaching
steady state. Working the math backward, the actual **free-air-only safe
dissipation ceiling for this package is ~4 W**, not 26 W. **Fixed:** added a
full θJA-based analysis showing that even a much more realistic 1 A load
(rather than the illustrative 3 A used in the original example) still
produces ~8.7 W — more than double the bare-package safe ceiling — meaning
this regulator requires real heatsinking or substantial PCB copper pour to
survive at **any** plausible input voltage, not only the wrong-voltage
failure case. This changes the practical takeaway: measuring load current
matters as much as measuring input voltage before any powered bench test.

### 4. Cosmo 357NT — operating temperature range flagged as likely cross-contaminated

**Severity: Low.** `09-cosmo-357nt/README.md` cited `−55°C to +115°C`, a
range that (a) is unusually wide for a small phototransistor optocoupler of
this class and (b) has a suspiciously identical lower bound (`−55°C`) to the
AO4606 MOSFET's operating range recorded elsewhere in this same database —
consistent with the number having been carried over from the wrong part
during the original synthesis across sources. **Fixed:** flagged as
unverified in the file, with a note on what a more typical range for this
device class looks like, pending a direct check of Cosmo's datasheet.

### 5. TI TLC5620 — max clock rate not independently confirmed

**Severity: Low.** The "~1 MHz" serial clock ceiling was carried from a
cross-referenced source without a directly-quoted datasheet passage backing
it up this session — every other number in that table did have a directly
quoted source. **Fixed:** flagged specifically as the one unconfirmed row in
an otherwise solid table, with a recommendation to verify by datasheet
lookup or logic-analyzer capture before relying on it for timing-critical
design work.

### 6. TI AM26LV32 — dynamic power figure not re-derived

**Severity: Low.** The "~235 mW at full 32 MHz / 4-channel switching" figure
was carried from a cross-referenced source without independent
re-verification. **Fixed:** flagged as indicative-only, order-of-magnitude
confidence, not a verified datasheet value.

---

## Findings noted but not requiring a fix — pinout table completeness

`05-ti-am26lv31/README.md`'s pinout table fills in pins 8 (GND) and 16 (VCC)
by process of elimination — the directly-quoted source covered only the 14
signal/enable pins. This inference is almost certainly correct (it's the
only way the pin count balances, and it matches conventional package
layout), but it wasn't originally flagged as distinct in confidence from the
other 14 pins, which *were* directly sourced. **Added** an explicit note
distinguishing the two, so a reader doesn't treat pins 8/16 with the same
confidence as the rest of the table.

---

## Spot-checks that passed — worth recording as confirmed, not just assumed

A senior review also means checking things that *could* have been wrong and
finding they aren't. These specific cross-checks were run and passed:

| Check | Result |
|---|---|
| Samsung K4S561632N part suffix `-LC75` vs. claimed 133 MHz clock | **Confirmed self-consistent.** `-75` denotes a 7.5 ns cycle time; `1/7.5ns ≈ 133 MHz` — the claimed speed matches what the part number itself implies |
| Cypress CY62128 access time claim (45 ns) vs. board's own part marking `CY62128EV30LL-45ZXI` | **Confirmed.** The `-45` in the marking is the 45 ns speed grade — direct agreement, not just an assumed default |
| DRV8818 RDS(on) breakdown: 0.22 Ω (HS) + 0.15 Ω (LS) vs. stated 0.37 Ω total | **Arithmetically correct** (0.22+0.15=0.37) |
| DRV8818 RC-timing constant: 47 kΩ × 1000 pF vs. claimed ~44 µs tOFF | **Order-of-magnitude correct** (RC = 47 µs, consistent with a ~44 µs measured off-time once the datasheet's actual gain constant is applied) |
| TLC5620 output formula direction (`VO = REF × CODE/256 × (1+RNG)`) vs. described ×1/×2 range-bit behavior | **Directionally correct** — RNG=0 gives ×1, RNG=1 gives ×2, matching the prose description |
| Mean Well NES-350-27 dimensions (`215×115×50mm`) vs. the separately-documented LRS-350-24 dimensions (`215×115×30mm`, in the project's own reference file) | **Consistent, not contradictory** — these are two different products in two different case heights, exactly matching this project's own earlier finding that "LRS-350 is a 30mm low-profile case; NES-350 is taller" |
| AO4606 VGS max (±20 V) vs. VDS breakdown (30 V / −30 V) | **Not contradictory** — gate-drive voltage rating and drain-source breakdown are independent specs; a lower VGS max than VDS is normal for a logic-level MOSFET |

---

## What this audit does not cover

This pass checked **internal technical consistency and dimensional/formula
soundness** of what's already written. It did **not** re-run fresh web
searches against every single claim in all 32 files — that would mean
literally repeating the entire original research effort. Where a number
looked suspicious enough to flag, it's flagged as such in the file rather
than silently corrected to a guessed "fixed" value, per the same
evidence-discipline the rest of this project already uses (`[P]`/`[I]`/`[?]`
marking). The 16 still-unidentified components in `33-unidentified-pending-id/`
are unaffected by this audit — there was nothing to validate for parts with
no confirmed identity.

## Recommendation

Treat this knowledge base as **reliable for engineering decisions**, with
the six flagged items now correctly downgraded to "verify before relying
on" rather than presented as settled fact. The SP29302 thermal finding
(#3 above) is the one result from this audit that should actually change a
practical decision: **measure the HMI board's real load current, not just
its input voltage, before any powered bench test of that regulator.**

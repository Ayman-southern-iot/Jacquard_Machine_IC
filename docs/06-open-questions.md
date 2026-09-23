# 06 — Open Questions

Everything this project does **not** know, ranked by how much it blocks the
drop-in replacement goal. Each entry says what would resolve it — or states
plainly that it cannot be resolved.

> **Project state:** the machine is dead and dismantled, the boards were removed
> before any cable or cabinet photographs were taken, and the carriage / selector
> board was never recovered. Three boards are the entire remaining evidence base.
> See [08-needed-items.md](08-needed-items.md).

---

## Permanently unresolvable

These are not pending. No amount of work on the three boards in hand will close
them.

### 1. Needle-selection protocol — unrecoverable without the selector board

**Status: ⛔ CLOSED — UNOBTAINABLE.** The carriage / selector board was never
recovered from the machine head, and the machine is dismantled. There is no
second copy of this interface anywhere in the project.

Needle selection is not driven from any board in hand. Total actuator capability
across all three is 16 Darlington channels, one relay, six stepper phases and two
servo pulse outputs — nowhere near a needle bed's requirement. The selector
electronics lived on the carriage, and they are gone.

What follows is the honest split between what can still be established and what
cannot.

#### 1a. What *can* still be established — from the master's half of the link

All of this is recoverable by bench work on the three boards in hand. None of it
needs the selector board.

| Recoverable | How | Confidence achievable |
|---|---|---|
| **Electrical standard** | AM26LV31C driver + AM26LV32C receiver are fitted on the Main Board → **RS-422 differential, full duplex** | High — already `[P]` |
| **Transceiver type and count** | Read from the board; the number of quad drivers/receivers bounds the channel count | High |
| **Connector identity and pinout** | Continuity from each transceiver pin to its connector pin — **C-07**, **C-11** | High |
| **Which FPGA pins drive it** | Continuity from the transceiver inputs back to the LFXP3C | High |
| **Termination** | Look for ~100–120 Ω across a pair — tells you whether the Main Board was a physical bus end or mid-chain | High |
| **Idle levels and fail-safe biasing** | Bias-resistor network around the receiver inputs | Moderate |
| **Wire count per direction** | Driver/receiver channel allocation, plus any silkscreened signal names on that connector — **B-16** | Moderate |
| **Signal-role naming** | If the connector carries the same `I`/`O`/`K` convention used elsewhere on these boards, the clock line is identifiable by name alone | Moderate |

**One correction worth making explicitly.** The 10 Mbit/s ceiling that the 6N137
imposes on the *Racking* link **does not automatically apply here.** The Main
Board has **no 6N137 fitted** — its only optocouplers are Cosmo 357NT parts on the
field I/O, not on the bus. So on the Main Board side the ceiling is set by the
AM26LV31/32 pair, which is good for tens of Mbit/s.

The real ceiling on the carriage link would have been set by whatever isolation
sat on the **selector board**, and that is unknown. So the honest statement is:

* Racking link: **≤ 10 Mbit/s**, `[P]` from the 6N137s.
* Carriage link: **ceiling unknown**, bounded above by the RS-422 transceivers
  only. It may well have run faster than the Racking bus — which would be
  consistent with it carrying bulk pattern data.

#### 1b. What *cannot* be established — at any price, from the boards alone

| Unrecoverable | Why |
|---|---|
| **Frame format** — length, delimiters, headers, padding | Defined by the agreement between two endpoints. Only one endpoint survives, and its behaviour is locked in FPGA fabric |
| **Bit order** — MSB- or LSB-first | Same |
| **Command semantics** — what any given bit or field *means* | Same |
| **Needle-to-bit mapping** — which bit selects which needle | Requires the selector board's wiring, or a working bed to observe |
| **Selection encoding** — whether the stream is a raw bitmap, run-length coded, or differential | Same |
| **Timing relationship to carriage position** | Requires simultaneous observation of the bus and the carriage encoder on a running machine |
| **Error handling** — CRC, parity, retry, watchdog | Only visible in traffic or in the bitstream |
| **Actuator electrical requirements** — coil current, pulse width, dwell, recovery time | The actuators were in the head. Not derivable from the driving board |
| **Which board even drove the carriage** | The HMI has two bus channels and the Main Board has an unassigned 40-pin IDC. **The harness record is gone** ([08](08-needed-items.md) §5), so this cannot be settled from the boards alone |

That last row is a loss created by the missing cabinet photographs, not by the
missing selector board. **C-14** and **B-16** can narrow it — by tracing which
Main Board connector the RS-422 transceivers actually reach — but narrowing is
not settling.

#### 1c. The three things that would change this

Only these, in descending order of likelihood:

1. **FPGA bitstream readback succeeds** (**C-16**). The master's state machine and
   pin mapping are inside the LFXP3C. Recovery is hard, but the pin mapping alone
   would resolve most of §1a to high confidence and give a real attack on framing.
2. **A donor selector board** from a scrapped Julong GDH122SA (**F-07**). This is
   the only route that restores the *other* endpoint, and therefore the only one
   that can fully answer §1b. Often cheaper than any amount of analysis.
3. **The machine carcass still exists and the head was not scrapped** (**F-06**).
   Worth one check before accepting the loss.

If none of the three land, needle selection has to be **re-engineered rather than
reverse-engineered** — designed fresh as a new subsystem. The plan for that is
[05-replacement-controller-plan.md](05-replacement-controller-plan.md) §10.

#### 1d. What this does to the project estimate

The three documented boards are roughly **half** the control system. The other
half — needle selection, the timing-critical operation the whole machine is built
around — is now a **clean-sheet design problem**, not a reverse-engineering one.

Any schedule or budget built from the three boards alone will understate the work
by about a factor of two. That is not a reason to stop; it is a reason to scope
honestly up front.

---

## Blocking

### 2. Machine-bus framing is undetermined

Clock frequency, clock polarity and phase, bit order, frame length, frame
structure, addressing, CRC/parity, frame rate. Full list in
[02-communication-protocol.md](02-communication-protocol.md) §4.

**Why it blocks:** Route A (speak the existing bus) cannot begin without it.

**Resolves by:** one good logic-analyser capture at the HMI test points
([02](02-communication-protocol.md) §7), or by successful FPGA readback
([05](05-replacement-controller-plan.md) §4).

### 3. Nobody knows whether FPGA readback is possible

All three FPGAs are non-volatile Lattice parts. If the security bit is clear,
readback recovers the design and most of question 2 dissolves. If it is set,
readback returns nothing.

**Why it blocks:** this is the Route A / Route B decision gate.

**Resolves by:** ~US$25 of hardware and one evening.

---

## High value, low cost

### 4. HMI boot flash is unidentified

The boot flash has no macro shot — and it is **not on the top side of the
mezzanine**. That side carries exactly three devices: `U6` CY62128 SRAM, `U3`
K4S561632N SDRAM and `U1` S3C2440AL CPU `[P]`. The flash is therefore on the
**underside**, which is why it was never photographed. **The mezzanine has to be
lifted off.**
Without its marking the firmware storage type and size are unknown — and the
firmware holds the pattern-file handling and the HMI's half of the bus driver.

**Resolves by:** **one photograph** (**B-02**). The HMI board is in hand, so this
is Tier 1 work — no machine access needed.

### 5. HMI input voltage is unknown

`J15` has no voltage silkscreened anywhere near it. `[I]` 12 V or 24 V.

**Why it matters:** the SP29302T5 is a *linear* 3 A regulator. Applying 24 V to a
12 V input turns every excess volt into heat and destroys it immediately.

**Resolves by:** ~~one multimeter reading~~ — the machine is gone, so it can no
longer be measured in situ. It must now be established by **tracing** (**C-12**):
follow `J15` → `D3` → `CE0` → `U20`, and read the bulk capacitor's voltage rating
plus the SP29302 configuration. Do this **before** any powered bench work on the HMI.

### 6. PSU units 3 and 4 are unidentified

Two smaller enclosed supplies sit beside the two NES-350-27 units; their model
labels face away from the camera. `[I]` they supply the +12 V rail that the 27 V
units cannot.

**Why it matters:** total system power budget is currently unquantified.

**Resolves by:** ⏸ **conditional on F-06** — the supplies were in the cabinet,
which is dismantled. Only resolvable if the machine carcass still exists.

### 7. The Main Board mode jumper is untranslated

Jumper `P1`, 2-position, with a printed truth table enumerating all four states of a
2-bit selector: `1-ON 2-OFF`, `1-OFF 2-ON`, `1-ON 2-ON`, `1-OFF 2-OFF` `[P]`. Each row
carries a Chinese annotation — 3 characters on the first three rows, 2 on the last.

**Why it matters:** these are almost certainly machine-configuration or
addressing modes that a replacement must reproduce.

**Resolves by:** a macro shot of `P1` (**B-05**) then translation (**F-01**). The
board is in hand, so this is Tier 1 work. The English rows are already read; only
the Chinese annotation column is below the resolution of the existing photograph.

### 8. Which DB9 is the RS-232 console

One MAX3232 against three AM26LV31C drivers. `[I]` one console, two RS-422.

**Resolves by:** one powered multimeter reading per connector — RS-232 transmit
idles at −5 V to −12 V and nothing else on these boards looks like that.
([02](02-communication-protocol.md) §6 step 1.)

---

## Needed before any build

### 9. No connector pinouts exist

No pin-1 marks have been read on any connector on any board. Signal *names* are
known from silkscreen; signal *positions* are not.

**Resolves by:** Phase 1 continuity mapping
([05](05-replacement-controller-plan.md) §3).

### 10. The stepper driver part is inferred, not read

Six HTSSOP-28 sites, each with two 0.25 Ω sense resistors and a bulk capacitor —
unambiguously a bipolar stepper driver of the DRV8818 / DRV8811 / A3977 class.
But **no marking has been read on any of the six**, and the `.webp` in the project
is a vendor catalog photo, not evidence from this machine.

**Why it matters:** current limit, microstep resolution and decay-mode behaviour
all differ between candidates.

**Resolves by:** one macro photograph at an angle that catches the laser marking —
they were unreadable in the existing overview because of glare.

### 11. Designator-to-channel mapping for the steppers

Seven designators are visible in the driver region (`U4` `U5` `U6` `U15` `U17`
`U18` `U19`); six sites are populated. Which designator drives which axis is
unknown.

**Resolves by:** a dedicated photograph plus Phase 1 continuity.

### 12. Unidentified ICs

| Board | Refs |
|---|---|
| Main | `U3` `U5` `U7` `U17`–`U19` `U_Z1` `U_K2` `U21` `U22` `Q_Z1` `Z2` |
| Racking | `U11` (TSSOP-16, by the `RST` net), `U13` |
| HMI | `U8` — confirmed **unpopulated** `[P]`, not a gap |

**Resolves by:** macro shots — see [07-photo-shot-list.md](07-photo-shot-list.md).

### 13. Fuse ratings

Only the Main Board's `F2` is legible (**2 A**). The second Main Board fuse and
both Racking Board fuses are unread.

**Resolves by:** reading the fuse bodies directly, or a close photograph.

### 14. Screw-terminal pinouts

The 4-pin DC inlets on the Main and Racking boards have their silkscreen obscured
by the terminal bodies. The labels may be on the solder side.

**Resolves by:** photographing the solder side under each terminal, or continuity
from the terminal pins to the rail test points.

---

## Context, not blocking

### 15. Machine attribution is external

The user reports a **Julong GDH122SA**. No make, model or OEM name is
silkscreened on any board photographed here. The attribution comes from outside
this photo set.

The `SWG-Yarn-A-02` silkscreen and the `JITIBAN-4` (机台板, "machine platform
board") naming are consistent with a Chinese-market flat-knitting / jacquard
controller of the 2010–2013 era, but they do not identify an OEM.

**Resolves by:** ⏸ **conditional on F-06** — the machine nameplate was on the
frame. This now matters more than it did: sourcing a donor selector board
(**F-07**) requires knowing the model with certainty, and the attribution is
currently unverified.

### 16. The servo amplifier make is inferred from signal names

`xLA`/`xLAR`/`xLB`/`xLBR`/`xPG`/`xINP`/`xRDY` matches the Mitsubishi MR-J series
signal set. That is a strong `[I]`, because these are an amplifier-vendor
convention rather than a house convention — but nobody has read a label on an
actual amplifier.

**Resolves by:** ⏸ **conditional on F-06** — photographing the amplifier
nameplates, which needs the cabinet to still exist. If it does not, the inference
stands unconfirmed; design against the MR-J spec and verify against whatever
amplifier is actually fitted at commissioning.

### 17. Why the Racking Board is a year newer

Main Board `2012-07-17`; Racking Board `SWG-Yarn-A-02 2013`, with a later MachXO2
lot code still. `[I]` that board was revised, replaced or reworked after the
original build. Worth knowing whether it is a field replacement, because if so
the rest of the machine may be older than 2012.

---

## No longer open

Recorded so nobody re-investigates:

| Was | Now |
|---|---|
| Racking Board year cut off in the photo | **2013** `[P]` — readable after edge crop |
| "Roughly 50 Darlington drivers along the top edge" of the Main Board | **Wrong.** That row is ~36–38 Cosmo 357NT **input** optocouplers. Darlington drive is 16 channels in two DIP-18 ULN2804A |
| Whether the Main Board has stepper drive | **Yes — six populated channels** |
| `FU120N` on the HMI |  **Resolved.** VBsemi FU120N, N-channel 100V MOSFET, DPAK - confirmed by datasheet lookup this session. DPAK power MOSFET at `U_D7` switching the CCFL backlight inverter via `J3`, gate driven by `Q2`/`Q3`/`Q4`. Detail: [docs/10](10-ic-technical-reference.md) section 7.3 |
| HMI `U8` unidentified | **Unpopulated footprint** |
| Whether a dedicated power-supply PCB exists | **It does not.** Bought-in Mean Well units; each board regulates locally |
| Is the machine running or dead? | **Dead**, and dismantled |
| Will the carriage / selector board be photographed? | **No.** It was never recovered from the head — ⛔ unobtainable |
| Will the cabinet and harness be photographed? | **No.** The boards were removed first — ⛔ the interconnection record is unrecoverable |
| Mode jumper truth table | Jumper is **`P1`**; four English rows read `[P]`. Only the Chinese annotation column remains |

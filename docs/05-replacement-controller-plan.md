# 05 — Replacement Controller Plan

Written for the stated objective: **build a drop-in replacement controller**.

This document is the practical path from where the project stands today —
three boards on a bench, no machine, no tools yet — to a controller that
can drive a machine. What has to be acquired first is tracked in
[08-needed-items.md](08-needed-items.md).

> **Project state:** the machine is dead and dismantled; the boards were removed
> before any cable or cabinet photographs were taken; the carriage / selector
> board was never recovered. Three boards are the entire evidence base. Needle
> selection cannot be reverse-engineered — see **§10**.

---

## 1. The strategic decision you have to make first

> **⚠ A prior question has opened up, and it outranks this one.**
>
> **A drop-in replacement for *which* machine?** The machine these boards came
> from is dead and dismantled. A "drop-in replacement controller" needs a host to
> drop into, and right now the project does not name one. The realistic
> possibilities lead to very different work:
>
> | If the target is… | Then |
> |---|---|
> | **Other GDH122SA machines still in service** | The strongest case. Those machines supply the field wiring, the needle bed, and a live reference system to test against — which also revives most of the ⏸ items and makes §10.5's "you need a needle bed" a non-issue |
> | **Rebuilding this machine** | Requires the carcass (**F-06**) and the head. Confirm both exist before planning |
> | **A product for third parties** | Much larger scope: certification, support, multiple machine variants |
> | **Learning / archival exercise** | Entirely legitimate — but then stop at Tier 1 and the documentation, and do not buy D-03…D-06 |
>
> This is tracked as **F-08** in [08-needed-items.md](08-needed-items.md).
> **Answer it before choosing between Route A and Route B below**, because Route B
> in particular assumes a host machine whose field wiring you keep.

There are two routes, and they diverge early. Choosing the wrong one wastes
months, so decide this before buying anything.

### Route A — Speak the existing bus

Reverse-engineer the private machine bus and build a node that the existing
boards accept as a peer. Replace one board at a time.

| | |
|---|---|
| **Pro** | Incremental. You can replace the failing board and keep the rest. Machine stays largely original; spares interchange |
| **Pro** | Lower risk per step — each swap is independently testable |
| **Con** | Requires full protocol decode: clock, framing, bit order, addressing, **and exact timing** |
| **Con** | You must match hard real-time behaviour you did not design and cannot see |
| **Con** | Blocked until you own a logic analyser — **and until C-13 shows the master still transmits with no machine attached** |

### Route B — Replace the whole control stack

Discard all three boards. Keep the **host machine's** *field wiring*: motors,
encoders, sensors, solenoids. Build a new controller against those, which are
standard interfaces. **Requires a host machine to exist — see F-08 above.**

| | |
|---|---|
| **Pro** | **You never have to decode the private bus at all.** Everything you interface to is documented: stepper coils, Mitsubishi-style encoder lines, 24 V sensor contacts |
| **Pro** | Not blocked on test equipment — continuity mapping starts as soon as the boards are on a bench |
| **Pro** | Modern, maintainable, and you own the source |
| **Con** | All-or-nothing commissioning. No partial fallback |
| **Con** | You must reimplement the *machine control logic* — cam timing, racking sequences, needle selection — which the FPGAs currently hold and which no document describes |
| **Con** | Needle selection is **unsolvable by reverse engineering** either way — the selector board is gone (§5, §10) |

### Recommendation

**Start down Route A's first two phases, because they are also Route B's first
two phases.** Phases 0–2 below — board intake, continuity mapping, FPGA readback —
produce exactly the same artefacts regardless of which route you finish on, and
they cost almost nothing.

Decide between A and B at the **Phase 2 gate**, when you know whether FPGA
readback works. If it does, Route A becomes dramatically easier. If the security
bit is set, Route B is usually the better economic choice.

---

## 2. Phase 0 — Board intake baseline (multimeter, unpowered)

> **Rewritten.** The original Phase 0 was a machine-isolation procedure. The
> machine is dismantled and the boards are already out, so that procedure no
> longer applies. What remains is board intake.

**Nothing else starts until this is done.**

- [ ] Photograph each board as received, both sides, before any probing — the current overview shots are good, but re-shoot if a board has been handled since.
- [ ] Record any visible damage: burnt components, lifted pads, corrosion, previous rework, socketed parts.
- [ ] **C-06 — rail resistance check, unpowered.** `+24V`, `+12V`, `3.3V` each to `GND`. Anything near 0 Ω means a shorted regulator or decoupling capacitor; find it before applying power.
- [ ] **C-04 — `GND` / `SGND` / `DGND` resistance**, each board. Record the readings; they define the isolation boundary.
- [ ] **C-12 — determine the HMI `J15` input voltage by tracing.** ⚠️ Do this before the HMI ever sees a bench supply.
- [ ] Note the Racking Board's `S1` DIP switch positions **as found** — that was its node address. It is one of the very few configuration settings that survived the teardown.
- [ ] Note the Main Board's `P1` jumper position **as found** — one of four machine modes.

The last two matter more than they look. With the harness record gone
([08](08-needed-items.md) §5), the switch positions are among the only surviving
evidence of how this machine was actually configured. **Record them before anyone
moves them.**

**Standing hazards** — reduced now that mains is out of the picture, but not zero:

| Hazard | Where |
|---|---|
| Several hundred volts AC from a low-voltage input | HMI CCFL inverter, `HQ-01-1S015REV1.0`, marked `DANGER HIGH VOLTAGE`. Live whenever the HMI is powered, **including from a bench supply** |
| Stored charge | Bulk electrolytics on the Main Board and HMI may still hold charge |
| Wrong bench voltage | The HMI's main regulator is linear. Over-volting destroys it instantly — hence C-12 first |
| ESD | The boards are now loose on a bench rather than grounded in a cabinet. Three irreplaceable FPGAs |

---

## 3. Phase 1 — Continuity mapping (multimeter, unpowered, ~2–3 days)

This is the largest block of useful work available, and **everything
later depends on it.** Full step-by-step procedure:
[02-communication-protocol.md](02-communication-protocol.md) §6.

### Deliverables

| # | Artefact | Where it goes |
|---|---|---|
| 1 | Which DB9 is the RS-232 console | [01 HMI README](../boards/01-hmi-board/README.md) |
| 2 | Differential pair map — which connector pins are pairs, and which are terminated (~100–120 Ω) | this folder |
| 3 | **Transceiver-pin → connector-pin table** for every AM26LV31/32 and AM26LS31 | this folder |
| 4 | Optocoupler in/out mapping on the Racking Board | this folder |
| 5 | Rail integrity + `GND`/`SGND`/`DGND` resistance readings | [03-power-architecture.md](03-power-architecture.md) |
| 6 | HMI `J15` input voltage — **determined by tracing (C-12)**, since it can no longer be measured in situ | [01 HMI README](../boards/01-hmi-board/README.md) |
| 7 | Pin-1 orientation and numbering for every connector | this folder |
| 8 | **C-14 — which Main Board connector carried the carriage link**, by tracing the RS-422 transceivers to their connector | [06-open-questions.md](06-open-questions.md) §1 |

Deliverable 3 is the important one. When it is done you will know **which physical
wire carries the clock**, because you can trace it back to the FPGA pin that
drives it. That converts "unknown link" into "known wires, unknown framing" — a
far smaller problem.

Deliverable 8 is new, and it exists because the harness record is gone: with no
cabinet photographs, **which board drove the selector is now itself an open
question**. Tracing narrows it; it cannot fully settle it.

> **Dropped from this phase.** The old deliverable 8 — mapping `J1` signal names
> to physical sensors by watching the status LEDs — required a wired-up machine.
> It is now ⏸ **C-10**, conditional on the carcass. The `J1` signal meanings stay
> `[I]` inferences.

### Also do this now

- [ ] **B-02** — photograph the HMI mezzanine's **boot flash**. The board is in hand; this is one photo and it is still the single biggest recoverable unknown.
- [ ] **B-05 / F-01** — macro-photograph jumper `P1` and translate its Chinese annotations. The four English rows are already read.
- [ ] **B-16** — photograph the Main Board's 40-pin top IDC and left-edge headers, to support C-14.
- [ ] **F-07** — start sourcing a donor selector board (§5). Calendar time, not working time.

---

## 4. Phase 2 — FPGA readback (~US$25, one evening)

**The highest-value single experiment in the project.** If it works, most of the
protocol problem disappears.

All three FPGAs are non-volatile parts with on-chip configuration memory:

| Board | Device | Header |
|---|---|---|
| HMI | MachXO LCMXO1200C | `JTAG` |
| Main | LatticeXP LFXP3C | `J1AG2` |
| Racking | MachXO2-7000HC | `CON1` (10-pin) |

### Procedure

1. Buy an **HW-USBN-2B clone** (~US$20–30) and install **Lattice Diamond Programmer** (free).
2. Work out the JTAG header pinout by continuity from the FPGA's TCK/TMS/TDI/TDO pins — a Phase 1 skill.
3. **Scan the chain and read the IDCODE first.** This confirms the part, proves your wiring, and is a board-health check worth having regardless.
4. Attempt bitstream readback.

### Expected outcomes

| Outcome | What it means | Next |
|---|---|---|
| Readback succeeds | You have the design. Netlist recovery from a Lattice bitstream is hard but the *pin mapping* is immediately readable, which alone resolves most of §4 of the protocol doc | **Route A** becomes strongly preferred |
| Security bit set | Readback returns nothing. Common in commercial designs | **Route B**, and needle selection falls to the **§10 fallback** |
| No IDCODE | Wiring wrong, or the header is not JTAG | Re-check against Phase 1 data |

**This is the decision gate between Route A and Route B** — and, for needle
selection specifically, between reverse-engineering and re-engineering (**§10**).

These are your own boards, and reading back your own hardware to keep a machine
you own running is ordinary repair work.

---

## 5. Phase 3 — The missing carriage board

> **⛔ Status changed: this board is UNOBTAINABLE.** It was never recovered from
> the machine head, and the machine is dismantled. This phase can no longer be
> executed as originally written.

Needle selection is not driven from any board in hand. Total actuator capability
across all three is 16 Darlington channels, one relay, six stepper phases and two
servo pulse outputs — nowhere near enough for a needle bed. The selector
electronics lived on the carriage and are gone.

**Consequence:** needle selection cannot be reverse-engineered. It has to be
**re-engineered** — designed fresh. See **§10** for what that requires.

The three things that would reopen this phase, in descending order of likelihood:

| Route | ID | Notes |
|---|---|---|
| FPGA bitstream readback succeeds | **C-16** | Recovers the master's pin mapping and possibly its state machine |
| Source a **donor selector board** from a scrapped Julong GDH122SA | **F-07** | The only route that restores the *other* endpoint. Often cheaper than any amount of analysis |
| The machine carcass still exists and the head was not scrapped | **F-06** | Worth one check before accepting the loss |

Full analysis of what survives and what does not:
[06-open-questions.md](06-open-questions.md) §1.

**Action: pursue F-07 in parallel with everything else.** A donor board is the
single highest-leverage acquisition available to this project, and sourcing one
takes calendar time rather than working time — so start looking now, not later.

---

## 6. Phase 4 — Bus capture (needs a logic analyser)

Blocked until you buy one. Kit list, probe points, channel map and trigger setup:
[02-communication-protocol.md](02-communication-protocol.md) §7.

Two things to get right:

* **Tap single-ended, never on the RS-422 pair.** Use the HMI test points — they
  are plated, accessible and already single-ended. That is what they are for.
* **Sample at ≥ 100 MS/s.** A 24 MS/s USB analyser is marginal against a
  `[I]` 1–5 MHz bus clock.

Capture idle first, then a single needle-selection cycle. The difference between
those two traces is where the pattern data lives.

Then work through the falsification tests in
[02-communication-protocol.md](02-communication-protocol.md) §8 — they check the
whole model at once and will tell you quickly if the analysis here is wrong.

---

## 7. Phase 5 — Build order

Whichever route you take, build in this order. It is ordered by *certainty*, so
you are always working on the best-understood thing available.

### 5.1 Racking Board first

The most self-contained board, and **its external interface is a documented
standard** — Mitsubishi MR-J-style servo lines. You can design and bench-test the
servo half against amplifier documentation **today**, with no protocol knowledge
at all.

It also has the smallest FPGA, the clearest connector labelling, and a DIP switch
that makes node addressing explicit.

### 5.2 Main Board second

Larger but conceptually simple: six stepper channels, ~36–38 isolated inputs, 16
Darlington outputs, one relay, four DAC channels. Every one of those is a standard
interface. The hard part is not the electronics — it is the **machine control
logic** currently living in the LatticeXP fabric.

### 5.3 HMI last

Most software, least real-time pressure. A modern SBC with a touch panel replaces
an S3C2440 and a CCFL-backlit LCD comfortably. Defer it: it is the least likely
board to fail and the easiest to replace when it does.

### 5.4 Carriage / selector board

Unscoped until Phase 3 produces photographs.

---

## 8. Non-negotiable design constraints

Any replacement must honour these. Each is drawn from the existing design, and
each will pass a bench test and fail on the machine if you get it wrong.

| # | Constraint | Why |
|---|---|---|
| 1 | **Rail is 27 V, not 24 V** | Drivers must be rated for 27 V continuous plus inductive flyback margin |
| 2 | **Keep the isolation barriers** | Every machine-facing signal crosses an optocoupler. Inverter and solenoid switching noise on a shared frame is severe |
| 3 | **Keep `GND` / `SGND` / `DGND` separate** | Three distinct grounds are named on the Racking Board. Bonding them defeats the isolation |
| 4 | **Differential on every long run** | The head traverses the full bed on a flexing cable. Single-ended will not survive it |
| 5 | **Match the 5 V / 3.3 V driver asymmetry** | The Racking Board drives at 5 V (AM26LS31C) and receives at 3.3 V (AM26LV32C). Deliberate — amplitude on the long run, FPGA level coming back |
| 6 | **Implement `UPS-T`** | Power-fail warning. Without an orderly park the machine can drop stitches or crash the carriage into the bed end. Easy to overlook; will not show up until the first real power failure |
| 7 | **Hard real-time in hardware** | The original puts needle and carriage timing in FPGA fabric, not software, and uses no microcontroller at all on two of three boards. That was a considered choice — treat a software-timed replacement as a decision to justify, not a default |
| 8 | **Preserve the safety relay function** | The HFD23 1-Form-C relay is the only electromechanical interlock on the Main Board |

---

## 9. Immediate next actions

Tracked as a checklist in [08-needed-items.md](08-needed-items.md). In order,
all cheap:

1. **Answer F-08 and F-06** — a replacement for which machine, and does the carcass still exist? If a site visit is possible, take [09-field-checklist.md](09-field-checklist.md).
2. **Start sourcing a donor selector board (F-07).** Calendar time, not working time — begin now.
3. Download the datasheets (E-01 … E-11) — free, unblocked.
4. Buy D-01, D-02, D-07 — under US$60.
5. Tier 1 bench work: macro photography (B-05 … B-12, B-16) and continuity mapping (C-07, C-11, C-04, C-09, C-08, C-06, C-12, C-14).
6. Buy D-03; run the IDCODE scan (C-15), then the readback attempt (C-16) — §4.
7. **Decide Route A, Route B, or the §10 fallback at the readback gate.**

---

## 10. Fallback — when readback fails or the device is read-protected

**Trigger:** C-16 returns nothing on the Main Board's LFXP3C, *and* no donor
selector board (F-07) has been sourced, *and* the carcass is gone (F-06 = no).

At that point needle selection must be **re-engineered, not reverse-engineered.**
This section says what that actually means, because it is a materially different
project from the one described in §1–§8.

### 10.1 What is lost and what survives

Full analysis: [06-open-questions.md](06-open-questions.md) §1. In brief:

| Survives — recoverable by bench work | Lost — unrecoverable |
|---|---|
| Electrical standard (RS-422 differential, full duplex) | Frame format, bit order |
| Transceiver type, channel count | Command semantics |
| Connector identity and pinout (C-07, C-11) | Needle-to-bit mapping |
| Which FPGA pins drive the link | Selection encoding |
| Termination, idle levels, fail-safe bias | Timing vs carriage position |
| Bit-rate ceiling on *our* side of the link | Actuator current, pulse width, dwell |

You keep the **physical layer** in full and lose the **link and application
layers** entirely.

### 10.2 Step 1 — Establish whether the master still talks (C-13)

**Do this before committing to anything in §10.3.** It is cheap and it changes the
answer.

Power the HMI — and optionally the Main Board — on a bench supply at the voltage
determined by C-12, with nothing else connected. Then check whether the bus master
transmits into an absent peer.

| Observation | Meaning |
|---|---|
| The master clocks and transmits regardless | **Live capture is possible without a machine.** Route A partially revives: you can recover framing, bit rate, clock phase and frame cadence from real traffic, even though semantics stay unknown |
| The master stays silent pending an interlock | `[I]` it is waiting on a safety, `UPS-T`, or peer-present condition. Try satisfying those one at a time before concluding it is dead |
| Nothing comes up at all | Board fault, or the input voltage was wrong. Re-check C-12 and C-06 first |

`[I]` **Plausible but untested.** A master that idles a clock and polls for peers
is the common design; a master gated on interlocks is also common. This single
experiment is worth more than any amount of further static analysis.

### 10.3 Step 2 — Build a selector-side stub

Whether or not the master talks, a **stub** is the tool that makes progress
possible. It is a small board — an FPGA dev board or a fast MCU is sufficient —
that:

1. Terminates the candidate carriage connector correctly (RS-422, ~120 Ω where
   C-08 says a terminator belongs).
2. **Listens passively** first, logging every edge. If C-13 showed traffic, this
   is your capture instrument and you may not need a separate logic analyser.
3. Later, **impersonates** a selector board — responds to whatever handshake the
   master expects — so you can watch how the master's behaviour changes when it
   believes a peer is present.

Step 3 is where genuine information appears. A master that gets a plausible reply
often advances past initialisation and begins sending real payload.

**Cost:** an iCE40 or similar dev board plus an RS-422 transceiver breakout —
roughly US$40–60. Cheaper than the logic analyser, and more useful here.

### 10.4 Step 3 — Treat needle selection as a new subsystem

If the stub yields nothing usable, stop trying to recover the original protocol
and design the subsystem fresh, against the interfaces you *do* understand.

This is a legitimate engineering path, not a defeat. You already hold:

* **Carriage position** — six stepper channels and the `AC2`/`AC3`/`AC4` cam-position
  and `AZL` home inputs on the Main Board. You can know where the head is.
* **Servo axes** — two, on a documented Mitsubishi MR-J-style interface. You can
  know where the bed and carriers are.
* **Machine I/O** — ~36–38 isolated inputs, 16 Darlington outputs, one safety relay.
* **Analog setpoints** — four DAC channels.

What you must newly specify, because the original is unrecoverable:

| Must design | Notes |
|---|---|
| **Selector actuator drive** | Current, pulse width, dwell, recovery. Must be characterised against whatever actuators the bed actually uses — **this requires physical access to a needle bed**, donor or otherwise |
| **Selector data link** | Your own protocol. You control both ends, so make it simple and documented |
| **Needle-to-channel mapping** | Your own convention |
| **Selection timing vs carriage position** | Derived from your own encoder feed, not from the OEM's timing |
| **Pattern-file format → selection stream** | Your own. The OEM's format is only relevant if you must read existing customer patterns — decide that explicitly |

### 10.5 The honest scope statement

Under this fallback the project stops being "replace a controller" and becomes
**"build a knitting-machine head controller, reusing three documented I/O boards."**

Two consequences worth stating plainly before anyone commits budget:

1. **You need a needle bed.** Not the electronics — the *mechanism*. Actuator
   characterisation cannot be done on paper, and without a bed to test against,
   §10.4 cannot be completed at all. If no bed is available either, the project
   has no achievable end state in its current form and that should be surfaced now.
2. **Route B becomes the default.** Once needle selection is clean-sheet, the
   argument for preserving a private bus you cannot fully decode largely
   disappears. Replacing the whole stack and keeping only the field wiring is
   usually both cheaper and more maintainable at that point.

### 10.6 Decision summary

```text
C-16 readback
   ├── succeeds ──────────────→ Route A (§1, §7)
   └── read-protected
         ├── F-07 donor board found ─────→ Route A, endpoint restored
         ├── F-06 carcass exists ────────→ check the head first
         └── neither
               └── C-13 bench bring-up
                     ├── master transmits ──→ stub capture (§10.3),
                     │                        partial framing recovery
                     └── master silent ─────→ new subsystem design (§10.4)
                                              → Route B, and confirm a
                                                needle bed is available
```

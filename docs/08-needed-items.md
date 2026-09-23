# 08 — Needed Items Register

The single canonical list of what this project needs. Tick items off here.

---

## Project status

| | |
|---|---|
| **Machine** | **DEAD.** Dismantled. Boards removed before any cable or cabinet photographs were taken |
| **Boards in hand** | **3** — HMI, Main, Racking |
| **Selector / carriage board** | **UNOBTAINABLE.** Never recovered from the machine head |
| **Interconnection record** | **UNRECOVERABLE.** No harness, cable or cabinet photographs exist |
| **Tools in hand** | None |
| **Work currently possible** | Bench-safe, unpowered work on three boards, plus desk work |

This is now a **board-level static analysis project**, not a machine
teardown. The boards are the entire remaining evidence base.

---

## Status key

| Mark | Meaning |
|---|---|
| ☐ | Open — actionable now |
| ⏸ | **CONDITIONAL** — depends on F-06 (does the machine carcass still exist?) |
| ⛔ | **CLOSED — UNRECOVERABLE** — the evidence no longer exists and cannot be reconstructed |
| ⛔ | **CLOSED — UNOBTAINABLE** — the physical item was never recovered |
| ☑ | Done |

---

> **Going to the factory?** Take
> [09-field-checklist.md](09-field-checklist.md) — every question below,
> reorganised by where you will be standing when you can answer it.

## 1. Two questions that outrank everything else

### 1.1 — F-08 — A drop-in replacement for *which* machine?

**Status: ☐ — this is now the project's most important open question.**

The objective is "build a drop-in replacement controller." The machine these
boards came from is dead and dismantled, so there is currently **no host for a
replacement to drop into.** The realistic targets lead to very different projects:

| If the target is… | Consequence |
|---|---|
| **Other GD-H122S machines still in service** | The strongest case by far. A live machine supplies field wiring, a needle bed, and a working reference to test against — reviving most ⏸ items and removing the hardest blocker in [05](05-replacement-controller-plan.md) §10.5 |
| **Rebuilding this machine** | Needs the carcass (F-06) **and** the head. Confirm both exist first |
| **A product for third parties** | Much larger scope — certification, support, machine variants |
| **Learning / archival** | Entirely legitimate. But then stop at Tier 1 plus documentation, and do not buy D-03 … D-06 |

**Answer this before spending anything.** It determines whether Tier 2 and Tier 3
are worth doing at all.

### 1.2 — F-06 — Does the dead machine carcass still physically exist anywhere?

**Status: ☐ — answer this before planning anything else.**

If the frame, cabinet, harness or head still exist in a scrapyard, a corner of the
workshop, or at the previous owner's site, then **12 items below move from ⏸ back
to ☐** — including the PSU nameplates, the servo amplifier identification, the
sensor-to-signal map, and possibly the selector board itself.

If the carcass is gone, those 12 close permanently and the project scope is fixed
at what three boards can tell you.

> **One caveat on A-02.** You have confirmed the selector board is unobtainable,
> and it is recorded that way below. But if the carcass does still exist, the head
> is worth one physical check before accepting that — the selector board changes
> project scope more than any other single item in this register. If the head was
> scrapped separately or the machine is genuinely gone, disregard this.

---

## 2. TIER 1 — bench-safe, unpowered, boards in hand

**Do these first.** They need no machine, no power, and no live access — only the
three boards, a multimeter, and a camera. This is now the project's critical path.

### 2.1 Continuity and resistance (unpowered)

| ID | Task | Resolves | Status |
|---|---|---|:--:|
| **C-07** | **Continuity map: every RS-422 transceiver pin → connector pin**, all three boards | **Identifies which physical wire is the clock.** The single highest-value measurement available | ☐ |
| **C-11** | Pin-1 orientation and numbering, every connector, every board | Prerequisite for any harness or replacement design | ☐ |
| **C-04** | `GND` ↔ `SGND` ↔ `DGND` resistance, each board | Where the isolation barrier actually sits | ☐ |
| **C-09** | Optocoupler in/out mapping, Racking Board, both sides of all 8 × 6N137 | Isolation boundary detail; bus path through the node | ☐ |
| **C-08** | Differential pair map — which pins are pairs, which read ~100–120 Ω | Bus topology; which boards are physical ends | ☐ |
| **C-06** | Rail integrity — `+24V`/`+12V`/`3.3V` to `GND` resistance, each board, looking for shorts | Board health before any power-up. **Unpowered check** | ☐ |
| **C-12** | **Determine the HMI `J15` input voltage by tracing**, not measuring — follow `J15` → `D3` → `CE0` → `U20`, and read `CE0`'s voltage rating plus the SP29302 configuration | Replaces C-02, which needed a live machine. **Do this before any powered work on the HMI** | ☐ |
| **C-14** | Trace which Main Board connector carried the carriage link — follow the AM26LV31C/32C outputs to their connector | **Which board drove the selector is now unknown** (see [06](06-open-questions.md)) | ☐ |

### 2.2 Macro photography (boards in hand)

| ID | Photograph | Resolves | Status |
|---|---|---|:--:|
| **B-05** | Jumper `P1` truth table, macro | The Chinese annotation column. English rows already read | ☐ |
| **B-06** | Six stepper driver ICs, oblique light, one macro each | Confirms DRV8818 vs a pin-compatible part | ☐ |
| **B-07** | Main Board unidentified ICs — `U3` `U5` `U7` `U17`–`U19` `U_Z1` `U_K2` `U21` `U22` `Q_Z1` `Z2` | 11 unknown parts | ☐ |
| **B-08** | Racking Board `U11`, `U13` | 2 unknown parts | ☐ |
| **B-09** | Connector pin-1 marks, every connector, every board | Pin numbering — C-11 depends on it | ☐ |
| **B-10** | Solder side under each 4-pin screw terminal (Main, Racking) | DC inlet pinouts, hidden by the terminal bodies | ☐ |
| **B-11** | Fuse bodies — Main (second), Racking ×2, HMI `F1`/`F2` | Fuse ratings; only Main `F2` = 2 A is known | ☐ |
| **B-12** | JTAG headers — HMI `JTAG`, Main `J1AG2`, Racking `CON1` | Programmer wiring for D-03 | ☐ |
| **B-16** | **Main Board 40-pin top IDC and all left-edge headers**, square-on with any silkscreen | Which connector served the carriage — supports C-14 | ☐ |
| **B-02** | HMI boot flash — **on the UNDERSIDE of the mezzanine**; lift it off. Top side is only SRAM + SDRAM + CPU | Firmware type and size | ☐ |

### 2.3 Tools needed for Tier 1

| ID | Item | Approx. | Status |
|---|---|---|:--:|
| **D-01** | Digital multimeter, continuity + resistance | US$20–40 | ☐ |
| **D-02** | Fine-tip probes / test hooks / SMD tweezer probes | US$15 | ☐ |
| **D-07** | Camera with macro capability + an oblique light source | — | ☐ |

**Tier 1 total cost: under US$60.** Nothing else is required to start.

---

## 3. TIER 2 — powered bench work

Needs a current-limited bench supply and C-12 resolved first. Everything here is
still doable **without the machine**.

| ID | Task | Notes | Status |
|---|---|---|:--:|
| **D-06** | Bench DC supply, 0–30 V / 5 A, **current-limited** | Current limit is not optional — it is what protects a board whose fault history is unknown | ☐ |
| **D-03** | Lattice HW-USBN-2B clone (USB JTAG programmer) | **The project's decision gate.** Highest value per dollar in this register | ☐ |
| **C-15** | **FPGA IDCODE scan**, all three boards | Confirms the parts, proves the wiring, and is a board-health check | ☐ |
| **C-16** | **Bitstream readback attempt**, all three boards | If it works, most of the protocol question dissolves. If not, see [05](05-replacement-controller-plan.md) §10 | ☐ |
| **C-03** | Which DB9 is RS-232 — HMI powered on the bench, each pin to `GND`, look for −5 V to −12 V idle | Console port identification | ☐ |
| **C-13** | **Bench bring-up: power the HMI alone and check whether the bus master transmits with no peers present** | `[I]` If it does, live bus capture becomes possible **without a machine** — this would substantially revive Route A. If the master stays silent pending an interlock, it does not. **Untested and important** | ☐ |
| **D-05** | USB-TTL serial adapter, 3.3 V | HMI console, once C-03 identifies the port | ☐ |
| **D-04** | Logic analyser, 8-ch, ≥ 100 MS/s | **Only buy this if C-13 shows live traffic.** Otherwise there is nothing to capture | ☐ |

**C-13 is the most important untested assumption in the project.** It decides
whether a dead machine also means a dead bus.

---

## 4. TIER 3 — conditional on the carcass (F-06)

All ⏸ pending the answer to §1. Do not plan around these.

| ID | Item | Why it needs the carcass | Status |
|---|---|---|:--:|
| **A-01** | Access to the machine | It is dismantled | ⏸ |
| **A-02** | **Carriage / selector board** | **Confirmed not recovered** — see §5 | ⛔ |
| **B-01** | Carriage / selector board photographs | The board does not exist to photograph | ⛔ |
| **B-03** | PSU units 3 & 4 nameplates | They were in the cabinet | ⏸ |
| **B-04** | Servo amplifier nameplates | They were in the cabinet | ⏸ |
| **B-15** | Machine nameplate | On the frame | ⏸ |
| **C-01** | Actual output voltage of the NES-350-27 units | Supplies were in the cabinet | ⏸ |
| **C-05** | AC input selector position on each Mean Well unit | Same | ⏸ |
| **C-10** | `J1` signal name → physical sensor, via status LEDs | Needs the machine's sensors wired up | ⏸ |
| **F-02** | OEM operator or service manual | **Manufacturer identified and located: Shaoxing Jinhao Machinery, Keyan Industrial Area, Shaoxing, Zhejiang** — call `0575-84291942` or visit `jindajx.com` directly. See [reference/README.md](../reference/README.md) | ☐ |
| **F-03** | Machine history — was the 2013 Racking Board a field replacement? | Needs records or the previous owner | ⏸ |
| **F-07** | **Source a donor selector board** — try the OEM directly first (see F-02's contact), or a scrapped Shaoxing Jinhao GD-H122S | Independent of this carcass — see §5 | ☐ |

---

## 5. CLOSED — unrecoverable and unobtainable

**These are not pending. They will not be resolved.** Recorded so nobody
re-opens them or plans work that assumes them.

| ID | Item | Status | Consequence |
|---|---|---|---|
| **B-13** | Cabinet as-found, doors open, connectors seated | ⛔ **CLOSED — UNRECOVERABLE** | The boards were removed before any photograph was taken |
| **B-14** | Every cable, both ends, before disconnection | ⛔ **CLOSED — UNRECOVERABLE** | Same |
| **A-02** | Carriage / selector board, physical | ⛔ **CLOSED — UNOBTAINABLE** | Never recovered from the machine head |
| **B-01** | Carriage / selector board photographs | ⛔ **CLOSED — UNOBTAINABLE** | Follows from A-02 |
| **C-02** | HMI `J15` input voltage, **measured in the live machine** | ⛔ **CLOSED — UNRECOVERABLE** | Superseded by **C-12**, which determines it by tracing instead |

### What B-13 and B-14 cost

The interconnection record is gone permanently. Three consequences, all
now-permanent unknowns:

1. **Which board drove the carriage / selector link is unknown.** The HMI has two
   bus channels and the Main Board has an unassigned 40-pin IDC. Either could have
   fed the head. Without the harness there is no way to tell from the boards alone
   — C-14 and B-16 can narrow it, but not settle it.
2. **Rail assignment per board is unknown.** Which supply fed which board, and on
   which terminal pin, cannot be recovered. Every board must be powered from a
   bench supply on determined-by-tracing voltages, not remembered ones.
3. **Sensor-to-signal mapping is gone.** `J1`'s 20-odd silkscreened names
   (`YTL`, `AZL`, `P-U`…) can no longer be tied to physical sensors by observation.
   Their meanings stay `[I]` inferences permanently.

This is the single largest avoidable loss in the project. It is recorded here so
the cost is visible, not to relitigate it.

---

## 6. Documents — all free, none blocked

**No machine access needed. These can all be done today.** Fetch list:
[../datasheets/README.md](../datasheets/README.md).

| ID | Document | Pri | Status |
|---|---|:--:|:--:|
| E-01 | **Lattice Diamond Programmer** (free) | P1 | ☐ |
| E-02 | Lattice MachXO, LatticeXP, MachXO2 datasheets + sysCONFIG guides | P1 | ☐ |
| E-03 | TI AM26LV31C / AM26LV32C / AM26LS31C datasheets | P1 | ☐ |
| E-04 | 6N137 datasheet | P1 | ☐ |
| E-05 | TI DRV8818 datasheet | P2 | ☐ |
| E-06 | ULN2804A, TLC5620C datasheets | P2 | ☐ |
| E-07 | **Mitsubishi MR-J series manual** | P1 | ☐ |
| E-08 | Samsung S3C2440A user manual | P2 | ☐ |
| E-09 | Mean Well NES-350-27 datasheet | P2 | ☐ |
| E-10 | Mean Well LRS-350-24 datasheet — verify trim reaches 27 V | P2 | ☐ |
| E-11 | Supporting: LM324/358/339/393, 74AHCT245, 74HC245, 74LVC07A, 74LVC14A, MAX3232, PC357, HFD23, AO4606, UF5408 | P3 | ☐ |

**E-07 is now more important than before.** The servo interface is the only part
of this system that remains a documented standard, and it needs no machine, no
carcass and no protocol decode. It is the most solid ground left.

---

## 7. Information

| ID | Need | Status |
|---|---|:--:|
| **F-08** | **A drop-in replacement for *which* machine?** The host machine is dead and dismantled. See §1.1 — this outranks everything, including the Route A / Route B decision | ☐ |
| **F-06** | **Does the dead machine carcass still exist anywhere?** Gates 12 items — see §1 | ☐ |
| **F-04** | What prompted this project — a specific board failure, or obsolescence? | ☐ |
| **F-07** | Can a donor selector board be sourced — **from the OEM directly (Shaoxing Jinhao Machinery, contact in reference/README.md), or from a scrapped Shaoxing Jinhao GD-H122S**? | ☐ |
| **F-01** | Translation of jumper `P1`'s Chinese annotations — needs B-05 first | ☐ |
| **F-02** | OEM operator or service manual for the Shaoxing Jinhao GD-H122S — **manufacturer identified, contact info in reference/README.md** | ☐ |
| **F-05** | ~~Is the machine running or dead?~~ | ☑ **Answered: dead** |
| **F-03** | Machine history — 2013 Racking Board a field replacement? | ⏸ |

---

## 8. Work order

```text
NOW ─────────────────────────────────────────────────────────────
  Answer F-08 (replacement for WHAT?)  ← outranks everything; see 1.1
  Answer F-06 (carcass still exist?)   ← gates 12 items, one phone call
  Call the OEM (F-02/F-07)             ← Shaoxing Jinhao, 0575-84291942 or
                                          jindajx.com — spares + manual, one call
  Download E-01 … E-11                 ← free, unblocked, in parallel
  Buy D-01, D-02, D-07                 ← under US$60

TIER 1 ── bench-safe, unpowered ─────────────────────────────────
  B-05 … B-12, B-16               ← macro photography
  C-07, C-11, C-04, C-09, C-08    ← continuity mapping
  C-06, C-12, C-14                ← rail checks + tracing

TIER 2 ── powered bench ─────────────────────────────────────────
  Buy D-06, D-03
  C-15 (IDCODE) → C-16 (readback) ← THE DECISION GATE
  C-13 (does the master transmit with no peers?)
  C-03, D-05

  ├── readback works ──────────→ Route A, see 05 §7
  └── read-protected ──────────→ FALLBACK, see 05 §10

TIER 3 ── only if F-06 = yes ────────────────────────────────────
  B-03, B-04, B-15, C-01, C-05, C-10, F-03

CLOSED ── do not plan around ────────────────────────────────────
  B-13, B-14  (interconnection record)
  A-02, B-01  (selector board)
  C-02        (superseded by C-12)
```

---

## 9. Progress

| Tier | Open | Conditional | Closed | Total |
|---|:--:|:--:|:--:|:--:|
| Tier 1 — unpowered bench | 21 | 0 | 0 | 21 |
| Tier 2 — powered bench | 8 | 0 | 0 | 8 |
| Tier 3 — carcass-dependent | 2 | 8 | 2 | 12 |
| Documents | 11 | 0 | 0 | 11 |
| Information | 6 | 1 | 1 | 8 |
| Closed | — | — | 5 | 5 |
| **Total** | **48** | **9** | **7** | **64** |

Nothing in §6 is blocked. Tier 1 needs under US$60 and no machine.

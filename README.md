# Jacquard Machine — Control Electronics Reverse Engineering

Teardown reference and reverse-engineering workspace for the control electronics
of a **Julong GDH122SA** jacquard / flat-knitting machine.

**Objective:** build a **drop-in replacement controller**.

The photographs are the primary record. Everything else in this repository is the
decode of what is in them, with each claim marked for how firmly it is known.

---

## ⚠ Project status

| | |
|---|---|
| **Machine** | **DEAD and dismantled.** Boards removed before any cable or cabinet photographs were taken |
| **Boards in hand** | **3** — HMI, Main, Racking |
| **Carriage / selector board** | ⛔ **UNOBTAINABLE** — never recovered from the machine head |
| **Interconnection record** | ⛔ **UNRECOVERABLE** — no harness or cabinet photographs exist |
| **Tools in hand** | None |

**Two consequences that shape everything else:**

1. **Needle selection cannot be reverse-engineered.** The selector board is gone,
   so that half of the system must be **re-engineered from scratch** —
   [docs/05](docs/05-replacement-controller-plan.md) §10. Treat the three
   documented boards as roughly half the control system.
2. **Which board drove the carriage is now itself unknown.** With no harness
   record, the HMI's two bus channels and the Main Board's unassigned 40-pin IDC
   are both candidates. Tracing narrows it; nothing settles it.

> **A site visit is planned.** Several ⛔ and ⏸ items above could reopen if the
> carcass, a sister machine, or a donor selector board turns up. Take
> [docs/09-field-checklist.md](docs/09-field-checklist.md).

**Open question that outranks all others:** a drop-in replacement for *which*
machine? The original host is gone. See
[docs/08](docs/08-needed-items.md) §1.1 (**F-08**).

---

## Evidence key

Used consistently throughout every document. Reverse engineering goes wrong when
observation and inference get blurred, so they are kept apart here.

| Mark | Meaning |
|---|---|
| `[P]` | **Photograph.** Read directly off a part marking or silkscreen. Reliable |
| `[I]` | **Inferred.** From datasheet role, package, and board topology. A working hypothesis, not a fact |
| `[?]` | **Unknown.** Needs more evidence |

---

## Start here

| If you want to… | Read |
|---|---|
| Understand the system in five minutes | [docs/04-interconnection-map.md](docs/04-interconnection-map.md) |
| Know what every chip does | [docs/01-ic-master-list.md](docs/01-ic-master-list.md) |
| Understand the machine bus | [docs/02-communication-protocol.md](docs/02-communication-protocol.md) |
| Know the power design | [docs/03-power-architecture.md](docs/03-power-architecture.md) |
| **Start actual work today** | [docs/05-replacement-controller-plan.md](docs/05-replacement-controller-plan.md) |
| Know what is still unknown | [docs/06-open-questions.md](docs/06-open-questions.md) |
| **Images I need from you** | [docs/07-photo-shot-list.md](docs/07-photo-shot-list.md) |
| **See everything the project still needs** | [docs/08-needed-items.md](docs/08-needed-items.md) |
| **Go to the factory — take this with you** | [docs/09-field-checklist.md](docs/09-field-checklist.md) |
| **Printable request — questions + marked photos** | [Jacquard-Information-Request.pdf](Jacquard-Information-Request.pdf) |

---

## The system in brief

Three control boards, one CPU between them, and an FPGA on each.

| Board | ID | Main device | Role |
|---|---|---|---|
| [01 HMI](boards/01-hmi-board/README.md) | *GOLDEN COREBOARD_V1.20* mezzanine, 2010-11-30 | Samsung S3C2440AL ARM920T + Lattice MachXO LCMXO1200C | Operator panel and **machine-bus master**. Display, keypad, Ethernet, USB, serial |
| [02 Main](boards/02-main-board/README.md) | `JITIBAN-4`, 2012-07-17 | Lattice LatticeXP LFXP3C (`U28`) | Machine I/O. 6 stepper channels, ~36–38 isolated inputs, 16 Darlington outputs, quad DAC, safety relay |
| [03 Racking](boards/03-racking-board/README.md) | `SWG-Yarn-A-02`, 2013 | Lattice MachXO2-7000HC | Racking / yarn-carrier node. Interfaces **2 AC servo axes**. Fully isolated |
| [04 Power](boards/04-power-supply/README.md) | — | 2 × Mean Well NES-350-27 | **No custom PSU PCB.** Bought-in supplies; each board regulates locally |

**Three architectural facts that govern any replacement:**

1. **One FPGA per board, exactly one CPU in the system.** Hard real-time needle
   and carriage timing lives in fabric. Two of the three boards have no processor
   at all.
2. **The machine bus is vendor-private.** Not Modbus, not CAN, not RS-485. A
   custom clocked serial link over isolated RS-422 — "isolated SPI over RS-422".
   No adapter exists; no decoder exists.
3. **The rail silkscreened `+24V` is actually fed at 27 V.** Deliberate, and it
   changes component ratings and bench-test setup.

---

## Layout

```text
Jacquard machine/
├── README.md                          this file
├── Jacquard-Information-Request.pdf   printable: all questions + marked board photos
│
├── boards/
│   ├── 01-hmi-board/                  operator panel + CPU mezzanine
│   │   ├── README.md                  IC list, connectors, power
│   │   └── images/{overview,ics}/
│   ├── 02-main-board/                 machine I/O motherboard "JITIBAN-4"
│   │   ├── README.md
│   │   └── images/{overview,ics,reference}/
│   ├── 03-racking-board/              racking / yarn node "SWG-Yarn-A-02"
│   │   ├── README.md
│   │   └── images/{overview,ics}/
│   └── 04-power-supply/               PSU rack (no custom PCB)
│       ├── README.md
│       ├── images/overview/
│       └── reference/                 candidate replacement specs
│
├── docs/
│   ├── 01-ic-master-list.md           every IC, every board, one line each
│   ├── 02-communication-protocol.md   the machine bus + RE attack surface
│   ├── 03-power-architecture.md       input/output power at every level
│   ├── 04-interconnection-map.md      board-to-board and peripheral map
│   ├── 05-replacement-controller-plan.md   the phased build plan
│   ├── 06-open-questions.md           what is not known, ranked
│   ├── 07-photo-shot-list.md          every image needed, and what each unlocks
│   ├── 08-needed-items.md             everything not yet in hand — the master checklist
│   └── 09-field-checklist.md          printable walk-in checklist for a site visit
│
├── datasheets/                        vendor PDFs (fetch list in README)
├── captures/                          continuity tables, logic captures
└── reference/                         cabinet shots, nameplates, translations
```

Image filenames are the **as-photographed silkscreen marking of the chip**,
transcribed line by line and joined with hyphens:

```text
11DLKTK-G4-AM26LV32C.jpg
└─lot──┘ └┘ └─part number─┘
         └ RoHS / lead-free suffix
```

* The **part number** is the segment that looks like a catalogue number
  (`AM26LV32C`, `LM324`, `TLC5620C`); the rest is date / lot / assembly code.
* `-G4` is TI's lead-free package suffix, not part of the type.
* A trailing `(2)` in a filename marks a second angle of the same part, not a different device.
* A few filenames contain transcription slips, corrected in the board READMEs.
  **Trust the image over the filename.**

---

## What you can do right now

Three boards on a bench, no machine, no tools. That is still enough for the
project's whole critical path — **Tier 1 is unpowered bench work and needs under
US$60.** Full register: [docs/08-needed-items.md](docs/08-needed-items.md).

| # | Task | Cost |
|---|---|---|
| 1 | **Answer F-08** — a replacement for *which* machine? The original host is gone | — |
| 2 | **Answer F-06** — does the machine carcass still exist anywhere? Gates 12 items | — |
| 3 | Download every datasheet (E-01 … E-11) — free, unblocked | — |
| 4 | Buy a multimeter, probes, and set up a camera | <US$60 |
| 5 | Board intake: record `S1` and `P1` switch positions **as found**, rail resistance checks | — |
| 6 | Macro photography — boot flash, jumper `P1`, stepper ICs, unidentified ICs, pin-1 marks | — |
| 7 | Continuity mapping — transceiver pin to connector pin (**C-07**) | ~2–3 days |
| 8 | **Start sourcing a donor selector board (F-07)** — calendar time, not working time | — |
| 9 | Order an HW-USBN-2B clone; run the FPGA IDCODE scan and readback | ~US$25 |

Item 5 matters more than it looks: with the harness gone, the DIP switch and
jumper positions are among the only surviving evidence of how this machine was
configured. **Record them before anyone moves them.**

Full procedures: [docs/05-replacement-controller-plan.md](docs/05-replacement-controller-plan.md).
Master checklist of everything still needed: [docs/08-needed-items.md](docs/08-needed-items.md).

---

## Known gaps

The three largest, in order:

1. ⛔ **The carriage / needle-selector board is unobtainable.** It was never
   recovered from the machine head. Nothing in hand can drive a needle bed, and
   this cannot be reverse-engineered — only re-engineered
   ([docs/05](docs/05-replacement-controller-plan.md) §10). Any estimate that
   excludes it is likely about half the real figure.
2. ⛔ **The interconnection record is unrecoverable.** The boards were removed
   before any cable or cabinet photographs were taken. Rail assignment, harness
   routing, and sensor-to-signal mapping are permanently gone.
3. **Machine-bus framing is undetermined,** and with the machine dead there may be
   no traffic to capture. **C-13** — does the bus master still transmit on a bench
   with no peers attached? — is the most important untested assumption left.

Full list: [docs/06-open-questions.md](docs/06-open-questions.md).

---

## Safety

* The PSU rack is at **mains potential** and the cabinet wiring is aged, dusty and
  partly unsleeved. Isolate and verify zero volts before touching anything.
* The HMI carries a **CCFL backlight inverter** marked `DANGER HIGH VOLTAGE`
  (`HQ-01-1S015REV1.0`). It produces several hundred volts AC from a low-voltage
  input and is live whenever the HMI is powered — independent of mains.
* Bulk capacitors hold charge after disconnection. Wait, then verify.
* Bench-power boards at **27 V**, not 24 V. The HMI is the exception — its input
  voltage is not silkscreened anywhere and the machine is gone, so it cannot be
  measured in situ. **Determine it by tracing first** (`J15` → `D3` → `CE0` → `U20`);
  its main regulator is linear, and over-volting it destroys the part immediately.

---

## How to read the analysis

* Part **markings** are transcribed from photographs and are reliable.
* Part **functions**, the topology diagram, and anything marked `[I]` are read off
  standard datasheet roles and board layout — not off vendor documentation for
  this machine. They are working hypotheses, and
  [docs/02-communication-protocol.md](docs/02-communication-protocol.md) §8 states
  what would prove the central ones wrong.
* Reference designators (`U28`, `J1`, `S1`, …) come from silkscreen visible in the
  overview shots.
* The **Julong GDH122SA** attribution comes from the machine owner. No make, model
  or OEM name is silkscreened on any board photographed here.

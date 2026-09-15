# Board 01 — HMI Board (operator panel head)

> The "control side" of the system. A two-board stack inside a sheet-metal
> display bezel: a **carrier board** holding all the I/O, plus a plug-in
> **CPU mezzanine** silkscreened `GOLDEN COREBOARD_V1.20` / `1010.11.30`
> (30 November 2010).
>
> No board ID is silkscreened on the carrier.

**Evidence key** — `[P]` read directly off a photograph · `[I]` inferred from
datasheet role + layout · `[?]` unknown.

Overviews: [with core board](images/overview/Top%20View%20With%20Core%20Board.jpg) ·
[carrier alone](images/overview/Top%20View%20without%20CoreBoard.jpg)

---

## 1. What this board is

The only board in the system with a CPU. It runs the operator interface, holds
the pattern files, talks to the outside world over Ethernet and USB — and acts as
the **master of the machine bus** that drives the Main Board and the Racking Board.

The split is deliberate and worth stating plainly:

* **ARM (S3C2440AL)** — user interface, file handling, networking. Soft real-time.
* **FPGA (MachXO LCMXO1200C)** — machine bus master, hard real-time timing.

If you are replacing this board, those are two separable jobs.

---

## 2. IC list

### 2.1 On the CPU mezzanine (`GOLDEN COREBOARD_V1.20`)

| Part | Package | Function | Image |
|---|---|---|---|
| Samsung **S3C2440AL-40** | TQFP/FBGA | ARM920T application processor, 400 MHz grade. This part normally carries embedded Linux or Windows CE. Lot `A1131` `[P]` | [S3C2440AL](images/ics/SAMSUNG-S3C2440AL-40-A1131-ARM-N3HYHG.jpg) |
| Samsung **K4S561632N-LC75** | TSOP-54 | 256 Mbit (16M × 16) SDRAM, 133 MHz — main system memory `[P]` | [K4S561632N](images/ics/SAMSUNG-125-K4S561632N-LC75-S5616-YAE0426UN.jpg) |
| Cypress **CY62128EV30LL-45ZXI** | TSOP-32 | 1 Mbit (128K × 8) asynchronous SRAM, 45 ns, low-power — scratch / buffer memory `[P]` | [CY62128EV30LL](images/ics/CY62128EV30LL-45ZXI-1313-K-04-611728.jpg) |
| **Boot flash** | — | NAND or NOR. **Marking never captured.** Without it the firmware storage type and size are unknown — and this is the single highest-value target for reverse engineering `[?]` | *missing* |

### 2.2 On the carrier board

| Ref | Part | Package | Function | Image |
|---|---|---|---|---|
| `U1` | Lattice **LCMXO1200C-3TN144C** | TQFP-144 | MachXO 1200-LUT **non-volatile** FPGA/CPLD. Glue logic plus the machine-bus master. Lot `A3141RV9` `[P]` | [LCMXO1200C](images/ics/LATTICE-LCMX01200C-3TN144C-A3141RV9.jpg) |
| `U19` | Davicom **DM9000AEP** | LQFP-48 | 10/100 Ethernet MAC **and** PHY on one die, ISA-like bus to the CPU. Feeds the HanRun RJ45 magjack `[P]` | [DM9000AEP](images/ics/DAVICON-DM9000AEP-1112NS-HN67J-007.jpg) |
| — | Maxim **MAX3232CSE** | SOIC-16 | 3 V dual RS-232 transceiver — serves the console / service serial port `[P]` | [MAX3232CSE](images/ics/MAX3232-CSE-1221%2B.jpg) |
| `U14` `U16` `U13` | TI **AM26LV31C** | SOIC-16 | Quad 3.3 V RS-422 differential line **driver** — machine bus outbound. Three fitted `[P]` designators read in situ | [AM26LV31C](images/ics/11AV9GM-AM26LV31C-G4.jpg) |
| `U11` | TI **AM26LV32C** | SOIC-16 | Quad 3.3 V RS-422 differential line **receiver** — machine bus inbound `[P]` | [AM26LV32C](images/ics/11CL00K-G4-AM26LV32C.jpg) |
| `U12` `U15` | **6N137** | DIP/SOIC-8 | 10 Mbit/s logic-gate optocoupler — galvanic isolation on the machine link. Two fitted, lot `1119T1` `[P]` | [6N137](images/ics/6N137-1119T1.jpg) |
| — | TI **LM393** | SOIC-8 | Dual differential comparator `[I]` threshold detection | [LM393](images/ics/LM393-11A-P0Z5-G4.jpg) |
| — | UTC **TL494** | SOIC-16 | PWM switching controller — local DC-DC / backlight supply `[I]` | [TL494](images/ics/UTC494-J0C42B.jpg) |
| `U8` | — | SOIC | **Unpopulated option site** `[P]` — the footprint is empty in [carrier alone](images/overview/Top%20View%20without%20CoreBoard.jpg) |

### 2.3 Power devices (see section 4)

| Ref | Part | Package | Function | Image |
|---|---|---|---|---|
| `U20` | Sipex **SP29302T5** | TO-263-5 | 3 A adjustable very-low-dropout regulator. Lot `1019LC919`. *The filename reads "sidex"; the logo on the package is **Sipex**.* `[P]` | [SP29302T5](images/ics/sidex-29302T5-1019LC919.jpg) |
| `U21` `U22` | **AMS1117** family | SOT-223 | 1 A LDO linear regulators, two fitted. Output-voltage variant not determinable from the marking `[P]` package · `[?]` variant | [1117](images/ics/1117M3-1002L-C251480.jpg) |
| `U_D7` | **FU120N** | DPAK / TO-252 | Marked `FU120N` / `141P1HR4`. Sits between the DC rail and connector `J3`, gate driven by `Q2`/`Q3`/`Q4` (SOT-23, marked `J3Y` = MMBT3904 class). `[I]` **power MOSFET switching the CCFL backlight inverter** — that is, the software brightness / blanking switch | [FU120N](images/ics/FU120N-141P1HR4-%2843%29.jpg) |
| — | Alpha & Omega **AO4606** | SO-8 | Complementary N+P channel MOSFET pair — load switch / reverse-polarity protection `[P]` part · `[I]` role | [AO4606](images/ics/4606-0J2H.jpg) |

---

## 3. Interfaces and connectors

### 3.1 Off-machine

| Interface | Detail |
|---|---|
| **Ethernet** | HanRun `HR911105A` RJ45 magjack, date code `10/39`, behind the DM9000AEP `[P]` |
| **USB host** | One type-A socket on a small daughter PCB at `J6` / `J7` `[P]` |
| **Serial ×3** | Three DB9 connectors: `J9`, `J8`, `J13` `[P]` |

> The three DB9s are **not** three RS-232 ports. Only one MAX3232 is fitted,
> against three AM26LV31C drivers and one AM26LV32C receiver. `[I]` **One DB9 is
> the RS-232 console; the other two carry the RS-422 machine bus.** Confirm by
> continuity before connecting anything — see
> [docs/05](../../docs/05-replacement-controller-plan.md).

### 3.2 Display and operator

| Item | Detail |
|---|---|
| LCD | FPC ribbon connector plus an `HZS1` / `HZSJ` adapter card `[P]` |
| Backlight | CCFL inverter, silkscreened `HQ-01-1S015REV1.0`, marked `DANGER HIGH VOLTAGE`. Fed from `J3` via `U_D7` `[P]` |
| Keypad | 40-pin IDC ribbon at `J12`, labelled `COM…`, to the membrane keypad `[P]` |
| Contrast | Trimpot `R15` `[P]` |
| Audible | Magnetic buzzer `[P]` |
| Config | 4-way DIP switch `SW1`, plus a `JTAG` header `[P]` |

### 3.3 Machine bus test points

Two rows of plated test points sit directly below the RS-422 transceivers `[P]`:

```
row 1:   MK1   MO1   MS1   MIK1   MIK2
row 2:   MS2   MO2   MK2   SO1    SO2    SK1   SK2
```

Silkscreened signal groups elsewhere on the board follow the same convention `[P]`:

```
MSI / MSO / MSK      SDI / SDO / SDK      MDI / MDO / MDK
HSI / HSO / HSK      MKI / MKO / MKK
```

The pattern is a repeated **three-wire group with `I` / `O` / `K` suffixes**
(in, out, clock — `K` for *Klock*), numbered `1` and `2` for two independent
channels. This is the key to the whole protocol; the analysis is in
[docs/02-communication-protocol.md](../../docs/02-communication-protocol.md).

**These test points are the best physical probe point in the entire system.**
They are single-ended, plated, accessible with the lid off, and they sit on the
logic side of the transceivers.

---

## 4. Power

| Item | Detail | Evidence |
|---|---|---|
| Inlet | `J15` — 2-pin through-hole header, white polarised plug | `[P]` |
| Protection | Fuse `F1`, ferrite/choke `FL1`, series diode `D3`, bulk electrolytic `CE0` | `[P]` |
| Main regulator | `U20` Sipex SP29302T5, 3 A LDO on a large thermal land | `[P]` |
| Secondary regulators | `U21`, `U22` — AMS1117-class SOT-223 LDOs | `[P]` |
| Backlight switch | `U_D7` (FU120N) driven by `Q2`/`Q3`/`Q4`, feeding `J3` → CCFL inverter | `[I]` |
| Secondary fuse | `F2` | `[P]` |

Input voltage is **not silkscreened** near `J15`. `[I]` most likely 12 V or 24 V
from the cabinet supply rack. **Measure it before powering this board from a bench
supply** — see [docs/03-power-architecture.md](../../docs/03-power-architecture.md).

---

## 5. Why this board matters most for reverse engineering

1. It is the only board with **firmware in flash** rather than in FPGA fabric.
2. It holds the **bus master**, so it defines the protocol the other boards obey.
3. It has the only **exposed standard interfaces** (Ethernet, USB, RS-232) — and
   an S3C2440 that will normally talk over its UART at boot.
4. It has the only **accessible single-ended test points** for the machine bus.

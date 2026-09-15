# 04 — System Interconnection Map

How the boards connect to each other and to the machine's peripherals.

**Evidence key** — `[P]` read directly off a photograph · `[I]` inferred ·
`[?]` unknown.

---

## 1. Topology

```
                         ┌───────────────────────────────┐
     AC mains ──────────►│  PSU RACK                     │
   100-120 / 200-240 V   │  2 × Mean Well NES-350-27     │
                         │      27 V, 13 A, 350 W each   │
                         │  2 × unidentified SMPS  [?]   │
                         └───────────────┬───────────────┘
                                         │  27 V / 12 V DC distribution
             ┌───────────────────────────┼───────────────────────────┐
             │                           │                           │
             ▼                           ▼                           ▼
 ┌───────────────────────┐   ┌───────────────────────┐   ┌───────────────────────┐
 │ 01  HMI BOARD         │   │ 02  MAIN BOARD        │   │ 03  RACKING BOARD     │
 │                       │   │     "JITIBAN-4"       │   │     "SWG-Yarn-A-02"   │
 │ S3C2440AL  ARM920T    │   │ LatticeXP LFXP3C      │   │ MachXO2-7000          │
 │ MachXO LCMXO1200C     │   │  (no CPU)             │   │  (no CPU)             │
 │                       │   │                       │   │                       │
 │  ** BUS MASTER **     │   │                       │   │  DIP S1 = node addr   │
 └──────────┬────────────┘   └───────────┬───────────┘   └───────────┬───────────┘
            │                            │                           │
            │  isolated RS-422 machine bus  (6N137 + AM26LV31/32)     │
            ├────────────────────────────┤                           │
            │        channel 1           │                           │
            └────────────────────────────┴───────────────────────────┘
                     channel 2            COM1 / COM2 daisy chain
```

> ⚠ **The harness is gone**, so this topology can no longer be confirmed by
> inspection — only narrowed by continuity on the boards themselves.

`[I]` **Two branches, not one chain.** The HMI's test points come in matched `…1`
and `…2` pairs, and three quad RS-422 drivers are fitted against one quad
receiver — see [02-communication-protocol.md](02-communication-protocol.md) §2.4.
Confirm by continuity (**C-07**, **C-14**) before relying on it.

---

## 2. Peripheral map

The user's peripheral list — AC servo, DC servo, stepper, sensors, HMI — maps
onto the boards as follows.

| Peripheral | Board | Interface | Ev |
|---|---|---|:--:|
| **AC servo ×2** | 03 Racking | 34-pin IDC. Mitsubishi MR-J-style: `xLA`/`xLAR`/`xLB`/`xLBR` encoder pairs, `xPG` pulse command, `xINP` in-position, `xRDY` ready. Axis prefixes `M` and `B` | `[P]` names · `[I]` mapping |
| **Stepper ×6** | 02 Main | Six HTSSOP-28 bipolar driver channels, each with two 0.25 Ω sense resistors and its own bulk capacitor. Outputs leave on the left-edge IDC headers | `[P]` |
| **DC servo** | 02 Main | `[I]` **Analog velocity command from the TLC5620 quad DAC**, with LM324 / LM358 conditioning and LM339 threshold detection on the tacho/feedback return. `ROE1` on `J2` `[I]` = roller enable. This is the classic take-down / roller drive topology | `[I]` |
| **Sensors** | 02 Main | ~36–38 isolated 24 V inputs via Cosmo 357NT, landing on `J1` and `J2`. **Every input has its own status LED** | `[P]` |
| **HMI / operator** | 01 HMI | LCD over FPC + `HZS1` adapter · CCFL backlight via `J3` · 40-pin membrane keypad at `J12` · buzzer · contrast trimpot `R15` | `[P]` |
| **Yarn carriers** | 03 Racking | Bottom-edge 34-pin IDC, `SR2` and `SL` headers | `[P]` presence · `[?]` detail |
| **Safety / enable** | 02 Main | HFD23-005-1ZS relay, 1 Form C, silkscreen `J_C2`/`D_C2` | `[P]` part · `[I]` role |
| **Needle selectors** | — | **NOT ON ANY PHOTOGRAPHED BOARD** — see §5 | — |

---

## 3. Connector inventory

### Board 01 — HMI

| Ref | Type | Goes to | Ev |
|---|---|---|:--:|
| `J15` | 2-pin header | DC power in from the cabinet | `[P]` |
| `J3` | 4-pin | CCFL backlight inverter (`HQ-01-1S015REV1.0`) | `[P]` |
| `J9` `J8` `J13` | 3 × DB9 | `[I]` one RS-232 console + two RS-422 machine bus | `[I]` |
| `J12` | 40-pin IDC | Membrane keypad | `[P]` |
| `J6` / `J7` | USB type-A | Pattern-file transfer (daughter PCB) | `[P]` |
| — | RJ45 (HanRun HR911105A) | Ethernet, 10/100 | `[P]` |
| — | FPC | LCD panel, via `HZS1`/`HZSJ` adapter | `[P]` |
| `J5` | header | `[?]` | `[?]` |
| `JTAG` | header | Lattice MachXO configuration | `[P]` |
| `SW1` | 4-way DIP | `[?]` config or address | `[P]` |

### Board 02 — Main

| Ref | Type | Goes to | Ev |
|---|---|---|:--:|
| screw terminal | 4-pin | DC power in | `[P]` |
| 40-pin IDC, top | ribbon | `[I]` machine bus toward HMI, or the carriage board | `[I]` |
| `J1` | IDC, lower right | Field sensor inputs — see §4 | `[P]` |
| `J2` | IDC, upper right | Enable / status group — see §4 | `[P]` |
| `JP2` + 3 more, left edge | IDC | `[I]` six stepper channels out, encoder returns in | `[I]` |
| `J1AG2` | header | Lattice LatticeXP JTAG | `[P]` |
| `P1`, 2-pos jumper | jumper | **Mode select — 4 modes.** All four states of a 2-bit selector are enumerated. Chinese annotations not yet readable | `[P]` |

### Board 03 — Racking

| Ref | Type | Goes to | Ev |
|---|---|---|:--:|
| screw terminal | 4-pin | DC power in | `[P]` |
| `COM1` / `COM2` | 2 × 20-pin IDC | Machine bus in / out — daisy chain | `[P]` presence · `[I]` roles |
| 34-pin IDC, left | ribbon | **Two AC servo amplifiers** | `[P]` |
| 34-pin IDC, bottom | ribbon | `[?]` second I/O group |
| `SR2`, `SL` | headers | `[I]` right / left limit or sensor groups |
| `S1` | 4-way DIP | **Node address** — ≤ 16 nodes | `[I]` |
| `CON1` | 10-pin | MachXO2 configuration | `[I]` |

---

## 4. Field signal reference — Main Board

Read from silkscreen `[P]`; meanings are `[I]`.

### `J1` — sensor inputs, each with a status LED

| Signal | Likely meaning |
|---|---|
| `UL1` `UL2` `UL3` `UL4` | Four-channel limit group — carriage / cam limits |
| `SLINE` `LLINE` | Short line / long line — knitting-width or traverse-end |
| `BT` | Belt / button / brake-test |
| `SMBRL` | Stepper-motor brake release |
| `SMF` | Stepper-motor fault |
| `AC2` `AC3` `AC4` | Cam-position / carriage-position sense group |
| `P-D` `P-U` | Presser down / presser up |
| `AZL` | A-axis zero limit — home reference |
| `YTL` `YTR` | Yarn tension left / right |
| `YDL` | Yarn detect left |
| `LPZ` `SZL` | Zero-position references |

### `J2` — enable and status

| Signal | Likely meaning |
|---|---|
| `UPS-T` | **Power-fail / UPS warning** — triggers an orderly park. See [03](03-power-architecture.md) §5 |
| `D1` `S` | Data / strobe |
| `SRE1` | Servo-ready enable |
| `ROE1` | Roller enable — `[I]` the DC servo take-down drive |
| `AE1` | Axis enable |
| `SROR` | Servo run / over-run |

---

## 5. The missing board — ⛔ UNOBTAINABLE

> **Status:** the carriage / selector board was **never recovered** from the
> machine head, and the machine is dismantled. It cannot be photographed or
> probed. Needle selection must be **re-engineered, not reverse-engineered** —
> [05](05-replacement-controller-plan.md) §10.
>
> **A second loss compounds this.** The boards were removed before any cable or
> cabinet photographs were taken, so the harness record is gone. That means
> **which board drove the carriage is itself now unknown** — the HMI has two bus
> channels and the Main Board has an unassigned 40-pin IDC, and either could have
> fed the head. Tracing (**C-14**, **B-16**) narrows it; nothing settles it.
>
> Full analysis: [06-open-questions.md](06-open-questions.md) §1.

**Needle selection is not driven from any board in this photo set.**

A jacquard / flat-knitting needle bed needs hundreds of selector actuators. The
total actuator capability across all three boards is:

* 16 Darlington channels (2 × ULN2804A) on the Main Board,
* 1 relay contact,
* 6 stepper phases,
* 2 servo pulse-train outputs.

That is nowhere near enough. `[I]` **There is a carriage-mounted selector driver
board**, fed over the flexing cable that runs to the traversing head, and it has
not been opened or photographed.

**This is the largest single gap in the project.** For a drop-in replacement it is
also the hardest part, because needle-selection timing is what the whole machine
is built around. Anyone scoping this work should treat the three documented boards
as roughly half the problem.

See [06-open-questions.md](06-open-questions.md).

---

## 6. Design language across the boards

Three consistent patterns, which make the architecture readable even without
schematics:

**One FPGA per node, exactly one CPU.** MachXO on the HMI, LatticeXP on the
motherboard, MachXO2 on the racking node — all non-volatile parts. Only the HMI
has a processor, and it sits on a swappable mezzanine. Hard real-time needle and
carriage timing lives in fabric; the ARM handles UI, files and networking.

**Isolation everywhere the machine touches the electronics.** Every machine-facing
signal crosses either a 6N137 (fast, for the bus) or a Cosmo 357NT (slow, for
field I/O). With inverter drives and solenoid switching on the same frame, this is
not optional.

**Differential everything on long runs.** RS-422 pairs for the bus and for the
servo encoder returns. The head traverses the full bed on a flexing cable; a
single-ended signal would not survive it.

A replacement controller that abandons any of these three will work on the bench
and fail on the machine.

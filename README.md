# Jacquard Machine — Control Electronics Teardown Reference

Photographic reference for the three control PCBs recovered from an electronic
Jacquard / flat-knitting machine controller. Each folder holds one board:
overview shots of the assembled PCB plus close-up macros of every identifiable
integrated circuit, so part numbers can be read without re-opening the machine.

The images are the primary record. This README is the index and the decode of
what is in them.

---

## 1. Contents at a glance

| Folder | Board silkscreen ID | Main logic device | Images | Role (inferred) |
|---|---|---|---|---|
| [HMI-BOARD/](HMI-BOARD/) | *no board ID visible; carries a `GOLDEN COREBOARD_V1.20 / 1010.11.30` mezzanine* | Samsung S3C2440AL ARM920T + Lattice MachXO LCMXO1200C | 17 | Operator panel / HMI head — display, keypad, Ethernet, USB, serial |
| [Main Board/](Main%20Board/) | `JITIBAN-4  2012-07-17` | Lattice LatticeXP LFXP3C (`U28`) | 19 | Machine I/O motherboard — solenoid drive, stepper, analog sense, RS-422 fan-out |
| [Racking Board/](Racking%20Board/) | `SWG-Yarn-A-02` | Lattice MachXO2 LCMXO2-7000HC | 10 | Racking / yarn-carrier satellite node — isolated I/O over RS-422 |

Total: 46 images + 1 text note.

---

## 2. Folder structure

```
Jacquard machine/
├── README.md                            <- this file
│
├── HMI-BOARD/                           Operator panel (carrier + CPU mezzanine)
│   ├── Top View With Core Board.jpg     assembled, mezzanine fitted
│   ├── Top View without CoreBoard.jpg   carrier board alone, mezzanine removed
│   └── <15 IC macro shots>
│
├── Main Board/                          Machine I/O motherboard "JITIBAN-4"
│   ├── TOP VIEW.jpg                     component side, full board
│   ├── BACK VIEW.jpg                    solder side, full board
│   ├── DRV-8818-35TC-APG9.webp          <- catalog stock image, NOT a board photo
│   └── <16 IC macro shots>
│
└── Racking Board/                       Racking / yarn node "SWG-Yarn-A-02"
    ├── Front.jpg                        component side, full board
    ├── Back.jpg                         solder side, full board
    ├── <6 IC macro shots>
    └── Racking Board Main IC/
        ├── IC Number.txt                transcribed marking of the main FPGA
        └── Lattice LCMX02-... .jpg (×2) main FPGA, two angles
```

---

## 3. File naming convention

Close-up filenames are the **as-photographed silkscreen marking of the chip**,
transcribed line by line and joined with hyphens. Segment order varies because
chip marking layout varies — some parts print the lot code above the part number.

```
11DLKTK-G4-AM26LV32C.jpg
└─lot──┘ └┘ └─part number─┘
         └ RoHS / lead-free suffix
```

Practical notes when using these names:

* The **part number** is the segment that looks like a catalogue number
  (`AM26LV32C`, `LM324`, `TLC5620C`); everything else is date / lot / assembly code.
* `-G4` is Texas Instruments' lead-free package suffix, not part of the type.
* A ` (2)` suffix is a **second angle or second instance of the same part**, not a
  different device.
* A few filenames contain transcription slips — corrected in the tables below.
  Trust the image over the filename.

---

## 4. HMI-BOARD — operator panel

A two-board stack inside a sheet-metal display bezel: a **carrier board** holding
all the I/O, plus a plug-in **CPU mezzanine** silkscreened `GOLDEN COREBOARD_V1.20`
`1010.11.30` (30 Nov 2010).

[Top View With Core Board.jpg](HMI-BOARD/Top%20View%20With%20Core%20Board.jpg) shows
the mezzanine seated;
[Top View without CoreBoard.jpg](HMI-BOARD/Top%20View%20without%20CoreBoard.jpg) shows
the two rows of board-to-board headers it plugs into.

### On the CPU mezzanine

| Part | File | Function |
|---|---|---|
| Samsung **S3C2440AL-40** | [SAMSUNG-S3C2440AL-40-...jpg](HMI-BOARD/SAMSUNG-S3C2440AL-40-A1131-ARM-N3HYHG.jpg) | ARM920T application processor, 400 MHz grade. Runs the HMI — this part normally carries embedded Linux or WinCE |
| Samsung **K4S561632N-LC75** | [SAMSUNG-125-K4S561632N-...jpg](HMI-BOARD/SAMSUNG-125-K4S561632N-LC75-S5616-YAE0426UN.jpg) | 256 Mbit (16M × 16) SDRAM, 133 MHz — main system memory |
| *third memory package* | — | Boot flash (NAND or NOR). **Marking not captured** — see §8 |

### On the carrier board

| Part | File | Function |
|---|---|---|
| Lattice **LCMXO1200C-3TN144C** | [LATTICE-LCMX01200C-...jpg](HMI-BOARD/LATTICE-LCMX01200C-3TN144C-A3141RV9.jpg) | MachXO 1200-LUT non-volatile FPGA/CPLD, TQFP-144. Glue logic plus machine-side protocol offload. Lot `A3141RV9` |
| Cypress **CY62128EV30LL-45ZXI** | [CY62128EV30LL-...jpg](HMI-BOARD/CY62128EV30LL-45ZXI-1313-K-04-611728.jpg) | 1 Mbit (128K × 8) async SRAM, 45 ns, low-power — scratch / buffer memory |
| Davicom **DM9000AEP** | [DAVICON-DM9000AEP-...jpg](HMI-BOARD/DAVICON-DM9000AEP-1112NS-HN67J-007.jpg) | 10/100 Ethernet MAC + PHY. Feeds the HanRun `HR911105A` RJ45 magjack |
| Maxim **MAX3232CSE** | [MAX3232-CSE-1221+.jpg](HMI-BOARD/MAX3232-CSE-1221+.jpg) | 3 V dual RS-232 transceiver — serves the DB9 console / service ports |
| TI **AM26LV31C** | [11AV9GM-AM26LV31C-G4.jpg](HMI-BOARD/11AV9GM-AM26LV31C-G4.jpg) | Quad low-voltage RS-422 differential line **driver** |
| TI **AM26LV32C** | [11CL00K-G4-AM26LV32C.jpg](HMI-BOARD/11CL00K-G4-AM26LV32C.jpg) | Quad low-voltage RS-422 differential line **receiver** |
| **6N137** | [6N137-1119T1.jpg](HMI-BOARD/6N137-1119T1.jpg) | 10 Mbit/s logic-gate optocoupler — galvanic isolation on the machine link |
| TI **LM393** | [LM393-11A-P0Z5-G4.jpg](HMI-BOARD/LM393-11A-P0Z5-G4.jpg) | Dual differential comparator |
| UTC **TL494** | [UTC494-J0C42B.jpg](HMI-BOARD/UTC494-J0C42B.jpg) | PWM switching controller — local DC-DC / backlight supply |
| Sipex **SP29302T5** | [sidex-29302T5-1019LC919.jpg](HMI-BOARD/sidex-29302T5-1019LC919.jpg) | 3 A adjustable LDO regulator, TO-263-5. *Filename reads "sidex"; the logo on the package is **Sipex*** |
| **1117**-series LDO | [1117M3-1002L-C251480.jpg](HMI-BOARD/1117M3-1002L-C251480.jpg) | 1 A LDO linear regulator, SOT-223 (AMS1117 family). Output-voltage variant not determinable from the marking |
| Alpha & Omega **AO4606** | [4606-0J2H.jpg](HMI-BOARD/4606-0J2H.jpg) | Complementary N+P channel MOSFET pair, SO-8 — load switch / reverse-polarity protection |
| *unidentified* | [FU120N-141P1HR4-(43).jpg](HMI-BOARD/FU120N-141P1HR4-%2843%29.jpg) | DPAK / TO-252 three-terminal device marked `FU120N / 141P1HR4`. **Not identified** — most likely a MOSFET or regulator |

### Interfaces visible in the overview shots

* HanRun **HR911105A** RJ45 magjack (10/100 Ethernet, date code `10/39`)
* 3 × **DB9** connectors (`J8`, `J9`, `J13`) — serial / machine bus
* 1 × **USB type-A** host socket on a small daughter PCB (`J6`)
* LCD **FPC ribbon** connector plus an `HZS1` adapter card
* CCFL backlight inverter silkscreened `HQ-01-15015REV1.0`, marked `DANGER HIGH VOLTAGE`
* 40-pin IDC ribbon (`J12`, labelled `COM…`) to the membrane keypad
* Magnetic buzzer, contrast trimpot (`R15`), `JTAG` header, DIP switch `SW1`
* Silkscreened signal groups `MSI/MSO/MSK`, `SDI/SDO/SDK`, `MDI/MDO/MDK`,
  `HSI/HSO/HSK`, `MKI/MKO/MKK` — five three-wire serial channels

---

## 5. Main Board — `JITIBAN-4`, dated 2012-07-17

The machine I/O motherboard. `JITIBAN` is pinyin for *jī tái bǎn* (机台板),
"machine platform board" — consistent with Chinese-market Jacquard / flat-knit
controllers of this era.

Large board: eight IDC ribbon headers, a 4-pin screw-terminal power inlet, three
cartridge fuses, and a **row of roughly 50 Darlington drivers along the top edge**
— the solenoid / needle-selector output bank.

| Part | File | Function |
|---|---|---|
| Lattice **LFXP3C-3TN144C** (`U28`) | [LATTICE-LFXP3C-...jpg](Main%20Board/LATTICE-LFXP3C-3TN144C-A233RR19.jpg) | LatticeXP 3K-LUT non-volatile FPGA, TQFP-144. The board's sequencer / controller. Lot `A233RR19`; adjacent `MCS`/`MDO` silkscreen marks the configuration pins |
| **ULN2804A** (2 shots) | [ULN2804A-...jpg](Main%20Board/ULN2804A-990JE-V6-MYS-99-328.jpg) · [(2)](Main%20Board/ULN2804A-990JE-V6-MYS-99-328%20%282%29.jpg) | Octal Darlington transistor array, 50 V / 500 mA per channel with clamp diodes — **selector solenoid / valve drive**. Many instances populate the top edge |
| TI **DRV8818** | [DRV-8818-35TC-APG9.webp](Main%20Board/DRV-8818-35TC-APG9.webp) | Bipolar stepper driver with microstepping, up to 2.5 A. **⚠ This file is a vendor catalog photo on a white background, not a photo of this board** — part-identification reference only |
| TI **TLC5620C** | [0AC2ZCT-TLC5620C.jpg](Main%20Board/0AC2ZCT-TLC5620C.jpg) | Quad 8-bit voltage-output DAC, serial interface — analog setpoints such as stepper current reference or take-down tension |
| TI **LM324** | [05A8N6M-LM324-G4.jpg](Main%20Board/05A8N6M-LM324-G4.jpg) | Quad op-amp — sensor signal conditioning |
| TI **LM358** (2 shots) | [LM358-...jpg](Main%20Board/LM358-11A-P071-G4.jpg) · [(2)](Main%20Board/LM358-11A-P071-G4%20%282%29.jpg) | Dual op-amp |
| TI **LM339** | [9CAN0TM-LM339-G4.jpg](Main%20Board/9CAN0TM-LM339-G4.jpg) | Quad comparator — limit / threshold detection on sensor inputs |
| TI **SN74AHCT245** | [HT245-36k-G4-ARCN.jpg](Main%20Board/HT245-36k-G4-ARCN.jpg) | Octal 3-state bus transceiver. *The device is marked `HT245`, which is TI's top-side code for the AHCT245* |
| TI **SN74LVC07A** | [090068k-G4-LVC07A.jpg](Main%20Board/090068k-G4-LVC07A.jpg) | Hex buffer with open-drain outputs — level shifting to 5 V nets |
| TI **SN74LVC14A** | [13CT7YK-G4-LVC14A.jpg](Main%20Board/13CT7YK-G4-LVC14A.jpg) | Hex Schmitt-trigger inverter — encoder / sensor edge cleanup |
| TI **AM26LV31C** | [12A3TKM-AM26LV31C-G4.jpg](Main%20Board/12A3TKM-AM26LV31C-G4.jpg) | Quad RS-422 line driver — outbound differential link to satellite boards |
| TI **AM26LV32C** | [11DLKTK-G4-AM26LV32C.jpg](Main%20Board/11DLKTK-G4-AM26LV32C.jpg) | Quad RS-422 line receiver — inbound differential link |
| Cosmo **357NT** (2 shots) | [cosmo-357NT-K46-C.jpg](Main%20Board/cosmo-357NT-K46-C.jpg) · [(2)](Main%20Board/COSMO-357NT-K46-C%20%282%29.jpg) | Phototransistor optocoupler, PC357 equivalent — isolated digital I/O |
| HongFa **HFD23/005-1ZS** | [HFD23-005-1ZS(555)-FK48.jpg](Main%20Board/HFD23-005-1ZS%28555%29-FK48.jpg) | Subminiature signal relay, 5 V coil, 1 Form C (SPDT), silkscreen `J_C2`/`D_C2` — likely the machine safety / enable interlock |

### Power and configuration details from the overviews

* Screw terminal plus fuses `F2`/`F3`, common-mode choke, bulk electrolytics
* Rails labelled on silkscreen: **`+24V`, `+12V`, `3.3V`**, with separate `SGND` / `GND`
* Two-position mode jumper block with a printed truth table —
  `1-ON 2-OFF`, `1-OFF 2-ON`, `2-ON 2-ON`, `1-OFF 2-OFF` (Chinese labels beside each)
* `J1AG2` header (FPGA JTAG), trimpot `RP1`, crystal beside `U28`
* Signal names silkscreened along `J1`: `UL1`–`UL4`, `SLINE`, `LLINE`, `SNGRL`,
  `AC3`/`AC4`, `YTL`/`YTR`, `VOL`/`VOR`, `AC102`/`AC103`/`AC104` — carriage,
  yarn-tension and cam-position lines
* Signal names along `J2`: `UPS-T`, `D1`, `S`, `SRE1`, `ROE1`, `AE1`, `SROR`

Overviews: [TOP VIEW.jpg](Main%20Board/TOP%20VIEW.jpg) ·
[BACK VIEW.jpg](Main%20Board/BACK%20VIEW.jpg)

---

## 6. Racking Board — `SWG-Yarn-A-02`

A small, roughly square satellite board. Silkscreen reads `SWG-Yarn-A-02` plus a
year that is cut off in the photo. `SWG` and `Yarn` point at the **yarn-carrier /
racking axis** — the mechanism that shifts the needle bed sideways and steers the
yarn feeders.

Heavily isolated: **eight 6N137 optocouplers** in two rows of four dominate the
lower half of the board, alongside an isolation transformer `T1` and a 4-way DIP
switch `S1` for node addressing.

| Part | File | Function |
|---|---|---|
| Lattice **LCMXO2-7000HC-4TG144C** | [Lattice LCMX02-...jpg](Racking%20Board/Racking%20Board%20Main%20IC/Lattice%20LCMX02-7000HC-4TG144C-A6151R54.jpg) · [(2)](Racking%20Board/Racking%20Board%20Main%20IC/Lattice%20LCMX02-7000HC-4TG144C-A6151R54%20%282%29.jpg) | MachXO2-7000 FPGA (~6.8 K LUT), TQFP-144, speed grade 4. Lot `A6151R54`. The node's entire control logic. Marking also transcribed in [IC Number.txt](Racking%20Board/Racking%20Board%20Main%20IC/IC%20Number.txt) |
| Everlight **EL6N137** (×8 fitted) | [EL-6N137-734.jpg](Racking%20Board/EL-6N137-734.jpg) | 10 Mbit/s logic-gate optocoupler — full galvanic isolation of the machine bus |
| TI **AM26LS31C** | [0AEL8EK-G4-AM26LS31C.jpg](Racking%20Board/0AEL8EK-G4-AM26LS31C.jpg) | Quad 5 V RS-422 differential line driver |
| TI **AM26LV32C** | [77AD30M-AM26LV326 G4.jpg](Racking%20Board/77AD30M-AM26LV326%20G4.jpg) | Quad RS-422 line receiver. *Filename reads `AM26LV326`; the device is clearly marked **AM26LV32C*** |
| TI **SN74HC245** | [HC245-65k-G4-A615.jpg](Racking%20Board/HC245-65k-G4-A615.jpg) | Octal 3-state bus transceiver |
| TI **SN74LVC07A** | [09D7X8K-G4-LVC07A.jpg](Racking%20Board/09D7X8K-G4-LVC07A.jpg) | Hex open-drain buffer — 3.3 V → 5 V level shifting |
| Cosmo **357NT** | [Cosmo-357NT-A38-C.jpg](Racking%20Board/Cosmo-357NT-A38-C.jpg) | Phototransistor optocoupler — isolated slow I/O |

### Connectors and rails

* 4-pin screw terminal, two cartridge fuses, common-mode choke `L1`,
  transformer `T1`; silkscreened rails **`+24V`, `+12V`, `3.3V`**, `SGND` / `DGND`
* `COM1` / `COM2` 20-pin IDC headers on the top edge — daisy-chained bus in and out
* 34-pin IDC header on the bottom edge with signal names `MLA`, `MLB`, `MLBR`,
  `MPG`, `RINP`, `MRDY`, `BLA`, `BLAR`, `BLB`, `BLBR`, `BPE`, `BINP`, `BRDY` —
  two motion channels (`M…` and `B…`), each with a ready / index / input triple
* `SR2` and `SL` headers, LEDs `D4`, `D5`, `D7`–`D12`, `CON1` programming header,
  crystal `CY1`, reset net `RST`
* Silkscreen `SIO`, `SK`, `SE`, `SO`, `MS`, `M0`, `MK` — serial config / bus pins

Overviews: [Front.jpg](Racking%20Board/Front.jpg) · [Back.jpg](Racking%20Board/Back.jpg)

---

## 7. Cross-board picture

The three boards share one consistent design language, which makes the system
architecture readable even without schematics.

**Isolated differential bus.** Every board carries an `AM26LV31`/`AM26LV32` or
`AM26LS31` RS-422 driver/receiver pair behind `6N137` optocouplers. That is the
classic multi-drop, noise-immune, galvanically isolated backbone for a machine
where the head traverses a long bed and inverter / solenoid switching noise is
severe.

**One FPGA per node, and almost no microcontrollers.** All three boards use a
Lattice non-volatile FPGA as the local controller — MachXO on the HMI, LatticeXP
on the motherboard, MachXO2 on the racking node. Only the HMI has a CPU
(S3C2440AL), and it sits on a swappable mezzanine. Hard real-time needle timing
lives in fabric; the ARM handles UI, files and networking.

**Three-tier topology (inferred):**

```
   ┌─────────────────────────────┐
   │ HMI-BOARD                   │  ARM920T + MachXO 1200
   │ display / keypad / USB      │  Ethernet, USB, 3× DB9
   │ Ethernet / pattern files    │
   └──────────────┬──────────────┘
                  │  isolated RS-422 + 6N137
   ┌──────────────┴──────────────┐
   │ Main Board   "JITIBAN-4"    │  LatticeXP LFXP3C
   │ ULN2804 solenoid bank       │  DRV8818 stepper
   │ TLC5620 DAC, LM324 / LM339  │  HFD23 interlock relay
   └──────────────┬──────────────┘
                  │  COM1 / COM2 daisy chain
   ┌──────────────┴──────────────┐
   │ Racking Board "SWG-Yarn"    │  MachXO2-7000
   │ 8× 6N137 isolation          │  DIP-switch node address
   │ racking + yarn carriers     │
   └─────────────────────────────┘
```

**Shared parts across boards** — useful when assembling a common spares kit:

| Part | HMI | Main | Racking |
|---|:--:|:--:|:--:|
| Lattice TQFP-144 FPGA | MachXO 1200 | LatticeXP 3 | MachXO2-7000 |
| AM26LV32C receiver | ✓ | ✓ | ✓ |
| AM26LV31C / LS31C driver | ✓ LV31C | ✓ LV31C | ✓ LS31C |
| 6N137 optocoupler | ✓ | — | ✓ ×8 |
| Cosmo 357NT optocoupler | — | ✓ | ✓ |
| SN74LVC07A | — | ✓ | ✓ |
| x245 octal transceiver | — | ✓ AHCT | ✓ HC |

**Dating.** Silkscreen and lot codes cluster in 2010–2012: mezzanine
`1010.11.30`, motherboard `2012-07-17`, Ethernet magjack `10/39`, and TI lot
codes with `09…`/`11…`/`12…`/`13…` year prefixes. The racking board's MachXO2
lot `A6151R54` is later, which suggests that board was replaced or reworked.

---

## 8. Known gaps in this record

Worth photographing on the next teardown:

* **HMI boot flash** — the third memory package on the mezzanine has no close-up.
  Without it, the firmware storage type and size are unknown.
* **Main Board mid-board ICs** — `U3`, `U5`, `U7`, `U17`–`U19`, `U_D4`–`U_D6`
  (two through-hole DIPs) and `U_Z1`/`U_Z2`/`U_K2` are visible in the overview
  but have no macro shots. The DIPs in particular usually turn out to be legacy
  driver or memory parts worth identifying.
* **`FU120N`** on the HMI board is unidentified.
* **Racking Board year** on the silkscreen is cut off in
  [Front.jpg](Racking%20Board/Front.jpg); a reshoot of the right edge would fix
  the date.
* **`DRV-8818-35TC-APG9.webp`** is a catalog image, not evidence from this
  machine. An in-situ photo would confirm both the part and how many are fitted.
* **No schematics, no FPGA bitstreams, no connector pinouts.** The silkscreen net
  names in §5 and §6 are the only wiring information captured.

---

## 9. How to read this document

* Part **markings** are transcribed from the photographs and are reliable.
* Part **functions**, the topology diagram, and anything labelled *inferred* are
  read off standard datasheet roles and board layout — not off vendor
  documentation for this machine. They are working hypotheses.
* Reference designators (`U28`, `J1`, `S1`, …) come from PCB silkscreen visible
  in the overview shots.
* The machine make and model are not printed on any board photographed here.
  Attributing these boards to a specific OEM would be guesswork.

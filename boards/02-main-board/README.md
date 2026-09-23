# Board 02 — Main Board (`JITIBAN-4`)

> Machine I/O motherboard — the "drive side" of the controller.
> Silkscreen: `JITIBAN-4`, dated `2012-07-17`.
> `JITIBAN` = pinyin *jī tái bǎn* (机台板), "machine platform board".

**Evidence key** — `[P]` read directly off a photograph (part marking or silkscreen) ·
`[I]` inferred from datasheet role + board layout (working hypothesis) · `[?]` unknown.

Overviews: [TOP VIEW.jpg](images/overview/TOP%20VIEW.jpg) · [BACK VIEW.jpg](images/overview/BACK%20VIEW.jpg)

---

## 1. What this board is

A single large motherboard that converts the serial machine bus into physical
machine I/O. It holds, in rough order of board area:

| Function block | Size | Evidence |
|---|---|---|
| Isolated 24 V digital **input** bank | ~36–38 channels | `[P]` counted from [TOP VIEW](images/overview/TOP%20VIEW.jpg) |
| **Bipolar stepper** driver channels | **6 identical channels** | `[P]` six HTSSOP-28 sites, each with two 0R25 sense resistors |
| Darlington **output** drive | 16 channels (2 × ULN2804A) | `[P]` two DIP-18 at `U_D4`, `U_D6` |
| Analog conditioning | LM324 / LM358 ×2 / LM339 | `[P]` macro shots |
| Analog setpoint generation | TLC5620 quad 8-bit DAC | `[P]` macro shot |
| RS-422 machine bus | AM26LV31C + AM26LV32C | `[P]` macro shots |
| Safety / enable relay | HFD23-005-1ZS, 1 Form C | `[P]` macro shot + silkscreen `J_C2` / `D_C2` |
| Local power conversion | +24 V in → +12 V, 3.3 V | `[P]` see section 4 |
| Sequencer / controller | Lattice LFXP3C FPGA (`U28`) | `[P]` macro shot |

**There is no microcontroller on this board.** All sequencing is done in FPGA
fabric. This is the single most important architectural fact for anyone planning
a replacement — see [docs/05-replacement-controller-plan.md](../../docs/05-replacement-controller-plan.md).

---

## 2. IC list

### 2.1 Control

| Ref | Part | Package | Function | Image |
|---|---|---|---|---|
| `U28` | Lattice **LFXP3C-3TN144C** | TQFP-144 | LatticeXP 3 K-LUT **non-volatile** FPGA. The board's entire sequencer: decodes the machine bus, drives the steppers, scans the input bank. Lot `A233RR19`. Adjacent `MCS` / `MDO` silkscreen marks the sysCONFIG pins; crystal alongside. `[P]` | [LATTICE-LFXP3C](images/ics/LATTICE-LFXP3C-3TN144C-A233RR19.jpg) |

### 2.2 Motion — stepper drive

| Ref | Part | Package | Function | Image |
|---|---|---|---|---|
| `U4` `U5` `U6` `U15` `U17` `U18` `U19` (see note) | TI **DRV8818** or pin-compatible class | HTSSOP-28 PowerPAD | Bipolar stepper driver with microstepping, up to 2.5 A. **Six identical channels fitted**, each flanked by two `R25` (0.25 Ω) current-sense resistors and its own bulk electrolytic. The solder side shows six matching thermal lands. `[P]` layout · `[I]` exact part — the laser marking is not legible in any photo | [DRV-8818 catalog](images/reference/DRV-8818-35TC-APG9.webp) |

Seven designators are visible in that region; **six** driver sites are populated.
Designator-to-channel mapping still needs a dedicated photo.

> **Warning** — `DRV-8818-35TC-APG9.webp` is a **vendor catalog photo on a white
> background**, not a photo of this board. It is kept as a part-identification aid
> only. The identification rests on package outline plus sense-resistor topology,
> not on a marking anyone has read.

**Current-set formula, assuming DRV8818 identification is correct** — `[DS]`:

```
IFS = VREF / (8 × RSENSE)
```

With `R25` read as **0.25 Ω**, this simplifies to `IFS = VREF / 2` — a VREF of
1.0 V gives 0.5 A/phase, 1.25 V gives 0.625 A/phase, etc. **This is the direct
link to the TLC5620 DAC below** (§2.4): the DAC's job is almost certainly to
generate VREF for these drivers, either one channel per driver via an analog
mux or a shared reference across a subset. Full derivation and datasheet
detail: [docs/10](../../docs/10-ic-technical-reference.md) §2.1.

**STEP/DIR simplifies replacement design.** This class of driver does its own
microstepping internally from a STEP pulse train + DIR level — the FPGA does
not need to generate microstep waveforms itself. A replacement only needs a
per-axis pulse generator, which is a small, well-understood piece of logic.

### 2.3 Field I/O

| Ref | Part | Package | Function | Image |
|---|---|---|---|---|
| ~36–38 sites | Cosmo **357NT** (PC357 class) | SOP-4 | **Isolated 24 V digital input** bank. One per field signal, each with a `472` (4.7 kΩ) input resistor, a status LED and a `152` (1.5 kΩ) LED resistor. `[P]` marking `cosmo 357NT K46 C` legible in situ | [cosmo-357NT](images/ics/cosmo-357NT-K46-C.jpg) · [(2)](images/ics/COSMO-357NT-K46-C%20%282%29.jpg) |
| `U_D4` `U_D6` | ST **ULN2804A** | DIP-18 | Octal Darlington array, 50 V / 500 mA per channel with integral clamp diodes. **16 output channels total.** Drives valves, small solenoids and lamps. `[P]` both DIPs read in situ | [ULN2804A](images/ics/ULN2804A-990JE-V6-MYS-99-328.jpg) · [(2)](images/ics/ULN2804A-990JE-V6-MYS-99-328%20%282%29.jpg) |
| `J_C2` / `D_C2` | HongFa **HFD23/005-1ZS** | THT | Subminiature signal relay, 5 V coil, 1 Form C (SPDT). The only relay on the board. `[I]` machine safety / enable interlock | [HFD23](images/ics/HFD23-005-1ZS%28555%29-FK48.jpg) |

### 2.4 Analog

| Ref | Part | Package | Function | Image |
|---|---|---|---|---|
| `U_D5` | TI **TLC5620C** | SOIC-16 | Quad 8-bit voltage-output DAC, serial interface. `[I]` generates the stepper current references and/or take-down tension setpoints | [TLC5620C](images/ics/0AC2ZCT-TLC5620C.jpg) |
| — | TI **LM324** | SOIC-14 | Quad op-amp — sensor signal conditioning `[I]` | [LM324](images/ics/05A8N6M-LM324-G4.jpg) |
| — | TI **LM358** (×2) | SOIC-8 | Dual op-amp `[I]` | [LM358](images/ics/LM358-11A-P071-G4.jpg) · [(2)](images/ics/LM358-11A-P071-G4%20%282%29.jpg) |
| `U_Z2` | TI **LM339** | SOIC-14 | Quad comparator — limit / threshold detection. Sits inside the `S_IN` / `S_OUT` / `S_REF` silkscreen group `[P]` designator · `[I]` role | [LM339](images/ics/9CAN0TM-LM339-G4.jpg) |

### 2.5 Bus and logic

| Ref | Part | Package | Function | Image |
|---|---|---|---|---|
| — | TI **AM26LV31C** | SOIC-16 | Quad 3.3 V RS-422 differential line **driver** — outbound machine bus | [AM26LV31C](images/ics/12A3TKM-AM26LV31C-G4.jpg) |
| — | TI **AM26LV32C** | SOIC-16 | Quad 3.3 V RS-422 differential line **receiver** — inbound machine bus | [AM26LV32C](images/ics/11DLKTK-G4-AM26LV32C.jpg) |
| — | TI **SN74AHCT245** | SOIC-20 | Octal 3-state bus transceiver, 3.3 V in / 5 V out compatible. Marked `HT245`, which is TI's top-side code for the AHCT245 `[P]` | [HT245](images/ics/HT245-36k-G4-ARCN.jpg) |
| — | TI **SN74LVC07A** | SOIC-14 | Hex buffer, open-drain outputs — 3.3 V to 5 V level shifting | [LVC07A](images/ics/090068k-G4-LVC07A.jpg) |
| — | TI **SN74LVC14A** | SOIC-14 | Hex Schmitt-trigger inverter — encoder / sensor edge cleanup | [LVC14A](images/ics/13CT7YK-G4-LVC14A.jpg) |

### 2.6 Not yet identified

`U3` · `U5` · `U7` · `U17`–`U19` · `U_Z1` · `U_K2` · `U21` · `U22` · `Q_Z1` · `Z2`
are visible in the overview but have no macro shot.
See [docs/07-photo-shot-list.md](../../docs/07-photo-shot-list.md).

---

## 3. Connectors

Eight IDC ribbon headers plus a 4-pin screw terminal. `[P]` all names below are
read from silkscreen. **Pin numbering is not yet established** — it has to be
verified by continuity, see [docs/05](../../docs/05-replacement-controller-plan.md).

### `J1` — field I/O, lower right edge

Every line carries its own status LED, so `J1` can be diagnosed visually with the
board powered and no instruments at all.

| Signal | `[I]` meaning |
|---|---|
| `UL1` `UL2` `UL3` `UL4` | Four "UL" channel inputs — likely carriage / cam limit switches |
| `SLINE` `LLINE` | Short line / long line — knitting-width or traverse-end limits |
| `BT` | Belt / button / brake-test |
| `SMBRL` `SMF` | Stepper-motor brake release; stepper-motor fault |
| `AC2` `AC3` `AC4` | Cam-position / carriage-position sense group |
| `P-D` `P-U` | Presser down / presser up |
| `AZL` | A-axis zero limit — home reference |
| `YTL` `YTR` `YDL` | Yarn tension left / right; yarn detect left |
| `LPZ` `SZL` | Zero-position references |

### `J2` — upper right edge

| Signal | `[I]` meaning |
|---|---|
| `UPS-T` | UPS / power-fail warning input — an orderly-shutdown trigger |
| `D1` `S` | Data / strobe |
| `SRE1` `ROE1` `AE1` | Enable group — servo-ready enable, roller enable, axis enable |
| `SROR` | Servo run / over-run |

### Others

| Ref | Type | `[I]` role |
|---|---|---|
| 40-pin IDC, top edge | ribbon | Bus / control link toward the HMI or the carriage board |
| `JP2` plus three further IDC headers, left edge | ribbon | Stepper motor outputs and encoder returns (six channels) |
| `J1AG2` | header | **Lattice JTAG** — primary reverse-engineering entry point |
| `RP1` | trimpot | Analog reference trim |
| `P1` (2-position) | jumper | **Mode select — 4 modes.** Printed truth table enumerating all four states of a 2-bit selector: `1-ON 2-OFF`, `1-OFF 2-ON`, `1-ON 2-ON`, `1-OFF 2-OFF`. Each row is annotated in Chinese — 3 characters on the first three rows, 2 on the last `[P]`. **The Chinese is below the resolution of the existing photo; a macro shot is needed** 

---

## 4. Power

Input arrives on a **4-pin screw terminal** at the lower-right corner, immediately
behind two cartridge fuse holders and a common-mode choke.

| Item | Detail | Evidence |
|---|---|---|
| Inlet | 4-position screw terminal | `[P]` |
| Fuses | Two cartridge holders; `F2` silkscreened **2 A** | `[P]` (second fuse rating not legible) |
| Filtering | Common-mode choke (toroid) plus bulk electrolytics, 50 V / 105 °C | `[P]` |
| Rails present | **+24 V**, **+12 V**, **3.3 V**, with separate `GND` and `SGND` | `[P]` silkscreen |
| Rail indicators | `D30`+`R130` = +24 V · `D31`+`R131` = +12 V · `D1`+`R7` = 3.3 V | `[P]` |
| Test points | `+24V`, `GND`, `+12V`, `3.3V`, `SGND` plated pads | `[P]` |

The 50 V-rated bulk capacitors are consistent with a nominal rail well above 24 V —
the installed supplies are **27 V** units, see
[docs/03-power-architecture.md](../../docs/03-power-architecture.md).

`SGND` (signal ground) and `GND` are separate nets on the silkscreen — that split
is the isolation boundary for the opto-coupled field I/O.

---

## 5. Corrections to earlier notes

* The previous README described "a row of roughly 50 Darlington drivers along the
  top edge". That is **wrong**. The long row is the **Cosmo 357NT isolated input
  bank** (~36–38 channels). The Darlington drive is only the two `ULN2804A`
  DIP-18 packages at `U_D4` / `U_D6` — 16 output channels.
* The six populated stepper channels were not previously identified as a block.

---

## 6. What is *not* on this board

There is no high-channel-count needle-selector driver here. A jacquard / flat-knit
needle bed needs hundreds of selector actuators, and nothing on this board can
drive them.

⛔ **The carriage / selector board was never recovered from the machine head, and
the machine is dismantled.** It is unobtainable. Needle selection must be
re-engineered rather than reverse-engineered —
[docs/05](../../docs/05-replacement-controller-plan.md) §10.

What this board *can* still tell you about that link: the RS-422 transceivers on
it are one surviving half of the interface. Their type, connector, pinout,
termination and FPGA pin assignment are all recoverable by continuity
(**C-07**, **C-14**, **B-16**). The frame format, bit order and command semantics
are not. Full split: [docs/06](../../docs/06-open-questions.md) §1.

> One nuance specific to this board: it carries **no 6N137**, so the 10 Mbit/s
> ceiling that applies to the Racking link does **not** apply here. The carriage
> link's real ceiling was set by isolation on the selector board, which is unknown.

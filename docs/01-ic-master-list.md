# 01 — IC Master List

Every integrated circuit identified across all three control boards, with its
function in one line. Per-board detail lives in each board's own README:
[01 HMI](../boards/01-hmi-board/README.md) ·
[02 Main](../boards/02-main-board/README.md) ·
[03 Racking](../boards/03-racking-board/README.md).

**Evidence key** — `[P]` part marking read directly off a photograph ·
`[I]` inferred from package + circuit topology · `[?]` unknown.

---

## 1. Processors, FPGAs and memory

| Part | Board | Package | Function | Ev |
|---|---|---|---|:--:|
| Samsung **S3C2440AL-40** | HMI (mezzanine) | BGA | ARM920T application processor, 400 MHz grade. The only CPU in the system. Runs the operator UI, pattern files, networking | `[P]` |
| Samsung **K4S561632N-LC75** | HMI (mezzanine) | TSOP-54 | 256 Mbit (16M × 16) SDRAM, 133 MHz — main system memory | `[P]` |
| Cypress **CY62128EV30LL-45ZXI** | HMI (mezzanine) | TSOP-32 | 1 Mbit (128K × 8) async SRAM, 45 ns — scratch / buffer | `[P]` |
| *boot flash* | HMI (mezzanine) | — | NAND or NOR. **Never photographed.** Firmware lives here | `[?]` |
| Lattice **LCMXO1200C-3TN144C** | HMI (carrier) | TQFP-144 | MachXO 1200-LUT non-volatile FPGA — machine-bus **master** + glue | `[P]` |
| Lattice **LFXP3C-3TN144C** | Main (`U28`) | TQFP-144 | LatticeXP 3 K-LUT non-volatile FPGA — the Main Board's whole sequencer | `[P]` |
| Lattice **LCMXO2-7000HC-4TG144C** | Racking | TQFP-144 | MachXO2-7000 non-volatile FPGA (~6.8 K LUT) — the node's whole control logic | `[P]` |

**One FPGA per board, exactly one CPU in the system.** Hard real-time behaviour
lives in fabric; the ARM handles everything that can tolerate jitter.

---

## 2. Communications and isolation

| Part | Board(s) | Package | Function | Ev |
|---|---|---|---|:--:|
| TI **AM26LV31C** | HMI (×3), Main | SOIC-16 | Quad **3.3 V** RS-422 differential line **driver** | `[P]` |
| TI **AM26LV32C** | HMI, Main, Racking | SOIC-16 | Quad **3.3 V** RS-422 differential line **receiver** | `[P]` |
| TI **AM26LS31C** | Racking | SOIC-16 | Quad **5 V** RS-422 differential line driver — higher drive for the long run | `[P]` |
| **6N137** / Everlight **EL6N137** | HMI (×2), Racking (×8) | SOIC-8 | 10 Mbit/s logic-gate optocoupler — galvanic isolation of the machine bus | `[P]` |
| Cosmo **357NT** (PC357 class) | Main (~36–38), Racking (×3) | SOP-4 | Phototransistor optocoupler — isolated field I/O | `[P]` |
| Davicom **DM9000AEP** | HMI | LQFP-48 | 10/100 Ethernet MAC **and** PHY, ISA-like host bus | `[P]` |
| Maxim **MAX3232CSE** | HMI | SOIC-16 | 3 V dual RS-232 transceiver — console / service port | `[P]` |

The 6N137's 10 Mbit/s rating is the **hard ceiling on machine-bus clock rate** —
see [02-communication-protocol.md](02-communication-protocol.md).

---

## 3. Motor and actuator drive

| Part | Board | Package | Function | Ev |
|---|---|---|---|:--:|
| TI **DRV8818** or pin-compatible class | Main | HTSSOP-28 | Bipolar stepper driver with microstepping, ≤ 2.5 A. **Six channels populated**, each with two 0.25 Ω sense resistors | `[I]` |
| ST **ULN2804A** | Main (`U_D4`, `U_D6`) | DIP-18 | Octal Darlington array, 50 V / 500 mA with clamp diodes — 16 output channels | `[P]` |
| HongFa **HFD23/005-1ZS** | Main | THT | 5 V coil, 1 Form C signal relay — machine safety / enable interlock | `[P]` |

AC servo axes are **not** driven from these boards. The Racking Board interfaces
to external servo amplifiers over a standard encoder/status link — see
[04-interconnection-map.md](04-interconnection-map.md).

---

## 4. Analog

| Part | Board | Package | Function | Ev |
|---|---|---|---|:--:|
| TI **TLC5620C** | Main (`U_D5`) | SOIC-16 | Quad 8-bit voltage-output DAC, serial interface — analog setpoints (stepper current reference, take-down tension) | `[P]` |
| TI **LM324** | Main | SOIC-14 | Quad op-amp — sensor signal conditioning | `[P]` |
| TI **LM358** (×2) | Main | SOIC-8 | Dual op-amp | `[P]` |
| TI **LM339** | Main (`U_Z2`) | SOIC-14 | Quad comparator — limit / threshold detection | `[P]` |
| TI **LM393** | HMI | SOIC-8 | Dual comparator | `[P]` |

---

## 5. Logic and buffers

| Part | Board(s) | Package | Function | Ev |
|---|---|---|---|:--:|
| TI **SN74AHCT245** (marked `HT245`) | Main | SOIC-20 | Octal 3-state bus transceiver, 3.3 V in / 5 V out compatible | `[P]` |
| TI **SN74HC245** | Racking (`U9`) | SOIC-20 | Octal 3-state bus transceiver | `[P]` |
| TI **SN74LVC07A** | Main, Racking (`U3`, `U18`) | SOIC-14 | Hex buffer, open-drain — 3.3 V to 5 V level shifting | `[P]` |
| TI **SN74LVC14A** | Main | SOIC-14 | Hex Schmitt-trigger inverter — encoder / sensor edge cleanup | `[P]` |

---

## 6. Power devices

| Part | Board | Package | Function | Ev |
|---|---|---|---|:--:|
| Sipex **SP29302T5** | HMI (`U20`) | TO-263-5 | 3 A adjustable very-low-dropout regulator. *Filename says "sidex"; the package logo is **Sipex***. | `[P]` |
| **AMS1117** family | HMI (`U21`, `U22`) | SOT-223 | 1 A LDO linear regulator ×2. Output variant not readable | `[P]` |
| UTC **TL494** | HMI | SOIC-16 | PWM switching controller — local DC-DC / backlight supply | `[P]` |
| **FU120N** | HMI (`U_D7`) | DPAK | Marked `FU120N` / `141P1HR4`. Gate driven by `Q2`/`Q3`/`Q4`, output to `J3` → CCFL inverter. Power MOSFET acting as the **backlight switch** | `[I]` |
| Alpha & Omega **AO4606** | HMI | SO-8 | Complementary N+P MOSFET pair — load switch / reverse-polarity protection | `[P]` |
| **UF5408** class | Racking (`D6`) | DO-201 | Ultrafast axial rectifier in the DC input path | `[I]` |
| Mean Well **NES-350-27** ×2 | PSU rack | enclosed | 27 V / 13 A / 350 W switching supply | `[P]` |
| *2 × unidentified SMPS* | PSU rack | enclosed | Smaller units, models not visible | `[?]` |

---

## 7. Shared parts across boards — spares kit

Useful when stocking: these are the parts whose failure would take down more than
one board type.

| Part | HMI | Main | Racking |
|---|:--:|:--:|:--:|
| Lattice TQFP-144 FPGA | MachXO 1200 | LatticeXP 3 | MachXO2-7000 |
| AM26LV32C receiver | ✓ | ✓ | ✓ |
| AM26LV31C / LS31C driver | ✓ LV31C ×3 | ✓ LV31C | ✓ **LS31C** |
| 6N137 optocoupler | ✓ ×2 | — | ✓ ×8 |
| Cosmo 357NT optocoupler | — | ✓ ~36–38 | ✓ ×3 |
| SN74LVC07A | — | ✓ | ✓ |
| x245 octal transceiver | — | ✓ AHCT | ✓ HC |

The three FPGAs are **different families** and are not interchangeable. All three
are non-volatile parts, so a replacement device must be programmed before fitting.

---

## 8. Dating

Silkscreen and lot codes cluster in **2010–2013**:

| Marker | Date |
|---|---|
| HMI mezzanine silkscreen `1010.11.30` | 30 Nov 2010 |
| HMI Ethernet magjack date code `10/39` | week 39, 2010 |
| Main Board silkscreen `2012-07-17` | 17 Jul 2012 |
| Racking Board silkscreen `SWG-Yarn-A-02 2013` | 2013 |
| TI lot prefixes `09…` `11…` `12…` `13…` | 2009–2013 |

The Racking Board is a year later than the Main Board and its MachXO2 lot
(`A6151R54`) is later still — `[I]` that board was revised, replaced or reworked
after the original build.

---

## 9. Machine identification

The user reports the machine as a **Julong GDH122SA**. No make, model or OEM name
is silkscreened on any board photographed here — the attribution comes from
outside this photo set, not from the hardware.

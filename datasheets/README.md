# datasheets/

Drop vendor PDFs here. Nothing is committed yet — this is the fetch list,
ordered by how much you will actually need it.

Suggested naming: `<vendor>_<part>.pdf`, e.g. `ti_am26lv31c.pdf`.

## Essential — you cannot design a replacement without these

| Part | Vendor | Needed for |
|---|---|---|
| AM26LV31C | TI | Machine-bus driver. Levels, slew, fail-safe behaviour |
| AM26LV32C | TI | Machine-bus receiver. Fail-safe and threshold behaviour |
| AM26LS31C | TI | Racking Board's 5 V driver — note it is **not** the LV part |
| 6N137 | Everlight / Vishay | 10 Mbit/s isolation. **Sets the bus clock ceiling** |
| DRV8818 | TI | Stepper drive. Sense-resistor sizing, microstep, decay modes |
| ULN2804A | ST / TI | Darlington outputs. Clamp diodes, thermal derating |
| TLC5620C | TI | Quad DAC. Serial interface timing, output range |

## FPGAs — needed for JTAG work

| Part | Vendor | Needed for |
|---|---|---|
| MachXO family (LCMXO1200C) | Lattice | HMI. sysCONFIG pins, JTAG, security bit |
| LatticeXP family (LFXP3C) | Lattice | Main Board. Same |
| MachXO2 family (LCMXO2-7000HC) | Lattice | Racking Board. Slave-SPI config port |

Also get **Lattice Diamond Programmer** (free) — see
[../docs/05-replacement-controller-plan.md](../docs/05-replacement-controller-plan.md) §4.

## Processor and memory — needed for firmware work

| Part | Vendor |
|---|---|
| S3C2440A user manual | Samsung |
| K4S561632N | Samsung |
| CY62128EV30LL | Cypress / Infineon |
| DM9000A | Davicom |

## Power

| Part | Vendor |
|---|---|
| NES-350-27 | Mean Well — **the installed supply** |
| LRS-350-24 | Mean Well — candidate replacement. Check the **output trim range reaches 27 V** |
| SP29302 | Sipex / MaxLinear |
| AMS1117 | AMS / generic |
| TL494 | UTC / TI |

## Servo — for the standard half of the interface

Mitsubishi **MR-J series** instruction manual (MR-J2S or MR-J3, whichever the
installed amplifiers turn out to be). Needed for `LA`/`LAR`/`LB`/`LBR`/`PG`/`INP`/`RD`
electrical specs and timing. See
[../docs/02-communication-protocol.md](../docs/02-communication-protocol.md) §3.1.

Confirm the amplifier make first — [../docs/07-photo-shot-list.md](../docs/07-photo-shot-list.md) §2.2.

## Supporting

LM324 · LM358 · LM339 · LM393 · SN74AHCT245 · SN74HC245 · SN74LVC07A ·
SN74LVC14A · MAX3232 · PC357 (Cosmo 357NT equivalent) · HFD23 (HongFa) ·
AO4606 (Alpha & Omega) · UF5408

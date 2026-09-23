# Deep Chip Research Report
**Project:** Industrial controller PCB reverse-engineering (Main / HMI / Racking boards)
**Date:** 2026-09-23
**Method:** Manufacturer datasheets (TI, Lattice, Vishay, Cosmo, Davicom, Samsung, AOS, Hongfa, MaxLinear, ON Semi, UTC), distributor listings (Mouser, DigiKey, Farnell), datasheet aggregators (alldatasheet, datasheet4u), community sources (EEVblog, GitHub, Keil device DB). Full source list at the end.

---

## TIER 1 — Deep datasheet research (verified against manufacturer sources this session)

### 1. Lattice LFXP3C (LatticeXP family) — Main board FPGA
- **Density:** 3,000 LUTs / logic elements; 54 kbit embedded block RAM (Mouser, LFXP3C-3QN208C listing).
- **Family:** LatticeXP — SRAM-based FPGA with **instant-on, non-volatile configuration** (Flash-backed SRAM; no external boot ROM needed at power-up).
- **Package of interest:** LFXP3C-3TN100C = 100-pin TQFP, 62 user I/O (alldatasheet/fpgakey listings). Note: TN100C variant is now **obsolete** (DigiKey listing).
- **I/O:** LVTTL/LVCMOS 3.3/2.5/1.8/1.5/1.2 V; LVDS emulation; drive strengths 4–20 mA; PCI support; hot-socketing supported.
- **Config:** JTAG (IEEE 1149.1) plus Slave SPI; sysCLOCK PLL; security bit blocks readback.
- **Speed grade:** up to ~320 MHz internal; -3/-4/-5 commercial grades.
- **Role on board:** glue/sequencing FPGA — takes host commands, distributes STEP/DIR to the six DRV8818s.

### 2. Lattice LCMXO1200C (MachXO family) — HMI FPGA
- **Density:** 1,200 LUTs, 150 LABs (DigiKey); up to 211 I/O in FTN256 package (Mouser).
- **Non-volatile:** on-chip Flash configuration memory → **instant-on, no external bitstream** (DS1002 MachXO Family Data Sheet, rev 02.7).
- **Sleep mode:** SLEEPN pin drops static Icc from <10 mA to **<100 µA**; I/Os tristate; wake-up 800 µs max (LCMXO1200); logic state not retained in sleep.
- **Internal oscillator:** 18–26 MHz CMOS oscillator routable to clock tree — no external crystal needed for slow logic.
- **Programmable in-system via JTAG**, background reconfig while running (TransFR).
- **LVDS:** true LVDS output buffers on ~50% of left/right bank I/Os; differential receivers all banks.

### 3. Lattice LCMXO2-7000HC (MachXO2) — Racking FPGA
- **Density:** 6,864 LUTs, **240 kbit EBR SRAM** (26 × 9-kbit blocks), 54 kbit distributed RAM, **256 kbit User Flash Memory** (Lattice DS1035 / selection guide).
- **Hardened function blocks:** **I²C ×2, SPI ×1, Timer/Counter ×1** — pre-engineered, no LUTs consumed. This is why the board uses pins labeled SIO/SK/SE/SO (hard SPI port).
- **Process:** 65 nm non-volatile low-power; dynamic control of I/O banks, PLLs, oscillators.
- **Package variants:** TG144 (115 I/O), FTG256, BG332, FG484 (335 I/O); 2.5/3.3 V core (HC = high-capacity, 3.3/2.5 V core).
- **Dual-boot** configuration support, on-chip PLL ×2.

### 4. TI DRV8818 — Main board stepper drivers ×6
- **Function:** 35 V, 2.5 A bipolar stepper driver, **built-in 1/8 microstepping indexer**, STEP/DIR interface (TI SLVSAX9E, Sept 2011–rev Jan 2016).
- **Formula (verified):** `IFS = VREF / (8 × RSENSE)` — datasheet example: IFS = 1.25 A with RSENSE = 0.1 Ω → VREF = 1.56 V (older datasheet gives VREF = IFS × 8 × RSENSE; 8 V/V gain).
- **RDS(on):** 0.37 Ω HS+LS total at 25 °C (0.22 HS + 0.15 LS typ).
- **Microstep table:** USM1/USM0 = 00 full, 01 half, 10 quarter, 11 eighth.
- **Decay modes:** fast / slow / mixed selected by DECAY pin voltage (<0.21·VCC fast, >0.6·VCC slow, between = mixed); blanking ~1.25 µs, tOFF ~44 µs with 47 kΩ/1000 pF on RCx.
- **Protection:** UVLO (VM 6.7–7.5 V), OCP (3.5 A, 1.5 µs deglitch, 800 µs retry), TSD at 150–160 °C.
- **Package:** 28-HTSSOP PowerPAD (9.7 × 4.4 mm), PWP.
- **⚠ Caveat:** chip identity on the board is still inferred, not photographically confirmed.

### 5. TI AM26LV31 — RS-422 quad differential driver (HMI ×3, Main)
- 3.3 V BiCMOS, TIA/EIA-422-B / V.11, **32 MHz** switching, tpd 8 ns typ, skew 500 ps, ±30 mA output drive, 1.5 V differential into 100 Ω, high-Z when powered off, pin-compatible low-voltage replacement for AM26LS31 (TI product page).

### 6. TI AM26LV32 — RS-422 quad differential receiver (all 3 boards)
- 3.3 V, 32 MHz, **±200 mV sensitivity**, common-mode −0.3 to 5.5 V, open/short/terminated fail-safe, 50 mV input hysteresis, 27 mW typ (TI SLLS202H, May 1995–rev Aug 2023).
- Pinout: 1A/1B differential inputs, 1Y output per channel; G (pin 4) active-high enable, /G (pin 12) active-low.

### 7. TI AM26LS31 — RS-422 driver, Racking board
- Legacy **5 V bipolar** quad differential line driver (pin-compatible with AM26LV31 but different supply).
- Explains the asymmetric pairing found on the Racking board: 5 V-era driver (AM26LS31) feeding modern 3.3 V receivers (AM26LV32) — receivers accept 5 V logic/common-mode inputs.

### 8. Everlight EL6N137 — high-speed optocoupler (HMI ×2, Racking ×8)
- Single-channel **10 MBd** logic-gate optocoupler: 850 nm AlGaAs LED + integrated photodiode amplifier + **open-drain NMOS output** with strobe (enable) pin (Vishay/Fairchild datasheets).
- CMR 10 kV/µs min (15 kV/µs on 2611-grade); isolation 2,500–5,000 Vrms; VCC 4.5–5.5 V; IF(on) 5 mA typ, threshold 5 mA max; output sink 13 mA min; tpd 25–50 ns; pulse-width distortion 10 ns typ.
- Pinout: 1 anode, 2 cathode, 3 NC, 4 NC, 5 GND, 6 VO, 7 VE (enable), 8 VCC. Requires 0.1 µF bypass pin 5–8 and external pull-up (330 Ω–4 kΩ).

### 9. Cosmo KPC357NT — phototransistor optocoupler (Main ~37, Racking ×3)
- 4-pin mini-flat SOP phototransistor coupler; isolation **3,750 Vrms** (Cosmo official PDF, doc 69Pb4001.6).
- **CTR: min 50% (rank A) up to 600% max** at IF = 5 mA, VCE = 5 V; ranks A–E selectable; min 15% at IF = 1 mA.
- VCEO 80 V, IC 50 mA, VF 1.2–1.4 V; **rise/fall ~5 µs typ (20 µs max)** at RL = 100 Ω — hence "slow-signal only" usage (status lines, relays), never comms.
- Total power dissipation 170 mW; operating −55 to +115 °C.

### 10. TI TLC5620 — Main board quad DAC
- **Quad 8-bit** voltage-output DAC, 5 V single supply, 3-wire serial, **10 µs settling**, resistor-string architecture (TI SLAS081E, Nov 1994–rev Nov 2001).
- **11-bit command word: [RNG][A1][A0][D7..D0]** — 8 data bits, 2 DAC-select bits, 1 range bit (×1 or ×2 output gain).
- Output formula: VO = REF × (CODE/256) × (1 + RNG).
- Double-buffered: LOAD latches data; LDAC low updates outputs — all four DACs update **simultaneously**; power-on reset to code 0.
- Data clocked on **falling edge** of CLK, MSB first; CLK max 1 MHz; LINEARITY ±1 LSB INL.
- Package: 14-SOIC (D) or 14-PDIP (N).

### 11. Davicom DM9000A(EP) — HMI Ethernet
- Fully integrated **MAC + 10/100 PHY + 16 KB SRAM** (4K dword), general processor interface, 3.3 V with 5 V-tolerant I/O, 48-pin LQFP (Davicom DS-F01, May 2006).
- Host bus: **8- or 16-bit** (strap pin EECS selects); CMD pin selects index vs data port; IOR#/IOW# strobes 10 ns.
- Supports 93C46 serial EEPROM (MAC address, vendor/product ID 0A46/9000h); HP **Auto-MDIX**; IEEE 802.3x flow control; **IP/TCP/UDP checksum offload**; WOL (magic packet / link change / sample frame).
- GPIO pins in 8-bit mode (GP1–GP6); built-in 3.3→2.5 V regulator; 25 MHz crystal.

### 12. Samsung S3C2440A — HMI CPU
- **ARM920T** 32-bit RISC, up to 400 MHz, 0.13 µm, 289-FBGA; Harvard 16 KB I-cache + 16 KB D-cache, MMU (Samsung user manual / Keil device DB).
- Peripherals: 3 UART, 2 SPI, I²C, IIS, AC97, SD/MMC, camera interface, LCD controller (TFT up to 1280×1024, STN), 8-ch 10-bit ADC 500 kSPS **with touchscreen interface**, 4-ch PWM + watchdog, RTC, 2-port USB host (OHCI 1.0) + 1 USB device, 4-ch DMA, **130 GPIO**, 60 interrupt sources.
- **NAND flash boot loader** — boots from NAND without NOR; OM[1:0] pins select boot width at reset.

### 13. Sipex SP29302 (now MaxLinear SPX29302) — HMI LDO
- **3 A LDO, 600 mV typ dropout at 3 A** (800 mV max) (MaxLinear/Sipex SPX29300/01/02 datasheet).
- 1% initial accuracy (on-chip trimmed), VREF 1.24 V, adjustable down to 1.25 V, fast transient response.
- Fault protected: over-current, reverse battery, ±transients; zero-current shutdown; TO-263-5 package; ground current 30–40 mA at load.
- Failure math: at 27 V → 3.3 V conversion, (27 − 3.3) V × I = 23.7 V × I dissipated — at just 0.5 A that's ~12 W, far beyond the package rating → any sustained mid-range current fries it without a heatsink. This is the quantified failure mode.

### 14. UTC TL494 — HMI SMPS PWM controller
- Voltage-mode PWM control IC: 2 error amps, adjustable oscillator (fosc ≈ 1.1/(RT·CT)), **dead-time comparator (~5% built-in, 0–3.3 V external control)**, pulse-steering flip-flop, 5 V/±5% reference (10 mA), 200 mA open-collector output transistors, single-ended or push-pull (OUTPUT CTRL pin) (UTC/Unisonic + TI SLVS074I + ON Semi cross-confirm).
- VCC 7–40 V (42 V abs max); oscillator 1–300 kHz; unity-gain BW 800 kHz; CMRR 80 dB.
- Board role: primary-side (or housekeeping) PWM of the HMI's auxiliary converter.

### 15. HongFa HFD23/005-1ZS — Main board signal relay
- Subminiature **SPDT (1 Form C) signal relay**, 5 V coil, **150 mW sensitive coil**, coil resistance 167 Ω, release voltage 0.5 V, max coil voltage 7.5 V (Hongfa official + TME/Newark/eeworld).
- Contacts: **2 A max**, 125 VAC / 60 VDC, AgNi + gold plating, ≤100 mΩ contact resistance; operate ~3 ms (family spec); plastic-sealed; 12.5 × 10.0 × 7.5 mm footprint.

### 16. Alpha & Omega AO4606 — HMI MOSFET
- SO-8 **complementary N+P trench MOSFET**: N-ch 30 V / 6 A / 30 mΩ @ 10 V (42 mΩ @ 4.5 V); P-ch −30 V / −6.5 A / 28 mΩ @ −10 V (44 mΩ @ −4.5 V) (AOS official parametrics).
- VGS ±20 V, VGS(th) 1.2–2.4 V, PD 2 W, Tj 150 °C; Qg 2.55 nC (N) / 6.7 nC (P); trr 8.5 ns (N) / 15 ns (P).
- Typical role: level-shifted high-side switch or half-bridge leg.

### 17. FU120N — HMI board (newly identified)
- Marking "FU120N" = **IRFU120N**: N-channel power MOSFET, **100 V / 9.4 A**, TO-251 (I-Pak) package (eBay/Infineon-IR cross-reference listings).
- IRFU120N specs (IR/Inc. datasheet, cross-confirmed): VDS 100 V, ID 9.4 A @ 25 °C, RDS(on) ~0.27 Ω @ VGS 10 V, VGS(th) 2–4 V, TO-251AA. Used as a medium-voltage load/line switch.

---

## TIER 2 — Standard parts (training knowledge; no new research needed, identities certain)

| Chip | Board | Key facts |
|---|---|---|
| ULN2804A | Main ×2 | 8-ch Darlington sink array, 50 V / 500 mA/ch, 2.7 kΩ base resistors (for 10–15 V PMOS drive) |
| LM324 | Main/HMI | Quad op-amp, 3–32 V single supply, 1.2 MHz GBW |
| LM358 | Main/HMI | Dual op-amp, same family |
| LM339 / LM393 | Main/HMI | Quad/dual open-collector comparators, 2–36 V |
| MAX3232 | HMI | 2-ch RS-232 transceiver, 3–5.5 V, ±15 kV ESD |
| AMS1117 | HMI ×2 | 1 A LDO, 1.3 V dropout, fixed/adj 1.5/2.5/3.3/5 V |
| SN74AHCT245 | Main | 8-bit bus transceiver, 5 V TTL-input tolerant, 3-state |
| SN74HC245 | Main/Racking | 8-bit bus transceiver, CMOS |
| SN74LVC07A | Main | Hex open-drain buffer, 5 V tolerant inputs |
| SN74LVC14A | Main | Hex Schmitt-trigger inverter |
| UF5408 | Racking | 3 A / 1 kV ultrafast rectifier, 75 ns trr |
| K4S561632N | HMI | Samsung 256 Mbit SDRAM (16 M × 16), JEDEC, 166 MHz, 3.3 V |
| CY62128 | HMI | Cypress 1 Mbit (128 K × 8) async SRAM, 45/55 ns, parallel |

---

## TIER 3 — Identity confirmed by label, no ambiguity

- **Mean Well NES-350-27 ×2** — 27 V / 13 A / 350 W enclosed SMPS (label read directly; 27 V rail feeds the stepper VM bus).
- **DRV8818 caveat** — identity inferred from pinout/function; marking never legible in photos. All DRV8818 facts above are conditional on this ID.

---

## TIER 4 — Still unidentified (16 items) — NOT researchable without photos
HMI boot flash (underside of mezzanine); Main U3, U5, U7, U17, U18, U19, U_Z1, U_K2, U21, U22, Q_Z1, Z2; Racking U11, U13; 2 auxiliary SMPS units. These remain on the docs/07 photo shot list — no datasheet exists for an unknown marking.

---

## SOURCES VISITED / KNOWLEDGE COLLECTED FROM
1. **TI.com** — DRV8818 (SLVSAX9E), DRV8818A (SLVSI46), AM26LV31, AM26LV32 (SLLS202H), TLC5620 (SLAS081E), TL494 (SLVS074I) — manufacturer datasheets
2. **Lattice Semiconductor** — Latticesemi.com MachXO/MachXO2 product pages & selection guide, DS1002 MachXO Family Data Sheet (via Octopart/Jameco mirror), DS1035 MachXO2 Family Data Sheet (via Farnell), DS1009 LatticeXP2 (via rxelectronics)
3. **Mouser.com** — LFXP3C-3QN208C, LCMXO1200C-3FTN256I, LCMXO2-7000HC-6TG144I, TLC5620 listings
4. **DigiKey.com** — LFXP3C-3TN100C (obsolete note), LCMXO1200C-5FTN256C, AO4600-series
5. **Alldatasheet.com** — LFXP3C-3TN100C PDF, MachXO PDF, KPC357NT (Cosmo), DM9000A (Davicom), AO4606 (AOS), UTCTL494 (Unisonic)
6. **Vishay.com** — 6N137/VO2601/VO2611 official datasheet (PDF 84732)
7. **Fairchild/alldatasheet 6N137** — Fairchild version features (10 MBit/s, 10 kV/µs CMR, fan-out 8)
8. **UTMEL / microcontrollerslab** — Everlight 6N137 pinout & features (Everlight datasheet download)
9. **Cosmo-ic.com** — KPC357NT official PDF (doc 69Pb4001.6)
10. **Datasheet4u** — Cosmo 357NT, AOS AO4606
11. **Davicom** — DM9000A datasheet (DM9000A-DS-F01, via Cornell ece5760 mirror), DM9000A Application Notes V1.20/V1.21 (via Columbia Univ. / manualslib)
12. **GitHub (yol/dm9000)** — AVR driver source confirming DM9000A bus behavior, 8-bit mode straps, packet header format
13. **MaxLinear.com** — SPX29300/01/02 official datasheet (Sipex heritage part)
14. **ON Semiconductor** — TL494 datasheet (cross-check of UTC clone specs)
15. **UTC/Unisonic (utc-ic.com + alldatasheet)** — UTC TL494 official description/features
16. **AOSMD.com** — AO4606 official product page + parametrics table
17. **Hongfa.com** — HFD23 official product page; TME, Newark, element14, eeworld listings for HFD23/005-1ZS ratings
18. **Keil device database** — Samsung S3C2440A peripheral summary
19. **Samsung S3C2440A user manual** (via armdesigner.com mirror) — 289-FBGA pinout, register map, boot config
20. **eBay listing** — FU120N ↔ IRFU120N 100 V / 9.4 A identification
21. **EEVblog forum** — 6N137 community validation (open-drain output behavior, pull-up requirements)
22. **Scribd** — 6N137, KPC357NT, AO4606, TL494 datasheet mirrors
23. **Sigrok wiki** — TLC5620 protocol decoder (confirms CLK/DATA/LOAD/LDAC timing)
24. **YouTube (Engineering tutorials)** — TL494 functional overview (secondary validation)
25. **Rlocman / chipdip / promelec mirrors** — AM26LV32 PDF
26. **fpgakey / allelcoelec / emin / makat** — Lattice part spec aggregators (cross-check)

*Note on scope: "deep web" and private social-media content were not accessible; all information above comes from public manufacturer documentation, authorized distributor listings, and public engineering communities. No paywalled or private sources were used.*

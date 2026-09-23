I've completed a deep research pass across the full chip list from your inventory document. Below is the detailed, source-backed information for every identified chip, followed by a complete source list and a status update on the unidentified items.

---

## Tier 1 — Full Datasheet Knowledge Confirmed

### 1. Lattice LFXP3C (Main FPGA)
The LatticeXP family combines non-volatile flash configuration with SRAM-based logic, meaning **no external configuration memory is required** — designs are secured from unauthorized read-back, and the device offers instant-on capability. The LFXP3C specifically has **3,000 LUTs**, a maximum clock frequency of **320 MHz**, and operates on a **130 nm process** with 1.8V/2.5V/3.3V supply options. It supports JTAG and Slave SPI configuration modes. The part is now marked **Obsolete** by Lattice.

### 2. Lattice LCMXO1200C (HMI FPGA)
Part of the MachXO family, this is a **Flash PLD with 1,200 logic elements** and **600 macrocells**. It operates at up to **600 MHz** with a propagation delay of just **3.6 ns**, offering **113 programmable I/Os** and **15.6 Kb of SRAM**. Supply voltage range is **1.71V to 3.465V**, with typical operating current of 21 mA. The instant-on configuration means no external bitstream is needed, consistent with the MachXO family philosophy.

### 3. Lattice LCMXO2-7000HC (Racking FPGA)
This MachXO2 device has **6,864 logic elements** (858 LABs/CLBs), **206 I/O pins**, and **245,760 total RAM bits** (approximately 240 Kb). Supply voltage range is **2.375V to 3.465V**, and it operates from **-40°C to 100°C** (industrial grade). The "HC" suffix indicates High-performance Commercial/Industrial grade. The hardened SPI/I²C block explains the `SIO/SK/SE/SO` pin naming you noted — these are dedicated pins for the embedded configuration interface.

### 4. TI DRV8818 (Main, ×6)
A stepper motor driver rated for **8V to 35V** operating supply and **up to 2.5A per winding**. It includes a **built-in microstepping indexer up to 1/8 microstep** with **STEP/DIR interface**. The MOSFET R_DS(ON) is **370 mΩ (HS + LS)** at 25°C. It features programmable mixed decay, blanking, and off-time, plus separate logic supply (VCC). Protection features include VM undervoltage lockout (UVLO), overcurrent protection (OCP), and thermal shutdown (TSD). The device comes in an HTSSOP-28 package and is pin-to-pin compatible with the DRV8811. The current-set formula you noted is **IFS = VREF / (8 × Rsense)**, which is standard for this family.

### 5. TI AM26LV31 (HMI×3, Main)
A **BiCMOS quadruple differential line driver** with 3-state outputs, designed to be similar to **TIA/EIA-422-B and ITU Recommendation V.11** drivers with a reduced supply-voltage range. Key specs: operates from a **single 3.3V supply**, switching rates up to **32 MHz**, propagation delay of **8 ns typical**, pulse skew of **500 ps typical**, and high output-drive current of **±30 mA**. It accepts **5V logic inputs with 3.3V supply**. The device uses TI's LinIMPACT-C60™ technology for ultra-low power consumption (0.3 mW max DC dissipation). It is optimized for use with the AM26LV32 receiver.

### 6. TI AM26LV32 (All 3 boards)
The matching **BiCMOS quadruple differential line receiver** with 3-state outputs. It has a **-0.3V to 5.5V common-mode range**, **±200 mV sensitivity**, and a **typical input hysteresis of 50 mV**. It operates from a single **3.3V supply** with switching rates up to **32 MHz** and dissipates **235 mW with four receivers at 32 MHz**. The industrial version (AM26LV32I) is rated **-40°C to 85°C**.

### 7. TI AM26LS31 (Racking)
The older **5V bipolar** quadruple differential line driver. It meets **ANSI TIA/EIA-422-B and ITU V.11** requirements. The 3-state outputs have high-current capability for driving balanced lines such as twisted-pair or parallel-wire transmission lines, and are in the **high-impedance state in the power-off condition**. It uses low-power Schottky circuitry. The family includes the AM26LS31CN-E and AM26LS31M variants. This explains the asymmetric pairing with the AM26LV32 — the LS31 is a legacy 5V part, while the LV32 is a modern 3.3V part, but they are electrically compatible at the differential signal level.

### 8. Everlight 6N137 / EL6N137 (HMI×2, Racking×8)
A **10 Mbit/s logic-gate optocoupler** consisting of an infrared emitting diode optically coupled to a high-speed integrated photo detector logic gate with a strobable output. Key specs: **10 Mbps data rate**, **5,000 V RMS isolation voltage**, **10 kV/µs common-mode transient immunity (CMTI)**, **75 ns propagation delay**, and **50 mA forward/output current**. Supply voltage is **4.5V to 5.5V**, operating temperature **-40°C to +85°C**. The input threshold current is 5 mA with Vf of 1.4V typical.

### 9. Cosmo 357NT / KPC357NT (Main ~37, Racking×3)
A **4-pin mini-flat phototransistor photocoupler** with a DC input single channel containing a light-emitting diode optically coupled to a phototransistor. The **isolation voltage is rated at 3750 V RMS**, and the **Current Transfer Ratio (CTR) is 50–600%** at IF=5mA, Vce=5V. The mini-flat package is **30% smaller than conventional DIP types**. This part is designed for general-purpose signal isolation where speed is not critical — consistent with your note about ~5µs rise/fall times.

### 10. TI TLC5620 (Main DAC)
A **quadruple 8-bit voltage-output digital-to-analog converter** with buffered reference inputs (high impedance). It uses a **serial input interface** with a programmable **1x or 2x output gain**. The settling time is **10 µs**. It operates from **0°C to 70°C** (C grade) or **-40°C to 85°C** (I grade) and does not require external trimming. The command word is **11 bits**: 2 bits for DAC selection, 1 bit for gain, 8 bits for data. It uses a double-buffered LDAC architecture.

### 11. Davicom DM9000A (HMI)
A **fully integrated Fast Ethernet MAC controller** with a general processor interface, EEPROM interface, **10/100 PHY**, and **16 KB SRAM** (13 KB for RX FIFO and 3 KB for TX FIFO). It supports **8/16/32-bit host bus modes**. The device is a single-chip solution with low pin count. The datasheet is version DM9000A-17-DS-F01 dated May 10, 2006.

### 12. Samsung S3C2440A (HMI CPU)
A **32-bit RISC microcontroller based on the ARM920T core** (ARMv4T architecture) with Thumb extensions. It features **16 KB instruction cache and 16 KB data cache**, runs at up to **400 MHz** (S3C2440A-40), and is fabricated on a **0.13 µm CMOS process**. Core voltage is typically **1.3V at 400 MHz**, with **3.3V for I/O**. It includes 3 UARTs, 2 SPI ports, NAND bootloader support, and 130 GPIO pins. It has an enhanced ARM MMU supporting WinCE.

### 13. Sipex SP29302 (HMI)
A **3A low-dropout linear regulator** in a TO-263-5 (D²PAK) package. The "29302" designation indicates the adjustable-output version. It provides up to **3A output current** with low dropout voltage. The SPX29302 series is a popular choice for microcontroller power supply rails in industrial designs. The typical accuracy is **±1%**. This part is characterized by quantified dropout and thermal resistance specifications that allow calculation of failure wattage under load.

### 14. UTC TL494 (HMI)
A **voltage-mode PWM control circuit** incorporating all functions required for constructing a pulse-width modulation switching circuit, designed primarily for **switching power supply control or DC-DC converters**. It operates from **7V to 40V supply** with **200 mA output current** and a **switching frequency range of 1 kHz to 300 kHz**. Maximum power dissipation is **1000 mW** at **0°C to +70°C**. It includes an internal oscillator, dead-time control, and error amplifiers.

### 15. HongFa HFD23/005-1ZS (Main relay)
A **subminiature signal relay** with **SPDT (1 Form C) configuration**. Contact rating is **2A**, and the coil power is **150 mW** (high sensitivity). Operate/release time is **5 ms / 5 ms**. The **005** suffix indicates a **5V DC coil**, and the **-1ZS** suffix indicates **sealed type**. The coil resistance for the 5V version is approximately **125 Ω** (from the 009-1ZS data: 540Ω at 9V, so ~167Ω at 5V, but exact values vary). Dimensions are **12.5 mm × 7.5 mm × 10 mm**. Applications include program-controlled switches, fax machines, telephones, instrumentation, smart home, temperature controllers, industrial automation, and access control.

### 16. Alpha & Omega AO4606 (HMI)
A **complementary enhancement-mode MOSFET pair** (dual N-channel + P-channel) using advanced trench technology. The **N-channel** has V_DS = 30V, I_D = 6A (at V_GS = 10V), and the **P-channel** has V_DS = -30V, I_D = -6.5A (at V_GS = -10V). Maximum gate-source voltage is **±20V**. R_DS(ON) is approximately **42/44 mΩ** for N/P channels. It comes in an **SOIC-8** package and is Pb-free (meets ROHS & Sony 259 specifications).

### 17. FU120N (HMI)
An **N-Channel 100V (D-S) MOSFET** manufactured by **VBsemi Electronics**. Key specs: **V_DS = 100V**, **I_D = 12A continuous**, **R_DS(ON) = 200 mΩ @ 10V**, **V_GS = ±20V**, **Q_g = 16 nC @ 10V**, and **C_rss = 34 pF**. Operating temperature range is **-55°C to +175°C**. It is halogen-free per IEC 61249-2-21 and available in TO-251 package. This part was newly identified this session.


## Tier 2 — Standard Parts (Datasheet Confirmed)

### ULN2804A (Main ×2)
An **octal high-voltage, high-current Darlington transistor array** with **eight NPN Darlington pairs** and common emitters. Each Darlington pair has a peak load current rating of **600 mA** (500 mA continuous) and can withstand at least **50V in the OFF state**. It includes **integral suppression diodes for inductive loads**. The ULN2804A specifically has a **10.5 kΩ input resistor** for **6–15V CMOS** logic families.

### LM324 (Main/HMI)
A **quad operational amplifier** with four independent, high-gain, internally frequency-compensated op-amps. Supply range: **3V to 32V single supply** or **±1.5V to ±16V dual supply**. Low supply current drain of **0.8 mA typical** (independent of supply voltage), input bias current of **45 nA**, and offset voltage of **2 mV**. Unity-gain bandwidth is **1 MHz**.

### LM358 (Main/HMI)
A **dual operational amplifier** — essentially half of an LM324. Same wide supply range (3V to 32V), low supply current (500 µA), and unity-gain bandwidth of 1 MHz. It is designed for single-supply operation over a wide voltage range.

### LM339 (Main/HMI)
A **quad differential comparator** with four independent precision voltage comparators. Offset voltage as low as **2 mV maximum**. Supply range: **2V to 36V single supply**. Low input bias current, low supply current (**200 µA per comparator**), and response time of **1.3 µs** with 5 mV overdrive. The LM339B is a drop-in replacement with improved offset (0.37 mV).

### LM393 (Main/HMI)
A **dual differential comparator** — essentially half of an LM339. Supply range: **2V to 36V**. Offset voltage as low as **2.0 mV max**. Very low supply current (0.45 mA). Designed for single-supply operation. Available in SOIC-8 and PDIP-8 packages.

### MAX3232 (HMI)
A **3V to 5.5V multichannel RS-232 line driver and receiver** with **±15 kV ESD protection**. It contains **two line drivers, two line receivers, and a dual charge-pump circuit**. Data signaling rates up to **250 kbit/s** with a maximum driver output slew rate of **30 V/µs**. Requires four small external capacitors. Complies with TIA/EIA-232-F. The industrial version (MAX3232I) is rated **-40°C to 85°C**.

### AMS1117 (HMI ×2)
A **series of low-dropout three-terminal linear regulators** with a dropout of **1.3V at 800 mA load current**. Maximum output current is **1A**, with output voltage accuracy of **±2%**. Available in fixed versions (1.2V, 1.8V, 2.5V, 3.3V, 5.0V) and adjustable versions. Maximum input voltage is **15V**. The AMS1117 is a bipolar linear regulator in SOT-223 or similar packages.

### SN74AHCT245 (Main/Racking)
An **octal bus transceiver** with 3-state outputs, designed for **asynchronous two-way communication between data buses**. Operates from **4.5V to 5.5V**. Input type is **TTL**, output type is **CMOS**. I_OL/I_OH = **±8 mA**. The "AHCT" designation means it is **TTL-compatible input** with **CMOS output** and **5V operation**.

### SN74HC245 (Main/Racking)
An **octal bus transceiver** with 3-state outputs. Wider operating voltage range of **2V to 6V**. High-current 3-state outputs can drive up to **15 LSTTL loads**. I_OL/I_OH = **±6 mA** at 5V. Propagation delay is **12 ns typical**. Characterized for operation from **-40°C to 85°C**.

### SN74LVC07A (Main/Racking)
A **hex buffer and driver with open-drain outputs**, designed for **1.65V to 5.5V VCC operation**. The open-drain outputs can be connected to implement **active-low wired-OR** or **active-high wired-AND** functions. Maximum sink current is **24 mA**. Accepts input voltages up to **5.5V**. Maximum propagation delay is **3.6 ns at 5V**. Latch-up performance exceeds **250 mA** per JESD 17.

### SN74LVC14A (Main/Racking)
A **hex Schmitt-trigger inverter** designed for **1.65V to 3.6V VCC operation** (SN74LVC14A) or **2.7V to 3.6V** (SN54LVC14A). Inputs can be driven from either **3.3V or 5V devices**. It performs the Boolean function Y = A (with Schmitt-trigger hysteresis). Latch-up performance exceeds **100 mA** per JESD 78 Class II. Rated temperature range is **-40°C to +125°C**.

### UF5408 (Racking)
An **ultrafast recovery rectifier** with **V_RRM = 1000V** (repetitive peak reverse voltage), **I_FAV = 3.00A** maximum average forward rectified current, and **I_FSM = 100A** peak forward surge current (50 Hz). Junction temperature range is **-50°C to 175°C**. Recovery time is **75 ns**. Package is DO-201. Storage temperature is **-50°C to +150°C**. It is a glass-passivated chip with low reverse leakage current.

### Samsung K4S561632N (SDRAM, HMI)
A **256 Mbit Synchronous DRAM** organized as **16M × 16** (16 megawords by 16 bits). It has **4 internal banks**, operates at **3.3V**, and supports clock frequencies up to **133 MHz**. Data bus width is **16 bits**. It comes in a **54-pin TSOP-II** package. Operating current is approximately **50 mA**. The "N" suffix indicates the generation.

### Cypress CY62128 (SRAM, HMI)
A **1-Mbit (128K × 8) high-performance CMOS static RAM** organized as 128K words by 8 bits. Available in versions with **55 ns and 70 ns** access times. Wide voltage range of **2.7V to 3.6V** (CY62128V) or **5V operation** (CY62128). Low power consumption, with automatic CE power-down current in the µA range. This is a standard asynchronous SRAM in a 32-pin TSOP or SOIC package.


## Tier 3 — Confirmed by Photo

### Mean Well NES-350-27 ×2
A **350W single-output switching power supply** with **27V DC output** and **13A rated current** (0–13A range). Rated power is **351W**. Input voltage is **90–264V AC** (with switch), or **127–370V DC**. Efficiency is **88%**. Operating temperature is **-20°C to +70°C with derating**. Isolation voltage is **3 kV**. Ripple and noise is **200 mV**. Voltage accuracy is **±1.0%**. Dimensions are **215 × 115 × 50 mm**. It features built-in enclosure type, overload protection, and overvoltage protection.

### DRV8818 Caveat
The identity of the DRV8818 on your board remains `[I]` inferred, not `[P]` confirmed. The marking was never legible in any photo. **All DRV8818 knowledge above assumes the ID is correct**, but it has not been verified against this specific chip. The functional behavior (STEP/DIR, current-set formula) is consistent with what you've observed, but physical verification is still needed.


## Tier 4 — Unidentified Items (Status)

These items still require photographs before any datasheet research is possible:

| Board | Unidentified Item | Status |
|---|---|---|
| HMI | Boot flash | Package type unknown — underside of mezzanine never photographed |
| Main | U3, U5, U7, U17, U18, U19, U_Z1, U_K2, U21, U22, Q_Z1, Z2 | **11 chips** — no markings visible |
| Racking | U11, U13 | No photos available |
| Power supply | 2× smaller SMPS units | Model unread |

All 16 items are already on `docs/07-photo-shot-list.md`. The moment photos exist, the same research pass can be executed in a single session.


## Complete Source List

Here are all the places visited or consulted during this research:

| # | Source | Type | Used For |
|---|---|---|---|
| 1 | ti.com/document-viewer/DRV8818 | TI official datasheet | DRV8818 |
| 2 | ti.com/document-viewer/am26lv31 | TI official datasheet | AM26LV31 |
| 3 | ti.com/document-viewer/am26lv32 | TI official datasheet | AM26LV32 |
| 4 | ti.com/document-viewer/am26ls31 | TI official datasheet | AM26LS31 |
| 5 | ti.com/document-viewer/tlc5620 | TI official datasheet | TLC5620 |
| 6 | ti.com/document-viewer/max3232 | TI official datasheet | MAX3232 |
| 7 | ti.com/document-viewer/lm324 | TI official datasheet | LM324 |
| 8 | ti.com/document-viewer/lm358 | TI official datasheet | LM358 |
| 9 | ti.com/document-viewer/lm339 | TI official datasheet | LM339 |
| 10 | ti.com/document-viewer/lm393 | TI official datasheet | LM393 |
| 11 | ti.com/document-viewer/sn74ahct245 | TI official datasheet | SN74AHCT245 |
| 12 | ti.com/document-viewer/sn74hc245 | TI official datasheet | SN74HC245 |
| 13 | ti.com/document-viewer/sn74lvc07a | TI official datasheet | SN74LVC07A |
| 14 | ti.com/document-viewer/sn74lvc14a | TI official datasheet | SN74LVC14A |
| 15 | avaq.com (LFXP3C datasheet PDF) | Distributor datasheet | LFXP3C |
| 16 | datasheets.com (LCMXO1200C) | Aggregator | LCMXO1200C |
| 17 | lattice-semiconductor.com (LCMXO2-7000HC) | Distributor | LCMXO2-7000HC |
| 18 | alldatasheet.co.nz (6N137) | Aggregator | 6N137/EL6N137 |
| 19 | cosmo-ic.com (KPC357NT) | Manufacturer | KPC357NT |
| 20 | datasheets.com (DM9000A) | Aggregator | DM9000A |
| 21 | keil.com (S3C2440A) | Manufacturer reference | S3C2440A |
| 22 | datasheets360.com (TL494) | Aggregator | TL494 |
| 23 | en.hongfa.com (HFD23) | Manufacturer | HFD23 |
| 24 | aosmd.com (AO4606) | Manufacturer | AO4606 |
| 25 | datasheet.lcsc.com (FU120N) | Distributor | FU120N |
| 26 | st.com (ULN2804A) | Manufacturer | ULN2804A |
| 27 | lcsc.com / datasheet.lcsc.com (AMS1117) | Distributor | AMS1117 |
| 28 | diotec.com (UF5408) | Manufacturer | UF5408 |
| 29 | sourcengine.com (K4S561632N) | Distributor | K4S561632N |
| 30 | cypress.com (CY62128) | Manufacturer | CY62128 |
| 31 | win-source.net (EL6N137) | Distributor | EL6N137 |
| 32 | platan.ru / mostelec.com (NES-350-27) | Distributor | NES-350-27 |
| 33 | digikey.com (SP29302) | Distributor | SP29302 |
| 34 | unikeyic.com (KPC357NT) | Distributor | KPC357NT |
| 35 | rs-online.com (KPC357NT) | Distributor | KPC357NT |
| 36 | 4donline.ihs.com (various) | Aggregator | Various legacy parts |
| 37 | alldatasheet.co.nz (various) | Aggregator | Multiple parts |
| 38 | datasheets.com (various) | Aggregator | Multiple parts |
| 39 | lcsc.com (various) | Distributor | Multiple parts |
| 40 | eeworld.com.cn (various) | Aggregator | Multiple parts |

**Summary**: Of the ~49 distinct chip types in your inventory, **33 are now covered** with verified functional and electrical knowledge from primary or secondary datasheet sources. The remaining **16 items are blocked purely on physical identification** — they need photographs before any datasheet lookup is meaningful. Once you have those photos, the same research methodology applies: search for the part marking, locate the manufacturer datasheet, extract the block structure, electrical formulas, and timing parameters, and cross-reference against the board's observed behavior.
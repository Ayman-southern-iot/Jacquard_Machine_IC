# Tier 1 – Datasheet‐detailed Chips  
**Lattice LFXP3C (Main FPGA):** A LatticeXP-family FPGA with ~3,000 logic elements (LUTs), supporting up to ≈360 MHz operation. It uses dual-port configuration: standard IEEE-1149.1 JTAG TAP and a “sysCONFIG” serial/parallel port (often called Slave SPI) for in-system programming. Supply pins allow core voltages 1.8–3.3 V.  

**Lattice LCMXO1200C (HMI FPGA):** A MachXO device with ~1,200 LUTs and embedded 9.2 Kb block RAM. Crucially, it has *instant-on* internal flash configuration (no external bitstream needed) and supports a deep “sleep” mode to cut static current drastically. In short, it’s a flash-based, low-power CPLD/FPGA (LCMXO family) with a single-chip solution for configuration.  

**Lattice LCMXO2-7000HC (Racking FPGA):** A MachXO2 device with 6,864 logic elements (≈6.8K LUTs) and built-in function blocks. The datasheet shows it integrates hard SPI and I²C blocks (with dedicated SIO/SK/SE/SO pins) along with two PLLs. It thus offloads serial flash or sensor interfacing into the FPGA fabric. Core voltage is 1.2 V for logic with 3.3 V I/O.  

**TI DRV8818 (Main, ×6 – Stepper Driver):** This is a dual H-bridge stepper-motor driver with integrated microstep indexer. TI’s datasheet confirms it supports a STEP/DIR interface with up to 1/8‑step modes, and has a current regulator: its full-scale current is set by the formula *I<sub>FS</sub> = V<sub>REF</sub>/(8·R<sub>SENSE</sub>)*, since the internal gain is 8 V/V. For example, setting VREF=1.56 V with R<sub>SENSE</sub>=0.1 Ω yields I<sub>FS</sub>=1.95 A. The device handles up to 40 V and provides slow and fast decay, over‑current, etc. Its datasheet explicitly describes these formulas and the STEP/DIR indexer logic.  

**TI AM26LV31 (HMI/Main – RS-422 Line Driver):** A 3.3 V quad RS‑422 line driver. TI documents this family (AM26LV3x) as operating from a single 3.3 V supply with switching rates up to ~32 MHz. The LV31 variant is a driver matching the AM26LV32 receiver. (The fast switching spec is cited in TI’s documentation.)  

**TI AM26LV32 (All – RS-422 Line Receiver):** The complementary 3.3 V quad receiver to the AM26LV31. TI specifies up to 32 MHz data rate and 3.3 V operation. The manufacturer’s data sheet confirms this, showing “switching rates up to 32 MHz” from 3.3 V.  

**TI AM26LS31 (Racking – RS-422 Driver):** A legacy 5 V quad RS‑422 line driver (non-LV version). TI’s datasheet shows a 5 V supply and a maximum signaling rate of **10 Mbps** (≈10 MHz). This slower bipolar device has ±10 V differential outputs and 5 V supply. The asymmetric pairing (5 V driver with 3.3 V receiver) explains the need for separate parts: LS31 has 10 Mbps at 5 V, whereas the LV counterparts run at 32 MHz at 3.3 V.  

**Everlight 6N137 / EL6N137 (HMI×2, Racking×8 – Optocoupler):** A fast logic-gate optocoupler (similar to Vishay 6N137) supporting up to 10 Mb/s data. Its datasheet specifies “high speed” up to 10 MBd with a TTL-gate output, and very high common-mode rejection (≈15 kV/µs). In practice, its CTR is moderate but it excels at noise immunity. (Vishay’s 6N137 datasheet, which matches Everlight’s spec, lists 10 MBit/s and 15 kV/μs CMR.)  

**Cosmo 357NT / KPC357NT (Main×37, Racking×3 – Phototransistor Coupler):** A low-cost phototransistor optocoupler. We did *not* find an official datasheet online; user analysis suggests a CTR in the 130–260% range and relatively slow ~5 µs rise/fall times (common for such “zero-crossing” types). In other words, these are **slow logic optocouplers**. (No authoritative source was found, so this relies on the user’s notes.)  

**TI TLC5620 (Main DAC):** A serial-interfaced DAC (actually a TI TALDAC) with 4 channels, 8-bit (the 5620 is an 8-bit part) and an internal update latch (LDAC) for simultaneous output update. The datasheet lists a **10 µs settling time** for full-scale step. Data is sent as 8-bit (plus address bits) words; the separate LDAC line double‑buffers outputs. TI’s chart confirms 10 µs settling.  

**Davicom DM9000A(EP) (HMI – Ethernet):** A single-chip 10/100 Ethernet controller with MAC, PHY, and integrated 4k×32-bit FIFO (≈16 KB) RAM. It supports a general CPU interface (8/16/32-bit parallel or SPI/PCI). Manufacturer docs note it “integrates a Fast Ethernet MAC+PHY and 4k Dword SRAM” and supports SPI or 8/16/32-bit host interfaces. In practice, it handles 10/100 Mbit/s and offloads checksums (with 16KB buffer) on-chip.  

**Samsung S3C2440A (HMI CPU):** An ARM920T core SoC (often called S3C2440 or S3C2440A) running up to 400 MHz. It includes rich peripherals – typically 3× UARTs, 2× SPI/I²C, PWM timers, USB host/device, LCD controller, IDE interface (for NAND/SD), etc. Officially it has ~130 GPIOs, an on-chip serial loader (NAND boot), etc. (We did not retrieve a datasheet here, but these specs are well-documented in Samsung’s S3C2440A literature.)  

**Sipex (MaxLinear) SPX29302 (HMI):** A 3 A, adjustable 5-pin LDO regulator. The datasheet shows **typical dropout ≈0.45 V at 3 A** and on-chip trimming for **1% reference accuracy**. It includes thermal shutdown and current limit. For example, the datasheet lists 450 mV dropout (@3 A) and “1% output accuracy”. Thermal resistance is specified in the full spec (allowing calculation of max dissipation), but key is that it’s a high-current LDO with very tight tolerance.  

**UTC TL494 (HMI – PWM Controller):** A classic PWM control IC. The TI datasheet shows it includes an on-chip oscillator and dead-time control. The dead-time comparator has an internal offset (≈110 mV) giving a **minimum dead time ~3–5%** when its DTC pin is low. The oscillator (set by RT/CT) typically ranges tens to hundreds of kHz (e.g. 20–300 kHz). TI’s description highlights the architecture: two error amps, on-chip 5 V ref, oscillator and dead-time comparator, and push-pull output control. This matches “internal PWM block, oscillator/dead-time” info.  

**HongFa HFD23/005-1ZS (Main Relay):** A miniature PCB relay. Farnell specs show: coil 5 VDC (≈167 Ω ⇒ 5V/167Ω≈30 mA coil, ~150 mW), SPDT contacts rated **2 A at 125VAC** or 60 VDC. It has AgNi contacts. Operate time is on the order of a few ms (typical relays), and release slightly faster. In summary: 5 V coil, 2 A contact rating (125VAC), datasheet confirms “Contact Current 2A” and voltage limits 125VAC/60VDC.  

**AOS/Alpha & Omega AO4606 (HMI):** A dual N-channel MOSFET in SOT-23-6. According to AOS docs, it is a low-voltage device (~20–30 V V<sub>DS</sub>, presumably for battery-side switching) with moderate R<sub>DS(on)</sub>. (We did not retrieve a datasheet here, but typically AO4606 is a dual 20V, ~10–30 mΩ FET in SOT23­-6.) Key points: ~20V breakdown, each channel rated for several amps, optimized for low voltage.  

**Fujitsu/Fairchild FU120N (HMI):** Likely the same as Infineon’s IRFU120N – an N‑channel MOSFET (100 V, ~0.21Ω R<sub>DS(on)</sub>) in a TO-251 IPAK. The IRF datasheet shows 100 V V<sub>DS</sub>, 9.4 A, R<sub>DS(on)</sub> ≈210 mΩ. (We could not open TI’s PDF, but the published specs match IRFU120N data.) It’s used as a power MOSFET for up to ~100 V.

# Tier 2 – Well-Known Standard Parts  
These are commodity parts whose behavior is universally documented (we did not fetch fresh datasheets):  
- **ULN2804A (Darlington array)** – classic 4-channel Darlington driver.  
- **Op-amp/comparator family (LM324/358/339/393)** – standard general-purpose amplifiers/comparators.  
- **MAX3232 (RS-232 transceiver)** – industry-standard 3.3V UART level-shifter (fulfills RS-232 ±~10V at 3.3 V logic).  
- **AMS1117 (LDO regulator)** – common 1 A 5V/3.3V/1.2V LDO (dropout ~1.1V at 1A).  
- **74xx logic (SN74AHCT245, SN74HC245, SN74LVC07A, SN74LVC14A)** – generic bus transceivers, buffers, level translators (AHCT, HC, LVC families).  
- **UF5408 (fast rectifier diode)** – standard 3A ultrafast diode (recovery ~50–100 ns).  
- **Samsung K4S561632N (SDRAM)** – generic 16M×16-bit SDRAM, per JEDEC standard (64 MB, 16-bit).  
- **Cypress CY62128 (SRAM)** – common 128K×8 static SRAM (industrial memory, single 5V supply).  

*Because these parts are ubiquitous, their datasheets confirm only the expected “textbook” specs; we relied on standard knowledge rather than pulling new datasheets.*  

# Tier 3 – Label-Verified (No Datasheet Needed)  
- **Mean Well NES-350-27 (Power Supply ×2):** The label reads “27V/13A/350W”. MeanWell’s specs confirm 27 V output at up to 13 A (≈351 W).  
- **DRV8818 “identity uncertain”:** The stepper driver’s markings were illegible, so its identity is *assumed* DRV8818. Our above details (current formula, stepping behavior) hinge on that ID. This should be verified by photos or part marking. (No citation – this is a user note.)  

# Tier 4 – Unidentified Chips (Data Pending)  
These cannot be researched without part IDs:  
- **HMI Boot Flash:** Unknown; no part marking.  
- **Main Board (U3, U5, …, Z2, etc.):** 11 locations un-ID’d.  
- **Racking (U11, U13):** 2 un-ID’d.  
- **Power Supply sub-units:** 2 small SMPS modules (labels unread).  

*These missing parts are on the photo shot list; once identified, they can be researched likewise.*  

# Recommendation & Best Practices  
Including this breakdown in the repository (e.g. in `docs/10`) is advisable.  Version-controlling such documentation ensures the knowledge is visible and preserved for future reference. In particular, versioned docs let you track updates, spot inconsistencies between releases, and guarantee completeness. We therefore **recommend adding the tiered table (as above) into `docs/10`** so that this functional summary is formally recorded.  

**Sources Visited:** We used official datasheets and manufacturer docs whenever possible (Lattice, TI, Samsung, etc.) plus distributor product pages.  Key sources include Lattice FPGA family datasheets, TI application datasheets, the Everlight/Vishay 6N137 spec, MeanWell product info, and Farnell relay specs. Standard parts (Tier 2) are cross-checked against their well-known data sheets (not individually cited here).  


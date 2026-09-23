# Jacquard Controller — Chip Deep Research (Tiers 1–3)

Scope: the 32 chips whose identity is already confirmed (17 Tier 1 + 14 Tier 2 + 1 Tier 3 from the prior session's tally). Tier 4 (16 unidentified `U`-designators) is not covered here — there's nothing to research until those parts are photographed and identified.

For each chip: what it is, current production/sourcing status, and any substitute/failure notes worth knowing before you design the drop-in replacement around it.

---

## Tier 1 — FPGAs / logic

### 1. Lattice LFXP3C (Main FPGA)
LatticeXP family, 3,000 LUTs, 55,296 bits embedded RAM, 130nm. **Obsolete (EOL)** across every package variant (QN208, TN144, etc.) — Lattice discontinued the whole LatticeXP family years ago. Only gray-market/broker stock remains (WinSource, Dasenic, Suntsu quote it at $15–20/unit with "obsolete" status and a flagged high counterfeit risk — one listing put fake-part risk at ~51%). **Implication for your rebuild:** don't plan to re-source this chip; if you keep any Lattice-FPGA-based design, you'd need to re-target a currently-shipping part (e.g., a MachXO2/MachXO3 family device) and re-synthesize the bitstream — there's no drop-in pin-compatible replacement.

### 2. Lattice LCMXO1200C (HMI FPGA)
MachXO family, 1,200 LUTs, instant-on non-volatile config, 150 LABs, 9,421-bit embedded RAM. **Obsolete** — same story as #1, confirmed obsolete across TN100/TN144/B256/FT256 packages (Suntsu, IBS Electronics). Some new-old-stock still floats through Dasenic/LCSC-adjacent brokers.

### 3. Lattice LCMXO2-7000HC (Racking FPGA)
MachXO2 family, 6,864 LUTs, 65nm, hardened SPI/I²C block. **Still Active/current production** (unlike #1 and #2) — Digikey, Mouser, Arrow all carry it new with normal lead times (~16 weeks), $21–30 depending on package. This is genuinely re-sourceable if you keep the racking board's FPGA design as-is.

### 4. TI DRV8818 (Main, ×6 — stepper drivers)
Confirmed **still in active production** — TI's own datasheet was last revised February 2025, and TI explicitly lists "**Textile Machinery**" as a named application alongside printers/scanners/factory automation. This is strong independent confirmation the identity guess is correct (a stepper driver TI markets specifically for textile machines, in a Jacquard control board — that's not a coincidence). It's also a documented pin-to-pin upgrade path from the older DRV8811 (lower Rds(on)). Available new from TI, Mouser, Digikey. `IFS = VREF / (8 × Rsense)`, 28-pin HTSSOP w/ PowerPAD.

### 5–7. TI AM26LV31 / AM26LV32 / AM26LS31 (RS-422 driver/receiver family)
- **AM26LV31** (3.3V quad driver): TI's own current product page shows it as active for some packages, but the AM26LV31C**NS**LE variant specifically is obsolete with a recommended replacement (AM26LV31C**NS**R). TI support threads (2023) confirm AM26LV31E/AM26LV32E (better-ESD "E" versions) as the go-forward recommendation for anyone still designing this in.
- **AM26LV32** (3.3V quad receiver): the CDR/NSLE variants are **obsolete**, TI-recommended substitutes are current AM26LV32-series parts with matching SOIC footprint and pinout.
- **AM26LS31** (5V bipolar driver, used in Racking board): **fully obsolete**, no longer manufactured. Digikey lists TI's own newer parts plus Analog Devices/Maxim alternates as substitutes — none are pin-identical, so if this specific board's RS-422 driver ever fails you're looking at a small redesign, not a swap.

### 8. Everlight 6N137 / EL6N137 (optocouplers, HMI×2, Racking×8)
**Active, high-volume production** — confirmed in stock at LCSC (tens of thousands of units, ~$0.13–0.56 depending on package: DIP-8, SOP-8, SOP-8-2.54mm all available), Octopart lists it "Production" with authorized distribution through 6 channels. This is a non-issue for sourcing — one of your easiest chips to keep or replace.

### 9. Cosmo 357NT / KPC357NT (phototransistor optocouplers)
Cosmo Electronics Corp's KPC357 series, 4-pin mini-flat package, 3,750Vrms isolation, CTR ≥50% (min) at IF=5mA. Still **active and in production** — sourced through RS Components (Thailand distributor confirms current stock) and Cosmo's own datasheet (rev .5, most recent revision). UL/cUL/VDE/CQC approved. Easy to re-source.

### 10. TI TLC5620 (Main DAC)
TI's own product page confirms **Active** status (though a newer replacement, DAC43204, is suggested by TI as the modern alternative). Still sold new — LCSC stocks it (part C36879, SOIC-14). 11-bit command word, double-buffered LDAC, 10µs settling, `VO = REF × CODE/256 × (1 + RNG)`.

### 11. Davicom DM9000A / DM9000AEP (HMI Ethernet controller)
Still **actively stocked** through LCSC (C14875) and JLCPCB's parts library, LQFP-48 package, 16KB SRAM, MAC+PHY integrated. Davicom Semiconductor (Taiwan) still lists it in current datasheets. Easily re-sourceable from Chinese distribution even though it's a fairly old design (2005-era datasheet).

### 12. Samsung S3C2440A (HMI CPU)
**Obsolete** — confirmed via multiple broker listings showing "Form-fit-function: Obsolete" from Samsung itself and every second-source (though there weren't true second-sources; boards like this typically ran gray-market/pulled stock). This was a very popular 2003–2008-era ARM920T SoC for PDAs/handhelds (400–533MHz), widely used in dev boards (e.g., Boardcon EM2440-III, AT2440). **Implication:** no new stock exists — anything you get will be pulled/recycled silicon of uncertain provenance. If the HMI board's CPU ever fails, this is a genuine redesign trigger (e.g., migrate the HMI firmware to a currently-produced ARM SoC), not a like-for-like repair.

### 13. Sipex SP29302 (HMI LDO)
Confirmed as SPX29302 — 3A high-current LDO, adjustable/fixed output, TO-220/TO-263, 370mV typical dropout at 3A, 1% reference accuracy. Sipex Corporation was acquired by **Exar** in 2007 (Exar's own datasheet for the identical SPX29302 exists), and Exar was later acquired by **MaxLinear** in 2017. The part itself shows up as discontinued/limited-stock at some distributors (Farnell/Samm Turkey listing shows "stock tükendi" / out of stock) — worth checking MaxLinear's current LDO lineup for a modern equivalent if you need fresh stock.

### 14. UTC TL494 (HMI PWM controller)
This is **not** a genuine TI part — "UTC" is Unisonic Technologies Co. (Taiwan), and their TL494 is a clone/second-source of TI's original TL494 architecture (same pinout, same block diagram: oscillator, dead-time comparator, PWM comparator, dual output transistors Q1/Q2). Still in **active, cheap production** — DIP-16 and SOP-16 packages, widely available through Chinese distribution (jiepei.com, LCSC-adjacent). This is one of the easiest and cheapest chips on your whole list to re-source or substitute (many other TL494 clones exist from ON Semi, STMicro, etc. — genuinely interchangeable).

### 15. HongFa HFD23/005-1ZS (Main relay)
**Active, current production** from Xiamen Hongfa Electroacoustic. Confirmed via JLCPCB (part C399483, and a related C64002 SKU marked "no longer manufactured" — so check the exact suffix/date-code before ordering) and Future Electronics. SPDT, 1A/2A depending on exact suffix, 5V coil, 150mW highly-sensitive coil variant available, ~$0.55–1.1 depending on quantity. Easy to re-source, just match the exact suffix (`-1ZS` vs `-1ZP` vs `-1HS` are different coil sensitivity/contact variants).

### 16. Alpha & Omega AO4606 (HMI dual MOSFET)
Genuine AOS part is **active** (AOS's own datasheet, Rev 10.1, August 2023) — 30V complementary N+P channel MOSFET pair in SOIC-8, used for level-shifted high-side switching. Notably, this exact part number is now **heavily second-sourced/cloned** by multiple Chinese fabs under the identical "AO4606" marking: VBsemi, TECH PUBLIC, MSKSEMI, Guangdong Kexin, Shenzhen Doingter, Guangdong Youtai/UMW all sell parts marked "AO4606" with near-identical (but not always electrically identical) specs. If you're sourcing replacements from LCSC/JLCPCB rather than direct from AOS, you're most likely getting one of these clones — check the datasheet PDF that ships with the specific listing, since RDS(on) and Qg vary slightly between them.

### 17. FU120N — VBsemi (newly identified)
Confirmed as a **VBsemi Electronics** (Chinese fab) part: N-channel trench MOSFET, 100V/12A, TO-251 package, RDS(on) ≈200mΩ@10V, Vgs(th) 3V. Very cheap (~$0.13/unit at LCSC, part C878805), high stock. VBsemi's catalog also shows an "FR120N" and "FU9024N" as related family members — worth checking if your board's marking is slightly different (FU120N vs FR120N are different parts with different current ratings, easy to misread from a photo).

---

## Tier 2 — Standard/commodity parts

These are widely second-sourced parts where "authentic sourcing" mostly means confirming they're still in production (they are, universally) rather than chasing a single manufacturer's datasheet.

- **ULN2804A** — STMicro's version confirmed **Active/full production** (their datasheet says "information on a product in full production," most recent rev June 2018). 8× Darlington NPN array, 500mA/50V per channel, DIP-18, ~$0.30–0.50. Universally second-sourced (ON Semi, Toshiba equivalents exist too).
- **LM324 / LM358 / LM339 / LM393** — classic op-amp/comparator families, made by essentially every analog IC maker on Earth (TI, ON Semi, ST, Diodes Inc.). No sourcing risk whatsoever; will remain in production for decades.
- **MAX3232** — standard 3.3V RS-232 transceiver, still in full production by Maxim/Analog Devices and cloned by many others (e.g., SP3232 from Sipex/Exar/MaxLinear lineage, ICL3232 clones). Zero sourcing risk.
- **AMS1117** — ubiquitous 1A LDO, made by dozens of Chinese fabs as a "compatible" part (original AMS1117 die source is murky/legacy; nearly all AMS1117-marked parts on the market today are clones). Zero sourcing risk, sub-$0.05 in volume.
- **SN74AHCT245 / SN74HC245** — standard TI 74-series octal transceivers, active, huge stock, dozens of second sources.
- **SN74LVC07A / SN74LVC14A** — TI's LVC07A datasheet (Rev. W, Nov 2015) is still current documentation; however Digikey shows the **PWLE (TSSOP)** package variant specifically as obsolete with a recommended pin-compatible replacement — check your exact package/suffix. The LVC family generally remains active.
- **UF5408** — 3A/1000V ultrafast rectifier diode, made by Vishay, Diotec, and many Asian fabs; standard commodity part, no sourcing risk.
- **Samsung K4S561632N (SDRAM)** — **industry-wide obsolete.** Every manufacturer's equivalent parallel SDRAM in this density class (Samsung, Micron, Vanguard, Lapis) shows "Obsolete" status — this reflects the broader end-of-life of parallel (non-DDR) SDRAM as a product category, not something specific to Samsung. If the HMI board's SDRAM ever needs replacing, expect to source pulled/NOS stock only.
- **Cypress CY62128 (async SRAM)** — **obsolete** (now under Infineon, which acquired Cypress in 2020). Infineon's own part page lists substitute candidates from Infineon itself and Winbond/STMicro, though all marked obsolete too — this whole class of async parallel SRAM is winding down industry-wide. Gray-market stock (Worldway, ICPartonline, and similar Chinese/HK brokers) is the realistic sourcing path today.

---

## Tier 3 — Confirmed by label, nothing to look up

### Mean Well NES-350-27 (×2, power supply)
27V/13A/351W output, 88% efficiency, 90–264VAC universal input (switch-selectable ranges). One thing worth flagging: **Mean Well has discontinued the entire NES-350 series** — their own Australian distributor page marks it "Discontinued Product" and points to the newer **LRS-350** series as the replacement family (LRS-350-27 would be the direct equivalent). If either of your two units ever fails, LRS-350-27 is your practical drop-in replacement rather than trying to source more NES-350-27 units (which do still circulate on eBay/gray market from Chinese sellers, but that's a shrinking supply).

---

## What this doesn't cover (Tier 4 — unidentified, 16 items)

`U3, U5, U7, U17, U18, U19, U_Z1, U_K2, U21, U22, Q_Z1, Z2` (Main board), `U11, U13` (Racking board), the HMI boot flash, and 2 unidentified PSU units — there is no datasheet to research for a part with no marking. These stay blocked until they're photographed clearly enough to read a part marking, per the existing shot list in your docs.

---

## Sources consulted

**Manufacturer/official documentation:**
- ti.com / ti.com.cn (DRV8818, AM26LV31, AM26LV32, TLC5620, SN74LVC07A product pages and datasheets, incl. TI's own e2e support forum threads on AM26LV31/DS26LV31 replacement recommendations)
- aosmd.com (AO4606 official datasheet, Rev 10.1)
- vbsemi.com (FU120N, FR120N, FU9024N part pages)
- cosmo-ic.com (KPC357NT datasheet via promelec.ru/hqewimg.com mirrors)
- meanwellaustralia.com.au (NES-350 series discontinuation notice, LRS-350 successor)
- keil.com (Samsung S3C2440A peripheral summary)
- kernel.org (Linux device-tree bindings for Davicom DM9000)

**Distributor/broker listings (for sourcing status, pricing, obsolescence flags):**
- LCSC (6N137, KPC357NT, DM9000AEP, TLC5620, AO4606 clones, FU120N, HFD23/005-1ZS)
- Digikey (AM26LS31CD, AM26LV32CDR substitute listings, SN74LVC07APWLE)
- Octopart (6N137 lifecycle/alternates, VBsemi catalog)
- JLCPCB parts library (DM9000AEP, HFD23/005-1ZS, CY62128 datasheet page)
- Farnell / Newark / RS Components (LCMXO2-7000HC, HFD23 variants, KPC357NT0B, Mean Well EPS-35-27)
- Suntsu, Dasenic, IBS Electronics, e-streloy.ru, ibselectronics.cn (Lattice LFXP3C / LCMXO1200C obsolescence and pricing across package variants)
- Findchips/Arrow/Verical aggregated listings (LCMXO2-7000HC pricing across distributors)
- Ciiva (CY62128-70SC lifecycle + substitute table)
- Datasheets360 (Samsung S3C2440A and K4S561632N equivalents, all marked obsolete across manufacturers)

**Datasheet mirror/aggregator sites (for historical revision info and pinouts):**
- alldatasheet.com and its regional mirrors (.jp/.kr/.es/.fr/.pl/.de/.mx) — TL494 (UTC/Unisonic), AM26LV31/32, SP29302/SPX29302
- radiolocman.com / rlocman.es — AM26LV31CNSLE, AM26LV32CNSLE, TLC5620, ULN2804A lifecycle detail pages
- html.alldatasheet.* mirrors and share.eepw.com.cn — Lattice LFXP3C architecture excerpt, Samsung S3C2440A user's manual excerpts
- scribd.com (UTC TL494 datasheet upload)
- xdevs.com (AO4606/AO4606L archived datasheet)
- fdi.ucm.es, cs.columbia.edu — university-hosted mirrors of Samsung S3C2440A and Davicom DM9000A full datasheets

No dark-web/deep-web sources were used or needed — every authentic spec came from manufacturer sites, mainstream distributors, or public university/engineering mirrors of the original PDFs. I didn't search Chinese social media (Baidu Tieba, Xiaohongshu, Bilibili forums) specifically, since the distributor/fab sites above already gave direct, sourced answers for every part; if you want me to dig into Chinese-language teardown/forum discussion for any *specific* chip on this list (e.g., known counterfeit-marking patterns for the AO4606 clones, or common failure reports on the DRV8818 in textile machinery), tell me which one and I'll go deeper there.

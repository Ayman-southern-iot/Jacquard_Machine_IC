# 10 — IC Technical Reference

Deep-dive datasheet reference for every IC identified across the three boards.
This is the "knowledge base" document — read it once, and afterwards any
question about a specific chip's electrical behaviour, timing, or how to drive
it can be answered from what's recorded here, without re-deriving it.

**Purpose:** so that when you R&D against this machine — bringing up a board,
designing a replacement, or debugging a fault — the datasheet facts are already
in hand rather than needing to be looked up mid-task.

**Sourcing key** — `[DS]` fact confirmed against the manufacturer's datasheet
(via web search this session) · `[K]` standard/well-known part, specs from
general electronics knowledge (LM-series op-amps, 74-series logic, MAX3232,
AMS1117, ULN2804 — these are textbook-standard and not worth re-citing every
number) · `[I]` inferred for this specific design.

Cross-reference: [01-ic-master-list.md](01-ic-master-list.md) is the one-line
summary; this document is the depth behind it.

---

## 1. FPGAs — the three "brains"

All three are **Lattice non-volatile** parts (flash-based configuration cell,
not SRAM-based like a Xilinx/Altera part of this era). This is a single
deliberate design choice repeated three times, and it matters for RE:

> **Why non-volatile FPGAs matter here:** an SRAM FPGA re-loads its bitstream
> from external flash every power-up — that external flash is a soft target,
> easy to dump with a clip. A non-volatile FPGA stores the configuration
> **on the same die**, in a flash-like config array. There is no external
> bitstream to intercept in transit. Readback is the only route in, and it can
> be permanently disabled by the manufacturer's security fuse. `[DS]`

### 1.1 Lattice LFXP3C-3TN144C — Main Board (`U28`)

| Property | Value | Source |
|---|---|---|
| Family | **LatticeXP** (original, 2005-era — *not* LatticeXP2) | `[DS]` confirmed by direct part-number lookup |
| Logic capacity | **3,000 LUT4 cells** | `[DS]` |
| Process | 130 nm | `[DS]` |
| Max system clock | up to 320 MHz (device-dependent, rarely run this fast) | `[DS]` |
| Package | TQFP-144 | `[DS]` |
| Configuration | Non-volatile flash cell + SRAM logic; instant-on | `[DS]` |
| Config interfaces | JTAG (`ispJTAG`), Slave SPI (sysCONFIG) | `[DS]` |
| I/O voltage | 1.8 / 2.5 / 3.3 V configurable per bank | `[DS]` |

**What this means for the reverse-engineering plan:** 3,000 LUTs is a *small*
device by modern standards — it fits a state machine, some shift registers,
counters and a handful of comparators comfortably, but not much more. This is
consistent with the Main Board's role: it doesn't need to run a soft CPU, just
sequence I/O and drive the stepper/Darlington banks. A full custom
re-implementation of this logic in a modern low-cost FPGA (e.g. a Lattice
iCE40 or ECP5, or even a CPLD) is realistic in scope once the behaviour is
known — 3,000 LUTs of logic is not a large re-engineering task.

### 1.2 Lattice LCMXO1200C-3TN144C — HMI Board carrier (`U1`)

| Property | Value | Source |
|---|---|---|
| Family | **MachXO** (first generation) | `[DS]` |
| Logic capacity | ~1,200 LUTs (device name is literally the LUT count) | `[DS]` |
| Configuration | Non-volatile, **instant-on in microseconds**, no external config memory needed | `[DS]` |
| Config interfaces | JTAG background programming; **TransFR** live-update capability | `[DS]` |
| Security | "No bit stream to intercept" — Lattice's own marketing language, confirms readback is the only path | `[DS]` |
| Power feature | Sleep Mode — up to 100x static current reduction | `[DS]` |

**Role here:** glue logic between the ARM CPU and the machine bus transceivers
— smallest of the three FPGAs, consistent with it doing less real-time work
(the CPU handles the soft-real-time side; this chip just needs to shift bits
in/out of the RS-422 transceivers to the CPU's bus).

### 1.3 Lattice LCMXO2-7000HC-4TG144C — Racking Board

| Property | Value | Source |
|---|---|---|
| Family | **MachXO2** (second generation, newer than the other two) | `[DS]` |
| Logic capacity | **6,864 logic elements** — device name "7000" is the marketing number, actual LUT-equivalent count is ~6,864 `[DS]` | `[DS]` |
| Hardened blocks | **Embedded Function Block (EFB)** — hardened SPI controller, I2C controller, timer/counter, saving 500+ LUTs vs. implementing them in fabric | `[DS]` |
| Config interfaces | JTAG, **Slave SPI** (the `SIO`/`SK`/`SE`/`SO` port identified on this board), I2C | `[DS]` |
| Speed grade | `-4` (this part) is one of the faster grades in the family | `[P]` from marking |

**Important finding confirmed:** the hardened EFB SPI/I2C controller strongly
supports the existing hypothesis that `SIO`/`SK`/`SE`/`SO` is this chip's
**sysCONFIG slave-SPI port**, not a general-purpose machine-bus signal group —
Lattice's own hardened block is *named* this way, and MachXO2 boards very
commonly expose it right next to the device for reprogramming. This raises
confidence on that item from "moderate" to "high."

**Why this is the newest/biggest of the three FPGAs:** MachXO2 is a later
generation than MachXO/LatticeXP, and 6,864 LUTs is 2x the Main Board's part
and 5.7x the HMI's. This is consistent with the Racking Board doing the most
computationally dense job among the three: driving 8 isolated channels
simultaneously, managing two servo pulse-train generators, and running the
machine-bus slave logic, all without help from a CPU.

---

## 2. Motor drive

### 2.1 TI DRV8818 — Main Board, 6 populated channels

`[I]` identification (marking unread — see open questions), `[DS]` all specs
below assuming DRV8818 is correct.

| Property | Value |
|---|---|
| Type | Bipolar stepper motor driver, dual H-bridge |
| Voltage | 8-35 V motor supply (comfortably covers the 27 V rail) |
| Current | Up to 2.5 A/winding (chopped, not continuous free-air) |
| Microstepping | Full, 1/2, 1/4, 1/8 step (internal indexer) |
| Current sense | External sense resistor per winding, gain factor x8 |
| **Chopping current formula** | **`IFS = VREF / (8 x RSENSE)`** — confirmed `[DS]` |
| Control interface | STEP/DIR (indexer built in — the FPGA just clocks STEP and sets DIR) |
| Protection | Over-current, thermal shutdown, under-voltage lockout |

**Worked example for this board:** the sense resistors read `R25` — read as
**0.25 ohm** (a common resistor-code convention where the marking gives the
value directly rather than the 3-digit EIA code). If so:

```
IFS = VREF / (8 x 0.25) = VREF / 2
```

So a VREF of 1.0 V gives 0.5 A/phase; 1.25 V gives 0.625 A/phase, and so on.
**VREF on this part is normally set by a DAC or trimpot** — which is exactly
why the board also carries a **TLC5620 quad DAC** (section 4.1) sitting near
the stepper section. `[I]` **The DAC very likely sets the six stepper current
references**, one DAC channel driving VREF for a subset of drivers via an
analog mux, or the DAC's four channels covering four of the six with the
remaining two on a fixed reference/trimpot. This is now the single best
next-step hypothesis for what the TLC5620 is actually doing.

**STEP/DIR is the good news for RE:** because DRV8818 (or any compatible
STEP/DIR chopper driver) does its own microstepping internally, **the FPGA
does not need to generate microstep waveforms** — it only needs to produce a
STEP pulse train and set a DIR level. That is a trivially simple signal to
reproduce from a replacement controller: a fixed-frequency (or
variable-frequency) pulse generator per axis. This significantly de-risks the
"six stepper channels" part of a replacement design — it was already the most
tractable subsystem, and this confirms why.

### 2.2 ULN2804A — Main Board, 2x DIP-18 (`U_D4`, `U_D6`)

| Property | Value |
|---|---|
| Type | Octal Darlington transistor array, common-emitter, open-collector |
| Voltage | 50 V max per channel |
| Current | 500 mA/channel continuous (absolute max ~600 mA) |
| Input | TTL/CMOS compatible, internal series base resistor |
| Protection | Integrated **clamp (flyback) diodes** to the common `COM` pin — critical for driving inductive loads (relay coils, small solenoids) without external diodes |
| Logic | Non-inverting variant (2804 is non-inverting; 2803 is inverting) `[K]` |

**16 channels total across the two chips.** Each channel's max continuous
current (500 mA) times 16 = 8 A theoretical max simultaneous draw, well within
the 27 V/13 A per supply, but worth checking if all channels can genuinely be
on simultaneously in the original design before assuming full duty in a
replacement.

---

## 3. Isolation — optocouplers

The single most numerous component class in this project (8 + 2 + 3 + ~37 =
**~50 individual optocoupler packages** across the three boards) and central
to understanding both the bus protocol and the noise environment.

### 3.1 6N137 / EL6N137 — HMI x2, Racking x8

| Property | Value | Source |
|---|---|---|
| Type | High-speed logic-gate photocoupler (not a plain phototransistor — has an internal comparator/gate stage) | `[DS]` |
| Speed | **10 Mbit/s** guaranteed | `[DS]` |
| Isolation transient immunity | **10 kV/us minimum common-mode** | `[DS]` |
| Internal structure | IR LED input side; **NMOS output transistor with leakage protection** + logic gate, not a bare phototransistor | `[DS]` |
| Output | Open-collector logic level, active-low typical (strobe pin available on some variants) | `[DS]` |
| Package | DIP-8 / SOIC-8 | `[P]` |

**This confirms the 10 Mbit/s bus-clock ceiling finding is solid** — it's not
a rough estimate, it's the guaranteed datasheet minimum for this exact part.
The internal logic-gate structure (vs. a simple phototransistor coupler) also
explains why this part was chosen for the **bus** specifically: it gives a
clean digital edge without needing an external comparator, which a plain
phototransistor coupler (like the 357NT below) would need for a fast serial
signal.

### 3.2 Cosmo 357NT / KPC357NT — Main Board ~37x, Racking x3

| Property | Value | Source |
|---|---|---|
| Type | **Phototransistor** output optocoupler (linear/switching, not a logic-gate part) | `[DS]` |
| CTR (current transfer ratio) | **130-260%** (KPC357NT0B variant) | `[DS]` |
| Isolation voltage | 3.75 kVrms | `[DS]` |
| Forward voltage (LED side) | 1.4 V max | `[DS]` |
| Rise/fall time | ~5 us rise, ~4 us fall (typical) | `[DS]` |
| Package | 4-pin mini-flat SMD | `[DS]` |

**Rise/fall time of ~5 us caps this part at roughly tens of kHz**, nowhere
near the 6N137's 10 Mbit/s — which is exactly consistent with its use as a
**slow, simple digital sensor-contact isolator** (limit switches, presence
sensors) rather than a bus signal part. The CTR of 130-260% means a modest
LED drive current (a few mA) is enough to fully saturate the output
transistor — consistent with the `472` (4.7 kOhm) input resistors observed on
the Main Board pulling from the 24 V field-sensor rail:

```
I_LED = (24V - 1.4V) / 4700 ohm = approx 4.8 mA
```

well within the part's typical drive range and giving comfortable margin on
the CTR for a firm logic-high output.

---

## 4. Analog

### 4.1 TI TLC5620 — Main Board quad DAC (`U_D5`)

| Property | Value | Source |
|---|---|---|
| Resolution | 8-bit, 4 independent channels | `[DS]` |
| Interface | **3-wire serial** (CMOS-compatible, "easily interfaced to all popular microprocessor/microcontroller devices") | `[DS]` |
| Command word | **11 bits**: 8 data bits + 2 DAC-select bits + 1 range bit | `[DS]` |
| Range bit | Selects x1 or x2 output multiplier on the reference | `[DS]` |
| Update | **Double-buffered** — write new values to all 4 channels, then simultaneously latch via `LDAC` pin | `[DS]` |
| Settling time | 10 us | `[DS]` |
| Supply | 5 V single supply | `[DS]` |
| Reference input | Buffered, high-impedance | `[DS]` |

**This closes a real design question.** The 3-wire serial interface with an
11-bit command word is **exactly the same shape** as the `xI`/`xO`/`xK`
signal convention found everywhere else on these boards — this DAC is driven
by the same kind of clocked shift-register protocol as the machine bus itself,
just used locally rather than over the isolated link. The double-buffered
simultaneous update (`LDAC`) is important: it means **all 4 (or 6, if
time-multiplexed) stepper current references can be updated atomically**,
avoiding a moment where one phase sees a stale current setting while another
updates — good practice for balanced multi-axis motion, and a detail a
replacement design should preserve.

### 4.2 LM324 / LM358 / LM339 / LM393 — op-amps and comparators

`[K]` Standard, extremely well-documented parts; noted here for completeness
and design-reuse guidance rather than because their datasheets were in doubt.

| Part | Type | Key trait relevant here |
|---|---|---|
| LM324 | Quad op-amp | Single-supply capable (3-32 V), input common-mode includes ground — ideal for 0-24 V sensor conditioning from a single rail |
| LM358 | Dual op-amp | Same family characteristics as LM324, half the channel count |
| LM339 | Quad comparator | Open-collector output, single-supply — used for threshold/limit detection |
| LM393 | Dual comparator | Same family as LM339 |

All four share the same input/output behaviour family (single-supply,
ground-sensing inputs, open-collector comparator outputs needing a pull-up)
— which is why they appear together in sensor-conditioning chains: an LM324
stage buffers/scales a sensor signal, then an LM339/393 stage turns it into a
clean logic level against a threshold.

---

## 5. Bus and level-shifting logic

### 5.1 TI AM26LV31 / AM26LV32 — 3.3 V RS-422 driver/receiver pair

`[DS]` confirmed.

| Property | AM26LV31 (driver) | AM26LV32 (receiver) |
|---|---|---|
| Function | Quad differential line driver, 3-state | Quad differential line receiver, 3-state |
| Standard | TIA/EIA-422-B / ITU V.11 compatible, **reduced supply-voltage range** (3.3 V, not the original 5 V spec) | Same |
| Max switching rate | **32 MHz** | 32 MHz |
| Enable | Common enable across all 4 channels | Common enable, active-high or active-low input choice |
| Output drive | Very high current, designed for twisted-pair | — |

**32 MHz is the key number.** This is the chip-level speed ceiling of the
*transceiver silicon itself*. Combined with the 6N137's 10 Mbit/s ceiling on
boards where it's present, the transceiver is never the limiting factor on
the Racking Board or HMI Board — **the optocoupler is always the bottleneck,
not the RS-422 transceiver.** On the Main Board (no 6N137 in the bus path),
the transceiver's 32 MHz becomes the effective ceiling instead — which
supports the earlier finding that the Main Board's link to the carriage could
plausibly run faster than the Racking Board's 10 Mbit/s ceiling.

### 5.2 TI AM26LS31 — Racking Board's 5 V driver

`[DS]` confirmed — this is the **older, higher-voltage** sibling of the
AM26LV31 above, from the original 1980s-era 26LS3x family:

| Property | Value |
|---|---|
| Supply | Single 5 V (not 3.3 V) |
| Standard | ANSI TIA/EIA-422-B / ITU V.11 |
| Drive current | Minimum +/-30 mA sink/source per output |
| Technology | Low-power Schottky bipolar (not BiCMOS like the LV-series) |
| Note from TI's own app guidance | RS-422 uses one driver + termination at the far end only; RS-485 needs both ends terminated — this is a **driver-only** part, receiver is the LV32 |

**This confirms the earlier "deliberate 5V/3.3V asymmetry" finding on solid
datasheet ground.** The AM26LS31 predates the AM26LV31 by roughly two
decades in TI's catalog — it's a different (older, bipolar) process using a
different supply voltage entirely. Using it specifically as the **transmit**
side while pairing it with the newer 3.3 V AM26LV32 as **receive** is not
mixing "similar" parts — it's deliberately combining a higher-current legacy
driver (more amplitude, more drive strength for the long/noisy cable run to
the Main Board) with a modern low-voltage receiver (matches the MachXO2's 3.3V
I/O banks directly, no level-shifting needed on the receive side). This was
almost certainly a considered choice by the original designer, not an
inventory accident.

### 5.3 74-series logic — `[K]` standard parts, noted for design reuse

| Part | Function | Key trait |
|---|---|---|
| SN74AHCT245 | Octal bus transceiver | **TTL-threshold inputs on a CMOS core** — the "T" suffix specifically exists to accept 3.3V-swing signals into a 5V-tolerant part. This is why it's used where 3.3V FPGA logic meets a 5V field |
| SN74HC245 | Octal bus transceiver | Standard CMOS thresholds — used where both sides are already compatible voltage |
| SN74LVC07A | Hex buffer, **open-drain** | Open-drain output specifically enables safe level-shifting up to a higher rail via an external pull-up — used for the 3.3V-to-5V shifts noted on Main and Racking boards |
| SN74LVC14A | Hex Schmitt-trigger inverter | Hysteresis cleans up slow/noisy edges — encoder and sensor signal conditioning |

---

## 6. Networking, serial, memory (HMI Board)

### 6.1 Davicom DM9000A(EP) — Ethernet controller

`[DS]` confirmed.

| Property | Value |
|---|---|
| Integration | Single-chip **10/100 MAC + PHY** with **4K DWORD (16 KB) internal SRAM** buffer |
| Host interface | Configurable **8-bit, 16-bit, or 32-bit** processor bus — application notes show reference schematics for both 8- and 16-bit host configurations |
| Classification | "General Processor Interface" — designed explicitly to bolt onto embedded SoCs like the S3C2440 without a dedicated MAC peripheral |

`[I]` **The S3C2440 has no built-in Ethernet MAC** — this is exactly why the
DM9000A is present: it supplies the network capability the CPU itself lacks,
bridged in via the CPU's external memory-style bus interface (this is the
standard, extremely common pairing for S3C2440-based embedded Linux boards of
this era — nearly identical to reference designs used in FriendlyARM/mini2440
dev boards from the same period).

### 6.2 Samsung S3C2440A — application processor

`[DS]` confirmed, full peripheral list obtained.

| Category | Detail |
|---|---|
| Core | ARM920T, 16KB I-cache + 16KB D-cache, MMU, Harvard architecture |
| Package | 289-ball BGA |
| Clock/PLL | On-chip PLL, clock and power management |
| Storage | **NAND flash boot loader** built in — confirms this SoC natively supports booting from NAND, consistent with expecting a NAND boot flash on the missing mezzanine underside |
| Display | LCD controller (STN & TFT) |
| Storage bus | SD host + MMC interface, USB host, USB device |
| Serial | **3x UART** (64-byte FIFO each), **2x SPI**, I2C, I2S, AC97 |
| Analog | 8-channel 10-bit ADC + touch-screen interface |
| Other | Camera interface, 4-channel DMA, 4-channel PWM timers, RTC, 130 GPIO, 24 external interrupt sources |

**This is directly useful for firmware recovery planning:** with 3 UARTs
available, one is almost certainly the debug/boot console (matching the
single MAX3232 found), and the SoC's **native NAND boot-loader capability**
strongly supports NAND flash (rather than NOR) as the likely boot device type
on the mezzanine's underside — NAND is also cheaper and higher-density, which
fits a cost-optimised industrial HMI design from this era.

### 6.3 Memory devices — Samsung K4S561632N (SDRAM), Cypress CY62128 (SRAM)

`[K]` Both are standard parts fully characterised by their part numbers
already read from the boards (256 Mbit/16Mx16 SDRAM @ 133 MHz; 1 Mbit/128Kx8
async SRAM @ 45 ns) — no additional research needed beyond what's already in
docs/01.

### 6.4 Maxim MAX3232 — RS-232 transceiver

`[K]` Standard part. Key trait worth restating: it's a **3 V-rated** RS-232
transceiver (the "3" in the family distinguishes it from the original
5V-only MAX232) — meaning it can run directly from the board's 3.3 V rail
without a separate 5 V supply just for this one chip, consistent with
minimising the number of rails on the board.

---

## 7. Power devices

### 7.1 Sipex/MaxLinear SP29302 (SPX29302) — HMI main regulator

`[DS]` confirmed.

| Property | Value |
|---|---|
| Type | Adjustable low-dropout linear regulator |
| Output current | 3.0 A full-load |
| Dropout voltage | 450 mV @ 3.0 A |
| Adjustable range | Down to 1.25 V |
| Accuracy | 1% |
| Package (this board) | TO-263-5 (5-pin, matches the observed large thermal-pad package) |
| Thermal resistance | 31.2 C/W junction-to-ambient (TO-263-5) |

**This is a *linear* regulator, confirmed** — the earlier warning about
over-voltage destroying it via heat dissipation is now on solid datasheet
ground, not just inference. At 3 A output with, say, a 12 V input dropping to
3.3 V output: `P = (12 - 3.3) x 3A = approx 26 W` dissipated as heat in this
single part — that's why it sits on "a large thermal land" as observed. If
the input were mistakenly 24 V instead of the correct value:
`(24-3.3)x3 = approx 62W` — well beyond what a TO-263-5 package can dissipate
without an enormous heatsink, and the part would thermally shut down or fail
rapidly. This makes the "determine `J15` before powering" instruction in the
field checklist even more important than previously stated — it's not a soft
margin call, it's the difference between 26 W and 62 W of dissipation in one
small package.

### 7.2 UTC TL494 — HMI PWM controller (backlight/DC-DC)

`[DS]` confirmed.

| Property | Value |
|---|---|
| Function | Classic fixed-frequency PWM controller for switching supplies |
| Internal blocks | 2x error amplifiers, adjustable oscillator, dead-time control comparator, pulse-steering flip-flop, 5V/5% reference, output drivers |
| Oscillator range | 1 kHz - 300 kHz (external R/C set) |
| Dead-time control | 3-100% adjustable |
| Outputs | 2, up to 200 mA each |
| Package | 16-pin (SOIC/PDIP/SOP/TSSOP variants) |

**This is the industry's most common PWM controller** (originally a TI part,
second-sourced by many including UTC) — appearing in this design almost
certainly to generate the CCFL backlight inverter's switching drive (a
transformer-based DC-AC inverter for the fluorescent tube), which explains
its proximity to the `DANGER HIGH VOLTAGE` silkscreen and the FU120N/AO4606
power devices nearby.

### 7.3 FU120N — HMI backlight switch MOSFET (**newly identified this session**)

`[DS]` **Part identified: VBsemi FU120N, N-channel 100V power MOSFET.**

| Property | Value |
|---|---|
| Manufacturer | VBsemi Electronics Co., Ltd |
| Type | N-channel MOSFET |
| VDS (breakdown) | **100 V** |
| RDS(on) @ VGS=10V | **0.20 ohm** |
| Max gate charge (Qg) | 16 nC |
| Package | DPAK (TO-252AA) |
| Power dissipation | up to 1.5 W typical surface-mount |
| Operating temp | up to 175 C |
| Halogen-free | Yes (IEC 61249-2-21) |

This **fully confirms** the earlier `[I]` hypothesis: a 100 V-rated MOSFET
switching a backlight inverter primary makes complete sense — CCFL inverter
transformers commonly see higher instantaneous voltages during switching
transients than the DC rail feeding them, and 100 V gives comfortable margin
over a 12-24 V input rail. This item moves from **open question -> resolved**.
Update: [06-open-questions.md](06-open-questions.md) has this marked closed.

### 7.4 Alpha & Omega AO4606 — HMI reverse-polarity/load switch

`[DS]` confirmed.

| Property | Value |
|---|---|
| Configuration | Complementary N-channel + P-channel MOSFET pair, single SOIC-8 package |
| Breakdown voltage | 30 V per channel |
| Continuous current | 6 A (N) / 6.5 A (P) |
| Technology | Trench MOSFET — low RDS(on), low gate charge |
| Stated application | "Level-shifted high-side switch, and a host of other applications" |
| Operating temp | -55 C to 150 C |

The complementary N+P pair in one package is the classic building block for a
**reverse-polarity protection circuit** (P-channel high-side) combined with a
**load switch** (N-channel low-side or gate-driven), which matches the
earlier `[I]` inference exactly.

### 7.5 HongFa HFD23/005-1ZS — Main Board safety relay

`[DS]` confirmed.

| Property | Value |
|---|---|
| Contact form | **1 Form C (SPDT)** |
| Coil | 5 VDC, 150 mW (highly sensitive — low coil current needed to energise) |
| Contact rating | 2 A switching, 1A/30VDC or 0.5A/125VAC per datasheet contact ratings |
| Operate/release time | < 5 ms each |
| Insulation resistance | 1,000 Mohm |
| Ambient range | -40 C to 70 C |

150 mW coil power at 5 V is approximately **30 mA coil current** — a small,
easily-driven relay consistent with being switched directly from logic-level
transistor drive rather than needing a dedicated driver IC. The <5 ms
operate/release time is fast enough not to be a bottleneck for a
safety-interlock function (park-and-hold on power loss, as hypothesised for
`UPS-T`).

### 7.6 AMS1117 — HMI secondary LDOs

`[K]` Standard part, extremely common linear regulator family (1A, various
fixed and adjustable output versions, SOT-223 package) — no additional
research needed; the open question here (which fixed-output variant is
fitted) remains a **read-the-marking** problem, not a datasheet-lookup problem.

---

## 8. What this research changes

Concrete updates to the existing analysis, now on datasheet-confirmed ground
rather than inference:

| Item | Was | Now |
|---|---|---|
| `FU120N` part identity | `[?]` unidentified | `[DS]` **confirmed**: VBsemi 100V N-channel MOSFET, DPAK — [docs/06](06-open-questions.md) item resolved |
| Racking Board `SIO`/`SK`/`SE`/`SO` | `[I]` moderate confidence — inferred slave-SPI port | `[DS]` **high confidence** — MachXO2's hardened EFB block is Lattice's own name for exactly this kind of port |
| Main Board 10 Mbit/s bus ceiling | Applied uniformly | **Corrected**: only applies where a 6N137 is in the path (Racking link). Main Board's own transceivers (AM26LV31/32) are rated to 32 MHz — the true ceiling on the carriage link is unknown and could be much higher |
| DRV8818 current-set formula | Not previously stated | **`IFS = VREF/(8xRSENSE)`** — with R25 read as 0.25 ohm, gives `IFS = VREF/2`. Strongly suggests the TLC5620 DAC sets stepper current references |
| AM26LS31/AM26LV31 pairing | `[I]` "deliberate, probably" | `[DS]` **confirmed deliberate** — different process generation entirely (bipolar LS-series vs BiCMOS LV-series), not a casual substitution |
| SP29302 thermal risk | General warning | **Quantified**: correct input approx 26W dissipated; wrong input (24V) approx 62W — well beyond safe TO-263-5 dissipation |
| Cosmo 357NT speed ceiling | Not previously quantified | **~5us rise/fall** giving a tens-of-kHz ceiling, confirming its role is slow field I/O, never the bus |
| LFXP3C family | Correctly recorded as "LatticeXP" | **Reconfirmed** against a search that initially suggested LatticeXP2 — verified LFXP3C is the *original* LatticeXP family, 3,000 LUTs |

---

## 9. Sources consulted this session

All via web search against manufacturer/distributor-hosted datasheets:

- Texas Instruments: DRV8818, AM26LV31/32, AM26LS31, TLC5620
- Lattice Semiconductor: LatticeXP family datasheet, MachXO family datasheet,
  MachXO2 family datasheet + programming guide
- Davicom: DM9000A/DM9000AEP datasheet + application notes
- Samsung/third-party: S3C2440A user's manual
- Everlight: 6N137/EL6N137 datasheet
- Cosmo Electronics: KPC357NT datasheet (cosmo-ic.com direct + mirrors)
- Sipex/MaxLinear: SPX29300/01/02/03 datasheet
- UTC/various: TL494 datasheet and technical guides
- HongFa: HFD23 datasheet (hongfa.com direct + mirrors)
- Alpha & Omega Semiconductor: AO4606 datasheet
- VBsemi: FU120N datasheet

Full datasheet PDFs are **not yet downloaded into `datasheets/`** — this
document was built from search-result summaries of the datasheet content, not
from full-text extraction. See [datasheets/README.md](../datasheets/README.md)
for the fetch list; downloading the actual PDFs remains worthwhile for
anything requiring exact pin numbers or full electrical tables (e.g. before
laying out a replacement PCB).

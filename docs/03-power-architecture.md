# 03 — Power Architecture

Input and output power at every level: cabinet supplies, per-board inlets, and
the rails each board generates for itself.

**Evidence key** — `[P]` read directly off a photograph · `[I]` inferred ·
`[?]` unknown.

> **Project state:** the machine is dismantled and the cabinet is gone. The supply
> rack below is recorded from one photograph; units 3 and 4 were never identified.
> See [08-needed-items.md](08-needed-items.md).

---

## 1. Cabinet supplies — the input side

There is **no custom power-supply PCB** in this system. Power comes from a rack of
bought-in enclosed switching supplies. Detail and photograph:
[boards/04-power-supply/](../boards/04-power-supply/README.md).

| # | Model | DC output | AC input | Ev |
|---|---|---|---|:--:|
| 1 | Mean Well **NES-350-27** | **27 V, 13 A, 350 W** | 100–120 VAC @ 7.0 A **or** 200–240 VAC @ 4.0 A, 50/60 Hz | `[P]` |
| 2 | Mean Well **NES-350-27** | **27 V, 13 A, 350 W** | same | `[P]` |
| 3 | *unidentified* | `[?]` | AC (`L`, `N`, `⏚` visible) | `[?]` |
| 4 | *unidentified* | `[?]` | AC (`L`, `N`, `⏚` visible) | `[?]` |

**Known DC capacity: 2 × 350 W = 700 W at 27 V (26 A).**
Total system capacity is higher — units 3 and 4 are unquantified.

`[I]` Units 3 and 4 supply the **+12 V** rail, and possibly +5 V. The control
boards all reference a `+12V` rail that a 27 V supply cannot produce, and only
some of it is plausibly generated on-board.

---

## 2. The 27 V vs 24 V finding

**The boards silkscreen `+24V`. The supplies are 27 V.**

This is deliberate, not a fault. `[I]`:

* **Faster solenoid actuation.** Current rise through an inductive load is
  `di/dt = V/L`. 27 V into a nominally 24 V coil gives ~12 % faster current slew,
  which directly reduces needle-selection latency — the timing-critical operation
  in the machine.
* **Cable-drop headroom.** The carriage traverses the full bed on a flexing cable;
  starting at 27 V keeps the far end above 24 V under load.

Corroboration: the Main Board's power-section bulk electrolytics are **50 V**
rated `[P]` — correct for 27 V, generous for 24 V.

**Practical consequences**

1. Bench-power a board at **27 V**, not 24 V, to reproduce real timing.
2. Any replacement supply must be **trimmable to 27 V**.
3. A replacement driver stage must be rated for 27 V continuous, plus inductive
   flyback margin — not merely "24 V rated".

---

## 3. Rail map

All three boards silkscreen the same three rails `[P]`:

```
+24V      main power and actuator drive   (actually fed at 27 V)
+12V      analog / interface
3.3V      FPGA core and I/O
```

Grounds are split, and the split is not cosmetic:

| Net | Boards | `[I]` role |
|---|---|---|
| `GND` | all three | Primary power return |
| `SGND` | all three | Signal / analog ground |
| `DGND` | **Racking only** | Isolated field-side ground, behind the optocouplers |

The Racking Board naming **three** grounds where the others name two is consistent
with it being the most heavily isolated board — 8 × 6N137 plus 3 × Cosmo 357NT.

> **Do not bond `GND`, `SGND` and `DGND` together when bench-testing.** That
> defeats the isolation the design depends on and can put optocoupler outputs at
> the wrong reference. Measure the resistance between them first and record it —
> see [02-communication-protocol.md](02-communication-protocol.md) section 6 step 4.

---

## 4. Per-board power delivery

### 4.1 Board 02 — Main Board

| Item | Detail | Ev |
|---|---|:--:|
| **Inlet** | 4-position screw terminal, lower-right corner | `[P]` |
| **Protection** | Two cartridge fuse holders; `F2` silkscreened **2 A** | `[P]` |
| **Filtering** | Common-mode choke (toroid) + bulk electrolytics, 50 V / 105 °C | `[P]` |
| **Regulation** | DPAK switchers beside the choke, generating +12 V and 3.3 V from the incoming rail | `[P]` packages · `[I]` topology |
| **Rails out** | `+24V`, `+12V`, `3.3V`, `GND`, `SGND` | `[P]` |
| **Indicators** | `D30`+`R130` = +24 V · `D31`+`R131` = +12 V · `D1`+`R7` = 3.3 V | `[P]` |
| **Test points** | Plated pads for all five nets | `[P]` |

**Power consumers on this board, largest first:**

| Load | Draw | Ev |
|---|---|:--:|
| 6 × bipolar stepper channels | Dominant. Up to 2.5 A/phase per channel if driven to the part limit — `[I]` actual limit is set by the 0.25 Ω sense resistors and the TLC5620 DAC reference, not by the driver rating | `[I]` |
| 16 × ULN2804A Darlington outputs | ≤ 500 mA per channel, 50 V rated | `[P]` datasheet |
| ~36–38 × Cosmo 357NT input opto LEDs | ~5 mA each through a 4.7 kΩ resistor at 24 V ≈ **0.2 A total** | `[I]` |
| ~36–38 status LEDs | ~15 mA each through 1.5 kΩ ≈ **0.5 A total** | `[I]` |
| FPGA, logic, analog | Small | `[I]` |

The stepper bank is what the 700 W of 27 V capacity is for.

### 4.2 Board 03 — Racking Board

| Item | Detail | Ev |
|---|---|:--:|
| **Inlet** | 4-position screw terminal, top edge | `[P]` |
| **Protection** | Two cartridge fuse holders (ratings not legible); `D6` ultrafast axial rectifier in series with the DC input — reverse-polarity protection | `[P]` |
| **Filtering** | Common-mode choke `L1` (toroid), ferrite beads, `E2` 100 µF / 50 V | `[P]` |
| **Regulation** | Local 3.3 V generation from the incoming rail | `[P]` |
| **Rails out** | `+24V`, `+12V`, `3.3V`, `GND`, `SGND`, `DGND` | `[P]` |
| **Indicator** | `D17` + `R47` | `[P]` |

**Loads:** 8 × 6N137 (each needs LED drive current on the input side), 3 × Cosmo
357NT, the MachXO2-7000, and the RS-422 line drivers. Modest — this board is an
interface node, not a power stage.

### 4.3 Board 01 — HMI Board

| Item | Detail | Ev |
|---|---|:--:|
| **Inlet** | `J15`, 2-pin through-hole with a polarised plug | `[P]` |
| **Input voltage** | **Not silkscreened.** `[I]` 12 V or 24 V from the cabinet rack | `[?]` |
| **Protection** | Fuse `F1`, ferrite `FL1`, series diode `D3`, bulk electrolytic `CE0`; second fuse `F2` | `[P]` |
| **Main regulator** | `U20` Sipex **SP29302T5**, 3 A adjustable very-low-dropout, on a large thermal land | `[P]` |
| **Secondary** | `U21`, `U22` — AMS1117-class SOT-223 LDOs, 1 A each | `[P]` |
| **Switching controller** | UTC **TL494** PWM controller | `[P]` |
| **Reverse-polarity / load switch** | Alpha & Omega **AO4606** complementary MOSFET pair | `[P]` part · `[I]` role |
| **Backlight switch** | `U_D7` (**FU120N**, DPAK), gate driven by `Q2`/`Q3`/`Q4` (MMBT3904 class), output to `J3` → CCFL inverter | `[I]` |

> **Determine `J15` before bench-powering this board.** The machine is gone, so it
> cannot be measured in situ — trace it instead (**C-12**). The input voltage is not
> printed anywhere on it. Applying 24 V to a 12 V input would destroy the LDOs
> immediately — the SP29302 is a *linear* regulator, so every excess volt becomes
> heat in a 3 A part.

**The CCFL inverter is a distinct hazard.** Silkscreened
`HQ-01-1S015REV1.0`, marked `DANGER HIGH VOLTAGE` `[P]`. It generates several
hundred volts AC from a low-voltage input and is live whenever the HMI is
powered — independent of whether mains is connected to the cabinet.

---

## 5. Power-on sequence and `UPS-T`

The Main Board's `J2` carries a signal silkscreened **`UPS-T`** `[P]`.

`[I]` This is a **power-fail / UPS warning input** — a signal asserted while the
supply still has holdup, telling the controller to park the carriage, release
tension and write state before the rails collapse. Machines that stop mid-row
without parking can drop stitches or crash the carriage into the bed end.

**A replacement controller must implement this.** It is easy to overlook because
it is one line among many on `J2`, and its absence will not show up until the
first real power failure.

---

## 6. Summary: input and output power

**Input to the system**

| | |
|---|---|
| AC mains | 100–120 VAC or 200–240 VAC, 50/60 Hz, switch-selected at each supply `[P]` |
| AC current (known units) | 2 × 7.0 A at 115 V, or 2 × 4.0 A at 230 V `[P]` |
| Earthing | Chassis / PE terminal on every supply `[P]` |

**Output from the supply rack**

| Rail | Source | Capacity | Ev |
|---|---|---|:--:|
| **27 V DC** | 2 × NES-350-27 | 26 A / 700 W | `[P]` |
| **12 V DC** `[I]` | units 3 / 4 | unquantified | `[?]` |
| 5 V DC `[I]` | possibly unit 3 or 4 | unquantified | `[?]` |

**Delivered by each board to the machine**

| Board | Delivers |
|---|---|
| Main | 6 × bipolar stepper phases · 16 × Darlington outputs (≤ 500 mA, 50 V) · 1 × relay contact (SPDT) · 4 × analog setpoints from the TLC5620 DAC |
| Racking | 2 × servo pulse-train command lines · isolated digital outputs |
| HMI | LCD backlight power via `J3` · keypad and display supply |

---

## 7. Open items

* Models of supplies 3 and 4 — ⏸ **conditional on F-06**; the supplies were in the
  cabinet. One photograph fixes it **if** the carcass still exists.
* HMI `J15` input voltage — the machine is gone, so it can no longer be measured
  in situ. Determine it by **tracing** (**C-12**): `J15` → `D3` → `CE0` → `U20`,
  reading `CE0`'s voltage rating. **Before any powered bench work on the HMI.**
* Second Main Board fuse rating, and both Racking Board fuse ratings.
* The 4-pin screw terminal pinouts on Main and Racking. The terminal bodies
  obscure their own silkscreen in the current photos; the labels may be on the
  solder side.

See [07-photo-shot-list.md](07-photo-shot-list.md).

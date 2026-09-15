# Board 04 — Power Supply Rack

> There is **no custom power-supply PCB** in this system. Power is a rack of
> bought-in enclosed switching supplies in the cabinet, and each control board
> then derives its own low-voltage rails locally.

**Evidence key** — `[P]` read directly off a photograph · `[I]` inferred ·
`[?]` unknown.

Photo: [Power_Supply Connections.jpeg](images/overview/Power_Supply%20Connections.jpeg)

---

## 1. What is actually installed

The photograph shows **four enclosed supplies** side by side on a DIN/chassis
mount, wired to a common AC distribution block.

| # | Model | Output | Input | Evidence |
|---|---|---|---|---|
| 1 | Mean Well **NES-350-27** | **27 V DC, 13 A, 350 W** | 100–120 VAC / 7.0 A or 200–240 VAC / 4.0 A, 50/60 Hz | `[P]` label fully legible |
| 2 | Mean Well **NES-350-27** | **27 V DC, 13 A, 350 W** | same | `[P]` label fully legible |
| 3 | *unidentified* enclosed SMPS | `[?]` | AC — only `L`, `N`, `⏚` visible | `[P]` presence · `[?]` model |
| 4 | *unidentified* enclosed SMPS | `[?]` | AC — only `L`, `N`, `⏚` visible | `[P]` presence · `[?]` model |

Units 3 and 4 are physically smaller than the NES-350 units and their model
labels face away from the camera. `[I]` these are the **+12 V** and possibly a
+5 V supply — the control boards need a 12 V rail that the 27 V units cannot
provide. **This needs one photograph to resolve** — see
[docs/07-photo-shot-list.md](../../docs/07-photo-shot-list.md).

### NES-350-27 terminal block, as labelled `[P]`

```
L        AC live
N        AC neutral
⏚        chassis / protective earth
-V  -V  -V      DC return
+V  +V  +V      DC output
+V ADJ          output trim potentiometer
```

---

## 2. The 27 V finding — read this before you change anything

**The control boards silkscreen their main rail `+24V`. The supplies feeding it
are set to 27 V.** That is not a fault, and it is not a mislabel to "fix".

`[I]` Two reasons this is done deliberately in knitting-machine controllers:

1. **Faster solenoid actuation.** Current rise through a solenoid is
   `di/dt = V/L`. Driving a nominally 24 V selector coil at 27 V raises the
   current slew by ~12 %, which directly reduces actuation latency — and needle
   selection is the timing-critical operation in the whole machine.
2. **Cable-drop headroom.** The carriage traverses the full bed on a flexing
   cable. Several volts of drop under load at the far end of travel is normal;
   starting at 27 V keeps the far end above 24 V.

Corroborating evidence: the Main Board's bulk electrolytics in the power section
are **50 V** rated `[P]`, which is the correct part choice for a 27 V rail and
generous for 24 V.

**Consequence:** any bench supply you use to power a board on the desk should be
set to **27 V**, not 24 V, if you want to reproduce real timing behaviour.

---

## 3. About `LRS-350-24__Power-supply.txt`

That file — kept in [reference/](reference/) — describes a Mean Well
**LRS-350-24** (24 V, 14.6 A, 350 W). It is a **candidate replacement**, not the
installed part. It came from a retail product listing, so treat its wording as
marketing copy rather than a datasheet.

If you substitute it:

| Check | Why |
|---|---|
| **Trim range must reach 27 V** | The installed rail is 27 V (section 2). An LRS-350-24 must be trimmed up. Confirm the output-adjustment range in Mean Well's own datasheet for your revision **before ordering**. |
| **Power, not current, is the limit** | At 27 V a 350 W unit delivers ~13 A, not 14.6 A. That matches one NES-350-27 — so it is a 1:1 swap on capacity, with no headroom gained. |
| **Mechanical fit** | LRS-350 is a 30 mm low-profile case; NES-350 is taller. Mounting holes differ. |
| **Fan** | NES-350 and LRS-350 differ in cooling. Check airflow clearance in the cabinet. |

---

## 4. Per-board power entry summary

Each control board has its own local power section. Details are in each board's
own README; the system view is in
[docs/03-power-architecture.md](../../docs/03-power-architecture.md).

| Board | Inlet | Fusing | Rails generated locally |
|---|---|---|---|
| [01 HMI](../01-hmi-board/README.md) | `J15`, 2-pin | `F1`, `F2` | SP29302T5 (3 A LDO) + 2 × AMS1117 |
| [02 Main](../02-main-board/README.md) | 4-pin screw terminal | `F2` = 2 A, plus one more | +12 V, 3.3 V from +24 V |
| [03 Racking](../03-racking-board/README.md) | 4-pin screw terminal | two cartridge | 3.3 V from +24 V |

---

## 5. Safety

The AC side of this rack is live at mains potential and the cabinet wiring in the
photograph is aged, dusty and partly unsleeved. Before any probing:

* Isolate at the main disconnect and **verify zero volts** at the terminal block
  with your multimeter, on every conductor, before touching anything.
* Bulk capacitors inside enclosed supplies hold charge after disconnection. Wait,
  then verify.
* The HMI board carries a **CCFL backlight inverter** marked
  `DANGER HIGH VOLTAGE` — several hundred volts AC at its output, from a low-voltage
  input. It is live whenever the HMI is powered, regardless of the mains state.

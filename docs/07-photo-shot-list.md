# 07 — Image Request List

Every photograph this project needs, what each one unlocks, and where the file
goes. This is the definitive list — [09-field-checklist.md](09-field-checklist.md)
is the walk-around version for a site visit.

**Split into two parts:**

* **Part A — boards in hand.** Can be shot today. No machine, no factory visit.
* **Part B — factory visit.** Needs the carcass, a sister machine, or a donor board.

Tracked as section B of [08-needed-items.md](08-needed-items.md).

---

## How to shoot

The existing 49 photographs are good. Every failure in them is one of two things,
both avoidable:

| Problem | Where it cost us | Fix |
|---|---|---|
| **Glare hides the laser marking** | All six stepper drivers — package clearly visible, marking unreadable | Light from an **angle**, never head-on. **Flash off.** Tilt the board until the marking catches a raking light |
| **Silkscreen runs off the frame** | Racking board year code; screw-terminal pin labels | Include **~20 mm of board beyond** whatever you are shooting |

Three more worth adopting:

* **Include the reference designator in frame** with every chip shot. Half the
  value of a part number is knowing which site it came from. The existing
  filenames record markings but not designators.
* **Shoot the solder side too** wherever a connector body hides its own silkscreen.
* **Shoot square-on** for connectors — at an angle, pin order cannot be counted.

**Naming:** `<board>_<designator>_<what>.jpg`, e.g. `main_U6_stepper-driver.jpg`.
Designator-first beats marking-first; we already have filenames we cannot map to
sites.

---

## PART A — Boards in hand

**Nothing here needs the factory.** This is the largest block of immediately
available work, and every item unlocks specific analysis.

### A1 — Highest value

| ID | Shot | What it unlocks |
|---|---|---|
| **IMG-01** | **HMI boot flash — lift the mezzanine off and shoot its UNDERSIDE.** The top side carries only `U6` (SRAM), `U3` (SDRAM) and `U1` (CPU), so the flash is underneath. Also shoot the empty board-to-board sockets | Firmware storage type and size. Currently the biggest **recoverable** unknown. Decides whether the firmware can be dumped in-circuit or needs desoldering |
| **IMG-02** | **Switch and jumper positions, AS FOUND** — Racking `S1` (4-way DIP), Main `P1` (2-way jumper), HMI `SW1` (4-way DIP). Close enough to read each position. **Before anyone moves them** | `S1` was the node address; `P1` selected one of four machine modes. With the harness gone these are among the only surviving configuration evidence |
| **IMG-03** | **Main board jumper `P1`** and its four-row printed truth table, close and square-on | The Chinese annotation column — 3 characters on rows 1–3, 2 on row 4. The English rows are already read. Gives us four machine modes a replacement must reproduce |
| **IMG-04** | **Six stepper driver ICs**, one macro each, **oblique light**, designator in frame | Confirms DRV8818 vs DRV8811 vs A3977 vs something else. These differ in current limit, microstep resolution and decay mode — all of which a replacement must match |
| **IMG-05** | **Main board 40-pin top IDC + all left-edge headers**, square-on, with any silkscreen | Which connector carried the **carriage / selector link**. With the harness gone this is now an open question in its own right |

### A2 — Unidentified parts

| ID | Shot | What it unlocks |
|---|---|---|
| **IMG-06** | Main board: `U3`, `U5`, `U7`, `U17`, `U18`, `U19` — macro each, designator in frame | Six unknown parts |
| **IMG-07** | Main board: `U_Z1`, `U_K2`, `U21`, `U22`, `Q_Z1`, `Z2` | Five more unknowns. **The through-hole DIPs especially** — in boards of this era they usually turn out to be legacy driver or memory parts worth identifying |
| **IMG-08** | Racking board: `U11` (TSSOP-16, beside the `RST` net), `U13` | Two unknown parts |
| **IMG-09** | HMI: `U_D7` (`FU120N`, DPAK) — closer than the existing shot | The part number is still unread. We know its **function** (backlight switch) but not what it is |
| **IMG-10** | HMI: `U20` (SP29302T5) and `U21`/`U22` (SOT-223 LDOs), markings legible | Confirms the regulator chain and the AMS1117 output variants — needed for IMG-13 |

### A3 — Connectors and pinouts

Needed before any continuity mapping produces a usable table.

| ID | Shot | What it unlocks |
|---|---|---|
| **IMG-11** | **Every connector on every board**, square-on, with its **pin-1 mark** (triangle, square pad, or "1") visible | Pin numbering. Everything in the replacement design depends on it. Roughly 20 shots |
| **IMG-12** | **Solder side underneath both 4-pin screw terminals** (Main, Racking) | DC inlet pinouts — the terminal bodies hide their own silkscreen on the component side |
| **IMG-13** | HMI `J15` power inlet and the trace path `J15` → `D3` → `CE0` → `U20`, including **`CE0`'s printed voltage rating** | The HMI input voltage, which is silkscreened nowhere. Its regulator is linear — over-volting destroys it instantly. This replaces a measurement we can no longer take |
| **IMG-14** | **JTAG headers** — HMI `JTAG`, Main `J1AG2`, Racking `CON1` — with surrounding silkscreen and pin-1 marks | Programmer wiring. Backwards wiring is the most common failure in the readback attempt |
| **IMG-15** | **Every fuse body**, all three boards, rating legible | Fuse ratings. Only Main `F2` = 2 A is currently known |

### A4 — Confirmation shots

Lower priority, but each one firms up something currently marked `[I]`.

| ID | Shot | What it unlocks |
|---|---|---|
| **IMG-16** | HMI: the two rows of plated **test points** below the RS-422 transceivers (`MK1` `MO1` `MS1` `MIK1` / `MS2` `MO2` `MK2` `SO1` `SO2` `SK1` `SK2`), sharp and square-on | These are the intended bus probe points and the basis of the whole protocol decode. A sharper shot confirms labels read off a downscaled crop |
| **IMG-17** | Main board: the area around `U28` (FPGA) including the `MCS` / `MDO` config silkscreen and the crystal | Config pin identification for the readback attempt |
| **IMG-18** | Racking board: the FPGA edge silkscreen `SIO` `SK` `SE` `SO` / `MIK` `MK` `M0` `MS`, sharp | Confirms the slave-SPI config port vs machine bus split |
| **IMG-19** | **HMI mezzanine removed** — both sides of the mezzanine, and the carrier board's empty board-to-board sockets | Any chips hidden under the mezzanine; the CPU-to-carrier interface |
| **IMG-20** | All three boards: **any silkscreened revision, date or board ID** not already captured, including board edges | Revision tracking. The Racking board is a year newer than the Main board and we do not know why |
| **IMG-21** | All three boards, **both sides, full board, square-on, sharp** — a clean re-shoot of the overviews now that they are on a bench | The existing overviews are hand-held. Flat, square, well-lit versions make every later crop more readable |

---

## PART B — Factory visit

Needs the carcass, a sister machine, or a donor board. Cross-referenced to
[09-field-checklist.md](09-field-checklist.md).

### B1 — The one that changes the project

| ID | Shot | What it unlocks |
|---|---|---|
| **IMG-30** | ⭐ **The selector board** — from the dead machine's head, a sister machine, or a donor. Both sides, **every** chip with designator, every connector, board ID, date code | **The needle-selection protocol.** This is the other endpoint of a link we can currently only see one half of. Nothing else in this list comes close in value |
| **IMG-31** | The **head-to-cabinet cable**, both ends, and how it routes | The physical carriage link |

### B2 — Recovering what was lost

A sister machine's cabinet gives us, by proxy, the interconnection record that was
lost when the boards were pulled.

| ID | Shot | What it unlocks |
|---|---|---|
| **IMG-32** | ⭐ **A wired cabinet, doors open, every connector seated**, before anything is touched | Board-to-board topology, which we can currently only infer |
| **IMG-33** | **Every cable, both ends**, and what each connects to | The harness map |
| **IMG-34** | Which **supply feeds which board**, on which terminal pin | Rail assignment — currently unknown per board |
| **IMG-35** | Which **connector on which board** goes to the machine head | Settles whether the HMI or the Main board drove the selector |
| **IMG-36** | DIN rail terminal blocks and their numbering | Field wiring reference |

### B3 — Identification

| ID | Shot | What it unlocks |
|---|---|---|
| **IMG-37** | **PSU units 3 and 4 nameplates** (the two smaller enclosed supplies), plus their wired DC output terminals | The +12 V rail source and the total power budget. Currently unquantified |
| **IMG-38** | **Servo amplifier nameplates**, and their connector wiring at the amplifier end | Confirms or refutes the Mitsubishi MR-J inference — the basis for the one part of this system that is a documented standard |
| **IMG-39** | **Machine nameplate** — make, model, serial, year, voltage, gauge | Identified as **Shaoxing Jinhao GD-H122S** per the owner, but that rests entirely on the report, not a photograph. Confirming it against the actual nameplate matters for sourcing a donor board from the correct manufacturer |
| **IMG-40** | AC voltage-selector switch position on each Mean Well supply | Mains configuration |

### B4 — Context

| ID | Shot | What it unlocks |
|---|---|---|
| **IMG-41** | **Every page of any documentation found** — manuals, wiring diagrams, parts lists. Chinese is fine, it can be translated. **Do not skim-select; shoot every page** | Potentially everything. One wiring diagram would close more open questions than the rest of this list combined |
| **IMG-42** | A running sister machine: **short video** of a knitting cycle — carriage traverse, racking, yarn carrier change | Machine behaviour and timing context |
| **IMG-43** | A running machine's **HMI screen** — main menu, settings, diagnostics, any version string | Firmware version, menu structure, what the operator actually controls |
| **IMG-44** | The **needle bed and its selector actuators**, close up | Actuator type and geometry — needed if needle selection has to be designed from scratch |
| **IMG-45** | Spares cupboard contents — any of our board types, any selector board | What is available without buying |

---

## Where the files go

```text
boards/01-hmi-board/images/ics/          IMG-01, 09, 10, 19
boards/01-hmi-board/images/overview/     IMG-02, 11, 13, 14, 16, 20, 21
boards/02-main-board/images/ics/         IMG-03, 04, 06, 07, 17
boards/02-main-board/images/overview/    IMG-02, 05, 11, 12, 14, 15, 20, 21
boards/03-racking-board/images/ics/      IMG-08, 18
boards/03-racking-board/images/overview/ IMG-02, 11, 12, 14, 15, 20, 21
boards/04-power-supply/images/overview/  IMG-37, 40
boards/05-carriage-board/images/         IMG-30, 31   (create if a board arrives)
reference/cabinet/                       IMG-32 … IMG-36
reference/servo-amplifiers/              IMG-38
reference/                               IMG-39, 41, 42, 43, 44, 45
```

Folders marked *create* do not exist yet — make them when the images do.

---

## If you can only shoot a few

| Rank | Image | Why |
|---|---|---|
| 1 | **IMG-30** — selector board | The only route to the needle-selection protocol |
| 2 | **IMG-32/33** — a wired cabinet | Recovers the interconnection record by proxy |
| 3 | **IMG-02** — switch positions as found | Trivially easy to destroy by accident, impossible to recover |
| 4 | **IMG-41** — documentation pages | One wiring diagram beats months of continuity work |
| 5 | **IMG-01** — HMI boot flash | One photo; unlocks the firmware question |

Everything in **Part A** can be shot today with the boards you already have. If
the factory visit produces nothing, Part A alone still moves the project forward.

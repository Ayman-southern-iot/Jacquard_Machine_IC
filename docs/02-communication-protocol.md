# 02 — Communication Protocol

**Goal context:** this document is written for the stated objective of building a
**drop-in replacement controller**. That means the protocol has to be understood
well enough to *speak* it, not merely to observe it.

**Evidence key** — `[P]` read directly off a photograph · `[I]` inferred from
component choice + topology · `[?]` unknown.

---

## 1. The short answer

The machine bus is **not a standard fieldbus.** It is a custom, vendor-private
**clocked (synchronous) serial link**, carried as **RS-422 differential pairs**,
**galvanically isolated** with 6N137 optocouplers, and implemented **entirely in
FPGA fabric** with no microcontroller anywhere in the loop.

The closest familiar description is **"isolated SPI over RS-422"** — a
master-driven clock, a separate data-out and data-in line, and a framing strobe.

What it is **not**, and the evidence for each:

| Not this | Why we can rule it out |
|---|---|
| **Modbus RTU / RS-485** | No RS-485 transceiver anywhere. RS-485 is half-duplex on one pair; every board here has a **separate driver and receiver** part (AM26LV31 + AM26LV32), which is full-duplex RS-422 `[P]` |
| **CAN / CANopen / DeviceNet** | No CAN transceiver and no CAN controller on any board `[P]` |
| **Profibus / Interbus** | No ASIC, no dedicated protocol controller `[P]` |
| **Plain UART / RS-232** | The only RS-232 transceiver (MAX3232) sits by itself against **three** RS-422 drivers. And every silkscreened signal group has a **third wire ending in `K`** — a UART link has no third wire `[P]` |
| **Ethernet / EtherCAT** | The DM9000AEP is on the HMI board only, wired to the RJ45. Neither satellite board has any Ethernet part `[P]` |

This matters enormously for planning. You cannot buy an adapter for this bus.
There is no off-the-shelf decoder. A replacement controller has to reimplement
the link from first principles — which is tractable, but it is an FPGA/timing job,
not a software job.

---

## 2. The evidence

### 2.1 Physical layer parts, on every board

| Board | Driver | Receiver | Isolation |
|---|---|---|---|
| HMI | AM26LV31C ×3 (3.3 V) | AM26LV32C ×1 | 6N137 ×2 |
| Main | AM26LV31C (3.3 V) | AM26LV32C | — (bus side); Cosmo 357NT on field I/O |
| Racking | **AM26LS31C (5 V)** | AM26LV32C (3.3 V) | **6N137 ×8** |

All `[P]`, read from macro shots and confirmed against in-situ designators.

Three observations that constrain the design:

1. **Full duplex.** Separate driver and receiver silicon on every node. Data
   flows both directions simultaneously; there is no turnaround delay to budget.
2. **The Racking Board drives at 5 V and receives at 3.3 V.** `[I]` deliberate —
   more amplitude onto the long cable run, FPGA-level signalling coming back.
   A replacement must match this or lose noise margin at the far end.
3. **The 6N137 sets the ceiling.** It is rated 10 Mbit/s. Every bus signal on the
   Racking Board passes through one. **The bus clock cannot exceed 10 MHz**, and
   realistically runs well below it — `[I]` 1–5 MHz is the plausible band.

### 2.2 The signal naming convention

This is the most informative evidence in the project, because the naming is
systematic across three boards designed years apart.

**HMI board, silkscreened signal groups** `[P]`:

```
MSI / MSO / MSK        SDI / SDO / SDK        MDI / MDO / MDK
HSI / HSO / HSK        MKI / MKO / MKK
```

**HMI board, plated test points below the RS-422 transceivers** `[P]`:

```
row 1:   MK1   MO1   MS1   MIK1   MIK2
row 2:   MS2   MO2   MK2   SO1    SO2    SK1   SK2
```

**Racking board, silkscreened along the FPGA edge** `[P]`:

```
SIO   SK   SE   SO           MIK   MK   M0   MS
```

### 2.3 Decoding the convention

Every group is `<2-letter channel prefix>` + `<1-letter signal role>`:

| Suffix | Meaning | Confidence |
|---|---|---|
| **`K`** | **Clock** (*Klock*) | **High.** Every single group has one. A UART group never does. This is the load-bearing deduction |
| **`I`** | Data **in** to the node | High |
| **`O`** | Data **out** of the node | High |
| **`S`** / **`E`** | **Strobe / enable** — frame delimiter or chip-select | Moderate `[I]` |
| trailing `1` / `2` | Independent **channel number** | High — the test points come in matched 1/2 pairs |

So a channel is a **3- or 4-wire synchronous group**: clock, data-out, data-in,
and (where present) a strobe. That is SPI in all but name.

The `SIO / SK / SE / SO` group on the Racking Board is a special case: `[I]` it is
the **MachXO2 slave-SPI sysCONFIG port** (`SI`, `SCK`, `SN` chip-select, `SO`),
i.e. the FPGA programming interface, not the machine bus. The naming convention is
simply reused.

### 2.4 Channel count

The HMI test points come in numbered pairs (`…1` and `…2`), and three AM26LV31C
drivers are fitted against one AM26LV32C receiver. A quad driver gives four
differential outputs; a quad receiver gives four differential inputs.

`[I]` **Two independent outbound machine-bus channels from the HMI**, each using
3 of the 4 driver pairs, with the single quad receiver collecting the return data
from both. This matches a **two-branch star**, not a single daisy chain:

* one branch toward the Main Board,
* one branch toward the satellite nodes (Racking, and any siblings).

---

## 3. The three separate protocol domains

A common early mistake is to treat "the protocol" as one thing. There are three,
with very different difficulty:

| # | Domain | Standard? | Difficulty |
|---|---|---|---|
| 1 | **Machine bus** — HMI ↔ Main ↔ Racking | **No.** Vendor-private synchronous serial | **Hard.** Needs capture + FPGA reimplementation |
| 2 | **Servo interface** — Racking ↔ AC servo amplifiers | **Yes.** Mitsubishi MR-J-style encoder + status lines | **Easy.** Publicly documented |
| 3 | **Host interfaces** — Ethernet, USB, RS-232 on the HMI | **Yes.** Standard | **Easy** to probe, but the *application* layer above them is unknown |

### 3.1 Domain 2 is your shortcut

The Racking Board's 34-pin connector is silkscreened `[P]`:

```
MLA  MLAR  MLB  MLBR  MPG  MINP  MRDY          <- axis "M"
BLA  BLAR  BLB  BLBR  BPG  BINP  BRDY          <- axis "B"
```

These map onto the **Mitsubishi MR-J series AC servo amplifier** signal set `[I]`:

| Silkscreen | MR-J name | Direction | Meaning |
|---|---|---|---|
| `xLA` / `xLAR` | `LA` / `LAR` | amp → board | Encoder A-phase and its inverse (RS-422 pair) |
| `xLB` / `xLBR` | `LB` / `LBR` | amp → board | Encoder B-phase and its inverse |
| `xPG` | `PG` | board → amp | Pulse-train command |
| `xINP` | `INP` | amp → board | **In-position** reached |
| `xRDY` | `RD` | amp → board | Amplifier **ready** |

This is a documented, published interface. **Two AC servo axes**, prefix `M` and
prefix `B`. You can design and test this half of a replacement controller today,
against amplifier documentation, without decoding anything.

That is worth emphasising: roughly half the motion interface of this system is
already a known quantity.

---

## 4. What is still unknown about the machine bus

Be clear-eyed about the gap. The following are **not** determined by the photos
and cannot be inferred from them:

| Unknown | Why it matters |
|---|---|
| **Clock frequency** | Sets your FPGA design constraints |
| **Clock polarity and phase** | Which edge samples data (SPI mode 0/1/2/3 equivalent) |
| **Bit order** | MSB-first or LSB-first |
| **Frame length** | Almost certainly fixed, `[I]` because a shift-register-in-fabric design has no reason to vary it |
| **Frame structure** | Field layout, addressing, whether there is a CRC or parity |
| **Frame rate** | Whether it is continuous, or bursts synchronised to carriage position |
| **Node addressing** | The Racking Board's 4-way DIP `S1` `[P]` implies addressing — up to 16 nodes, or a mix of address and mode bits `[I]` |
| **Which DB9 is which** | One RS-232, `[I]` two RS-422 — but not yet confirmed by continuity |
| **Pin numbering on every connector** | No pin-1 marks have been read |

None of this is exotic. All of it falls out of **one good logic-analyser capture**
plus a continuity map. It simply cannot be read off photographs.

---

## 5. Reverse-engineering attack surface, ranked

Ranked by value-per-effort for the drop-in-replacement goal.

### Rank 1 — Lattice FPGA readback via JTAG

**Why it is first:** if the security bit is not set, you recover the *entire*
design from all three boards, and the protocol question dissolves.

All three FPGAs are **non-volatile** parts with on-chip configuration memory:

| Board | Device | Header |
|---|---|---|
| HMI | MachXO LCMXO1200C | `JTAG` header `[P]` |
| Main | LatticeXP LFXP3C | `J1AG2` `[P]` |
| Racking | MachXO2-7000HC | `CON1`, 10-pin `[I]` |

Procedure: Lattice Diamond Programmer plus an HW-USBN-2B clone (~US$20–30).
Scan the chain, read the IDCODE first — that alone confirms the part and proves
your wiring. Then attempt bitstream readback.

**Realistic expectation:** many commercial designs set the security bit, and
readback then returns nothing. Even so, this costs one evening and one cheap
cable, and the IDCODE scan is worth it regardless as a board-health check.

*Note:* these are your own boards. Reading back your own hardware to keep a
machine you own running is ordinary repair work.

### Rank 2 — Continuity mapping (you can start this today)

**No instruments beyond a multimeter.** See section 6 — this is the largest block
of useful work currently available to you, and every later step depends on it.

### Rank 3 — HMI boot flash dump

The mezzanine's third memory package holds the firmware. It has **never been
photographed** — the first step is simply to photograph its marking.

Once identified: NOR flash can be read in-circuit with a clip; NAND generally
needs desoldering. The S3C2440 also has a well-known serial download mode, and
the board has a `JTAG` header.

The firmware will contain the pattern-file handling and the HMI's half of the bus
driver. `[I]` Given the era and the chip, expect embedded Linux or Windows CE.

### Rank 4 — Live bus capture

The definitive answer, but it needs a logic analyser you do not yet have.
See section 7.

### Rank 5 — Host interfaces

Ethernet (DM9000AEP → RJ45) and the RS-232 console. `[I]` The console is likely a
boot log and possibly a shell — cheap to check once you can identify which DB9 it
is (section 6).

---

## 6. What you can do right now with a multimeter

This is a genuinely productive work package, not a consolation prize. Work
through it in order. **Power off and verified dead for steps 1–4.**

### Step 1 — Identify the RS-232 DB9

With the HMI **powered**, measure each DB9 pin against the board's `GND` test point:

* **RS-232 transmit** idles at a **negative** voltage, typically −5 V to −12 V.
  That is unmistakable and no other signal on these boards looks like it.
* **RS-422** pairs sit between 0 V and +5 V with a small differential between the
  two wires of a pair.

One measurement per DB9 tells you which connector is the console. Record it.

### Step 2 — Find the differential pairs

In continuity/resistance mode, power off:

* Probe between candidate pin pairs on the DB9s and the `COM1`/`COM2` headers.
* A **~100–120 Ω** reading across two pins is an **RS-422 termination resistor** —
  that pair is a differential signal, and that end is a bus terminator.
* Note which pairs are terminated and which are not. Terminated ends are the
  physical ends of the bus; unterminated ones are mid-chain.

### Step 3 — Map transceiver pins to connector pins

This is the highest-value task in the whole document.

For each AM26LV31C / AM26LV32C / AM26LS31C:

1. Pin 1 is marked by the package dimple; count anticlockwise from it.
2. Buzz out each of the four differential outputs (or inputs) to the connector.
3. Record it as a table: `IC ref → IC pin → connector → connector pin`.

Do the same for the 6N137 optocouplers on the Racking Board, both sides.

When this is done you will know **exactly which physical wire carries the clock**,
because you can follow it back to the pin the FPGA drives. That converts the
protocol problem from "unknown link" to "known wires, unknown framing" — a much
smaller problem.

### Step 4 — Verify rail integrity

At each board's test points, in resistance mode, power off:

* `+24V` to `GND`, `+12V` to `GND`, `3.3V` to `GND` — expect tens of ohms to
  kilohms, **never near 0 Ω**. A short indicates a failed regulator or a shorted
  decoupling capacitor.
* `GND` to `SGND` — these are separate nets. `[I]` Expect **high resistance or a
  single bonding point**, not a dead short. Record what you find, because it
  tells you where the isolation barrier is.

### Step 5 — Power on and read the LEDs

The Main Board gives you a free logic analyser for its input bank: every `J1`
input has its own status LED `[P]`. Actuate machine sensors by hand and watch
which LED follows. That maps signal names to physical sensors with no
instruments at all.

Rail LEDs: `D30` = +24 V, `D31` = +12 V, `D1` = 3.3 V `[P]`.

---

## 7. When you buy test gear

You will eventually need a logic analyser. Two constraints govern the choice:

**Constraint 1 — you cannot probe the differential lines directly.** A logic
analyser expects single-ended logic. You must tap at either:

* the **HMI test points** (section 2.2) — already single-ended, plated,
  accessible; **this is the intended probe point**, or
* the **logic-side pins of the AM26LV32C receivers**, or
* the **output side of a 6N137**.

Do not clip onto an RS-422 pair and expect a usable trace.

**Constraint 2 — sample rate.** With a `[I]` 1–5 MHz bus clock, the common
24 MS/s USB analysers are marginal: you want roughly 10× the clock to recover
setup/hold relationships cleanly. Target **≥ 100 MS/s** and at least 8 channels.
A DSLogic-class instrument is the usual sensible choice at this budget.

**Minimum viable kit**, in buying order:

| Item | Approx. | Why |
|---|---|---|
| Lattice HW-USBN-2B clone | US$20–30 | Rank-1 attack: FPGA IDCODE + readback attempt |
| 8-ch logic analyser, ≥100 MS/s | US$70–150 | The only way to answer section 4 |
| USB-TTL serial adapter (3.3 V) | US$5 | HMI console, once the DB9 is identified |
| Fine-tip probes / test hooks | US$15 | Nothing here is through-hole-friendly |

> ⚠ **The machine is dead and dismantled.** There is no running machine to
> capture. This procedure only applies if **C-13** shows the bus master still
> transmits on a bench with no peers attached — see
> [05](05-replacement-controller-plan.md) §10.2. Establish that before buying a
> logic analyser.

**Channel map for the first capture**, once you have the analyser — tap the HMI
test points:

| Ch | Test point | Expect |
|---|---|---|
| 0 | `MK1` | Clock, channel 1 — look for a regular burst |
| 1 | `MO1` | Data out, channel 1 |
| 2 | `MS1` | Strobe / enable, channel 1 — frames the burst |
| 3 | `MIK1` | Return path / second clock, channel 1 |
| 4 | `MK2` | Clock, channel 2 |
| 5 | `MO2` | Data out, channel 2 |
| 6 | `SO1` | Return data, channel 1 |
| 7 | `SK1` | Return clock, channel 1 |

Trigger on the falling edge of `MS1`. On a bench rig the only available states are
power-up and idle; the original plan — capture idle, then a single needle-selection
cycle, and diff them — **needs a running machine and is no longer possible**. That
diff was where the pattern data would have shown itself.

---

## 8. How to falsify this model

Good reverse engineering states what would prove it wrong. This model predicts:

1. `MK1` carries a **regular clock** — a burst of evenly spaced edges.
   *If instead it shows asynchronous, data-like transitions with no fixed period,
   the "K = clock" deduction is wrong and the link is some async scheme.*
2. `MO1` transitions are **aligned to `MK1` edges**.
   *If they are not, the two lines are unrelated and the grouping is wrong.*
3. `MS1` frames a **fixed-length** burst on `MK1`.
   *A varying burst length would mean a variable-length, probably byte-oriented
   protocol — significantly harder, and it would change the replacement design.*
4. The bus clock is **below 10 MHz**, because every Racking Board bus signal goes
   through a 6N137.
   *A faster clock would mean those optocouplers are not in the bus path at all,
   and the isolation topology is different from what is assumed here.*

One capture tests all four at once.

---

## 9. Summary for the replacement design

| Question | Answer | Confidence |
|---|---|---|
| Is it a standard fieldbus? | **No** | High `[P]` |
| Physical layer | RS-422 differential, full duplex | High `[P]` |
| Isolation | 6N137, 10 Mbit/s ceiling | High `[P]` |
| Signalling | Synchronous, master-supplied clock | High `[I]` |
| Wires per channel | 3–4 (clock, out, in, strobe) | High `[I]` |
| Channels from HMI | 2 | Moderate `[I]` |
| Topology | Two-branch star, with daisy-chain at the nodes | Moderate `[I]` |
| Node addressing | DIP switch, ≤ 16 nodes | Moderate `[I]` |
| Clock rate, framing, bit order | **Unknown — needs capture** | — |
| Servo interface | Mitsubishi MR-J style, documented | Moderate–high `[I]` |

Next: [05-replacement-controller-plan.md](05-replacement-controller-plan.md).

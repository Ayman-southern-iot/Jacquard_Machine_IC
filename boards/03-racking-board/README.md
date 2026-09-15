# Board 03 — Racking Board (`SWG-Yarn-A-02`)

> Racking / yarn-carrier satellite node.
> Silkscreen: `SWG-Yarn-A-02  2013` `[P]` — the final digit sits at the board edge
> and is partly clipped in the photo, but reads as `3`.
>
> `SWG` and `Yarn` point at the **racking axis** (the mechanism that shifts the
> needle bed sideways) and the **yarn-carrier** feeders.

**Evidence key** — `[P]` read directly off a photograph · `[I]` inferred from
datasheet role + layout · `[?]` unknown.

Overviews: [Front.jpg](images/overview/Front.jpg) · [Back.jpg](images/overview/Back.jpg)

---

## 1. What this board is

A small, roughly square, **heavily isolated** satellite node. Eight 6N137
optocouplers in two columns of four dominate the left half of the board.

Its job, read off the connector silkscreen, is to be the **interface between the
machine bus and two AC servo amplifiers** — plus local isolated I/O.

This is the most self-contained board in the system and therefore the easiest one
to understand, emulate, or replace first.

---

## 2. IC list

| Ref | Part | Package | Function | Image |
|---|---|---|---|---|
| — | Lattice **LCMXO2-7000HC-4TG144C** | TQFP-144 | MachXO2-7000 **non-volatile** FPGA, ~6.8 K LUT, speed grade 4. Lot `A6151R54`. The node's entire control logic — there is no CPU. Marking also transcribed in [IC Number.txt](images/ics/IC%20Number.txt) `[P]` | [LCMXO2-7000HC](images/ics/Lattice%20LCMX02-7000HC-4TG144C-A6151R54.jpg) · [(2)](images/ics/Lattice%20LCMX02-7000HC-4TG144C-A6151R54%20%282%29.jpg) |
| `U4` `U5` `U6` `U7` `U12` `U15` `U16` `U17` | Everlight **EL6N137** | SOIC-8 | 10 Mbit/s logic-gate optocoupler. **Eight fitted**, two columns of four — full galvanic isolation of the machine bus and the servo interface `[P]` | [EL6N137](images/ics/EL-6N137-734.jpg) |
| `U19` `U20` `U21` | Cosmo **357NT** | SOP-4 | Phototransistor optocoupler — isolated slow I/O `[P]` | [Cosmo 357NT](images/ics/Cosmo-357NT-A38-C.jpg) |
| — | TI **AM26LS31C** | SOIC-16 | Quad **5 V** RS-422 differential line driver `[P]` | [AM26LS31C](images/ics/0AEL8EK-G4-AM26LS31C.jpg) |
| `U8` | TI **AM26LV32C** | SOIC-16 | Quad 3.3 V RS-422 differential line receiver. *The filename reads `AM26LV326`; the device is clearly marked **AM26LV32C**.* `[P]` | [AM26LV32C](images/ics/77AD30M-AM26LV326%20G4.jpg) |
| `U9` | TI **SN74HC245** | SOIC-20 | Octal 3-state bus transceiver `[P]` | [HC245](images/ics/HC245-65k-G4-A615.jpg) |
| `U3` `U18` | TI **SN74LVC07A** | SOIC-14 | Hex buffer, open-drain outputs — 3.3 V to 5 V level shifting `[P]` | [LVC07A](images/ics/09D7X8K-G4-LVC07A.jpg) |
| `U11` | — | TSSOP-16 | Small logic device beside the `RST` net. No macro shot `[?]` |
| `U13` | — | SOIC | No macro shot `[?]` |
| `D6` | **UF5408** class | DO-201 axial | Ultrafast rectifier in the DC input path — reverse-polarity / freewheel protection `[P]` package + partial marking · `[I]` exact type |

### Note on the driver mix

This board uses the **5 V** `AM26LS31C` to drive, but the **3.3 V** `AM26LV32C` to
receive. That asymmetry is deliberate: it puts more drive amplitude on the long
cable run back toward the Main Board while keeping the receiver at FPGA level.
Any replacement must match it, or the bus will not meet RS-422 margins over the
full cable length.

---

## 3. Connectors

### 3.1 The 34-pin IDC on the left edge — **the servo interface**

Read directly from silkscreen, in physical order `[P]`:

```
MLA   MLAR   MLB   MLBR   MPG   MINP   MRDY        <- axis "M"
BLA   BLAR   BLB   BLBR   BPG   BINP   BRDY        <- axis "B"
```

This naming is not generic. It matches the **Mitsubishi MR-J series AC servo
amplifier** signal set almost exactly `[I]`:

| Silkscreen | Mitsubishi MR-J equivalent | Meaning |
|---|---|---|
| `xLA` / `xLAR` | `LA` / `LAR` | Encoder A-phase pulse output, and its inverse |
| `xLB` / `xLBR` | `LB` / `LBR` | Encoder B-phase pulse output, and its inverse |
| `xPG` | `PG` | Pulse-train command line |
| `xINP` | `INP` | **In-position** status output from the amplifier |
| `xRDY` | `RD` | **Ready** status output from the amplifier |

So this board is talking to **two AC servo axes** — prefix `M` and prefix `B` —
taking quadrature encoder feedback differentially and reading back in-position and
ready status. That is consistent with the racking axis plus a yarn-carrier or
take-down axis.

> This is the strongest single piece of protocol evidence in the whole project,
> because these signal names are an amplifier-vendor convention rather than a
> house convention. It tells you the servo side is a **standard, documented
> interface** — unlike the machine bus, which is not.

### 3.2 `COM1` / `COM2` — the machine bus

Two 20-pin IDC headers on the lower right edge `[P]`. `[I]` **daisy-chain in and
out** — one carries the bus from upstream, the other passes it to the next node.
That is the standard pattern for addressable satellite nodes.

### 3.3 Other

| Ref | Type | Role |
|---|---|---|
| 34-pin IDC, bottom edge | ribbon | Second I/O group `[?]` |
| `SR2`, `SL` | headers | `[I]` right / left limit or sensor groups |
| `S1` | 4-way DIP switch | `[I]` **node address** — up to 16 addresses, or a mix of address and mode bits. Critical for any multi-node emulation |
| `CON1` | 10-pin header | `[I]` FPGA programming / configuration |
| `CY1` | crystal | FPGA clock, beside the `CLK` silkscreen |
| `D4` `D5` `D7`–`D12` | LEDs | Status indication |
| `RST` | net | Reset |

### 3.4 FPGA configuration pins

Silkscreened along the FPGA edge, beside the device pins `[P]`:

```
SIO   SK   SE   SO          MIK   MK   M0   MS
```

`[I]` The `S…` group is the MachXO2 **slave SPI** sysCONFIG port
(`SI` / `SCK` / `SN` chip-select / `SO`). The `M…` group follows the same
three-wire in/out/clock convention used across the whole system — see
[docs/02-communication-protocol.md](../../docs/02-communication-protocol.md).

Series resistors marked `200` (20 Ω) sit on the differential lines — standard
source-termination damping.

---

## 4. Power

| Item | Detail | Evidence |
|---|---|---|
| Inlet | 4-position screw terminal, top edge | `[P]` |
| Fuses | Two cartridge holders (`FL3` region and one more) | `[P]` (ratings not legible) |
| Filtering | Common-mode choke `L1` (toroid), ferrite beads, `E2` 100 µF / 50 V electrolytic | `[P]` |
| Protection | `D6` ultrafast axial rectifier in series with the DC input | `[P]` |
| Rails present | **+24 V**, **+12 V**, **3.3 V**, with `GND`, `SGND` and `DGND` all silkscreened separately | `[P]` |
| Rail indicator | `D17` + `R47` | `[P]` |
| Test points | `+12V`, `3.3V`, `GND`, `SGND` plated pads | `[P]` |

**Three** distinct ground nets (`GND`, `SGND`, `DGND`) are named here versus two
on the Main Board. `[I]` `DGND` is the isolated field-side ground behind the
optocouplers. Do not bond them when bench-testing.

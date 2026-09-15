# 09 — Field Checklist

**Print this and take it to the factory.** Every question this project needs
answered, organised by where you will be standing when you can answer it.

Fill in the answer lines. Anything you cannot answer, mark **N/A** or **?** — a
recorded "don't know" is worth more than a blank, because it tells us the question
is still open rather than overlooked.

**Take photographs of everything, even things not on this list.** You may not get
a second visit. Photographs cost nothing; a missing photograph has already cost
this project the entire interconnection record
([08-needed-items.md](08-needed-items.md) §5).

---

## Before you leave

**Bring:**

- [ ] Phone / camera, **fully charged**, plenty of free storage
- [ ] A torch — cabinets and machine heads are dark
- [ ] Numbered cable labels or masking tape + marker
- [ ] Multimeter, if you have one by then
- [ ] This checklist, printed
- [ ] A bag or box for anything you are allowed to take away

**Photograph technique** — the two failures that cost us already:

| Problem | Fix |
|---|---|
| Glare hides chip markings | Light from an **angle**, never head-on. Turn the flash **off** |
| Silkscreen runs off the edge of the frame | Include ~20 mm of board beyond whatever you are shooting |

**Safety:** if any machine on the floor is powered or running, do not open
cabinets, touch terminals or reach into a head. Ask the maintenance staff to
isolate it first. Nothing in this document is worth an injury.

---

## Station 1 — The four questions that outrank everything

Answer these first. They decide whether the rest of the project is worth doing,
and you may be able to answer them from the doorway.

**Q-01 — Does the dead machine still exist anywhere? (F-06)**
› Frame, cabinet, harness, head — any part of it. Scrapyard, back of the
workshop, outside under a tarpaulin.
› **Gates 12 items in the register.**

```
Answer: ______________________________________________________________
Where exactly: _______________________________________________________
```

**Q-02 — Are there OTHER machines of the same model at this factory? (F-07, F-08)**
› **This is probably the most valuable question in the document.** A sister
machine gives you a live reference, field wiring, a needle bed, and a possible
donor selector board — all at once.

```
How many: ________   Model(s): ____________________________________
Running / idle / scrapped: ___________________________________________
Same controller boards? (open one and check): ________________________
```

**Q-03 — A replacement controller for *which* machine? (F-08)**
› The original host is gone. Ask whoever owns the project what the finished
controller is supposed to go into.

```
☐ Other machines still in service — how many: ______
☐ Rebuilding the dead machine
☐ A product to sell to others
☐ Learning / archival only
Answer: ______________________________________________________________
```

**Q-04 — Why was it dismantled? What actually failed? (F-04)**
› Ask the maintenance person, not the manager. "What stopped working first?"

```
Answer: ______________________________________________________________
Which board was suspected: ___________________________________________
Was anything replaced before it died: ________________________________
```

---

## Station 2 — If the machine carcass exists

Skip to Station 4 if Q-01 was "no".

**Q-05 — Is the carriage head still on the machine?**

```
☐ Yes   ☐ No   ☐ Removed, stored separately — where: ________________
```

**Q-06 — ⭐ Is the selector board still inside the head?**
› **The single most important physical item in this project.** It is the board
that drives needle selection. Everything about needle selection is currently
unrecoverable without it.
› Open the head. Look for a PCB with **many identical driver chips in rows** and
a fat flexible cable going back to the cabinet.

```
☐ Yes — PHOTOGRAPH EVERYTHING, both sides, every chip, every connector
☐ No — head is empty
☐ Could not open the head
Notes: _______________________________________________________________
```

**If it is there: can you take it with you?**  ☐ Yes  ☐ No

**Q-07 — Is the cabinet still wired, with connectors seated?**
› If yes, **photograph before unplugging anything.** This is the record we lost.

```
☐ Intact and wired   ☐ Partly stripped   ☐ Empty
```

- [ ] Whole cabinet, doors open, as found
- [ ] Every board still mounted, with its harness attached
- [ ] **Every cable, both ends**, before anything is disconnected
- [ ] DIN rail terminal blocks and their numbering
- [ ] The fat cable running from the cabinet to the machine head, both ends

**Q-08 — The power supplies**
› We know two are Mean Well NES-350-27 (27 V). Two smaller ones are unidentified.

```
Unit 3 model: ________________  Output: ______ V ______ A
Unit 4 model: ________________  Output: ______ V ______ A
AC selector switch position (115 V / 230 V): _________________________
Measured output of each NES-350-27 (if powered): _____________________
```

- [ ] Nameplate of every supply
- [ ] Their DC output terminals, **wired**, so rails can be traced

**Q-09 — Servo amplifiers**
› We believe they are Mitsubishi MR-J series, inferred from signal names only.

```
How many: ______  Make: __________________  Model: ___________________
```

- [ ] Nameplate of each amplifier
- [ ] Their connector wiring at the amplifier end

**Q-10 — Machine nameplate**
› The **Julong GDH122SA** attribution currently rests on nobody having read a
nameplate.

```
Make: _______________  Model: _______________  Serial: _______________
Year: ________  Voltage: ________  Needle gauge / bed width: _________
```

- [ ] Photograph the nameplate

**Q-11 — Is the needle bed intact?**
› Matters because actuator characterisation cannot be done without one.

```
☐ Intact   ☐ Damaged   ☐ Stripped / gone
Are the selector actuators still in it: ______________________________
```

---

## Station 3 — If a sister machine is running

The highest-value station in the document if Q-02 was yes. **Ask permission
before opening anything on a working machine.**

**Q-12 — Is its controller the same as ours?**
› Open the cabinet and compare against our board photos.

```
HMI board same?     ☐ Yes ☐ No ☐ Similar — differences: _______________
Main board same?    ☐ Yes ☐ No ☐ Similar — silkscreen ID: _____________
Racking board same? ☐ Yes ☐ No ☐ Similar — silkscreen ID: _____________
```

**Q-13 — ⭐ Photograph its cabinet fully wired**
› This **recovers the interconnection record we permanently lost** — not for our
dead machine, but for the model. It is nearly as good.

- [ ] Whole cabinet, doors open, connectors seated
- [ ] **Every cable, both ends**, and where each one goes
- [ ] Which supply feeds which board, and on which terminal
- [ ] Which connector on which board goes to the machine head
- [ ] The three boards in place, with harness attached
- [ ] DIN terminal numbering

**Q-14 — Its selector board, inside the head**

- [ ] Both sides, every chip, every connector, board ID and date code
- [ ] The head-to-cabinet cable, both ends

**Q-15 — Can a selector board be bought, borrowed, or swapped out? (F-07)**

```
☐ Spare in stock — how many: ______
☐ Can be ordered — supplier: _________________  Price: _______________
☐ Can borrow one temporarily
☐ No
```

**Q-16 — Spares stock**
› Ask to see the spares cupboard. Any of our three boards, any selector board,
any Lattice-based board at all.

```
What is in stock: ____________________________________________________
```

**Q-17 — Can you watch one run?**
› Video is fine and takes seconds. Useful: carriage traversing, racking
movement, yarn carriers changing, the HMI screen during normal operation.

- [ ] Short video of a normal knitting cycle
- [ ] Photograph the HMI screen — main menu, settings, any diagnostics page
- [ ] Note any model/version string shown on the HMI

---

## Station 4 — The three boards we have

Wherever they are stored now.

**Q-18 — Condition**

```
Any visible damage, burn marks, corrosion, previous repair: ___________
______________________________________________________________________
```

**Q-19 — ⭐ Switch and jumper positions, AS FOUND — do not move them**
› With the harness gone, these are among the only surviving evidence of how this
machine was configured. **Photograph before touching.**

```
Racking board S1 (4-way DIP):  1=____ 2=____ 3=____ 4=____
Main board P1 (2-way jumper):  1=____ 2=____
HMI board SW1 (4-way DIP):     1=____ 2=____ 3=____ 4=____
```

**Q-20 — Photographs still needed from the boards in hand**

- [ ] **HMI boot flash** — **lift the CPU module off and shoot its UNDERSIDE.** The top side has only three chips and none is the flash
- [ ] Main board jumper `P1` and its four-row Chinese truth table, close up
- [ ] The six stepper driver chips, **oblique light** — markings are unreadable in every existing photo
- [ ] Main board unidentified chips: `U3` `U5` `U7` `U17`–`U19` `U_Z1` `U_K2` `U21` `U22`
- [ ] Racking board `U11`, `U13`
- [ ] Every connector, square-on, with its **pin-1 mark** visible
- [ ] Solder side underneath both 4-pin screw terminals
- [ ] Every fuse body, so ratings can be read
- [ ] JTAG headers on all three boards, with surrounding silkscreen

---

## Station 5 — People

Often worth more than the hardware. Find the person who maintained this machine.

**Q-21 — Who maintained it, and are they reachable?**

```
Name: _________________________ Contact: _____________________________
```

**Q-22 — Ask them directly:**

```
What plugged into what? (sketch it if they remember) __________________
______________________________________________________________________
Which supply fed which board: ________________________________________
What did the Main board's J1 sensors actually connect to: _____________
______________________________________________________________________
Did the machine ever get modified or repaired: ________________________
Was there a UPS fitted? (there is a UPS-T signal on the board) ________
```

**Q-23 — The supplier or dealer**
› They may sell the selector board, or have manuals.

```
Company: ______________________ Contact: _____________________________
Still trading: ☐ Yes ☐ No
```

---

## Station 6 — Paperwork

**Q-24 — Any documentation at all?**

```
☐ Operator manual        ☐ Service manual      ☐ Wiring diagrams
☐ Parts list             ☐ Invoice / purchase records
☐ Chinese-language docs (fine — photograph them, they can be translated)
☐ Nothing
```

- [ ] Photograph **every page** of anything you find. Do not skim-select.

**Q-25 — Pattern files**
› The HMI took pattern files over USB. Any surviving USB stick, SD card or PC
with the pattern software would show the file format.

```
☐ USB stick found      ☐ Design PC exists      ☐ Software name: ______
```

**Q-26 — Factory electrical supply**

```
Mains voltage: ________ V,  ________ phase,  ________ Hz
```

---

## Station 7 — Before you leave

- [ ] Re-read Q-01, Q-02, Q-03, Q-06 — are they actually answered?
- [ ] Have you photographed the **selector board** if one exists anywhere on site?
- [ ] Have you photographed a **sister machine's wired cabinet** if one exists?
- [ ] Switch and jumper positions recorded **before** anything was moved?
- [ ] Backed the photos up before leaving the site?
- [ ] Asked whether you can take the selector board, a spare board, or any manual?

**If you can take only one thing away from this visit, take a selector board.**
If you can take only one photograph, photograph a sister machine's cabinet fully
wired with every connector seated.

---

## Quick reference — the five things that matter most

| # | Thing | Why |
|---|---|---|
| 1 | **A selector board**, from anywhere | The only route to the needle-selection protocol |
| 2 | **A sister machine's wired cabinet**, photographed | Recovers the interconnection record for the model |
| 3 | **Answer to Q-03** — replacement for what? | Decides whether the project is worth continuing |
| 4 | **Switch / jumper positions**, before anyone moves them | The last surviving configuration evidence |
| 5 | **The maintenance person's phone number** | They remember the harness. Nothing else does |

---

## After the visit

Bring the answers and photographs back and these get updated:

| Answers feed into |
|---|
| [08-needed-items.md](08-needed-items.md) — the register; many ⏸ items resolve |
| [06-open-questions.md](06-open-questions.md) — §1 may reopen if a selector board turns up |
| [05-replacement-controller-plan.md](05-replacement-controller-plan.md) — Route A/B and the §10 fallback |
| [04-interconnection-map.md](04-interconnection-map.md) — topology, if a wired cabinet is photographed |
| [03-power-architecture.md](03-power-architecture.md) — rail assignment and the unidentified supplies |

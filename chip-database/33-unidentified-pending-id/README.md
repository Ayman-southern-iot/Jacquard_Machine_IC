# Unidentified Components — Pending Photo Identification

**16 items across the three boards.** No datasheet research is possible for
any of these until a legible part marking is photographed — this is a
photography/access blocker, not a research gap. See the project's
`docs/07-photo-shot-list.md` for the exact shot needed for each.

Once any of these is identified, it gets its own numbered folder in this
`chip-database/`, following the same template as the other 32 entries.

---

## HMI Board

| Item | Location | What's needed |
|---|---|---|
| Boot flash | Underside of the CPU mezzanine (`GOLDEN COREBOARD_V1.20`) | The mezzanine's top side carries exactly 3 devices (SRAM, SDRAM, CPU — see `31-`, `30-`, `12-`), so the boot flash **must** be underneath. Lift the mezzanine off and photograph its solder side. |

## Main Board — 11 unidentified designators

| Designator | What's needed |
|---|---|
| `U3` | Macro photo, designator in frame, oblique light |
| `U5` | Same |
| `U7` | Same |
| `U17` | Same |
| `U18` | Same |
| `U19` | Same |
| `U_Z1` | Same |
| `U_K2` | Same |
| `U21` | Same (note: this designator also exists on the HMI board as an AMS1117 — different physical chip, same label, different board) |
| `U22` | Same (same note as U21) |
| `Q_Z1` | Same — likely a discrete transistor rather than an IC given the `Q` prefix |
| `Z2` | Same — the `Z` prefix commonly denotes a zener diode or similar 2-terminal part in this board's own designator convention |

One useful note from the general research: in boards of this era, unread
through-hole DIPs in a mixed-signal design like this one usually turn out to
be either legacy op-amp/comparator families (same class as the LM324/339
already found) or small linear regulators — worth checking those hypotheses
first once a marking is visible, though this is a general pattern, not
specific evidence about this board.

## Racking Board — 2 unidentified designators

| Designator | What's needed |
|---|---|
| `U11` | TSSOP-16 package, sits beside the `RST` net — macro photo, designator in frame |
| `U13` | SOIC package — macro photo, designator in frame |

## Power Supply Rack — 2 unidentified units

| Item | What's needed |
|---|---|
| PSU unit 3 | Smaller enclosed SMPS beside the two Mean Well NES-350-27 units — photograph the nameplate/label directly |
| PSU unit 4 | Same |

---

## Research methodology reminder (once a marking is available)

Per the cross-check across all 7 independent research passes on this
project, the reliable workflow once a marking is legible is:

1. Search the exact marking string as printed (including any prefix/suffix —
   don't normalize or guess at missing characters).
2. Locate the manufacturer's own datasheet first; treat distributor/
   aggregator mirrors as secondary confirmation, not primary source.
3. Cross-check the physical evidence already on hand: package outline, pin
   count, nearby passive components, and — critically — **what the chip is
   wired to** (power rail, nearby connector, adjacent parts) before assuming
   a datasheet match is the correct part on this specific board.
4. Record the lifecycle/sourcing status alongside the functional
   specification, not just the electrical behavior — several "obvious"
   identifications in this project turned out to be obsolete parts with no
   drop-in replacement, which matters as much as knowing what the chip does.
5. State confidence honestly: family-confirmed (marking matches a known
   family but exact suffix unclear) is a different, lower confidence level
   than exact-part-confirmed (full ordering code matches). Don't collapse
   these into a single "confirmed" bucket.

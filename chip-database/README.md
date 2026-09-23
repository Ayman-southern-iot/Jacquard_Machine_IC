# Chip Database — Index

One folder per distinct chip identified across the three control boards.
Each folder's `README.md` is a self-contained dossier: what the chip is, full
electrical specs, board role, **lifecycle/sourcing status**, what a
replacement design needs to know, and a confidence assessment.

**This database has been through a senior-engineer technical audit** —
every formula checked dimensionally, every claim cross-checked for internal
consistency, six issues found and corrected. See
[VALIDATION-REPORT.md](VALIDATION-REPORT.md) for the full audit trail.

**Purpose:** so that any question about a specific chip — "what's the max
current on the stepper drivers," "can I still buy this part," "why is this
paired with that" — can be answered from what's recorded here, without
re-researching it each time.

---

## How this was built

This project's own research (`docs/10-ic-technical-reference.md`) was
cross-checked against **6 independent research passes from other AI
systems**, saved in `Other_AI_Research/` at the project root:

| File | System |
|---|---|
| `jacquard-chip-deep-research_claude.md` | Claude (a prior session on this project) |
| `deep-research-report-chatgpt.md` | ChatGPT |
| `grok.md` | Grok |
| `deepseek.md` | DeepSeek |
| `chip-research-report_kimi.md` | Kimi |
| `ic_datasheet_inventory_gemini.md` | Gemini |
| `perplexity.md` | Perplexity |

Every chip folder in this database synthesizes across all 7 sources
(including this one), and flags where they agreed, where they disagreed, and
where one pass found something the others missed. A few genuinely valuable
findings only surfaced because of this cross-check:

- **Lifecycle/obsolescence status** — several other passes went and checked
  current distributor stock and manufacturer lifecycle flags, which this
  project's own first pass hadn't done. Result: 2 of the 3 FPGAs and several
  other parts turned out to be **obsolete with no drop-in replacement** —
  a materially important fact for planning any repair or replacement design
  that this project's own first research pass had missed entirely.
- **FU120N identity** was resolved this session by cross-referencing two
  independent leads (VBsemi's own "FU120N" part vs. the IR/Infineon
  "IRFU120N" family) — they turned out to be the same functional device
  under two different manufacturer naming conventions, not a conflict.
- **Perplexity's pass** was the most methodologically skeptical of the
  seven, and correctly pushed back on overconfident claims elsewhere (e.g.,
  that a device family name alone proves an exact board-level pin function,
  or that "no external bitstream" is the same as "unhackable"). Its
  calibration standard — distinguish *family confirmed* from *exact part
  confirmed* from *board role confirmed* — is the one applied consistently
  across every folder in this database. See the confidence key below.

## Confidence key (applied in every chip folder)

| Level | Meaning |
|---|---|
| `[P]` Photo-confirmed | The exact marking was read from a photograph of this specific board |
| `[I]` Inferred | Identity guessed from package/circuit topology; marking not legible |
| **Family confirmed** | The chip family/architecture is correctly identified, but the exact ordering suffix (package, speed grade, temp range) is not verified against the photo |
| **Board role inferred** | What the chip *does electrically* is well documented; what it's *wired to on this specific board* is a plausible hypothesis from layout, not confirmed by schematic or continuity trace |

**A datasheet proves what a chip can do. It does not prove that a specific
board uses it that way** — this distinction is called out explicitly wherever
it matters, per Perplexity's methodology critique.

---

## Full chip list

### FPGAs / processing core

| # | Chip | Board | Status |
|---|---|---|---|
| [01](01-lattice-lfxp3c/) | Lattice LFXP3C | Main | Obsolete |
| [02](02-lattice-lcmxo1200c/) | Lattice LCMXO1200C | HMI | Obsolete |
| [03](03-lattice-lcmxo2-7000hc/) | Lattice LCMXO2-7000HC | Racking | **Active** |
| [12](12-samsung-s3c2440a/) | Samsung S3C2440A | HMI | Obsolete |

### Motor drive / analog

| # | Chip | Board | Status |
|---|---|---|---|
| [04](04-ti-drv8818/) | TI DRV8818 (×6) | Main | Active — `[I]` unconfirmed ID |
| [10](10-ti-tlc5620/) | TI TLC5620 | Main | Active (successor suggested) |
| [18](18-uln2804a/) | ULN2804A (×2) | Main | Active |
| [19](19-lm324/) | LM324 | Main | Active |
| [20](20-lm358/) | LM358 (×2) | Main | Active |
| [21](21-lm339/) | LM339 | Main | Active |
| [22](22-lm393/) | LM393 | HMI | Active |

### Bus / isolation / logic

| # | Chip | Board | Status |
|---|---|---|---|
| [05](05-ti-am26lv31/) | TI AM26LV31 (×3+1) | HMI, Main | Active (check suffix) |
| [06](06-ti-am26lv32/) | TI AM26LV32 | All 3 boards | Active (check suffix) |
| [07](07-ti-am26ls31/) | TI AM26LS31 | Racking | **Obsolete, no pin-compatible sub** |
| [08](08-everlight-6n137/) | Everlight 6N137 (×10) | HMI, Racking | Active |
| [09](09-cosmo-357nt/) | Cosmo 357NT (~40) | Main, Racking | Active |
| [23](23-max3232/) | Maxim MAX3232 | HMI | Active |
| [25](25-sn74ahct245/) | SN74AHCT245 | Main | Active |
| [26](26-sn74hc245/) | SN74HC245 | Racking | Active |
| [27](27-sn74lvc07a/) | SN74LVC07A | Main, Racking | Active (check package) |
| [28](28-sn74lvc14a/) | SN74LVC14A | Main | Active |

### Networking / memory

| # | Chip | Board | Status |
|---|---|---|---|
| [11](11-davicom-dm9000a/) | Davicom DM9000A | HMI | Active |
| [30](30-samsung-k4s561632n/) | Samsung K4S561632N (SDRAM) | HMI | **Obsolete, industry-wide** |
| [31](31-cypress-cy62128/) | Cypress CY62128 (SRAM) | HMI | **Obsolete** |

### Power devices

| # | Chip | Board | Status |
|---|---|---|---|
| [13](13-sipex-sp29302/) | Sipex/MaxLinear SP29302 | HMI | Discontinued/limited stock |
| [14](14-utc-tl494/) | UTC TL494 | HMI | Active, cheap, many clones |
| [15](15-hongfa-hfd23/) | HongFa HFD23/005-1ZS | Main | Active (check suffix) |
| [16](16-ao4606/) | Alpha & Omega AO4606 | HMI | Active (genuine); heavily cloned |
| [17](17-fu120n/) | FU120N / IRFU120N | HMI | Active |
| [24](24-ams1117/) | AMS1117 (×2) | HMI | Active |
| [29](29-uf5408/) | UF5408 | Racking | Active |
| [32](32-meanwell-nes-350-27/) | Mean Well NES-350-27 (×2) | PSU rack | **Discontinued — LRS-350-27 successor** |

### Unidentified

| # | Item | Board |
|---|---|---|
| [33](33-unidentified-pending-id/) | 16 items — see folder for full list | HMI, Main, Racking, PSU |

**33 folders total: 32 identified chips + 1 folder tracking the 16 still
unidentified.**

---

## The one fact that changes planning priorities most

**Obsolescence, not electrical function, is now the biggest open risk** in
this system. Five parts have no current-production drop-in replacement:

1. Lattice LFXP3C (Main FPGA) — no pin-compatible substitute at all
2. Lattice LCMXO1200C (HMI FPGA) — same
3. TI AM26LS31 (Racking RS-422 driver) — obsolete, substitutes are not
   pin-identical
4. Samsung K4S561632N (HMI SDRAM) — industry-wide EOL for this memory class
5. Cypress/Infineon CY62128 (HMI SRAM) — same
6. Mean Well NES-350-27 (PSU) — discontinued, but a same-spec successor
   (LRS-350-27) exists

**Practical consequence:** any of these six failing is a redesign trigger,
not a repair. This should weigh heavily in deciding whether to pursue a
board-level repair strategy versus a full replacement-controller design for
this machine — see `docs/05-replacement-controller-plan.md` for how that
decision is framed elsewhere in this project.

---

## Still missing

**16 unidentified components** (see [33](33-unidentified-pending-id/)) are
the only remaining blocker to a complete chip database. All 16 are already
on `docs/07-photo-shot-list.md`. The moment a legible photo exists for any of
them, the same research methodology applies immediately — a new folder gets
created following the template of the other 32.

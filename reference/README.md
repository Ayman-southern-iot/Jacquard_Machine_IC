# reference/

Material about the machine that is not a board photograph and not a vendor
datasheet.

## Layout

```
reference/
├── cabinet/              in-situ shots: whole cabinet, harnesses, terminal blocks
├── servo-amplifiers/     amplifier nameplates and their connector wiring
└── (this file)
```

Both subfolders are created when the photographs exist —
[../docs/07-photo-shot-list.md](../docs/07-photo-shot-list.md) §2.2, §4.2.

## What belongs here

| Item | Why |
|---|---|
| Machine nameplate photo | The **Julong GDH122SA** attribution currently rests entirely on the user's report. Nothing on any board carries a make or model |
| Cabinet as-found photos | Answer "what plugs into what" faster than a continuity session |
| Servo amplifier nameplates | Confirm or refute the Mitsubishi MR-J inference in [../docs/02-communication-protocol.md](../docs/02-communication-protocol.md) §3.1 |
| Cable and harness photos, both ends | Needed before anything is unplugged |
| OEM manuals, if any surface | Operator or service manuals for the machine |
| Translations | The Main Board mode-jumper truth table, and any other Chinese silkscreen |

## What does not belong here

* Vendor datasheets → [`../datasheets/`](../datasheets/)
* Board photographs → `../boards/<board>/images/`
* Measurements and captures → [`../captures/`](../captures/)
* Product listings for candidate replacement parts → beside the board they
  replace, e.g. `../boards/04-power-supply/reference/`

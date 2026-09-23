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
| Machine nameplate photo | Identified as a **Computerized Flat Knitting Machine, model GD-H122S** by **Shaoxing Jinhao Machinery**, per the owner — but this rests entirely on that report. Nothing on any board carries a make or model, and the actual nameplate has not been photographed |
| Cabinet as-found photos | Answer "what plugs into what" faster than a continuity session |
| Servo amplifier nameplates | Confirm or refute the Mitsubishi MR-J inference in [../docs/02-communication-protocol.md](../docs/02-communication-protocol.md) §3.1 |
| Cable and harness photos, both ends | Needed before anything is unplugged |
| OEM manuals, if any surface | Operator or service manuals for the machine |
| Translations | The Main Board mode-jumper truth table, and any other Chinese silkscreen |

## Manufacturer — Shaoxing Jinhao Machinery Co., Ltd. (绍兴金昊机械制造有限公司)

Confirmed via web research this session (three independent listings agreed
on the model spec — see [../docs/01-ic-master-list.md](../docs/01-ic-master-list.md)
§9 for the full cross-checked spec sheet).

| | |
|---|---|
| Location | Keyan Industrial Area, Shaoxing County, Shaoxing, Zhejiang, China |
| Contact (from search listing) | Phone `0575-84291942`, contact 孔庆苗 (Kong Qingmiao), mobile `13857525289` — **unverified, from a marketplace listing, not confirmed live this session** |
| Manufacturer showroom / product page | `jindajx.com` — exists and returned a listing for this exact model, but blocked automated fetching (403). **Visit directly in a browser** |
| Marketplace listings | made-in-china.com (showroom `jindajx`), zhiyingmachinery.com (reseller), chinatexnet.com |
| Listing freshness | The made-in-china.com listing shows "Last Active: October 2009" — this may mean the *listing* is stale, not that the company has closed. **Status of current operation is not confirmed** |
| Main products | Glove machines, sock machines, flat knitting machines |

**Action worth taking:** call the number above, or visit `jindajx.com` directly,
to ask about spare parts (especially a selector/carriage board — see
[../docs/06-open-questions.md](../docs/06-open-questions.md) §1), operator or
service manuals, and whether the GD-H122S is still in production. This is a
real lead, not a dead end — treat it as the first thing to try before
assuming a donor board must come from a scrapped machine.

## What does not belong here

* Vendor datasheets → [`../datasheets/`](../datasheets/)
* Board photographs → `../boards/<board>/images/`
* Measurements and captures → [`../captures/`](../captures/)
* Product listings for candidate replacement parts → beside the board they
  replace, e.g. `../boards/04-power-supply/reference/`

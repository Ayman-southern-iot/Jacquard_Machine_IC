# Cosmo 357NT / KPC357NT

**Board:** Main Board ~36–38 instances, Racking Board ×3
**Identification confidence:** `[P]` Confirmed by photograph, marking
`cosmo 357NT K46 C` legible in situ

---

## What it is

A **phototransistor-output** optocoupler — mechanically small (4-pin
mini-flat SMD package) but electrically much slower than the 6N137
(`08-everlight-6n137/`), because it's a plain phototransistor rather than a
logic-gate part. This is the isolation device for the ~37-channel field
sensor input bank on the Main Board.

## Key specs

| Parameter | Value |
|---|---|
| Manufacturer | Cosmo Electronics Corporation (Taiwan) |
| Type | 4-pin mini-flat phototransistor photocoupler |
| Current Transfer Ratio (CTR) | Rank-dependent, roughly **50–600%** overall family range; the specific `K46`/`0B`-class rank commonly cited at **130–260%** at IF=5mA, VCE=5V |
| Isolation voltage | **3,750 Vrms** |
| Forward voltage (LED side) | 1.2–1.4 V typical |
| Rise/fall time | **~5 µs rise, ~4 µs fall** typical (up to 20 µs max at RL=100Ω) — this is the key number that caps its use case |
| VCEO | 80 V |
| IC max | 50 mA |
| Package size | ~30% smaller footprint than a conventional DIP optocoupler |
| Certifications | UL/cUL/VDE/CQC approved |
| Operating temp | −55°C to +115°C (one source) — **flagged on validation**: this range is unusually wide for a small optocoupler of this class and closely resembles the operating-temperature range cited elsewhere in this database for an unrelated part (the AO4606 MOSFET, `−55°C to +150°C`). Possible cross-contamination between sources during the original research pass. Treat as **unverified** until checked against Cosmo's own datasheet directly — a more typical range for this device class is `−30°C to +100°C` or similar |

## Why it's slow, and why that's fine here

~5 µs rise/fall caps this part at roughly tens-of-kHz signalling — nowhere
near the 6N137's 10 Mbit/s. This is entirely consistent with its role:
isolating **slow field sensors** (limit switches, presence detectors, status
lines) rather than anything on the fast machine bus. The CTR range (130–260%
for the observed rank) means a modest LED drive current is enough to fully
saturate the output transistor — matching the `472` (4.7 kΩ) input resistors
observed pulling from the board's 24 V field-sensor rail:

```
I_LED ≈ (24V − 1.4V) / 4700Ω ≈ 4.8 mA
```

well within the part's drive range, giving comfortable margin for a firm
logic-high output.

## Board role

Main Board: one instance per isolated 24 V digital sensor input, feeding the
`J1`/`J2` field connectors. Racking Board: 3 instances for isolated slow I/O
alongside the 8× 6N137 fast bus isolation.

## Lifecycle / sourcing status

**Active, in production.** Confirmed current stock via RS Components
(Thailand distributor) and Cosmo's own official datasheet (most recent
revision seen: rev .5–.7 depending on exact document). Easy to re-source.

## What this means for a replacement design

No sourcing risk. If exact CTR-rank matching matters for a specific circuit
(some designs are sensitive to CTR variation between ranks), specify the
same suffix (`K46`/equivalent rank code) when re-ordering rather than a bare
"357NT."

## Confidence caveats

CTR range specifically is the one number that varies most across sources —
different research passes cited ranges from "50% min" to "130–260%" to
"50–600% overall family" depending on which rank/grade and test condition
each source was reading. This is expected: CTR is not a single fixed gain,
it's rank- and condition-dependent, and the board's exact populated rank
(`K46` per the photographed marking) is the one that matters here. Treat the
130–260% figure as the best-match value for the specific rank on this board,
not a universal constant for "357NT" as a family name.

## Sources

- Cosmo Electronics Corporation: official KPC357NT datasheet (doc
  69Pb4001.x, multiple revisions)
- RS Components (Thailand): current stock confirmation
- Cross-verified across 7 independent research passes (this document + 6
  other AI sessions in `Other_AI_Research/`)

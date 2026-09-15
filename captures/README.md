# captures/

Bus captures, continuity tables and measurement logs. Empty until Phase 1 of
[../docs/05-replacement-controller-plan.md](../docs/05-replacement-controller-plan.md)
begins.

## Layout

```
captures/
├── continuity/      Phase 1 — multimeter work, no instruments needed
├── logic/           Phase 4 — logic-analyser captures (.sr, .csv)
└── measurements/    rail voltages, resistances, scope notes
```

## Naming

```
YYYY-MM-DD_<board>_<what>.<ext>

2026-09-20_hmi_db9-identification.md
2026-09-22_main_j1-pinout.csv
2026-10-05_hmi_bus-idle.sr
2026-10-05_hmi_bus-selection-cycle.sr
```

## Continuity tables — suggested columns

The important one is deliverable 3 of Phase 1: transceiver pin to connector pin.

```csv
board,ic_ref,ic_pin,signal_name,connector,conn_pin,notes
main,U12,3,MO1+,J2,7,"120R to conn_pin 8 - terminated"
```

Record the **measured** resistance across candidate pairs. A ~100–120 Ω reading
means an RS-422 termination, which tells you that end is a physical end of the
bus, not mid-chain.

## Logic captures — what to record alongside the file

A capture without its context is nearly worthless six months later. For each one,
note:

| Field | Example |
|---|---|
| Probe points | HMI test points `MK1` `MO1` `MS1` `MIK1` … |
| Channel map | ch0=`MK1`, ch1=`MO1`, … |
| Sample rate | 100 MS/s |
| Trigger | falling edge on `MS1` |
| Machine state | idle / single needle-selection cycle / racking move |
| Rail voltage at the time | 27.1 V measured |

The channel map and trigger setup for the **first** capture are in
[../docs/02-communication-protocol.md](../docs/02-communication-protocol.md) §7.

## Before you capture

Read [../docs/02-communication-protocol.md](../docs/02-communication-protocol.md) §7.
Two mistakes waste a whole session:

* **Never clip onto an RS-422 pair.** Tap single-ended — the HMI test points are
  plated, accessible and exist for this.
* **≥ 100 MS/s.** A 24 MS/s USB analyser is marginal against a `[I]` 1–5 MHz bus clock.

Then work through the falsification tests in §8 — they check the whole protocol
model at once.

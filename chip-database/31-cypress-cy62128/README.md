# Cypress CY62128EV30LL-45ZXI

**Board:** HMI Board mezzanine — scratch/buffer SRAM
**Identification confidence:** `[P]` Confirmed by photograph

---

## What it is

A 1 Mbit (128K × 8) asynchronous static RAM, providing fast scratch/buffer
memory alongside the main SDRAM (`30-samsung-k4s561632n/`).

## Key specs

| Parameter | Value |
|---|---|
| Density | 1 Mbit (128K × 8) |
| Access time | 45 ns (this part's grade); family also offers 55/70 ns grades |
| Supply | Low-power variant, single supply (specific voltage per exact suffix) |
| Package | TSOP-32 |
| Power-down | Automatic CE power-down current in the µA range |

## Board role

Scratch/buffer memory for the ARM CPU — fast asynchronous access for
whatever doesn't need the SDRAM's synchronous interface.

## Lifecycle / sourcing status

**Obsolete.** Cypress was acquired by **Infineon** in 2020; Infineon's own
part page lists this as obsolete, with substitute candidates from Infineon
itself and Winbond/STMicro — though those substitutes are also marked
obsolete. This entire class of async parallel SRAM is winding down
industry-wide, not just this one part.

## What this means for a replacement design

Gray-market stock (through brokers like Worldway, ICPartonline, and similar
Chinese/Hong Kong distributors) is the realistic sourcing path today if this
exact part is ever needed. A genuine redesign around a currently-produced
memory type is the more durable long-term option.

## Sources

- Cypress / Infineon: CY62128 datasheet and lifecycle status page
- Ciiva: CY62128-70SC lifecycle and substitute-table cross-check
- Cross-verified across 7 independent research passes (this document + 6
  other AI sessions in `Other_AI_Research/`)

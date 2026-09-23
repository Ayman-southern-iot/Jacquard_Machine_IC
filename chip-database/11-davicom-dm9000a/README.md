# Davicom DM9000A / DM9000AEP

**Board:** HMI Board — Ethernet controller
**Identification confidence:** `[P]` Confirmed by photograph

---

## What it is

A single-chip **10/100 Ethernet MAC + PHY** controller with a general
processor bus interface, purpose-built to bolt onto embedded SoCs that lack
their own Ethernet MAC — exactly the S3C2440A CPU's situation on this board
(see `12-samsung-s3c2440a/`).

## Key specs

| Parameter | Value |
|---|---|
| Integration | MAC + 10/100 PHY + internal SRAM buffer on one die |
| Internal SRAM | 16 KB (4K DWORD), commonly split ~13 KB RX FIFO / ~3 KB TX FIFO |
| Host bus | Configurable **8-bit or 16-bit** parallel processor interface (strap-pin selected); some documentation references 32-bit modes for related family members, but confirm against the exact DM9000A datasheet rather than assuming all widths apply |
| Bus control | `CMD` pin selects index vs. data port; `IOR#`/`IOW#` strobes, ~10 ns |
| Auto-negotiation | HP Auto-MDIX supported |
| Flow control | IEEE 802.3x |
| Offload | Hardware IP/TCP/UDP checksum offload |
| Power management | Wake-on-LAN (magic packet / link change / sample frame match) |
| EEPROM | Optional 93C46 serial EEPROM support for MAC address / vendor-product ID (0A46/9000h) |
| Package | 48-pin LQFP |
| Supply | 3.3 V core with 5 V-tolerant I/O |
| Clock | 25 MHz crystal |

## Board role

Bridges the S3C2440A's external-memory-style bus to a standard Ethernet PHY,
feeding the HanRun HR911105A RJ45 magjack observed on the board. This is why
the DM9000A is present at all: the CPU has no built-in MAC.

## Lifecycle / sourcing status

**Actively stocked.** Confirmed current at LCSC (part C14875) and listed in
JLCPCB's parts library. Davicom Semiconductor (Taiwan) still lists current
datasheets for the part. Easily re-sourceable from Chinese distribution
despite being a fairly old design (datasheet dates to ~2005–2006).

## What this means for a replacement design

Low risk — this part remains buyable new. If a replacement HMI design keeps
a similar parallel-bus MAC+PHY architecture, this exact chip (or a
same-family sibling) is a reasonable choice; if migrating to a modern SoC
with a built-in MAC (very common today), this chip becomes unnecessary
entirely.

## Confidence caveats

Core integration (MAC+PHY+16KB SRAM) and Auto-MDIX/checksum-offload features
are confirmed and consistent across every research pass. **One research pass
(Perplexity) specifically flagged the "8/16/32-bit host bus modes" claim as
needing verification against the exact datasheet and strap configuration** —
do not assume all three widths are simultaneously available or that this
board uses anything other than whichever single mode its strap pins select.
That specific mode has not been confirmed by continuity tracing on this
board.

## Sources

- Davicom Semiconductor: DM9000A / DM9000AEP datasheet (DS-F01, dated
  ~May 2006), Application Notes v1.20/v1.21
- LCSC: current stock confirmation (part C14875)
- Columbia University / Cornell ECE5760 course mirrors of the datasheet and
  application notes
- GitHub (yol/dm9000 AVR driver source) — confirms bus behavior and packet
  header format in practice
- Cross-verified across 7 independent research passes (this document + 6
  other AI sessions in `Other_AI_Research/`)

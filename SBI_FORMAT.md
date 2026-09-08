# SBI format — the IOP VAG bank

Status: **CONFIRMED**. Byte-exact round-trip passes on both sample banks (Test A), and
resize-and-rebuild passes at 0.5×, 1.0× and 2.5× payload size (Test C).

Engine name: this is the resource consumed by `sound::IOPVAGBankParm`, whose constructor in the
GoW2 ELF is `IOPVAGBankParm(IFF::Header const*, sound::BankLoadParm const*)`. Its IFF client type
is `kIOPBank` (4).

## Layout

All little endian except the embedded VAG headers.

```
+0x00  u16  ServerId     0x18 (GoW1) / 0x15 (GoW2)  -- IFF::Header.m_ID
+0x02  u16  Version      4                          -- IFF::Header.m_Version = kIOPBank
+0x04  u32  EntryCount
+0x08  Entry[EntryCount]
...    VAG payloads, packed back to back
...    [GoW2 only] zero padding up to a 0x10 boundary
```

### Directory entry — 0x1C (28) bytes

| Offset | Size | Type | Field | Description | Confidence |
|---|---|---|---|---|---|
| +0x00 | 0x18 | char[24] | Name+Ext | `"NAME\0ext\0"` packed, zero padded | CONFIRMED |
| +0x18 | 4 | u32 | Offset | absolute file offset of the VAG payload | CONFIRMED |

The name field is **not** two fixed sub-fields. It is a NUL-terminated name immediately followed
by a NUL-terminated extension, then zero padding. Evidence:

```
entry 0 : "DIEVOC1\0"  at +0x00..+0x07, "vag\0" at +0x08..+0x0B   (7-char name)
entry 1 : "H_ATTKL1"   at +0x00..+0x07, NUL at +0x08, "vag" at +0x09..+0x0B  (8-char name)
```

An 8-character name fills the first eight bytes with no room for a terminator there, so the
extension slides by one. A fixed `char name[8]; char ext[4];` reading would be wrong.

**There is no size field.** A payload's length comes from the VAG header it points at
(`0x30 + DataSize`). Verified for all 32 entries across both banks: each entry's offset equals
the previous offset plus `0x30 + DataSize`, with zero gap.

## The Hero banks, fully decoded

Both banks: `EntryCount = 16`, directory `0x08..0x1C8`, first payload at `0x1C8`, all 16 VAGs at
**14000 Hz**, version 0, blank embedded names.

| # | Name | Offset | Size | Rate | Duration |
|---|---|---|---|---|---|
| 0 | DIEVOC1 | 0x0001C8 | 0x02F30 | 14000 | 1.504 s |
| 1 | H_ATTKL1 | 0x0030F8 | 0x02370 | 14000 | 1.128 s |
| 2 | H_ATTKL2 | 0x005468 | 0x02ED0 | 14000 | 1.488 s |
| 3 | H_ATTKS1 | 0x008338 | 0x01460 | 14000 | 0.644 s |
| 4 | H_ATTKS2 | 0x009798 | 0x014D0 | 14000 | 0.658 s |
| 5 | H_ATTKS3 | 0x00AC68 | 0x018C0 | 14000 | 0.784 s |
| 6 | H_ATTKS4 | 0x00C528 | 0x01590 | 14000 | 0.686 s |
| 7 | H_DMG1 | 0x00DAB8 | 0x014B0 | 14000 | 0.651 s |
| 8 | H_DMG2 | 0x00EF68 | 0x012B0 | 14000 | 0.581 s |
| 9 | H_DMG3 | 0x010218 | 0x01350 | 14000 | 0.602 s |
| 10 | H_DMG4 | 0x011568 | 0x01300 | 14000 | 0.595 s |
| 11 | H_DMG5 | 0x012868 | 0x013C0 | 14000 | 0.616 s |
| 12 | H_DMG6 | 0x013C28 | 0x00FC0 | 14000 | 0.483 s |
| 13 | JUMPVOC1 | 0x014BE8 | 0x013B0 | 14000 | 0.616 s |
| 14 | JUMPVOC2 | 0x015F98 | 0x010A0 | 14000 | 0.518 s |
| 15 | LIFTVOC1 | 0x017038 | 0x01340 | 14000 | 0.602 s |

Last payload ends at `0x18378`. GoW1 file size = `0x18378`; GoW2 file size = `0x18380`
(padded to 0x10).

## Alignment

| Rule | Value | Confidence |
|---|---|---|
| Payload start alignment | none — payloads are packed with zero gap | CONFIRMED |
| VAG DataSize granularity | 16 (one ADPCM frame) | CONFIRMED |
| File-size padding, GoW1 | none | CONFIRMED (1 sample) |
| File-size padding, GoW2 | pad to 0x10 | HIGH (1 sample, exact arithmetic) |

No global alignment is applied. The writer uses the rule for the game it detected from the
server id, and reproduces the source file's convention on an unmodified rebuild.

## Rebuild rules

1. `EntryCount` is written from the entry list.
2. The directory is `8 + count*0x1C` bytes; the first payload starts immediately after it.
3. Each payload is written at the running cursor and its offset stored at entry `+0x18`.
4. Payloads may grow or shrink freely — later entries simply move.
5. GoW2 files are padded to 0x10; GoW1 files are not.
6. An entry whose name is unchanged reuses its original 24-byte name field verbatim, so an
   untouched bank rebuilds byte-for-byte.

### Test C results (resize round-trip on `SBI_Hero_GOW2_PS2`, entry `H_ATTKL1`)

| Scale | Payload before | Payload after | New file size | Structure valid | Other entries intact |
|---|---|---|---|---|---|
| 1.0× | 0x2370 | 0x2380 | 99,216 | yes | yes |
| 0.5× | 0x2370 | 0x11E0 | 94,704 | yes | yes |
| 2.5× | 0x2370 | 0x5860 | 112,752 | yes | yes |

In each case the rebuilt file was re-parsed from scratch and every following entry's offset was
checked to equal the previous offset plus its payload size, with all other entries' ADPCM bytes
compared byte-for-byte against the original.

## Structural limits

No size field anywhere in the SBI caps a payload, and offsets are full 32-bit, so the container
itself imposes no practical limit. The real constraint is downstream: the bank is uploaded to
SPU2 sound RAM (2 MB total on the PS2, shared with everything else playing). A bank that grows
far beyond its original size may load structurally but fail to fit in SPU RAM at run time. The
tool does not enforce a limit — it reports the new total size in the log so the size increase is
visible.
Confidence on the SPU-RAM reasoning: **MEDIUM** (architectural, not measured against these files).

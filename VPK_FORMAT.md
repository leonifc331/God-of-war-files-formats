# VPK format — interleaved multi-channel PS-ADPCM stream

Status: **CONFIRMED** for parse, decode and rebuild. Byte-exact round-trip passes on both sample
files; stereo decode verified by ear-independent measurement (L and R differ, plausible RMS).

## Header

| Offset | Size | Type | Field | GoW1 sample | GoW2 sample | Confidence |
|---|---|---|---|---|---|---|
| 0x00 | 4 | char[4] | Magic | `20 4B 50 56` | `20 4B 50 56` | CONFIRMED |
| 0x04 | 4 | u32 | BytesPerChannel | 973,904 | 4,388,512 | HIGH |
| 0x08 | 4 | u32 | DataOffset | 0x800 | 0x800 | CONFIRMED |
| 0x0C | 4 | u32 | Interleave | 0x2000 | 0x2000 | CONFIRMED |
| 0x10 | 4 | u32 | SampleRate | 32000 | 32000 | CONFIRMED |
| 0x14 | 4 | u32 | Channels | 2 | 2 | CONFIRMED |
| 0x18 | 4 | u32 | Unknown18 | 0x010E007F | 0x010E007F | UNKNOWN |
| 0x1C | 4 | u32 | Unknown1C | 0x005A007F | 0x005A007F | UNKNOWN |
| 0x20 | 0x7E0 | — | zero | all zero | all zero | CONFIRMED |

### The magic is a reversed FourCC

The bytes `20 4B 50 56` read as ASCII are `" KPV"`; reversed they are `"VPK "`. As a
little-endian `u32` they are `0x56504B20`. This is the same convention as the SBlk bank ids
(`2NEG` → `GEN2`). Confidence: **CONFIRMED** — the reading is consistent with a second,
independent instance of the same convention in a different structure of the same engine.

### Field 0x04 is bytes per channel

This is the reading that makes the arithmetic work:

```
RH5006C (GoW2):
  file 8,783,872 - 0x800 header       = 8,781,824 body
  8,781,824 / 0x2000                  = 1072 blocks exactly
  body / 2 channels                   = 4,390,912 bytes per channel available
  field@0x04                          = 4,388,512
  4,388,512 / 16 * 28                 = 7,679,896 samples per channel
  7,679,896 / 32000                   = 239.997 s   <-- exactly a four-minute stream
  padding                             = 2,400 bytes per channel

ATH5C5_2 (GoW1):
  body 1,949,696 / 0x2000             = 238 blocks exactly
  field@0x04                          = 973,904  -> 53.260 s
  padding                             = 944 bytes per channel
```

A four-minute stream landing within 3 ms of exact is not a coincidence, and the field being just
under half the body in both files rules out "total bytes". Confidence: **HIGH**.

An alternative reading — that field 0x04 is a *sample* count — gives 30.4 s for the GoW1 file,
which does not correspond to any round number and does not divide evenly by the frame size.
Rejected.

### Unknown18 / Unknown1C

`0x010E007F` and `0x005A007F` are byte-identical in both files, across both games. Splitting them
as `u16` pairs gives `(127, 270)` and `(127, 90)`. The value 127 is a natural full-scale volume on
a 0..127 mixer, and 270 / 90 look like angles in degrees — which would be a stereo pan pair for a
two-channel stream. **This is a hypothesis and nothing in the code depends on it.** The same
values 270 and 90 also appear in the 24-byte stream-descriptor records inside `SBK_Title01_GOW1`,
which is suggestive but not proof. Confidence: **LOW**. The fields are preserved verbatim on
rebuild.

## Body layout

Fixed 0x2000-byte blocks, round-robin across channels:

```
block 0 -> channel 0
block 1 -> channel 1
block 2 -> channel 0
block 3 -> channel 1
...
```

Both files contain a whole number of complete interleave groups. Each channel's stream is ordinary
PS-ADPCM (see `VAG_FORMAT.md`) with no per-channel header.

## Loop flags

Every frame flag byte in both files is **0** — 121,856 frames in the GoW1 file and 548,864 in the
GoW2 file, all zero. VPK streams carry no loop markers, no loop start/end and no terminator frame.
Confidence: **CONFIRMED** (exhaustive census, not a sample).

## Alignment

| Rule | Value | Confidence |
|---|---|---|
| Data start | 0x800 | CONFIRMED |
| Interleave block | 0x2000 | CONFIRMED |
| Body total | whole multiple of `Interleave × Channels` | CONFIRMED |
| BytesPerChannel | multiple of 16 | CONFIRMED |

## Rebuild

`VpkFile.Build` re-interleaves the per-channel streams and writes the 0x800 header. On
`ReplaceAudio` the tool resamples to the header's rate, encodes each channel independently,
sets `BytesPerChannel` to the true encoded length, then pads each channel up to a whole
interleave block so the body stays a whole number of groups.

Round-trip results:

```
ATH5C5_2_GOW1_PS2.VPK   1,951,744 -> 1,951,744 bytes, 0 differing   PASS
RH5006C_GOW2_PS2.VPK    8,783,872 -> 8,783,872 bytes, 0 differing   PASS
```

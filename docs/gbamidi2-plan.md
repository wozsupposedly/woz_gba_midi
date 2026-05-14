# GBAMIDI2 Implementation Plan

## Immediate Direction

GBAMIDI2 uses the linked tangrs/usb-gba-multiboot second-stage loader design.
The BIOS multiboot image remains a small stage1 loader. That loader relocates
its receive loop to IWRAM, receives a larger stage2 runtime into EWRAM at
`0x02000000`, verifies a checksum, and jumps to the stage2 runtime.

The RP2040 first sends `gbamidi/stage1/loader.gba` with the existing encrypted
BIOS multiboot handshake. It then streams `gbamidi/gbacode/gbamidi.gba` as
stage2 using the tangrs 32-bit SIO normal-mode protocol:

1. exchange magic `0xfa57b007`;
2. send 32-bit word count;
3. send stage2 words, little-endian in memory and shifted MSB-first on the wire;
4. exchange additive checksum;
5. stage1 jumps to `0x02000000`.

This requires the GBA `SO` line wired to RP2040 `GPIO4`. The current GBAMIDI2
stage profile uses `SD -> GPIO5` and `SO -> GPIO4`; the older GBA-cable stage
profile is deprecated for now. The old three-wire SpritesMods MIDI path is not
enough for the linked normal-mode second-stage protocol.

## Channel Map Target

- Channel 1: new sample-instrument page, 8-voice polyphonic.
- Channel 2: current direct-sound poly engine.
- Channel 4: current channel 1 pulse/duo behavior.
- Channel 5: current channel 2 sample behavior.
- Channel 6: new 8-voice FM electric piano engine.
- Channel 10: current noise behavior unless reassigned.

All channel numbers should become settings rather than hardcoded routing.

## Sound Engine Direction

Do not embed copyrighted commercial game samples in this repository. The engine
can support user-supplied banks named after games or patches, but committed test
banks should be original or permissively licensed.

For the game-style sample bank, prefer an asset pipeline that accepts small
mono 8-bit looped samples plus metadata:

- bank name
- instrument name
- root MIDI note
- loop start/end
- tuning
- envelope defaults

Sappy is useful as a reference and as an import target for user-provided assets,
but not as the runtime engine. A full Sappy player is song/event oriented, while
GBAMIDI2 needs low-latency live MIDI instruments. Maxmod is a stronger standard
engine for module/song playback, but it owns Timer 0, DMA 1/2, VBlank, and
Direct Sound registers. That conflicts with the current custom live direct-sound
mixer unless GBAMIDI2 migrates entirely to Maxmod.

The practical first implementation is to extend the current direct-sound mixer:

- add a generic 8-voice sample instrument allocator;
- add a compact built-in placeholder bank;
- later add an offline importer for user-supplied Sappy/SF2-derived samples.

## FM Electric Piano

Channel 6 should use a lightweight fixed-point FM voice:

- 8 active voices;
- carrier plus one modulator;
- infinite sustain while note is held;
- release only on note-off;
- one "algo/tone" parameter mapped sharp-to-mellow by changing modulation index,
  operator ratio, and simple low-pass/damping behavior.

The existing direct-sound interrupt/mixer path is the right place to prototype
this, because FM voice mixing and sample-instrument mixing can share the same
output buffers.

## Persistent Settings Protocol

The current RP2040 path sends MIDI bytes to the GBA in the low byte of a 16-bit
link word. Persistent settings need a small bidirectional control protocol.

Recommended staging:

1. Add GBA-to-RP2040 control messages using reserved non-MIDI link words.
2. Add RP2040 nonvolatile settings storage.
3. On boot, RP2040 sends a SysEx settings dump to the GBA runtime.
4. GBA UI edits send a settings-save SysEx/control packet back to the RP2040.
5. RP2040 stores the latest settings and replays them after the next ROM upload.

Use SysEx-compatible payloads on the USB/DIN side, but keep the GBA link layer
framed independently so it cannot be confused with normal MIDI data.

## Staged Milestones

1. Header text and build pipeline: done.
2. Two-stage loader using tangrs normal-mode protocol: initial implementation done.
3. Hardware test of stage1 plus stage2 with `SD -> GPIO5` and `SO -> GPIO4`.
4. Channel routing table and UI labels.
5. Bidirectional link/control test: GBA sends one settings packet; RP2040 logs
   or echoes it.
6. RP2040 settings persistence and boot restore.
7. Channel 6 FM EP prototype with 8 voices.
8. Channel 1 sample-instrument prototype with original placeholder samples.
9. User asset-bank importer for permissive or user-supplied sample banks.

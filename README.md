# Woz GBA MIDI

RP2040/Arduino sketch for uploading an embedded GBA MIDI multiboot ROM, then forwarding USB/DIN MIDI to the running GBA program.

## Main Sketch

- `GBA_Midi_RP2040_Woz.txt` is the current working RP2040 sketch.
- It uploads the embedded GBA MIDI ROM over the GBA link port, then switches to MIDI forwarding after the ROM upload succeeds.
- Current working cable profiles:
  - Profile A: `SC=GPIO2`, `SI=GPIO3`, `SD=GPIO4`
  - Profile B: `SC=GPIO2`, `SI=GPIO3`, `SD=GPIO5`
- The sketch tries Profile A first, then Profile B, and only enters MIDI mode after a full ROM upload succeeds.

## Other Files

Backup and diagnostic sketches are preserved as `GBA_Midi_RP2040_*` files.

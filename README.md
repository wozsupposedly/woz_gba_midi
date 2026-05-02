# Woz GBA MIDI

RP2040/Arduino sketch for uploading an embedded GBA MIDI multiboot ROM, then forwarding USB/DIN MIDI to the running GBA program.

This project is based on and adapted from the SpritesMods GBA MIDI project by Jeroen Domburg / Sprite_tm:

https://spritesmods.com/?art=gbamidi

## License

This repository is licensed under Creative Commons Attribution 2.0 Generic (`CC-BY-2.0`). See `LICENSE.md`.

Please preserve attribution to SpritesMods for the original GBA MIDI concept, protocol work, and ROM behavior this adaptation builds on.

## Main Sketch

- `GBA_Midi_RP2040_Woz.txt` is the current working RP2040 sketch.
- It uploads the embedded GBA MIDI ROM over the GBA link port, then switches to MIDI forwarding after the ROM upload succeeds.
- Current working cable profiles:
  - Profile A: `SC=GPIO2`, `SI=GPIO3`, `SD=GPIO4`
  - Profile B: `SC=GPIO2`, `SI=GPIO3`, `SD=GPIO5`
- The sketch tries Profile A first, then Profile B, and only enters MIDI mode after a full ROM upload succeeds.

## Other Files

Backup and diagnostic sketches are preserved as `GBA_Midi_RP2040_*` files.

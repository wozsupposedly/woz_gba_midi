# Woz GBA MIDI

RP2040/Arduino sketch for uploading an embedded GBA MIDI multiboot ROM, then forwarding USB device MIDI, USB host MIDI, and DIN MIDI to the running GBA program.

This project is based on and adapted from the SpritesMods GBA MIDI project by Jeroen Domburg / Sprite_tm:

https://spritesmods.com/?art=gbamidi

## License

This repository is licensed under Creative Commons Attribution 2.0 Generic (`CC-BY-2.0`). See `LICENSE.md`.

Please preserve attribution to SpritesMods for the original GBA MIDI concept, protocol work, and ROM behavior this adaptation builds on.

## Main Sketch

- `GBA_Midi_RP2040_Woz.ino` is the current working RP2040 sketch.
- It uploads a small embedded GBA stage1 loader over the GBA link port, then streams the larger GBAMIDI2 runtime as stage2 before switching to MIDI forwarding.
- Built-in USB enumerates as a USB MIDI device.
- GPIO14/GPIO15 PIO USB MIDI host code is present but disabled by default because starting `USBHost.begin()` currently stops built-in USB-device MIDI after a few seconds.
- Current GBAMIDI2 two-stage cable profile:
  - `SC=GPIO2`, `SI=GPIO3`, `SD=GPIO5`, `SO=GPIO4`
- The older GBA-cable stage profile is deprecated for now; the sketch only tries the GBAMIDI2 profile and only enters MIDI mode after the stage1 and stage2 uploads succeed.

## USB Host Wiring

- `GPIO14` = USB host `D+` through a `22 ohm` series resistor.
- `GPIO15` = USB host `D-` through a `22 ohm` series resistor.
- Provide external `5V VBUS` to the hosted USB connector.
- Add `15k` pulldown from `D+` to ground and another `15k` pulldown from `D-` to ground.
- Use Arduino-Pico with `USB Stack: Adafruit TinyUSB` and `CPU Speed: 240 MHz (Overclock)`.

## Other Files

Backup and diagnostic sketches are preserved as `GBA_Midi_RP2040_*` files.

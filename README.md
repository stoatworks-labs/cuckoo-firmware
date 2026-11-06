# Cuckoo firmware

Release builds of the firmware for [Cuckoo](https://stoatworks-labs.com/hardware/video/#cuckoo), the HDMI EDID
box. There are downloads here and nothing else, because the source isn't public.

Cuckoo has two processors. Its ESP32-C3 runs the Wi-Fi access point, the web page and the console, and
carries the RP2354A's firmware in two of its partitions. From the console, it puts that firmware on the
RP2354A (`rp flash`) or loads it straight into its RAM (`rp uartboot`). So flashing the ESP32 is all
you do from here.

> **Only half proven.** The ESP32-C3 image has run on one board. The RP2354A images it carries haven't run
> yet.

## Variants

| Variant | What the RP2354A gets |
|---|---|
| **Bring-up** (recommended) | Its bring-up checks for `rp flash`, and its flasher for `rp uartboot`. This is what board 1 runs. |
| DDC bridge | The DDC bridge for `rp flash` instead of the bring-up checks. Never run on a board. |

The ESP32 image is the same in both.

## Flashing it

The easy way is **[Stoatworks Flasher](https://flasher.stoatworks-labs.com/?device=cuckoo)**, in desktop
Chrome or Edge, or [Burrow](https://stoatworks-labs.com/burrow/). Either one downloads the release, checks
every part against its SHA-256, and writes only the parts, so Cuckoo keeps its Wi-Fi password.

Plug Cuckoo's USB-C into the computer, which powers it too, and pick *USB JTAG/serial debug unit*
(Espressif, 303a:1001). If it won't connect: unplug it, hold TP7 (ESP_BOOT, GPIO9) to ground, plug it back
in, and let go.

Its access point, Cuckoo-XXXX, comes up a few seconds after it boots. Type `wifi` in the serial console to
see its password.

### By hand

Every release carries a whole-flash image per variant, `cuckoo-firmware-<release>-<variant>.bin`, for
writing at 0x0:

```bash
esptool --chip esp32c3 write_flash 0x0 cuckoo-firmware-main-4300e74-bringup.bin
```

esptool writes every byte of it, the blank settings partition included, so Cuckoo **forgets its Wi-Fi
password** and makes a new one.

The release zip, `cuckoo-firmware-<release>.zip`, is the manifest and the parts one by one. The flasher
(under *Your own file*) and Burrow both read it, so you can keep a copy and flash offline.

## Releases

| Tag | Firmware | Date | |
|---|---|---|---|
| [v0.1.0](https://github.com/stoatworks-labs/cuckoo-firmware/releases/tag/v0.1.0) | main-4300e74 | 2026-11-06 | The first public build. ESP-IDF 5.5.5, Pico SDK 2.3.1. |

## Licence

The images are free to download, flash and pass on unmodified: see [LICENSE](LICENSE). They contain other
people's work under their own licences, listed in [THIRD-PARTY-NOTICES.md](THIRD-PARTY-NOTICES.md).

There's no warranty. If it misbehaves, open an issue here and say which release and variant.

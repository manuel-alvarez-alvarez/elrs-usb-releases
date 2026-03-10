# ELRS USB - Firmware Releases

Pre-built firmware binaries for [ELRS USB](https://manuel-alvarez-alvarez.github.io/elrs-usb/), an ESP32-S3 firmware that bridges an ExpressLRS receiver to a USB HID joystick. Plug in an ELRS RX, connect USB, and it shows up as a native gamepad — no drivers needed.

## Download

Go to [Releases](https://github.com/manuel-alvarez-alvarez/elrs-usb-releases/releases) and download the latest version. Each release includes:

| File | Description |
|------|-------------|
| `elrs_usb-vX.Y.Z.bin` | Main firmware binary |
| `bootloader-vX.Y.Z.bin` | Bootloader binary |
| `partition-table-vX.Y.Z.bin` | Partition table binary |
| `ota_data_initial-vX.Y.Z.bin` | OTA data partition (initial state) |
| `flasher_args.json` | Flash addresses and arguments |
| `*.sha256` / `*.sha512` | Checksum files for verification |

## Flashing

### First-time flash (USB)

Requires [esptool](https://github.com/espressif/esptool):

```bash
esptool.py --chip esp32s3 --port /dev/ttyACM0 \
  write_flash \
  0x0      bootloader-vX.Y.Z.bin \
  0x8000   partition-table-vX.Y.Z.bin \
  0x9000   ota_data_initial-vX.Y.Z.bin \
  0x20000  elrs_usb-vX.Y.Z.bin
```

### OTA update (WiFi)

Once the firmware is running, connect to the **ELRS-USB-Config** WiFi AP and open `http://10.0.0.1`. Use the Firmware Update page to upload the new `elrs_usb-vX.Y.Z.bin`.

## Verify checksums

```bash
sha256sum -c elrs_usb-vX.Y.Z.bin.sha256
sha512sum -c elrs_usb-vX.Y.Z.bin.sha512
```

## Hardware

Any ESP32-S3 board with a USB-OTG port. The internal USB PHY is used (no external PHY needed).

| Signal | Default GPIO |
|--------|-------------|
| UART RX (from ELRS TX) | GPIO 2 |
| UART TX (to ELRS RX) | GPIO 1 |

## Features

- **16-channel CRSF decoder** at 420 kbaud with CRC validation
- **USB HID joystick** with dynamic descriptor — up to 8 axes + 16 buttons at 1000 Hz
- **Named radio profiles** stored in NVS flash (up to 10 configs)
- **WiFi configuration portal** — AP mode with captive portal at `10.0.0.1`
- **OTA firmware updates** with dual partitions and automatic rollback
- **WS2812 status LED** with configurable effects

## Libraries

This project relies on two ESP-IDF components:

- **[esp-crsf-rx](https://github.com/manuel-alvarez-alvarez/esp-crsf-rx)** — CRSF protocol receiver for decoding ELRS channel data over UART
- **[esp-hid-gamepad](https://github.com/manuel-alvarez-alvarez/esp-hid-gamepad)** — USB HID gamepad with runtime-configurable axes, buttons, and switches via TinyUSB

## Performance

Tested with [GamepadLA](https://gamepadla.com/soft.pdl) at 1000 Hz polling rate:

| Metric | Value |
|--------|-------|
| Average polling rate | 1000.85 Hz |
| Median polling rate | 999.00 Hz |
| Min interval | 0.96 ms |
| Median interval | 1.00 ms |
| Average interval | 1.00 ms |
| Max interval | 4.00 ms |
| Jitter (StdDev) | 0.47 ms |

## Links

- [Project page](https://manuel-alvarez-alvarez.github.io/elrs-usb/)
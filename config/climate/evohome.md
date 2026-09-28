# Honeywell EvoHome

## TLDR

Use `ramses_esp`, which can run on a standalone device connected to the wireless network and integrates easily with `ramses_rf` via MQTT. For hardware, take the DIY route with an `ESP32-S3-WROOM-1 N16R8` development board and a `CC1101` module. I'm running both `ramses_cc` and `evoGateway` in parallel for testing, but leaning towards `ramses_cc` due to its tighter integration with HASS.

## Introduction

The heating system in my house uses Honeywell EvoHome components:

- R8810A: OpenTherm Bridge
- HR92: Valves
- ATC928: Controller
- RFG100: Internet Gateway

It is integrated into Home Assistant via the official [Honeywell Total Connect Comfort (Europe)](https://www.home-assistant.io/integrations/evohome/) integration. The valves are locked down so that the temperature can only be set remotely. The thermostats created by the EvoHome integration are wrapped in the [Better Thermostat](https://better-thermostat.org/) integration. This adds support for external temperature sensors to compensate for unreliable temperature readings from the HR92s, as well as integration with window sensors. Schedules are no longer set from the Honeywell controller, but rather via the [Scheduler Card](https://github.com/nielsfaber/scheduler-card) in HASS. The integration is very stable and reliable despite its legacy status, but it requires an internet connection and access to Honeywell's cloud service.

## Ramses RF

Enter **[Ramses RF](https://github.com/ramses-rf/ramses_rf)**, a Python library and CLI utility that supports central heating (CH) and domestic hot water (DHW) systems using Honeywell's proprietary Ramses-II RF (868 MHz) protocol. This library is used in the [Ramses CC](https://github.com/ramses-rf/ramses_cc) integration available via HACS and the standalone [evoGateway](https://github.com/smar000/evoGateway) app that integrates with MQTT.

However, it requires an RF interface: either a Honeywell HGI80 (rare and expensive) or a USB/MQTT dongle running specialized firmware that can work with Ramses-II packets.

### Firmware Options

- [evofw3](https://github.com/ghoti57/evofw3): the original firmware for decoding the Ramses-II protocol; only works via a serial connection
- [ramses_esp](https://github.com/PWhite-Eng/ramses_esp_eth): a modern port of `evofw3` with a network stack (Wi-Fi only) that can publish Ramses-II packets to an MQTT topic
- [ramses_esp_eth](https://github.com/PWhite-Eng/ramses_esp_eth): a port of `ramses_esp` that allegedly has full feature parity with the original and adds support for a wired connection. This opens up the tempting option of using PoE, but it looks like a one-person project (at least for now)

### Hardware Options

#### Pre-Assembled

- [Ramses_ESP](https://indalotech.co.uk/products/ramses_esp) - a ready-to-use device flashed with `ramses_esp`, produced by the creator of the firmware
- [nanoCUL 868](https://schlauhaus.biz/en/product-2/nanocul-868/) - another pre-assembled, ready-to-use device that uses `evofw3`

#### DIY

> ℹ️ The number of options is virtually limitless. However, the following boards and configurations are recommended by the firmware authors themselves.

- [ESP32-S3-WROOM-1 N16R8](https://nl.aliexpress.com/w/wholesale-ESP32%2525252dS3WROOM1-N16R8.html) - supports `ramses_esp`, but requires a few GPIO customizations during the build
- [Waveshare ESP32-S3](https://www.waveshare.com/esp32-s3-eth.htm) - supported by `ramses_esp_eth` and can be bought with an additional PoE module
- [Lilygo T-ETH Elite](https://lilygo.cc/products/t-eth-elite-1) - should also theoretically work with `ramses_esp_eth`, but this is unconfirmed

For the radio, use any CC1100 or [CC1101](https://nl.aliexpress.com/w/wholesale-cc1101-868-mhz.html) module that supports 868 MHz.

## Appendix 1: Building `ramses_esp` for ESP32-S3-WROOM-1 N16R8

### Versions Used

```text
ramses_esp: v0.6.6d
ESP-IDF: v5.2.1
Python: 3.13.x
Target: esp32s3
```

### ESP-IDF

```bash
# Clone ESP-IDF
mkdir -p ~/esp
cd ~/esp

git clone -b v5.2.1 --recursive https://github.com/espressif/esp-idf.git

cd ~/esp/esp-idf
./install.sh esp32s3
```

I had problems with the automatically created Python environment, so I ended up using a fresh Python 3.13 ESP-IDF environment.

```bash
# Set the environment explicitly
export IDF_PATH="$HOME/esp/esp-idf"
export IDF_PYTHON_ENV_PATH="$HOME/.espressif/python_env/idf5.2_py3.13_env"
export PATH="$IDF_PYTHON_ENV_PATH/bin:$IDF_PATH/tools:$PATH"

hash -r

# Verify
python --version
python "$IDF_PATH/tools/idf.py" --version
```

Use this form for all subsequent commands, instead of plain `idf.py`, to avoid `pyenv` or another Python installation taking precedence.

```bash
python "$IDF_PATH/tools/idf.py"
```

### ramses_esp: Setup

```bash
cd ~/esp
git clone https://github.com/IndaloTech/ramses_esp.git
cd ~/esp/ramses_esp
git checkout v0.6.6d

# Clean any previous configuration
python "$IDF_PATH/tools/idf.py" fullclean
# Set target
python "$IDF_PATH/tools/idf.py" set-target esp32s3
# Open configuration
python "$IDF_PATH/tools/idf.py" menuconfig
```

> ℹ️ The following configuration options need to be changed for these reasons:
>
> - There is a known problem that can cause the board to get stuck in a boot loop.
> - The board uses the default GPIO pins (35-37) for other purposes, so a different set is required.
> - The configuration must match the N16R8 module: N16 = 16 MB flash; R8 = 8 MB PSRAM.

```text
Serial flasher config
    Flash SPI mode -> DIO
    Flash SPI speed -> 80 MHz
    Flash size -> 16 MB
Component config
    ESP PSRAM
        Mode (QUAD/OCT) of SPI RAM chip in use -> Octal Mode PSRAM
        Type of SPIRAM chip in use -> Auto-detect
        Set RAM clock speed -> 80MHz clock speed
        [*] Initialize SPI RAM during startup
        SPI RAM access method -> Make RAM allocatable using malloc() as well
        [*] Run memory test on SPI RAM initialization
CC1101 ESP32-S3
    CSN  -> GPIO10
    MOSI -> GPIO11
    SCK  -> GPIO12
    MISO -> GPIO13
    GDO0 -> GPIO14
    GDO2 -> GPIO21
    VCC  -> 3V3
    GND  -> GND
```

Save `menuconfig` and exit.

```bash
# Build bootloader, partition table and ramses_esp
python "$IDF_PATH/tools/idf.py" fullclean
python "$IDF_PATH/tools/idf.py" build

# Confirm device identifier, e.g. /dev/cu.usbmodem14101
ls /dev/cu.usb*
# Flash everything
python "$IDF_PATH/tools/idf.py" -p /dev/cu.usbmodem14101 flash
```

### ramses_esp: Validation

```bash
# Start serial monitor
python "$IDF_PATH/tools/idf.py" -p /dev/cu.usbmodem14101 monitor
```

Make sure that the CC1101 is connected. Otherwise, the firmware waits for the module to initialize and is eventually reset by the watchdog. Therefore, after the firmware is built and flashed, connect the CC1101 using the custom GPIO mapping before expecting normal operation.

With the CC1101 connected correctly, the device immediately starts receiving RAMSES traffic:

```text
I --- 04:183745 --:------ 01:048459 3150 002 0100
I --- 04:066551 --:------ 01:048459 3150 002 0300
RQ --- 30:078410 01:048459 --:------ 0006 001 00
RP --- 01:048459 30:078410 --:------ 0006 004 0005092B
```

### ramses_esp: Configuration

```text
wifi ssid <ssid>
wifi password <password>
wifi restart
mqtt broker mqtt://<broker>:<port>
sntp server pool.ntp.org
timezone CET
```

> ℹ️ I encountered a problem where the Wi-Fi configuration could not be changed after the initial setup, so I had to reset all settings with `nvs erase *`.

## Appendix 2: CC1101 Pinout

```text
        ┌────────── CC1101 ─────────┐
 VCC ───●   ┌──┐      ┌───────┐     │
 GND ───●   └──┘      │       │     │
MOSI ───●             │CC1101 │     ●── GND
SCLK ───●             │       │     │
MISO ───●             └───────┘     ●── ANT
GDO2 ───●                           │
GDO0 ───●            ┌─────┐        ●── GND
 CSN ───●            │XTAL │        │
        └────────────┴─────┴────────┘
```

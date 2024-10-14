# ADLINK MXA-200 IoT Gateway

This is the base Nerves System configuration for the
[ADLINK MXA-200](https://www.adlinktech.com/Products/Industrial_IoT_and_Cloud_solutions/IoTGateway/MXA-200).

| Feature        | Description                                                 |
| -------------- | ----------------------------------------------------------- |
| CPU            | 1.8 GHz quad-core Cortex-A53 (64-bit)                       |
| Storage        | eMMC                                                        |
| Linux kernel   | 5.15                                                        |
| IEx terminal   | UART `ttymxc1`                                              |
| GPIO, I2C, SPI | Yes - [Elixir Circuits](https://github.com/elixir-circuits) |
| Ethernet       | Yes                                                         |
| WiFi           | Yes                                                         |
| Bluetooth      | Yes                                                         |
| RTC            | Yes                                                         |
| HW Watchdog    | Yes                                                         |

### Getting started

Flashing firmware for the first time will be done via NXP's Universal Update
Utility using the Micro-B USB port. A Nerves firmware project using this system
as a target is required. For more information see
[Nerves' Getting Started](https://hexdocs.pm/nerves/getting-started.html#creating-a-project)
guide.

Debug Console is available through the internal 3 pin 2.5mm header labeled as
`CN2102`.

| Pin | Signal |
| --- | ------ |
| 0   | TX     |
| 1   | RX     |
| 2   | GND    |

By default the device is configured to boot off `eMMC`, this can be changed via
the Boot Settings DIP switch labeled as `SW701`. This switch is also used to
place the device in `Recovery` mode for flashing using `uuu` cli.

| 1 | 2 | 3 | 4 | Boot Settings |
| - | - | - | - | ------------- |
| 0 | 1 | 1 | 0 | SD Card       |
| 0 | 1 | 0 | 0 | eMMC          |
| 0 | 0 | 0 | 1 | Recovery      |

#### Prerequisites

- [NXP mfgtools](https://github.com/nxp-imx/mfgtools)
- [fwup](https://github.com/fwup-home/fwup)

#### Building a firmware image

In your Nerves Firmware Project directory, run:
```shell
mix firmware
```

Generate an image file using `fwup`.
```shell
fwup -a \
  -d /tmp/firmware.img \
  -i _build/${MIX_TARGET}_${MIX_ENV}/nerves/images/${YOUR_PROJECT_NAME}.fw \
  -t complete
```

#### Flashing the firmware image

**Note**: `uuu` requires the bootloader to be provided even if it might be
embedded in the full firmware image.

Extract the bootloader from your `.fw` firmware file.
```shell
unzip -d /tmp/ \
  _build/${MIX_TARGET}_${MIX_ENV}/nerves/images/${YOUR_PROJECT_NAME}.fw
```

Set the Boot Settings DIP switch to `Recovery` mode and ensure your devices is
listed when running `uuu -lsusb`.

```shell
uuu \
  -b emmc_all \
  /tmp/data/imx8-boot-sd.bin \
  /tmp/firmware.img
```

Power off the device and toggle Boot Settings DIP switch back to `eMMC` mode.

#### Flashing only U-Boot

Extract the bootloader from your `.fw` firmware file.
```shell
unzip -d /tmp/ \
  _build/${MIX_TARGET}_${MIX_ENV}/nerves/images/${YOUR_PROJECT_NAME}.fw
```

```shell
uuu -b emmc /tmp/data/imx8-boot-sd.bin
```

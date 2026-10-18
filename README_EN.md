<!-- markdownlint-disable MD041 -->

<div align="center">

# 🚁 ESP-Drone · YaoKun Chinese Fork

**Espressif's official ESP32 Wi-Fi drone — controlled from a phone app or gamepad over Wi-Fi**

[![Chipset](https://img.shields.io/badge/Chipset-ESP32%20%7C%20ESP32--S2%20%7C%20ESP32--S3-red)](https://www.espressif.com/)
[![Framework](https://img.shields.io/badge/Framework-ESP--IDF%20v5.0-orange)](https://docs.espressif.com/projects/esp-idf/)
[![Core](https://img.shields.io/badge/Core-Port%20of%20Crazyflie-blue)](https://www.bitcraze.io/crazyflie/)
[![License](https://img.shields.io/badge/License-GPLv3-blue.svg)](LICENSE)
[![Build](https://github.com/espressif/esp-drone/actions/workflows/build_project.yml/badge.svg)](https://github.com/espressif/esp-drone/actions/workflows/build_project.yml)

[**简体中文**](README.md) · **English**

</div>

---

> 📌 **This repository is a Chinese-documented fork of [espressif/ESP-Drone](https://github.com/espressif/ESP-Drone).**
> The firmware source is kept **100% identical to upstream** (only the directory layout changed,
> not a single line of logic). What this fork adds: expanded Chinese documentation and a
> reorganized directory structure.
> Upstream is an **official Espressif** project, copyright Espressif Systems, under **GPL-3.0**.

## 📌 What is it

**ESP-Drone** is Espressif's **open-source drone solution** built on ESP32/ESP32-S2/ESP32-S3
Wi-Fi chips.

It features **simple hardware, clear and extensible code architecture**:
the whole aircraft talks over **Wi-Fi** and is controlled from a phone app or a gamepad —
**no dedicated transmitter required**, and no separate ground-station software.

The key point: its **flight-control kernel is ported from Bitcraze's open-source
[Crazyflie](https://github.com/bitcraze/bitcraze-firmware) project** — meaning you get a
proven, mature flight-control implementation rather than a toy written from scratch.

> **Development environment**: use the ESP-IDF [release/v5.0](https://docs.espressif.com/projects/esp-idf/en/release-v5.0/esp32s2/get-started/index.html) branch.
> **Supported chips**: ESP32, ESP32-S2, ESP32-S3.

<img src="Docs/_static/espdrone_s2_v1_2_2.png" width="500" alt="ESP-Drone">

## ✨ Features

| Feature | Description |
|---------|-------------|
| **Stabilize mode** | Attitude stabilization — levels out when you release the sticks |
| **Height-hold mode** ¹ | Height hold using barometer / rangefinder |
| **Position-hold mode** ¹ | Advanced flight modes from the Crazyflie ecosystem |
| **App control** | Official iOS / Android apps over Wi-Fi |
| **Gamepad control** | ESP-BOX3 joystick support (over ESP-NOW) |
| **cfclient ground station** | Works with [crazyflie-clients-python](https://github.com/leeebo/crazyflie-clients-python) |
| **Deck expansion** | Mainboard + deck architecture — stack optical flow, rangefinder and more |

> ¹ **Height-hold and position-hold require an expansion deck** — see the
> [hardware reference](Docs/en/rst/hardware.rst). Stabilize mode flies with the
> onboard sensors alone.

## 📁 Directory layout (reorganized in this fork)

> 🔧 **Why reorganize**: upstream keeps the ESP-IDF project, hardware design files and Sphinx
> docs flat in the repo root. This fork moves **everything firmware-related under `Firmware/`**,
> containing a **complete ESP-IDF project** (`CMakeLists.txt` sits next to `main/` and
> `components/`, with `sdkconfig.defaults*` applied in place), so **`idf.py build` works
> immediately after `cd Firmware/` — zero configuration changes**.

```text
ESP-Drone/                     # ← repo root: docs + license
├── README.md                  # Chinese readme (default)
├── README_EN.md               # This file
├── LICENSE                    # GPL-3.0 License (Espressif)
├── Firmware/                  # ★ Firmware project (complete ESP-IDF project)
│   ├── CMakeLists.txt         #   ★ IDF entry point (component search paths)
│   ├── main/                  #   Application (main.c, Kconfig, linker fragment)
│   ├── components/            #   Components
│   │   ├── core/              #     ★ Flight-control kernel
│   │   │   └── crazyflie/     #       Ported from Crazyflie (control, state machine, comms)
│   │   ├── drivers/           #     Hardware drivers
│   │   │   ├── deck/          #       Deck driver framework
│   │   │   ├── general/       #       General drivers
│   │   │   ├── i2c_bus/       #       I2C bus
│   │   │   ├── i2c_devices/   #       I2C devices (IMU, barometer, compass…)
│   │   │   └── spi_devices/   #       SPI devices
│   │   ├── lib/               #     Third-party libraries
│   │   │   └── dsp_lib/       #       ESP-DSP (port of STM32 DSP library)
│   │   ├── platform/          #     Platform abstraction (platform_esp32.c etc.)
│   │   └── config/            #     Configuration (Kconfig)
│   ├── sdkconfig.defaults         # Default config
│   ├── sdkconfig.defaults.esp32s2  # ESP32-S2 defaults
│   └── sdkconfig.defaults.esp32s3  # ESP32-S3 defaults
├── Docs/                      # ★ Documentation (Sphinx, EN + ZH)
│   ├── zh_CN/rst/             #   Chinese sources
│   ├── en/rst/                #   English sources
│   ├── _static/               #   Images
│   ├── conf_common.py         #   Shared Sphinx config
│   └── UPSTREAM_README.md         # Archived upstream English README
│       UPSTREAM_README_CN.md      # Archived upstream Chinese README
├── Hardware/                  # ★ Hardware design files (not firmware, hence separate)
│   ├── ESP32_S2_Drone_V1_2/       #   V1.2 mainboard (schematic + PCB)
│   ├── ESP32_S2_Drone_Flow_Deck/   #   Optical-flow deck
│   ├── ESPlane_FC_V1/              #   ESPlane FC V1
│   ├── ESPlane_V2_S2/              #   ESPlane V2 (S2)
│   └── datasheet/                  #   Component datasheets (PMW3901 / MPU6050 / VL53L1X …)
└── .github/workflows/         # CI: multi-IDF-version build + pre-commit checks
```

**Before / after:**

| Original path | New path | Why |
|---------------|----------|-----|
| `CMakeLists.txt`, `main/`, `components/`, `sdkconfig.defaults*` | All under `Firmware/` | Merged into **one complete ESP-IDF project** — internal relative paths unchanged |
| `docs/` | `Docs/` | Documentation |
| `hardware/` | `Hardware/` | Hardware design files (schematics / PCB / datasheets), separated from firmware |
| `README.md` / `README_cn.md` | `Docs/UPSTREAM_README*.md` | Upstream docs archived, not overwritten by the new README |

## 🚀 Quick start

### Environment setup

1. Install **ESP-IDF v5.0** (use the release/v5.0 branch):

   ```bash
   git clone -b release/v5.0 --depth 1 https://github.com/espressif/esp-idf.git
   cd esp-idf && ./install.sh esp32,esp32s2,esp32s3
   . ./export.sh
   ```

2. Install Python dependencies:

   ```bash
   pip install idf-component-manager idf-build-apps
   ```

### Build the firmware

```bash
git clone https://github.com/YaoKunS/ESP-Drone.git
cd ESP-Drone
git checkout yaokun-organized   # ★ switch to the reorganized branch (master mirrors upstream)
cd Firmware                     # ← ESP-IDF project root

idf.py set-target esp32s2    # Choose target chip (esp32 / esp32s2 / esp32s3)
idf.py build                 # Build
idf.py -p /dev/ttyUSB0 flash # Flash
idf.py monitor               # Watch the logs
```

You can also use the batch builder (what upstream CI uses):

```bash
cd Firmware
idf-build-apps find
idf-build-apps build
```

### Flashing

1. Connect the board over USB
2. Hold **BOOT** → tap **EN** → release BOOT (enter download mode)
3. Run `idf.py -p <port> flash`

## 🧩 Hardware reference

| Board | Description |
|-------|-------------|
| **ESP32_S2_Drone_V1_2** | Mainboard with ESP32-S2 module and the sensors needed to fly |
| **ESP32_S2_Drone_Flow_Deck** | Optical-flow deck (enables position hold) |
| **ESPlane_FC_V1** | ESPlane flight controller V1 |
| **ESPlane_V2_S2** | ESPlane V2 (S2 version) |

Schematics and PCB files live in [`Hardware/`](Hardware/); component datasheets in
`Hardware/datasheet/`.

Mainboard specs and pin allocation: [Hardware Reference](Docs/en/rst/hardware.rst).

## 📱 Mobile apps

Espressif publishes full app sources for both platforms:

| Platform | Repository |
|----------|------------|
| iOS | [EspressifApps/ESP-Drone-iOS](https://github.com/EspressifApps/ESP-Drone-iOS) |
| Android | [EspressifApps/ESP-Drone-Android](https://github.com/EspressifApps/ESP-Drone-Android) |

## 📚 Documentation

Full docs (Sphinx sources) are in [`Docs/`](Docs/):

| Language | Directory |
|----------|-----------|
| Chinese | [Docs/zh_CN/rst/](Docs/zh_CN/rst/) |
| English | [Docs/en/rst/](Docs/en/rst/) |

Highlights:

* [Getting started](Docs/en/rst/gettingstarted.rst) — start from zero
* [Hardware reference](Docs/en/rst/hardware.rst) — specs, decks, pin allocation
* [System architecture](Docs/en/rst/system.rst) — software components and data flow
* [Communication](Docs/en/rst/communication.rst) — Wi-Fi / ESP-NOW message formats
* [Driver development](Docs/en/rst/drivers.rst) — how to write a driver for a new sensor
* [ESP-IDF setup](Docs/en/rst/getespidf.rst) — environment and project structure in depth

Online: https://docs.espressif.com/projects/espressif-esp-drone/zh_CN/latest/gettingstarted.html

## 📜 License & credits

* **Upstream project**: ESP-Drone © **Espressif Systems** ·
  source at https://github.com/espressif/ESP-Drone
* **License**: **GPL-3.0** (full text in [LICENSE](LICENSE))
* **This fork**: Chinese documentation and directory reorganization only.
  **No firmware logic was modified.**
* **Fork maintained by**: YaoKunSmart · https://github.com/YaoKunS
* **Bilibili**: https://space.bilibili.com/3707011699050767

### Third-party code

| Component | License | Source | Commit ID |
| :---:  | :---: | :---: |:---: |
| core/crazyflie | GPL-3.0 | [Crazyflie](https://github.com/bitcraze/crazyflie-firmware/tree/2021.01) | tag_2021_01 b448553 |
| lib/dsp_lib | — | [esp32-lin](https://github.com/whyengineer/esp32-lin/tree/master/components/dsp_lib) | 6fa39f4c |

> ⚠️ **GPL-3.0 is a strong copyleft (viral) license**: when distributing this project or
> derivatives, you must keep the same license, retain copyright notices, and provide the
> corresponding source code. Follow all GPL-3.0 terms when using, modifying or redistributing.

### Credits

1. Thanks to **Bitcraze** for the excellent [Crazyflie](https://www.bitcraze.io/) drone project
2. Thanks to **Espressif** for the ESP32 chips and the powerful [ESP-IDF framework](https://docs.espressif.com/projects/esp-idf/en/latest/esp32s2/get-started/index.html)
3. Thanks to **WhyEngineer** for the ESP-DSP port of the STM32 DSP library ([esp-dsp](https://github.com/whyengineer/esp32-lin/tree/master/components/dsp_lib))

## 📋 Support policy

Since December 2022, Espressif offers **limited support** for this project — but pull
requests are still welcome!

## ⚠️ Disclaimer

This project is provided "as is", without warranty of any kind. Flying a drone carries
**personal and property risks**:

1. Test in an **open area**, keeping people and buildings at a safe distance
2. Check that motors and propellers are firmly mounted, and prop rotation matches the motor
3. Comply with local drone regulations (registration, no-fly zones, altitude limits)

⚠️ Neither the author nor this fork is responsible for any damage, injury or loss resulting from
use of this project. **Use at your own risk!**

---

<div align="center">

**🚁 ESP-Drone** — an official [Espressif](https://github.com/espressif) project · GPL-3.0 License
· Chinese fork maintained by [YaoKunSmart](https://github.com/YaoKunS)

</div>
<div align="center">

# 🚁 ESP-Drone · 耀坤复刻版

**乐鑫官方 ESP32 Wi-Fi 无人机 —— 可用手机 APP 或手柄通过 Wi-Fi 操控**

![Chipset](https://img.shields.io/badge/Chipset-ESP32%20%7C%20ESP32--S2%20%7C%20ESP32--S3-red)

![Framework](https://img.shields.io/badge/Framework-ESP--IDF%20v5.0-orange)

![Core](https://img.shields.io/badge/Core-Port%20of%20Crazyflie-blue)

![License](https://img.shields.io/badge/License-GPLv3-blue.svg)

![Build](https://github.com/espressif/esp-drone/actions/workflows/build_project.yml/badge.svg)

**简体中文** · [**English**](README_EN.md)

</div>

---

> 📌 **本仓库是 [espressif/ESP-Drone](https://github.com/espressif/ESP-Drone) 的中文复刻整理版。**  
> 固件源码 100% 保持上游原样（仅调整了目录位置，未改一行代码逻辑），  
> 本仓库额外提供：更完整的中文说明、重新规划的目录结构。  
> 上游为**乐鑫官方（Espressif）**&#x9879;目，版权归乐鑫所有，采用 **GPL-3.0** 协议。

## 📌 这是什么

**ESP-Drone** 是乐鑫基于 ESP32/ESP32-S2/ESP32-S3 Wi-Fi 芯片推出的**开源无人机方案**。

它的特点是**硬件简单、代码清晰、可扩展**：  
整机通过 **Wi-Fi** 通信，用手机 APP 或游戏手柄就能操控 ——  
**不需要专用遥控器**，也不需要额外的地面站软件。

最关键的一点：它的**飞控内核移植自 Bitcraze 的 [Crazyflie](https://github.com/bitcraze/crazyflie-firmware) 开源工程**，  
也就是说，你拿到的是一套经过验证的成熟飞控算法，而不是从零起步的玩具代码。

> **开发环境**：请使用 ESP-IDF [release/v5.0](https://docs.espressif.com/projects/esp-idf/en/release-v5.0/esp32s2/get-started/index.html) 分支。  
> **已支持芯片**：ESP32、ESP32-S2、ESP32-S3。

![ESP-Drone](Docs/_static/espdrone_s2_v1_2_2.png)

## ✨ 功能特性

| 功能               | 说明                                                                                |
| ---------------- | --------------------------------------------------------------------------------- |
| **自稳定模式**        | 姿态自稳，松杆自动回平                                                                       |
| **定高模式** ¹       | 配合气压计/测距传感器实现高度保持                                                                 |
| **定点模式** ¹       | 飞控 Crazyflie 生态的进阶飞行模式                                                            |
| **APP 控制**       | 官方 iOS / Android APP，手机 Wi-Fi 直连操控                                                |
| **手柄控制**         | 支持 ESP-BOX3 摇杆（通过 ESP-NOW）                                                        |
| **cfclient 上位机** | 适配 [crazyflie-clients-python](https://github.com/leeebo/crazyflie-clients-python) |
| **扩展板支持**        | 主板 + 扩展板架构，可堆叠光流、测距等模块                                                            |

> ¹ **定高与定点模式需要外接扩展板**，详见[硬件参考](Docs/zh_CN/rst/hardware.rst)。  
> 自稳定模式使用主板自带传感器即可飞行。

## 📁 目录结构（本复刻版重新整理）

> 🔧 **为什么要重整**：上游把 ESP-IDF 工程、硬件设计文件、Sphinx 文档平铺在根目录。  
> 本复刻版把**固件相关的一律收进 `Firmware/`**，其中包含**完整的 ESP-IDF 工程**  
> （`CMakeLists.txt` 与 `main/`、`components/` 同级，`sdkconfig.defaults*` 就地生效），  
> 因此**进入 `Firmware/` 后 `idf.py build` 即可直接编译，零配置改动**。

```text
ESP-Drone/                     # ← 仓库根：说明文档 + 许可证
├── README.md                  # 本文件（中文）
├── README_EN.md               # 英文版说明
├── LICENSE                    # GPL-3.0 许可（乐鑫）
├── Firmware/                  # ★ 固件工程（完整 ESP-IDF 项目）
│   ├── CMakeLists.txt         #   ★ IDF 工程入口（声明组件搜索路径）
│   ├── main/                  #   应用主程序（main.c、Kconfig、链接片段）
│   ├── components/            #   组件
│   │   ├── core/              #     ★ 飞控内核
│   │   │   └── crazyflie/     #       移植自 Crazyflie（控制律、状态机、通信）
│   │   ├── drivers/           #     硬件驱动
│   │   │   ├── deck/          #       扩展板驱动框架
│   │   │   ├── general/       #       通用驱动
│   │   │   ├── i2c_bus/       #       I2C 总线
│   │   │   ├── i2c_devices/   #       I2C 设备（IMU、气压计、罗盘…）
│   │   │   └── spi_devices/   #       SPI 设备
│   │   ├── lib/               #     第三方库
│   │   │   └── dsp_lib/       #       ESP-DSP（STM32 DSP 库移植）
│   │   ├── platform/          #     平台抽象（platform_esp32.c 等）
│   │   └── config/            #     配置（Kconfig）
│   ├── sdkconfig.defaults         # 默认配置
│   ├── sdkconfig.defaults.esp32s2  # ESP32-S2 默认配置
│   └── sdkconfig.defaults.esp32s3  # ESP32-S3 默认配置
├── Docs/                      # ★ 文档（Sphinx，中英双语）
│   ├── zh_CN/rst/             #   中文文档源
│   ├── en/rst/                #   英文文档源
│   ├── _static/               #   文档图片资源
│   ├── conf_common.py         #   Sphinx 公共配置
│   └── UPSTREAM_README.md         # 上游官方英文 README（存档）
│       UPSTREAM_README_CN.md      # 上游官方中文 README（存档）
├── Hardware/                  # ★ 硬件设计文件（不属于固件，故独立成目录）
│   ├── ESP32_S2_Drone_V1_2/       #   V1.2 主板（原理图 + PCB）
│   ├── ESP32_S2_Drone_Flow_Deck/   #   光流扩展板
│   ├── ESPlane_FC_V1/              #   ESPlane FC V1
│   ├── ESPlane_V2_S2/              #   ESPlane V2 S2
│   └── datasheet/                  #   器件数据手册（PMW3901 / MPU6050 / VL53L1X 等）
└── .github/workflows/         # CI：多 IDF 版本构建 + pre-commit 检查
```

**整理前后对照：**

| 原始路径                                                         | 整理后路径                      | 理由                               |
| ------------------------------------------------------------ | -------------------------- | -------------------------------- |
| `CMakeLists.txt`、`main/`、`components/`、`sdkconfig.defaults*` | `Firmware/` 下同级            | 合并为**一个完整 ESP-IDF 工程**，内部相对路径零改动 |
| `docs/`                                                      | `Docs/`                    | 文档                               |
| `hardware/`                                                  | `Hardware/`                | 硬件设计文件（原理图/PCB/数据手册），与固件分离       |
| `README.md` / `README_cn.md`                                 | `Docs/UPSTREAM_README*.md` | 保留上游官方说明存档，不被新 README 覆盖         |

## 🚀 快速开始

### 环境准备

1. 安装 **ESP-IDF v5.0**（务必用 release/v5.0 分支）：
   ```bash
   git clone -b release/v5.0 --depth 1 https://github.com/espressif/esp-idf.git
   cd esp-idf && ./install.sh esp32,esp32s2,esp32s3
   . ./export.sh
   ```
2. 安装 Python 依赖：
   ```bash
   pip install idf-component-manager idf-build-apps
   ```

### 编译固件

```bash
git clone https://github.com/YaoKunS/ESP-Drone.git
cd ESP-Drone
git checkout yaokun-organized   # ★ 切到耀坤整理版分支（默认 master 是上游原版结构）
cd Firmware                     # ← ESP-IDF 工程根

idf.py set-target esp32s2    # 选择目标芯片（esp32 / esp32s2 / esp32s3）
idf.py build                 # 编译
idf.py -p /dev/ttyUSB0 flash # 烧录
idf.py monitor               # 打开监视器看日志
```

也可以用批量构建工具（上游 CI 用的就是这个）：

```bash
cd Firmware
idf-build-apps find
idf-build-apps build
```

### 烧录到无人机

1. 用 USB 线连接开发板
2. 按住 **BOOT** 键 → 按一下 **EN** 键 → 松开 BOOT（进入下载模式）
3. 执行 `idf.py -p <端口> flash`

## 🧩 硬件参考

| 板卡                           | 说明                        |
| ---------------------------- | ------------------------- |
| **ESP32_S2_Drone_V1_2**      | 主板，集成 ESP32-S2 模块与飞行必需传感器 |
| **ESP32_S2_Drone_Flow_Deck** | 光流扩展板（配合光流传感器实现定点）        |
| **ESPlane_FC_V1**            | ESPlane 飞控 V1             |
| **ESPlane_V2_S2**            | ESPlane V2（S2 版本）         |

完整原理图与 PCB 文件在 [`Hardware/`](Hardware/) 目录，器件数据手册在 `Hardware/datasheet/`。

主机板详细规格与引脚分配见：[Hardware Reference](Docs/zh_CN/rst/hardware.rst)（中文）。

## 📱 手机 APP

ESP-Drone 官方提供了完整的手机端 App 源码，可自行编译改造：

| 平台      | 仓库                                                                                    |
| ------- | ------------------------------------------------------------------------------------- |
| iOS     | [EspressifApps/ESP-Drone-iOS](https://github.com/EspressifApps/ESP-Drone-iOS)         |
| Android | [EspressifApps/ESP-Drone-Android](https://github.com/EspressifApps/ESP-Drone-Android) |

## 📚 文档

完整文档（Sphinx 源文件）位于 [`Docs/`](Docs/)：

| 语言 | 目录                                  |
| -- | ----------------------------------- |
| 中文 | [Docs/zh\_CN/rst/](Docs/zh_CN/rst/) |
| 英文 | [Docs/en/rst/](Docs/en/rst/)        |

主要内容：

- [快速入门](Docs/zh_CN/rst/gettingstarted.rst) —— 从零开始上手
- [硬件参考](Docs/zh_CN/rst/hardware.rst) —— 规格、扩展板、引脚分配
- [系统架构](Docs/zh_CN/rst/system.rst) —— 软件组成与数据流
- [通信协议](Docs/zh_CN/rst/communication.rst) —— Wi-Fi/ESP-NOW 通信格式
- [驱动开发](Docs/zh_CN/rst/drivers.rst) —— 如何为新传感器写驱动
- [开发环境](Docs/zh_CN/rst/getespidf.rst) —— ESP-IDF 环境与工程结构详解

在线阅读：<https://docs.espressif.com/projects/espressif-esp-drone/zh_CN/latest/gettingstarted.html>

## 📜 开源协议与致谢

- **上游项目**：ESP-Drone © **Espressif Systems（乐鑫）** ·  
  源码仓库 <https://github.com/espressif/ESP-Drone>
- **许可证**：**GPL-3.0**（完整文本见 [LICENSE](LICENSE)）
- **本复刻版**：仅做中文文档与目录整理，**未修改固件逻辑**。
- **本仓库整理**：耀坤智联 YaoKunSmart · <https://github.com/YaoKunS>
- **B 站**：<https://space.bilibili.com/3707011699050767>

### 第三方代码

本项目包含以下第三方版权代码：

|       组件       | License |                                          源代码                                         |      Commit ID      |
| :------------: | :-----: | :----------------------------------------------------------------------------------: | :-----------------: |
| core/crazyflie | GPL-3.0 |       [Crazyflie](https://github.com/bitcraze/crazyflie-firmware/tree/2021.01)       | tag_2021_01 b448553 |
|   lib/dsp_lib  |    —    | [esp32-lin](https://github.com/whyengineer/esp32-lin/tree/master/components/dsp_lib) |       6fa39f4c      |

> ⚠️ **GPL-3.0 是强copyleft（传染性）许可**：分发本项目或其衍生作品时，  
> 必须保留相同协议、保留版权声明，并提供对应源代码。  
> 使用、修改、分发时请务必遵守 GPL-3.0 的全部条款。

### 致谢

1. 感谢 **Bitcraze** 开源组织提供很棒的 [Crazyflie](https://www.bitcraze.io/) 无人机项目代码
2. 感谢**乐鑫 Espressif** 提供 ESP32 芯片与强大的 [ESP-IDF 框架](https://docs.espressif.com/projects/esp-idf/en/latest/esp32s2/get-started/index.html)
3. 感谢 **WhyEngineer** 提供的 STM32 DSP 移植库 [esp-dsp](https://github.com/whyengineer/esp32-lin/tree/master/components/dsp_lib)

## 📋 支持政策

自 2022 年 12 月起，乐鑫对本项目提供**有限支持**，但仍然欢迎 Pull Request！

## ⚠️ 免责声明

本项目按"现状"提供，不附带任何担保。飞行无人机存在**人身与财产风险**：

1. 首次试飞请在**空旷场地**进行，人员与建筑保持安全距离
2. 仔细检查电机与桨叶安装牢固性，确认桨叶旋向与电机匹配
3. 遵守当地无人机法规（实名登记、禁飞区、限高等）

⚠️ 因使用本项目造成的任何损坏、伤害或损失，作者与本复刻整理方均不承担责任。**风险自负！**

---

<div align="center">

**🚁 ESP-Drone** · 由 [Espressif 乐鑫](https://github.com/espressif) 官方推出 · GPL-3.0 License  
· 本中文复刻整理版由 [耀坤智联](https://github.com/YaoKunS) 维护

</div>

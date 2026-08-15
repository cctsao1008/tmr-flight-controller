# TMR-FC — Top Multi-Rotor Flight Controller

TMR-FC is an open hardware and software multirotor flight-controller platform originally developed as a university graduation project.

The platform combines an STM32-based flight controller with onboard inertial and environmental sensors, navigation interfaces, actuator outputs, data logging, and PX4-derived firmware. A second-generation design explored integration with Raspberry Pi for computer vision, ROS, MAVLink, and optical-flow applications.

> **Project status:** Historical / research project. The repository documents the original TMR-FC hardware and software architecture and is retained primarily for reference, study, and future experimentation.

## Overview

TMR-FC was designed as a research-oriented multirotor flight-control platform rather than a commercial product.

Two major hardware generations are documented:

- **TMR-FC V1.0** — STM32F405RG-based standalone flight controller.
- **TMR-FC V2.0** — STM32F407VG-based design intended to work standalone or as a Raspberry Pi daughter board for higher-level vision and robotics applications.

The original firmware work was based on / ported from the PX4 project.

## System Concept

```text
                 +---------------------------+
                 |       Raspberry Pi        |
                 | OpenCV / ROS / MAVLink    |
                 | Vision / Optical Flow     |
                 +-------------+-------------+
                               |
                         V2.0 integration
                               |
                 +-------------v-------------+
                 |          TMR-FC           |
                 | STM32F405 / STM32F407     |
                 |                           |
                 | IMU / Magnetometer        |
                 | Barometer / GPS           |
                 | PWM / PPM / S.BUS         |
                 | USB / SD / CAN / GPIO     |
                 +-------------+-------------+
                               |
                      ESCs / Actuators
                               |
                 +-------------v-------------+
                 |      Multirotor Airframe  |
                 +---------------------------+
```

## Hardware

### MCU

| Version | MCU |
|---|---|
| V1.0 | STM32F405RG |
| V2.0 | STM32F407VG |

### Sensors

#### V1.0

- I2C: MPU6050
- I2C: HMC5883
- I2C: MS5611

#### V2.0

- I2C: HMC5983
- I2C: MPL3115A2 or MS5611
- SPI: MPU6000
- Optional SPI: LSM303DLM
- Optional SPI: L3G4200D
- Optional ADC: ADXRS652 × 3

Some V2.0 sensor data was intended to be shareable with a Raspberry Pi for higher-level applications.

## I/O and Board Features

### V1.0

- 12-channel PWM output
- PPM input
- Futaba S.BUS input
- Built-in 10-DOF sensor set
- PCA9533 / PCA9536 controlled LEDs
- GPS port via UART / I2C
- Auxiliary SPI and GPIO
- RF port for APC230 or Bluetooth modules
- LiPo voltage measurement via ADC
- Beeper / tone alarm
- SWD debug interface
- SONAR interface
- USB VCP and MSC
- MicroSD storage
- Light-bar LED control
- Internal Flash EEPROM emulation
- RTC backed by CR1220 battery
- USB OTG

### V2.0 additions

- CAN transceiver support using TJA1050 or MAX3051
- Raspberry Pi daughter-board concept
- Camera integration for optical-flow / vision experiments

## Software Architecture

The original software stack was derived from the PX4 autopilot ecosystem and integrated with TMR-FC-specific board support.

The current top-level repository structure is:

```text
tmr-flight-controller
├── Bootloader
├── Firmware
├── libopencm3
├── .gitmodules
└── README.md
```

`Bootloader`, `Firmware`, and `libopencm3` are Git submodules. The historical PX4-derived firmware tree also referenced NuttX within the firmware stack.

The project also explored Raspberry Pi integration with:

- OpenCV
- ROS
- MAVLink
- Camera-based optical flow

## Firmware Update

### Bootloader with `dfu-util`

```bash
sudo dfu-util -a 0 -d 0x0483:0xdf11 \
  --dfuse-address 0x08000000 \
  -D tmrfc_bl.bin
```

A Segger J-Link can also be used for bootloader programming.

### Flight-control firmware with `dfu-util`

```bash
sudo dfu-util -a 0 -d 0x0483:0xdf11 \
  --dfuse-address 0x08004000 \
  -D tmrfc-v1_default.bin
```

The project also supported the historical PX4 `QUpgrade` workflow when the TMR-FC bootloader had already been programmed.

## Getting the Source

Clone the repository and initialize its submodules:

```bash
git clone https://github.com/cctsao1008/tmr-flight-controller.git
cd tmr-flight-controller
git submodule update --init --recursive
```

The current `.gitmodules` file references:

- `https://github.com/cctsao1008/Firmware`
- `https://github.com/cctsao1008/libopencm3`
- `https://github.com/cctsao1008/Bootloader`

> Note: Some historical submodule references, upstream URLs, toolchains, or build dependencies may no longer be directly usable without additional restoration work.

## Historical Resources

The original project published supporting material including:

- TMR-FC user guide
- Hardware schematic
- Gerber files
- PX4-derived software
- DIY Drones project write-up

Some external links from the original project date back to the early 2010s and may no longer be available.

## Development Notes

TMR-FC V2.0 was conceived as a hybrid architecture in which the STM32 flight controller handled deterministic low-level flight-control tasks while a Raspberry Pi provided higher-level compute for vision and robotics workloads.

That architectural split remains a useful embedded-systems pattern:

```text
Real-time MCU
    ├── sensor acquisition
    ├── attitude / flight control
    ├── actuator output
    └── safety-critical timing

High-level processor
    ├── computer vision
    ├── navigation
    ├── ROS integration
    └── application logic
```

## Project History

TMR-FC was originally created by **Chia-Cheng Tsao (Ricardo Tsao)** at NTUT, Taiwan, as a graduation research project.

The project reflects an early open-hardware / open-software flight-control architecture built around STM32, PX4-derived firmware, and experimentation with companion-computer integration.

## License

No explicit license is currently documented in this repository. Unless a license is added, normal copyright rules apply to the source code and hardware design files.

## Acknowledgements

The project referenced and built upon work from the PX4, NuttX, libopencm3, Raspberry Pi, ROS, OpenCV, and MAVLink ecosystems.

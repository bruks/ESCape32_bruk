# ESCape32 Heli Spool-Up Fork (unofficial)

This is an unofficial fork of [ESCape32](https://github.com/neoxic/ESCape32) by Arseny Vakhrushev. It adds Hobbywing-style soft start for helicopters. It is not supported by the upstream project, so please report issues here, not on the ESCape32 Discord.

**Version:** 17.50 (based on upstream revision 17, patch 2)

## What's changed
- Helicopter soft start / spool-up ramp (default 15 s)
- Bailout window and bailout spool time, adjustable over the Wi-Fi link
- New settings: `heli_spoolup_sec`, `heli_bail_window_sec`, `heli_bail_spool_ms`

Tested on a Sequre 28120 ESC with a Flywing H1 Pro flight controller.

## ⚠️ Safety
Always test with the main and tail blades removed first. Confirm the spool-up time and bailout behavior before flying. Use at your own risk.

## License
GPLv3, same as upstream. Original copyright belongs to Arseny Vakhrushev. Modifications © 2026 Bruk. Full source for every release is in this repository.

---


ESCape32
========

Firmware for 32-bit BLDC motor electronic speed controllers that aims for simplicity. It is designed to deliver smooth and efficient motor drive, fast transitions from a complete stop to full throttle, robust direction reversals, and maximum hardware support.


Features
--------

+ Servo PWM, Oneshot125, automatic throttle calibration
+ DSHOT 300/600/1200, bidirectional DSHOT, extended telemetry
+ Analog/serial/iBUS/SBUS/SBUS2/CRSF/EXBUS/HoTT input mode
+ KISS/iBUS/S.Port/CRSF/MSB/HoTT telemetry
+ DSHOT 3D mode, turtle mode, beacon, LED, programming
+ Sine startup mode, brushed mode, hybrid mode (sensored/sensorless)
+ Proportional brake, adjustable drag brake
+ Temperature/voltage/current/stall protection
+ Variable PWM frequency, active freewheeling
+ Customizable startup music/sounds


Installation
------------

The list of compatible ESCs can be found [here](https://github.com/neoxic/ESCape32/wiki/Targets).

The latest release can be downloaded [here](https://github.com/neoxic/ESCape32/releases).

Visit the [ESCape32 Wiki](https://github.com/neoxic/ESCape32/wiki) for more information.


Dependencies
------------

+ cmake
+ arm-none-eabi-gcc
+ arm-none-eabi-binutils
+ arm-none-eabi-newlib
+ libopencm3
+ stlink


Building from source
--------------------

Use `LIBOPENCM3_DIR` to specify a path to LibOpenCM3 if it is not in the system root:

```
git clone https://github.com/libopencm3/libopencm3.git
make -C libopencm3 TARGETS='stm32/f0 stm32/g0 stm32/g4 stm32/l4'
cmake -B build -D LIBOPENCM3_DIR=libopencm3
```

Use `CMAKE_INSTALL_PREFIX` to specify an alternative system root:

```
cmake -B build -D CMAKE_INSTALL_PREFIX=~/local
```

To build all targets, run:

```
cmake -B build
cd build
make
```

To flash a particular target using an ST-LINK programmer, run:

```
make flash-<target>
```


Building on GitHub
------------------

+ Fork the repository.
+ Go to _Actions_.
+ Run the _Build ESCape32_ workflow.

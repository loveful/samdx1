# samdx1

This guide explains how to modify memory addresses and memory allocation size for the [UF2-SAMDX1 bootloader](https://github.com/adafruit/uf2-samdx1) and [Arduino core](https://github.com/adafruit/ArduinoCore-samd) to allow for other MPUs (micro-processing units) that are not currently supported. This is a useful technique for those who are looking to work with boards that are not yet supported by the existing bootloader and core.

## Why do we need this?

Generally, UF2-SAMDX1 bootloaders tend to work with MPUs in the same series, however the memory mapping can sometimes be different. For instance, if you attempt to flash a Feather M4 bootloader (SAMD51J*19*A) onto a board with the SAMD51J*18*A, it may seem like the flash was successful. But, when you try to upload code via the Arduino IDE, you run into memory corruption and that causes the device to not be recognized over USB. Unfortuntely, reflashing the bootloader won't fix the issue, you'll need to get your hands dirty.

## UF2-SAMDX1
### Prerequisites

Before you start, it is beneficial to have an understanding of how a bootloader works, how the Arduino IDE compiles and uploads, and the fundamentals of memory addresses, memory allocation size, and byte manipulation.

I'm going to presume that you have the following installed:
* [Arduino IDE](https://www.arduino.cc/en/software)
* [Adafruit SAMD Boards Support Package (Arduino Core)](https://learn.adafruit.com/adafruit-arduino-ide-setup/arduino-1-dot-6-x-ide)
* [UF2-SAMDX1](https://github.com/adafruit/uf2-samdx1)

### Modifying the UF2-SAMDX1 Bootloader
Make sure that the UF2-SAMDX1 bootloader source even builds a working binary file. <br />
Find the board pinout that closely resembles your own, then modify the `CHIP_VARIANT` in the .mk file to your chip.

For example, the original FEATHER_M4's board.mk:
```
CHIP_FAMILY = samd51
CHIP_VARIANT = SAMD51J19A
```

And I modified it to SAMD51J18A since I was using a SAMD51J18A:
```
CHIP_FAMILY = samd51
CHIP_VARIANT = SAMD51J18A
```

This will enable the build to be compiled with your chip if it is already included in the [default Atmel atpack](https://github.com/adafruit/uf2-samdx1/tree/master/lib) (Look into the atpack and see if your chip has their own .h file in the `include` of your chip family, for example, I am using a samd51, so I go into the `/lib/samd51/include` and see a `samd51j18a.h`, this means that it should compile fine).


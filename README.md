# samdx1

This guide explains how to modify memory addresses and memory allocation size for the [UF2-SAMDX1 bootloader](https://github.com/adafruit/uf2-samdx1) and [Arduino core](https://github.com/adafruit/ArduinoCore-samd) to allow for other MPUs (micro-processing units) that are not currently supported. This is a useful technique for those who are looking to work with boards that are not yet supported by the existing bootloader and core.

## Why do we need this?

Generally, UF2-SAMDX1 bootloaders tend to work with MPUs in the same series, however the memory mapping can sometimes be different. For instance, if you attempt to flash a Feather M4 bootloader (SAMD51J*19*A) onto a board with the SAMD51J*18*A, it may seem like the flash was successful. But, when you try to upload code via the Arduino IDE, you run into memory corruption and that causes the device to not be recognized over USB. Unfortuntely, reflashing the bootloader won't fix the issue, you'll need to get your hands dirty.

## Prerequisites

Before you begin, you should have a basic understanding of how a bootloader functions and how the Arduino IDE compiles and uploads. It is also helpful to have a good understanding of memory addresses and memory allocation size.

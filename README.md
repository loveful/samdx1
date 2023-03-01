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

This will enable the build to be compiled with your chip if it is already included in the [default Atmel atpack](https://github.com/adafruit/uf2-samdx1/tree/master/lib) <br />

#### Modifying linkers and self-linkers
Now we need to modify the self-linker and linker scripts for the memory space definitions:
Find the [SAMDX1 datasheet](https://ww1.microchip.com/downloads/aemDocuments/documents/MCU32/ProductDocuments/DataSheets/SAM-D5x-E5x-Family-Data-Sheet-DS60001507.pdf), and find the Configuration Summary for your board.

Go to the `scripts` directory and there should be 2 linker scripts, one for the samd21j18a and one for the samd51j19a. Create a copy for the self linker and the linker script for the one that represents your board, I chose the samd51j19a, so I created a copy of those two and renamed the name to be "samd51j18a.ld" and "samd51j19a_self.ld".

Open both of those ld files that you just made and modify both the `memory space definitions`s `LENGTH` accordingly to the datasheet you opened. To my knowledge, we don't need to modify the section definitions so don't worry about that unless you need to and you know exactly what you're doing. <br />
You can also modify the `ORIGIN` for the starting address in the memory block, but I don't suggest unless you know exactly what you're doing.

#### Modifying the Makefile
Now open the `Makefile` in the main directory and find the lines:
```

ifeq ($(CHIP_FAMILY), samd21)
LINKER_SCRIPT=scripts/samd21j18a.ld
BOOTLOADER_SIZE=8192
SELF_LINKER_SCRIPT=scripts/samd21j18a_self.ld
endif

ifeq ($(CHIP_FAMILY), samd51)
LINKER_SCRIPT=scripts/samd51j19a.ld
BOOTLOADER_SIZE=16384
SELF_LINKER_SCRIPT=scripts/samd51j19a_self.ld
endif
```
Adjust based on file name of the linker and self-linker scripts and chip you're using and save.

Now, build the source to create a modified .bin file that is flashable for your chip and corresponds to the correct memory mapping.

### Uploading the Bootloader to your Board

Now flash the .bin file you just compiled onto the board with a device programmer (e.c JTAG) and something that can talk to your device programmer (e.c Atmel Studio)

## Modifying Arduino Core for Arduino IDE
*to be contiuned*

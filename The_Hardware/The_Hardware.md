# The Hardware

##	The MyStorm BlackIce II Development Board

![myStorm BlackIce II][img1]

The [myStorm BlackIce II][] is a development board with:
*	a Lattice HX4K TQFP144 Ice40 FPGA with 16KB of block RAM
*	an STM32L433 processor with 256KB of flash memory and 64KB or RAM
*	A full capability USB port attached to the STM32
*	A USB UART switchable between the STM32 and the FPGA
*	6 double and 2 single Pmods
*	512KB of external SRAM
*	An SD card reader available to both the FPGA and STM32
*	Arduino Uno headers (with 4 extra pins on Digital3)
*	a short Raspberry Pi header.
*	a reset button for the STM32
*	two buttons available to the FPGA
*	4 dip switches available to the FPGA
*	A power LED
*	An LED that indicates when the FPGA has been configured (DONE)
*	An LED available to the STM32 (DEBUG)
*	4 LEDs available to the FPGA

The design of the board is slightly unusual as flash memory is not directly available to the FPGA, so configuration of the FPGA must be done via the STM32 processor (or via the Raspberry Pi header).

Another oddity is that although the Ice40 chip is an HX4K, it is really an HX8K whose resources have been limited by the Lattice software. As we are using the icestorm tools, we get the full resources of an HX8K.

The standard [iceboot][] STM32 firmware supports configuring the FPGA by copying the bit stream to the cdc-acm USB port (/dev/ttyACM0 on Linux) or by copying it from a fixed address in the STM32 flash memory.

However, it is possible to write custom firmware that can provide extra functions but must provide some way to configure the FPGA.

The board also includes a Mux that allows some of the components to be switched. The STM32 can switch the Ice40 SPI slave used for configuration between itself and the RPi header.  When the SPI slave is switched to the RPi header the 4 LEDs are not available to the Ice40.

As the icestorm tools treat the HX4K device as an HX8K device, the specs of the HX8K are most relevant to BlackIce II.

[img1]:					./MyStormBlackIceII.jpg "MyStorm BlackIce II Development Board"
[myStorm BlackIce II]:	https://www.tindie.com/products/Folknology/blackice-ii/
[iceboot]:				https://github.com/mystorm-org/BlackIce-II/tree/master/firmware/iceboot

##	The Lattice ICE40 HX4K FPGA
As the device is effectively an HX8K when programmed with the icestorm tools, the specs for that device are most relevant. It comes in a TQFP144 144 pin package.  There are 56 pins available via the Pmod connectors, and a total of 107 pins used (PIOs).

There are two PLL (but the 2nd one may not work) and 960 Programmable Logic Blocks (PLLs) which each consist of a 4 input look-up table (4-LUT) and a 1-bit D flip-flop. There are 32 blocks, each of 512 bytes, giving a total of 16kb of BRAM.

Verilog written for other devices, provided it does not use directives specific to Xilinx or Altera or other manufacturers, will normally work on the Ice40, as long as it does not use too many resources. The icestorm tools report on all resources used.

There are some differences due to use of the icestorm tools. It is not usually possible to deduce the usage of pins marked as inout in module interfaces, and there is limited support for tri-state logic. For this reason, the SB_IO directive (see the Directives chapter below) needs to be used for inout pins and the should be tri-stated by setting the direction to input.

Although all the examples in this book are in Verilog, it is possible to program the device using the icestorm tools, in VHDL. See the Languages chapter below.

##	STM32 Processor
The STM32L433 is a 32-bit ARM device that is connected to the Arduino headers and shares some pins with the Ice40 and with the Rpi header.

The STM32L433 uses a 12Mhz crystal, but a PLL multiplies this by 9, so the clock speed is 108Mhz.

The main ways of programming the STM32 processor are:
-	The arm-none-eabi gnu toolchain with STM drivers and libraries
-	The Arduino IDE

The first of these ways is usually done by copying and modifying the iceboot firmware.  This is described in the STM32 Programming chapter below.

How to use the Arduino IDE is in the Arduino chapter of this book.

##	Normal/DFU Mode Jumper
The STM32 has a built-in bootloader that boots into DFU mode by default. In DFU mode the device accepts DFU commands from dfu-util. This allows program and data to be uploaded into the STM32 flash memory. This is useful for replacing the firmware on the STM32 or for writing Ice40 bitstreams into STM32 flash memory (at address 0x0801f000) from where the iceboot firmware will read them and configure the Ice40 at start up.

If the jumper switch shown in the diagram above is in position, the BOOT pin on the STM32 is brought low which causes the STM32 to boot into normal mode and run the iceboot firmware.

When running most of the examples in this book, the board should be in normal mode.

It needs to be in DFU mode when using the Arduino IDE as that environment use dfu-util to configure the device.

It also needs to be in DFU mode in to flash some software such as the retro computer applications, as these write custom firmware and an Ice40 bitstream to the STM32 flash memory.

If the Rpi header is in use, the jumper needs to be removed and the Raspberry Pi will then control whether the STM32 is in normal or DFU mode.

##	Reset Button
When the reset button is pressed the STM32 resets. If the board is in DFU mode it goes into its DFU bootloader. If the board is in normal mode, it starts the iceboot firmware or any custom firmware that has replaced iceboot, such as an Arduino application, or the custom firmware that comes with the retro computer applications.

If the iceboot firmware is running it will look for an Ice40 bitstream in flash memory (address 0x0801f000) and if found will configure the FPGA. If no such bitstream is found, the Ice40 will be unaffected and will continue to run its existing bitstream. Iceboot will then wait fort bitstreams on its USB port.

##	PMODS
Most of the hardware is connected via [Pmod][] connectors.

The Pmod standard was introduced by Digilent and they sell a large variety of Pmods.

There is [a kit][] for producing your own Pmods that can optionally be bought with the BlackIce II.

There are also Pmods available on open source hardware sites such as OSH Park, for example those produced by the myStorm designer [Folknology][] and by [BlackMesaLabs][] and [BikerGlen][].

Here is a collection of Digilent, home-made and OSH Park Pmods:
![Collection of Pmods][img2]
 
There are 6 double and 2 single Pmods available on the BlackIce II.

They are called Pmod1 – Pmod14 as shown in this picture from the BlackIce II Wiki at the start of this chapter.

Pmod shares 3 of its pins with the USB2 UART, which limits its uses if your application uses the UART.

The pins on a Pmod are numbered 1-6 from the right. For a double Pmod the top pins are 1-6 and the bottom ones 7-12. Pines 5 and 11 are connected to GND. Pins 6 and 12 are connected to +3.3v.

BlackSoC (see below) numbers its Pmods differently. It only number the double Pmods and PMOD 1 / 2 is not used because of the conflict with the UART which is used by BlackSoC. So BlackSoC has 5 double Pmods called Pmod1 – Pmod5, corresponding to PMOD 3 / 4, PMOD 5 / 6, PMOD 7 /8, PMOD 9 / 10, and PMOD 11 / 12.

The pins on Pmods 13 and 14 and reused in multiple ways and so great care must be taken if you use those Pmods.

Pmod13 is shared with the slider switches which have 10k pull-up resistors to 3.3v. In the ON position the sliders are hard-wired to GND. They are also available as pins DIG16- 19 on the Arduino Digital3 header. Those pins are not present on most Arduino shields. The pins are also used for the SD card. So, it is only safe to use Pmod13 for input if the sliders are in the OFF position and even then, you must take into account that they have a 10k pullup resistor. Similarly, the SD card can only be used when the sliders are in the OFF position.

Pmod14 is shared with the Leds.

You can plug a huge array of hardware into the Ice40 FPGA using single row and double row Pmods. And using double or even triple Pmods.

If your device needs a 5v supply, it can get it from the VIN pin on the Arduino header.

[Pmod]:				https://en.wikipedia.org/wiki/Pmod_Interface
[a kit]:			https://www.tindie.com/products/Folknology/the-mystorm-hackers-pmod-kit/
[Folknology]:		https://www.oshpark.com/profiles/Folknology
[BlackMesaLabs]:	https://www.oshpark.com/profiles/BlackMesaLabs
[BikerGlen]:		https://oshpark.com/profiles/bikerglen
[img2]:				./Pmods.jpg "A Collection of Pmods"

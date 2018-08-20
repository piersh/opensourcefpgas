#	The MyStorm BlackIce II Development Board

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
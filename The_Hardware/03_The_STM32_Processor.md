#	STM32 Processor
The STM32L433 is a 32-bit ARM device that is connected to the Arduino headers and shares some pins with the Ice40 and with the Rpi header.

The STM32L433 uses a 12Mhz crystal, but a PLL multiplies this by 9, so the clock speed is 108Mhz.

The main ways of programming the STM32 processor are:
-	The arm-none-eabi gnu toolchain with STM drivers and libraries
-	The Arduino IDE

The first of these ways is usually done by copying and modifying the iceboot firmware.  This is described in the STM32 Programming chapter below.

How to use the Arduino IDE is in the Arduino chapter of this book.

# Arduino

The BlackIce has an STM32 processor that is normally used to configure the FPGA, but it can be used to run programs. One convenient way to do this is to use the Arduino IDE.

Arduino hardware support for myStorm has been provided by Richard Miller. The library supports configuring the Ice40 from a uart or USB stream or from the STM32's flash memory. It also supports communication between the STM32 and the Ice40 via SPI or QSPI.

## Installing the Arduino Mystorm Package

Installation instructions:
1.	First install Thomas Roell's arduino-STM32L4 package using instructions under __Board Manager__ [here][].
2.	Under Preferences in the Arduino IDE, append to the __Additional Board Manager URLs__ the URL http://www.hamnavoe.com/package_millerresearch_mystorm_index.json
3.	Open the Boards Manager from the Tools>Board: menu and type "mystorm" into the search box to find and select "MyStorm by Richard Miller". Select the most recent version and Install.
4.	Under Tools>Board: you should now be able to select "myStorm BlackIce" from the list of boards.
5.	Once the board is selected, BlackIce-specific options will appear on the Tools menu to configure serial port, USB and SD card.
6.	Example programs mentioned above can be found under File>Examples>MyStorm.

The package now includes an updated version of dfu-util that works on BlackIce II.

As it uses dfu-util to write the Arduino program to the STM32, the jumper needs to be removed from the RPi header.

There are two SPI interfaces between the STM32 and the Ice40: SPI and SPI2.

As the default one (SPI) is multiplexed with the LEDs, it cannot be used if the LEDs are required, so SPI2 should be used instead, but that uses the same pins and the dip switches, so they must be in the off position. SPI1 is pins D10-D13 on the Arduino header. Those pins are also used for the second i2c instance, Wire1. Wire 2 appears to use one of the SPI/LED pins and one of the QSPI pins.

The latest version of the library has an example of using QSPI between the STM32 and the Ice40.

There are multiple Serial devices available. Serial corresponds to USB 1, which is /dev/ttyACM0 on Linux. Serial1 corresponds to the USB 2 uart and corresponds to /dev/ttyUSB0 on Linux. Serial2 uses two of the QSPI pins and can be used to talk to the Ice40, provided that QSPI is not in use. Serial 3 is pins D0 and D1 on the Arduino header.

Most of the Arduino examples build the FPGA binary into a header file, include it in the STM32 program, which then uses it to configure the Ice40 at start-up or reboot of the STM32.

The Arduino program replaces the standard iceboot firmware. It can include a simple version of the USB stream configuration that iceboot does.

When you have finished using an Arduino program, you should reinstate iceboot and clear flash memory. And you should put the jumper back in the RPi header, if required.

You can access Arduino shields, preferably 3.3v ones, although all pins other than the analog ones are 5v tolerant. There are 4 extra digital pins available: DIG16 – 19, which are D20 – D23 in Arduino.

The are 4 examples in the MyStorm library:
-	SPIreadswitches, which uses SPI to read the Ice40 switches
-	SPIswriteleds, which uses SPI2 to write the leds
-	SDcard, which reads a bitstream from an SD card and configures the Ice40 with it
-	Iceboot, which emulates the iceboot firmware but reads from the serial stream in a simpler way

[here]:									https://github.com/GrumpyOldPizza/arduino-STM32L4

## QSPI

The best way to transfer information between the STM32 and the Ice40 is to use QSPI. As there are up to 4 data pins used for the transfer, this is up to 4 times faster than using SPI and the pins do not clash with use of the LEDs, switches or the SD card.

To do this with Arduino, you can udse the QSPI library that is part of the myStorm Arduino package.

The QSPI library has one example:
-	QSPIswtoleds, which uses QSPI to read the switches and then write the values to the leds

The example includes a QSPI slave that runs on the Ice40 and which does 2-way transfer.

Here is [another example][], which is put together using the SDCard example from the myStorm library, the QSPI example and the PWM-out audio code from the fpga.fun site.  On start-up, it lists the files on the SD card, and invites you to type the name of one. The SD card needs to contain an 8-bit WAV file. When you type the name of that file, it is transferred to the Ice40 using QSPI and played on a 1-bit DAC on pin 34 of the Ice40: [Watch on YouTube][]

[another example]:						https://github.com/lawrie/verilog_examples/blob/master/fpga/qspiplay/
[Watch on YouTube]:						https://www.youtube.com/watch?v=03obvUfjgA8

## GameDuino 3

One shield that works well with is the Gameduino 3. It makes a large touchscreen with fast graphics available to BlackIce. There is a terminal program available for the Gameduino 3 that talks to the Ice40. It is useful if you are running the Mecrisp Forth system on the Ice40. It was written by Anthony DiGirolamo - [https://github.com/AnthonyDiGirolamo/blackice_gameduino_terminal](https://github.com/AnthonyDiGirolamo/blackice_gameduino_terminal).

![GameDuino 3][img1]

[img1]:									./GameDuino3.jpg "GameDuino 3"

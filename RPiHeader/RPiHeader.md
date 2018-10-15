# RPi Header

![RPi Header][img1]

![RPi Header with Pi Zero][img2]

The BlackIce II can be controlled by a Raspberry Pi using the short Raspberry Pi header. It is an alternative to using the USB ports for both powering the device and programming it.

The Raspberry Pi headers supports:
-	Setting the boot mode of the STM32
-	Resetting both the STM32 and the Ice40
-	SPI to the Ice40
-	I2c to the STM32 or Ice40
-	UART to the STM32 or Ice40 as an alternative to USB2
-	SWDIO for debugging or writing to flash memory (as an alternative to dfu-util).
-	Access to the DONE pin and DEBUG LED

The [mystorm-rpi][] project has software to support all this.

[img1]:									./RPiHeader.jpg "RPi Header"
[img2]:									./RPiHeaderWithPiZero.jpg "RPi Header with Pi Zero"
[mystorm-rpi]:							https://github.com/lawrie/mystorm-rpi

## Preparing RPi SD Card

You can use the standard Raspbian image, which you should write to an SD card, such as a 16Gb high speed card.

The Raspberry Pi Zero W does not seem to have enough memory to do all the compilations that these set-up instructions need, so if you plan to use a Raspberry Pi Zero, you may need to set up the SD card on a full-size Raspberry Pi and then transfer it to your Raspberry Pi Zero.

When Raspbian desktop boots, you should set up connect Wifi to your router. It is a good idea to give the RPi a fixed IP address by editing /etc/dhcpcd.conf.

You should run raspi-config, give the machine a name, set up localisation options, and switch on the SSH and SPI options. You should disable the serial console but keep the serial uart enabled.

It is recommended that you disable Bluetooth so that /dev/ttyAMA0, rather than the mini-uart, is available to talk to the BlackIce II. The instructions are [here][id1]. There may be other options such as using the mini uart for Bluetooth, if you need to keep Bluetooth functionality.

As SSH has been switched on, you should change the password for user pi. You can set up and use a different user if you wish.

You should then follow the [Getting Started][] instructions to set up the icestorm tools and ensure that your username is in the required groups. As we won’t be using /dev/ttyACM0, you should not follow the instructions for uploading your first bitstream.

We will be using the mystorm-rpi tools project for accessing the BlackIce II over the Rpi header, so you should download it by:

	git clone https://github.com/lawrie/mystorm-rpi

This project needs the bcm2835 for SPI, so you should [follow these instructions][] to set it up.

Once that is done you can build the rpiprog tool by running the buildrpiprog script in my-storm-rpi/src:

	cd mystorm-rpi/src
	./buildrpiprog
	sudo cp rpiprog /usr/local/bin

If you plan to run BlackSoC, you should download it by:

	git clone https://github.com/lawrie/icotools

You will need to edit icotools/icosoc/icosoc.py and change all uses of /dev/ttyUSB0 to /dev/ttyAMA0. You will also need to change the way that the bitstream is configured from “cat icosoc.bin >/dev/ttyACM0” to “sudo rpiprog icosoc.bin”. But before you do this you need an extra command to switch SPI to the RPi header. The easiest way to do this is to add “python mystorm-rpi/src/boot.py”.

To compile BlackSoC C programs you will need the riscv32 toolchain. Instructions for setting it up are [here][id2].

Once all that is done you should have a working SD card.

[id1]:									https://www.raspberrypi.org/documentation/configuration/uart.md
[Getting Started]:						https://github.com/mystorm-org/BlackIce-II/wiki/Getting-Started
[follow these instructions]:			http://www.airspayce.com/mikem/bcm2835/
[id2]:									https://github.com/cliffordwolf/picorv32

## Preparing BlackIce II

You will need to update the BlackIce II to run the [rpiceboot][] firmware. This can be set up with the Arduino IDE.  See the  on the Arduino chapter below. Just open rpiiceboot.ino in the Arduino IDE, select the mystorm board and the port, and press the upload button. Running Arduino on IDE is not that easy, so it is best to run Arduino on a PC connected to the BlackIce by USB 1. BlackIce must be in DFU mode with the jumper on the RPi header removed.

[rpiceboot]:							https://github.com/lawrie/mystorm-rpi/tree/master/rpiiceboot

## Running BlackIce II Using the RPi Header

Once the SD card and the BlackIce have been prepared, you should insert the SD card in the Raspberry PI and connect your Raspberry Pi to your BlackIce II either by a ribbon cable or by soldering a female header to a Raspbery Pi Zero W and plugging it in the BlackIce II Rpi header, as shown in the pictures.

You can use the python programs boot.py and dfu.py to reboot in normal or dfu mode. They are in mystorm-rpi/src.

boot.py pulls down the boot pin and reset the STM32. dfu.py set the boot pin high and reboots into dfu mode.

When you are in boot mode the rpiiceboot firmware will be running and will have switch the mux to enable SPI from the RPi header.

That allows you to use “sudo rpiprog <bitstream>” to configure the Ice40. It is very fast and gives a console message to say if it worked. It won’t work if you are in DFU mode, rpiiceboot is not running, or the mux is not set to the RPi header. When the config has been done, rpiprog sets the mux to show the LEDs. This means you should run boot.py again before doing another call to rpiprog.

You can request that rpiiceboot switches the mux by sending a command to the uart /dev/ttyAMA0. You should set:

	stty -F /dev/ttyAMA0 raw -echo 115200

The mux has three states:
1.	Disabled. In this state SPI connects from the STM32 to the Ice40.
2.	Enabled and LEDs selected. The LEDs are available to the Ice40.
3.	Enabled and RPi selected. The LEDs will be off. SPI connects from the Rpi to the Ice40.

The uart commands to rpiiceboot are single characters. Any line feeds or newlines are ignored

The command are:
-	“l” switches to LED mode
-	“r” switches to Rpi mode

## Streaming SPI Over the RPi Header

You can do fast streaming of data from the Raspberry Pi to the Ice40 when the mux is in RPi mode. You will normal have to send an “r” command to it to ensure this.

You can the use the spi and spistream programs in mystorm-rpi/src. You should first build them with buildspi or buildspistream. The spi program sends single bytes to the Ice40. The spistream program streams data over SPI. In both cases an SPI slave must be running on the Ice40.

Streaming is fast and can support full speed audio or a low resolution, low frame rate video.

## Using SWDIO Over the RPi Header

You can use the SWDIP and SWCLK pins to run openOCD and optionally the gnu debugger over the RPi header. The required configuration files are in mystorm-rpi/src. To run openocd put openopen.cfg in the current directory, put raspberrpi-blackice.cfg in the directory with the other source config files and do “sudo openocd”.

This allows you to write to the STM32 flash memory, as an alternative to using dfu-util. It also allows you to debug STM32 programs.

## Using I2C Over the RPi Header

If you enable i2c on the Raspberry Pi you can use hardware i2c it over the RPi header. For example you can use Arduino to run an i2c device on the STM32 and run an i2c master program on the Raspberry Pi. There is an example python program in mystorm-rpi/src. Sending data to the STM32 works but receiving data does not seem to.

## Architecture Summary

![Architecture Summary][img3]
 
This diagram summarises the architecture when using the RPi header. The rpiiceboot firmware runs on the STM32, together with an i2c device program if you are using i2c.

On the Ice40 an SPI slave must be running if you are streaming SPI.

Not shown is the uart connection to rpiiceboot that lets you send commands to switch the mux.

On the Raspberry Pi you can run:
-	rpiprog to configure the Ice40
-	spistream to stream data to the Ice40
-	boot.py to reboot the STM32 in normal mode
-	dfu.py to reboot the STM32 in DFU mode
-	readi2c to send requests to the i2c device on the STM32
-	openocd to program and debug the STM32

[[img3]:								./ArchitectureSummary.jpg "Architecture Summary"

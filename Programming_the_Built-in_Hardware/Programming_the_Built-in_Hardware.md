# Programming the Built-in Hardware

This chapter describes how to use Verilog to access the built-in hardware on the BlackIce II board.

This built-in hardware covered consists of:  
- The 4 user LEDS
- The two user buttons
- The 4 DIP switches
- The UART

Memory access including accessing the SRAM, and access to the SD card reader is covered in later chapters.

## Development environment

All the projects are built using the open source icestorm tools: yosys, arachne-pnr, and icepack, and Makefiles are used to build the bit streams to configure the Ice40 FPGA.

Instructions for setting the tools up for BlackIce II are [here][].


[here]:		https://github.com/mystorm-org/BlackIce-II/wiki/Getting-Started

Each project has its own directory contains the Verilog (.v) files, a .pcf file that maps pin names onto physical pin numbers, and a Makefile of the form:

	VERILOG_FILES = <list of Verilog files>
	PCF_FILE = <pcf file>

	include blackice.mk

Where blackice.mk is:

	chip.bin: $(VERILOG_FILES) ${PCF_FILE}
		yosys -q -p "synth_ice40 -blif chip.blif" $(VERILOG_FILES)
		arachne-pnr -d 8k -P tq144:4k -p ${PCF_FILE} chip.blif -o chip.txt
		icepack chip.txt chip.bin

	.PHONY: upload
	upload: chip.bin
				stty -F /dev/ttyACM0 raw
		cat chip.bin >/dev/ttyACM0

	.PHONY: clean
	clean:
		$(RM) -f chip.blif chip.txt chip.bin

## LEDs

![LEDs][img1]

The BlackIce II board has 4 built in LEDs that are available to the FPGA:
- A blue LED, labelled LED1, with pin number 71;
- A green LED, labelled LED2, with pin number 67;
- An orange LED, labelled LED3, with pin number 68;
- A red LED, labelled LED4, with pin number 70.

[img1]:				./LEDs.jpg

### Turn on an LED

You should create a development directory for running the examples in this book. Put the blackice.mk file given above in that directory.

Inside that development directory, make a directory called led, and in it add the following files.

You need a pcf file, led.pcf, to give the pin a name:

	set_io blue_led 71

And a Verilog file, led.v:

	module led(
		output blue_led
	);

		assign blue_led = 1;
  
	endmodule
	
And a Makefile:

	VERILOG_FILES = led.v 
	PCF_FILE = led.pcf

	include ../blackice.mk

Before building this example, you should do:

	stty -F /dev/ttyACM0 raw
	cat /dev/ttyACM0

This lets you see messages from the iceboot software when you upload bitstreams.

To run this example, type:

	make upload

You should see messages on the iceboot console including the version number of iceboot and “Config done”.  You may also see “Setup done” and “Waiting for serial”.

When configuration is successful the green (DONE) LED will be on and the red (DBG1) LED will be off.

Your design should then run, and the blue LED should turn on. The other LEDs may be either on or off or floating depending on their previous state. It is therefore more sensible to program all 4 LEDs as an array – see the next section.

before you run “make upload”.

If the config fails for any reason, the green (DONE) LED will be off and red (DBG1) LED will be on.  DBG1 comes on while the config is in progess.

Config will fail if /dev/tyyACM0 has not been set to raw, or if the bitstream (chip.bin) is invalid in some way. After a config failure, you will need to press the reset button before you try again. Config can also fail if you have removed the jumper from the RPi header so you are in DFU and not normal mode. If can fail if the internal mux on the board is enabled and switched to the RPi header, but that only happens if you are using non-standard firmware.

Another problem that can occur on Linux machine is that a program called modemmanager is running and accessing /dev/ttyACM0. If modemmanager is installed on your Linux machine, you should uninstall it, disble it ot stop it while you are doing BlackIce development.

### An array of LEDs

You can address all 4 LEDs at once. Make a directory called led and in it add:

leds.pcf:

	set_io leds[0] 71
	set_io leds[1] 67
	set_io leds[2] 68
	set_io leds[3] 70

leds.v:

	module leds(
		output [3:0] leds
	);

		assign leds = 4’b1111;
  
	endmodule
 
Makefile:

	VERILOG_FILES = leds.v 
	PCF_FILE = leds.pcf

	include ../blackice.mk

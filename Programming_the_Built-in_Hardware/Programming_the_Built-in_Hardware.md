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

	VERILOG_FILES = `<list of Verilog files`>
	PCF_FILE = `<pcf file`>

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


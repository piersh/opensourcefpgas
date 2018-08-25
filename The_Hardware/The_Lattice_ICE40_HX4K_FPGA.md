#	The Lattice ICE40 HX4K FPGA
As the device is effectively an HX8K when programmed with the icestorm tools, the specs for that device are most relevant. It comes in a TQFP144 144 pin package.  There are 56 pins available via the Pmod connectors, and a total of 107 pins used (PIOs).

There are two PLL (but the 2nd one may not work) and 960 Programmable Logic Blocks (PLLs) which each consist of a 4 input look-up table (4-LUT) and a 1-bit D flip-flop. There are 32 blocks, each of 512 bytes, giving a total of 16kb of BRAM.

Verilog written for other devices, provided it does not use directives specific to Xilinx or Altera or other manufacturers, will normally work on the Ice40, as long as it does not use too many resources. The icestorm tools report on all resources used.

There are some differences due to use of the icestorm tools. It is not usually possible to deduce the usage of pins marked as inout in module interfaces, and there is limited support for tri-state logic. For this reason, the SB_IO directive (see the Directives chapter below) needs to be used for inout pins and the should be tri-stated by setting the direction to input.

Although all the examples in this book are in Verilog, it is possible to program the device using the icestorm tools, in VHDL. See the Languages chapter below.

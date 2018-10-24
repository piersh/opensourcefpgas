# Languages

## Verilog

Verilog is the main language that is used for programming the BlackIce II.

## VHDL

It is possible to use VHDL on BlackIce II or other boards that use the icestorm tools by following these instructions:

Firstly build yosys et al. in the usual way.

Next:

	$ git clone git://github.com/cliffordwolf/yosys-plugins
	$ cd yosys-plugins/vhdl

... run your editor on vhdl_frontend.cc ...

In your editor, you need to find the line __log_header("Executing VHDL frontend.\n");__ and remove the ___header__ so that it reads __log("....");__

Now find the call to __AST::process()__ later in the file, and add __false__, between __...flag_dump_vlog, flag_nolatches,...__ so that it reads __...flag_dump_vlog, false, flag_nolatches,...__

Now you should:

	$ make test

... tests run, ensure they pass...

	$ make install

Finally, wherever yosys is invoked in your makefile, add __-m vhdl__ to its arguments and ensure that VHDL source files end in __.vhd__ rather than just __.v__ and things should "just work".

## C and C++

Most soft processors support C or a C-like language either by using a GNU gcc toolchain, or my using their own tools.

If a GNU toolchain is used then C++ and other languages are normally supported.

BlackSoc supports C, C++ and other languages, but has mainly be tested using C.

## CLike

The system described in the High Level Synthesis section below supports a c-like language called clike.

## Forth

There are a few ways of running Forth on BlackIce II.

One way is to use the Jupiter Ace emulation described in the Retro computing chapter.

Another way is to use the Mecrisp system which is available for BlackIce at <https://sourceforge.net/projects/mecrisp/files/mecrisp-ice-1.3.tar.gz/download>

It runs James Bowman’s J1A Forth system.

You interact with it by connecting a terminal to /dev/ttyUSB0. The terminal script is part of the Mecrisp installation.

The Mecrisp system will also run on the STM32. The download for that is <https://sourceforge.net/projects/mecrisp/files/mecrisp-stellaris-2.4.4.tar.gz/download>.

Instructions for using Mecrisp are at <http://mecrisp.sourceforge.net/>.

## Risc-V assembler

When using one of the Risc-V implementations such as icicle, picorv32 or BlackSoC,  a Risc-V assembler is part of the toolchain and you can use that to build Risc-V assembly programs.

## BCPL

The OPC6 system includes an implementation of BCPL.

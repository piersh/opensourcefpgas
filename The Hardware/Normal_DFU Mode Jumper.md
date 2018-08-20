#	Normal/DFU Mode Jumper
The STM32 has a built-in bootloader that boots into DFU mode by default. In DFU mode the device accepts DFU commands from dfu-util. This allows program and data to be uploaded into the STM32 flash memory. This is useful for replacing the firmware on the STM32 or for writing Ice40 bitstreams into STM32 flash memory (at address 0x0801f000) from where the iceboot firmware will read them and configure the Ice40 at start up.

If the jumper switch shown in the diagram above is in position, the BOOT pin on the STM32 is brought low which causes the STM32 to boot into normal mode and run the iceboot firmware.

When running most of the examples in this book, the board should be in normal mode.

It needs to be in DFU mode when using the Arduino IDE as that environment use dfu-util to configure the device.

It also needs to be in DFU mode in to flash some software such as the retro computer applications, as these write custom firmware and an Ice40 bitstream to the STM32 flash memory.

If the Rpi header is in use, the jumper needs to be removed and the Raspberry Pi will then control whether the STM32 is in normal or DFU mode.

# Cameras

## OV7670

Processing images of more than VGA resolution (640 x 480 pixels) is not very practical on BlackIce II. One reason is insufficient memory. A frame buffer for a VGA frame with 12 bits per pixel (4 bits for each of red, green and blue), takes 460,800 bytes. This only just fits in SRAM.

The OV7670 VGA camera module is available for 2 or 3 dollars on ebay or AliExpress. The one without the Fifo is cheapest sand best for been driven by an FPGA.

It supports 640 x 480 images in RGB oy YUV format and is configurable by an i2c interface called SCCB.  RGB can be in 16-bit 565 format or 12-bit 444 format, and the latter is best for BlackIce.

![OV7670][img1]

[Here][here1] is an example of continuous capture on an image into dual-ported BRAM and display to a VGA monitor.

And [here][here2] is the code to configure the camera. This example needs the camera in RGB mode rather than its default YUV mode.

A [PCB for an OV7670 camera module][] is available on OSH Park. The PCB has an 8x2 connector, but the OV7670 has 9x2 pins. The reset and power down pins are not usually used, so do not need to be connected. A 9x2 female header can be soldered to the PCB, with the pins for reset and power down removed.

[img1]:									./OV7670.jpg "OV7670"
[here1]:								https://github.com/lawrie/verilog_examples/tree/master/fpga/vgabuff
[here2]:								https://github.com/lawrie/OV7670-Verilog/tree/master/src
[PCB for an OV7670 camera module]:		https://oshpark.com/shared_projects/HAZ1ZB7w

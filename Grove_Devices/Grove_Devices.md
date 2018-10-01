# Grove devices

Seeedstudio make a lot of reasonably cheap Grove devices with a 4-pin connector. It is reasonably easy to make a lot of these work with the BlackIce II, particularly the devices that work with 3.3v supply and logic.

The Grove devices are sometimes simple digital devices that use one or two pins for input or output. Others are i2c devices that can be make to work with the BlackIce II, particularly when a soft processor is available.

Here is a Grove I2C RGB back-lit Text LCD working with BlackSoC. The device needs a 5V supply which is taken from the Arduino header, but it is fine with 3.3v volt logic, as many i2c devices are. It is being driven by BlackSoC using the mod_i2c_master module. It is connected using a Grove to male breadboard connector adapter cable.

![Grove I2C LCD][img1]

Diligent make a Grove Pmod, but it is easy to make your own using Grove PCB sockets.  Here are some of the simpler digital Grove devices such as buttons, LEDs and buzzers working with the BlackIce II using a homemade Pmod.

![Home-made Grove Pmod][img2]

[img1]:									./GroveI2CDisplay.jpg "Grove I2C LCD"
[img2]:									./HomemadeGrovePmod.jpg "Home-made Grove Pmod"

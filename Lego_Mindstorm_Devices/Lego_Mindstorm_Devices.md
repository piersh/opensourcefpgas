# LEGO Mindstorms devices

Most LEGO Mindstorms EV3 sensors are UART sensors. They have an interesting communication protocol that starts of at 2400 baud. The sensors send out their metadata saying what modes they support with what datatypes and what speed they wish to use for communication. If they receive an acknowledgement of the metadata, they switch to the speed they specified and go into data mode, sending a steady stream of data in their default mode. The host can then change mode.

One problem with accessing them from an FPGA is that most UART implementations have the speed as a parameter and it cannot be changed dynamically. To get around this a set of UART modules can be connected to different pins and a different module selected when the speed changes. In practice nearly all Ev3 sensors use 57600 baud as the speed.

Another issue is that not all EV3 sensors may work at 3.3v, but most seem to.

There is a BlackSoc example that accesses EV£ sensors and displays the data, but it is not yet fully working. A soft processor is needed to interpret the metadata and cope with data types including floats.

![LEGO Mindstorm Devices][img1]

[img1]:									./LegoMindstormDevices.jpg "LEGO Mindstorm Devices"

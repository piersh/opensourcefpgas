# Robotics

Robotics applications can be built using the components described in this book.

Encoder motors are best for wheeled robots as they provide the odometry that can be used for dead-reckoning to determine the position of the robot and can be used in combination with PID controllers to drive the robot with accurately calibrated velocity.

The Digilent dual H-bridge Pmod can be used to drive two encoder motors with a differential drive. This is the minimum needed for a wheeled robot or one with tank drive.

Odometry by itself is not a very accurate way to determine the robot’s position and it is usually complemented by an Inertial Motion Unit (IMU) consisting of an acceleration sensor, a gyroscope and sometimes a magnetic compass.

But odometry and an IMU is not sufficient to accurately determine the robot’s position. Accurate localization requires Lidar, 3D cameras or optical cameras.

Robots typically have input and output devices such as touchscreens to display information and to give commands or set parameters.

They may have speakers and microphones for speech input and speech or audio output.

Other components like robot arms can use servos and stepper motors.

Even simple mobile robots usually have sensors to avoid obstacles. A minimum is usually an ultrasonic, infra-red or laser distance sensor at the front of the robot.

Most robots also need some form or wireless communication.

BlackSoC is particularly suited to running robot applications as it supports a variety of i2c and SPI sensors, and a C program is useful for overall control of the robot.

![Robotic Vehicle][img1]

[img1]:									./RoboticVehicle.jpg "Robotic Vehicle"

Here is the [robot running][]:

[robot running]:						https://www.youtube.com/watch?v=vyuKBphg4wc

Here is the [robot application][].

[robot application]:					https://github.com/lawrie/verilog_examples/tree/master/fpga/robot

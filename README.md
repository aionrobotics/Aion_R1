# Aion Robotics - r1_control pkg

<img src="https://cdn.shopify.com/s/files/1/1759/4103/products/Aion-Robotics-R1-Gravel-Anamorphic-Flare-Front-25_1024x1024@2x.jpg?v=1516468791" width="400">

The r1_control pkg allows for manual ROS Joystick control of the R1 rover. This is accomplished by establishing a direct serial connection between the companion computer and Roboclaw motor driver.

Physical control is provided by a wireless Joystick controller. (Playstation, Xbox etc)

The following topics are also published for use within more advanced projects:
```
/cmd_vel
/diagnostics
/joy
/motors/commanded_speeds
/motors/read_currents
/odom
/rosout
/rosout_agg
/tf
```


## Configure Roboclaw Firmware

Setup Roboclaw motor driver

|Mode|Option|Definition|
|-----|-----|------|
|Packet Mode|7|address 0x80 (128)|
|Serial Mode|6|115200 Baud rate|

- [x] Connect Wheel Encoders to Roboclaw
- [x] Tune Encoders and perform Velocity Calibration within [Ion Motion Studio App](http://downloads.ionmc.com/software/IonStudio/setup.exe)

*Detailed setup instructions [HERE](http://downloads.ionmc.com/docs/roboclaw_user_manual.pdf)*


### The following prerequisites must be installed/setup on NVIDIA Jetson TX2

- [x] Ubuntu 16.04lts
- [x] ROS Kinetic-*Desktop*
- [x] Package - "joy" (Installed for you if using the script)
- [x] Package - "teleop-twist-joy" (Installed for you if using the script)
- [x] Package - "joystick-drivers" (Installed for you if using the script)
- [x] Connection to the internet
- [x] Enable Bluetooth and trust/pair joystick device

*Easy Button? - Click [HERE]() for script to flash the required Jetson image.*

### Joystick Setup

- Find Joystick:
```
ls /dev/input
```
This will print a list of input devices. The Joystick will show up as `jsX` or, `js0` in our example.


- Test Joystick:
```
jstest /dev/input/js0
```
Joystick readings will print to screen.
press ctrl-q to stop process

- Map Joystick to match ROS:
```
gstest-gtk /dev/input/js0
```
*Note: If ssh into TX2 you must first export display to remote machine:*
```
export DISPLAY=:10
```

**Sample mapping for Playstation Navigation Controller:**

|Function|Map To|Invert|
|-----|----|----|
|Linear Axis|1|no|
|Angular Axis|0|yes|
|Enable Button|8|N/A|
|Turbo Button|14|N/A|

*Note: The "Enable" button acts as a "Safety Switch". The Robot will not move unless the enable button is mapped correctly and held down. Ultimately, you should map the button layout according to your preference and Joystick type.*


# Installing r1_control pkg on the TX2

`User: nvidia`
`Password: nvidia`

*(Unless you changed after install)*


Open a terminal from the TX2 desktop or ssh into the TX2 from a host machine.

If ssh from remote machine:
```
ssh -X nvidia@<ipaddress>
```

1. Setup Workspace:
```
mkdir catkin_ws
cd catkin_ws
mkdir src
```

2. Clone roboclaw_node:
```
cd <workspace>/src
git clone https://github.com/aionrobotics/roboclaw_ros.git
```

3. Clone r1_control pkg:
```
git clone https://github.com/aionrobotics/aion_r1.git
cd ..
catkin_make
```

4. Source:
```
source devel/setup.bash
```
*(Must be done each time you open a new terminal before attempting to launch from that terminal)*

5. Launch r1_control pkg:
```
roslaunch r1_control teleop.launch
```
*(If you get "not found" error, source from `/catkin_ws`*


## Running Tests
Open a new terminal

- View Topics:
```
rostopic list
```

- View Topic Output:
```
rostopic echo <topic_name>
```

- Visualize Nodes/Topics:
```
rqt_graph
```
*Note: If ssh into TX2 you must first export display to remote machine:*
```
export DISPLAY=:10
```

## User Parameters

- To modify robolaw_node, edit:

`~/aion_r1/r1_control/launch/teleop.launch`


|Parameter|Default|Definition|
|-----|----------|-------|
|dev|/dev/ttyACM0|Device that is Roboclaw|
|baud|115200|Baud rate the Roboclaw is configured for|
|address|128|The address the Roboclaw is set to, 128 is 0x80|
|max_abs\_linear\_speed|2.0|Max linear speed allowed in meters per second|
|max_abs\_angular\_speed|2.0|Max angular speed allowed in meters per second|
|accel_lim|2.0|Max accel allowed in m/s^2|
|ticks\_per_meter|2263.6|The number of encoder ticks per meter of movement|
|base_width|0.431|Width from one wheel edge to another in meters|


- To modify teleop_twist\_joy, edit:

`~/aion_r1/r1_control/config/teleop.yaml`

```
  axis_linear: 1
  scale_linear: 1
  scale_linear_turbo: 2
  axis_angular: 0
  scale_angular: 2
  scale_angular_turbo: 2
  enable_button: 8 #L2 trigger
enable_turbo_button: 14 #L1 trigger
```
*Note: This changes ROS mapping*


## License

This project is licensed under the GPLv3 License - see the [LICENSE.md](LICENSE.md) file for details

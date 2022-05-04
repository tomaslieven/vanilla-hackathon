# VANILLA Hackathon
![enter image description here](https://imgs.xkcd.com/comics/pointers.png)

Steps inside this tutorial will help you to get up and running with simulation using LGSVL and ROS2. 

LGSV is Unity-based multi-robot simulator for autonomous vehicle developers and recommended to use NVIDIA based laptop to accelerate development. 

The Robot Operating System (ROS) is a set of software libraries and tools for building robot applications. From drivers to state-of-the-art algorithms, and with powerful developer tools, ROS has what you need for your next robotics project. And it's all open source.

# ROS2 FOXY INSTALL
Steps below are tested under Ubuntu 20, you can also follow up with slightly modified tutorial on Mac or Windows
https://docs.ros.org/en/foxy/Installation/Ubuntu-Install-Debians.html

## Set locale
Make sure you have a locale which supports UTF-8.

    locale  # check for UTF-8
    sudo apt update && sudo apt install locales
    sudo locale-gen en_US en_US.UTF-8
    sudo update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8
    export LANG=en_US.UTF-8
    locale  # verify settings

## Setup Sources

    sudo apt update && sudo apt install curl gnupg2 lsb-release
    sudo curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key  -o /usr/share/keyrings/ros-archive-keyring.gpg

And then add the repository to your sources list:

    echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] http://packages.ros.org/ros2/ubuntu $(source /etc/os-release && echo $UBUNTU_CODENAME) main" | sudo tee /etc/apt/sources.list.d/ros2.list > /dev/null

## Install ROS 2 packages
Update your apt repository caches after setting up the repositories to get ROS2 FOXY running

    sudo apt update

Install ROS, RViz, demos, tutorials.

    sudo apt install ros-foxy-desktop
    source /opt/ros/foxy/setup.bash

## Try some examples
Simple example to verify communication from terminal1 to terminal2

### Terminal1:

    source /opt/ros/foxy/setup.bash
    ros2 run demo_nodes_cpp talker

### Terminal2:
In another terminal source the setup file and then run a Python listener:

    source /opt/ros/foxy/setup.bash
    ros2 run demo_nodes_py listener

You should see the talker saying that it’s Publishing messages and the listener saying I heard those messages. This verifies both the C++ and Python APIs are working properly. 

**A JE TO!**


# LGSVL setup with bridge
The SVL Simulator can publish and subscribe to ROS 2 messages by connecting to the ROS2 LGSVL Bridge 

### Installing Colcon 

Colcon is a command line tool that facilitates building ROS packages. To install:

```bash
sudo apt update
sudo apt install python3-colcon-common-extensions
sudo apt install libboost-all-dev
```

## Installing the ROS 2 LGSVL Bridge  
 Using the Package Manager, if you need help building manually follow steps to build from source
 https://www.svlsimulator.com/docs/system-under-test/ros2-bridge/
 
```bash
source /opt/ros/foxy/setup.bash
sudo apt update
sudo apt install ros-foxy-lgsvl-bridge
```

## Running the ROS2 LGSVL Bridge 

Run ROS2 bridge:

```bash
source /opt/ros/foxy/install/setup.bash
lgsvl_bridge
```

# Download & run simulator
### Installing the simulator 

The Simulator release can be downloaded as a prebuilt binary from the  simulator distribution https://github.com/lgsvl/simulator/releases/latest

1.  Download and extract the simulator binary zip file for your OS to a desired location.
    -   For Linux:  _svlsimulator-linux64-{release-version}.zip_
    -   For Windows:  _svlsimulator-windows64-{release-version}.zip_
2. Run ./simulator or simulator.exe
3. Register and link to cloud your device, this will create your own "cluster" where you can run simulations

## GET Vanilla assets 
1.  Inside LGSVL web dashboard go to Store > Maps and serach for AutonomouStuff, and just click on "Add to Library"
2.  Store > Vehicle search for "Vanilla pod" and "Add to Library"
3.  in Library > Vehicles should be now "Vanilla pod", on right you will find Sensors configuration
4.  Add new sensor configuration, Name: vanilla_config, Access: Private, Brdige: ROS2
5.  Under SAVE button is icon to edit configuration "{..}", place inside RAW configuration and SAVE: https://raw.githubusercontent.com/tomaslieven/vanilla-hackathon/main/vanilla_config
6.  From sidebar Simulations > Add new
7.  In setting up New simulation > Add name/description , cluster select your cluster which you linked and created and Keep only "Interactive mode" selected
8.  On Next page > Runtime templace: "Random Traffic", Map: AutonomousStuff, Vehicle: Vanilla, Sensor configuration: vanilla_config
9.  From Autopilot select "Other ROS2 Autopilot", bridge connection should be set to localhost:9090 if you running locally or set IP of remote machine where the bridge is running
10.  Run simulator, click PLAY in simulator window. you should be able to control with arrows vehicle, F12 resets position 

## Access simulation and make vehicle move
You should have lgsvl_bridge running in terminal1 following up with SImulator instance, following commnad should control vehicle

```bash
ros2 topic pub -r 10 /lgsvl/vehicle_control_cmd lgsvl_msgs/msg/VehicleControlData "{
acceleration_pct: 0.3,
braking_pct: 0.0,
target_wheel_angle: 2.0,
target_wheel_angular_rate: 1,
target_gear: 1
}"
```


### Access all topics available
```bash
source /opt/ros/foxy/install/setup.bash
ros2 topic list 
```


listen to messages from any topic i.e. GPS:
```bash
ros2 topic echo /gnss/fix
```
you should see following message:
```bash
---
header:
  stamp:
    sec: 1651661340
    nanosec: 304243712
  frame_id: gnss
status:
  status: 0
  service: 1
latitude: 37.38052298600204
longitude: -121.90916628995981
altitude: 11.981121063232422
position_covariance:
- 0.0001
- 0.0
- 0.0
- 0.0
- 0.0001
- 0.0
- 0.0
- 0.0
- 0.0001
position_covariance_type: 2
---
```

### Visualize topics from simulator
Lidar, trajectory, TFs and other topics can be visualized from tool RVIZ:
```bash
rviz2 -d vanilla_hackathon.rviz 
```

### happy hacking :) 
tomas[x]vanilla.cz / jan[x]vanilla.cz




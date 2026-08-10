# Overview
- MAVLink or Micro Air Vehicle Link is a protocol for communicating with small unmanned vehicles.
- Was released in early 2009 by Lorenz Meier under LGPL license.
- It specifies a set of messages that are exchanged between a small and unmanned vehicle and a ground station.
- The Mavlink protocol is typically implemented inside autopilots (e.g. Ardupilot and PX4)
- There exists a [[#MAVROS]] protocol that connnects MAVLink to ROS.

# MAVROS



# PIXHAWK
- Pixhawk is an open-source hardware standard for flight controllers that acts as the central brain for unmanned aerial vehicles, combining onboard sensor processing with motor mixing algorithms. 
- By running open-source flight stacks like PX4 or ArduPilot, it provides the computational foundation for real-time stabilization, autonomous navigation, and integration into hardware-in-the-loop simulation environments.
- May have a buzzer connection for Feedback
- Offers telemetry to Groundstation software.
- Connected to motors via ESC (Electronic Speed Controller)
- Typically connected to a LiPO battery via some power module.
- May be connected to a reciver (which receives remote control commands)
- Autopilot also has an I2C port that allows connection to an external compas and GPS.
-

# Ground Station
- A software that communicates with the micro-vehicle through a serial or network interface by exchanging MAVLink messages.
- It allows to control the micro-vehicle and monitor its status in real-time
- There are several ground stations for different platforms e.g.:
  1. QGroundControl (QGC)
  2. Mission Planner
  3. AMP Planner 2 \[Considered best for MAC and Ubuntu operating Systems\]
  4. MAVProxy (Written in python and extensible via python)
  5. DroidPlanner (Tower) \[For android platform\]


# QGroundControl (GGC)
- Fully vehicle setup support for ArduPilot and PX4
- Developed in C++
- Mission planning for autonomous flight.
- Flight map display showing vehicle position, flight track, way-points and vehicle instruments.
- Video streaming with instrument display overlyas.
- Flight support for any MAVLink capable vehicle.
- Runs on Windows, OS X and Linux platforms as well as IOS and Android devices.


# MAVLink Messages and Commands
![[MAVLINK.png]]

Example Messages (From UAV to GS)
1. HEARTBEAT (#0) message: The heartbeat message shows that a system is present and responding.
2. SYS_STATUS (#1) message: It gives general information about the status of the system, the sensors available in the system and their status,..
3. GLOBAL_POOSITION_INT (#33) message: The filtered global position given by the GPS.

Example Commands (From GS to UAV)
1. COMMAND_LONG (#76) message: Send a command with up to seven parameters to the MAV. This can be used for example to implement a takeoff and land command.
2. SET_MODE (#25) message: Change the flight mode determined by base mode and custom mode.
---
tags:
  - MSc
  - tutorial
---

# Installation
Follow the installation Instructions from the [Official Documentation](https://docs.ros.org/en/kilted/Installation/Ubuntu-Install-Debs.html) These Instructions are valid for Ubuntu 24.04. Running the Latest ROS2 distribution as of February 2026 ([Jazzy Jalisco](https://docs.ros.org/en/jazzy/Releases/Release-Jazzy-Jalisco.html)).

# Sourcing files
You will need to run this command on every new shell you open to have access to the ROS 2 commands, like so:
```bash
source /opt/ros/jazzy/setup.bash
```
# Domain IDs
The default middleware that ROS 2 uses for communication is DDS. In DDS, the primary mechanism for having different logical networks share a physical network is known as the Domain ID. ROS 2 nodes on the same domain can freely discover and send messages to each other, while ROS 2 nodes on different domains cannot. All ROS 2 nodes use domain ID 0 by default. To avoid interference between different groups of computers running ROS 2 on the same network, a different domain ID should be set for each group.

For a detailed explanation of domain IDs to choose, visit here. Otherwise, to choose a safe number, simply choose a domain ID between 0 and 101, inclusive.

```bash
export ROS_DOMAIN_ID=<your_domain_id>
```
To maintain this setting between shell sessions, you can add the command to your shell startup script:
```bash
echo "export ROS_DOMAIN_ID=<your_domain_id>" >> ~/.bashrc
```

# Setup troubleshooting
The ROS 2 development environment needs to be correctly configured before use. This can be done in two ways: either sourcing the setup files in every new shell you open, or adding the source command to your startup script.

If you ever face any problems locating or using packages with ROS 2, the first thing you should do is check your environment variables and ensure they are set to the version and distro you intended.

Visit [here](https://docs.ros.org/en/jazzy/Tutorials/Beginner-CLI-Tools/Configuring-ROS2-Environment.html).

# Start up - Tutorial
A comprehensive tutorial on how to use ROS2 can be found on the [official documentation](https://docs.ros.org/en/jazzy/Tutorials/Beginner-CLI-Tools/Introducing-Turtlesim/Introducing-Turtlesim.html).


# Useful commands
A set of useful ROS2 commands can be found [[ROS2 Commands|here]].
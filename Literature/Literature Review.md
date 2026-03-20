# Ghamry, 2016: Real-Time Autonomous Takeoff, Tracking and Landing of UAV on a moving UGV Platform

## 1. Introduction
UAVs usually have limited payload since the main features of UAV design is to be smaller with better maneuverability for easy use and operation. As a result they have limited battery life, and consequently limited running time. On the other hand, unmanned ground vehicles (UGVs) offer high payload and longer running time. The pairing of UAVs with UGVs can solve the problem of limitation of UAV in long endurance to a practical mission.

The coordination of between UAVs and UGVs recieves a great interest in:
- Reconnaissance
- Surveillance
- Exploration applications

Local controllers for each vehicle is used to track thier reference trajectories, while a formation controller is used in a leader follower manner using a sliding control (SMC)\[6\]. The UGV is assumed as the leader and the UAV follows it during tracking and landing stages. 

This work evaluates 2 cases:
1. Regular tracking and landing.
2. The UAV takes off from the UGV, tracks it receives a command to do a specific mission. The UAV then accomplishes the mission, tracks the UGV and finally lands on the UGV to be prepared for recharging/refueling for the next mission.

## 2. Dynamics and Local control of UAV and UGV

### A. Quadrotor UAV Dynamics and Modelling:
The vehicle is a quad-rotor helicopter, with 4 rotors laid up symetrically about the center of mass (see fig below)
![[p1-Quadrotor_Illustration.png]]

The quadrotor dynamic model is obtained using the Lagrange method. The simplified quadrotor dynamics are as follows:
$$
\ddot{x} = (\sin\psi \sin\phi + \cos\psi \cos\phi)\frac{U_z}{M}
$$



# Applications of Autonomous UAVs (Motivation)
- Autonomous flight of UAV is demanded in applications in surveillance, rescue and aerial observation. Vision-based autonomous hovering and landing is vital for autonomous flight in GPS-denied environments.

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
\begin{aligned}
\ddot{x} &= (\sin\psi \sin\phi + \cos\psi \sin\theta \cos\phi)\frac{U_z}{M} \\
\ddot{y} &= (\sin\psi \sin\theta \cos\phi - \cos\psi \cos\phi)\frac{U_z}{M} \\
\ddot{z} &= -g + (\cos\theta \cos\phi)\frac{U_z}{M} \\
\ddot{\phi} &= \frac{U_\phi}{J_{xx}} \\
\ddot{\theta} &= \frac{U_\theta}{J_{yy}} \\
\ddot{\psi} &= \frac{U_\psi}{J_{zz}}
\end{aligned}
$$

Forces and moments along quadrotor axes can be deﬁned as:
$$
\begin{aligned}
U_z &= F_1 + F_2 + F_3 + F_4 \\
U_\phi &= L(F_3 - F_4) \\
U_\theta &= L(F_1 - F_2) \\
U_\psi &= K_{yaw}(F_1 + F_2 - F_3 - F_4)
\end{aligned}
$$


The quadrotor forces above can be found by:
$$
	F_i = K\frac{\omega_m}{s+\omega_m}u_i
$$
where $K$ is a positive gain, $\omega_m$ is the motor bandwidth, and $u_i$ is the control input.


### B. UGV Kinematics and Modeling

The robots considered in this work are differentially driven wheeled mobile robots (WMRs). 
![[Differenctially_Steered_Bot.png]]

The nonlinear kinematic equations of the robot are represented as follows:
$$
\dot{q} = \begin{bmatrix}
\dot{x} \\
\dot{y} \\
\dot{\phi}
\end{bmatrix} = \begin{bmatrix}
\cos \phi & 0 \\
\sin \phi & 0 \\
0 & 1
\end{bmatrix} \begin{bmatrix}
v \\
\omega
\end{bmatrix}
$$
The right and left velocities of the robot wheels are:
$$
\begin{align}
v_R = v + h\omega \\
V_L = v - h\omega
\end{align}
$$
where h is the distance between the vehicle longitudinal axisand each wheel.
**Note:** The non-holonomic nature of this confiiguration means that path planning for the UAV landing would be much more difficult because the UGV wouldn't be able to "slide" under the drone to correct lateral errors instantly. 

### C. UAV Control

The UAV control system is divided into two subgroups to manage its nonholonomic features:

- **Fully Actuated Subsystem**: Manages altitude ($z$) and yaw ($\psi$). It uses **Sliding Mode Control (SMC)** to minimize errors and ensure the UAV reaches the desired altitude and orientation.
- **Under-Actuated Subsystem**: Manages horizontal position ($x, y$) and attitude (roll $\phi$, pitch $\theta$). This is controlled via two loops:
    - **Outer Loop**: Uses a **Linear Quadratic Regulator (LQR)** to determine the desired roll and pitch angles needed to reach specific horizontal coordinates.
    - **Inner Loop**: Uses **SMC** to stabilize the actual Euler angles toward the desired values provided by the outer loop.
        

### D. UGV Control
- **Strategy**: The UGV uses a **pure-pursuit controller** for path and waypoint tracking.
- **Mechanism**: It is a geometric approach where the vehicle follows an arc to a goal point based on a "look-forward" distance ($L$).
- **Implementation**: This controller calculates the necessary speeds for the right and left wheels ($V_R, V_L$) to steer the differentially driven robot toward its target.
    

## Tracking and Landing Controller
- **Approach**: Uses a decentralized **leader-follower method**, where the UGV is the leader and the UAV is the follower.
- **Formation Controller**: This controller, based on **SMC**, generates position commands for the UAV based on the UGV's current position and a desired height ($Z_d$).
- **Phases**:
    - **Take-off**: Stabilizes the UAV at the desired height $Z_d$.
    - **Tracking**: Maintains the relative formation at height $Z_d$.        
    - **Landing**: Converges the relative $x, y, z$ distances between the vehicles to zero.

### IV. Experimental Results Analysis
The strategies were tested in a laboratory environment using a **Qball-X4 quadrotor** (UAV) and a **Quanser QGV** (UGV). Two scenarios were analyzed:
- **Case 1 (Regular Tracking and Landing)**: The UAV successfully took off from the moving UGV, tracked it, and landed back on it, with position errors converging to zero.
- **Case 2 (Mission Execution)**: The UAV took off from the UGV, diverted to a specific mission waypoint (0, -0.5, 0.7), executed a task, and then returned to track and land on the moving UGV.
    

### V. Conclusion
The paper concludes that the combined control strategy (SMC and LQR for the UAV, pure-pursuit for the UGV) is effective for real-time autonomous coordination. Experimental results confirmed the system's stability and its ability to handle complex mission profiles, such as diverting for a task and returning to a moving platform.

## Limitaions
- **Simplifying Assumptions**: To solve the formation problem, the authors assumed a **zero yaw angle** for simplicity. This may not account for real-world scenarios requiring complex aerial orientations.
- **Pure-Pursuit Parameter Selection**: Regarding the UGV's pure-pursuit controller, the authors note there is **no universal standard** for determining the "look-forward distance" ($L$), which is a critical parameter for the controller's effectiveness.
- **Constant UGV Velocity**: In the experimental setup, the UGV's speed was kept at a **constant value of 0.10 m/s**. The paper does not explore the controller's performance under variable or high-speed UGV maneuvers.
- **Computational Latency and Hardware**: The high-level controller was implemented on a separate ground station PC, which then calculated and sent PWM commands to the vehicles. This setup may introduce communication delays not present in a fully onboard system.
- **Limited Physical Scope**: The quadrotor used (Qball-X4) was enclosed in a protective cage, and both vehicles were tested within the specific confines of the NAV-Lab, which may not represent the turbulence or terrain irregularities of the intended applications, such as forest fire monitoring.


---

# Quaternion feedback attitude control system design based on weighted–ℒ2 –gain performance

## Motivation
- There are several possibilities to represent the attitude of rotation motions, including Euler angles [2–4], Rodrigues parameters [5,6], modified Rodrigues parameters (MRP) [5,7,8], and unit quaternion [9–11]. The attitude representations mentioned in the preceding sentence are thought to be parameterization of rotation matrix or direction-cosine matrix belonging to the special orthogonal group of order 3, SO(3) [12–14].Among those attitude representations, the unit quaternion is the only attitude parameteriza­tion without singularity. Therefore, it can represent the attitude glob­ally.


---
# Autonomous Takeoff, Tracking and Landing of a UAV on a Moving UGV Using Onboard Monocular Vision

## Introduction (Motivation)
- The UAVs offer a broad field of view and a complete 3-D sensing at higher speed compared with ground vehicles. However, the payload of micro UAVs is very limited. On the contrary, unmanned ground vehicles (UGVs) offer high-resolution views over short ranges, high payload and longer running time, although they have limited field of view and low coverage speed. The coordination between groups of UAVs and UGVs is of interests in reconnaissance and exploration applications [8] [9].
- In the paper, a visual-based approach employing a single onboard camera is presented for cooperation between an UAV and an UGV. The proposed visual-based approach is more flexible for UAVs to detect and track various objects compared with schemes employing LEDs for the flight guidance.

## System Description

### 1. The Quadrotor UAV platform
![[Quadrotor_UAV_Platform.png]]

The following details describe the hardware and operational specifications of the DJI F450 quadrotor and its supporting ground station:

- **Physical Specifications:** * **Model:** DJI F450.
    
    - **Weight:** 1.2 kg (with a 0.3 kg payload capacity).
    - **Size:** 0.5 m diameter.
    - **Propulsion:** Four brushless DC motors enabling 6-DOF (Degrees of Freedom) movement.
        
- **Onboard Sensors & Vision:**
    
    - **IMU:** A 9-DOF Inertial Measurement Unit for estimating attitude and position.
    - **Altimeter:** A sonar sensor to measure flight height.
    - **Primary Camera:** A 40 g monocular camera mounted at the geometric center (25 fps, 85° field of view).
    - **Navigation:** Uses downward-looking imagery to estimate relative position to a moving Unmanned Ground Vehicle (UGV).
        
- **Computing & Communication:**
    
    - **Microcontroller:** ARM Cortex-M4 (100 MHz) for autonomous stabilization.
    - **Ground Station:** Intel Core i7-3610QM laptop for processing position data, parameter adjustment, and flight command (departure/landing).
    - **Connectivity:** Dual wireless radio frequency modules for communication between the UAV and the ground station.
        
- **Landing Infrastructure:**
    
    - **Marker:** A circle-shaped landing target with a 20 cm radius.

### Ground Vehicle (UGV) Specifications
- **Platform:** A three-wheeled omnidirectional robot ($82 \times 63 \times 14$ cm) acting as a mobile landing pad.
    
- **Sensing & Control:** Uses three 500 pulses/rev optical encoders for velocity measurement, managed by a 16-bit Freescale MC9S12XS128 processor.
    
- **Communication:** Utilizes a ZigBee module to transmit status to a ground station, which handles trajectory settings and parameter tuning.
    
- **Landing Interface:** Equipped with a $103 \times 80 \times 6$ cm landing pad featuring a circular marker for UAV tracking.


## Quadrotor (UAV) Dynamics & Control

- **Modeling:** The dynamics are defined by a 6-DOF non-linear model in the inertial frame, accounting for linear position $(x, y, z)$ and attitude $(\phi, \theta, \psi)$.
- **Control Strategy:** To simplify the highly coupled non-linear system, the model is linearized around the hover state (where angles approach zero).
- **Stabilization:** Flight stability is maintained using three independent PID controllers for roll, pitch, and yaw.
- **Hardware:** Control and estimation algorithms run in real-time on an ARM Cortex-M4 microcontroller.

![[Sensor_Fused_Control_Scheme.png]]

### Attitude Estimation & Sensor Fusion
- **Sensor Suite:** Uses a 9-DOF IMU (gyroscope, accelerometer, and magnetometer).
- **Algorithm:** Employs an optimized gradient descent algorithm using **quaternion representation** to fuse sensor data, which compensates for gyroscope drift with low computational load.
- **Implementation:** Although internal calculations use quaternions to maintain accuracy, the system transforms these into **Euler angles** to feed the PID control loops.


## Autonomous Takeoff, Tracking & Landing of UAVs using an On-board Monocular Camera
- As absolute linear positions ( x, y, z ) can not be obtained from the onboard IMU, visual based navigation is critical for autonomous flights in GPS-denied environments.
- A low-cost monocular camera is mounted at the geometric center of the UAV downwards.
- A circle with a radius of 20 cm attached on the landing pad of the ground vehicle is used as a landing marker. The relative position of the target circle can be estimated with the aid of an onboard camera.
- The images captured by the onboard camera are transmitted to the land laptop over wireless channels. The laptop detects the target marker from the received image, estimates the currently relative position to the center of the target circle, and transmits the estimated relative position over a wireless radio frequency transmission module.

### Vision based target position estimate
The Vision based architecture is as follows:

![[Image_Processing_Circle.png]]

The following points detail the strategy and performance of the target location algorithm for UAV landing:

- **Restricted Search Area Strategy:**
    
    - To reduce computational load, the algorithm limits its search to a **square area** once the target circle is identified.
        
    - The square is centered on the target circle, with its side length equal to the circle's diameter plus a constant.
        
    - This restriction provides robustness by ignoring potential interference outside the designated square.
        
- **Target Acquisition & Recovery:**
    
    - **Loss of Target:** If the target is lost, the algorithm reverts to searching the **entire image**.
        
    - **Ambiguity Resolution:** If multiple circles are detected, the system selects the one closest to the **last known position** of the target circle.
        
    - **Reliability:** Experiments confirm successful identification in cluttered environments and in the presence of deliberate disturbances.
        
- **Processing & Hardware:**
    
    - **Latency:** The detection approach is highly efficient, taking only **25–30 ms** per frame on a laptop.
        
    - **Portability:** The processing laptop can be carried by a cooperating UGV, though the algorithm is optimized enough for potential future use on onboard microcontrollers.
        
- **Flight Control Loop:**
    
    - **Data Transmission:** The estimated relative position of the marker's center is sent to the UAV via a wireless radio frequency module.
        
    - **Autonomous Adjustment:** The UAV uses **closed-loop PID controllers** to automatically correct its $(x, y)$ position errors based on the received coordinates.

### Autonomous hovering, tracking and landing
The following points outline the sensing and control architecture used to achieve stable flight and target tracking:

- **Altitude Sensing & Filtering:**
    
    - **Primary Sensor:** A sonar sensor measures the UAV's altitude.
        
    - **Noise Reduction:** To counter high-frequency vibrations, sensor data is processed through a **median filter** (window size of 9) to ensure smooth readings.
        
- **Control System Architecture:**
    
    - **PID Framework:** The system utilizes **six independent closed-loop PID controllers**.
        
    - **Inner vs. Outer Loops:** * **Inner Loop (3 controllers):** Manages self-stabilized flight.
        
        - **Outer Loop (3 controllers):** Manages position and altitude tracking.
            
- **Motion & Thrust Mapping:**
    
    - **Vertical Thrust ($U_1$):** Driven by the altitude controller output.
        
    - **Yawing Moment ($U_4$):** Driven by the yaw controller output.
        
    - **Rolling Moment ($U_2$):** Formed by combining $x$-position and roll controller outputs.
        
    - **Pitching Moment ($U_3$):** Formed by combining $y$-position and pitch controller outputs.
        
- **Operational Modes:**
    
    - **Hovering:** Achieved using relative position feedback through the PID controllers.
        
    - **Tracking:** The UAV autonomously follows the landing marker when the UGV is in motion.

The control architecture is summarized in the figure below:
![[PID_Control_Scheme.png]]


## Autonomous vertical takeoff, landing and hovering at a set point
The whole system architecture is summarized below:
![[System_Architecture.png]]

The following points summarize the UAV's performance during takeoff, hovering, landing, and its response to external disturbances:
- **Flight Phases & Stability:**
    
    - **Takeoff/Landing:** The UAV experienced higher angular "jitters" and position errors during these phases due to **ground effect** interference.
    - **Stability Threshold:** Self-stabilized flight was consistently achieved once the UAV climbed above **0.28 m**, at which point angular positions stayed within $\pm 2^{\circ}$.
    - **Hovering:** The UAV successfully maintained a steady hover at specified set points (e.g., 120 cm and 140 cm altitude).
        
- **Vision System Limitations:**
    
    - Visual-based position estimation is **unavailable** when the flight height is below approximately **0.28 m**, contributing to the larger $x$ and $y$ errors seen during initial takeoff and final landing.
        
- **Robustness to Perturbations:**
    
    - **Impulse Testing:** The UAV was subjected to external impulse forces along the $x$ and $y$ axes during hover.
    - **Recovery:** PID controllers allowed the UAV to immediately retrieve its desired position.
    - **Response:** Roll and pitch angles adjusted instantly to counteract forces, demonstrating that the control system is robust against external atmospheric or physical disturbances.

### Autonomous taking off, tracking and landing on a moving UGV

The following points summarize the experimental performance of the UAV during autonomous landing on a moving Unmanned Ground Vehicle (UGV):

- **Tracking Performance (Circular Path):**
    - **Test Parameters:** The UGV moved in a 2 m diameter circle at a constant speed of 0.63 m/s.
    - **Accuracy:** The UAV maintained position errors within $\pm$**20 cm** in the $x$ and $y$ axes, confirming high-precision tracking and landing capabilities.
        
- **High-Speed and Maneuverability Testing:**
    - **Sharp Turns:** The system was tested against $90^\circ$ sharp turns at higher speeds (**1.0 m/s to 1.2 m/s**).
    - **Results:** While larger position and angle errors occurred at the turning corners compared to smooth paths, the UAV still successfully tracked and landed on the vehicle.
        
- **Success Rates:**
    - **Moving Target:** **88.24%** success rate (15 out of 17 flights) with the UGV moving at speeds up to 1.2 m/s.
    - **Stationary Target:** **93.75%** success rate (15 out of 16 flights).
        
- **Comparative Error Analysis:**
    - **Hovering vs. Tracking:** Statistical data (Tables 1 & 2) indicates that tracking a moving UGV results in larger mean and standard deviation errors than stationary hovering.
    - **Final Accuracy:** In all successful landings, the maximum absolute position error remained **less than 20 cm** (the radius of the landing marker).
    - **Blind Zone:** Position estimation is unavailable when the UAV is lower than **0.28 m**, as the camera becomes too close to the target for effective processing.
        
- **Future Improvements:**
    - The current system uses $640 \times 480$ resolution images at altitudes of 120–140 cm. Higher resolution imaging is expected to further increase $x$ and $y$ position accuracy.

# Conclusion and Future Work Summary
The paper concludes with an overview of the vision-based cooperation system between a UAV and UGV, highlighting its current successes and future research directions:

- **System Achievement:**
    - **Autonomous Operation:** Successfully demonstrated vertical takeoff, tracking, and landing on a moving UGV using a single onboard camera.
    - **Control Architecture:** Utilized 6-DOF PID controllers and a 9-DOF IMU for self-stabilized flight and precise positioning.
    - **Efficiency:** The detection algorithm is computationally efficient enough to be processed on a laptop (carried by the UGV) or potentially an onboard microcontroller.
        
- **Core Capabilities:**
    - **Robust Detection:** The vision-based approach provides a flexible and reliable method for identifying and tracking specific markers (the target circle) amidst noise.
    - **Practical Validation:** Experimental results confirmed the system’s ability to handle the transition from takeoff to landing on a mobile platform.
        
- **Future Research Directions:**
    - **Performance Enhancements:** Investigating optical flow techniques for velocity control and achieving faster, smoother tracking.
    - **Hardware Upgrades:** Incorporating wide-angle lenses or pan-tilt cameras to expand the field of view for high-speed tracking.
    - **Precision & Scaling:** Moving toward higher-resolution imagery for better accuracy and exploring multi-agent cooperation involving multiple UAVs and UGVs.


# Research on Aerial Autonomous Docking and Landing Technology of Dual Multi-rotor UAV

### TBD (Please Remember to paste the relevant info...)


# Stabilized Prediction Error Method for Closed-loop Identification of Unstable Systems

## 1. Introduction
- If we look at large-scale networked systems, each subsystem exists in a closed-loop. Furthermore, the closed loop setting is known to be useful in identification for control. More Importantly, if the plants are unstable, it is not able to identify them in open-loop.
- Closed-loop Identification is known to be difficult because of the correlation between the noise in output measurement and the input.
- Prediction Error Method (PEM) based on one-step prediction gives correct estimates in many situations.
- There are various approaches to direct closed-loop identification where unknown (or uncertain) controllers are present:
	- **Joint input output identification:** The controllers (unknown) should be linear, and the number of parameters tends to be large.
	- **Instrumental Variable (IV) methods:** It does not matter whether the controllers are linear or non-linear, while the model structure matters.
	- **Subspace Identification methods:** These employ the innovation from which enables identification of unstable plants. However, the identification of unstable plants is difficult and most methods produce inconsistent estimates.
	- **Dual Youla method:** It is inherently robust in identifying unstable systems. It reduces the original closed-loop identification to an open-loop identification of a stable system via Youla parameterization. 
- *The purpose* of this paper is to present a simple framework for identification of unstable plants, which is applicable both for linear and nonlinear cases, and the models can be obtained for non-experts with the aid of numerical optimization tools.
- The proposed method has the versatility to accommodate a wide range of non-linear models as long as it can simulate the output of the target system.
- This method can generate unbiased estimates when the closed-loop system is linear and the controller is known.
![[Prediction_Error_in_an_OOEM-1.png]]


## Motivating Example

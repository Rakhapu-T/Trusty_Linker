# Whole System Pipeline Design
![[System Design.png]]
*Generated with Claude*

# Phase 0
- [ ] Establish OS Version
- [ ] Get stock PX4 v1.15.4 building and flying gz_x500 with QGroundControl, no MATLAB involved.
- [ ] Install Matlab support package and run one of its examples unmodified.
- [ ] Generate px4_sitl_default and launch it against Gazebo from the terminal rather than through the wizard.
- [ ] Cross compile that some Trivial model for the kakute H7 target. No need to flash it just prove that it works.
- [ ] Do some bandwith resolution (Figure out the latencies at each level)
- [ ] Do some propulsion parameter fitting.
- [ ] Build the parameterised vehicle model
- [ ] generate the SDF, and pass the hover check.
- [ ] 
- [ ] **Finally:** Create a simulink-generated module flies the x500 in Gazebo and the identical model builds for the Kakute H7.

# Phase 1 (Build the world)
- [ ] Author the UGV as an SDF with the gz-sim diff-drive system.
- [ ] Bridged through ros_gz_bridge to /cmd_vel and odometry. 
- [ ] Teleop it around an empty world. 
- [ ]  Start Gazebo standalone with the created world and attach PX4 SITL so the drone spawns into it.
- [ ] **Finally:** Drive the voyager with a keyboard while the drone hovers in the same world.

# Phase 2 (Ground Station Skeleton)
- [ ] Create the ROS2 package with both adapter interfaces defined (Optitrack & Gazebo ground truth) but only the Gazebo implementations written. Bridge the drone's ground-truth pose, convert to NED, and stream ODOMETRY over UDP to PX4 SITL at 50Hz. Then turn off GPS aiding and make the aircraft fly on external vision alone.
- [ ] **Finally:** Make sure the Drone holds position in simulation using only the pose stream. (This proves that my frame conversion is correct)


# Phase 3 (Make the drone aware of the voyager)
- [ ] Add the second MAVLink stream carrying the voyager's pose and confirm with listener on the PX4 console that the conrresponding uORB topic is populating with sensible numbers.
- [ ] Drive the voyager by teleop and watch the values change onboard.

# Phase 4 (Guidance_module_A)
- [ ]  Write the state machine, publishing trajectory_setpoint so PX4's position controller stays in the loop. 
	- [ ] takeoff and hold;
	- [ ] approach and track a stationary voyager;
	- [ ] approach and track the voyager driving in constant velocity in a straight line.
	- [ ] descent
	- [ ] touchdown
	- [ ] Add the stateless guard and abort path before attempting the first moving descent
- [ ] **Finally:** A repeatable landing on a rover moving in a staright line


# Phase 5 (Harden and Instrument)
- [ ] Fix the contact friction and resolution in the SDF so the aircraft stays on the deck rather then sliding off (In simulation).
- [ ] Inject Artificial latency, jitter and dropouts into the sim pose adapter to approximate realistic timing.
- [ ] Add the energy metric from the actuator_motors and script the whole pipeline to run headless, 50 times with varied rover speeds and approach geometries.
- [ ] **Finally:** Obtain a success rate and an energy figure across many runs, not a single video.


# Phase 6 (Take over the outer loop)
- [ ] Move the module's output down to vehicle_attitude_setpoint, replacing PX4's position controller with my own. Re-run **phase 5** and compare my attitude control vs that shipped with px4.
- [ ] **Finally:** Create a controller that matches or beats PX4 on the same benchmark and continue with Iteration.


# Phase 7 (Hardware in stages)
- [ ] Parameter Identification:
      - motor_thrust_constant (Fixed)                                        -> Mpilo data
      - moment_constant (Fixed)                                                 -> Mpilo data
      - max_rotational_velocity (Fixed)                                      -> Mpilo data
      - time_constant (Fixed)                                                         -> Mpilo data
      - weight (Will_Change)                                                          -> Measure It
      - Inertia_Tensor (Will_Change)                                            -> CAD / bifilar pendulum
      - Center_of_gravity_offset (Will_Change)                      -> Balance test
- [ ] Update the SDF file and re-run **Phase 5** again.


# Phase 8* (Deeper Control)
- [ ] Drop to the vehicle_rates_setpoint for a custom attitude controller and run a custom estimator in parallel with EKF2.


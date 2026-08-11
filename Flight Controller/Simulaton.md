# Motor Simulation Parameters That have to match that of the real Drone
1. Thrust coefficient
2. Moment constant 
3. Motor time constant
4. Max RPM of the motors

**Note**: These parameters can be measured on a thrust stand. **These are High priority Before proper simulation takes place!** 


# Differences Between Simulated and Real Quadrotor Characteristics:
- For simulation the control allocator ("mixer level of the control loop"), the loop is **open**. PX4 says "motor 2 at 0.63" and trusts that the ESC delivers roughly the right thrust. There's no feedback that the motor actually reached that speed. The closed loop shuts at the _rate_ level: if a motor underperforms, the aircraft rotates wrong, the gyro sees it, and the rate controller corrects. So the system self-corrects at 1 kHz even though nothing measures the propellers.


# Motor Simulation Parameters That have to match that of the real Drone
1. Thrust coefficient
2. Moment constant 
3. Motor time constant
4. Max RPM of the motors

**Note**: These parameters can be measured on a thrust stand. **These are High priority Before proper simulation takes place!** 


# Differences Between Simulated and Real Quadrotor Characteristics:
- For simulation the control allocator ("mixer level of the control loop"), the loop is **open**. PX4 says "motor 2 at 0.63" and trusts that the ESC delivers roughly the right thrust. There's no feedback that the motor actually reached that speed. The closed loop shuts at the _rate_ level: if a motor underperforms, the aircraft rotates wrong, the gyro sees it, and the rate controller corrects. So the system self-corrects at 1 kHz even though nothing measures the propellers.

# Physics engine
There are various physics engines available in Gazebo:
1. DART
2. Bullet
3. Bullet Fearhterstone
4. Custom Engine (could maybe include Vayun's Implementation)


## DART
- Open source C++ physics engine designed for **robotics**, **computer animation** and **machine learning research**.
- Uses the generalised Featherstone's algorithm to provide stable, high accurate articulated rigid body dynamics.
- DART integrates Newton–Euler rigid body dynamics and resolves contacts and friction through a constraint solver.

Features
- **Analytical Access:** Users can directly query mass matrices, Coriolis forces, centrifugal forces, and Jacobians.
- **Frame Semantics:** Supports arbitrary inertial and non-inertial reference frames.
- **Constraint Resolution:** Employs velocity-based linear complementarity problems (LCP) to implicitly handle contacts, non-penetration, and Coulomb friction.
- **Python Bindings:** Includes robust Python support alongside its native C++ core. 

# For my simulation
- DART integrates Newton–Euler rigid body dynamics and resolves contacts and friction through a constraint solver.
- All Physics includes **Gravity**, **inertia**, **contact**. Everything aerodynamic has to be _injected_ as an external force by a plugin.
- No blade element theory. No advance ratio, so the rotor produces the same thrust whether stationary or translating. No induced velocity, no wake.
- **Ground effect is not modelled at all.** Within roughly one rotor diameter of a surface, real thrust rises noticeably for the same RPM. Your entire final descent happens inside that region, and in simulation it simply won't exist. On hardware you'll see the aircraft resist the last portion of the descent in a way your simulation never predicted. That deck is also small and moving, so the effect is partial and asymmetric — genuinely hard to model, and worth treating as a known sim-to-real gap rather than something you'll close.
- **Contact behaviour is modelled, but it's entirely down to your parameters.** DART's constraint solver will handle the touchdown, but whether the aircraft settles, slides off, or tips over on an accelerating platform is decided by the friction coefficients and restitution you write into the SDF for the landing feet and the deck surface. These are usually left at defaults that nobody has validated. Since "does it stay on the moving robot after touchdown" is arguably your success criterion, these deserve real attention.
- **Battery sag doesn't exist.** Real thrust for a given command decays as the pack drains. For energy-efficiency work this is a meaningful omission, and it's another reason your energy figure from simulation is a _relative_ comparison metric between control strategies rather than an absolute prediction.
- One known trap while you're parameterising: the plugin multiplies the commanded velocity by a `rotorVelocitySlowdownSim` factor before squaring, so full-throttle thrust ends up as `(maxRotVelocity × 10)² × motorConstant` — people routinely compute their motor constant without accounting for it and end up with a wildly overpowered aircraft. [PX4 Autopilot](https://docs.px4.io/main/en/sim_gazebo_gz/vehicles)
- 
---

## project: Autonomous UAV Precision Landing on a Moving UGV platform: PX4 v1.15.4 / Simulink / ROS 2 / Gazebo status: planning tags: [uav, px4, ros2, gazebo, simulink, optitrack]

# Whole System Pipeline Design

![[System Design.png]]

---

## How to use this document

Each phase has a **Goal**, a **task list**, an **exit criterion**, and a **risk note**.

Two rules govern the ordering:

1. **One new risk per phase.** When something fails, there should be exactly one candidate explanation.
2. **Do not start a phase until the previous exit criterion is demonstrably met.** Not "mostly working" — met.

Legend: `- [ ]` pending · `- [x]` done · `⚠️` known risk · `❓` open decision

---

## Phase summary

|#|Phase|Goal|Exit criterion|
|---|---|---|---|
|0|Foundations|Prove the toolchain, the link, and the vehicle model|Simulink module flies the x500 in Gazebo; same model builds for Kakute H7|
|1|The world|Drone and Voyager coexist in one Gazebo world|Teleop the Voyager while the drone hovers alongside|
|2|Ground station skeleton|Pose pipeline into EKF2|Drone flies on external vision only, GPS disabled|
|3|Target awareness|Voyager pose reaches the flight controller|Onboard uORB values track the Voyager as it drives|
|4|Guidance module A|Working state machine on PX4's position controller|Repeatable landing on a Voyager moving in a straight line|
|5|Harden and instrument|Turn one landing into statistics|Success rate and energy distribution over N runs|
|6|Own the outer loop|Replace PX4's position controller|Your controller matches or beats the Phase 5 baseline|
|7|Hardware in stages|Move to the real aircraft|The validated manoeuvre, executed in the lab|
|8|Deeper control|Custom attitude loop and estimator|Optional; only on a proven baseline|

---

# Phase 0 — Foundations

> [!info] Goal Retire the three largest unknowns before writing a single line of control logic: whether the toolchain works end to end, whether the radio link can carry the data, and what the aircraft is physically capable of.

These four sub-phases are **independent of each other** and can run in parallel. 0.3 in particular is desk-and-bench work that isn't blocked by anything installing.

## 0.1 Toolchain proof

- [x] Determine the OS version required by the UAV Toolbox Support Package for PX4 Autopilots ✅ 2026-08-13
    - [ ] ⚠️ This single answer fixes your ROS 2 distribution _and_ your Gazebo version. Nothing else gets installed until it's settled.
    - [ ] Ubuntu 24.04 → ROS 2 Jazzy + Gazebo Harmonic (preferred pairing)
    - [x] Ubuntu 22.04 → ROS 2 Humble + older Gazebo (more version friction) ✅ 2026-08-13
- [x] Record the chosen stack in a version lockfile: Ubuntu / ROS 2 / Gazebo / PX4 / MATLAB release / support package version ✅ 2026-08-13
- [x] Clone PX4 **v1.15.4** — this is the working tree ✅ 2026-08-13
- [x] Build and fly stock: `make px4_sitl gz_x500`, connect QGroundControl, confirm arm → takeoff → land ✅ 2026-08-13
    - [x] No MATLAB involved at this step. Prove PX4 alone works first. ✅ 2026-08-13
- [x] Clone PX4 **main** separately as a **read-only reference tree** ✅ 2026-08-13
    - [x] Source of design intelligence for the Vision Target Estimator and the `moving_platform` world ✅ 2026-08-13
    - [x] ⚠️ Never build from this tree. It exists to be read. ✅ 2026-08-13
- [x] Install the MATLAB support package and run one of its bundled examples **unmodified** ✅ 2026-08-14
- [x] Build a **trivial passthrough** Simulink model (does nothing interesting) and generate `px4_sitl_default` ✅ 2026-08-14
- [x] Launch that MATLAB-built tree against Gazebo **from the terminal**, not through the wizard ✅ 2026-08-14
    - [ ] ⚠️ **Highest-risk unknown in the project.** The wizard only knows how to launch jMAVSim. This combination is not a documented workflow.
- [x] Cross-compile the same trivial model for the `kakuteh7` target via the "My board is not listed here" path ✅ 2026-08-14
    - [x] Compile only. Do not flash. ✅ 2026-08-14
    - [x] ⚠️ MathWorks explicitly advises against manufacturer-supported autopilots, which is what the Kakute H7 is. ✅ 2026-08-14
- [x] Initialise a git repository for your own code: ground station, vehicle models, Simulink models, launch files ✅ 2026-08-14

> [!success] Exit criterion A Simulink-generated module flies the x500 in Gazebo, **and** the identical model cross-compiles for the Kakute H7.

> [!warning] Risks and fallbacks If the hand-launched Gazebo path fights you, budget time to patch the build output layout — the pieces are individually well-established, the combination isn't. If the `kakuteh7` target proves intractable, **decide the fallback board now**, while there is still time to change hardware. Candidates: Kakute H7 Mini (same MCU, similar weight) or a Pixhawk-standard board at a weight penalty.

## 0.2 Link budget

Note: **bandwidth and latency are different problems.** Both matter, for different reasons.

- [ ] Compute required throughput
    - [ ] `ODOMETRY` @ 50 Hz — roughly 100 bytes framed, so ~40 kbps
    - [ ] `LANDING_TARGET` or `FOLLOW_TARGET` @ 20–30 Hz
    - [ ] Heartbeats, status text, and baseline telemetry
    - [ ] Sum with margin
- [ ] Compare against candidate radios' **usable** throughput, not raw baud rate
    - [ ] ⚠️ A 57600-baud SiK radio does not deliver 57.6 kbps of payload once overhead and retries are accounted for
- [ ] Bench-measure round-trip latency on the candidate link
- [ ] ❓ Decide: SiK radio vs WiFi MAVLink bridge
    - [ ] An ESP32 running serial-to-WiFi MAVLink passthrough is grams and milliwatts. **This is not a companion computer** — it runs no ROS, no DDS, no logic. It is a radio with a different physical layer.
    - [ ] Indoors in a mocap volume you do not need long range, so the range/bandwidth trade favours WiFi
- [ ] Record the decision, the measured numbers, and the reasoning

> [!success] Exit criterion A chosen link with a **measured** throughput and latency figure that supports 50 Hz `ODOMETRY` with margin. EKF2 will not fuse external vision below 30 Hz, so this is a hard floor, not a target.

## 0.3 Propulsion characterisation

Source: existing experimental motor and propeller data. These parameters are **fixed** — motors and props are not changing.

- [ ] Fit `motorConstant` — `T = k_T · ω²`, ω in rad/s (`ω = RPM · 2π/60`), thrust in newtons, per rotor
- [ ] Fit `momentConstant` — the plugin treats this as a length: `τ = momentConstant × thrust`
    - [ ] From torque data: fit `τ = k_Q · ω²`, then `momentConstant = k_Q / k_T`
    - [ ] Typical values land near 0.016 m — use as a sanity check, not a substitute
- [ ] Determine `maxRotVelocity` — maximum ω in rad/s at nominal pack voltage
- [ ] Determine `timeConstantUp` and `timeConstantDown` from a throttle step with RPM logging
    - [ ] Spin-down is normally slower than spin-up, which is why they are separate fields
    - [ ] ⚠️ If this isn't in your dataset, **measure it**. It directly bounds your achievable attitude bandwidth.
- [ ] Fit an **electrical power model** — `P_elec` as a function of ω, or directly of thrust
    - [ ] This is what turns your energy metric from a proxy into a measurement traceable to your hardware
    - [ ] Requires voltage and current alongside thrust and RPM in the dataset
- [ ] Document units and cite the source dataset for every fitted value

> [!tip] Why static bench data is the right data Your measurements are almost certainly static, with no inflow. Gazebo's motor model also assumes no advance ratio, so the two match well. This would be a serious mismatch for fast forward flight; it is not one for landing-approach speeds.

## 0.4 Vehicle model

- [ ] Create a **single vehicle parameter file** — YAML or MATLAB struct — as the sole source of truth
- [ ] Write a script that generates the SDF from that file
    - [ ] ⚠️ Never hand-edit the SDF. Otherwise the day comes when the controller is tuned for 1.4 kg and the simulator is flying 1.2 kg.
    - [ ] The same file should feed your Simulink model's parameters, so sim and controller cannot disagree
- [ ] Enter provisional mass (weigh it), inertia tensor (component-based estimate), and CG offset
    - [ ] Flag these explicitly as provisional — they change in Phase 7
- [ ] Spawn in Gazebo and run the **hover check**
    - [ ] Read hover throttle. It should sit near 40–50% for a thrust-to-weight around 2.
    - [ ] ⚠️ If it hovers at ~5%, you've hit the `rotorVelocitySlowdownSim` scaling ambiguity — divide `motorConstant` by 100 and re-check.
    - [ ] This two-minute test is decisive and beats resolving the plugin's internals

> [!success] Exit criterion The model hovers at a plausible throttle, the SDF is generated rather than hand-written, and thrust-to-weight matches your physical expectation.

---

# Phase 1 — Build the world

> [!info] Goal Get both vehicles into a single Gazebo world, with the Voyager exposing exactly the ROS 2 interface the real robot does.

- [ ] Author the Voyager SDF
    - [ ] Links, masses, inertias
    - [ ] Collision geometry — **including the landing deck surface**, since that's what the drone will touch
    - [ ] Crib from PX4's gz rover models as a starting point
- [ ] Add the `gz-sim` diff-drive system plugin
    - [ ] ⚠️ Not `libgazebo_ros_diff_drive.so` — that's the Gazebo Classic plugin and will not wire up. Most online tutorials still show it.
- [ ] Bridge `/cmd_vel` and odometry through `ros_gz_bridge`
- [ ] Drive it around an empty world with `teleop_twist_keyboard`
- [ ] Start Gazebo **standalone** with your world, then attach PX4 SITL so the drone spawns into it
- [ ] Verify you can restart PX4 **without** reloading the world
    - [ ] This is a large iteration win — you will restart PX4 constantly

> [!success] Exit criterion Drive the Voyager with a keyboard while the drone hovers in the same world, and restart PX4 independently of Gazebo.

---

# Phase 2 — Ground station skeleton

> [!info] Goal Prove the pose pipeline and, critically, prove the coordinate frame conversion — before anything depends on it.

- [ ] Create the ROS 2 package scaffold
- [ ] Define both adapter **interfaces**: `PoseSource` and `VehicleLink`
    - [ ] Implement only the Gazebo pose source and the UDP MAVLink link for now
    - [ ] OptiTrack and serial-radio implementations arrive in Phase 7 and change nothing above them
- [ ] Bridge the drone's ground-truth pose from Gazebo
- [ ] Convert to PX4's frame — **in exactly one place in the codebase**
    - [ ] PX4 uses FRD: X forward, Y right, Z down
    - [ ] For orientation, keep the quaternion scalar part `w` unchanged and swap `x`, `y`, `z` the same way you swapped position axes
    - [ ] Write **unit tests** against known vectors before flying anything
- [ ] Stamp messages with **capture time**, never `now()` at arrival
    - [ ] ⚠️ Arrival-time stamping folds network jitter into your estimator and is the root of most divergence problems
- [ ] Stream `ODOMETRY` at 50 Hz to `udp://127.0.0.1:14540`
- [ ] Configure EKF2 for external vision
    - [ ] Enable external vision position and yaw aiding
    - [ ] Set height mode to vision
    - [ ] Disable GPS aiding
    - [ ] Decide magnetometer handling
- [ ] Fly on external vision alone — hold position, then fly a square

> [!success] Exit criterion The drone holds position and flies a commanded square in simulation using **only** your pose stream, with GPS aiding disabled.

> [!warning] Risk Frame bugs present as "the vehicle flies toward the wrong wall" and are miserable to find once scattered through a codebase. Validate axis-by-axis with single-axis translations before attempting any manoeuvre.

---

# Phase 3 — Make the drone aware of the Voyager

> [!info] Goal Pure plumbing. No control logic in this phase at all.

- [ ] ❓ Decide the message: `LANDING_TARGET` or `FOLLOW_TARGET`
    - [ ] `LANDING_TARGET` → `landing_target_pose` uORB; the precision-landing plumbing already consumes it
    - [ ] `FOLLOW_TARGET` → `follow_target` uORB; carries target **velocity**, which is what you want for a moving platform
    - [ ] Record the choice and the reasoning
- [ ] Add the second MAVLink stream carrying the Voyager's pose (and velocity, if `FOLLOW_TARGET`)
    - [ ] 20–30 Hz is sufficient — the platform's dynamics are far slower than the aircraft's
- [ ] Confirm the uORB topic populates: `listener landing_target_pose` on the PX4 console
- [ ] Drive the Voyager by teleop and watch the onboard values track it
    - [ ] Check **signs and frame**, not just that numbers are moving

> [!success] Exit criterion Onboard values track the Voyager's motion with the correct sign and in the correct frame.

---

# Phase 4 — Guidance module (stage A)

> [!info] Goal The state machine, running on top of PX4's known-good position controller. If the vehicle misbehaves here, the bug is in your logic, not your control law.

Output: `trajectory_setpoint`. PX4's position controller stays in the loop.

## 4.1 Instrumentation first

- [ ] Publish a status topic (`debug_vect` or `debug_array`) carrying:
    - [ ] Current state index
    - [ ] Relative position
    - [ ] Relative velocity
    - [ ] Time since last target update
- [ ] ⚠️ Do this **before** the logic. Without it, diagnosing a failed attempt is guesswork.

## 4.2 Build the states in order

- [ ] Takeoff and hold
- [ ] Approach a **stationary** Voyager
- [ ] Track a stationary Voyager (station-keeping above it)
- [ ] Track a Voyager driving at **constant velocity in a straight line**
- [ ] Descend
- [ ] Touchdown

## 4.3 Safety and robustness

- [ ] **Staleness guard** — if the target pose age exceeds a timeout, abort
    - [ ] ⚠️ Add this **before** the first moving descent, not after. The alternative is descending toward where the Voyager _was_.
    - [ ] Short coast on the last estimate, then a hard timeout — borrow the shape from PX4's Vision Target Estimator
- [ ] Abort path: climb away and return to Approach
- [ ] **Hysteresis** on every state transition
    - [ ] Without it, the vehicle oscillates between Track and Descend indefinitely
- [ ] Transition guards on **sustained** conditions, not single samples
- [ ] Tolerance window **tightens as altitude decreases**
    - [ ] Half a metre of lateral error at 3 m is recoverable; at 20 cm it's a tipover
- [ ] **Feedforward** of target velocity
    - [ ] Pure feedback will always lag a moving platform

> [!tip] Framing Work in the target's frame. Your state is relative position and relative velocity. Written this way, the moving platform becomes a regulation problem and the target's motion enters as feedforward, rather than a disturbance you chase forever.

> [!success] Exit criterion A repeatable landing on a Voyager moving in a straight line at constant velocity.

---

# Phase 5 — Harden and instrument

> [!info] Goal The first successful landing is usually luck. This phase turns it into evidence.

## 5.1 Contact physics

- [ ] Set friction coefficients and restitution in the SDF for the landing feet and the deck surface
    - [ ] ⚠️ These are normally left at unvalidated defaults. "Does it stay on the moving robot after touchdown" is arguably your success criterion, so they deserve real attention.

## 5.2 Realistic timing

- [ ] Inject **tunable** artificial latency, jitter and dropouts into the sim pose adapter
    - [ ] Gazebo hands you zero-latency ground truth by default — the one condition you will never have in the lab
    - [ ] Test both perfectly-timed and deliberately mis-timed

## 5.3 Energy metric

- [ ] Log `actuator_motors`
- [ ] Map each normalised command back to ω through the same relation the plugin uses
- [ ] Run through the **measured power curve** from 0.3
- [ ] Integrate over the manoeuvre
- [ ] ⚠️ Note the known omissions: no battery voltage sag, no ground effect. This is a **relative comparison metric between control strategies**, not an absolute prediction.

## 5.4 Scoring and test harness

- [ ] Define the metrics precisely:
    - [ ] Touchdown position error relative to deck centre
    - [ ] Relative velocity at contact
    - [ ] Time from arm to touchdown
    - [ ] Energy consumed
    - [ ] Outcome classification: success / abort / crash / slid off
- [ ] Script the whole pipeline headless (`HEADLESS=1`)
- [ ] Run N ≈ 50 with varied Voyager speeds and approach geometries
- [ ] Run a **sensitivity sweep**: mass ±20%, inertia ±30%
    - [ ] If performance barely moves, you can stop worrying about the airframe changes entirely
    - [ ] If it moves a lot, that's a genuine robustness finding worth writing up
- [ ] Define a results archive format so runs are comparable across months

> [!success] Exit criterion A success rate and an energy distribution across many runs, plus a known sensitivity to the parameters that are still going to change. Not a single video.

---

# Phase 6 — Take over the outer loop

> [!info] Goal Replace PX4's **position controller** with yours, on an identical benchmark.

> [!warning] Correction This phase replaces the **position/velocity controller**, not the attitude controller. Publishing `vehicle_attitude_setpoint` means you emit thrust and desired tilt, and PX4's attitude and rate loops continue underneath. The custom **attitude** controller is Phase 8.

- [ ] Move the module's output from `trajectory_setpoint` to `vehicle_attitude_setpoint`
- [ ] Verify the correct PX4 module is displaced in the startup script
    - [ ] ⚠️ Your module must replace `mc_pos_control`, not compete with it for the same uORB topic
    - [ ] Test this with a trivial passthrough before adding real logic
- [ ] Re-run the **Phase 5 test suite unchanged**
- [ ] Compare against the Phase 5 baseline on identical scenarios

> [!success] Exit criterion Your position controller matches or beats PX4's on the same benchmark. This is a result, not an assertion — which is the whole reason for building the harness first.

---

# Phase 7 — Hardware, in stages

> [!info] Goal Move to the real aircraft one risk at a time.

## 7.1 Airframe parameter identification

These are the **volatile** parameters — the ones deferred from Phase 0.

|Parameter|Method|Status|
|---|---|---|
|Mass|Weigh it|Will change|
|Inertia tensor|CAD model, or bifilar pendulum|Will change|
|CG offset|Balance test|Will change|

- [ ] Measure all three on the final airframe
- [ ] Update the vehicle parameter file, regenerate the SDF
- [ ] Re-run the Phase 5 suite against the corrected model
    - [ ] By now this costs one command rather than a week

## 7.2 Bring-up

- [ ] Flash the PX4 bootloader, **then** the firmware
- [ ] **Props off**: verify motor order, motor direction, RC link, failsafes, kill switch
- [ ] Manual flight, no mocap — confirm the airframe is sound and tune the rate loop
- [ ] ⚠️ Do not proceed until the aircraft flies well manually

## 7.3 Mocap integration

- [ ] Configure Motive
    - [ ] Set Z-up if your licence allows — makes the frame conversion trivial rather than a two-stage rotation
    - [ ] Define rigid bodies for both the drone and the Voyager
- [ ] Write the NatNet `PoseSource` implementation
    - [ ] ⚠️ Nothing above the adapter should change. If it does, the abstraction leaked.
- [ ] Establish time synchronisation between the mocap PC and the ground station (NTP or PTP)
- [ ] Hover on external vision only
- [ ] Tune `EKF2_EV_DELAY`
    - [ ] It represents the difference between your vision timestamp and the actual capture time on EKF2's IMU clock
    - [ ] Rough estimate: compare IMU and EV rates in the logs (enable the computer-vision bit in `SDLOG_PROFILE`)
    - [ ] Refine by finding the value giving the lowest EKF innovations during dynamic manoeuvres
    - [ ] Technically zero if timestamping and time sync are perfect — in practice they never are
- [ ] Test a **sustained** hover, over two minutes, watching for slow divergence

## 7.4 Landing campaign

- [ ] Low-altitude or tethered first
- [ ] Stationary Voyager
- [ ] Moving Voyager, increasing speed incrementally
- [ ] **Verify the abort path in flight** — deliberately occlude a marker mid-descent
    - [ ] ⚠️ Do this early and at low altitude. Discovering the guard doesn't work during a real dropout is not the way to learn.

> [!danger] Safety Kill switch reachable at all times. Someone on the RC throughout. Netting or tether for early attempts. Prop guards. Never trust a first flight of anything.

> [!success] Exit criterion The same manoeuvre validated in simulation, executed in the lab, with logs that let you compare the two directly.

---

# Phase 8 — Deeper control (optional)

> [!info] Goal Only worth doing on a proven baseline, and only if your contribution requires it.

## 8.1 Custom attitude controller

- [ ] Verify timing with **processor-in-the-loop** on the actual H743 first
    - [ ] ⚠️ 50 Hz generated code is trivial on an H743. 250 Hz alongside EKF2 is not automatically fine. Measure before attaching motors.
- [ ] Move output to `vehicle_rates_setpoint`
- [ ] Re-run the Phase 5 suite and compare

## 8.2 Custom estimator

- [ ] Run **in parallel** with EKF2 — never as a swap
    - [ ] Leave EKF2 enabled and publishing normally
    - [ ] Publish your filter to a **separate** topic
    - [ ] Let the controller choose via a parameter
- [ ] ⚠️ `vehicle_local_position` and `vehicle_attitude` are consumed by commander, navigator, the land detector, the geofence, and every arming check. Replacing them is not a contained change.
- [ ] Score both estimators against mocap ground truth on every flight
    - [ ] OptiTrack gives you a reference most estimator work doesn't have — use it
- [ ] Only disable EKF2 once yours is demonstrably as good

---

# Continuous practices

Not a phase — these run throughout.

- [ ] **The logging triple**, on a common time base:
    - [ ] ULog from the aircraft
    - [ ] rosbag from the ground station
    - [ ] Raw mocap stream as ground truth
    - [ ] This is what lets you say "the estimator was accurate to X, the controller tracked to Y, the manoeuvre cost Z joules". Much harder to reconstruct after the fact than to set up now.
- [ ] Keep the version lockfile current
- [ ] Maintain a decision log — every ❓ resolved, with the reasoning
- [ ] Every simulation result reproducible from a scripted run

---

# Open decisions

|#|Decision|Blocks|Status|
|---|---|---|---|
|1|MATLAB support package OS support|ROS 2 and Gazebo versions — everything|Open|
|2|Kakute H7 target buildability|Board choice; possible hardware change|Open|
|3|Telemetry link: SiK vs WiFi bridge|Hardware order|Open|
|4|`LANDING_TARGET` vs `FOLLOW_TARGET`|Phase 3 implementation|Open|
|5|Whether the `moving_platform` plugin ports back to v1.15|Nice-to-have only — the Voyager _is_ the moving platform|Open|
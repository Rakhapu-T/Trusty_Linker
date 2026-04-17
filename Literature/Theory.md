---
tags:
  - notes
---
# Sliding Mode Control
Here is a MATLAB ted talk by Brian Douglas to explain the operation:
<iframe width="560" height="315" src="https://www.youtube.com/embed/RD-2oiwEbDo?si=HoarjUeKIAlFwj0c" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Watch the video to build some intuition!

### Mathematical Derivation
1. For an n dimensional state system. where $x$ is the $n \times 1$ vector of the controllable system states. Rewrite the system in the following form:
$$\dot{x} = f(x) + g(x)u(t)$$
	The above form can be used for both linear and non-linear systems.

2. Define a switching function $S = C^T x$ (Linear combination of all of the states):

3. Define S such that $S \cdot S <0$ (Readability condition):
$$\dot{S} = h(S(x))$$
$$\dot{S} = \frac{\partial S}{\partial x} \frac{\partial x}{\partial t} = C^T \cdot [\dot{x}] = C^T \cdot [f(x) + g(x)u(t)]
$$
$$\implies u(t) = (C^T g(x))^{-1}(-C^Tf(x) + h(S(x)))$$
Where the equation above is the standard sliding mode control compensator formular.


# PID Control
![[control.jpeg]]

## Integrator Anti windup
In control systems, **integrator windup** is a problem that occurs when a controller with an integral term (like a PI or PID controller) faces a physical limit—such as a motor reaching its maximum speed or a valve being fully open.

When the actuator saturates and can no longer increase its output, the "error" between the setpoint and the actual value persists. The integral term, however, doesn't know the hardware has hit a wall; it keeps "winding up" (accumulating) that error over time. By the time the setpoint is reached, the accumulated integral value is so huge that the controller keeps the actuator saturated in the wrong direction for a long time, causing a massive overshoot.

**Anti-windup** is the logic used to stop this accumulation the moment saturation is detected.

The following list contains common anti windup techniques:
1. Clamping
2. Back-calculation
3. Observer approach

### 1. Clamping
This is simply turning the integrator off. A conditional Integration schematics is shown below:
![[clamping_scematic.png]]

## Noise Filtering
The derivative path amplifies high frequency noise. Hence it is of best interest to use a LPF before differentiating. The diagram below summarizes the process. It also shows alternative implementations of this where the LHS approach contains easier to code and understand logic whilst the RHS contains a more computationally efficient algorithm.
![[Noise_Filtering.png]]

## PID Tuning (Overview)
The Flow chart below summarizes the processes and techniques used in PID Tuning:
![[PID_Tuning_Flowchart.png]]

An overview of the PID Tuning is explained in the following video:
<iframe width="560" height="315" src="https://www.youtube.com/embed/sFOEsA0Irjs?list=PLn8PRpmsu08pQBgjxYFXSsODEF3Jqmm-y" title="A PID Tuning Guide | Understanding PID Control, Part 4" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

## Generating a mathematical model for tuning

There exists many methods for determining the system model. Popular ones include:
1. Deriving the system model from scratch using first principles
2. Deriving the system model using system ID (Black box approach)
3. Linearization

These are all summarized in this video:
<iframe width="700" height="350" src="https://www.youtube.com/embed/qhIjIu-Zk10?list=PLn8PRpmsu08pQBgjxYFXSsODEF3Jqmm-y" title="3 Ways to Build a Model for Control System Design | Understanding PID Control, Part 5" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>


## PID Tuning (Using a mathematical model)
The video below Demonstrates 3 methods for PID tuning using system models:
<iframe width="700" height="350" src="https://www.youtube.com/embed/qj8vTO1eIHo?list=PLn8PRpmsu08pQBgjxYFXSsODEF3Jqmm-y" title="Manual and Automatic PID Tuning Methods | Understanding PID Control, Part 6" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

This video (0:00 - 13:31) explains how to systematically tune a **PID controller** using a mathematical model of a system, moving beyond basic trial-and-error guessing. It highlights that PID tuning is essentially about positioning two zeros and adjusting the loop gain.

**Key Tuning Methods Discussed:**

- **Manual Tuning via Pole Placement (5:01):** Using the **Root Locus** plot to graphically place the closed-loop poles where you want them for a desired response.
- **Manual Tuning via Loop Shaping (6:03):** Using the **Bode plot** to adjust the frequency response characteristics.
- **Automatic Tuning (10:54):** Utilizing software tools, such as the **PID Tuner app** in Simulink, to quickly find optimal gains based on desired speed and robustness requirements.

**Important Considerations:**

- **Actuator Saturation (9:06):** The video demonstrates that a fast theoretical response can lead to high controller effort that exceeds physical limits (like voltage limits). It emphasizes checking for saturation to ensure the design is actually feasible.
- **Model Validation (12:21):** The video verifies that gains tuned on a linearized model can successfully control the nonlinear physical system.

# State-Space Equations

State-space models are numerically efficient, to solve, can handle complex systems, allow for a more geometric understanding of dynamic systems, and form the basis for much of modern control theory.

An introduction is summarized in the following video:
<iframe width="700" height="350" src="https://www.youtube.com/embed/hpeKrMG-WP0" title="Introduction to State-Space Equations | State Space, Part 1" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
The three video series Gives a nice overview of State space modelling of Dynamic Systems. This is especially useful as a introduction or a refresher. A more in depth Analysis can be found in the following you-tube playlist of a Lecture series on Modelling by Dr. Rick Hill:
https://www.youtube.com/watch?v=vwso-xHLNGc&list=PL4VMBEQr3gME29eeHZyT_lDw3KCdAmwXR

More specifically, for State-Space Equations start from Module 27a - Introduction to State-Space Modelling:
<iframe width="700" height="350" src="https://www.youtube.com/embed/X3TOZLJCWiY?list=PL4VMBEQr3gME29eeHZyT_lDw3KCdAmwXR" title="System Dynamics and Control: Module 27a - Introduction to State-Space Modeling" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

## Useful slides (Quick Summary):
The general form of a state space model is illustrated in the slide below:
![[State-space_Modeling.png|630]]
**Note:** 
- The state equations are first order, and comprise of functions of state variables $x_i$ and inputs $u_i$
- the functions $f_n(\vec x, \vec u)$ can be non-linear and can be time varying.
- The coefficients of the functions can be time varying (non-constant)
- The output equations are algebraic equations (No derivatives)
- The output equation is a function of state variables $x_i$ and inputs $u_i$

For Linear Systems, State Models can be written as Matrices:
$$
	\vec x = \mathbf{A} \vec{x} + \mathbf{B}\vec u
$$
$$
	\vec y = \mathbf{C} \vec{x} + \mathbf{D}\vec u
$$


![[State-space_to_tf.png]]


![[Poles_of_a_ss_function.png]]

![[tf_to_ss.png]]

![[Summary_of_SS.png]]


# Quaternions

The best explanation for Quaternion Double-Cover (That I could find) can be found [here](https://www.reedbeta.com/blog/why-quaternions-double-cover/).
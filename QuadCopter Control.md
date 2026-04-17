
# Quadcopter Dynamics

## Quadcopter frames
There's two types of quadcopters:
- X-shaped
- H-shaped

These are Illustrated in the figure below:
![[Quadcopter frames.png]]


## Reference frames 
The Body frame of the quadcopter is typically placed at the center of mass of the quadcopter which simplifies the mathematics.

There are two common ways for determining the orientation of the the body frame {B}:
1. align $\hat x_B$ and $\hat y_b$ axes such that they are parallel to the rotor arms (see Figure 9.2 left), or
2.  align $\hat x_B$ and $\hat y_b$ 1. axes such that the axes have been rotated by $45^{\circ}$ about $\hat z_B$ away from the rotor arms (see Figure 9.2 right)
![[QuadcopterReferenceFrame.png]]

The first orientation is simpler to reason about (more intuitive) however the second representation has benefits such as increasing rolling/pitching moments, and also offering failure redundancy during motor/propeller failures. 

## Rotor thrust and torque equations
TBD

## Rotor Speeds
Each rotor is driven by an electric speed controller (ESC) that controls the rotational velocity of the propellers. Each rotational velocity $w_i$ is illustrated in the figure below:
<a name="fig9.4"></a>![[Rotor_Speed_Figure.png]]

There is typically a delay between the desired motor speed $\omega^*_i$ and the actual motor speed $\omega_i$. The relation between these two quantities in the Laplace Domain is thus:
$$
	\bar{\omega}_i(s) = \bar{\omega^*}_i(s)e^{-st_d}
$$
where $t_d$ is the delay period.

## Rotor Thrust equation
In the case of NWU axis convention, the the each rotor's thrust acts parallel $\mathbf{\hat{z}}_B$. Based on blade element theory and momentum theory, the thrust produced by rotor i is approximately given by:
$$f_i = \frac{1}{6} \rho c n C_L \bar{\omega}_i^2 R^3,$$
The quantities in the above equation are summarized as follows:
- $\mathbf{f_i:}$ The thrust excerted by rotor i
- $\mathbf{\rho}:$ The air density
- $\mathbf{c}:$ The average cord length of the propeller
- $\mathbf{n}:$ The number of propeller blades
- $C_L:$ The lift coefficient
- $\mathbf{\bar \omega_i}$ : Angular velocity of the $i^{th}$ rotor
- $\mathbf{R}$: Radius of the propeller

Assuming the quad-copter is operating in a limited volume, the above quantities (except for rotor velocity) can be approximated as being constant, yielding the following compact form:
$$
	f_i = k_f \bar \omega^2_i
$$
where $k_f = \frac{1}{6} \rho c n C_L R^3$ is a quantity referred to as the thrust coefficient. 

## Rotor torque equation
 The torque produced by the $i^{th}$rotor is approximately given by:
 $$
	\tau_i = (-1)^{i+1}\frac{1}{8}pcnC_D\bar{\omega}^2_iR^4 = (-1)^{i+1}k_\tau\bar\omega^2_i
$$
where $C_D$ is the drag coefficient and $k_\tau = \frac{1}{8}pcnC_DR^4$ is referred to as the torque coefficient. 

## Attitude Dynamics
The thrust produced by each rotor also causes a moment on the rigid body, based on:
$$
	m_i = lf_i
$$
where l is the (common) lever arm distance from the centre of mass to the rotor centre:
![[Thrust_Induced_Moments.png]]

Using the rotor direction convention Illustrated in [[QuadCopter Control#Rotor Speeds| Rotor Speeds]], the resulting torques about $\mathbf{\hat x}_B$ from the rotor thrust is given by:
$$
\begin{array}{ll}
m_x 
&=& l\left(f_4-f_2\right),\\
&=& lk_f\left(\overline\omega^2_4-\overline\omega^2_2\right).
\end{array}
$$

Similarly, the resulting torques about $\mathbf{\hat y}_B$ is:
$$
\begin{array}{ll}
m_y 
&=& l\left(f_3-f_1\right),\\
&=& lk_f\left(\overline\omega^2_3-\overline\omega^2_1\right).
\end{array}
$$
![[Torques.png]]

Withe reference to the above figure, the total torque about $\mathbf{\hat z}_B$ is a result of summing up all the rotor torques. This is given by:

$$
\begin{array}{ll}
m_z
&=&\sum_{i=1}^4\tau_i,\\
&=& k_\tau\left(\overline\omega_1^2-\overline\omega_2^2+\overline\omega_3^2-\overline\omega_4^2\right),\\
&=&k_\tau\sum_{i=1}^4\left(-1\right)^{i+1}\overline\omega^2_i.
\end{array}
$$
Note again that the torque produced by each rotor is in the direction opposite to the propeller's rotational direction. The resulting torque vector follows as:
$$
{^B{\bf m}}= \left[\begin{array}{cccccccc}    m_x \\    m_y \\ m_z \end{array}\right],
$$

which can also be written as a function of the rotor speeds by:
$$
{^B{\bf m}}= \left[\begin{array}{cccccccc}    lk_f\left(\overline\omega^2_4-\overline\omega^2_2\right) \\    lk_f\left(\overline\omega^2_3-\overline\omega^2_1\right) \\ k_\tau(\left(\overline\omega_1^2-\overline\omega_2^2+\overline\omega_3^2-\overline\omega_4^2\right) \end{array}\right].
$$


## Rotational Dynamics
Based on Euler's rotational equation, the rotational dynamics of a generalized rigid body is given by:
$$
{\bf J}{^B\dot{\bf \omega}}+{^B{\bf \omega}}\times \left( {\bf J} {^B{\bf \omega}} \right) = {^B{\bf m}},
$$
where:
- $\mathbf{J}$ is the symmetric, positive-definite inertial matrix.
- $\mathbf{^B\omega}$ describes the angular velocity of the rigid body in B
- $\mathbf{^B m}$ is the applied moment in {B}

The above equation applies for any rigid body in question (It extends beyond just quad-copters). The second term in the equation above, namely ${^B{\bf \omega}}\times \left( {\bf J} {^B{\bf \omega}} \right)$, is referred to as the centripetal moment and arises because we are describing the dynamics in {B}, which is a non-inertial reference frame. 

**Note**: **J** is approximately diagonal (By design) is approximated as follows:
$$
{\bf J}\approx \left[\begin{array}{cccccccc}    J_{xx} & 0 & 0 \\    0 & J_{yy} & 0\\ 0 & 0 & J_{zz}  \end{array}\right]=\left[\begin{array}{cccccccc}    J_{xx} & 0 & 0 \\    0 & J_{xx} & 0\\ 0 & 0 & J_{zz}  \end{array}\right].
$$


## Obtaining Orientation
For the purposes of modelling and simulation, one needs to be able to solve Euler's rotational equation {${\bf J}{^B\dot{\bf \omega}}+{^B{\bf \omega}}\times \left( {\bf J} {^B{\bf \omega}} \right) = {^B{\bf m}}$},  for rotational acceleration in order to ultimately determine the resulting orientation over time. If the applied moment, $^B\mathbf{m}(t)$, and current angular velocity, $^B\mathbf{\omega(t)}$, are known at time t, one can then solve for $^B \dot {\omega}(t)$. The updated angular velocity at time  $t + \Delta t$ is then obtained via integration of the rotational acceleration based on:
$${^B{\bf \omega}}(t+\Delta t)={^B{\bf \omega}}(t)+\int_{t}^{t+\Delta t} {^B\dot{\bf \omega}}(t)dt.$$
Once the angular velocity has been determined, The corresponding incremental quaternion, describing the incremental rotation that took place over $\Delta t$ units of time, follows as:
$$
\begin{array}{cc}\Delta{\mathbf q} = \left[\begin{array}{cc}    \cos{\frac{\Delta\alpha}{2}} \\ \sin{\frac{\Delta\alpha}{2}} {^B\hat{\mathbf \omega}}     \\ \end{array}\right] =     \left[\begin{array}{cc}    \cos{\frac{|{^B{\bf \omega}}|\Delta t}{2}} \\     \sin{\frac{|{^B{\bf \omega}}|\Delta t}{2}} \hat{\omega}_x \\    \sin{\frac{|{^B{\bf \omega}}|\Delta t}{2}} \hat{\omega}_y \\    \sin{\frac{|{^B{\bf \omega}}|\Delta t}{2}} \hat{\omega}_z    \\ \end{array}\right].\end{array}
$$

where:
$$
\Delta \alpha=|{^B{\bf \omega}}|\Delta t,
$$

Finally, the absolute quaternion describing the updated pose of the quadcopter in ${W}$ is determined using:
$$
^W{\bf q}(t+\Delta t)=^W{\bf q}(t)\otimes \Delta {\bf q}
$$
where $^W{\bf q}(t)$ represents the orientation of the quadcopter prior to the incremental rotation taking place.

## Summary of Rotational Dynamics
The rotational dynamics of:
$${\bf J}{^B\dot{\bf \omega}}+{^B{\bf \omega}}\times \left( {\bf J} {^B{\bf \omega}} \right) = {^B{\bf m}},$$
including its dependence of rotor speed can be expressed up until the orientation level as shown in [Figure 9.7](#M_1fd8), where
$$
{{\bf A}}  

= \left[\begin{array}{cccccccc}   

0 & -lk_f & 0 & lk_f \\
-lk_f &0 & lk_f & 0\\
k_\tau & -k_\tau & k_\tau & -k_\tau 
\end{array}\right],
$$
Based on
$$
{^B{\bf m}}  
=\left[\begin{array}{cccccccc} m_x \\m_y\\m_z  \end{array}\right]
= \left[\begin{array}{cccccccc}   

0 & -lk_f & 0 & lk_f \\
-lk_f &0 & lk_f & 0\\
k_\tau & -k_\tau & k_\tau & -k_\tau 
\end{array}\right]
\left[\begin{array}{cccccccc} \overline\omega_1^2 \\ \overline\omega_2^2 \\ \overline\omega_3^2 \\ \overline\omega_4^2 \end{array}\right].
$$
![[RotationDynamicsBlockDiagram.png]]
**Note** that the rotation matrix form of orientation update is only used for sake of notational convenience. In practice it would still be more pragmatic to make use of the quaternion counterpart.

## Translational Dynamics
**TBD**


---
# Drone Control Overview
In the case of classical control theory, it is common practice to design two separate controllers — one for the rotational system, and one for the translational system. Because the quadcopter is only able to traverse in the $\hat{\bf x}_B$- $\hat{\bf y}_B$ plane as a result of a roll or pitch motion, the attitude controller can be thought of an inner loop, with the translational controller considered as the outer loop. This is often referred to as a cascaded control scheme. The figure below gives a high-level illustration of this concept:
![[HighLevelBlockDiagram.png]]

The following steps will be followed during the control design:
1. **control allocation** — mapping desired thrust and torque vectors to the rotor speeds;
2. **dynamic analysis** — assessing the dynamics of the system in order to inform an appropriate flight envelope;
3. **attitude control design** — design a controller that can stabilise the orientation of the quadcopter whilst also meeting the prescribed specifications;
4. **thrust vectoring** — relate the position control command to the orientation error in order to dictate orientation changes required;
5. **position control design** — design a controller that can stabilise the translation of the quadcopter whilst also meeting the prescribed specifications;


---
# Attitude control

## 1. Control allocation
The controllable torque vector is as follows:
$$
{^B{\bf m}}= \left[\begin{array}{cccccccc}    lk_f\left(\overline\omega^2_4-\overline\omega^2_2\right) \\    lk_f\left(\overline\omega^2_3-\overline\omega^2_1\right) \\ k_\tau(\left(\overline\omega_1^2-\overline\omega_2^2+\overline\omega_3^2-\overline\omega_4^2\right) \end{array}\right],
$$
where:
- $\mathbf{l}$ is the distance from the center of mass to each motor shaft.
- $\mathbf{k_f}$ is the thrust coefficient.
- $\omega_i$ is the is the angular velocity of rotor $i$.

Additionally, the net thrust produced by all four rotors is as follows:
$$
\begin{array}{ll}
f_\Sigma 
&=& k_f\sum_{i=1}^4\overline\omega^2_i.\\
\end{array}
$$
The torque vector is used to control the orientation of the quad-copter, whilst the net thrust controls the heave of the platform. Together these quantities dictate the transnational motion of the quad-copter. For this reason, $^B \bf m^*$ and $f^*_\Sigma$ can be thought of as virtual control inputs.

The mappings from rotor speeds to the virtual control inputs is as follows:
$$
 \left[\begin{array}{cccccccc} f_\Sigma \\{^B{\bf m}}  \end{array}\right]
=\left[\begin{array}{cccccccc} f_\Sigma \\m_x \\m_y\\m_z  \end{array}\right]
= \left[\begin{array}{cccccccc}   
k_f & k_f & k_f & k_f\\
0 & -lk_f & 0 & lk_f \\
-lk_f &0 & lk_f & 0\\
k_\tau & -k_\tau & k_\tau & -k_\tau 
\end{array}\right]
\left[\begin{array}{cccccccc} \overline\omega_1^2 \\ \overline\omega_2^2 \\ \overline\omega_3^2 \\ \overline\omega_4^2 \end{array}\right]
$$
The above relationship can be inverted to obtain the desired rotor speeds:
$$
\begin{array}{ll}
\left[\begin{array}{cccccccc} \overline\omega_1^2 \\ \overline\omega_2^2 \\ \overline\omega_3^2 \\ \overline\omega_4^2 \end{array}\right] ^*

&=& \left[\begin{array}{cccccccc}   
k_f & k_f & k_f & k_f\\
0 & -lk_f & 0 & lk_f \\
-lk_f &0 & lk_f & 0\\
k_\tau & -k_\tau & k_\tau & -k_\tau 
\end{array}\right]^{-1}\left[\begin{array}{cccccccc} f^*_\Sigma \\{^B{\bf m}^*}  \end{array}\right],\\
&=& \left[\begin{array}{cccccccc}   
\frac{1}{4k_f} & 0 & -\frac{1}{2lk_f} & \frac{1}{4k_\tau}\\
\frac{1}{4k_f} & -\frac{1}{2lk_f} & 0 & -\frac{1}{4k_\tau} \\
\frac{1}{4k_f} &0 & \frac{1}{2lk_f} & \frac{1}{4k_\tau}\\
\frac{1}{4k_f} & \frac{1}{2lk_f} & 0 & -\frac{1}{4k_\tau} 
\end{array}\right]\left[\begin{array}{cccccccc} f_\Sigma^* \\{^B{\bf m}^*}  \end{array}\right].\\
\end{array}
$$

recalling that:
$$
\overline\omega_i(s)=\overline\omega_i^*(s)e^{-st_d}
$$

## 2. Dynamic Analysis
Recall that the orientation dynamics of a quad-copter (RIGID BODY) are as follows:
$$
{\bf J}{^B\dot{\bf \omega}}+{^B{\bf \omega}}\times \left( {\bf J} {^B{\bf \omega}} \right) = {^B{\bf m}},
$$
Where:
- $$
{\bf J}=\left[\begin{array}{cc}
J_{xx} & 0 & 0 \\
0 & J_{yy} & 0 \\
0 & 0 & J_{zz}
\end{array}\right],
$$
- $${^B{\bf \omega}}\times \left( {\bf J} {^B{\bf \omega}} \right)=\left[\begin{array}{cc}
\omega_y\omega_z\left(J_{zz} -J_{yy} \right) \\
\omega_x \omega_z\left(J_{xx}   -J_{zz} \right) \\
\omega_x \omega_y\left(J_{yy}  -J_{xx} \right) 
\end{array}\right]$$
- $$^B\bf \omega =\left[\begin{array}{cc}
\omega_x \\
\omega_y\\
\omega_z
\end{array}\right]$$
Quadcopters are usually designed such that $J_{xx}\approx J_{yy}$ , which suggests that the centripetal moment reduces to:
$$
{^B{\bf \omega}}\times \left( {\bf J} {^B{\bf \omega}} \right)=\left[\begin{array}{cc}
\omega_y\omega_z\left(J_{zz} -J_{yy} \right) \\
\omega_x \omega_z\left(J_{xx}   -J_{zz} \right) \\
0
\end{array}\right]
$$

Additionally, Regulating the motion of the quad-copter about $\hat{\bf z}_B$ such that $\omega_z\approx 0$ which gives:
$${^B{\bf \omega}}\times \left( {\bf J} {^B{\bf \omega}} \right)\approx 0$$
Which removes moment coupling caused by the Centripetal moment. This leads to the simplified attitude dynamics:
$${\bf J}{^B\dot{\bf \omega}}\approx {^B{\bf m}}$$

## PID Control
**TBD**


## Determining Orientation Error
The quaternion orientation error in {B} is given by:
$$
\begin{array}{cc}{^B{\mathbf q}_D} = {^B{\mathbf q}_W} \otimes {^W{\mathbf q}_D},\end{array}
$$
The error quaternion can be re-written as:
$$\begin{array}{cc}{^B{\mathbf q}_D} =    \left[\begin{array}{cc}    \cos{\frac{\alpha_e}{2}} \\ \sin{\frac{\alpha_e}{2}} {^B\hat{\bf v}_e}     \\ \end{array}\right]\end{array}$$

With the corresponding orientation error being:
$${^B{\bf e}_o}=\alpha_e^B\hat{\bf v}_e$$
The error vector can be used in an attitude control scheme to inform rotational corrections about all three axes. The block diagram structure is shown below:
![[Quaternion_Control.png]]


## Thrust vectoring
An alternative approach to determining the orientation error of the quadcopter is to formulate the incremental orientation required in  {B} based on the direction in which we desire $\bf \hat z_B$ to point. Given that {D} describes the desired frame, we can then determine the description of  $\bf \hat z_D$ in {B}, which is expressed as $^B\hat{\bf z}_D=\hat{\bf z}^*_B$ . The incremental orientation required to rotate $\hat{\bf z}_B=[0~~0~~1]^T$ to $^B\hat{\bf z}_D$ is described by:

$$\alpha_e{^B\hat{\bf v}}~:~\hat{\bf z}_B\rightarrow {^B\hat{\bf z}}_D.$$
such that:
$$\begin{array}{ll}
\alpha_e 
&= \cos^{-1}\left({\hat{\bf z}_B}\cdot {^B\hat{\bf z}_D}\right), 
\end{array}$$
$${^B\hat{\bf v}}=\frac{1}{\sin\alpha_e}\hat{\bf z}_B\times {^B\hat{\bf z}_D},

$$
where:
$$\begin{array}{ll}
{^B\hat{\bf z}_D}
&=&{^B{\bf R}_W}{^W{\bf R}_D}{\hat{\bf z}_D},\\
&=&{^B{\bf R}_W}{^W{\bf R}_D}[0~~0~~1]^T.
\end{array}$$
The resulting exponential coordinates, ${^B{\bf e}_o}=\alpha_e^B\hat{\bf v}$, can be used as the tracking error for the quadcopter's roll and pitch controllers. This approach avoids rotation about the $\hat{\bf z}_B$ axis which ensures that ${^B{\bf \omega}}\times \left( {\bf J} {^B{\bf \omega}} \right)\approx 0$, simplifying the attitude dynamics as discussed in [[#2. Dynamic Analysis]]. This is summarized in the block diagram below:
![[Thrust_Vectoring.png]]

## Decoupled rotational Dynamics
With the assumption that $\omega_z \approx 0$, the attitude dynamics simplify to:
$${\bf J}{^B\dot{\bf \omega}}\approx {^B{\bf m}}$$

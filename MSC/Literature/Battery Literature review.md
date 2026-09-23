# Range, Endurance, and Optimal Speed Estimates for Multicopters
**By: Leonard Bauersfeld and Davide Scaramuzza**

## Intro

Estimating the range and endurance of a multicopter re- quires an accurate model of the vehicle’s power consumption. This is particularly difficult because the instantaneous power draw is influenced by the airflow around the vehicle [6], by the rotor speeds of the individual motors, and by the particular motor-propeller combination [7]. Furthermore, a battery model that still accurately holds when the commonly used LiPo batteries (lithium-polymer) are discharged at very high rates is required.

Existing approaches for range and endurance estimates often focus on hover-endurance [8], [9], where the complex aero- dynamic effects of multicopter flight can be neglected. Works concerned with estimating the maximum flight range use sim- plistic multicopter models for forward flight [10], [11], [12]. Such models neglect key aerodynamic effects experienced by all rotary wing aircraft: as the vehicle flies forward, the dynamic lift experienced by the propellers yields a reduction in the multicopter’s power consumption. Furthermore, linear rotor drag (induced drag) is not considered albeit significantly contributing to the overall drag [13].

The above mentioned works either assume that the battery is an ideal energy storage or use the **Peukert model** [14] to calculate the battery capacity. However, this model is only accurate for the low to medium discharge rates [15] typically encountered in ground vehicles or fixed-wing aircraft but not well-suited for the very high power demand of multicopters.

Aerodynamic models for multicopter thrust and axial torque typically assume a quadratic relationship proportional to propeller rotational speed, which works reliably for hover flight [17-20]. However, this model degrades significantly at higher forward speeds because it ignores critical aerodynamic forces:
- **Induced propeller drag (linear drag):** The primary unmodeled effect at speed, which can be partially mitigated by adding a velocity-dependent drag term [13, 18].
- **Dynamic lift:** An increase in thrust caused by higher in-plane airspeed across the propeller blades, which the quadratic model cannot accurately capture [2].
Because of these limitations, the standard quadratic model is suitable only for hover endurance estimates, not range or forward-flight performance. For accurate force and torque modeling across a broad operating envelope, blade-element-momentum (BEM) theory serves as the superior alternative [6, 21-23].

Next to the well-established first-principles models, a recent line of work on machine-learned multicopter models has emerged [24], [25], [26], [27]. Despite being accurate, they are not well suited for range, endurance, and speed estimation of general multicopters because they do not predict the power consumption and only apply to the exact vehicle they have been trained on. 

The earliest work on battery modeling dates back to the late 19th century, when Peukert studied how the capacity of lead- acid batteries depends on the discharge current [14]. Due to its simplicity, the Peukert Model has since become the standard approach to model the effective capacity under load. It has also been shown to hold for LiPo batteries at medium discharge rates [15], [28]. Generalizations to medium discharge (around 1 C) rates exist as well [29], [15].


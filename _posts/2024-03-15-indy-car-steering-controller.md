---
layout: post
title: "Autonomous Indy Car Steering Control"
categories: misc
---
## Project Overview

Working alongside my talented teammates as a team of four, we set out to design a steering control system that could manage the aggressive dynamics of an Indy car. The project was structured into multiple phases:

- **System Modeling:** We began by deriving the equations of motion that describe the vehicle dynamics.
- **Control Design:** Using the derived equations, we developed transfer functions and state-space representations to capture the system’s behavior.
- **Analysis Techniques:** Our approach included classical methods like Root Locus and Bode Plot analysis to assess stability and performance. We also ran step response simulations to verify our control design.
- **Validation:** Finally, the performance of the steering control system was evaluated in a simulated Indy 500 track environment at a top speed of **108 m/s**, ensuring that our design was robust under high-speed conditions.

## System Modeling & Vehicle Dynamics

### The Bicycle Vehicle Dynamics Model

The bicycle model is a widely used simplification in vehicle dynamics that represents a car with two wheels—one at the front and one at the rear. This model captures the essential lateral and yaw dynamics, making it a powerful tool for designing and analyzing steering control systems.

**Key Features of the Model:**

- **Lateral Dynamics:**  
  The model accounts for the lateral movement of the vehicle. It explains how the vehicle responds to steering inputs by generating lateral tire forces.
  
- **Yaw Dynamics:**  
  It also models the yaw motion—the rotation of the vehicle about its vertical axis. The yaw rate is influenced by the distribution of mass and the forces acting at the front and rear tires.
  
- **Simplified Representation:**  
  By combining the left and right wheels into a single front and rear wheel, the bicycle model reduces the complexity of the vehicle dynamics while preserving the key behaviors necessary for control analysis.

<img src="{{ '/Media/IndyCar/bicycle_model.png' | relative_url }}" alt="Bicycle Vehicle Model" />


#### System Modeling: Equations of Motion

**Summation of Forces**  
$$ \sum F = m \bigl(v_{\text{car}}\dot{\psi} + v_{\text{car}}\beta\bigr) = -C_r\,\beta +\frac{C_rb}{V_{car}}\Psi - C_f\bigl(\beta - \delta\bigr) - \frac{C_fa}{V_{car}}\Psi $$

**Summation of Moments**  
$$
\sum M = J\,\ddot{\psi} = -b\left(\frac{-C_r}{V_{Car}} \left(V_{car}\beta - \dot{\Psi}b\right)\right) + a\left(\frac{-C_f}{V_{Car}} \left(V_{car}\beta - V_{car}\delta + \dot{\Psi}a\right)\right)
$$

Here:
- $\(m\)$: The vehicle mass.
- $\(v_{\text{car}}\)$: The constant forward velocity of the vehicle.
- $\(\dot{\psi}\)$: The yaw rate, representing the rate of change of the vehicle’s yaw angle.
- $\(\beta\)$: The sideslip angle, which quantifies the angle between the vehicle’s actual velocity vector and its longitudinal axis.
- $\(\delta\)$: The steering angle, the angle by which the front wheels are turned.
- $\(C_f\)$: The cornering stiffness of the front tires, indicating how much lateral force the tires generate per unit of slip angle.
- $\(C_r\)$: The cornering stiffness of the rear tires, similar to \(C_f\) but for the rear axle.
- $\(J\)$: The yaw moment of inertia of the vehicle, which quantifies its resistance to changes in yaw rate.
- $\(a\)$: The distance from the vehicle’s center of gravity (CG) to the front axle.
- $\(b\)$: The distance from the vehicle’s center of gravity (CG) to the rear axle.


### Control Strategy

#### Cascaded PD Controller

Our control strategy employed a **cascaded PD controller** with two loops:
- **Outer Loop:** Controls an input voltage on a motor on the steering column to front tire angle.
- **Inner Loop:** Maps tire angle to the vehicle yaw angle response.

*Note: The voltage-to-tire angle model is omitted for brevity.*

### Simulation and Verification

With our models and controller in place, we moved to verify performance using simulation:

- **Step Response Analysis:**  
  The step response, as illustrated in `step15b.jpg`, highlighted how the uncontrolled system reacted to inputs, setting the stage for refining our controller.
  
- **Model Matching and P-code Verification:**  
  These steps ensured that our theoretical models aligned with the expected performance of the physical system.
  
- **Frequency Response:**  
  The bode plots available in files such as `bodePoint7.jpg` and `partbfixedbode.jpg` confirmed our model's frequency characteristics and helped fine-tune our controller.

### High-Speed Evaluation

One of the most thrilling parts of the project was the validation phase. We tested the steering control system on an Indy 500 track simulation, achieving a remarkable top speed of **108 m/s**. The high-speed tests are documented with several key figures:

- **Motor Voltage Input:** `MotorVoltageInput_108jpg.jpg`  
  *This figure captures the voltage profile applied to the motor at 108 m/s, demonstrating the input dynamics of the system.*
  
- **Tire Angle Input:** `TireAngleInput_108jpg.jpg`  
  *This image shows the tire angle command at 108 m/s, providing insight into the steering control under high-speed conditions.*

These figures, along with additional plots such as the step response (`step15b.jpg`) and frequency response (`bodePoint7.jpg`), illustrate the rigorous testing and validation process that ensured the system’s performance.

## Lessons Learned and Future Directions

This project was a tremendous learning experience:

- **Real-World Application of Control Theory:**  
  It underscored how classical methods can be adapted and extended to modern autonomous systems.
  
- **Teamwork and Iterative Design:**  
  Collaborating with a diverse team of engineers taught us the value of iterative design and robust testing.
  
- **Future Work:**  
  The success of our steering control system opens up new questions—such as integrating additional sensor feedback or adapting the controller for different racing conditions—which are exciting avenues for further exploration.

## Conclusion

Our autonomous steering control project not only provided hands-on experience with advanced control techniques but also demonstrated the potential for applying classical system dynamics to modern, high-speed applications. The blend of theory, simulation, and real-world validation has given us a robust foundation to push the boundaries of autonomous vehicle technology.

For a detailed walkthrough of our methodology, challenges, and results, please refer to our final presentation and accompanying figures.

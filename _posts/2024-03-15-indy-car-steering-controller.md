# Steering into the Future: Autonomous Control for an Indy Car

When I look back at the autonomous Indy car project for my System Dynamics and Controls course, I’m filled with excitement about the challenges we tackled and the innovative solutions we developed. Our project—affectionately dubbed “We’re so Good it Hertz”—was not only a deep dive into control theory but also a hands-on exploration of real-world dynamics at high speed.

## Project Overview

Working alongside my talented teammates—Caroline Perkinson, Austin Phillips, James Cannon, and Noah Murphree—we set out to design a steering control system that could manage the aggressive dynamics of an Indy car. The project was structured into multiple phases:

- **System Modeling:** We began by deriving the equations of motion that describe the vehicle dynamics.
- **Control Design:** Using the derived equations, we developed transfer functions and state-space representations to capture the system’s behavior.
- **Analysis Techniques:** Our approach included classical methods like Root Locus and Bode Plot analysis to assess stability and performance. We also ran step response simulations to verify our control design.
- **Validation:** Finally, the performance of the steering control system was evaluated in a simulated Indy 500 track environment at a top speed of **108 m/s**, ensuring that our design was robust under high-speed conditions.

The detailed progression of our work—from the foundational equations to the final performance evaluation—is outlined in our final PowerPoint presentation (*MECH 3140 Final Project.pptx*).

## Diving into the Details

### System Modeling & Vehicle Dynamics

#### The Bicycle Vehicle Dynamics Model

The bicycle model is a widely used simplification in vehicle dynamics that represents a car with two wheels—one at the front and one at the rear. This model captures the essential lateral and yaw dynamics, making it a powerful tool for designing and analyzing steering control systems.

**Key Features of the Model:**

- **Lateral Dynamics:**  
  The model accounts for the lateral (side-to-side) movement of the vehicle. It explains how the vehicle responds to steering inputs by generating lateral tire forces.
  
- **Yaw Dynamics:**  
  It also models the yaw motion—the rotation of the vehicle about its vertical axis. The yaw rate is influenced by the distribution of mass and the forces acting at the front and rear tires.
  
- **Simplified Representation:**  
  By combining the left and right wheels into a single front and rear wheel, the bicycle model reduces the complexity of the vehicle dynamics while preserving the key behaviors necessary for control analysis.

The tire forces are typically modeled based on the slip angles:
$$
\alpha_f = \delta - \frac{v + a\,r}{u}, \quad \alpha_r = -\frac{v - b\,r}{u},
$$
where \(\delta\) is the steering angle, \(v\) is the lateral velocity, \(u\) is the forward velocity, \(r\) is the yaw rate, and \(a\) and \(b\) are the distances from the center of gravity to the front and rear axles respectively.

**Reference Figure:**  
A schematic diagram that illustrates the key components of the bicycle model—showing the vehicle's mass, tire forces, and geometry—is available in the uploaded file `bsintire.jpg`. This figure provides a clear visual representation of how the vehicle's dynamics are modeled using this approach.

#### System Modeling: Equations of Motion

**Summation of Forces**  
$$ \sum F = m \bigl(v_{\text{car}}\dot{\psi} + v_{\text{car}}\beta\bigr) = -C_r\,\beta - C_f\bigl(\beta - \delta\bigr) $$

**Summation of Moments**  
$$ \sum M = J\,\dot{\psi} = -b\,C_r\,\beta + a\,C_f\bigl(\beta - \delta\bigr) $$

Here:
- \(m\) is the vehicle mass.
- \(v_{\text{car}}\) is the (constant) forward velocity.
- \(\dot{\psi}\) is the yaw rate.
- \(\beta\) is the sideslip angle.
- \(\delta\) is the steering angle.
- \(C_f\) and \(C_r\) are the cornering stiffnesses of the front and rear tires, respectively.
- \(J\) is the yaw moment of inertia.
- \(a\) and \(b\) are the distances from the vehicle’s center of gravity to the front and rear axles, respectively.

### Control Strategy

#### Cascaded PD Controller

Our control strategy employed a **cascaded PD controller** with two loops:
- **Outer Loop:** Converts voltage commands to tire angle commands.
- **Inner Loop:** Maps tire angle commands to vehicle angle responses.

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

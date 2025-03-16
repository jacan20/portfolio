---
layout: post
title: "MECH 3140 - Autonomous Indy Car Steering Control"
date: 2024-04-25
categories: [Academic Project]
---

## Introduction

In this final group project for System Dynamics and Controls, our team of four set out to develop a steering control system for the autonomous IndyLight race car (a Dallara IL-15 used in the Indy Autonomous Challenge). The project is done entirely in MATLAB, with a password protected MATLAB function (run_indy_car.p) serving as the simulated model of the vehicle. The setup involves a DC motor on the steering column, with the voltage to that motor being the input to the system. The project's primary goal is to design a robust controller that enables the vehicle to navigate a racetrack efficiently while meeting stringent performance criteria.

## Project Objectives and Requirements

The assignment document outlines several key objectives and requirements:

1. **Dynamic Modeling and System Identification:**
   - **Model Development:**  
     Derive the system dynamics by summing forces in the lateral direction and moments about the vehicle's center of gravity. This involves formulating the equations of motion for both lateral and yaw dynamics.
   - **Transfer Function Creation:**  
     Combine the derived equations to develop a transfer function with the steering input (voltage command, through the steering mechanism) as the input and the yaw rate as the output.

2. **Controller Design:**
   - **Feedback Control System:**  
     Develop a feedback control system that computes the desired voltage to control the vehicle’s heading (yaw) or lateral position. A cascaded PD controller is implemented where:
       - The **outer loop** converts the voltage command into a tire angle reference.
       - The **inner loop** maps the tire angle reference to the vehicle’s actual response.
     *Note: The voltage-to-tire angle model is omitted for brevity.*
   - **Performance Targets:**  
     The controller must track a constant desired reference with zero steady-state error. Key performance metrics include a settling time of less than 0.6 seconds and a maximum overshoot of less than 5% at speeds around 15 m/s.

3. **Validation and Testing:**
   - **Simulation and Real-World Testing:**  
     Validate the developed model against the `run_Indy_car.p` simulation, ensuring that both the system identification and control system meet the expected performance across various speeds (including speeds >60 m/s).

This project not only reinforces our understanding of vehicle dynamics and control theory but also challenges us to integrate modeling, simulation, and real-world validation into a cohesive system for autonomous vehicle control. Much of the work for this project is omitted, for academic honesty reasons, as versions of this project are given to students every semester.

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

<img src="{{ '/Media/IndyCar/bicycle_model.png' | relative_url }}" alt="Bicycle Vehicle Model" width="80%"/>


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

The following figure is the root locus used for pole-zero placement (controller gain selection) for the inner loop controller. This root locus was generated for a vehicle at 108 m/s.

<img src="{{ '/Media/IndyCar/108RootLocus.jpg' | relative_url }}" alt="108 root locus for inner controller" width="60%"/>


*Note: The voltage-to-tire angle model is omitted for brevity.*

### Simulation and Verification

One of the most thrilling parts of the project was the validation phase. We tested the steering control system on an Indy 500 track simulation, achieving a remarkable top speed of **108 m/s**. This was the highest speed our controller was stable; it was also stable at lower speeds. it The high-speed tests are documented with several key figures:

- **Steering Column Motor Voltage Input:**
  Here is the input voltage the to the motor on the steering column. We are not modeling the dynamics of a motor driver here. The system dynamics include that of the DC motor, and the gearboxes, etc that allow the motor to move the wheels. 
  <img src="{{ '/Media/IndyCar/voltage.png' | relative_url }}" alt="108 m/s Steering Motor Input" width="60%"/>

  This figure captures the voltage profile applied to the motor at 108 m/s, demonstrating the input dynamics of the system. This shows an area for improvement, as the voltage rapidly cycles between its maximum and minimum values. However, the controller is able to control the vehicle, despite the +/- 24V operational limit of the motor on the steering column.
  
- **Tire Angle Input/output:** 
  Here is the tire angle input to the inner control loop. You can see both the desired tire angle, and the actual tire angle the outer loop is able to deliver.

  <img src="{{ '/Media/IndyCar/tireangle.png' | relative_url }}" alt="108 m/s Tire Angle Input" width="60%"/>

  This image shows the tire angle command at 108 m/s, providing insight into the steering control under high-speed conditions. This was the output of the outer control loop, and the input to the inner control loop

- **Simulated Vehicle Lap:**
  Finally, putting every thing together, and running the the controller on the password protected p-code Indy Car model, we can see the indy car completeing laps at the Indianapolis Motor Speedway. The vehicle just barely stays in the lanes on the turns; any faster and our turning radius is larger than the track (and given GPS waypoints) can support.

    <img src="{{ '/Media/IndyCar/indycar108track.png' | relative_url }}" alt="108 m/s Tire Angle Input" width="60%"/>



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

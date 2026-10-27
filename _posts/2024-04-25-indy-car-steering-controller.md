---
layout: post
title: "MECH 3140 - Autonomous Indy Car Steering Control"
date: 2024-04-25
categories: [Academic Project]
---

## Introduction

For the final group project in System Dynamics and Controls, my team of four designed a steering controller for an autonomous Indy-style race car. The project was done entirely in MATLAB. A password-protected MATLAB function, `run_indy_car.p`, served as the simulated vehicle. A DC motor on the steering column turns the front wheels, and the voltage to that motor is the system's input. The goal was a controller that drives the car around a racetrack while meeting strict performance requirements.

Much of the detailed work is omitted here for academic honesty reasons, since versions of this project are assigned every semester.

## Objectives and Requirements

1. **Dynamic modeling and system identification**
   - Derive the equations of motion by summing lateral forces and yaw moments about the vehicle's center of gravity.
   - Combine them into a transfer function from the steering input to the vehicle's yaw rate.

2. **Controller design**
   - Design a feedback controller that computes the steering motor voltage needed to control the vehicle's heading (yaw) or lateral position. We used a cascaded PD controller:
     - The **inner loop** drives the steering motor voltage so the front tire angle tracks a tire angle reference.
     - The **outer loop** generates that tire angle reference from the vehicle's yaw (heading) error.
   - Track a constant reference with zero steady-state error, with a settling time under 0.6 s and overshoot under 5% at about 15 m/s.

3. **Simulation testing**
   - Validate the model and controller against the `run_indy_car.p` simulation across a range of speeds, including speeds above 60 m/s.

## System Modeling

### The Bicycle Model

The bicycle model is a standard simplification for vehicle lateral dynamics. It lumps the left and right wheels into a single front wheel and a single rear wheel. That keeps the two motions that matter for steering control while greatly reducing complexity:

- **Lateral motion:** how the vehicle's sideslip responds to the lateral forces the tires generate when steered.
- **Yaw motion:** rotation about the vertical axis, driven by the moments of the front and rear tire forces about the center of gravity.

<img src="{{ '/Media/IndyCar/bicycle_model.png' | relative_url }}" alt="Bicycle vehicle model" width="80%"/>

### Equations of Motion

Using linear tire models, where each tire's lateral force is its cornering stiffness times its slip angle:

**Lateral forces**

$$
m V \bigl(\dot{\beta} + \dot{\psi}\bigr) = -C_f\left(\beta + \frac{a\,\dot{\psi}}{V} - \delta\right) - C_r\left(\beta - \frac{b\,\dot{\psi}}{V}\right)
$$

**Yaw moments**

$$
J \ddot{\psi} = a\,C_f\left(\delta - \beta - \frac{a\,\dot{\psi}}{V}\right) - b\,C_r\left(\frac{b\,\dot{\psi}}{V} - \beta\right)
$$

where:
- \\(m\\): vehicle mass
- \\(V\\): forward speed, held constant
- \\(\psi\\), \\(\dot{\psi}\\): yaw angle and yaw rate
- \\(\beta\\): sideslip angle, between the vehicle's velocity vector and its longitudinal axis
- \\(\delta\\): front wheel steering angle
- \\(C_f\\), \\(C_r\\): front and rear tire cornering stiffness (lateral force per unit slip angle)
- \\(J\\): yaw moment of inertia
- \\(a\\), \\(b\\): distances from the center of gravity to the front and rear axles

## Control Strategy

The controller is a **cascaded PD controller** with two loops:
- **Inner loop:** sets the steering motor voltage so the front tire angle tracks its reference.
- **Outer loop:** commands the tire angle reference that steers the vehicle's yaw angle toward the desired heading.

The vehicle dynamics change with speed, so the outer loop gains were chosen for the speed of interest. The root locus below, used for pole placement (gain selection) in the outer (yaw) loop, was generated at 108 m/s.

<img src="{{ '/Media/IndyCar/108RootLocus.jpg' | relative_url }}" alt="Root locus for the outer (yaw) loop controller at 108 m/s" width="60%"/>

*The voltage-to-tire-angle (inner loop) model is omitted for brevity.*

## Simulation Results

We tested the controller on a simulated lap of the Indianapolis Motor Speedway. It stayed stable up to **108 m/s (~242 mph)**, the fastest stable controller in the class, and it was also stable at lower speeds. The figures below are from the 108 m/s run.

- **Steering motor voltage:** This is the voltage commanded to the steering column motor. The motor driver isn't modeled, but the plant does include the DC motor and the gearing that turns the wheels.

  <img src="{{ '/Media/IndyCar/voltage.png' | relative_url }}" alt="Steering motor voltage at 108 m/s" width="60%"/>

  The voltage switches rapidly between the motor's ±24 V limits, which leaves room for improvement. Even with that saturation, the controller keeps the vehicle under control.

- **Tire angle:** This plot shows the desired tire angle (the outer loop's output and the inner loop's input) alongside the actual tire angle the inner loop delivers.

  <img src="{{ '/Media/IndyCar/tireangle.png' | relative_url }}" alt="Commanded and actual tire angle at 108 m/s" width="60%"/>

- **Simulated lap:** Running the full controller on the p-code Indy car model, the car completes laps of the Indianapolis Motor Speedway. It barely stays within the track in the turns. Any faster, and the car's turning radius would be larger than the track and its GPS waypoints allow.

  <img src="{{ '/Media/IndyCar/indycar108track.png' | relative_url }}" alt="Simulated lap at 108 m/s on the Indianapolis Motor Speedway" width="60%"/>

## Lessons Learned

- **Classical control applied to a modern problem:** Root-locus PD design, split into inner and outer loops, was enough to steer an autonomous race car at over 100 m/s in simulation, even with the motor voltage saturating.
- **Teamwork and iteration:** Working as a team of four reinforced the value of iterative design and thorough testing.
- **Future work:** Natural next steps include smoothing the saturated motor voltage, adding sensor feedback, and adapting the controller to different racing conditions.

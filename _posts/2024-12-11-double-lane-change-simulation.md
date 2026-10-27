---
layout: post
title: "MECH 6970 - Double Lane Change INS/GPS/Simulation Analysis"
date: 2024-12-11
categories: [Academic Project]
use_math: true
---

In December 2024, a team of three of us benchmarked vehicle navigation methods for a class project. We compared GPS-based navigation, INS dead reckoning, and a bicycle-model simulation similar to the p-code vehicle model from my MECH 3140 project.

## Project Overview

The test followed ISO 3888-2, the severe lane-change (obstacle avoidance) maneuver. We collected data from a 2012 Kia Optima instrumented by Auburn's GPS and Vehicle Dynamics Lab (GAVLab): IMU (inertial measurement unit) and GPS measurements, plus speedometer readings and steering wheel angle. Our goal was to compare the position error of three navigation methods:

- **INS dead reckoning:** integrating measured acceleration and angular rate to propagate position.
- **GPS navigation:** using satellite position fixes.
- **Model-based navigation:** driving a bicycle model of the vehicle with the measured speed and steering inputs.

<br>
<img src="/portfolio/Media/navproject/iso38888-2.png" alt="ISO 3888-2 test course layout" style="max-width: 100%;"/>

## Methodology

### Initialization

We compared two ways to initialize the INS orientation:

1. **GPS heading:** A fixed heading referenced to north set the IMU's initial orientation.
2. **Initial motion:** The direction of the vehicle's initial motion defined the rotation matrix aligning the IMU with the navigation frame.

<br>
<img src="/portfolio/Media/navproject/kiasoul.jpg" alt="2012 Kia Optima, the test vehicle model" style="max-width: 100%;"/>

### IMU Data Processing

- **Filtering:** A 4th-order Butterworth low-pass filter with a 25 Hz cutoff smoothed the accelerometer and gyroscope signals.

  <img src="/portfolio/Media/navproject/imu_data_filtered.png" alt="Filtered IMU data" style="max-width: 100%;"/>

- **Bias and gravity correction:** Readings taken while the vehicle was stationary were subtracted to remove sensor biases and the gravity component. This simple approach leaves small residual errors.

### IMU Mechanization

The mechanization equations propagate attitude, velocity, and position in the navigation frame at each time step \\(\Delta T\\):

1. **Incremental rotation:**

$$
\Delta \vec{\theta} = \vec{\omega}\,\Delta T
$$

2. **Attitude update** (first-order, small-angle approximation):

$$
C(+) = C(-)\,\bigl(I_3 + [\Delta \vec{\theta}]_\times\bigr)
$$

where \\(C\\) rotates vectors from the body frame to the navigation frame, \\(I_3\\) is the 3×3 identity matrix, and \\([\Delta \vec{\theta}]_\times\\) is the skew-symmetric matrix of \\(\Delta \vec{\theta}\\).

3. **Navigation-frame acceleration**, from the bias- and gravity-corrected body-frame acceleration:

$$
\vec{a}_{\mathrm{nav}} = C(+)\,\vec{a}_{\mathrm{body}}
$$

4. **Velocity update:**

$$
\vec{v}_{\mathrm{nav}}(t) = \vec{v}_{\mathrm{nav}}(t-\Delta T) + \vec{a}_{\mathrm{nav}}\,\Delta T
$$

5. **Position update:**

$$
\vec{r}_{\mathrm{nav}}(t) = \vec{r}_{\mathrm{nav}}(t-\Delta T) + \vec{v}_{\mathrm{nav}}(t-\Delta T)\,\Delta T + \tfrac{1}{2}\,\vec{a}_{\mathrm{nav}}\,\Delta T^2
$$

<br>
<img src="/portfolio/Media/navproject/imu3d.jpg" alt="IMU mechanization schematic" style="max-width: 100%;"/>

### Bicycle Model

The measured speed and steering angle drive a kinematic bicycle model, which propagates the vehicle's position and heading:

$$
\dot{x} = v\cos\psi, \qquad
\dot{y} = v\sin\psi, \qquad
\dot{\psi} = \frac{v}{L}\tan\delta
$$

where \\((x, y)\\) is the vehicle's position in the plane, \\(\psi\\) is the heading, \\(v\\) is the longitudinal velocity, \\(L\\) is the wheelbase, and \\(\delta\\) is the front wheel steering angle.

<br>
<img src="/portfolio/Media/navproject/bicycle_model.png" alt="Bicycle model diagram" style="max-width: 100%;"/>

## Results

We compared the position estimates from:
1. Pure INS dead reckoning
2. GPS position fixes
3. The model-based (bicycle) simulation

Model-based navigation, combined with IMU initialization from the vehicle's initial motion, produced the lowest position error over time. Initializing from initial motion also outperformed the fixed GPS-heading method for INS orientation.

<br>
<img src="/portfolio/Media/navproject/methodscompared2D.jpg" alt="Comparison of navigation methods" style="max-width: 100%;"/>

### Error Over Time

<br>
<img src="/portfolio/Media/navproject/error.jpg" alt="Position error over time for each method" style="max-width: 100%;"/>

The model-based path stays closest to the ground truth.

## Conclusion

The project itself was fairly straightforward, but it was my first time building a complete 6-DOF IMU mechanization, bias correction, and initialization scheme from scratch. It also showed how much filtering improves IMU data quality, and how differently each navigation method performs on the same maneuver. [If only there were a way to fuse these data streams into a single, more accurate navigation solution!](https://en.wikipedia.org/wiki/Kalman_filter)

---
layout: post
title: "MECH 6970 - Double Lane Change INS/GPS/Simulation Analysis"
date: 2024-12-11
categories: [Academic Project]
use_math: true
---

[Higher resolution figures coming soon (tm). These were copied from the final presentation.]

In December 2024, I worked with a team of three on a class project aimed at benchmarking vehicle navigation techniques. Our objective was to compare traditional GPS-based navigation with INS dead reckoning and a bicycle-model simulation, similar to the pcode from my MECH 3140 project.

## Project Overview

The study was designed around the ISO 3888-2 test standard. We collected data from a 2012 Kia Optima, instrumented through Auburn’s GAVLab, capturing both IMU (Inertial Measurement Unit) and GPS measurements. We also collected vehicle speedometer readings, and steering wheel angles. Our primary goal was to evaluate the positional errors of three distinct navigation methods:

- **INS Dead Reckoning:** Calculating position by integrating acceleration and angular velocity from sensor data.  
- **GPS Navigation:** Using satellite data for position fixing.  
- **Model-Based Navigation:** Tuning a lateral dynamics (bicycle) model of the vehicle with the collected test data.

<br>
<img src="/Media/navproject/iso38888-2.png" alt="ISO 3888-2 Standard Layout" style="max-width: 100%;"/>

## Methodology

### Initialization Techniques

We used two methods to initialize the navigation systems:

1. **GPS Position Fixing with Approximate Rotation Matrix**  
   A fixed GPS heading (using North) was employed to set the initial set orientation of the IMU. 

2. **Initial Heading Method**  
   The vehicle’s initial motion defined a rotation matrix, aligning the IMU’s orientation with the desired navigation frame.

<br>
<img src="/Media/navproject/kiasoul.jpg" alt="Instrumented Kia" style="max-width: 100%;"/>

### IMU Data Processing

To ensure reliable sensor data, we applied several processing techniques:

- **Filtering:** A 4th-order Butterworth low-pass filter with a 25 Hz cutoff smoothed accelerometer and gyroscope signals.

  <img src="/Media/navproject/imu_data_filtered.png" alt="Filtered IMU Data" style="max-width: 100%;"/>

- **Bias Correction:** Stationary readings helped subtract inherent sensor biases and address gravity in a straightforward manner, albeit with minor residual errors.

### IMU Mechanization

Below are the equations for mechanization. These equations describe the update process for the attitude, acceleration, velocity, and position in a navigation frame. They are typically used in inertial navigation systems to propagate the state of a vehicle over time.

1. **Incremental Change in Angle:**

$$
\Delta \theta \;=\; \omega \,\Delta T
$$

2. **Updated Attitude Matrix:**

$$
C(+) \;=\; C(-)\,\Bigl(I_3 \;+\; [\Delta \theta]_\times\Bigr)
$$

where \\(I_3\\) is the 3x3 identity matrix and \\([\Delta \theta]_\times\\) is the skew-symmetric matrix of \\(\Delta \theta\\).

3. **Navigation-Frame Acceleration:**

$$
\vec{a}_{\mathrm{nav}} \;=\; C(+) \,\vec{a}_{\mathrm{body}}
$$

4. **Velocity Update:**

$$
\vec{v}_{\mathrm{nav}}(t) \;=\; \vec{v}_{\mathrm{nav}}(t-\Delta T) \;+\; \vec{a}_{\mathrm{nav}}\,\Delta T
$$

5. **Position Update:**

$$
\vec{r}_{\mathrm{nav}}(t) \;=\; \vec{r}_{\mathrm{nav}}(t-\Delta T) \;+\; \vec{v}_{\mathrm{nav}}(t)\,\Delta T \;+\; \tfrac{1}{2}\,\vec{a}_{\mathrm{nav}}\,\Delta T^2
$$

<br>
<img src="/Media/navproject/imu3d.jpg" alt="IMU Mechanization Schematic" style="max-width: 100%;"/>

### Model Integration (Bicycle Model)

We fed speedometer velocities and measured steering angles into a simplified bicycle model. This model computes lateral and yaw responses of the vehicle.

where \\((x, y)\\) is the vehicle’s position in the plane, \\(\psi\\) is the heading, \\(v\\) is the longitudinal velocity, \\(L\\) is the wheelbase, and \\(\delta\\) is the steering angle.

<br>
<img src="/Media/navproject/bicycle_model.png" alt="Bicycle Model Diagram" style="max-width: 100%;"/>

## Results & Insights

We compared the position estimates from:
1. Pure INS dead reckoning
2. GPS position fixing
3. Model-based (bicycle) simulation

Our analysis revealed that the model-based navigation, combined with IMU initialization via the vehicle’s initial motion, produced the lowest positional error over time. This hybrid approach outperformed the static GPS-heading method for INS orientation.

<br>
<img src="/Media/navproject/methodscompared2D.jpg" alt="Comparison of Methods" style="max-width: 100%;"/>

### Error Over Time

<br>
<img src="/Media/navproject/error.jpg" alt="Positional Error Plot" style="max-width: 100%;"/>

As shown above, the model-based path remains closest to the ground truth.

## Conclusion

Reflecting on this project, I recognize it was relatively straightforward; however, it was my first time developing a complete 6 DOF IMU mechanization, bias correction, and initialization scheme from scratch. It also highlighted how significantly filtering can improve IMU data quality. Ultimately, this was a valuable learning experience in the benefits of multiple navigation solution methodologies.  [Now, if only there were a way to that could elegantly fuse these data streams into a single, more accurate navigation solution!](https://en.wikipedia.org/wiki/Kalman_filter) 






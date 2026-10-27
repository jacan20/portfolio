---
layout: post
title: "Inverted Pendulum: Analog and Digital PD Control (MECH 5840)"
date: 2025-03-07
categories: [Academic Project]
slug: inverted-pendulum-pd-control
redirect_from:
  - "/academic project/2025/03/07/analog-inverted-pendulum-controller.html"
---

In Applied Mechatronics, my team of four balanced an inverted pendulum twice: first with a PD controller built entirely from op-amps, then with the same control law discretized and run on an Arduino. I contributed to the circuit design and wrote the Arduino controller code.

## The System

A potentiometer at the pivot measures the pendulum angle, outputting 0–5 V over 350° of rotation, so the upright position sits near the 2.5 V midpoint. A DC motor (torque constant 0.0259 N·m/A), driven by a servo amplifier (0.3 A/V), applies torque to the pendulum. The controller's job is to turn the measured angle into a ±10 V command for the servo amplifier.

## Controller Design

Linearized about upright, the pendulum behaves like an unstable second-order system. Gravity pushes it further from vertical, and the motor pushes back:

$$
J\,\ddot{\theta} = K\,\theta + A\,u
$$

$$
A = (0.3\ \mathrm{A/V})(0.0259\ \mathrm{N{\cdot}m/A}) = 0.00777\ \mathrm{N{\cdot}m/V}
$$

where \\(J = 3.762 \times 10^{-4}\ \mathrm{kg{\cdot}m^2}\\) is the pendulum's inertia about the pivot, \\(K = 0.03849\ \mathrm{N{\cdot}m/rad}\\) is the gravitational stiffness, and \\(u\\) is the controller voltage. With PD feedback, \\(u = -(k_p\,\theta + k_d\,\dot{\theta})\\), the closed loop becomes a stable second-order system:

$$
J\,\ddot{\theta} + A k_d\,\dot{\theta} + (A k_p - K)\,\theta = 0
$$

We targeted a damping ratio of \\(\zeta = 0.707\\) and a 2% settling time of 0.5 s, which gives \\(\omega_n = 4/(\zeta t_s) \approx 11.3\\) rad/s. Matching coefficients:

$$
k_p = \frac{\omega_n^2 J + K}{A} = 11.152\ \mathrm{V/rad}, \qquad k_d = \frac{2\zeta\omega_n J}{A} = 0.7747\ \mathrm{V{\cdot}s/rad}
$$

## Part 1: Analog Controller

The analog controller implements the PD law with op-amps (LF412s on ±15 V):

1. **Sensor excitation and offset removal:** an LM336 voltage reference, buffered by an op-amp, supplies a stable excitation voltage to the angle potentiometer. A summing amplifier then subtracts the 2.5 V upright offset (set by a trim pot from the −15 V rail) and applies a gain of 2.
2. **Amplification:** an inverting stage (×5) and a non-inverting stage (×7) bring the total gain to 70, so ±10° of tilt maps to ±10 V.
3. **Derivative:** a practical differentiator (an op-amp with RC input and feedback networks, which limits high-frequency noise gain) produces the rate term.
4. **PD summation:** the proportional and derivative signals are weighted by resistor ratios and summed into the command for the servo amplifier.

<img src="{{ '/Media/pendulum/analog-schematic.png' | relative_url }}" alt="As-built schematic of the analog PD controller op-amp stages" width="100%"/>

<img src="{{ '/Media/pendulum/analog-breadboard.jpg' | relative_url }}" alt="Analog controller built on a breadboard with four op-amp packages and trim potentiometers" width="80%"/>

The analog controller balanced the pendulum. The oscilloscope trace below shows the controller output. It sits near 0 V while the pendulum is balanced, swings hard when the pendulum is disturbed, and saturates near the supply rails when the pendulum is pushed far from upright.

<img src="{{ '/Media/pendulum/analog-scope.png' | relative_url }}" alt="Oscilloscope trace of analog controller output voltage over about 58 seconds" width="100%"/>

<div style="display: flex; justify-content: center;">
  <iframe src="https://www.youtube.com/embed/1mcQGkhZS4c" title="Analog inverted pendulum controller" style="width: 100%; max-width: 320px; aspect-ratio: 9 / 16; border: 0;" allowfullscreen></iframe>
</div>

## Part 2: Digital Controller

For the digital version, the same PD gains were discretized with Tustin's (bilinear) method, \\(s \approx \frac{2}{T}\frac{z-1}{z+1}\\):

$$
u_k = \left(k_p + \frac{2k_d}{T}\right)e_k + \left(k_p - \frac{2k_d}{T}\right)e_{k-1} - u_{k-1}
$$

where \\(T\\) is the sample period and \\(e_k\\) is the angle error at step \\(k\\). The potentiometer's 0–5 V range already matched the Arduino's analog input, so it connects directly. On the output side, the Arduino can only produce 0–5 V, so a two-stage op-amp circuit subtracts 2.5 V and applies a gain of 4 to produce the ±10 V command.

<img src="{{ '/Media/pendulum/digital-output-circuit.jpg' | relative_url }}" alt="Hand-drawn schematic of the op-amp circuit converting the Arduino's 0-5 V output to plus or minus 10 V" width="80%"/>

We ran the controller at loop rates of 100, 200, and 400 Hz. At every rate it held the pendulum upright, but with continuous oscillation. The 400 Hz setting couldn't be sustained and actually ran at about 260 Hz.

<img src="{{ '/Media/pendulum/digital-400hz-run.png' | relative_url }}" alt="Pendulum angle over a 60-second run at 400 Hz, starting hanging at minus 180 degrees and held upright after about 12 seconds" width="80%"/>

<img src="{{ '/Media/pendulum/digital-400hz-zoom.png' | relative_url }}" alt="Zoomed, filtered angle over 0.6 seconds showing sustained oscillation of roughly plus or minus 5 to 10 degrees" width="60%"/>

<div style="display: flex; flex-wrap: wrap; gap: 1rem; justify-content: center;">
  <figure style="margin: 0; flex: 1 1 240px; max-width: 320px;">
    <iframe src="https://www.youtube.com/embed/f-OPqFFVo9A" title="Digital controller at 200 Hz" style="width: 100%; aspect-ratio: 9 / 16; border: 0;" allowfullscreen></iframe>
    <figcaption style="text-align: center; font-size: 0.9em;">Digital controller, 200 Hz</figcaption>
  </figure>
  <figure style="margin: 0; flex: 1 1 240px; max-width: 320px;">
    <iframe src="https://www.youtube.com/embed/D53ljEDE4lY" title="Digital controller at 400 Hz" style="width: 100%; aspect-ratio: 9 / 16; border: 0;" allowfullscreen></iframe>
    <figcaption style="text-align: center; font-size: 0.9em;">Digital controller, 400 Hz (about 260 Hz actual)</figcaption>
  </figure>
</div>

At the time, we attributed the oscillation to sensor noise, electromagnetic interference, and quantization.

## Looking Back: What Actually Went Wrong

Reviewing the code later, I found two bugs that matter more than noise.

**1. The derivative gain was effectively zero.** The Tustin coefficients were computed as:

```cpp
float k1 = kp + (2*kd/(loopInterval*10^-6));
```

In C++, `^` is bitwise XOR, not exponentiation, and `*` binds tighter than `^`. On the Arduino this evaluates to `(loopInterval*10) XOR -6`, about 4.29 billion instead of 0.0025. That makes the derivative contribution about \\(10^{-10}\\), so both coefficients collapsed to \\(k_p\\). With \\(k_1 = k_2 = k_p\\), the controller reduces to \\(k_p(z+1)/(z+1) = k_p\\): pure proportional control with no damping, at every loop rate.

**2. The proportional gain was about 57 times too high.** The gains were derived with the angle in radians, but the code computes the error in degrees. With \\(k_p = 11.152\\) applied per degree, any tilt beyond about 0.9° saturates the ±10 V output.

Together, the digital controller acted like a nearly bang-bang proportional controller with no derivative damping, which fits the sustained oscillation we saw. The fixes are small:

```cpp
const float T = loopInterval * 1e-6;          // sample period in seconds
float k1 = kp + 2*kd/T;
float k2 = kp - 2*kd/T;
current_error = (set_point - current_value) * DEG_TO_RAD;  // gains are per radian
```

A few other improvements would help as well:
- **Filter the derivative.** Tustin-discretizing a pure derivative places a pole at \\(z = -1\\), which can ring at half the sample rate. A first-order low-pass on the derivative term avoids that.
- **Print less often.** Each loop sent about 50 characters at 115200 baud, about 4.3 ms of serial time. That likely explains why the 400 Hz loop only reached about 260 Hz.
- **Smooth the output.** `analogWrite` produces PWM rather than a true analog voltage, so a low-pass filter or a real DAC on the output would give the servo amplifier a cleaner command.

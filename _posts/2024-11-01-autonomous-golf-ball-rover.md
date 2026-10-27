---
layout: post
title: "MECH 3200 - Autonomous Golf Ball Launching/Catching Rover"
date: 2024-11-01
categories: [Academic Project]
---

For the final project in Concepts of Mechanical Design, my team of five built an autonomous rover that drives a set distance, launches a golf ball through an overhead hoop while moving, catches it in a cup, and parks in a target box.

<img src="{{ '/Media/rover/cad.jpg' | relative_url }}" alt="SolidWorks model of the rover with the launcher tube, catch funnel, laser-cut truss chassis, and drivetrain" width="80%"/>

## The Challenge

Each run had to complete this sequence autonomously:

1. Drive 10 ft.
2. Launch a golf ball through a hoop, without stopping.
3. Catch the ball in a cup supplied by the professor, with fixed dimensions.
4. Drive another 10 ft and stop inside a box.

The hoop was about 2 ft in diameter and could be set at either of two heights about 1 ft apart. With an accurate enough launcher, a single trajectory could pass through the hoop at either height, so we designed for one trajectory that covered both.

## My Role

I was responsible for the electronics and controls: the battery, the Arduino Uno, the encoder on the drive motor, the wiring and soldering, and the programming, including the timing of the solenoids that fire the launcher. I also designed, laser cut, and assembled the chassis, and I modeled and 3D printed the catch funnel, whose size was capped by the rules. My teammates designed the launcher and the drivetrain and turned the axles on a lathe.

## Design

### Chassis

The chassis is a laser-cut truss frame that carries the launcher tube, the funnel, the electronics, and the drivetrain. The truss cutouts keep the electronics inside visible.

<img src="{{ '/Media/rover/chassis.jpg' | relative_url }}" alt="Side view of the laser-cut truss chassis with the launcher column mounted on top" width="60%"/>

### Electronics and Control

An Arduino Uno runs the whole sequence. The encoder on the drive motor tracks how far the rover has traveled, which sets both stopping points. It also triggers the launch: the rover fires while moving, so the Arduino fires the solenoids once the encoder distance reaches a calculated offset from the hoop, timing the shot so the ball passes through it.

<img src="{{ '/Media/rover/electronics.jpg' | relative_url }}" alt="Underside of the rover showing the Arduino, wiring, encoder-equipped drive motor, and turned axle" width="80%"/>

### Launcher

The launcher is spring-powered with an adjustable launch angle. Solenoids release the trigger to fire it. In an early version they pushed directly on the release, putting axial load on their plungers, which solenoids don't tolerate well. In the final revision they rotate a dial that trips the release instead.

<img src="{{ '/Media/rover/launcher-funnel.jpg' | relative_url }}" alt="Top view of the launcher tube, the trigger solenoid, and the catch funnel with a golf ball resting in the padded cup" width="80%"/>

<div style="display: flex; flex-wrap: wrap; gap: 1rem; justify-content: center;">
  <figure style="margin: 0; flex: 1 1 240px; max-width: 320px;">
    <video controls muted playsinline preload="metadata" poster="{{ '/Media/rover/launcher-test-poster.jpg' | relative_url }}" style="width: 100%;">
      <source src="{{ '/Media/rover/launcher-test.mp4' | relative_url }}" type="video/mp4">
    </video>
    <figcaption style="text-align: center; font-size: 0.9em;">Development test: launcher firing</figcaption>
  </figure>
  <figure style="margin: 0; flex: 1 1 240px; max-width: 320px;">
    <video controls muted playsinline preload="metadata" poster="{{ '/Media/rover/drive-test-poster.jpg' | relative_url }}" style="width: 100%;">
      <source src="{{ '/Media/rover/drive-test.mp4' | relative_url }}" type="video/mp4">
    </video>
    <figcaption style="text-align: center; font-size: 0.9em;">Development test: autonomous drive</figcaption>
  </figure>
</div>

### Catch Funnel

The rules allowed a funnel above the supplied cup, up to a maximum size. I modeled and printed one at that maximum to give the falling ball the largest possible target and guide it into the cup.

## Results and Lessons Learned

The rover completed every required task and earned an A. Two things I'd improve:

- **Launcher tolerances:** the launcher had a lot of slop, which made its trajectory less repeatable. Tighter tolerances would make it more consistent.
- **Catch reliability:** the ball didn't land in the cup every time. It succeeded often enough to meet the requirements, but tightening up the launcher would be the first step toward making it reliable.

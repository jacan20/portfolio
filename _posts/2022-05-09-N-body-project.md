---
layout: post
title: "N-Body Newtonian Gravitation Simulation"
date: 2022-05-09
categories: [Personal Project]
slug: n-body-gravity-simulation
redirect_from:
  - "/personal project/2022/05/09/N-body-project.html"
---

I first wrote this N-body gravity simulation in high school, out of an interest in both physics and programming. I later came back to it and reworked the code and the numerical methods to make it more stable and accurate.

<iframe src="https://editor.p5js.org/jacan/full/OVvz2aPL4" width="100%" height="800" style="border:0;"></iframe>

## Physics

Each pair of bodies attracts according to Newton's law of universal gravitation:

$$
F = G \frac{m_1 m_2}{r^2}
$$

Here \\(F\\) is the magnitude of the force between bodies of mass \\(m_1\\) and \\(m_2\\), \\(G\\) is the gravitational constant, and \\(r\\) is the distance between their centers. The force on each body points toward the other. The simulation sums these pairwise forces on every body, then updates positions and velocities with velocity Verlet integration.

Velocity Verlet replaced the explicit Euler method from my early versions. Euler integration steadily adds or removes energy, so orbits spiral outward or inward over time. Velocity Verlet is a symplectic method, so its energy error stays bounded instead of growing, and orbits remain stable over long runs.

## Code structure

A `Ball` class holds each body's position, velocity, mass, and radius, along with methods to update its state and draw it on the canvas. A `Universe` class keeps the array of `Ball` objects, computes the forces between them, and steps the whole system forward. Splitting the code this way keeps the physics separate from the rendering and makes each piece easier to change.

## Numerical safeguards

The \\(1/r^2\\) force grows without bound as two bodies approach each other. Near a collision, that produces huge accelerations that a fixed time step can't resolve, and the simulation blows up. To prevent this, the force calculation never uses a distance smaller than the sum of the two bodies' radii.

As a second safeguard, the simulation caps each body's speed after the velocity update. Close encounters can still produce velocity spikes large enough to destabilize the integration, and the cap keeps them in check. The tradeoff is that the cap and the minimum distance are both non-physical: they give up exact energy and momentum conservation during close encounters in exchange for a simulation that stays stable.

## Reflection

The project began as a high school experiment and grew through later refinements: a better integrator, a cleaner code structure, and explicit handling of the numerical edge cases. Coming back to it showed me how much the choice of numerical method matters, even for a simulation built on one simple law.

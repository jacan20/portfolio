---
layout: post
title: "N-Body Newtonian Gravitation Simulation"
categories: [Personal Project]
---

# N-Body Gravitational Simulation: A Journey from High School to Modern Modeling

I originally developed this N-body gravitational simulation during my high school years, driven by a fascination with both physics and programming. Over time, I revisited the project and made significant improvements, refining the code and numerical methods to create a more robust and accurate simulation. 

<iframe src="https://editor.p5js.org/jacan/full/OVvz2aPL4" width="100%" height="800" style="border:0;"></iframe>

At its core, the simulation models gravitational interactions using Newton's Law of Universal Gravitation:

$$
F = G \frac{m_1 m_2}{r^2}
$$

Here, \(F\) is the gravitational force between two bodies with masses \(m_1\) and \(m_2\), \(G\) is the gravitational constant, and \(r\) is the distance between their centers. I calculate these forces between every pair of objects and update their positions and velocities using the Velocity Verlet integration method. This method is particularly effective at conserving energy over time, a significant improvement over the simpler Euler integration method I used in my early versions.

The simulation is structured using object-oriented programming (OOP) principles. I created a `Ball` class to encapsulate the properties of each object—such as position, velocity, mass, and radius—and to provide methods for updating its state and rendering it on the canvas. The `Universe` class orchestrates the simulation by maintaining an array of `Ball` objects, computing the forces between them, and updating the system as a whole. This modular structure not only makes the code more maintainable but also mirrors how engineers model complex systems by breaking them down into interacting components.

A particularly challenging aspect of the simulation is handling singularities. When two objects come very close, the force computed using the \(1/r^2\) relationship can become extremely large, leading to numerical instability. To address this, I enforce a minimum effective distance—set to the sum of the radii of the colliding balls—so that when objects get too close, the distance used in the force calculation never falls below this threshold. This technique helps prevent the simulation from "blowing up" due to extreme forces.

Another important feature I added is a velocity cap. After updating the velocity via the Velocity Verlet method, I use a cap to limit the maximum speed of each ball. This is crucial because, in cases of close encounters or large force calculations, velocities can spike and introduce further numerical errors. By capping the velocity, the simulation remains stable and the integration stays robust, ensuring realistic behavior even under extreme conditions.

Overall, this project is a blend of my early experimentation and later refinements. It demonstrates how a high school project can evolve into a sophisticated simulation tool, integrating sound mathematical modeling with practical engineering considerations. Revisiting this work has been both nostalgic and enlightening, reminding me of the value of continuous improvement and the powerful intersection of programming and physics in engineering modeling.


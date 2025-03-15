---
layout: post
title: "Monte Carlo Dart Board Pi Approximator"
date: 2021-03-14
categories: [Personal Project]
---
This project approximates π using a Monte Carlo simulation—a method I originally developed in high school and still appreciate as a college student. The basic idea is to randomly generate points ("darts") within a square that encloses a circle. By counting how many darts land inside the circle and comparing that to the total number of darts, π is estimated using the formula:

$$ \pi \approx 4 \times \frac{\text{Number of Darts Inside the Circle}}{\text{Total Number of Darts}} $$

<div style="text-align: center;">
  <iframe src="https://editor.p5js.org/jacan/full/g3i6vUn2m" width="80%" height="800" frameborder="0" allowfullscreen></iframe>
</div>

  The simulation computes the estimated value of π by multiplying the ratio of darts that fall inside the circle by 4. It then calculates the percent error relative to the actual value of π. These statistics are updated live in the information panel.  

  The simulation begins with a familiar, tangible setup—a dartboard-like configuration in which a square encloses a circle—making the concept immediately accessible. Beneath this visual representation, however, lies a robust mathematical framework: the random dart throws serve as a probabilistic experiment, and as the number of darts increases, the observed ratio converges to the actual area ratio between the circle and the square. This convergence, governed by the law of large numbers, reveals π purely through mathematical principles. In essence, while the physical setup of the problem provides a framework for human intuition, by implementing this model, the emergence of π results from only fundamental math on randomly generated points, independent of any graphical representation.






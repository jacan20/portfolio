---
layout: post
title: "Monte Carlo Dart Board Pi Approximator"
date: 2021-03-14
categories: [Personal Project]
---
This project estimates π with a Monte Carlo simulation, an idea I first built in high school. Random points ("darts") are scattered uniformly over a square that encloses a circle. The fraction of darts landing inside the circle approaches the ratio of the two areas, π/4, so:

$$ \pi \approx 4 \times \frac{\text{Number of darts inside the circle}}{\text{Total number of darts}} $$

<div style="text-align: center;">
  <iframe src="https://editor.p5js.org/jacan/full/g3i6vUn2m" width="80%" height="800" frameborder="0" allowfullscreen></iframe>
</div>

The simulation multiplies the inside-to-total ratio by 4 to estimate π, then computes the percent error against the true value. Both numbers update live in the information panel.

The dartboard makes the idea easy to picture, but the estimate itself depends only on arithmetic on random points. By the law of large numbers, the observed ratio converges to π/4 as the number of darts grows. Convergence is slow: the statistical error shrinks in proportion to \\(1/\sqrt{N}\\), so each additional digit of accuracy takes roughly 100 times as many darts.

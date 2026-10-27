---
layout: post
title: "C-WAVE: CubeSat Constellation for Crop Water-Stress Monitoring (SPCE 6200)"
date: 2026-07-28
categories: [Academic Project]
slug: c-wave-constellation-design
---

For the final project in Spacecraft and Satellite Architecture Design and Integration (SPCE 6200, part of Auburn's Space Systems Graduate Certificate), I designed an Earth-observation constellation end to end: mission definition, payload optics, orbit and coverage, communications, power, pointing, thermal, mass, Δv, and disposal. The customer was a hypothetical California water authority that needs parcel-scale vegetation data to manage irrigation during drought.

## Mission

**Deliver consistent, time-resolved NDVI (Normalized Difference Vegetation Index) measurements across California's agricultural regions to characterize crop water stress.**

| Requirement | Target | Result |
|---|---|---|
| Spatial resolution | < 10 m | 8.64 m ground sample distance |
| Revisit | ≤ 5 days | 2.78 days worst case, 1.04 days mean |
| Radiometric stability | NDVI drift < 0.02 over 10 years | Drives lossless compression and onboard calibration |
| Lifetime | 10 years | Sets battery cycling, Δv, and coating degradation |

Revisit was the requirement that mattered most to the customer. Water stress develops over days, so monthly data wouldn't support any of the decisions the mission exists for.

## Payload

The payload is a four-band (blue, green, red, near-infrared) pushbroom imager sized for a 3U CubeSat: 5.5 µm pixels, a 350 mm effective focal length, and 2,048 cross-track detector elements running at 935 lines/s. At 550 km it delivers 8.64 m cross-track and 8.02 m along-track sampling. The near-square pixels confirm that the line rate matches the ground-track velocity.

Resolution closed easily. The cost was a 17.7 km swath, which is why the mission needs a constellation rather than a single satellite.

## Orbit and Coverage: The Central Finding

Sizing for continuous coverage would take **2,265 satellites**. Because the customer's need is a revisit time rather than constant coverage, I sized the constellation statistically instead: a binomial access model at California's latitude gives **25 satellites** for a mean revisit under 5 days.

Simulating those 25 satellites against 18 California target sites over 30 days showed that the count alone wasn't the answer. Their arrangement was:

| Configuration | Mean revisit | Worst-case revisit | Meets requirement? |
|---|---|---|---|
| 25 sats, 1 plane, sun-synchronous (97.6°) | ~4.0 days | 16.95 days | No. One groundwater hotspot was imaged once in 30 days. |
| 25 sats, Walker 25/5/1, 43° inclination | 1.04 days | 2.78 days | Yes, at all 18 sites |

Both options use the same satellites, mass, and cost; only the geometry differs. The statistical model reported only an average revisit, so it could never have flagged a 17-day gap. Only the per-site simulation revealed it. A 43° inclination concentrates passes over California's 32–40° N latitude band and matches SpaceX's mid-inclination rideshare.

I also chose the coverage figure of merit deliberately: **95% of targets revisited within 5 days**. A pure average would have accepted the failing sun-synchronous design. A strict worst case is too conservative, given that the simulation doesn't model cloud cover.

## Subsystem Budgets

| Budget | Status | Governing number |
|---|---|---|
| Payload / GSD | Closes | 8.64 m vs < 10 m |
| Coverage / revisit | Closes | 2.78-day worst case vs 5 days |
| Data | Closes comfortably | 2.56 vs 6.0 GB/day (+134%) |
| X-band link | Closes, thinly | +3.07 dB margin |
| Mass | Closes | 4.0 kg vs 5.58 kg allowable |
| Power | Marginal | −1.2% array, −9.0% battery as allocated |
| Δv | Marginal | 145 vs 156 m/s (+7.6%) |
| Thermal | Marginal | Sunlit cases close; eclipse heater unresolved |
| Pointing | Does not close | 73–149 m geolocation error vs 8.64 m required |

A few of the trades:

- **Data:** I chose 2:1 *lossless* compression. Lossy artifacts could look like real vegetation-index drift and corrupt the mission's central science product.
- **Power:** the always-on bus, not imaging or downlink, drives daily energy (89% of 214 Wh/day). Body-mounted cells supply only 7.3 W against a 25.3 W requirement on every face of a 3U, so deployable arrays are mandatory rather than an open trade.
- **Δv:** with propellant expressed as a fraction \\(K\\) of dry mass, the rocket equation becomes \\(\Delta v = g_0 I_{sp} \ln(1+K)\\), independent of spacecraft size. A first pass at \\(K = 0.06\\) (125.7 m/s) missed the 145 m/s requirement for plane phasing, stationkeeping, and disposal, so I iterated to \\(K = 0.075\\) (156 m/s). I traded electric propulsion and rejected it, because the power budget had no room for it.
- **Plane phasing:** a rideshare drops every satellite into one plane. Reaching five planes means either several launches or a J2 differential drift: drop to 500 km and wait about 514 days for the orbit plane to drift 72° (about 1.4 years) for 55 m/s. That makes it a schedule problem rather than a propellant problem.

## Thermal Surface Selection

The binding thermal constraint was the battery's 0 to +40 °C charge window, cycled about 15 times a day for ten years. I ran a seven-coating trade, and no single catalog finish satisfied both sunlit cases:

- **White paint** (α = 0.17, ε = 0.86) passes the hot case but leaves the battery at −13.5 °C in sunlit standby, too cold to charge.
- **Anodized aluminum** fixes standby but overshoots the hot case by 7.5 °C.
- **Low-ε finishes** look ideal in eclipse but reach +245 to +365 °C in sunlight.

The selected surface is an 85% white / 15% black blend (α = 0.28, ε = 0.86), with no radiator required. I wrote it as a specification rather than a recipe: **α = 0.28 ± 0.03 with ε ≥ 0.86, held across ten years of degradation.** The feasible window is narrow. With the margined peak power, the acceptable α range shrinks to 0.266–0.277, tighter than a real coating's manufacturing tolerance. At ε = 0.80, no α works at all.

The single-node model also called for a 23.4 W survival heater on a spacecraft whose entire eclipse power mode is 8 W. That number is an artifact of assuming steady state: a 4 kg spacecraft doesn't reach equilibrium during a 35-minute eclipse. It also exposed a gap between the budgets. The power budget's 8 W eclipse mode already includes a heater allocation, and the thermal analysis sized another heater on top of it. Resolving that needs a transient, multi-node model.

## Pointing: Moving a Requirement to the Right Subsystem

Parcel-level enforcement needs geolocation within one pixel (8.64 m), which is about 3.2 arcseconds at 550 km. The error budget falls short by roughly an order of magnitude, and better hardware doesn't fix it. Mechanical alignment alone (53 m) is six times the requirement, and every term in the attitude and orbit knowledge chain individually exceeds a pixel. Even a flight-proven CubeSat ADCS misses by about 3× on its own.

My conclusion was that geolocation is a **ground-segment** requirement, not an ADCS requirement. Onboard pointing gets the image within roughly 100–180 m, and ground registration against control points and a terrain model gets it to sub-pixel accuracy, which is how commercial imaging constellations do it. Target acquisition, by contrast, is easy: the pointing error is under 2% of the half-swath.

## Disposal and Operations

At 550 km, natural decay takes 15–25 years, which violates the 5-year post-mission disposal guideline. Since the spacecraft already needs propulsion for phasing, a 70 m/s perigee-lowering burn at end of life is the simplest compliant option. A drag sail would add a second deployable mechanism and remove attitude control at the moment the satellite becomes a collision hazard. The 550 km shell is also the most crowded in LEO: about 0.4 conjunction screening events per satellite per day, or roughly 3,700 a year across the fleet. That makes automated conjunction screening a ground-segment requirement from day one.

## Lessons

- **A model can't report a metric it doesn't represent.** The statistical sizing model had no notion of worst-case revisit, so it couldn't warn about one. Only the per-site simulation exposed the failing design.
- **Budgets can each be correct and still contradict each other.** The power and thermal budgets each held up on their own terms, but the interface between them was never defined.
- **Some requirements belong to a different subsystem than they first appear to.** Geolocation looked like a pointing problem and turned out to be a ground-processing problem.

The open issues I would take to a preliminary design review: the power/thermal heater interface, unmodeled cloud cover (every revisit figure is an upper bound for an optical payload), an unbudgeted 3U volume shared by deployable arrays, propulsion, and thermal hardware, and a link budget that rests on a receive-antenna gain I derived myself rather than one published by the ground-station provider.

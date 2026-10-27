---
layout: post
title: "C-WAVE: CubeSat Constellation for Crop Water-Stress Monitoring (SPCE 6200)"
date: 2026-07-28
categories: [Academic Project]
slug: c-wave-constellation-design
---

For the final project in Spacecraft and Satellite Architecture Design and Integration (SPCE 6200, part of Auburn's Space Systems Graduate Certificate), I designed an Earth-observation CubeSat constellation end to end. The design covered mission definition, payload optics, orbit and coverage, ground contact and operations, data, link, power, pointing, thermal, mass and launch, Δv, and disposal. The customer was a hypothetical California water authority that needs parcel-scale vegetation data (NDVI) to manage irrigation during drought.

*This page is a high-level summary. I've left out the specific design values and analysis details because the instructor may reuse this project in future courses.*

## The Problem

The customer needed three things at once:
- resolution fine enough to resolve individual farm parcels,
- revisits frequent enough to catch water stress as it develops over a few days,
- calibration stable enough that a vegetation trend measured over a decade reflects the crops, not the instrument.

The resolution requirement closed comfortably with a compact multispectral imager on a 3U CubeSat. The cost was a narrow swath, which meant revisit, not resolution, would drive the architecture.

## Key Conclusions

**Size the constellation for revisit, not continuous coverage.** Requiring every target to be in view at all times would have taken thousands of satellites. The customer's decisions happen on a multi-day cycle, so the right requirement is a maximum time between looks, and that brought the constellation down to a few dozen satellites.

**Satellite count isn't the answer; arrangement is.** A statistical sizing model gave a satellite count that met the *average* revisit target. A per-site simulation then showed that the obvious arrangement left some high-priority sites unseen for weeks. Rearranging the same satellites into a different orbit geometry met the requirement at every site, with margin, at the same mass and cost. The statistical model couldn't have caught this, because it has no concept of worst-case revisit.

**Pick a coverage metric that matches the customer's real tolerance.** A pure average can hide a constellation that consistently fails a subset of targets. A strict worst case is too conservative, given that the analysis doesn't model cloud cover. I used a percentile: a set fraction of targets must be revisited within the deadline.

**Protect the science product in the data chain.** I chose lossless compression even though lossy would save more bandwidth. Compression artifacts could look like real vegetation-index drift and corrupt the mission's central measurement.

**Specify the thermal surface as a requirement, not a catalog part.** In a trade of common spacecraft coatings, none satisfied both the battery's charge temperature limits in sunlight and the hot case during downlink. The answer was a blended surface, specified as a target absorptance/emittance window with tolerance and allowance for ten years of degradation. That window turned out to be narrow, about as tight as a real coating's manufacturing tolerance.

**Recognize when a model's answer is an artifact.** The single-node thermal model called for a survival heater larger than the spacecraft's entire eclipse power budget. That's a product of the steady-state assumption: a small spacecraft doesn't reach equilibrium during one eclipse. It also exposed an undefined interface: the power budget already included heater power that the thermal analysis then sized again on top of it. Both budgets were correct on their own terms but inconsistent with each other.

**Assign the requirement to the right subsystem.** Parcel-level geolocation looked like an attitude-control requirement, but the pointing error budget missed it by about an order of magnitude, spread across every term in the knowledge chain. Better attitude hardware couldn't fix that. Geolocation belongs to the ground segment: onboard pointing gets the target in the frame, and ground registration against control points delivers sub-pixel accuracy, as commercial imaging constellations do.

**Commit to deployable arrays and propulsion.** Body-mounted solar cells couldn't meet the power need, so deployable arrays were mandatory. Propulsion became required for three independent reasons: spreading satellites into separate orbit planes after a shared rideshare launch, stationkeeping against drag, and end-of-life disposal within the 5-year guideline. I traded electric propulsion and rejected it, because the power budget had no room for it.

**Operations are part of the design.** The orbit that gives the best coverage sits in one of the most crowded parts of low Earth orbit. Collision screening across the fleet becomes a routine daily workload, so automated conjunction screening has to be designed into the ground segment from the start.

## Budget Status

| Budget | Status |
|---|---|
| Payload resolution, coverage/revisit, data, mass, launch | Close |
| Communications link | Closes, thinly |
| Power, Δv | Marginal |
| Thermal | Marginal; eclipse heater unresolved |
| Pointing (geolocation) | Doesn't close onboard; assigned to ground processing |

## Lessons

- **A model can't report a metric it doesn't represent.** The worst-case revisit problem was invisible until I used a tool that tracked each site individually.
- **Budgets can each be correct and still contradict each other.** The power/thermal interface was never defined, and it became the most important unresolved item.
- **Some requirements belong to a different subsystem than they first appear to.**

The open issues I'd take to a preliminary design review are the power/thermal heater interface, cloud cover (unmodeled, so every revisit figure is an upper bound for an optical payload), unbudgeted interior volume shared by deployable arrays, propulsion, and thermal hardware, and a communications link margin that depends on an unverified ground-station antenna figure.

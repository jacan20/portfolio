---
layout: post
title: "NAR/Tripoli L1 Certification Vehicle"
date: 2023-10-22
categories: [Personal Project]
redirect_from:
  - "/personal project/2023/10/07/nar-l1-certification-vehicle.html"
---

My Level 1 high-power certification rocket, flown on October 22, 2023.

## Vehicle Overview

- **Airframe:** 2.56-inch diameter
- **Nosecone:** 3D printed
- **Payload:** a custom LoRa GPS tracker (below)
- **Motor:** H73J

Level 1 is the first tier of high-power rocketry certification with NAR and Tripoli. It qualifies the flier to buy and fly H- and I-impulse motors, and it's the prerequisite for the [Level 2 vehicle]({{ '/projects/nar-l2-certification-vehicle/' | relative_url }}) I'm building now.

## Payload: LoRa GPS Tracker

The payload was a GPS tracking system I built from two Heltec LoRa boards, and I wrote all of the Arduino code for both. The tracker rides in the rocket's payload compartment and broadcasts its GPS position over LoRa radio. The ground station receives those packets and shows the tracker's latest location, the signal strength (RSSI), and the time since the last packet. On the L1 flight, the ground station received the tracker's position.

<div style="display: flex; flex-wrap: wrap; gap: 1rem; justify-content: center;">
  <figure style="margin: 0; flex: 1 1 260px; max-width: 420px;">
    <img src="{{ '/Media/L1/tracker-screen.jpg' | relative_url }}" alt="Tracker board with its screen showing the L1 Tracker label, latitude, longitude, and satellite count" style="width: 100%;"/>
    <figcaption style="text-align: center; font-size: 0.9em;">Tracker: GPS fix and satellite count</figcaption>
  </figure>
  <figure style="margin: 0; flex: 1 1 260px; max-width: 420px;">
    <img src="{{ '/Media/L1/ground-station-screen.jpg' | relative_url }}" alt="Ground station board showing RSSI, time since the latest packet, and the tracker's location" style="width: 100%;"/>
    <figcaption style="text-align: center; font-size: 0.9em;">Ground station: received tracker location and RSSI</figcaption>
  </figure>
</div>

*Coordinates on the screens are blurred.*

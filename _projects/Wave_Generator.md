---
layout: page
title: Wave Generator (IOT)
description: Custom remote controlled wave generator with MQTT.
img: assets/img/wave_generator.jpg
importance: 3
category: work
related_publications: false
mermaid:
  enabled: true
  zoomable: true
---


<video width="640" height="480" controls>
  <source src="https://github.com/ELROSTEM/shoreline-wave-analysis/raw/main/OurWave2.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>


In this project, I built a system to control a wave tank remotely using a stepper motor, an Arduino, and a custom Python GUI. By leveraging the MQTT protocol, the system allows for real-time control of wave patterns, speed, and distance without needing a physical connection to the hardware.

{% include figure.liquid loading="eager" path="assets/img/wave_generator_architecture.png" class="img-fluid rounded z-depth-1" %}

## How It Works

The system consists of two main components: a Python-based control dashboard and an Arduino-based motor controller. They communicate over WiFi using a public MQTT broker.

### 1. The Control Dashboard (Python)

{% include figure.liquid loading="eager" path="assets/img/wave_generator_gui.png" class="img-fluid rounded z-depth-1" %}

The user interface is built with Streamlit. The dashboard allows users to select a wavetype (e.g., MonoPulse), set the speed, and define the distance the motor should travel.

When the "Update and run" button is clicked, the app connects to the `test.mosquitto.org` broker and publishes a formatted string containing the wave parameters to the topic `wave/motor`.

### 2. The Hardware Controller (Arduino)
On the hardware side, an Arduino (using the `WiFiNINA` library) connects to WiFi and subscribes to the same `wave/motor` topic. 

The main loop listens for incoming messages. When a message is received, it parses the string to extract the `wavetype`, `speed`, and `distance`. Based on the "wavetype" received, the Arduino executes specific motor movements using the `AccelStepper` library:

* **MonoPulse (Type 1):** Moves the motor to a set distance and immediately reverses it to create a single pulse.
* **Wavetype 2:** Constant 10 mono-pulses.
* **Wavetype 3:** Variable 10 mono-pulses.

## Conclusion

This project was a simple IoT project where the physical hardware can now be controlled from a user friendly software interface anywhere on the network.
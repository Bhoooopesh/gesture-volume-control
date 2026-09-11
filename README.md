# Touchless Gesture Volume Controller

A real-time Computer Vision application that uses AI-powered hand tracking to control your computer's master volume through natural hand gestures. 

This repository documents my first hands-on engineering journey into spatial computing, real-time image processing, and human-computer interaction (HCI).

---

## The Core Problem & Solution
* **The Challenge:** Traditional volume controls require physical keyboards, mice, or sliders, which can be inefficient during presentations or media playback.
* **The Engineering Solution:** By tracking the Euclidean distance between a user's **Thumb** (Landmark 4) and **Index Finger** (Landmark 8), this Python script accurately maps physical hand coordinates to system audio levels (0% to 100%) with minimal latency.

---

## Live Demonstration & System Architecture

### System Output in Action


### 🧩 How the Data Flows
1. **Frame Capture:** OpenCV pulls raw video matrices from the host webcam at 30+ FPS.
2. **AI Inference:** MediaPipe processes the frame, extracting a 21-point spatial coordinate map of the human hand.
3. **Mathematical Mapping:** The pixel distance between the finger tips is computed and normalized into a system volume range using mathematical interpolation.
4. **Hardware Execution:** The backend audio API interacts directly with the computer's sound card to apply the changes instantly.

---

## Production Tech Stack
* **Core Language:** Python
* **Computer Vision Framework:** OpenCV (Open Source Computer Vision Library)
* **Machine Learning Pipeline:** Google MediaPipe (Hand Landmarking Topology)
* **Hardware Interface API:** PyCaw / OS Audio Bindings

---

## Project Structure & Implementation

The entire pipeline is neatly containerized within a single, highly readable script for ease of testing:

```
```

]
---

## Key Personal Learning Takeaways
As a beginner in software engineering, building this project taught me critical foundation blocks:
* **Environment Management:** Resolving package dependencies and working with libraries like OpenCV.
* **Data Pipelines:** Understanding how real-time matrix data (video frames) is transformed into spatial coordinates.
* **Algorithmic Logic:** Working with mathematical ranges to translate physical distances into software commands.

***

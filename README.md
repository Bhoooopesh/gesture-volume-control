# Touchless Gesture Volume Controller

A real-time Computer Vision application that uses AI-powered hand tracking to control your computer's master volume through natural hand gestures. 

This repository documents my first hands-on engineering journey into spatial computing, real-time image processing, and human-computer interaction (HCI).



## The Core Problem & Solution
* **The Challenge:** Traditional volume controls require physical keyboards, mice, or sliders, which can be inefficient during presentations or media playback.
* **The Engineering Solution:** By tracking the Euclidean distance between a user's **Thumb** (Landmark 4) and **Index Finger** (Landmark 8), this Python script accurately maps physical hand coordinates to system audio levels (0% to 100%) with minimal latency.


## Live Demonstration & System Architecture

### System Output in Action
## Max Volume ( Distance between two fingers is more)
<img width="1244" height="1280" alt="volume up" src="https://github.com/user-attachments/assets/1bc2e8e7-e0ec-44c3-b4e9-7dac6f6433d4" />
## Low Volume ( Distance between two fingers is less)
<img width="1280" height="1247" alt="volume down" src="https://github.com/user-attachments/assets/a46725a0-0788-4720-aa2e-ce42cdaeb087" />


## How the Data Flows

The system follows this workflow:

1. **Frame Capture**  
   OpenCV captures live video frames from the computer's webcam.

2. **AI Inference**  
   MediaPipe processes each frame and detects the hand using **21 landmark points**, providing their normalized spatial coordinates.

3. **Mathematical Mapping**  
   The program calculates the distance between the **thumb tip (landmark 4)** and **index-finger tip (landmark 8)**. This distance is mapped to a **0–100% volume range**.

4. **System Volume Control**  
   The calculated volume value is sent through **Pycaw** to control the **Windows system's master playback volume** in real time.

## Production Tech Stack
* **Core Language:** Python
* **Computer Vision Framework:** OpenCV (Open Source Computer Vision Library)
* **Machine Learning Pipeline:** Google MediaPipe (Hand Landmarking Topology)
* **Hardware Interface API:** PyCaw / OS Audio Bindings


## Python code


```
import cv2, mediapipe as mp, math
from mediapipe.tasks import python
from mediapipe.tasks.python import vision
from pycaw.pycaw import AudioUtilities

volume = AudioUtilities.GetSpeakers().EndpointVolume

options = vision.HandLandmarkerOptions(
    base_options=python.BaseOptions(model_asset_path="hand_landmarker.task"),
    num_hands=1
)
detector = vision.HandLandmarker.create_from_options(options)

cap = cv2.VideoCapture(0)

while cap.isOpened():
    ok, frame = cap.read()
    if not ok:
        continue

    frame = cv2.flip(frame, 1)
    h, w, _ = frame.shape
    rgb = cv2.cvtColor(frame, cv2.COLOR_BGR2RGB)

    result = detector.detect(
        mp.Image(image_format=mp.ImageFormat.SRGB, data=rgb)
    )

    if result.hand_landmarks:
        hand = result.hand_landmarks[0]

        x1, y1 = int(hand[4].x * w), int(hand[4].y * h)
        x2, y2 = int(hand[8].x * w), int(hand[8].y * h)

        distance = math.hypot(x2 - x1, y2 - y1)
        percent = max(0, min(100, int((distance - 30) / 220 * 100)))

        volume.SetMasterVolumeLevelScalar(percent / 100, None)

        cv2.line(frame, (x1, y1), (x2, y2), (255, 0, 255), 3)
        cv2.circle(frame, (x1, y1), 10, (255, 0, 255), -1)
        cv2.circle(frame, (x2, y2), 10, (255, 0, 255), -1)

        cv2.putText(
            frame, f"Volume: {percent}%",
            (20, 50), cv2.FONT_HERSHEY_SIMPLEX, 1, (255, 255, 255), 2
        )

    cv2.imshow("Hand Volume Control", frame)

    if cv2.waitKey(1) & 0xFF == ord("q"):
        break

cap.release()
cv2.destroyAllWindows()
detector.close()
```




## Key Personal Learning Takeaways
As a beginner in software engineering, building this project taught me critical foundation blocks:
* **Environment Management:** Resolving package dependencies and working with libraries like OpenCV.
* **Data Pipelines:** Understanding how real-time matrix data (video frames) is transformed into spatial coordinates.
* **Algorithmic Logic:** Working with mathematical ranges to translate physical distances into software commands.


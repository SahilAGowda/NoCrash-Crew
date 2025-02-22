# Hand-Detection
This repository contains all the code we plan to use for implementing a Hand Detection system, which powers a mechanical crane on wheels.

## Overview
We used the functions provided by MediaPipe for [Hand Detection](https://google.github.io/mediapipe/solutions/hands.html) as the core of the program. We also used [cv2](https://opencv.org) libraries to enable camera live feed and display the results.

Then, we turn this `.py` file into an executable and, using the [ROS](https://www.ros.org) software, we convert the matrix output by the program into movement coordinates. These coordinates are then passed to the Arduino file `Arduino_code.ino`, which powers a bot equipped with the required components.

## The Code
Computing the landmarks for the different IDs issued by MediaPipe, we first run a configuration cycle (`CONFIG_CYCLE`) to measure the extremes of the hand in the frame.

The cycle reruns every time there is a `NULL` detection on the screen, making the code "switchable" between different people during the same run.

We then put all the detected gestures into a matrix for transmission:

```
A = [TRNSL, CLAWED]
    [ROTAT, SWITCH]
```
* `TRNSL` determines the translation state of the bot, denoted by `[-1, 0, 1]` for `[BWD, STOP, FWD]`. This is detected by the relative position of the hand on the screen.
* `CLAWED` controls the crane's claw state, denoted by `[0, 1]` for `[NOT_CLAWED, CLAWED]`. This is detected by the relative position of the middle fingertip to the palm.
* `ROTAT` determines the bot's rotation state, denoted by `[-1, 0, 1]` for `[ROT_R, NO_ROT, ROT_L]`. This is detected by the orientation of the hand on the screen; the bot rotates in the direction the thumb is pointing.
* `SWITCH` determines the bot's operational state, denoted by `[0, 1]` for `[OFF, ON]`. The bot turns `ON` when it detects `NO_ROT` and `STOP` for the first time.

> **Note:** The bot never rotates while translating to maintain precise movements. If `TRNSL` is anything other than `STOP`, `ROTAT` remains `NO_ROT`, regardless of the hand's orientation.

## Hardware Configuration
Each sensor plays a unique role in detecting obstacles and hazards:

- **LiDAR (Light Detection and Ranging):** Uses laser pulses to create a 3D map of the surroundings, detecting objects at long distances with high accuracy.
- **GPS (Global Positioning System):** Provides precise location data of the crane and moving obstacles in real time.
- **Ultrasonic Sensors:** Detects objects at close range (blind spots) where LiDAR might struggle with smaller obstacles.

All sensors are strategically mounted on the crane to ensure full coverage. The sensor data is processed by an Edge AI device for real-time decision-making.

### Data Collection & Processing
Once the sensors collect data, it follows this flow:
1. **LiDAR scans** the environment and creates a 3D point cloud, mapping the position of obstacles.
2. **GPS provides** the exact location of the crane and moving objects.
3. **Ultrasonic sensors detect** close-range hazards, covering blind spots.
4. **Multi-Sensor Fusion Algorithms** integrate data from all three sources for accuracy.
5. **AI Model Analysis:** A deep-learning model classifies obstacles, determines distances, and predicts movement.

### Operator Notification & Warning System
When an obstacle is detected, the AI triggers multiple safety mechanisms:

- **Audio & Visual Alerts:** A buzzer and warning light inside the crane cabin alert the operator.
- **Holographic AR Display:** Operators using AR headsets (e.g., Microsoft HoloLens) see real-time overlays indicating the obstacle’s position and distance.
- **Automated Emergency Response:** If the obstacle is too close, the AI slows down or stops the crane automatically to prevent collisions.

### Visualization of Obstacle Detection
To help the operator see obstacles clearly, real-time visualizations include:

- **Camera View with Object Bounding Box:** A live feed from a mounted camera (RGB + LiDAR fused) highlights detected obstacles with bounding boxes and distance indicators.
- **3D Map Display:** A dashboard inside the crane cabin displays a real-time 3D visualization of nearby objects.
- **Color-Coded Alerts:** Obstacles are color-coded based on proximity: `Green = Safe`, `Yellow = Caution`, `Red = Danger`.

## Get the Code!
If you want to test the code without ROS integrations, you can download `nROSmaster.py` and run it with the required dependencies.

To get the full repository, run the following command:

```
git clone https://github.com/Fifirex/Hand-Detection.git
```

Once you have the code, install the required Python libraries:

```
pip install numpy opencv-python mediapipe
```

Replace `pip` with `pip3` if needed.

## The Team
This project is created for the DIY Assignment this semester by **NoCrash Crew**.

### Team Lead
- **Shreya**


# LIMO Web Control Dashboard

This project explores the development of a **web-based control dashboard** for the **AgileX LIMO robot** during my internship at **LPRI (Laboratoire Pluridisciplinaire de Recherche et Innovation)** at EMSI.

The goal is to build a browser-based interface that allows a user to **monitor and control the robot** without relying on the official control application.

---

## Objective

Develop a web dashboard that enables:

- Viewing the robot's camera feed
- Controlling robot movement from a web browser
- Providing a simple and accessible interface for teleoperation

The dashboard is intended to work on any device connected to the same network as the robot.

---

## Current Progress

Initial development focuses on the **control interface prototype**.

Implemented so far:

- A simple **HTML-based control page**
- **Keyboard-based driving input (WASD)**
- **Hold-based control logic** instead of single key presses
- Support for **multiple simultaneous inputs** (e.g., forward + left)

This control model mimics natural driving behavior used in many teleoperation systems.

---

## Control Scheme

| Key | Action |
|----|------|
| W | Move forward |
| S | Move backward |
| A | Turn left |
| D | Turn right |

Features of the control logic:

- Continuous movement while keys are held
- Combined inputs (e.g., forward-left, forward-right)
- Basic driving-style behavior

---

## Planned System Architecture

The expected system architecture is:
```
Web Browser (Dashboard Interface)
↓
Backend Bridge
↓
ROS2
↓
Robot Base (/cmd_vel)
```

For camera streaming:
```
Robot Camera
↓
ROS2 Camera Topic
↓
Backend / Streaming Bridge
↓
Web Dashboard
```

The backend will translate browser inputs into ROS2 commands that control the robot.

---

## Next Steps

Planned next steps include:

- Connecting the dashboard to a backend service
- Converting control inputs into ROS2 velocity commands
- Integrating the robot camera feed into the dashboard
- Testing the interface directly on the AgileX LIMO robot

---

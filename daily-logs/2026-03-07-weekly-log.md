# Weekly Learning Log (Feb 28 - Mar 6, 2026)

## Overview
This week marked the beginning of my hands-on IoT journey with my lab mentor. I completed online courses and immediately applied the concepts through practical projects involving Arduino, sensors, Node-RED, and MQTT protocol.

## First Half of the Week: Online Courses

### Coursera Courses (UCI)
1. **Introduction to the Internet of Things**
   - Covered fundamental IoT concepts and architecture
   - Platform overview and use cases
   - Prepared foundation for practical implementations

2. **The Arduino Platform and C Programming**
   - Learned embedded C programming basics
   - Arduino IDE familiarization
   - Hardware-software interaction principles

## Second Half of the Week: Practical Projects with Mentor

### Day 1: Distance Sensor System & Node-RED Alert System

#### Project 1: Distance Sensor with Serial Monitor
**Objective:** Read distance sensor data and output to serial monitor with delay

**Technical Details:**
- **Hardware Used:** Arduino board, ultrasonic distance sensor
- **Implementation:** Created Arduino sketch that:
  - Reads analog/digital input from distance sensor
  - Processes distance values
  - Outputs data to serial monitor with configurable delay
  - Established baseline for sensor integration

#### Project 2: Node-RED Data Visualization
**Objective:** Graph distance measurements over time using Node-RED

**Technical Details:**
- **Architecture:** Serial monitor input → Node-RED flow → Real-time graph
- **Node-RED Components:**
  - Serial input node (receiving Arduino data)
  - Data parsing nodes
  - Dashboard graph node for visualization
- **Functionality:** Real-time distance vs. time graph display

#### Project 3: Alert System with Email Notification
**Objective:** Create automated alert when distance drops below threshold

**Technical Details:**
- **Platform:** Fully implemented within Node-RED
- **Logic Flow:**
  - Monitor distance sensor values
  - Compare against set threshold
  - Trigger alert when threshold breached
  - Send email notification automatically
- **Outcome:** Successful alert system deployment

---

### Day 2: ESP32 & MQTT Protocol Integration

#### Challenge: ESP32 Connection Issues
**Problem:** ESP32 failed to connect via standard cable connection
**Solution:** Discovered ESP32 required manual boot sequence
**Learning:** Hardware-specific configuration requirements and troubleshooting

#### MQTT Protocol & HiveMQ Learning
**What I Learned:**
- **MQTT (Message Queuing Telemetry Transport):**
  - Lightweight publish-subscribe messaging protocol
  - Ideal for IoT applications with bandwidth constraints
  - Broker-based architecture for decoupled communication  
  
- **HiveMQ Broker:**
  - MQTT broker service for message distribution
  - Cloud-based broker for remote connectivity
  - Topic-based message routing

#### Project: ESP32 WiFi & MQTT Implementation

**Technical Architecture:**
1. **ESP32 Setup:**
   - Connected to WiFi network
   - Configured with MQTT broker credentials (HiveMQ)
   - Programmed in Arduino IDE

2. **Data Generation & Publishing:**
   - Arduino sketch generates random numbers
   - Publishes to HiveMQ broker at regular intervals (every few seconds)
   - Uses specific MQTT topic for organization

3. **Node-RED Subscriber:**
   - Added MQTT input node in Node-RED
   - Subscribed to same topic as ESP32
   - Configured broker address and port
   - Output values to debug menu for monitoring

**Outcome:** Successfully demonstrated:
- WiFi connectivity on ESP32
- MQTT publish/subscribe pattern
- Remote sensor data transmission via MQTT broker
- Cross-platform integration (Arduino IDE → HiveMQ → Node-RED)
- Mentor approval of implementation

---

## Key Technical Learnings
- **Arduino C Programming:** Sensor interfacing and serial communication
- **Node-RED:** Visual programming for IoT data processing and visualization
- **MQTT Protocol:** Pub-sub messaging paradigm for distributed IoT systems
- **HiveMQ:** Cloud broker implementation and topic management
- **ESP32:** WiFi-enabled microcontroller programming and connectivity
- **Problem-Solving:** Hardware troubleshooting and finding alternative solutions

---

## Next Steps
- **Upcoming:** Start working on AgileX LIMO project with mentor
- **Schedule:** Meeting scheduled for next Tuesday (Mar 11, 2026)
- **Focus:** Apply learned IoT concepts to robotics platform

---

## Reflection
This week exceeded my expectations in practical application. Moving from theoretical coursework to hands-on implementation within a single week demonstrated the power of guided learning. The mentor's approach of introducing problems and having me find solutions accelerated my understanding of IoT ecosystems.

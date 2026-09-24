# 🌡️ IoT Smart Temperature Monitoring System

## 📋 Project Overview
This project is a distributed Internet of Things (IoT) system designed to monitor ambient temperature, trigger state-based alarms, and automatically adjust a physical actuator (simulating a smart window) to regulate the environment. The system allows both automated control via a central backend and manual local overrides.

It features a multi-node architecture communicating via **MQTT** and **Serial Connection**, orchestrated by a concurrent Python backend and visualized through a real-time web dashboard.

### 🎥 Demo
*Check out the system in action:* [Click here to watch the demonstration video](./documentation/video.mp4)

---

## 🏗️ System Architecture
The system is divided into four main subsystems, designed with high cohesion and low coupling:

1. **Temperature-Monitoring Subsystem (ESP32-s3)**
   * **Role:** Edge node reading temperature data and publishing it to the MQTT broker.
   * **Tech:** C++, FreeRTOS, PubSubClient (MQTT).
   * **Design:** Asynchronous Event-Driven Finite State Machine (FSM). Handles WiFi/MQTT drops and reconnects automatically without blocking the execution flow.

2. **Window-Controller Subsystem (Arduino UNO)**
   * **Role:** Actuator node controlling a servo motor (window) and an LCD screen, with a potentiometer and button for manual overrides.
   * **Tech:** C++, Object-Oriented design.
   * **Design:** Asynchronous FSM. Implements the **Strategy Design Pattern** to abstract hardware components, ensuring highly modular, decoupled, and maintainable code.

3. **Control Unit Backend (Python)**
   * **Role:** The core orchestrator. Analyzes temperature trends, manages system state transitions (Normal, Hot, Too Hot, Alarm), and dispatches commands to the Edge and Actuator nodes.
   * **Tech:** Python, Flask, Paho-MQTT, Multithreading.
   * **Design:** Multithreaded architecture. Uses dedicated background threads for non-blocking I/O operations (Serial communication, MQTT callbacks) to ensure real-time responsiveness while serving the web API.

4. **Dashboard Frontend (Web)**
   * **Role:** Single Page Application (SPA) for real-time monitoring and remote control.
   * **Tech:** HTML5, CSS3, JavaScript.
   * **Design:** Uses HTTP polling (`/api/status`) for real-time updates and REST API POST requests (JSON format) to send commands to the central unit.

---

## 🧠 Software Engineering Highlights
A strong emphasis was placed on software engineering principles to ensure modularity, responsiveness, and reliability across the distributed system:
* **Finite State Machines (FSM):** All subsystems (Arduino, ESP32, Python) are modeled strictly using asynchronous FSMs, eliminating the need for blocking `delay()` calls and ensuring real-time hardware responsiveness.
* **Strategy Pattern:** Hardware interactions in the C++ embedded code are abstracted using interfaces (e.g., `ServoMotor`, `UserInputSource`), making the business logic hardware-agnostic.
* **Concurrency:** The Python backend safely handles HTTP requests, MQTT callbacks, and Serial data streams concurrently using Python threading, avoiding bottlenecks during heavy network or serial I/O.

### 📊 Architectural FSM Diagrams
<details>
<summary><b>Click to expand System State Machines</b></summary>

**1. Control Unit Backend FSM**
![Control Unit FSM](./documentation/python_fsm.png)

**2. Temperature Monitor (ESP32) FSM**
![ESP32 FSM](./documentation/esp32_fsm.png)

**3. Window Controller (Arduino) FSM**
![Arduino FSM](./documentation/arduino_fsm.png)

</details>

---

## ⚙️ Hardware Components
* **Microcontrollers:** 1x Arduino UNO, 1x ESP32-s3
* **Sensors & Actuators:** TMP36 Temperature Sensor, Servo Motor
* **UI/Peripherals:** 16x2 LCD Display, Potentiometer, Push Button, LEDs (Red/Green), Resistors

---

## 🚀 Getting Started

### Prerequisites
* Arduino IDE (or PlatformIO)
* Python 3.x
* An active MQTT Broker (e.g., [Eclipse Mosquitto](https://mosquitto.org/) running locally on port 1883)

### Installation & Setup

1. **Hardware Setup:** 
   Wire the components according to the schematics provided in the `/documentation` folder.

2. **ESP32 Node:** 
   Flash the ESP32 with the code in the `TemperatureMonitor` folder. Make sure to update the WiFi credentials and MQTT Broker IP inside the configuration file.

3. **Arduino Node:** 
   Flash the Arduino UNO with the code in the `WindowController` folder.

4. **Control Unit & Dashboard:**
   Open a terminal, navigate to the backend folder, and run:
   ```bash
   # Install dependencies
   pip install -r requirements.txt
   
   # Start the Python Control Unit
   python app.py
   ```
5. **Access the System:**
   Open a web browser and navigate to http://localhost:5000 to view the dashboard.

Developed by Nicolò Morini (nicomorini25@gmail.com)

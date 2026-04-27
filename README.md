# 🧠 Smart Agriculture IoT - Backend Logic Engine (Node-RED)

This repository/directory contains the backend logic and data flow configuration for the Smart Agriculture IoT Capstone Project. Built on top of **Node-RED**, this component serves as the central nervous system of the architecture, bridging the physical Edge Devices (ESP32 via MQTT) with the End-User application (ReactJS via REST API) and data persistence layer (PostgreSQL).

## 🎯 Core Responsibilities

1. **Message Routing (MQTT):** Subscribes to hardware telemetry and status topics, and publishes execution commands to the actuators.
2. **REST API Gateway:** Exposes HTTP endpoints allowing the ReactJS frontend to pull real-time data securely without direct exposure to the MQTT broker.
3. **Data Persistence:** Parses incoming IoT payloads and logs historical data (sensor telemetry and watering history) into the PostgreSQL database.
4. **State Management:** Utilizes Node-RED's context memory (`flow` variables) to cache the latest hardware states, enabling low-latency API responses.

## 🗺️ Communication Map

### MQTT Topics (Mosquitto Integration)
* **📥 Subscribe:**
  * `kebun/sensor/telemetri` : Receives 1-minute interval sensor data (Temperature, Humidity, Light, Soil Moisture).
  * `kebun/pompa/status` : Receives instant Closed-Loop Feedback (ACK) from the ESP32 when the pump state changes.
* **📤 Publish:**
  * `kebun/pompa/cmd` : Sends `ON` or `OFF` string payloads to trigger the physical relay on the ESP32.

### REST API Endpoints (Frontend Integration)
#### `GET /kebun/pompa/status`
Fetches the last known status of the water pump.
* **Response (200 OK):**
  ```json
  {
    "Pump": "ON" 
  }
(Note: Returns {"Pump": "UNKNOWN"} if no status has been reported since the server started).

🚀 Deployment Guide (Docker)
This Node-RED instance is designed to run in a containerized environment alongside the Mosquitto broker and PostgreSQL database using Docker Compose.

Prerequisites
Docker and Docker Compose installed on the host machine (Ubuntu 24.04 VPS).

An active internal Docker network linking to the Mosquitto container (iot_mosquitto).

Installation
Start the container via the main docker-compose.yml:

Bash
docker compose up -d nodered
Importing the Flows:

Access the Node-RED editor at http://<YOUR_VPS_IP>:1880.

Navigate to Menu (≡) > Import.

Paste the provided flows.json file from this repository to deploy the exact API and MQTT routing configuration.

🔒 Security Configuration
To prevent unauthorized access to the logic flows, the Node-RED UI editor is strictly secured using bcrypt authentication.

To configure the admin credentials:

Generate a bcrypt hash for your password inside the container:

Bash
docker exec -it iot_nodered node-red admin hash-pw
Edit the settings.js file located in the mounted Docker volume (nodered_data):

JavaScript
adminAuth: {
    type: "credentials",
    users: [{
        username: "admin", // Change to your preferred username
        password: "$2b$08$YOUR_GENERATED_HASH_HERE",
        permissions: "*"
    }]
}
Restart the container:

Bash
docker restart iot_nodered
🛠️ Tech Stack & Dependencies
Engine: Node.js / Node-RED

Core Nodes Used: mqtt in, mqtt out, http in, http response, change, function

Database Driver: node-red-contrib-postgresql (Ensure this is installed via the Palette Manager for database connections).

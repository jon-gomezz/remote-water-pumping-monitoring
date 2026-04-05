# Architecture

## Architecture Summary

The system follows an industrial IoT architecture designed to move operational data from remote water pumping stations to a centralized monitoring environment in Araka.

At each station, field signals are collected by a Siemens S7-1200 PLC. These data are exposed through Modbus RTU over RS485 and read by a Dragino RS485-LN device, which converts the information into LoRaWAN messages for long-range wireless transmission. The LoRaWAN traffic is received by a The Things Indoor Gateway (TTIG) and managed through The Things Network (TTN). From there, data are forwarded via MQTT to Node-RED, where they are decoded, processed, and displayed in dashboards. Finally, processed data are sent to ThingSpeak for storage, historical visualization, and additional analysis.

## High-Level Data Flow

```text
Field signals
  -> Siemens S7-1200 PLC
  -> Modbus RTU over RS485
  -> Dragino RS485-LN
  -> LoRaWAN
  -> TTIG gateway
  -> The Things Network (TTN)
  -> MQTT
  -> Node-RED in Araka
  -> ThingSpeak
```

## System Layers

### 1. Field Layer

The field layer contains the process signals coming from each pumping station. Depending on the station, these signals may include:

* Pump running status.
* Pump fault status.
* Tank level.
* Well level.
* Pressure.
* Flow.
* Chlorine-related variables.

These signals represent the operational state of each remote installation and are the starting point of the monitoring chain.

### 2. Control Layer

Each station is built around a Siemens S7-1200 PLC. The PLC is responsible for:

* Acquiring digital and analog signals.
* Organizing variables internally.
* Exposing the relevant data for communication.

The PLC provides the industrial robustness of the system and acts as the local control and acquisition unit at station level.

### 3. Local Communication Layer

The communication between the PLC and the LoRaWAN transmission device is based on Modbus RTU over RS485.

This layer serves as the bridge between industrial control hardware and the wireless communication system. In this project, the Dragino RS485-LN acts as the Modbus master and queries the PLC to retrieve the required values.

## LoRaWAN Communication Architecture

### 4. End Device Layer

The station-side communication endpoint is the Dragino RS485-LN. Its main role is to:

* Read station data through RS485.
* Prepare the payload.
* Transmit the information through LoRaWAN.

This device makes it possible to connect conventional industrial equipment to a long-range wireless IoT network without redesigning the local control logic.

### 5. Gateway Layer

The wireless transmission is received by a The Things Indoor Gateway (TTIG), which acts as the bridge between the radio network and the internet-connected backend.

Its function is to receive LoRa radio packets from the remote stations and forward them to the network server.

### 6. Network Server Layer

The LoRaWAN network server is provided by The Things Network (TTN).

In this architecture, TTN is responsible for:

* Device registration.
* Network management.
* Packet routing.
* Secure handling of LoRaWAN traffic.

A relevant architectural decision in the project was to use a LoRaWAN setup with an independent network server rather than a gateway with an embedded server. This approach offered better flexibility and scalability for a multi-station deployment.

## Data Processing and Visualization

### 7. MQTT Transport Layer

Once data reach TTN, they are forwarded through MQTT.

MQTT is used as the transport mechanism between the network platform and the central monitoring environment. This keeps the integration lightweight and practical for IoT-oriented data flows.

### 8. Monitoring and Visualization Layer

Node-RED is the main visualization and data-handling platform in the central environment.

Its responsibilities include:

* Subscribing to MQTT topics.
* Decoding incoming payloads.
* Transforming the received data.
* Distributing values to dashboard widgets.
* Presenting station information in near real time.

Node-RED was also a practical choice because it provided an accessible visual environment for building dashboards and interfacing with existing supervisory logic.

### 9. Edge Host Layer

Node-RED is hosted on an Orange Pi 3 LTS, which is used to keep the monitoring service running continuously.

This component provides a lightweight computing platform for:

* Continuous reception of station data.
* Local dashboard serving.
* Integration with downstream services.

### 10. Storage and Analytics Layer

ThingSpeak is used as the storage and cloud analytics layer.

Its role in the architecture is to:

* Store historical station data.
* Support trend visualization,
* Enable additional statistical analysis.
* Serve as a base for alerts or future anomaly-oriented logic.

This makes the overall solution more than a real-time dashboard: it also introduces a historical data perspective.

## Why This Architecture Was Chosen

The selected architecture was aligned with the operational and engineering constraints of the project.

It was designed to provide:

* Long-range communication at relatively low cost.
* Compatibility with industrial equipment.
* Centralized supervision of multiple remote stations.
* Short refresh times for monitoring.
* Scalability for future station additions.
* Practical integration with monitoring tools.

The resulting design balances industrial reliability at station level with lightweight IoT communication and visualization technologies.

## Main Architectural Decisions

### Industrial acquisition with PLCs

The project uses PLC-based acquisition rather than fully consumer-grade IoT hardware. This improves robustness and makes the solution more credible for real infrastructure.

### RS485 / Modbus as the local interface

Using Modbus RTU over RS485 provides a simple and well-known industrial communication layer between acquisition and wireless transmission.

### LoRaWAN for remote connectivity

LoRaWAN was selected because the stations are geographically distributed and the project required long-range communication with low operating cost.

### Independent network server model

The system was designed around TTN as an external network server, which is more scalable than keeping the entire network logic inside a single gateway.

### Node-RED as the central monitoring layer

Node-RED offered a practical way to decode messages, build dashboards, and create a bridge toward supervisory environments.

### ThingSpeak for historical data

ThingSpeak added persistence, historical visualization, and a basis for alerting and further analysis.

## Scalability Considerations

The architecture was conceived as a reusable pattern rather than a one-off integration.

Adding a new station would mainly require:

* Deploying the station-side PLC and communication node.
* Mapping the new signals.
* Registering the new device in TTN.
* Extending the Node-RED / ThingSpeak logic.

This means the architecture can grow without changing its core communication model.

## Limitations of the Architecture

Although the architecture is practical and scalable, it also has some limitations:

* It is focused on remote monitoring more than on full remote control.
* Node-RED is highly practical but not the same as a full industrial SCADA platform.
* MQTT and Node-RED security hardening would need special attention in a production-grade deployment.
* The public portfolio version cannot include the original confidential infrastructure details.

## Architecture Perspective in This Repository

This repository documents the system from a portfolio and engineering communication perspective. It does not attempt to publish the original production setup in full detail. Instead, it explains the logic of the architecture, the responsibilities of each component, and the main decisions that made the solution viable for remote water station monitoring.


# Remote Water Pumping Monitoring

## Overview

This section contains the full English documentation for a technical portfolio project based on a real industrial internship case involving the remote monitoring of water pumping stations.

<p align="center">
  <img src="/assets/images/readme/system-architecture.png" alt="System architecture" width="400"><br>
  <em>High-level architecture of the remote water pumping monitoring system.</em>
</p>

The project focuses on how geographically distributed pumping facilities can be monitored remotely by combining industrial automation, long-range wireless communications, IoT messaging, and cloud-based visualization tools.

Rather than reproducing the original academic report as-is, this documentation reorganizes the work into a portfolio-oriented technical narrative focused on architecture, implementation decisions, system integration, validation, and engineering conclusions.

---

## Project Context

Water pumping infrastructure is often distributed across remote locations, which makes continuous in-person supervision inefficient. In this project, the goal was to explore a practical monitoring architecture capable of acquiring process data from the industrial control layer and making it available remotely through an IoT-based telemetry pipeline.

The solution was conceived around the integration of PLC data acquisition, RS-485/Modbus communications, LoRaWAN transmission, MQTT-based message exchange, Node-RED processing, and dashboard visualization.

---

## What You Will Find in This Documentation

This English documentation is organized as a technical portfolio package covering:

* The industrial problem and project scope
* The proposed system architecture
* The selected hardware and communication technologies
* The telemetry and data flow logic
* The implementation approach
* The validation process and observed results
* Deployment considerations
* Lessons learned and future improvements

---

## Documentation Contents

### 1. Project Overview

File: [`project-overview.md`](project-overview.md)

Describes the industrial context, the problem being addressed, the project objectives, and the intended scope of the monitoring solution.

### 2. Architecture

File: [`architecture.md`](architecture.md)

Explains the high-level architecture of the system, the role of each subsystem, and the end-to-end communication chain from the field layer to the visualization layer.

### 3. Hardware and Communications

File: [`hardware-and-communications.md`](hardware-and-communications.md)

Summarizes the main hardware elements, communication interfaces, and protocol choices used in the project.

### 4. Data Flow

File: [`data-flow.md`](data-flow.md)

Describes how process variables are acquired, encoded, transmitted, decoded, processed, and finally published to monitoring dashboards.

### 5. Validation and Results

File: [`validation-and-results.md`](validation-and-results.md)

Presents how the solution was tested, what results were observed, and what technical conclusions can be extracted from the validation stage.

### 6. Deployment Notes

File: [`deployment-notes.md`](deployment-notes.md)

Collects operational notes, configuration guidance, integration considerations, and useful deployment remarks.

### 7. Lessons Learned

File: [`lessons-learned.md`](lessons-learned.md)

Highlights engineering reflections, design trade-offs, system limitations, and possible next steps for improvement.

---

## High-Level Technical Summary

The project can be understood as a telemetry pipeline with the following logic:

1. Operational variables are acquired from the control system.
2. Data is extracted through industrial communication interfaces.
3. A long-range wireless communication layer enables remote transmission.
4. Messages are received and decoded in the IoT network layer.
5. Node-RED processes and routes the incoming data.
6. Process variables are published to a cloud visualization platform.
7. The final result is remote visibility into pumping station status and selected operating conditions.

---

## Main Technologies Involved

* Siemens PLC environment
* RS-485 / Modbus communication
* Dragino communication hardware
* LoRa / LoRaWAN
* The Things Network (TTN)
* MQTT
* Node-RED
* ThingSpeak
* Orange Pi support environment

## Recommended Reading Order

For a first technical review, the suggested reading sequence is:

1. `project-overview.md`
2. `architecture.md`
3. `data-flow.md`
4. `validation-and-results.md`
5. `lessons-learned.md`

This order helps the reader move from problem framing to architecture, then to execution and technical conclusions.

---

## Relationship with the Original Internship Work

This repository is a portfolio adaptation of a real engineering internship project. The original work has been restructured to improve clarity and to emphasize technical communication, system design rationale, and implementation value.

Because of that, some content may appear condensed, reorganized, or simplified when compared to the original academic version.

---

## Navigation

* Back to the [repository root README](../../README.md)
* Switch to the [Spanish documentation](../es/README.md)

---

## Current Documentation Status

The documentation is being progressively refined as part of a portfolio-oriented GitHub repository.

Current work includes:

* structuring bilingual documentation
* preparing architecture-focused writeups
* organizing visuals and figures
* converting academic material into concise technical documentation

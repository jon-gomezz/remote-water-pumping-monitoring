# Remote Water Pumping Monitoring

A technical portfolio project based on an industrial internship case focused on the remote monitoring of water pumping stations.

This repository presents the design and implementation of a monitoring solution for geographically distributed pumping facilities, combining industrial control systems, long-range wireless communications, IoT platforms, and cloud-based data visualization.

The project is documented in two equivalent versions:

* [English version](docs/en/README.md)
* [Versión en castellano](docs/es/README.md)

---

## Project Overview

Water pumping stations are often located in remote areas where continuous on-site supervision is inefficient and costly. This project explores how operational variables can be acquired from existing control hardware and transmitted to a remote monitoring environment using an industrial IoT architecture.

The solution integrates PLC-based data acquisition, LoRaWAN communications, MQTT messaging, Node-RED processing, and cloud dashboards to provide near real-time visibility into station status and process variables.

---

## Main Goals

* Enable remote monitoring of distributed water pumping stations
* Reduce the need for manual on-site checks
* Build a reliable end-to-end data pipeline from field devices to dashboards
* Validate the feasibility of low-power, long-range communications in this use case
* Present the work as a structured technical portfolio project rather than as an academic thesis copy

---

## System Architecture

> Suggested main image location: `assets/images/overview/system-architecture.png`

```md
![System architecture](assets/images/overview/system-architecture.png)
*Figure 1. High-level architecture of the remote monitoring solution.*
```

At a high level, the monitored signals are acquired from the industrial control layer, transmitted through a LoRaWAN communication chain, decoded and processed through MQTT and Node-RED, and finally visualized in a cloud dashboard environment.

---

## Technical Scope

This repository focuses on:

* industrial context and problem definition
* system architecture and communication flow
* hardware and protocol selection
* implementation logic and deployment decisions
* validation approach and observed results
* engineering lessons learned

It intentionally avoids reproducing the original internship report in full academic format. Instead, the content has been reorganized to highlight practical engineering decisions, architecture design, implementation details, and technical outcomes.

---

## Technology Stack

* **Industrial control:** Siemens PLC environment
* **Field communications:** RS-485 / Modbus
* **Wireless transmission:** LoRa / LoRaWAN
* **Network server:** The Things Network (TTN)
* **Messaging:** MQTT
* **Processing and orchestration:** Node-RED
* **Visualization:** ThingSpeak
* **Edge / support computing:** Orange Pi environment

---

## Repository Structure

```text
remote-water-pumping-monitoring/
├── README.md
├── LICENSE
├── .gitignore
├── assets/
│   ├── images/
│   │   ├── overview/
│   │   ├── architecture/
│   │   ├── hardware/
│   │   ├── dashboards/
│   │   └── installation/
│   └── figures-index.md
├── docs/
│   ├── en/
│   │   ├── README.md
│   │   ├── project-overview.md
│   │   ├── architecture.md
│   │   ├── hardware-and-communications.md
│   │   ├── data-flow.md
│   │   ├── implementation.md
│   │   ├── validation-and-results.md
│   │   ├── deployment-notes.md
│   │   └── lessons-learned.md
│   └── es/
│       ├── README.md
│       ├── project-overview.md
│       ├── architecture.md
│       ├── hardware-and-communications.md
│       ├── data-flow.md
│       ├── implementation.md
│       ├── validation-and-results.md
│       ├── deployment-notes.md
│       └── lessons-learned.md
├── examples/
│   ├── ttn-payload-decoder/
│   │   └── sample-decoder.js
│   ├── node-red/
│   │   └── sample-flow.json
│   └── plc-data-mapping/
│       └── signal-map-example.md
└── references/
    └── source-context.md
```

---

## Documentation Map

### English

* [Project overview](docs/en/project-overview.md)
* [Architecture](docs/en/architecture.md)
* [Hardware and communications](docs/en/hardware-and-communications.md)
* [Data flow](docs/en/data-flow.md)
* [Implementation](docs/en/implementation.md)
* [Validation and results](docs/en/validation-and-results.md)
* [Deployment notes](docs/en/deployment-notes.md)
* [Lessons learned](docs/en/lessons-learned.md)

### Castellano

* [Resumen del proyecto](docs/es/project-overview.md)
* [Arquitectura](docs/es/architecture.md)
* [Hardware y comunicaciones](docs/es/hardware-and-communications.md)
* [Flujo de datos](docs/es/data-flow.md)
* [Implementación](docs/es/implementation.md)
* [Validación y resultados](docs/es/validation-and-results.md)
* [Notas de despliegue](docs/es/deployment-notes.md)
* [Lecciones aprendidas](docs/es/lessons-learned.md)

---

## Why This Project Matters

This project demonstrates the ability to:

* translate a real industrial problem into a deployable monitoring architecture
* connect OT and IoT layers in a coherent system
* work with industrial protocols and telemetry pipelines
* document technical decisions clearly for engineering audiences
* convert internship experience into a professional portfolio asset

---

## Project Status

This repository is being progressively documented and refined as a technical portfolio project.

Current focus:

* repository structure definition
* bilingual technical documentation
* architecture visuals and supporting diagrams
* selected implementation examples

---

## Notes

This repository is a portfolio adaptation of a real engineering internship project. Some implementation details may be simplified, anonymized, or reorganized in order to improve clarity, readability, and presentation quality.

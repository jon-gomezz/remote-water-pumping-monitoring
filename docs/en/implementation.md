# Implementation

## Purpose of This Document

This document explains how the solution was implemented in practice.

While the architecture and data-flow documents describe how the system is structured conceptually, this section focuses on the engineering work required to move from design to a functioning pilot. It highlights the implementation sequence, the main configuration tasks, the evolution of the solution during development, and the practical decisions that turned the architecture into an operational monitoring system.

This is intentionally written as a **technical portfolio implementation narrative**, not as a full installation manual.

---

## Implementation Strategy

The implementation was not carried out as a single all-at-once deployment. Instead, it followed a staged engineering process that reduced risk and allowed each part of the system to be validated progressively.

A good way to understand the work is through four implementation stages:

1. **Design and integration planning**
2. **Bench tests and early prototypes**
3. **Reception and decoding workflow development**
4. **Final reception changes, dashboards, and pilot deployment**

This staged approach was one of the strongest practical aspects of the project, because it avoided jumping directly into field installation before the communication chain had been understood and tested.

---

## Recommended Main Figure

Place this image near the top of the page, after the implementation strategy section.

```md
<p align="center">
  <img src="../../assets/images/implementation/implementation-phases.png" alt="Implementation phases" width="950"/>
</p>
<p align="center"><em>Figure 1. Implementation sequence from early design work to pilot deployment and dashboard refinement.</em></p>
```

---

## Stage 1. Design and Integration Planning

The first implementation stage focused on building a technically coherent basis for the solution.

At this point, the objective was not yet full operation, but rather answering the key engineering questions:

* which wireless architecture best fits the project requirements
* how the PLC should communicate with the telemetry device
* how the payload should be structured
* how the central monitoring point should receive and present the information

The main activities in this stage were:

* defining an initial project diagram
* studying LoRa and LoRaWAN network options
* reviewing the Siemens S7-1200 PLC environment
* analyzing the Dragino RS485-LN as the RS-485 to LoRaWAN bridge
* studying Modbus RTU as the local communication protocol
* deciding on a LoRaWAN architecture with an independent network server

This stage was critical because it established the design logic that later implementation work depended on.

### Implementation output of Stage 1

By the end of this stage, the project had:

* a clear end-to-end architecture
* a justified communication stack
* a defined station-side hardware combination
* an initial plan for payload handling and centralized reception

---

## Stage 2. Bench Tests and Early Prototypes

Once the main architectural decisions were clear, the next step was to verify that the communication chain actually worked under controlled conditions.

This stage was built around prototyping and isolation testing.

The practical objective was to avoid debugging the entire system at once. Instead, each critical relationship was tested progressively.

### Main tasks in this stage

* registering and testing the Dragino RS485-LN in TTN
* performing first connection tests between the telemetry device and the LoRaWAN platform
* injecting signals with a signal generator to isolate communication behavior before integrating the PLC
* designing the PLC-side emission logic
* building the first complete Dragino + PLC S7-1200 prototype

This sequence is especially important because it shows disciplined engineering practice:

* first validate the LoRaWAN path
* then validate the converter behavior
* then validate PLC integration
* only then move toward a broader central monitoring workflow

### Why the signal-generator step mattered

Using a signal generator before final PLC integration was a very good implementation choice.

It made it possible to:

* test the telemetry chain in isolation
* identify whether issues belonged to the wireless link or to the PLC side
* reduce ambiguity during debugging
* avoid mixing field logic problems with transmission problems

That kind of staged isolation is exactly the kind of implementation thinking worth highlighting in a technical portfolio.

---

## Stage 3. Reception and Decoding Development

After the station-side prototype was functioning, the project moved to the reception side.

This stage focused on the problem of how to receive TTN data and transform it into something usable for monitoring and control workflows.

### Main tasks in this stage

* selecting MQTT as the reception protocol
* creating the first decoding logic for received payloads
* exploring how received data could be transformed into a format readable by PLC-oriented environments
* implementing deserialization logic for the received payload
* studying bidirectional communication possibilities through MQTT

This stage is important to describe honestly.

At this point, the solution still explored reception paths that were more closely tied to PLC-centered logic. That means the project was not yet in its final monitoring form. Instead, it was still discovering the most practical way to integrate received data into the central environment.

### Why MQTT was selected

MQTT was chosen because it provided a clean and lightweight way to move data from TTN toward the reception layer.

From an implementation perspective, MQTT offered several practical benefits:

* low overhead
* easy topic-based routing
* compatibility with IoT workflows
* straightforward integration with Node-RED
* good fit for multi-device telemetry pipelines

---

## Stage 4. Reception Changes, Dashboards, and Pilot Deployment

This stage contains one of the most important implementation stories in the entire project.

The original reception approach changed due to real deployment constraints in Araka.

Because of complications with Ethernet installation in the central SCADA environment, the project shifted toward **Node-RED as the main reception, processing, and visualization layer**.

This is not a secondary detail. It is a real engineering adaptation to infrastructure constraints, and it greatly improves the credibility of the project.

### Main tasks in this stage

* configuring Node-RED to receive and process incoming data
* building the Node-RED flows needed to decode and route payload values
* integrating MQTT reception from TTN into Node-RED
* deploying the required hardware and software at the pumping-station and central sides
* creating dashboards for real-time supervision
* integrating ThingSpeak for storage and analysis
* improving dashboards and adding alarm-oriented capabilities

This stage is where the project became a usable monitoring solution rather than a communication prototype.

---

## Station-Side Implementation

A large part of the implementation effort happened at station level.

### 1. PLC installation and signal integration

The Siemens S7-1214C PLC was used as the station-side acquisition unit.

At implementation level, this involved:

* wiring digital and analog process signals to the PLC
* adding the required communication and expansion modules where needed
* mapping the available variables according to each station
* preparing the PLC program to expose structured values for transmission

For Mandojana in particular, the monitored implementation included:

* digital states for pump operation, water-shortage alarm, and valve status
* analog values such as well level, tank level, flow, and chlorine-related variables

### 2. CM 1241 serial communication configuration

The CM 1241 module was configured in TIA Portal to provide the required RS-422/485 serial communication capabilities.

This required defining parameters such as:

* transmission speed
* parity
* data bits
* communication behavior consistent with Modbus RTU

### 3. PLC memory and data-block structuring

A very important implementation choice was to structure station data inside the PLC using a uniform format.

Instead of treating each station as a completely independent case, the implementation organized data into a common 4-word structure:

* two words for digital information
* two words for analog information

This made later configuration easier in the Dragino, TTN decoder, and Node-RED flows.

### 4. Modbus slave role

At station level, the PLC was implemented as the **Modbus slave**, while the Dragino RS485-LN operated as the **Modbus master**.

This meant the telemetry converter initiated the read operations and the PLC responded with the requested register data.

---

## Suggested PLC Implementation Figure

Place this image after the station-side implementation section.

```md
<p align="center">
  <img src="../../assets/images/implementation/plc-mandojana-diagram.png" alt="Mandojana PLC diagram" width="950"/>
</p>
<p align="center"><em>Figure 2. Example of the PLC-side implementation and signal integration used in the Mandojana station.</em></p>
```

---

## Dragino RS485-LN Implementation

The Dragino RS485-LN was one of the most hands-on parts of the implementation.

It had to be configured not just as a radio device, but as an operational bridge between Modbus RTU and LoRaWAN.

### Practical implementation tasks

* physically connecting and powering the device
* connecting the antenna and local serial interface
* configuring the module through AT commands during setup
* defining the Modbus requests to be sent to the PLC
* selecting the returned bytes that would form the payload
* validating correct uplink behavior toward TTN
* preparing downlink capability when needed for remote command behavior

### Why this step was important

This stage is where the project moved from “PLC data exists” to “PLC data becomes remote telemetry.”

The Dragino configuration had to align with:

* PLC memory layout
* Modbus register addressing
* expected byte ordering
* payload size constraints
* TTN decoder expectations

Because of that, the Dragino was not a plug-and-play black box. It required deliberate configuration decisions.

### Configuration path used

The module could be configured locally using **AT commands**, typically through a PC connection using a USB/TTL interface.

This is worth mentioning in the portfolio because it shows real device-level configuration work rather than only software-side orchestration.

---

## TTN and Network-Side Implementation

After station-side data could be transmitted, the LoRaWAN network layer had to be configured so that incoming packets were usable.

### Main implementation tasks in TTN

* registering the Dragino end device
* configuring the project application context
* validating uplink reception through the TTIG gateway
* defining the uplink message path
* implementing the payload decoder
* preparing the MQTT delivery path toward the application side

### Why this part matters

TTN was not only used as a passive network endpoint. It became a configuration layer where the project defined how raw radio messages would become meaningful application data.

The implementation benefit of decoding payloads in TTN was that downstream tools received a cleaner structure and required less low-level byte interpretation.

---

## Suggested TTN Figure

Place this image after the TTN implementation section.

```md
<p align="center">
  <img src="../../assets/images/implementation/ttn-device-and-decoder.png" alt="TTN device and decoder" width="920"/>
</p>
<p align="center"><em>Figure 3. TTN-side implementation including end-device registration and payload decoding logic.</em></p>
```

---

## Node-RED Implementation

Node-RED became the core of the central-side implementation.

This happened especially after the project adapted to the practical limitation of not being able to rely on a direct Ethernet-based PLC reception path in Araka.

### Main implementation tasks in Node-RED

* subscribing to TTN MQTT topics
* receiving uplink messages from the relevant devices
* extracting the decoded payload object
* routing each value to the correct dashboard widget
* creating station-specific visualization layouts
* keeping the monitoring environment running continuously on the Orange Pi 3 LTS

### Dashboard implementation

The dashboards were implemented to make station conditions interpretable at a glance.

Depending on the station, the Node-RED interface included elements such as:

* status LEDs
* gauges
* trend charts
* text indicators
* dedicated station panels

For Mandojana, this included the visualization of pump status, water alarm state, valve state, flow, tank level, well level, and chlorine-related values.

### Why Node-RED is central in the implementation story

A lot of technical portfolio projects describe dashboards as a cosmetic final step. That is not the case here.

In this project, Node-RED became a true implementation pivot because it solved several problems at once:

* central reception of MQTT data
* flexible payload processing
* SCADA-like visualization
* rapid dashboard construction
* operational usability at the control point

---

## Suggested Node-RED Figure

Place this image after the Node-RED implementation section.

```md
<p align="center">
  <img src="../../assets/images/implementation/node-red-dashboard-programming.png" alt="Node-RED dashboard programming" width="920"/>
</p>
<p align="center"><em>Figure 4. Node-RED flow used to process incoming MQTT telemetry and update dashboard elements.</em></p>
```

---

## ThingSpeak Implementation

Once Node-RED was receiving and routing the data, the implementation was extended with ThingSpeak to support storage and analysis.

### Main implementation tasks

* creating the required ThingSpeak channels
* publishing processed values from Node-RED to the correct channels
* organizing field mappings for station variables
* enabling trend visualization for historical monitoring
* configuring alarms and events for abnormal conditions

### Why ThingSpeak was added after Node-RED

This implementation order makes sense:

* first make data visible in the operational dashboard
* then extend the system with storage, trending, and alert-oriented capabilities

That sequencing reflects a practical engineering priority: immediate supervision first, deeper historical analysis second.

---

## Suggested ThingSpeak Figure

Place this image after the ThingSpeak section.

```md
<p align="center">
  <img src="../../assets/images/implementation/thingspeak-integration.png" alt="ThingSpeak integration" width="920"/>
</p>
<p align="center"><em>Figure 5. ThingSpeak integration used for telemetry storage, trend visualization, and alarm-oriented analysis.</em></p>
```

---

## Pilot Deployment Context

The project was conceived for several stations, but the practical implementation was validated as a pilot.

The pilot focused on:

* **Mandojana** as the monitored pumping-station implementation case
* **Araka** as the central reception and supervision environment

This is an important implementation point to communicate clearly.

The portfolio version should not imply that every planned station was fully deployed in the same maturity level. Instead, it should emphasize that the pilot proved the technical viability of the implementation pattern and created a repeatable basis for future rollout.

---

## Physical Installation and Commissioning

A technical portfolio should also mention the practical work needed beyond software configuration.

This included:

* powering the PLC and expansion modules with 24 V infrastructure
* connecting communication modules physically at the station
* mounting and wiring the relevant interfaces
* installing the Dragino with antenna and local configuration access
* checking connectivity in each deployed environment
* starting up the station-side and central-side environments
* verifying correct transmission from field to dashboard

This matters because it shows that the project involved real commissioning work, not just architecture design.

---

## Implementation Challenges and Adaptations

One of the most valuable aspects of this project is that the implementation changed in response to real constraints.

### 1. Ethernet limitations in Araka

A direct internet-connected PLC-based reception path was not practical in the Araka SCADA environment due to infrastructure limitations.

**Adaptation:** the project shifted reception toward Node-RED, which became the central-side processor and dashboard layer.

### 2. Integration across heterogeneous layers

The implementation had to connect:

* industrial control hardware
* serial communications
* wireless LPWAN networking
* cloud services
* message-oriented software tools

**Adaptation:** the project standardized PLC data structures and aligned Dragino, TTN, and Node-RED logic around that structure.

### 3. Payload interpretation complexity

Low-level byte payloads are not convenient for operator-facing applications.

**Adaptation:** payload decoding was moved upstream so that Node-RED could work with more readable values.

These kinds of implementation adjustments are worth emphasizing because they show real engineering iteration rather than a linear textbook deployment.

---

## Implementation Strengths

The strongest aspects of the implementation are:

* staged validation rather than direct full deployment
* good use of prototype isolation before PLC integration
* uniform PLC-side data structuring
* practical adaptation to infrastructure constraints in the central environment
* fast creation of usable dashboards through Node-RED
* successful pilot linking field hardware, LoRaWAN, MQTT, dashboarding, and cloud analysis

---

## Implementation Limitations

A credible portfolio document should also note the limits of the implemented solution.

Relevant limitations include:

* the pilot was validated on a reduced subset of the planned stations
* the initial use of TTN means the pilot depends on an external network service
* Node-RED is excellent for flexible implementation, but not equivalent to a hardened enterprise SCADA platform
* the solution is optimized for supervisory monitoring, not low-latency control
* security hardening and private network-server migration are logical next implementation steps

---

## Suggested Final Visual

Place this image near the end of the document, before the conclusion.

```md
<p align="center">
  <img src="../../assets/images/implementation/pilot-deployment-view.png" alt="Pilot deployment view" width="950"/>
</p>
<p align="center"><em>Figure 6. Pilot implementation view connecting Mandojana station telemetry with centralized monitoring in Araka.</em></p>
```

---

## Conclusion

The implementation of this project was not just a matter of configuring devices independently. It required coordinating multiple engineering layers:

* station-side signal acquisition
* PLC programming and data structuring
* Modbus RTU integration
* Dragino payload configuration
* TTN device registration and decoding
* MQTT-based reception
* Node-RED dashboard construction
* ThingSpeak storage and alert-oriented analysis

What makes the implementation especially valuable as a portfolio project is that it reflects real engineering practice:

* prototype first
* isolate failures early
* adapt to infrastructure constraints
* standardize data structures
* build an operational pilot before scaling further

That makes this project much stronger than a purely conceptual architecture exercise.

---

## What Comes Next

After implementation, the next logical step is to document how the pilot was tested and what results were observed.

Continue with: [`validation-and-results.md`](validation-and-results.md)

---

## Navigation

* Back to the [English documentation index](README.md)
* Back to [Data Flow](data-flow.md)
* Switch to the [Spanish version](../es/implementation.md)

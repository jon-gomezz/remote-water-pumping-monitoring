# Deployment Notes

## Purpose of This Document

This document collects the practical considerations that matter when deploying, reproducing, or extending the monitoring solution.

Unlike the implementation document, which explains how the project was built step by step, this section focuses on what an engineer should keep in mind when moving the architecture into operation: hardware preparation, communication setup, configuration consistency, commissioning checks, and operational constraints.

This is intentionally written as a **deployment-oriented technical note**, not as a full installation manual.

---

## What “Deployment” Means in This Project

In this project, deployment is not limited to installing software on one machine.

It involves coordinating several layers at the same time:

* Field-side industrial hardware
* Serial communication configuration
* LoRaWAN device registration and connectivity
* Central MQTT reception
* Node-RED dashboard operation
* ThingSpeak publication and monitoring support

Because of that, deployment should be understood as the process of making the full telemetry chain operational and stable from the station to the central supervision point.

---

## Recommended Main Figure

Place this image near the top of the page, after the introductory section.

```md
<p align="center">
  <img src="../../assets/images/deployment/deployment-overview.png" alt="Deployment overview" width="950"/>
</p>
<p align="center"><em>Figure 1. Practical deployment view of the monitoring solution from station hardware to central supervision.</em></p>
```

---

## Deployment Scope in the Pilot

The broader project vision considered several pumping stations, but the practical deployment validation focused on a pilot configuration.

The deployment context can be summarized as:

* **Station side:** Mandojana
* **Central supervision side:** Araka

This is important when presenting the repository.

The deployment notes should describe a **validated pilot pattern** that can be repeated and extended, not claim that the full multi-station rollout was completed everywhere.

---

## Pre-Deployment Checklist

Before installation or replication, the following questions should be reviewed.

### Process and station readiness

* Which variables need to be monitored at the station?
* Are they digital, analog, or both?
* Is the local signal count compatible with the selected PLC and expansion setup?
* Is the intended polling/update rhythm appropriate for the process?

### Hardware readiness

* Is the PLC installed and powered correctly?
* Is the CM 1241 module available and configured?
* Is the Dragino RS485-LN installed with the correct antenna and wiring?
* Is the gateway position suitable for reliable LoRaWAN reception?
* Is the Orange Pi or equivalent central runtime available and stable?

### Communication readiness

* Are Modbus RTU parameters aligned between PLC and Dragino?
* Is the end device correctly registered in TTN?
* Is MQTT connectivity available for the central monitoring layer?
* Are the payload format and decoder logic synchronized?

### Monitoring readiness

* Are the Node-RED flows prepared for the incoming device topics?
* Are dashboard widgets mapped to the right variables?
* Are ThingSpeak channels and fields defined consistently?

This checklist is useful because most deployment failures in a system like this do not come from a single big error, but from small mismatches across layers.

---

## Station-Side Deployment Notes

### PLC installation and wiring

The PLC should be treated as the structured acquisition core of the station.

Deployment considerations include:

* Correct connection of digital and analog signals
* Correct supply voltage and industrial power conditions
* Proper organization of field wiring
* Clear mapping between physical signals and PLC variables
* Enough I/O capacity for the selected station

A deployment lesson from this type of system is that signal clarity matters as much as communication setup. If the input mapping is inconsistent, the telemetry chain may work technically while still delivering misleading process information.

### CM 1241 installation

The CM 1241 must be installed and parameterized consistently with the intended serial communication mode.

Important deployment checks:

* Baud rate
* Parity
* Data bits
* Stop bits
* Station behavior as Modbus slave
* Consistency with Dragino-side request definitions

Even small mismatches in serial parameters can prevent successful polling, so this stage should always be verified before looking for problems in LoRaWAN or MQTT.

### PLC data structure consistency

One of the most important deployment requirements is to keep the PLC memory structure aligned with the rest of the system.

If the station uses the shared 4-word model described in the documentation, that structure must remain consistent across:

* PLC variables
* Modbus addressing
* Dragino payload extraction
* TTN payload decoder
* Node-RED dashboard mapping

This is one of the key practical rules of the whole project:

**if one layer changes its data structure, the dependent layers must be updated as well.**

---

## Suggested Station Deployment Figure

Place this image after the station-side section.

```md
<p align="center">
  <img src="../../assets/images/deployment/station-installation-checkpoints.png" alt="Station installation checkpoints" width="930"/>
</p>
<p align="center"><em>Figure 2. Main station-side checkpoints for PLC, serial module, Dragino, and field signal integration.</em></p>
```

---

## Dragino Deployment Notes

The Dragino RS485-LN is one of the most sensitive deployment elements because it sits exactly between industrial communications and LoRaWAN telemetry.

### Practical installation points

* Ensure stable power supply
* Verify RS-485 wiring polarity and terminal correctness
* Connect the antenna properly
* Confirm that the physical location is suitable for radio transmission
* Keep local access available during commissioning when possible

### Configuration notes

During deployment, the Dragino configuration should be reviewed carefully:

* Modbus request definition
* Polling interval
* Selected response bytes
* Payload size
* LoRaWAN join and network parameters
* Remote downlink behavior if used

### Deployment risk to avoid

A very common risk in systems like this is assuming that a correct Modbus response automatically means a correct LoRaWAN payload.

That is not always true.

The deployment check must validate both:

1. Correct register reading from the PLC
2. Correct selection and transmission of the intended bytes

---

## Gateway and LoRaWAN Deployment Notes

### Gateway placement

The gateway should be installed in a location that supports reliable radio reception from the deployed station node.

Deployment considerations include:

* Distance and line-of-sight conditions
* Physical obstacles
* Indoor vs outdoor attenuation
* Stability of internet connectivity toward TTN

Even though the project uses a pilot-friendly LoRaWAN architecture, radio coverage still has to be treated as an operational deployment concern, not as an assumption.

### TTN registration and device management

For deployment, each end device should be clearly identified and documented.

Useful notes include:

* Device name
* Station name
* Application context
* Decoder version used
* Topic structure expected downstream

This matters especially when the project grows from one pilot station to several monitored assets.

### Decoder version control

The TTN decoder logic should be treated as part of the deployed system, not as an informal script.

A good deployment habit is to track:

* Which decoder version is active
* Which byte structure it expects
* Which station payload it corresponds to

Without this discipline, multi-station maintenance becomes much harder.

---

## Suggested LoRaWAN Deployment Figure

Place this image after the gateway and TTN section.

```md
<p align="center">
  <img src="../../assets/images/deployment/lorawan-deployment-path.png" alt="LoRaWAN deployment path" width="930"/>
</p>
<p align="center"><em>Figure 3. Deployment path from station-side uplink transmission to TTN reception and decoder execution.</em></p>
```

---

## Central-Side Deployment Notes

### Orange Pi runtime stability

The Orange Pi 3 LTS plays a practical but important deployment role because it hosts the Node-RED environment.

Important checks:

* Continuous power availability
* Stable operating environment
* Network access for MQTT and external services
* Automatic service startup where possible
* Sufficient monitoring of runtime state

The main deployment idea here is simple: the dashboards are only useful if the central runtime is continuously available.

### Node-RED deployment

Node-RED should be treated as a production-facing operational component in the pilot context.

Useful deployment notes include:

* Verify subscription to the correct TTN MQTT topics
* Validate message parsing against the active decoder output
* Check that every variable reaches the intended widget
* Organize flows clearly by station or functional purpose
* Keep a backup/export of the flow definitions

This last point is especially important in practice. Even in a portfolio pilot, exported Node-RED flows are part of the deployable system and should be preserved as configuration assets.

### Dashboard readability

A deployment is not successful just because data appears somewhere on screen.

The dashboards should also be:

* Easy to interpret quickly
* Consistent across stations
* Explicit about units and variable meaning
* Visually clear for alarms and key states

This matters because the operational value of the system depends on usability as much as connectivity.

---

## ThingSpeak Deployment Notes

ThingSpeak extends the system from live supervision into storage and historical analysis.

### Practical deployment checks

* Confirm that the correct channel exists for the intended station or variable group
* Map fields consistently to the transmitted values
* Verify successful publication from Node-RED
* Validate chart readability and historical continuity
* Configure alerts only after confirming stable incoming data

### Field-mapping discipline

A simple but important deployment rule is to keep the same field meaning across time.

For example, if one field is assigned to tank level, that meaning should not later be changed casually to a different variable without versioning or documentation.

This keeps historical analysis interpretable.

---

## Suggested Central Monitoring Figure

Place this image after the central-side deployment sections.

```md
<p align="center">
  <img src="../../assets/images/deployment/central-monitoring-stack.png" alt="Central monitoring stack" width="930"/>
</p>
<p align="center"><em>Figure 4. Central-side deployment stack including Orange Pi, MQTT reception, Node-RED dashboards, and ThingSpeak publication.</em></p>
```

---

## Commissioning Sequence

A practical commissioning sequence for this project would be:

1. Verify PLC power, wiring, and variable mapping
2. Verify CM 1241 serial settings
3. Verify Dragino power, antenna, and RS-485 connectivity
4. Test Modbus polling locally
5. Verify TTN device registration and uplink arrival
6. Verify payload decoding correctness
7. Verify MQTT delivery to Node-RED
8. Verify dashboard updates in Node-RED
9. Verify publication to ThingSpeak
10. Verify end-to-end monitoring continuity over time

This order matters because it moves from the station outward, reducing troubleshooting ambiguity.

---

## Troubleshooting Notes

### If no useful data appears in Node-RED

Possible causes:

* No TTN uplink is arriving
* MQTT credentials or topic subscription are incorrect
* The decoder output format changed
* The flow is extracting the wrong property path

### If TTN receives uplinks but values are wrong

Possible causes:

* Incorrect PLC register mapping
* Wrong Modbus address or byte order
* Wrong Dragino byte selection
* Outdated or mismatched decoder logic

### If Modbus polling fails

Possible causes:

* RS-485 wiring issue
* Baud rate mismatch
* Parity mismatch
* PLC not behaving as expected as slave
* Wrong request definition in the Dragino

### If dashboards update but historical storage is wrong

Possible causes:

* Incorrect ThingSpeak field mapping
* Partial publication logic in Node-RED
* Field reuse without documentation

This troubleshooting framing is useful because it keeps the engineer thinking in layers instead of guessing randomly.

---

## Operational Constraints to Keep in Mind

The deployed solution should be understood within its intended operating scope.

### Supervisory monitoring, not fast control

The deployment supports remote visibility and supervisory response, with refresh behavior appropriate for operational monitoring.

It should not be treated as a platform for tight real-time control loops.

### Pilot/public network context

The use of TTN is practical and effective for pilot validation, but a future production-oriented version may require a more controlled network-server strategy depending on operational and cybersecurity requirements.

### Multi-station growth requires discipline

Scaling to more stations is feasible, but only if naming, decoder management, dashboard organization, and variable mapping remain structured.

---

## Documentation and Configuration Artifacts Worth Preserving

A solid deployment package should preserve more than just narrative documents.

Useful artifacts include:

* PLC variable map
* Modbus register map
* Dragino configuration notes
* TTN device and decoder definitions
* Node-RED flow exports
* ThingSpeak channel-field mapping
* Station naming conventions
* Commissioning checklist and test notes

Even if the repository only includes simplified examples, keeping this mindset improves the professional quality of the project.

---

## Suggested Final Figure

Place this image near the end of the document, before the conclusion.

```md
<p align="center">
  <img src="../../assets/images/deployment/commissioning-sequence.png" alt="Commissioning sequence" width="950"/>
</p>
<p align="center"><em>Figure 5. Suggested commissioning and deployment validation sequence for reproducing the monitoring solution.</em></p>
```

---

## Deployment Best Practices Derived from the Project

A few practical rules summarize the most important deployment lessons:

* Validate each layer before moving to the next one
* Keep data structures consistent across PLC, Dragino, TTN, and Node-RED
* Treat decoder logic as a versioned deployment artifact
* Organize stations with clear naming and mapping conventions
* Design dashboards for operational clarity, not only technical completeness
* Document configuration decisions while deploying, not after problems appear

These principles are small, but they make a major difference in distributed telemetry systems.

---

## Conclusion

The deployment of this monitoring solution depends on much more than installing a few devices. It requires alignment across industrial hardware, serial communication, LoRaWAN connectivity, message routing, dashboards, and storage services.

What the pilot demonstrates is that this alignment is achievable in practice when the system is deployed in a disciplined way:

* Station signals are mapped clearly
* The PLC exposes structured data
* The Dragino sends the correct payload
* TTN decodes it consistently
* Node-RED presents it clearly
* ThingSpeak stores it meaningfully

That is what turns the architecture into an operational remote-monitoring system rather than a conceptual design.

---

## What Comes Next

After deployment notes, the last document should focus on engineering reflections, trade-offs, and future improvements.

Continue with: [`lessons-learned.md`](lessons-learned.md)

---

## Navigation

* Back to the [English documentation index](README.md)
* Back to [Validation and Results](validation-and-results.md)
* Switch to the [Spanish version](../es/deployment-not

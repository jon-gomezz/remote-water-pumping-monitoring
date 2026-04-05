# Lessons Learned

## Purpose of This Document

This document summarizes the main engineering lessons extracted from the project.

In a portfolio repository, this section is especially important because it shows judgment, reflection, and technical maturity. The goal is not only to say that the system worked, but to explain what the project taught about architecture, deployment, integration, scalability, and practical decision-making in an industrial OT/IoT context.

This is intentionally written as a **technical reflection document**, not as a generic conclusion chapter.

---

## Why This Section Matters

A project like this is valuable not only because it connects devices and dashboards, but because it reveals how real engineering systems behave when several technologies must work together under practical constraints.

The most useful lessons did not come from theory alone. They came from implementation, iteration, and adapting the design to real operational limitations.

That is why this section focuses on lessons that would still matter if the system were extended to more stations or redesigned for a more production-oriented deployment.

---

## Recommended Main Figure

Place this image near the top of the page, after the introduction.

```md
<p align="center">
  <img src="../../assets/images/lessons-learned/engineering-lessons-overview.png" alt="Engineering lessons overview" width="950"/>
</p>
<p align="center"><em>Figure 1. Summary view of the main technical lessons learned during the design, implementation, and pilot validation of the monitoring solution.</em></p>
```

---

## Lesson 1. A Good Industrial IoT Solution Starts with Clear Problem Framing

One of the strongest lessons from the project is that the technical stack only makes sense when the operational problem is clearly defined first.

This project was not about “using LoRaWAN” or “building dashboards” in isolation. It was about solving a specific problem:

* remote pumping stations were difficult to supervise continuously
* local-only visibility increased operational friction
* centralized monitoring could reduce unnecessary travel and improve response capacity

The technologies were useful because they served that operational need.

### Why this matters

In future projects, it is important to frame the problem first in terms of:

* what needs to be monitored
* how often the information needs to be refreshed
* what operators need to see
* what constraints exist at the field side and at the central side

That problem-first mindset prevents unnecessary complexity.

---

## Lesson 2. Layered Architectures Are Easier to Build, Explain, and Scale

A second key lesson is the value of thinking in layers.

The project worked well because it was not treated as one opaque system. Instead, it was structured into layers:

* field acquisition
* industrial serial communication
* telemetry conversion
* LoRaWAN networking
* MQTT delivery
* dashboard processing
* storage and analysis

### Why this was important

This made the solution easier to:

* reason about during design
* test during implementation
* troubleshoot during deployment
* communicate in documentation and interviews
* extend toward additional stations

When systems are designed in layers, failures are easier to isolate and responsibilities are easier to assign.

---

## Lesson 3. Standardizing Data Early Simplifies the Entire Pipeline

One of the most important technical lessons in the project is that **data structure matters as much as communication**.

The PLC-side decision to organize variables into a consistent internal format was extremely valuable.

That structure made it easier to align:

* Modbus register access
* payload construction in the Dragino
* TTN decoder logic
* Node-RED variable extraction
* dashboard mapping

### Main takeaway

If the project had treated every station as a fully custom case from the beginning, the system would have become much harder to maintain.

The lesson is clear:

**standardize data before transmitting it whenever possible.**

This is one of the most transferable lessons of the whole project.

---

## Lesson 4. OT/IoT Integration Is Mostly an Alignment Problem

At first glance, a project like this may look like a connectivity problem. In practice, it is more accurate to say that it is an **alignment problem across multiple technical worlds**.

The project had to align:

* field signals and process meaning
* PLC memory organization
* Modbus addressing
* Dragino payload selection
* TTN decoder assumptions
* MQTT topic reception
* Node-RED dashboard interpretation
* ThingSpeak field mapping

### What this teaches

Most integration failures do not come from one dramatic error. They come from small mismatches between layers.

For that reason, successful OT/IoT projects require strong configuration discipline and clear interface definitions.

---

## Suggested Data-Alignment Figure

Place this image after Lesson 4.

```md
<p align="center">
  <img src="../../assets/images/lessons-learned/data-alignment-chain.png" alt="Data alignment chain" width="930"/>
</p>
<p align="center"><em>Figure 2. Alignment dependency between PLC structure, Modbus addressing, payload definition, decoder logic, and dashboard mapping.</em></p>
```

---

## Lesson 5. Prototyping in Isolated Steps Saves Time Later

Another major lesson is the practical value of staged validation.

The implementation did not go directly from concept to final deployment. Instead, parts of the system were tested progressively:

* wireless path validation
* signal-generator tests
* PLC + Dragino prototype tests
* reception and decoding development
* Node-RED-based supervision
* pilot validation

### Why this worked well

Testing subsystems in isolation reduced ambiguity.

For example, using a signal generator before full PLC integration helped distinguish whether a problem belonged to:

* the wireless transmission path
* the converter logic
* the PLC data side

### Main takeaway

In distributed telemetry systems, **progressive subsystem validation is far more efficient than trying to debug the full stack at once.**

---

## Lesson 6. Real Infrastructure Constraints Can Change the Best Technical Path

One of the most valuable lessons came from the adaptation required in Araka.

The initial reception logic evolved because the practical infrastructure conditions did not support the most straightforward Ethernet-centered approach in the central environment.

That forced the project to shift toward Node-RED as the main reception, processing, and visualization layer.

### Why this matters

This was not a failure of the design. It was good engineering adaptation.

A strong engineering solution is not the one that follows the first plan rigidly. It is the one that preserves the project goals while adapting intelligently to real conditions.

### Main takeaway

**Field reality always has a vote.**

Design flexibility is just as important as technical correctness.

---

## Lesson 7. Node-RED Can Be Much More Than a Simple Dashboard Tool

Before implementation, Node-RED could have been seen as a visualization add-on. In practice, it became a central integration component.

It solved several needs simultaneously:

* MQTT reception
* decoded payload handling
* variable routing
* dashboard presentation
* central operational visibility

### Why this lesson is important

The project shows that low-code tools can play a serious engineering role when used in the right context.

Node-RED worked well because the goal was supervisory monitoring, fast iteration, and flexible integration, not hard real-time control.

### Main takeaway

Tool selection should depend on the operational role of the system, not only on whether the tool looks “industrial” enough at first glance.

---

## Suggested Node-RED Role Figure

Place this image after Lesson 7.

```md
<p align="center">
  <img src="../../assets/images/lessons-learned/node-red-central-role.png" alt="Node-RED central role" width="930"/>
</p>
<p align="center"><em>Figure 3. Node-RED as a central integration layer rather than only a dashboarding tool.</em></p>
```

---

## Lesson 8. LoRaWAN Is Well Suited to Supervisory Monitoring, Not Everything

The project reinforces an important architecture lesson: communication technology should be matched to operational requirements.

LoRaWAN was a good choice here because the use case required:

* long-range communication
* low operating cost
* support for geographically distributed assets
* intermediate refresh times rather than very fast control cycles

### What this teaches

LoRaWAN is highly effective when the problem matches its strengths.

It is not the right technology for every industrial use case, especially not for applications that require:

* high throughput
* very low latency
* continuous dense data transmission
* tight closed-loop control

### Main takeaway

A technology can be a very good choice without being a universal choice.

That distinction improves design quality.

---

## Lesson 9. Pilot Success Does Not Mean Full Production Readiness

The pilot validated the architecture successfully, but it also highlighted the difference between a **working pilot** and a **fully hardened production system**.

The pilot demonstrated:

* end-to-end functionality
* useful centralized monitoring
* viable long-range communication
* repeatable station-side pattern

But a broader production rollout would still require further work in areas such as:

* large-scale deployment discipline
* long-duration field validation
* stronger security hardening
* more formal operational support procedures
* possibly more controlled network-server ownership depending on requirements

### Main takeaway

A good engineer should be able to say both:

* “the pilot worked”
* “there is still work to do before production hardening”

That balance improves credibility.

---

## Lesson 10. Documentation Is Part of the Engineering Work, Not a Final Formality

Another clear lesson is that multi-layer systems are difficult to maintain without disciplined documentation.

This project depends on several artifacts staying aligned:

* PLC variable map
* Modbus register map
* Dragino configuration
* TTN decoder logic
* MQTT topic expectations
* Node-RED flow design
* ThingSpeak field mapping

### Why this matters

If these elements are not documented clearly, the system may still work temporarily, but future extension and maintenance become much harder.

### Main takeaway

In systems integration projects, **documentation is part of the deployment itself**.

It is not something that should be postponed until the very end.

---

## Lesson 11. Simplicity Usually Wins in Distributed Monitoring Projects

A particularly practical lesson from the project is that simplicity creates reliability.

The solution did not try to solve everything at once. It focused on a manageable objective:

* acquire meaningful variables
* transmit them efficiently
* decode them cleanly
* visualize them clearly
* store them for later review

### Why this was valuable

Trying to add too many features too early would likely have made the project more fragile and harder to validate.

### Main takeaway

In remote monitoring systems, a smaller but coherent architecture is often better than a larger but unstable one.

---

## Lesson 12. Repeatability Is a Better Design Goal Than One-Off Optimization

The architecture was conceived for more than one station, even though the pilot validated a reduced subset.

That means the real achievement is not that one station worked, but that the project established a **repeatable station-to-central pattern**.

### Why this matters

Repeatability is what makes the design valuable for future growth.

It means that expansion can be approached by reusing:

* the same acquisition logic pattern
* the same communication chain
* the same decoder approach
* the same dashboard organization model

### Main takeaway

In industrial portfolio projects, showing that a pattern can be repeated is often more valuable than showing excessive optimization for a single site.

---

## Suggested Repeatability Figure

Place this image after Lesson 12.

```md
<p align="center">
  <img src="../../assets/images/lessons-learned/repeatable-station-pattern.png" alt="Repeatable station pattern" width="940"/>
</p>
<p align="center"><em>Figure 4. Repeatable station-to-central design pattern derived from the pilot architecture.</em></p>
```

---

## Design Trade-Offs That Became Clear

The project also made several trade-offs more visible.

### Industrial robustness vs rapid experimentation

Using PLC-centered acquisition improved realism and field suitability, but also increased configuration effort compared with a purely software-simulated pipeline.

### Centralized simplicity vs field-side flexibility

Standardizing station data made expansion easier, but it also required discipline when local station realities differed.

### Fast dashboarding vs enterprise hardening

Node-RED enabled fast and effective monitoring, but it is not automatically equivalent to a hardened enterprise SCADA solution.

### Public-network convenience vs production control

TTN was extremely useful for the pilot, but a more controlled network-server model may be preferable for some production scenarios.

Recognizing trade-offs like these is a sign of design maturity.

---

## What I Would Keep the Same

If the project were repeated with the same pilot objectives, the following choices would still make sense:

* use a layered architecture
* standardize data early inside the PLC
* validate subsystems progressively
* use LoRaWAN for long-range supervisory telemetry
* use MQTT for clean message delivery
* use Node-RED for flexible central-side integration and dashboards
* keep historical storage as a separate concern from live monitoring

These decisions created most of the project’s strengths.

---

## What I Would Improve in a Next Iteration

A strong lessons-learned section should also show what could be improved.

### 1. Stronger configuration versioning

Track more formally the relationships between PLC variables, Modbus maps, payload structures, and decoders.

### 2. Better deployment artifact packaging

Preserve sample configurations, exports, and mapping tables in a more reusable repository structure.

### 3. Broader field validation

Extend validation across more stations and over longer operating periods.

### 4. Stronger security hardening

Review the communication and access model more deeply if the system moves toward a production environment.

### 5. More explicit alarm logic and analytics

Extend the monitoring layer with richer event detection, historical insight, and maintenance-oriented alerting.

These are natural next steps after a successful pilot.

---

## Suggested Improvement Roadmap Figure

Place this image near the end of the document, before the conclusion.

```md
<p align="center">
  <img src="../../assets/images/lessons-learned/next-iteration-roadmap.png" alt="Next iteration roadmap" width="950"/>
</p>
<p align="center"><em>Figure 5. Main improvement directions identified after the pilot validation of the monitoring system.</em></p>
```

---

## Final Reflection

The most important lesson from this project is that useful engineering solutions are rarely the result of one clever technology choice.

They come from:

* understanding the operational problem clearly
* structuring the system in manageable layers
* keeping interfaces aligned
* validating progressively
* adapting when real infrastructure constraints appear
* documenting the system well enough that it can be extended later

This project is a good example of that.

Its value lies not only in the fact that it produced a working pilot, but in the fact that it built a realistic and repeatable bridge between industrial control, long-range telemetry, centralized dashboards, and cloud-supported analysis.

---

## Conclusion

From a portfolio perspective, the lessons learned can be summarized in a simple way:

* start from the real operational need
* design in layers
* standardize data early
* test progressively
* adapt to deployment reality
* document interfaces clearly
* separate pilot success from production readiness
* design for repeatability rather than one-off complexity

Those lessons are broader than this single project. They are useful principles for future OT/IoT monitoring systems as well.

---

## Navigation

* Back to the [English documentation index](README.md)
* Back to [Deployment Notes](deployment-notes.md)
* Switch to the [Spanish version](../es/lessons-learned.md)

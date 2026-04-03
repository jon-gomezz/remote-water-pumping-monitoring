# Project Overview

## Project Context

This project was developed in the context of an engineering internship focused on water supply infrastructure in Vitoria-Gasteiz. The work addressed the need to improve the supervision of remote pumping and storage stations that were geographically distributed and operationally important for the supply network.

These installations contained process signals and equipment that were relevant for daily operation, but visibility over them was more limited when relying only on local control or physical inspections. In that context, the project was conceived as a practical industrial digitalization initiative: not only to measure variables locally, but to make them accessible from a central point through a coherent communication and monitoring approach.

## Operational Need

The operational need behind the project was clear: remote stations had to be monitored in a more efficient and centralized way. From an engineering perspective, the challenge was not just signal acquisition, but building a usable communication chain between field-level equipment and supervisory tools.

The solution had to help operators and technical staff gain better visibility over the state of remote assets, detect incidents faster, reduce dependence on manual checks, and create a structure that could later be reused in additional installations.

## Pilot Scope

The project was approached as a real pilot with practical engineering value. Its scope was centered on remote monitoring rather than full remote control. In other words, the main goal was to capture and transmit relevant operational data from remote stations to a central monitoring environment.

The work focused on defining a viable communication and supervision strategy, validating that the selected components could work together, and demonstrating that field variables from distributed water infrastructure could be integrated into a centralized monitoring workflow.

## Stations Considered

The project was intended for remote water-related installations within the supply system, especially pumping and storage environments where operational visibility was important. Depending on the station, the monitored setup could involve pumps, storage deposits, wells, pressure points, and water quality control variables.

A relevant aspect of the pilot was that the solution was not conceived for a single isolated installation only. It was designed with the idea that similar stations could follow the same monitoring logic and communication structure.

## Variables to Monitor

One of the practical engineering tasks of the project was defining which field variables were worth integrating into the monitoring system. Depending on the station, the relevant variables could include:

- Pump running status.
- Pump fault status.
- Tank level.
- Well level.
- Pressure values.
- Flow rate.
- Chlorine concentration.

This mix of digital and analog signals made the project more representative of a real industrial monitoring scenario. It also required organizing information in a structured and reusable way rather than treating each signal independently.

## Engineering Constraints

Several engineering constraints shaped the project from the beginning.

First, the stations were geographically distributed, so the communication approach had to be suitable for long-distance transmission. Second, the solution had to remain technically realistic and economically reasonable for this type of infrastructure. Third, the monitoring architecture needed to be robust enough to work with industrial equipment while still being simple enough to replicate.

Another important constraint was scalability. The project was not only about making one station visible, but about proposing an approach that could be extended to several remote installations with similar needs. Interoperability with existing or future supervisory environments was also an important consideration.

## Why the Project Mattered

This project mattered because it addressed a very real industrial problem: how to improve visibility over remote infrastructure without relying exclusively on local supervision. In operational environments such as water supply systems, delayed visibility can translate into slower reaction to incidents, less efficient maintenance, and reduced overall situational awareness.

From a portfolio perspective, the value of the project lies in the fact that it was not a toy example. It involved industrial context, process variables, communication constraints, systems integration decisions, and practical engineering trade-offs.

## Scope of My Work

My contribution was centered on the engineering and integration side of the project. The work included understanding the monitoring needs of the installations, helping define the communication approach, participating in the organization of field signals, and contributing to the logic needed to move information from station-level equipment toward a centralized monitoring environment.

More broadly, my role was part of the system integration effort: connecting industrial sensing and control elements with communication and visualization layers in a technically coherent way.

## What Was Achieved

The project achieved a solid proof that a centralized monitoring approach for remote water stations was feasible using an industrial IoT architecture. It established a workable basis for supervising relevant process variables from a central point and showed that distributed field assets could be integrated into a broader monitoring strategy.

Just as importantly, the project helped define a reusable engineering approach. Instead of solving only one isolated case, it contributed to a structure that could support similar remote stations in future iterations.

## What Was Out of Scope

The project was not intended to be a public release of a full production system. For that reason, this repository does not include sensitive configuration details, confidential infrastructure information, or credentials.

It is also important to note that the main scope of the work was remote monitoring and communication, not the complete redesign of plant control systems, advanced analytics, or a fully reproducible software product. This repository should therefore be understood as a professional reconstruction of the engineering work, focused on documentation, architecture logic, and portfolio presentation.



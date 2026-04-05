# Project Overview

## Summary

This project documents the design of an industrial IoT monitoring system for remote water pumping stations connected to the water supply network of Vitoria-Gasteiz. The main purpose of the solution was to centralize the supervision of distributed installations, improve visibility over field assets, and reduce the operational limitations of relying only on local monitoring or manual inspections.

The system combines industrial automation components and lightweight communication technologies. At station level, Siemens S7-1200 PLCs acquire digital and analog signals from field devices. These data are exposed through Modbus RTU over RS485 and sent through Dragino RS485-LN LoRaWAN communication devices. The information is then managed through The Things Network (TTN), transported using MQTT, visualized in Node-RED, and stored or analyzed through ThingSpeak.

This repository presents a portfolio-oriented reconstruction of that engineering work. Sensitive operational details, credentials, and internal production configurations are intentionally omitted or sanitized.

## Context

Water infrastructure is often composed of geographically dispersed pumping stations, storage points, and control elements. These installations may include pumps, tank level measurement, pressure monitoring, flow monitoring, and water quality variables such as chlorine concentration. In many cases, the difficulty is not only measuring the process variables, but integrating them into a centralized monitoring system that is technically viable, scalable, and economically reasonable.

The project was developed in the context of an internship and focused on the communication and monitoring needs of remote stations belonging to the water supply system. The goal was not simply to acquire signals locally, but to design a complete communication chain from field instrumentation to supervisory visualization.

## Main Problem

Before this solution, remote stations depended more heavily on local systems and physical checks. That makes response times slower, complicates the detection of incidents, and reduces the ability to supervise several installations from a single point.

The engineering challenge was to create a system capable of:

* Acquiring operational variables from remote stations.
* Transmitting data over long distances.
* Integrating industrial automation hardware with IoT communication layers.
* Presenting the information in a central interface.
* Defining a structure that could be replicated across additional stations.

## Project Objectives

The project objectives were the following:

* Centralize the monitoring of several remote water pumping stations.
* Capture both digital and analog industrial signals.
* Use a long-range and low-power communication approach.
* Create a practical bridge between field instrumentation and cloud or supervisory tools.
* Support future integration with SCADA-style environments.
* Establish a reusable architecture for similar remote installations.

## Solution Scope

The solution was conceived as an end-to-end monitoring architecture covering the following layers:

* **Field layer:** sensors, pump-related signals, and process variables.
* **Control layer:** Siemens PLC-based signal acquisition and internal variable mapping.
* **Communication layer:** Modbus RTU over RS485 and LoRaWAN transmission.
* **Network and transport layer:** TTN and MQTT-based message flow.
* **Visualization layer:** Node-RED dashboards and ThingSpeak cloud views.

This scope made the project more than a simple automation exercise. It became a systems integration project involving industrial control, communications, data transport, and visualization.

## Stations and Operational Variables

Depending on the station, the monitored information could include:

* Pump running status.
* Pump fault status.
* Tank level.
* Well level.
* Line or suction pressure.
* Flow rate.
* Chlorine concentration.

A key part of the engineering work was not only capturing those variables, but structuring them in a consistent way so that the same communication logic could be reused across different stations.

## Why This Architecture Was Relevant

This architecture was technically attractive because it balanced several requirements at once:

* Industrial robustness through PLC-based acquisition.
* Communication over geographically distributed sites.
* Reduced deployment complexity compared with heavier alternatives.
* Support for multiple remote stations.
* Periodic updates suitable for operational monitoring.
* Future scalability.

In other words, the project was designed not just to make one station visible, but to define a monitoring approach that could be extended.

## My Role

My contribution focused on the engineering and integration side of the project. The work included:

* Understanding the operational needs of the stations.
* Contributing to the communication architecture definition.
* Participating in signal mapping and data structuring.
* Working with PLC-centered acquisition logic.
* Integrating Modbus and LoRaWAN communication layers.
* Supporting the data flow toward TTN, MQTT, Node-RED, and ThingSpeak.
* Participating in testing and validation.

## Main Outcome

The project showed that it was feasible to build a centralized remote monitoring architecture for water infrastructure using a combination of industrial automation and IoT technologies. It established a functional basis for supervising field assets from a central point and demonstrated how PLC-based industrial systems can be connected to lightweight long-range communications and cloud visualization tools.

Beyond the specific implementation, the project was also valuable as a practical example of industrial digitalization: it connected field devices, industrial protocols, wireless communications, dashboards, and cloud services into a coherent workflow.

## Repository Perspective

This repository is intended to present the project from a portfolio and engineering documentation perspective. It focuses on:

* The problem that was solved.
* The architecture that was selected.
* The system integration logic behind the design.
* The technologies involved.
* The lessons that can be generalized to similar industrial IoT scenarios.

It should be understood as a professional reconstruction of a real project rather than as a full publication of the original production environment.

---

# Descripción general del proyecto

## Resumen

Este proyecto documenta el diseño de un sistema de monitorización de IoT industrial para estaciones remotas de bombeo de agua conectadas a la red de abastecimiento de Vitoria-Gasteiz. El propósito principal de la solución era centralizar la supervisión de instalaciones distribuidas, mejorar la visibilidad sobre los activos de campo y reducir las limitaciones operativas de depender únicamente de la monitorización local o de inspecciones manuales.

El sistema combina componentes de automatización industrial y tecnologías de comunicación ligeras. A nivel de estación, los PLCs Siemens S7-1200 adquieren señales digitales y analógicas de dispositivos de campo. Estos datos se exponen mediante Modbus RTU sobre RS485 y se envían a través de dispositivos LoRaWAN Dragino RS485-LN. La información se gestiona después mediante The Things Network (TTN), se transporta usando MQTT, se visualiza en Node-RED y se almacena o analiza mediante ThingSpeak.

Este repositorio presenta una reconstrucción orientada a portfolio de ese trabajo de ingeniería. Los detalles operativos sensibles, las credenciales y las configuraciones internas de producción se han omitido o saneado intencionadamente.

## Contexto

La infraestructura hidráulica suele estar compuesta por estaciones de bombeo, puntos de almacenamiento y elementos de control distribuidos geográficamente. Estas instalaciones pueden incluir bombas, medida de nivel de depósito, monitorización de presión, monitorización de caudal y variables de calidad del agua como la concentración de cloro. En muchos casos, la dificultad no está solo en medir las variables de proceso, sino en integrarlas en un sistema de monitorización centralizado que sea técnicamente viable, escalable y razonable desde el punto de vista económico.

El proyecto se desarrolló en el contexto de unas prácticas y se centró en las necesidades de comunicación y monitorización de estaciones remotas pertenecientes al sistema de abastecimiento de agua. El objetivo no era simplemente adquirir señales de forma local, sino diseñar una cadena de comunicación completa desde la instrumentación de campo hasta la visualización supervisora.

## Problema principal

Antes de esta solución, las estaciones remotas dependían más de sistemas locales y de comprobaciones presenciales. Eso hace que los tiempos de respuesta sean más lentos, complica la detección de incidencias y reduce la capacidad de supervisar varias instalaciones desde un único punto.

El reto de ingeniería consistía en crear un sistema capaz de:

* Adquirir variables operativas de estaciones remotas.
* Transmitir datos a larga distancia.
* Integrar hardware de automatización industrial con capas de comunicación IoT.
* Presentar la información en una interfaz central.
* Definir una estructura que pudiera replicarse en estaciones adicionales.

## Objetivos del proyecto

Los objetivos del proyecto eran los siguientes:

* Centralizar la monitorización de varias estaciones remotas de bombeo de agua.
* Capturar señales industriales tanto digitales como analógicas.
* Utilizar un enfoque de comunicación de largo alcance y bajo consumo.
* Crear un puente práctico entre la instrumentación de campo y las herramientas cloud o de supervisión.
* Facilitar una futura integración con entornos tipo SCADA.
* Establecer una arquitectura reutilizable para instalaciones remotas similares.

## Alcance de la solución

La solución se concibió como una arquitectura de monitorización de extremo a extremo que cubría las siguientes capas:

* **Capa de campo:** sensores, señales relacionadas con bombas y variables de proceso.
* **Capa de control:** adquisición de señales basada en PLC Siemens y mapeo interno de variables.
* **Capa de comunicación:** Modbus RTU sobre RS485 y transmisión LoRaWAN.
* **Capa de red y transporte:** flujo de mensajes mediante TTN y MQTT.
* **Capa de visualización:** dashboards en Node-RED y vistas cloud en ThingSpeak.

Este alcance hizo que el proyecto fuera algo más que un simple ejercicio de automatización. Se convirtió en un proyecto de integración de sistemas que involucraba control industrial, comunicaciones, transporte de datos y visualización.

## Estaciones y variables operativas

Dependiendo de la estación, la información monitorizada podía incluir:

* Estado de marcha de bombas.
* Estado de fallo de bombas.
* Nivel de depósito.
* Nivel de pozo.
* Presión de línea o de aspiración.
* Caudal.
* Concentración de cloro.

Una parte clave del trabajo de ingeniería no consistió solo en capturar esas variables, sino en estructurarlas de forma consistente para que la misma lógica de comunicación pudiera reutilizarse en distintas estaciones.

## Por qué esta arquitectura era relevante

Esta arquitectura resultaba técnicamente atractiva porque equilibraba varios requisitos a la vez:

* Robustez industrial mediante adquisición basada en PLC.
* Comunicación entre emplazamientos distribuidos geográficamente.
* Menor complejidad de despliegue frente a alternativas más pesadas.
* Soporte para múltiples estaciones remotas.
* Actualizaciones periódicas adecuadas para monitorización operativa.
* Escalabilidad futura.

En otras palabras, el proyecto se diseñó no solo para hacer visible una estación, sino para definir un enfoque de monitorización que pudiera ampliarse.

## Mi papel

Mi contribución se centró en la parte de ingeniería e integración del proyecto. El trabajo incluyó:

* Comprender las necesidades operativas de las estaciones.
* Contribuir a la definición de la arquitectura de comunicaciones.
* Participar en el mapeo de señales y en la estructuración de datos.
* Trabajar con lógica de adquisición centrada en PLC.
* Integrar las capas de comunicación Modbus y LoRaWAN.
* Dar soporte al flujo de datos hacia TTN, MQTT, Node-RED y ThingSpeak.
* Participar en pruebas y validación.

## Resultado principal

El proyecto mostró que era viable construir una arquitectura centralizada de monitorización remota para infraestructuras hidráulicas usando una combinación de automatización industrial y tecnologías IoT. Estableció una base funcional para supervisar activos de campo desde un punto central y demostró cómo los sistemas industriales basados en PLC pueden conectarse a comunicaciones ligeras de largo alcance y a herramientas cloud de visualización.

Más allá de la implementación concreta, el proyecto también fue valioso como ejemplo práctico de digitalización industrial: conectó dispositivos de campo, protocolos industriales, comunicaciones inalámbricas, dashboards y servicios cloud dentro de un flujo coherente.

## Perspectiva del repositorio

Este repositorio está pensado para presentar el proyecto desde una perspectiva de portfolio y documentación de ingeniería. Se centra en:

* El problema que se resolvió.
* La arquitectura que se seleccionó.
* La lógica de integración de sistemas detrás del diseño.
* Las tecnologías implicadas.
* Los aprendizajes que pueden generalizarse a escenarios similares de IoT industrial.

Debe entenderse como una reconstrucción profesional de un proyecto real, y no como una publicación completa del entorno de producción original.

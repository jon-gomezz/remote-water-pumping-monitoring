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

---

# Arquitectura

## Resumen de la arquitectura

El sistema sigue una arquitectura de IoT industrial diseñada para trasladar datos operativos desde estaciones remotas de bombeo de agua hasta un entorno centralizado de monitorización en Araka.

En cada estación, las señales de campo son recogidas por un PLC Siemens S7-1200. Estos datos se exponen mediante Modbus RTU sobre RS485 y son leídos por un dispositivo Dragino RS485-LN, que convierte la información en mensajes LoRaWAN para su transmisión inalámbrica de largo alcance. El tráfico LoRaWAN es recibido por un The Things Indoor Gateway (TTIG) y gestionado a través de The Things Network (TTN). A partir de ahí, los datos se reenvían mediante MQTT a Node-RED, donde se decodifican, procesan y muestran en paneles de control. Finalmente, los datos procesados se envían a ThingSpeak para su almacenamiento, visualización histórica y análisis adicional.

## Flujo de datos de alto nivel

```text
Señales de campo
  -> PLC Siemens S7-1200
  -> Modbus RTU sobre RS485
  -> Dragino RS485-LN
  -> LoRaWAN
  -> Gateway TTIG
  -> The Things Network (TTN)
  -> MQTT
  -> Node-RED en Araka
  -> ThingSpeak
```

## Capas del sistema

### 1. Capa de campo

La capa de campo contiene las señales de proceso procedentes de cada estación de bombeo. Dependiendo de la estación, estas señales pueden incluir:

* Estado de marcha de bombas.
* Estado de fallo de bombas.
* Nivel de depósito.
* Nivel de pozo.
* Presión.
* Caudal.
* Variables relacionadas con el cloro.

Estas señales representan el estado operativo de cada instalación remota y son el punto de partida de la cadena de monitorización.

### 2. Capa de control

Cada estación se articula en torno a un PLC Siemens S7-1200. El PLC se encarga de:

* Adquirir señales digitales y analógicas.
* Organizar internamente las variables.
* Exponer los datos relevantes para su comunicación.

El PLC aporta la robustez industrial del sistema y actúa como unidad local de control y adquisición a nivel de estación.

### 3. Capa de comunicación local

La comunicación entre el PLC y el dispositivo de transmisión LoRaWAN se basa en Modbus RTU sobre RS485.

Esta capa actúa como puente entre el hardware de control industrial y el sistema de comunicación inalámbrica. En este proyecto, el Dragino RS485-LN actúa como maestro Modbus y consulta el PLC para recuperar los valores necesarios.

## Arquitectura de comunicación LoRaWAN

### 4. Capa de dispositivo final

El extremo de comunicación situado en la estación es el Dragino RS485-LN. Su función principal es:

* Leer datos de la estación a través de RS485.
* Preparar el payload.
* Transmitir la información mediante LoRaWAN.

Este dispositivo permite conectar equipos industriales convencionales a una red IoT inalámbrica de largo alcance sin rediseñar la lógica de control local.

### 5. Capa de gateway

La transmisión inalámbrica es recibida por un The Things Indoor Gateway (TTIG), que actúa como puente entre la red de radio y el backend conectado a internet.

Su función es recibir paquetes LoRa procedentes de las estaciones remotas y reenviarlos al servidor de red.

### 6. Capa de servidor de red

El servidor de red LoRaWAN lo proporciona The Things Network (TTN).

En esta arquitectura, TTN se encarga de:

* Registro de dispositivos.
* Gestión de la red.
* Enrutado de paquetes.
* Gestión segura del tráfico LoRaWAN.

Una decisión arquitectónica relevante del proyecto fue utilizar una configuración LoRaWAN con un servidor de red independiente en lugar de un gateway con servidor embebido. Este enfoque ofrecía mayor flexibilidad y escalabilidad para un despliegue con múltiples estaciones.

## Procesamiento y visualización de datos

### 7. Capa de transporte MQTT

Una vez que los datos llegan a TTN, se reenvían mediante MQTT.

MQTT se utiliza como mecanismo de transporte entre la plataforma de red y el entorno central de monitorización. Esto mantiene la integración ligera y práctica para flujos de datos orientados a IoT.

### 8. Capa de monitorización y visualización

Node-RED es la principal plataforma de visualización y tratamiento de datos en el entorno central.

Sus responsabilidades incluyen:

* Suscribirse a tópicos MQTT.
* Decodificar los payloads entrantes.
* Transformar los datos recibidos.
* Distribuir valores a widgets del dashboard.
* Presentar la información de las estaciones casi en tiempo real.

Node-RED también fue una elección práctica porque proporcionaba un entorno visual accesible para construir paneles e integrarse con lógica de supervisión existente.

### 9. Capa de host edge

Node-RED está alojado en una Orange Pi 3 LTS, utilizada para mantener el servicio de monitorización en funcionamiento continuo.

Este componente proporciona una plataforma de computación ligera para:

* Recepción continua de datos de las estaciones.
* Servicio local del dashboard.
* Integración con servicios posteriores.

### 10. Capa de almacenamiento y analítica

ThingSpeak se utiliza como capa de almacenamiento y analítica en la nube.

Su papel en la arquitectura es:

* Almacenar datos históricos de las estaciones.
* Dar soporte a la visualización de tendencias.
* Permitir análisis estadístico adicional.
* Servir de base para alertas o lógica futura orientada a anomalías.

Esto hace que la solución global sea algo más que un dashboard en tiempo real: también incorpora una perspectiva histórica de los datos.

## Por qué se eligió esta arquitectura

La arquitectura seleccionada estaba alineada con las restricciones operativas y de ingeniería del proyecto.

Se diseñó para proporcionar:

* Comunicación de largo alcance a un coste relativamente bajo.
* Compatibilidad con equipos industriales.
* Supervisión centralizada de múltiples estaciones remotas.
* Tiempos de refresco cortos para monitorización.
* Escalabilidad para futuras incorporaciones de estaciones.
* Integración práctica con herramientas de monitorización.

El diseño resultante equilibra la fiabilidad industrial a nivel de estación con tecnologías ligeras de comunicación IoT y visualización.

## Principales decisiones arquitectónicas

### Adquisición industrial con PLCs

El proyecto utiliza adquisición basada en PLC en lugar de hardware IoT puramente de consumo. Esto mejora la robustez y hace que la solución sea más creíble para una infraestructura real.

### RS485 / Modbus como interfaz local

El uso de Modbus RTU sobre RS485 proporciona una capa de comunicación industrial sencilla y conocida entre la adquisición y la transmisión inalámbrica.

### LoRaWAN para la conectividad remota

LoRaWAN se seleccionó porque las estaciones están distribuidas geográficamente y el proyecto requería comunicación de largo alcance con bajo coste operativo.

### Modelo de servidor de red independiente

El sistema se diseñó en torno a TTN como servidor de red externo, lo que resulta más escalable que mantener toda la lógica de red dentro de un único gateway.

### Node-RED como capa central de monitorización

Node-RED ofrecía una forma práctica de decodificar mensajes, construir dashboards y crear un puente hacia entornos de supervisión.

### ThingSpeak para datos históricos

ThingSpeak añadió persistencia, visualización histórica y una base para alertas y análisis posteriores.

## Consideraciones de escalabilidad

La arquitectura fue concebida como un patrón reutilizable, no como una integración aislada.

Añadir una nueva estación requeriría principalmente:

* Desplegar el PLC y el nodo de comunicación en la estación.
* Mapear las nuevas señales.
* Registrar el nuevo dispositivo en TTN.
* Extender la lógica de Node-RED / ThingSpeak.

Esto significa que la arquitectura puede crecer sin cambiar su modelo de comunicación principal.

## Limitaciones de la arquitectura

Aunque la arquitectura es práctica y escalable, también tiene algunas limitaciones:

* Está enfocada más en la monitorización remota que en el control remoto completo.
* Node-RED es muy práctico, pero no equivale a una plataforma SCADA industrial completa.
* El endurecimiento de seguridad de MQTT y Node-RED requeriría especial atención en un despliegue de nivel productivo.
* La versión pública orientada a portfolio no puede incluir los detalles confidenciales de la infraestructura original.

## Perspectiva de la arquitectura en este repositorio

Este repositorio documenta el sistema desde una perspectiva de portfolio y comunicación de ingeniería. No pretende publicar la configuración original de producción con todo detalle. En su lugar, explica la lógica de la arquitectura, las responsabilidades de cada componente y las principales decisiones que hicieron viable la solución para la monitorización remota de estaciones de agua.

# Resumen del proyecto

## Introducción

Este proyecto presenta una adaptación técnica, orientada a portfolio, de un caso real de prácticas industriales centrado en la monitorización remota de estaciones de bombeo de agua en Vitoria-Gasteiz.

El reto original surgió de la necesidad operativa de supervisar de forma más eficiente instalaciones de bombeo distribuidas geográficamente. En lugar de depender en gran medida de inspecciones presenciales, el proyecto explora cómo recoger variables de proceso desde estaciones remotas a partir de la capa de control industrial y transmitirlas a un entorno de monitorización centralizado mediante una arquitectura de comunicaciones basada en IoT.

El resultado es una solución práctica de monitorización que combina automatización industrial, comunicaciones inalámbricas de largo alcance, integración basada en mensajería y herramientas de visualización en la nube.

<p align="center">
  <img src="/assets/images/overview/monitored-stations-overview.png" alt="Vista general de las estaciones monitorizadas" width="500"/>
</p>
<p align="center"><em>Contexto general de las estaciones de bombeo distribuidas consideradas en el proyecto.</em></p>

---

## Contexto industrial

Las estaciones de bombeo de agua son activos críticos dentro de los sistemas de abastecimiento de agua potable. Su funcionamiento correcto afecta directamente a la continuidad del servicio, a la seguridad operativa y a la eficiencia del mantenimiento.

En este caso, la infraestructura monitorizada está distribuida en distintas ubicaciones remotas. Esto genera varias dificultades operativas:

* La supervisión está descentralizada
* El personal técnico puede necesitar desplazarse con frecuencia para inspeccionar los equipos
* La detección de fallos puede retrasarse cuando no existe visibilidad centralizada
* Las variables de proceso son más difíciles de comparar y analizar entre estaciones
* Los tiempos de respuesta pueden empeorar cuando la información operativa está fragmentada

Por ello, el proyecto se planteó como una iniciativa de monitorización y centralización de datos orientada a mejorar la visibilidad, reducir desplazamientos innecesarios y permitir una respuesta operativa más rápida.

---

## Planteamiento del problema

El problema principal abordado por este proyecto es la falta de un mecanismo simple y escalable para monitorizar remotamente múltiples estaciones de bombeo desde un punto centralizado.

La solución de monitorización debía responder a una necesidad industrial práctica: adquirir señales relevantes desde el hardware de control existente, transmitirlas a larga distancia con un coste operativo bajo y poner los datos resultantes a disposición de los operadores en un formato interpretable de forma rápida.

No se trata solo de un problema de conectividad. También es un problema de integración de sistemas que involucra instrumentación de campo, lógica de PLC, protocolos de comunicación industrial, telemetría inalámbrica, enrutado de mensajes y visualización de datos.

---

## Objetivo del proyecto

El objetivo principal del proyecto fue diseñar y validar una solución profesional y eficiente para la monitorización y gestión remota de estaciones de bombeo de agua, centralizando la información operativa en un punto de control situado en Araka.

Desde la perspectiva de portfolio, el valor del proyecto reside en mostrar cómo una necesidad real de monitorización industrial puede transformarse en una arquitectura coherente de extremo a extremo que conecta la capa de tecnología operacional con servicios IoT modernos y entornos de dashboards.

---

## Objetivos técnicos

El proyecto se desarrolló en torno a varios objetivos técnicos:

1. Adquirir datos operativos de las estaciones de bombeo mediante hardware de control industrial.
2. Configurar una capa de adquisición basada en PLC capaz de recoger señales digitales y analógicas relevantes.
3. Transmitir datos de campo mediante una solución de comunicación inalámbrica de largo alcance adecuada para infraestructuras remotas.
4. Integrar la telemetría entrante en una canalización de procesamiento centralizada.
5. Visualizar el estado de las estaciones y las variables de proceso mediante dashboards.
6. Almacenar y analizar los datos transmitidos para tareas de monitorización y soporte operativo.
7. Validar que la solución propuesta es funcional y lo bastante escalable como para permitir futuras ampliaciones.

---

## Alcance de la solución

El alcance del proyecto incluye el diseño y la integración de una canalización de monitorización de extremo a extremo con las siguientes capas:

* **Capa de adquisición en campo:** recogida de señales digitales y analógicas desde las estaciones de bombeo
* **Capa de comunicación industrial:** extracción y transferencia de datos del PLC mediante RS-485 / Modbus
* **Capa de transmisión inalámbrica:** comunicación de largo alcance usando LoRa / LoRaWAN
* **Capa de red IoT:** gestión de mensajes mediante The Things Network (TTN)
* **Capa de procesamiento:** decodificación, transformación y enrutado de datos mediante MQTT y Node-RED
* **Capa de visualización y almacenamiento:** visualización en dashboards y almacenamiento cloud mediante ThingSpeak

<p align="center">
  <img src="/assets/images/overview/system-architecture.png" alt="Arquitectura de alto nivel de la monitorización" width="400"/>
</p>
<p align="center"><em>Arquitectura de alto nivel de la solución de monitorización remota.</em></p>

Este repositorio se centra en la arquitectura de monitorización y en la lógica de integración, más que en reproducir la memoria académica completa.

---

## Variables monitorizadas y cobertura de estaciones

La solución global se concibió para dar soporte a múltiples estaciones de bombeo y a una combinación de señales digitales y analógicas de proceso.

Dependiendo de la estación, las variables monitorizadas pueden incluir:

* Estado de funcionamiento de bombas
* Señales de fallo de bombas
* Estado de válvulas
* Alarmas de falta de agua
* Señales de nivel de depósito
* Medidas de nivel de pozo
* Presión de red
* Presión de aspiración
* Medidas de caudal
* Valores de proceso relacionados con el cloro

El proyecto se planificó pensando en varias estaciones, aunque la validación piloto se centró en un conjunto más reducido de ubicaciones.

---

## Contexto de la validación piloto

Aunque la arquitectura se concibió para un despliegue más amplio, la validación práctica se llevó a cabo como una implementación piloto.

La solución se probó en la estación de bombeo de Mandojana y en las instalaciones de almacenamiento de agua de Araka. Esta fase piloto sirvió para verificar que la arquitectura propuesta era técnicamente válida y funcionalmente viable antes de una ampliación a otras estaciones adicionales.

<p align="center">
  <img src="/assets/images/overview/pilot-sites.png" alt="Ubicaciones de validación piloto" width="500"/>
</p>
<p align="center"><em>Contexto de validación piloto en Mandojana y Araka.</em></p>

Este es un punto importante en la versión de portfolio del proyecto: el énfasis no está en afirmar un despliegue completo en producción, sino en demostrar un piloto técnicamente sólido y con un potencial claro de expansión.

---

## Por qué esta arquitectura era relevante

La arquitectura propuesta respondía a necesidades operativas clave:

* Permitía una visibilidad centralizada sobre activos remotos
* Reducía la dependencia de la supervisión manual
* Utilizaba comunicaciones de largo alcance adecuadas para infraestructuras geográficamente dispersas
* Permitía una monitorización casi en tiempo real mediante intervalos de actualización intermedios
* Facilitaba la captura simultánea de múltiples estaciones
* Creaba una base escalable para futuras ampliaciones

Esto hace que el proyecto sea especialmente relevante como ejemplo de integración OT/IoT en un entorno industrial.

---

## Principales tecnologías utilizadas

El proyecto combina varias tecnologías con funciones diferentes dentro de la canalización de monitorización:

* **PLCs Siemens S7-1214** para la adquisición de datos en campo
* **Módulo de comunicación CM 1241** como soporte de comunicación industrial
* **Dragino RS485-LN** para la transmisión de telemetría RS-485 a LoRaWAN
* **LoRa / LoRaWAN** para comunicaciones inalámbricas de largo alcance
* **The Things Network (TTN)** como capa de red LoRaWAN
* **MQTT** para el intercambio de mensajes
* **Node-RED** para el procesamiento de datos y la lógica de dashboard
* **ThingSpeak** para almacenamiento, visualización y análisis orientado a alertas
* **Orange Pi** como entorno de computación de soporte para la pila de monitorización

---

## Valor de ingeniería del proyecto

Como proyecto de portfolio, este trabajo demuestra varias capacidades de ingeniería:

* Comprender un problema operativo real dentro de una infraestructura industrial
* Diseñar una arquitectura completa de telemetría en lugar de un componente aislado
* Integrar hardware de control con comunicaciones inalámbricas y plataformas software
* Equilibrar practicidad, coste, escalabilidad y fiabilidad
* Traducir requisitos de campo a una solución de monitorización de extremo a extremo
* Documentar trabajo técnico de forma concisa y profesional

## Qué viene después

Una vez entendido el contexto y el alcance del proyecto, el siguiente paso es examinar en detalle la arquitectura del sistema.

Continúa con: [`architecture.md`](architecture.md)

---

## Navegación

* Volver al [índice de documentación en castellano](README.md)
* Volver al [README raíz del repositorio](../../README.md)
* Cambiar a la [versión en inglés](../en/project-overview.md)

# Monitorización remota de estaciones de bombeo de agua

## Visión general

Esta sección contiene la documentación completa en castellano de un proyecto técnico de portfolio basado en un caso real de prácticas industriales centrado en la monitorización remota de estaciones de bombeo de agua.

<p align="center">
  <img src="../../assets/images/overview/system-architecture.png" alt="System architecture" width="500"/><br>
  <em>Arquitectura de alto nivel del sistema de monitorización remota de estaciones de bombeo de agua.</em>
</p>

El proyecto se centra en cómo supervisar de forma remota instalaciones de bombeo distribuidas geográficamente combinando automatización industrial, comunicaciones inalámbricas de largo alcance, mensajería IoT y herramientas de visualización en la nube.

En lugar de reproducir literalmente la memoria académica original, esta documentación reorganiza el trabajo en un relato técnico orientado a portfolio, centrado en la arquitectura, las decisiones de implementación, la integración del sistema, la validación y las conclusiones de ingeniería.

---

## Contexto del proyecto

La infraestructura de bombeo de agua suele estar distribuida en ubicaciones remotas, lo que hace ineficiente una supervisión presencial continua. En este proyecto, el objetivo fue explorar una arquitectura de monitorización práctica capaz de adquirir datos de proceso desde la capa de control industrial y ponerlos a disposición de forma remota mediante una canalización de telemetría basada en IoT.

La solución se concibió en torno a la integración de adquisición de datos desde PLC, comunicaciones RS-485/Modbus, transmisión LoRaWAN, intercambio de mensajes mediante MQTT, procesamiento con Node-RED y visualización en dashboards.

---

## Qué encontrarás en esta documentación

Esta documentación en castellano está organizada como un paquete técnico de portfolio que cubre:

* el problema industrial y el alcance del proyecto
* la arquitectura propuesta del sistema
* el hardware seleccionado y las tecnologías de comunicación
* la lógica de telemetría y flujo de datos
* el enfoque de implementación
* el proceso de validación y los resultados observados
* las consideraciones de despliegue
* las lecciones aprendidas y las posibles mejoras futuras

---

## Contenido de la documentación

### 1. Resumen del proyecto

Archivo: [`descripcion-del-proyecto.md`](descripcion-del-proyecto.md)

Describe el contexto industrial, el problema que se aborda, los objetivos del proyecto y el alcance previsto de la solución de monitorización.

### 2. Arquitectura

Archivo: [`arquitectura.md`](arquitectura.md)

Explica la arquitectura de alto nivel del sistema, el papel de cada subsistema y la cadena de comunicación de extremo a extremo desde la capa de campo hasta la capa de visualización.

### 3. Hardware y comunicaciones

Archivo: [`comunicaciones-y-hardware.md`](comunicaciones-y-hardware.md)

Resume los principales elementos hardware, las interfaces de comunicación y las decisiones de protocolo empleadas en el proyecto.

### 4. Flujo de datos

Archivo: [`flujo-de-datos.md`](flujo-de-datos.md)

Describe cómo las variables de proceso se adquieren, codifican, transmiten, decodifican, procesan y, finalmente, se publican en dashboards de monitorización.

### 5. Validación y resultados

Archivo: [`validacion-y-resultados.md`](validacion-y-resultados.md)

Presenta cómo se probó la solución, qué resultados se observaron y qué conclusiones técnicas pueden extraerse de la fase de validación.

### 6. Notas de despliegue

Archivo: [`notas-de-despliegue.md`](notas-de-despliegue.md)

Recoge notas operativas, guía de configuración, consideraciones de integración y observaciones útiles para el despliegue.

### 7. Lecciones aprendidas

Archivo: [`lecciones-aprendidas.md`](lecciones-aprendidas.md)

Destaca reflexiones de ingeniería, compromisos de diseño, limitaciones del sistema y posibles siguientes pasos de mejora.

---

## Resumen técnico de alto nivel

El proyecto puede entenderse como una canalización de telemetría con la siguiente lógica:

1. Se adquieren variables operativas desde el sistema de control.
2. Los datos se extraen a través de interfaces de comunicación industrial.
3. Una capa de comunicaciones inalámbricas de largo alcance permite la transmisión remota.
4. Los mensajes se reciben y decodifican en la capa de red IoT.
5. Node-RED procesa y enruta los datos entrantes.
6. Las variables de proceso se publican en una plataforma de visualización en la nube.
7. El resultado final es visibilidad remota del estado de las estaciones de bombeo y de determinadas condiciones de operación.

---

## Tecnologías principales implicadas

* Entorno PLC Siemens
* Comunicación RS-485 / Modbus
* Hardware de comunicaciones Dragino
* LoRa / LoRaWAN
* The Things Network (TTN)
* MQTT
* Node-RED
* ThingSpeak
* Entorno de soporte con Orange Pi

## Orden de lectura recomendado

Para una primera revisión técnica, la secuencia de lectura sugerida es:

1. `project-overview.md`
2. `architecture.md`
3. `data-flow.md`
4. `validation-and-results.md`
5. `lessons-learned.md`

Este orden ayuda al lector a avanzar desde el planteamiento del problema hasta la arquitectura, y después hacia la ejecución y las conclusiones técnicas.

---

## Relación con el trabajo original de prácticas

Este repositorio es una adaptación para portfolio de un proyecto real de prácticas de ingeniería. El trabajo original se ha reestructurado para mejorar la claridad y para poner el foco en la comunicación técnica, el razonamiento de diseño del sistema y el valor de la implementación.

Por ello, parte del contenido puede aparecer condensado, reorganizado o simplificado en comparación con la versión académica original.

---

## Navegación

* Volver al [README raíz del repositorio](/README.md)
* Cambiar a la [documentación en inglés](docs/en/README.md)

---

## Estado actual de la documentación

La documentación se está refinando progresivamente como parte de un repositorio de GitHub orientado a portfolio.

El trabajo actual incluye:

* estructuración de la documentación bilingüe
* preparación de textos centrados en la arquitectura
* organización de elementos visuales y figuras
* conversión del material académico en documentación técnica concisa

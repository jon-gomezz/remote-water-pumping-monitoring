# Arquitectura

## Propósito de esta arquitectura

La arquitectura de este proyecto se diseñó para resolver un problema industrial práctico: cómo recoger datos operativos de estaciones de bombeo de agua distribuidas geográficamente y hacer que esa información esté disponible en un punto de monitorización centralizado sin depender de una supervisión presencial constante.

Desde la perspectiva de portfolio, el valor de la arquitectura está en la integración de hardware de control industrial, comunicaciones inalámbricas de largo alcance, servicios de red IoT, procesamiento basado en mensajería y visualización remota dentro de una única canalización de monitorización coherente.

---

## Resumen arquitectónico

A alto nivel, el sistema sigue esta cadena:

**Señales de la estación de bombeo → PLC Siemens → RS-485 / Modbus → Dragino RS485-LN → LoRaWAN → gateway TTIG → TTN → MQTT → Node-RED en Araka → ThingSpeak**

<p align="center">
  <img src="/assets/images/architecture/system-architecture.png" alt="Arquitectura del sistema" width="500"/>
</p>
<p align="center"><em>Arquitectura de extremo a extremo del sistema de monitorización remota de estaciones de bombeo de agua.</em></p>

Esta arquitectura transforma datos operativos a nivel de campo en dashboards de monitorización accesibles de forma remota y en telemetría almacenada en la nube.

## Por qué se eligió esta arquitectura

La arquitectura del sistema estuvo condicionada por un conjunto de requisitos prácticos de diseño:

* comunicación a larga distancia con bajo coste operativo
* uso de hardware de grado industrial
* supervisión centralizada de múltiples estaciones remotas
* compatibilidad con un contexto SCADA ya existente
* tiempos de refresco aceptables para monitorización operativa
* soporte para señales digitales y analógicas
* robustez en entornos industriales
* escalabilidad para añadir nuevas estaciones en el futuro

Más que optimizar el máximo rendimiento, la arquitectura se optimizó para ofrecer fiabilidad, alcance, simplicidad y facilidad de mantenimiento dentro de un contexto de infraestructura hidráulica distribuida.

---

## Visión por capas de alto nivel

La solución puede entenderse como seis capas conectadas entre sí.

### 1. Capa de campo

Cada estación de bombeo contiene las señales locales del proceso que deben monitorizarse, como el estado de las bombas, señales de fallo, niveles de depósito, presiones, variables relacionadas con el caudal o valores vinculados a la calidad del agua, dependiendo de la instalación.

Estas señales se originan en el proceso físico y representan el estado operativo de cada activo remoto.

### 2. Capa de control y adquisición

En cada estación monitorizada se utiliza un PLC Siemens S7-1214 como dispositivo local de adquisición y control.

Su papel dentro de la arquitectura es:

* leer las señales digitales y analógicas disponibles
* organizarlas en una estructura interna coherente
* exponer los valores seleccionados para comunicación externa
* actuar como interfaz industrial entre el proceso y la cadena de telemetría

Un módulo de comunicación CM 1241 amplía el PLC con capacidad de comunicación RS-422/485, habilitando así el enlace serie utilizado en el proyecto.

### 3. Capa de conversión de comunicaciones en el edge

El Dragino RS485-LN actúa como puente entre las comunicaciones serie industriales y la transmisión inalámbrica IoT.

Esta es una decisión arquitectónica clave. En lugar de sustituir la capa de control industrial por dispositivos IoT nativos, el proyecto conserva la lógica basada en PLC y añade una capa de conversión que:

* consulta el PLC mediante Modbus RTU
* recibe los datos solicitados de los registros
* prepara la carga útil que se va a transmitir
* envía esa carga mediante LoRaWAN

Dentro de la red Modbus local, el módulo Dragino se comporta como maestro y el PLC como esclavo.

### 4. Capa de red inalámbrica de largo alcance

Una vez extraídos los datos del PLC, estos se transmiten mediante LoRaWAN.

Esta capa de comunicación se eligió porque ofrece:

* cobertura de largo alcance
* bajo consumo y bajo coste operativo
* idoneidad para infraestructuras distribuidas geográficamente
* una arquitectura que puede crecer para dar soporte a más estaciones

El proyecto no se planteó como un simple enlace LoRa punto a punto. En su lugar, adoptó una **arquitectura LoRaWAN con un servidor de red independiente**, mucho más adecuada para supervisión centralizada y futuras ampliaciones.

### 5. Capa de red y mensajería

El tráfico inalámbrico es recibido por un gateway TTIG y reenviado a The Things Network (TTN), que actúa como servidor de red LoRaWAN.

En esta fase, TTN se encarga de:

* recibir uplinks desde el gateway
* gestionar los dispositivos finales registrados
* enrutar los datos recibidos
* actuar como intermediario a nivel de red entre los dispositivos de campo y las aplicaciones de capas superiores

Una vez que los datos llegan a TTN, se reenvían mediante MQTT hacia el entorno central de procesamiento.

Esto convierte a MQTT en el mecanismo de transporte entre el lado LoRaWAN de la arquitectura y el lado de procesamiento de datos.

### 6. Capa de procesamiento, visualización y almacenamiento

Node-RED, ejecutándose en el entorno de Araka con soporte de Orange Pi, recibe los mensajes MQTT y se convierte en la capa operativa de presentación del sistema.

Sus funciones incluyen:

* recepción de cargas útiles
* decodificación y transformación de datos
* envío de valores a widgets de dashboard
* presentación de información de proceso en tiempo real a los operadores

Tras el procesamiento en Node-RED, los datos también se envían a ThingSpeak para:

* almacenamiento histórico
* análisis gráfico
* monitorización orientada a alarmas
* visualización adicional en la nube

Esto crea un modelo de consumo en dos niveles:

* **Node-RED** para monitorización operativa e interacción con dashboards
* **ThingSpeak** para almacenamiento, tendencias y análisis ampliado

---

## Camino de datos de extremo a extremo

<p align="center">
  <img src="/assets/images/architecture/functional-blocks.png" alt="Bloques funcionales" width="500"/>
</p>
<p align="center"><em>Descomposición funcional de la arquitectura desde la adquisición en campo hasta la visualización en la nube.</em></p>

El flujo arquitectónico completo puede describirse paso a paso:

1. Se leen las variables de proceso en la estación de bombeo.
2. El PLC adquiere y organiza las señales locales.
3. El CM 1241 habilita la comunicación serie con el Dragino RS485-LN.
4. El módulo Dragino solicita datos mediante Modbus RTU.
5. Los valores resultantes se empaquetan en una carga útil LoRaWAN.
6. La carga se transmite de forma inalámbrica.
7. El gateway TTIG recibe el mensaje por radio.
8. TTN gestiona el tratamiento a nivel de red LoRaWAN.
9. Los datos se reenvían mediante MQTT.
10. Node-RED recibe, decodifica y visualiza los valores.
11. Los datos procesados también se publican en ThingSpeak para almacenamiento y análisis.

---

## Diagrama de arquitectura por bloques funcionales

Una forma útil de explicar el sistema en entrevistas es agrupar la arquitectura en los siguientes bloques funcionales:

* **Bloque de adquisición:** PLC + señales locales
* **Bloque de conversión:** CM 1241 + Dragino RS485-LN
* **Bloque de transmisión:** LoRa / LoRaWAN + gateway
* **Bloque de red:** TTN
* **Bloque de procesamiento:** MQTT + Node-RED
* **Bloque analítico:** ThingSpeak

Este enfoque hace que la arquitectura sea más fácil de comunicar que si solo se describe como una lista de dispositivos.

---

## Por qué se eligió una arquitectura LoRaWAN con servidor de red en lugar de modos LoRa más simples

<p align="center">
  <img src="/assets/images/architecture/lorawan-network-options.png" alt="Opciones de red LoRaWAN" width="500"/>
</p>
<p align="center"><em>Comparación de los enfoques de conectividad LoRa considerados durante el diseño arquitectónico.</em></p>

El espacio de diseño original incluía distintos modelos de conectividad LoRa, como punto a punto, punto a multipunto, gateway con servidor de red embebido y red LoRaWAN con servidor de red independiente.

Para este proyecto, el enfoque con servidor de red independiente era el más adecuado porque ofrece:

* gestión centralizada de la red
* mejor escalabilidad al añadir más estaciones
* supervisión y mantenimiento remotos más sencillos
* separación más clara entre dispositivos de campo, gateways y lógica de aplicación

Esta elección encaja mejor que los enlaces LoRa directos porque el objetivo principal no es solo transmitir datos, sino habilitar una monitorización centralizada y estructurada sobre múltiples activos remotos.

---

## Por qué Node-RED era arquitectónicamente importante

<p align="center">
  <img src="/assets/images/architecture/node-red-dashboard-flow.png" alt="Flujo de dashboard en Node-RED" width="500"/>
</p>
<p align="center"><em>Node-RED como capa de procesamiento y visualización en el entorno central de monitorización.</em></p>

Node-RED no es solo una herramienta de dashboards en este proyecto. Es una capa de integración.

Esto es especialmente importante porque el punto de monitorización en Araka tenía restricciones prácticas relacionadas con el contexto SCADA existente. En lugar de depender de una integración directa de PLC con acceso a internet en el entorno central, Node-RED aportó una capa software flexible capaz de:

* recibir datos MQTT desde TTN
* decodificar las cargas entrantes
* construir dashboards de monitorización con rapidez
* adaptar la visualización a cada estación
* actuar como puente entre la telemetría entrante y la supervisión orientada a operador

Por ello, Node-RED debe entenderse como un componente arquitectónico central y no como un simple complemento secundario de visualización.

---

## Papel de Orange Pi en la arquitectura

La Orange Pi 3 LTS es la plataforma de computación de soporte que mantiene Node-RED disponible de forma continua.

Su papel es más operativo que conceptual, pero sigue siendo importante dentro de la arquitectura porque proporciona un entorno de ejecución dedicado a la capa de procesamiento y dashboards.

Eso significa que la arquitectura no termina en la mensajería cloud. También incluye un nodo local persistente capaz de recibir, transformar y presentar telemetría en vivo.

---

## Soporte para múltiples estaciones

La arquitectura se concibió para varias estaciones de bombeo dentro del sistema de abastecimiento de Vitoria-Gasteiz, aunque la validación piloto se centró en un subconjunto más reducido.

Desde el punto de vista arquitectónico, esto es relevante porque el diseño no se construyó alrededor de un único activo. Se construyó a partir de un patrón repetible por estación:

* una configuración local de adquisición por estación
* una ruta de telemetría inalámbrica por estación
* un entorno centralizado de monitorización para todos los datos entrantes

Esta repetibilidad es uno de los aspectos más fuertes del proyecto como portfolio porque demuestra que la arquitectura puede replicarse y no queda ligada a una instalación aislada.

---

## Alineación entre arquitectura y requisitos de diseño

La arquitectura responde a los principales requisitos de diseño de la siguiente manera:

### Comunicación de largo alcance y bajo coste

LoRaWAN proporciona la capa inalámbrica de largo alcance sin necesidad de infraestructura de comunicaciones costosa.

### Robustez industrial

El uso de hardware Siemens y de módulos de comunicación industriales mantiene la fiabilidad en la capa de campo.

### Integración compatible con SCADA

Node-RED proporciona una capa software práctica para supervisión centralizada y monitorización basada en dashboards.

### Monitorización simultánea de múltiples estaciones

TTN y el enrutado de mensajes basado en MQTT permiten la recepción centralizada de datos procedentes de varias ubicaciones remotas.

### Intervalos de refresco aceptables

La arquitectura admite ciclos de actualización intermedios adecuados para monitorización operativa, no para control en lazo cerrado de muy baja latencia.

### Capacidad suficiente de señales

La capa de adquisición basada en PLC soporta múltiples variables digitales y analógicas según la estación.

### Escalabilidad

Pueden incorporarse nuevas estaciones repitiendo el patrón de adquisición y comunicación del lado de estación sin rediseñar toda la arquitectura central.

---

## Fortalezas de la arquitectura

Los aspectos más sólidos de la arquitectura son:

* separación clara de responsabilidades entre adquisición, transmisión, gestión de red, procesamiento y visualización
* reutilización de hardware industrial ya adecuado para el entorno
* monitorización centralizada de activos distribuidos geográficamente
* escalabilidad más allá del despliegue piloto
* integración práctica entre capas OT e IoT
* combinación de dashboards operativos y analítica histórica en la nube

---

## Principales limitaciones

Esta versión de portfolio también debe ser honesta respecto a las limitaciones arquitectónicas.

Algunas limitaciones relevantes son:

* el sistema está diseñado para monitorización, no para lazos de control de alta velocidad
* la dependencia de TTN introduce una dependencia de un servicio público externo en la arquitectura piloto
* Node-RED es flexible, pero no equivale a una pila SCADA empresarial endurecida
* el endurecimiento de ciberseguridad puede ampliarse mucho más en futuras iteraciones

Mencionar estos límites mejora la credibilidad del portfolio porque demuestra criterio de ingeniería en lugar de vender en exceso la solución.

## Conclusión

La arquitectura de este proyecto se entiende mejor como un sistema de telemetría industrial que conecta estaciones de bombeo remotas con un entorno centralizado de monitorización mediante una canalización OT/IoT en capas.

Su valor de ingeniería no proviene del uso aislado de una sola tecnología, sino de la combinación de adquisición basada en PLC, comunicaciones serie industriales, red LoRaWAN, mensajería MQTT, dashboards en Node-RED y analítica en ThingSpeak dentro de una solución práctica y escalable para la monitorización de infraestructuras distribuidas.

---

## Qué viene después

Después de la arquitectura, el siguiente documento debe explicar con más detalle los dispositivos, interfaces y decisiones de protocolo.

Continúa con: [`comunicaciones-y-hardware.md`](comunicaciones-y-hardware.md)

---

## Navegación

* Volver al [índice de documentación en castellano](README.md)
* Volver a [Resumen del proyecto](descripcion-del-proyecto.md)
* Cambiar a la [versión en inglés](/docs/en/architecture.md)

# Hardware y comunicaciones

## Propósito de este documento

Este documento explica los principales componentes hardware, las interfaces de comunicación y las decisiones de protocolo utilizadas en el proyecto.

El objetivo no es reproducir una lista de dispositivos en formato catálogo, sino mostrar cómo contribuye cada componente a la canalización completa de monitorización y por qué la pila de comunicaciones elegida es adecuada para una infraestructura distribuida de estaciones de bombeo de agua.

---

## Papel técnico de la pila hardware

A nivel de estación, el sistema debía cumplir cuatro funciones prácticas:

1. Adquirir señales de campo de forma fiable
2. Exponer esas señales mediante una interfaz de comunicación industrial
3. Convertir los datos de la estación en telemetría inalámbrica de largo alcance
4. Entregar la información a un entorno centralizado de monitorización

Por ello, el hardware se seleccionó como una pila por capas y no como un conjunto de dispositivos aislados.

---

## Visión general del hardware

Los principales bloques hardware son:

* **Familia de PLC Siemens S7-1200 (S7-1214C)** para adquisición y control local de datos
* **Módulo de comunicación CM 1241** para soporte de comunicación serie
* **Dragino RS485-LN** para conversión de RS-485 a LoRaWAN
* **The Things Indoor Gateway (TTIG)** como gateway LoRaWAN
* **Orange Pi 3 LTS** como plataforma de soporte para la ejecución continua de Node-RED
* **Infraestructura de alimentación a 24 V** para los dispositivos industriales del lado de estación
* **Módulos adicionales de expansión de E/S** cuando lo exige el número de señales de cada estación

Estos dispositivos se conectan mediante una cadena de comunicaciones que enlaza control industrial, intercambio de datos serie, telemetría inalámbrica y monitorización en capa de aplicación.

## Hardware del lado de estación

### PLC Siemens S7-1214C

<p align="center">
  <img src="/assets/images/hardware/plc-wiring-mandojana.png" alt="Esquema de cableado del PLC" width="900"/>
</p>
<p align="center"><em>Ejemplo de cableado del PLC y disposición de módulos utilizados en la configuración de la estación de Mandojana.</em></p>

El Siemens S7-1214C actúa como el principal dispositivo industrial de adquisición y control en cada estación de bombeo.

Su papel dentro del proyecto es:

* Leer señales digitales y analógicas de proceso
* Organizar las variables monitorizadas en una estructura interna coherente
* Proporcionar una interfaz industrial robusta entre la instrumentación de campo y la capa de telemetría
* Permitir futuras ampliaciones mediante programación adicional y configuración modular

Fue una elección adecuada por varias razones:

* Es hardware de grado industrial, apropiado para entornos de infraestructura hidráulica
* Permite integrarse con lógica de control ya existente
* Ofrece capacidad local suficiente para un tratamiento estructurado de señales
* Puede programarse y configurarse mediante un flujo de ingeniería estándar utilizando TIA Portal

En términos prácticos, el PLC es el componente que transforma condiciones operativas brutas en datos estructurados listos para su transmisión.

### Programación del PLC y entorno de ingeniería

El PLC se programa a través de su interfaz Ethernet utilizando **TIA Portal V16**.

Desde la perspectiva de portfolio, esto es relevante porque demuestra que el proyecto no trató solo de comunicaciones. También implicó trabajar con un entorno estándar de ingeniería de automatización industrial para:

* Configuración del PLC
* Ajuste de parámetros de comunicación
* Diseño de bloques de datos
* Mapeo de variables
* Pruebas de integración

---

## Hardware de comunicación serie

### Módulo de comunicación CM 1241

El CM 1241 amplía el PLC con capacidad de comunicación serie y habilita la interfaz RS-422/485 requerida por el proyecto.

En esta solución, su objetivo principal es proporcionar el puente físico y compatible a nivel de protocolo entre el PLC y el convertidor Dragino RS485-LN.

Este módulo es importante porque el proyecto no evita las comunicaciones industriales. En su lugar, utiliza primero una capa serie industrial convencional antes de pasar a la transmisión inalámbrica IoT.

Esa decisión de diseño mejora el realismo y la compatibilidad en un entorno industrial.

### Por qué RS-485 era relevante

RS-485 se utiliza ampliamente en entornos industriales porque es una solución simple, consolidada y fiable para comunicación serie a distancias prácticas dentro de planta.

En este proyecto, RS-485 era adecuada porque:

* Encaja bien con la comunicación Modbus RTU
* Es habitual en integraciones de control industrial
* Permite una conexión simple y robusta entre el PLC y el convertidor de telemetría
* Evita complejidad arquitectónica innecesaria a nivel de estación

---

## Hardware de conversión de telemetría

### Dragino RS485-LN

<p align="center">
  <img src="/assets/images/hardware/dragino-installation.png" alt="Instalación del Dragino" width="500"/>
</p>
<p align="center"><em>Instalación hardware y enfoque de conexión RS-485 del módulo Dragino RS485-LN.</em></p>

El **Dragino RS485-LN** es uno de los componentes más importantes del proyecto porque realiza la conversión desde comunicación serie industrial hacia telemetría LoRaWAN.

Desde un punto de vista funcional, actúa como el dispositivo frontera entre el mundo OT y el mundo LPWAN/IoT.

Su función incluye:

* Consultar el PLC a través de RS-485 / Modbus RTU
* Recibir los valores solicitados desde el PLC
* Construir el payload que va a transmitirse
* Enviar los datos resultantes mediante LoRaWAN
* Recibir comandos downlink cuando sea necesario

En la relación de comunicación local, el Dragino actúa como **maestro Modbus**, mientras que el PLC se comporta como **esclavo**.

Este es un detalle de ingeniería muy relevante porque define cómo se inicia el intercambio de datos y cómo funciona la lógica de sondeo a nivel de estación.

### Por qué el Dragino RS485-LN encajaba bien

Este dispositivo fue especialmente útil en el proyecto porque permite integrar dispositivos RS-485 existentes dentro de una arquitectura LoRaWAN sin rediseñar desde cero la capa de control.

Eso aporta varias ventajas a la solución:

* Reutilización de hardware industrial ya presente o ya adecuado para el proceso
* Menor esfuerzo de integración que sustituir el PLC por un controlador IoT nativo
* Separación más limpia entre lógica de control y transporte de telemetría
* Mayor facilidad para escalar mediante un patrón repetible del lado de estación

### Enfoque de configuración

El RS485-LN puede configurarse mediante **comandos AT**, normalmente conectándolo a un PC, y también puede recibir instrucciones remotas mediante **comandos downlink** una vez conectado al entorno LoRaWAN.

Esta doble vía de configuración es valiosa porque permite tanto puesta en marcha local como ajustes remotos en operación.

---

## Hardware de gateway y conectividad central

### The Things Indoor Gateway (TTIG)

El gateway utilizado en el proyecto es el **The Things Indoor Gateway (TTIG)**.

Su papel es:

* Recibir mensajes de radio LoRa desde los dispositivos Dragino
* Convertir esos mensajes de radio en tráfico de red
* Reenviar los datos a The Things Network (TTN) mediante conectividad a internet

Esto convierte al TTIG en el puente radio-red del sistema.

### Por qué el TTIG era adecuado

El TTIG fue una elección práctica para el piloto porque es relativamente sencillo de configurar y encaja bien con una arquitectura LoRaWAN basada en TTN.

Aun así, también conviene ser precisos respecto a sus limitaciones:

* Está muy vinculado al ecosistema TTN
* Resulta cómodo para despliegues piloto y prototipos
* Es menos flexible que otras alternativas de gateway más abiertas o más industriales si la arquitectura migra en el futuro a una estrategia de servidor de red privado distinta

Mencionar esto en un portfolio es útil porque demuestra criterio realista de sistema en lugar de presentar cada componente como universalmente óptimo.

---

## Hardware de soporte al procesamiento central

### Orange Pi 3 LTS

La **Orange Pi 3 LTS** se utiliza como plataforma de computación de soporte para mantener Node-RED funcionando de forma continua en el entorno central.

Su papel es sencillo, pero operativamente importante:

* Alojar el entorno de procesamiento y visualización
* Proporcionar un runtime persistente para la recepción MQTT y la lógica de dashboards
* Mantener disponible la capa central de monitorización sin depender de una estación de trabajo generalista de ingeniería

Esto es importante porque la solución no es solo un concepto de integración cloud. También incluye un entorno de ejecución dedicado para la monitorización operativa.

---

## Consideraciones de alimentación e integración física

La configuración de PLC y comunicaciones del lado de estación se alimenta mediante **fuentes industriales de 24 V**, algo coherente con instalaciones típicas de control industrial.

Desde el punto de vista del portfolio, es útil mencionarlo porque refuerza que el diseño estaba basado en una práctica hardware desplegable y no solo en simulación software.

En algunas estaciones también puede requerirse capacidad adicional de E/S dependiendo del número de señales monitorizadas. Esto introduce una consideración importante de diseño:

* La arquitectura de comunicaciones debe escalar no solo entre estaciones
* También debe escalar dentro de cada estación en función del número local de señales y de la mezcla de instrumentación

---

## Visión general de la pila de comunicaciones

<p align="center">
  <img src="/assets/images/hardware/lorawan-elements-project-context.png" alt="Elementos LoRaWAN" width="500"/>
</p>
<p align="center"><em>Relación entre dispositivos finales, gateway, servidor de red y capa de aplicación dentro del contexto del proyecto.</em></p>

La pila de comunicaciones del proyecto puede resumirse así:

**Señales de campo → E/S del PLC → CM 1241 / RS-485 → Modbus RTU → Dragino RS485-LN → LoRa / LoRaWAN → TTIG → TTN → MQTT → Node-RED → ThingSpeak**

Cada capa resuelve un problema distinto:

* **PLC y E/S** resuelven la adquisición
* **RS-485 y Modbus RTU** resuelven la comunicación local entre dispositivos
* **LoRa / LoRaWAN** resuelven el transporte inalámbrico de largo alcance
* **TTN** resuelve la gestión de dispositivos y paquetes a nivel de red
* **MQTT** resuelve la entrega de mensajes hacia el lado de aplicación
* **Node-RED y ThingSpeak** resuelven la monitorización, el almacenamiento y el análisis orientado al operador

---

## Protocolos de comunicación y su papel

### Modbus RTU

**Modbus RTU** es el protocolo local del lado de estación utilizado entre el dispositivo Dragino y el PLC.

Por qué encaja en el proyecto:

* Es un protocolo industrial estándar y ampliamente conocido
* Funciona de forma natural sobre RS-485
* Es adecuado para sondeo maestro-esclavo de registros estructurados
* Permite un intercambio fiable de datos de proceso en contextos industriales

Qué aporta:

* Estructura determinista de petición/respuesta
* Adquisición de datos sencilla basada en registros
* Compatibilidad con diseños industriales centrados en PLC

Su principal limitación en el contexto de este proyecto es que se trata de un protocolo serie tradicional con seguridad nativa limitada y una velocidad de comunicación modesta frente a alternativas Ethernet más modernas. Aun así, para el sondeo de telemetría del lado de estación es una elección apropiada y realista.

### LoRa

**LoRa** es la tecnología inalámbrica física de largo alcance utilizada para transportar los datos de estación hasta el gateway.

Por qué se eligió:

* Gran alcance de cobertura
* Bajo coste operativo
* Características de bajo consumo
* Buen encaje con infraestructuras distribuidas geográficamente
* Adecuación para un piloto que puede ampliarse después a varias estaciones

En este proyecto, LoRa no es solo un enlace inalámbrico genérico. Es la tecnología habilitadora que hace viable la cobertura remota de estaciones de bombeo sin necesidad de una infraestructura de comunicaciones costosa.

### LoRaWAN

**LoRaWAN** es el protocolo de capa de red utilizado por encima de LoRa.

Por qué importa:

* Proporciona registro de dispositivos y organización de red
* Soporta arquitecturas escalables con múltiples dispositivos
* Separa dispositivos finales, gateways, gestión de red y aplicaciones
* Es mucho más apropiado que enlaces LoRa directos ad hoc para supervisión centralizada

El proyecto adopta específicamente una **arquitectura LoRaWAN con un servidor de red independiente**, lo que mejora la gestión centralizada y la escalabilidad.

### MQTT

**MQTT** se utiliza después de TTN para mover la telemetría hacia el lado de aplicación de la solución.

Por qué se eligió:

* Modelo ligero de publicación/suscripción
* Adecuado para distribución de telemetría IoT
* Eficiente para integración basada en mensajes
* Fácil de conectar con flujos y dashboards de Node-RED

En la práctica, MQTT es lo que hace que la transición entre el entorno LoRaWAN y el entorno de procesamiento y visualización sea limpia y flexible.

---

## Relación entre dispositivos y protocolos por capas

Una forma útil de explicar el sistema es asociar cada componente a la capa de comunicación a la que pertenece.

### Capa de adquisición

* E/S del PLC
* Cableado local de señales

### Capa de comunicación industrial

* CM 1241
* RS-485
* Modbus RTU

### Capa de conversión de telemetría

* Dragino RS485-LN

### Capa LPWAN

* LoRa
* LoRaWAN
* Gateway TTIG
* TTN

### Capa de mensajería de aplicación

* MQTT

### Capa de monitorización y analítica

* Node-RED
* ThingSpeak
* Soporte de runtime sobre Orange Pi

Este enfoque por capas suele ser más claro que describir el sistema como una única lista de dispositivos.

---

## Por qué esta pila de comunicaciones fue una buena elección de ingeniería

La combinación de comunicación serie industrial y telemetría LPWAN encajaba bien con el problema porque equilibra:

* **Compatibilidad industrial** en el lado de estación
* **Transmisión de largo alcance y bajo coste** entre activos remotos
* **Supervisión centralizada** a nivel de sistema
* **Repetibilidad** en múltiples estaciones de bombeo
* **Capacidad de ampliación** para futuros despliegues

Es especialmente valiosa como ejemplo de portfolio porque demuestra que el diseño no dependió de una única tecnología de moda. En su lugar, combinó comunicación industrial madura y redes IoT modernas de una forma coherente.

---

## Principales fortalezas prácticas

Las decisiones más sólidas de hardware y comunicaciones del proyecto son:

* Uso de PLCs industriales en lugar de hardware edge frágil o de tipo hobby en el lado de estación
* Un punto de conversión limpio entre Modbus/RS-485 y LoRaWAN
* Una arquitectura de red adecuada para más de un activo remoto
* Entrega eficiente de mensajes hacia Node-RED mediante MQTT
* Una plataforma central de runtime que mantiene los dashboards disponibles de forma continua

---

## Principales limitaciones prácticas

Un portfolio técnico creíble también debe reconocer limitaciones.

Entre las limitaciones relevantes están:

* Modbus RTU es simple y fiable, pero limitado en velocidad y seguridad nativa
* El TTIG es cómodo para pilotos con TTN, pero menos flexible para una futura migración a red privada
* La solución está diseñada para monitorización y visibilidad supervisora, no para control rápido en lazo cerrado
* La dependencia de red pública a nivel de servidor de red puede no ser ideal para un entorno productivo endurecido
* El endurecimiento de seguridad puede ampliarse de forma significativa en futuras iteraciones

## Conclusión

El diseño hardware y de comunicaciones de este proyecto se entiende mejor como un puente práctico entre automatización industrial y telemetría remota.

La capa del lado de estación utiliza hardware de control industrial fiable. La capa de comunicación utiliza protocolos serie industriales estándar. La capa de telemetría convierte esos datos en tráfico LoRaWAN. La capa central transforma los mensajes recibidos en dashboards y telemetría almacenada.

Esa combinación es la que da valor técnico al proyecto: no es solo un concepto de monitorización, sino una cadena completa y desplegable desde las señales de campo hasta la visibilidad centralizada.

---

## Qué viene después

Una vez entendida la pila hardware y de comunicaciones, el siguiente paso es describir cómo se mueve realmente la información a través del sistema.

Continúa con: [`data-flow.md`](data-flow.md)

---

## Navegación


* Volver al [índice de documentación en castellano](README.md)
* Volver a [Arquitectura](architecture.md)
* Cambiar a la [versión en inglés](../en/hardware-and-communications.md)

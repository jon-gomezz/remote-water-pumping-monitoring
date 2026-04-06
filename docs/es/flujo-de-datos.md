# Flujo de datos

## Propósito de este documento

Este documento explica cómo se mueve la información a través del sistema, desde las señales de campo dentro de las estaciones de bombeo hasta los dashboards finales y la capa de almacenamiento en la nube.

En un proyecto de portfolio como este, el flujo de datos es una de las partes más importantes que conviene explicar con claridad. No solo muestra qué dispositivos se utilizaron, sino también cómo las señales industriales en bruto se transformaron en información operativa útil.

---

## Resumen de alto nivel del flujo de datos

<p align="center">
  <img src="/assets/images/data-flow/end-to-end-data-flow.png" alt="Flujo de datos de extremo a extremo" width="500"/>
</p>
<p align="center"><em>Flujo de datos de extremo a extremo, desde la adquisición en campo hasta la visualización y el almacenamiento centralizados.</em></p>

A alto nivel, el proyecto sigue esta lógica:

**señales de campo → bloques de datos del PLC → sondeo Modbus RTU → generación de payload en Dragino → uplink LoRaWAN → recepción en TTN → decodificación del payload → entrega por MQTT → enrutado en Node-RED → almacenamiento y análisis en ThingSpeak**

Esta cadena convierte variables dispersas de distintas estaciones en un flujo de monitorización centralizado que puede visualizarse y analizarse de forma remota.

## Qué debe conseguir este flujo

La canalización de datos se diseñó para responder a una necesidad práctica de monitorización, no como un simple ejercicio teórico de comunicaciones.

El flujo debía:

* Recoger variables digitales y analógicas de estaciones remotas
* Mantener una estructura interna coherente entre estaciones
* Transmitir solo la información útil a través de un enlace inalámbrico de largo alcance con limitaciones
* Transformar el payload recibido en valores de proceso legibles
* Enrutar los datos hacia dashboards para los operadores
* Almacenar la información para análisis posterior y gestión de alarmas
* Dar soporte a más de una estación mediante un patrón repetible

Por ello, el flujo de datos no trata solo de transmisión. También trata de **estructurar, reducir, decodificar y distribuir la información de forma eficiente**.

---

## Paso 1. Adquisición de señales de campo

El flujo de datos comienza dentro de cada estación de bombeo con la adquisición de variables operativas.

Dependiendo de la estación, estas variables pueden incluir:

* Estado de funcionamiento de bombas
* Estado de fallo de bombas
* Condiciones de nivel de depósito
* Estado de válvulas
* Alarmas de falta de agua
* Presión de red o de aspiración
* Nivel de pozo
* Nivel de depósito
* Caudal
* Valores relacionados con el cloro

Estas son las señales reales del proceso que importan a los operadores. En esta etapa, la información sigue estando ligada directamente al equipo físico y a la instrumentación de campo.

---

## Paso 2. Estructuración de datos en el PLC

Después de la adquisición, el PLC no expone simplemente una colección aleatoria de señales. En su lugar, las variables monitorizadas se organizan en un formato interno coherente.

Esta es una decisión de diseño importante porque facilita mucho el procesamiento posterior.

La estructura general descrita para las estaciones se organiza en **4 palabras**:

### Primeras 2 palabras: entradas digitales

* La **Palabra 1** contiene los estados digitales principales
* La **Palabra 2** queda reservada para futuras ampliaciones

Una asignación típica incluye:

* Bits de bomba 1 en marcha / fallo
* Bits de bomba 2 en marcha / fallo
* Bits de nivel alto / bajo de depósito
* Bits libres para uso futuro

### Siguientes 2 palabras: entradas analógicas

* La **Palabra 3** contiene el primer valor analógico de interés
* La **Palabra 4** queda reservada o se utiliza para futuras ampliaciones analógicas según la estación

Esta estructura proporciona al sistema un modelo interno uniforme, incluso cuando las señales reales de cada estación son diferentes.

---

## Por qué importa el formato de datos del PLC

Una buena explicación de portfolio debe dejar este punto claro:

El proyecto no se limita a transmitir datos. Primero **estandariza** los datos.

Esa estandarización aporta varias ventajas:

* Acceso Modbus más sencillo
* Selección de bytes más fácil para crear el payload
* Lógica de decodificación más predecible en TTN
* Diseño de flujos más simple en Node-RED
* Mayor escalabilidad al añadir nuevas estaciones

Sin este formato estructurado en el lado del PLC, el resto de la cadena de telemetría sería mucho más difícil de mantener.

---

## Ejemplo de mapeo de señales a nivel de estación

Aunque el proyecto utiliza una estructura compartida, cada estación tiene su propio mapeo práctico de señales.

Un ejemplo claro es **Mandojana**, donde los valores monitorizados incluyen:

### Señales digitales

* Bomba en marcha
* Alarma de falta de agua
* Válvula cerrada
* Válvula abierta

### Señales analógicas

* Nivel de pozo
* Nivel de depósito
* Caudal
* Cloro

Otras estaciones utilizan combinaciones más simples, normalmente centradas en el estado de bombas y la presión, mientras que algunas también incluyen señales de nivel de depósito.

Esto significa que el flujo está estandarizado a nivel de estructura, pero sigue siendo lo bastante flexible como para representar realidades distintas en cada estación.

---

## Paso 3. Sondeo Modbus RTU entre Dragino y PLC

Una vez organizados los datos del PLC, el Dragino RS485-LN los recupera mediante **Modbus RTU** sobre **RS-485**.

En esta etapa, el flujo local trabaja con un modelo de comunicación maestro-esclavo:

* el **Dragino RS485-LN** actúa como maestro
* el **PLC** actúa como esclavo

Esto significa que el intercambio de datos lo inicia el Dragino. Envía peticiones predefinidas al PLC solicitando los registros relevantes, y el PLC devuelve los valores solicitados.

En términos prácticos, esta etapa convierte el modelo de memoria del PLC en una respuesta serie que después puede transformarse en un payload inalámbrico.

### Qué ocurre en esta etapa

1. El Dragino envía una petición Modbus.
2. El PLC devuelve los valores de los registros solicitados.
3. El Dragino recibe la respuesta serie.
4. Se seleccionan los bytes útiles para la transmisión.

Este es el primer punto en el que la información bruta de la estación se convierte en una candidata explícita a mensaje de telemetría.

---

## Paso 4. Preparación del payload en el Dragino RS485-LN

El Dragino hace más que reenviar datos serie de forma ciega.

Una parte clave del flujo de datos es que el Dragino puede configurarse para:

* Enviar comandos Modbus predefinidos
* Procesar la respuesta recibida
* Seleccionar solo los bytes relevantes
* Construir el payload que se enviará al servidor LoRaWAN

Este es un paso de eficiencia muy importante.

En lugar de enviar bloques grandes con contenido vacío o irrelevante, el proyecto recorta la información para transmitir solo los bytes útiles. Eso reduce el tamaño del payload y simplifica la decodificación posterior.

Esta etapa es especialmente importante porque LoRaWAN no está pensada para mensajes grandes y verbosos. Diseñar payloads compactos forma parte de una buena solución de ingeniería.

---

## Lógica de diseño del payload

El diseño del payload sigue un principio práctico:

**mantener solo la información que realmente va a utilizarse aguas abajo**.

Eso significa que:

* Los estados digitales se empaquetan de forma eficiente en bytes o palabras
* Los valores analógicos se colocan en posiciones conocidas
* Los bytes no utilizados se excluyen cuando es posible
* Las posiciones de los bytes pueden variar según la configuración de cada estación

Este último punto es muy importante: la lógica de decodificación en TTN depende de cómo se haya definido el payload de Dragino para cada estación.

Por eso, el flujo completo está estrechamente conectado entre capas:

* Estructura interna del PLC
* Patrón de lectura Modbus
* Selección de payload en Dragino
* Lógica de decodificación en TTN
* Lógica de extracción en Node-RED

El proyecto funciona bien porque estas etapas se diseñaron de forma conjunta y no por separado.

## Paso 5. Uplink LoRaWAN hacia TTN

Una vez creado el payload, el Dragino lo transmite mediante **LoRaWAN**.

La ruta inalámbrica de los datos es:

* El Dragino RS485-LN envía el uplink
* El gateway TTIG recibe el mensaje por radio
* TTN recibe el paquete reenviado
* TTN gestiona el enrutado a nivel de red y la administración de dispositivos

En este punto, el payload ya ha alcanzado la capa de red, pero todavía no está en el formato más útil para dashboards o análisis.

Aquí empieza el siguiente paso importante: **la decodificación del payload**.

---

## Paso 6. Decodificación del payload en TTN

En TTN, el uplink recibido contiene inicialmente el payload en una forma orientada a máquina.

Para que los datos sean más fáciles de utilizar, el proyecto incluye una etapa de decodificación mediante JavaScript en TTN.

La lógica de esta etapa consiste en:

* Leer los bytes brutos entrantes
* Interpretarlos según el formato de payload de la estación
* Reconstruir valores digitales y analógicos
* Devolver el resultado decodificado en un objeto más legible

Esta es una de las partes más valiosas del flujo de datos porque evita trasladar la interpretación de bytes de bajo nivel a las capas posteriores.

### Por qué fue buena idea decodificar en TTN

Al decodificar en la etapa TTN:

* Node-RED recibe datos más limpios
* La lógica aguas abajo se simplifica
* Los dashboards pueden construirse más rápido
* El payload resulta más interpretable y mantenible

Esto reduce la complejidad del resto del sistema.

### Ejemplo de lógica de decodificación

Un decodificador típico:

* Combina bytes para formar palabras
* Extrae los bits digitales activos
* Interpreta las palabras analógicas
* Escala los valores analógicos a magnitudes de ingeniería cuando es necesario
* Devuelve el resultado como un objeto de payload decodificado

<p align="center">
  <img src="/assets/images/data-flow/ttn-payload-decoder.png" alt="Decodificador de payload en TTN" width="500"/>
</p>
<p align="center"><em>Lógica de decodificación del payload en TTN utilizada para transformar bytes brutos en variables legibles.</em></p>

Ese payload decodificado pasa a ser la entrada principal para el procesamiento en Node-RED.

## Paso 7. Entrega por MQTT a Node-RED

Después de la decodificación, TTN reenvía el mensaje mediante **MQTT**.

En esta etapa, el sistema pasa de la capa de red a la capa de procesamiento de aplicaciones.

Node-RED se suscribe al topic MQTT asociado a cada dispositivo de TTN y recibe los mensajes uplink.

Aquí es donde los datos pasan a formar parte del entorno de supervisión orientado al operador.

### Por qué MQTT encaja bien aquí

MQTT es una buena elección para este proyecto porque:

* Es ligero
* Soporta comunicación publish/subscribe
* Se integra de forma natural con Node-RED
* Encaja bien en canalizaciones de telemetría con varios dispositivos

Actúa como un mecanismo limpio de entrega entre TTN y la lógica de dashboards.

### Ejemplo de estructura de topic

Un topic típico de uplink de TTN utilizado en el proyecto sigue este patrón:

`v3/{application-id}@ttn/devices/{device-id}/up`

Los valores exactos dependen de la aplicación y del registro del dispositivo en TTN, pero la idea importante es que cada mensaje entrante puede asociarse con el dispositivo correcto de la estación.

---

## Paso 8. Extracción y enrutado de datos en Node-RED

Una vez que el mensaje MQTT llega a Node-RED, el flujo de datos entra en su fase principal de procesamiento y presentación.

Node-RED recibe el mensaje, extrae el **payload decodificado** y enruta cada variable hacia el elemento correspondiente del dashboard.

Esto incluye enviar valores a:

* Indicadores de estado
* LEDs
* Medidores
* Gráficas
* Widgets de texto
* Vistas específicas por estación

En este punto, el sistema ya no está gestionando transporte. Está gestionando **visibilidad para el operador**.

### Qué hace Node-RED en la práctica

* Recibe el mensaje uplink por MQTT
* Accede al objeto de payload decodificado
* Extrae los valores de interés
* Envía cada valor a la salida correcta
* Actualiza el dashboard de la estación casi en tiempo real

<p align="center">
  <img src="/assets/images/data-flow/node-red-routing-flow.png" alt="Flujo de enrutado en Node-RED" width="500"/>
</p>
<p align="center"><em>Flujo de Node-RED utilizado para extraer valores del payload decodificado y enviarlos a los widgets del dashboard.</em></p>

Esta etapa es la que convierte la telemetría entrante en supervisión útil.

---

## Por qué Node-RED se convirtió en una pieza central del flujo

Un punto especialmente relevante en este proyecto es que la estrategia de recepción de datos evolucionó.

En una fase anterior, se exploró el uso de MQTT y deserialización más cerca del lado del PLC. Más adelante, debido a dificultades prácticas relacionadas con la disponibilidad de Ethernet en Araka, el proyecto pasó a una estrategia basada en Node-RED para la recepción y el procesamiento.

Eso convierte a Node-RED en algo más que una herramienta de visualización.

Se convierte en:

* El suscriptor MQTT
* El procesador del payload decodificado
* La capa de enrutado
* La capa de dashboards
* El punto de entrada operativo para la monitorización centralizada

Esto es una buena historia de ingeniería para portfolio porque demuestra adaptación a restricciones reales de despliegue.

## Paso 9. Visualización en dashboards

Después del enrutado, las variables aparecen en dashboards diseñados para la monitorización en tiempo real de cada estación de bombeo.

Estos dashboards pueden mostrar:

* Estado de bombas
* Alarmas
* Posición de válvulas
* Tendencias de nivel
* Tendencias de caudal
* Indicadores de presión
* Valores relacionados con el cloro o la calidad del agua

Esta etapa es esencial porque cierra el ciclo entre la telemetría técnica y la toma de decisiones operativas.

Sin esta capa, el sistema seguiría transmitiendo datos, pero no apoyaría de forma realmente práctica una supervisión centralizada.

---

## Paso 10. Publicación en ThingSpeak

La parte final del flujo envía los datos procesados desde Node-RED a **ThingSpeak**.

Esto crea una segunda vía de consumo para la misma telemetría.

### Node-RED aporta

* Monitorización inmediata
* Dashboards de estación
* Supervisión orientada al operador

### ThingSpeak aporta

* Almacenamiento histórico
* Visualización en la nube
* Gráficas y tendencias
* Análisis adicional
* Posibilidades de alarma y notificación

Esta es una fortaleza arquitectónica importante porque el proyecto no depende de una única plataforma final. Separa la visualización operativa a corto plazo del almacenamiento y análisis a más largo plazo.

## Comportamiento de refresco y ritmo de monitorización

El proyecto se diseñó en torno a **intervalos de refresco intermedios**, aproximadamente en el rango de **3 a 5 minutos**.

Esto tiene implicaciones importantes para el flujo de datos:

* El sistema es adecuado para monitorización y supervisión
* Permite detectar incidencias operativas con suficiente rapidez
* No está pensado para control en lazo cerrado de alta velocidad
* La pila de comunicaciones y procesamiento está alineada con necesidades supervisoras y no con automatización subsegundo

Este tipo de claridad de ingeniería mejora mucho un proyecto de portfolio.

---

## Patrón de flujo de datos multiestación

El sistema se concibió para dar soporte a varias estaciones, no solo a una.

Eso significa que el flujo de datos completo es repetible:

1. Cada estación estructura los datos localmente en el PLC
2. Cada estación expone valores mediante Modbus RTU
3. Cada estación transmite a través de un nodo Dragino
4. TTN recibe mensajes de varios dispositivos
5. Node-RED identifica y enruta los datos entrantes de cada estación
6. Los dashboards y canales de almacenamiento se actualizan en consecuencia

Esta repetibilidad es uno de los aspectos más fuertes del proyecto, porque muestra un patrón de telemetría escalable en lugar de un prototipo puntual.

---

## Por qué este flujo de datos es sólido desde el punto de vista de ingeniería

El flujo de datos es un buen diseño porque equilibra varias restricciones al mismo tiempo:

* Compatibilidad industrial en la capa de adquisición
* Transmisión compacta del payload sobre LoRaWAN
* Decodificación legible antes del procesamiento en dashboards
* Entrega centralizada basada en MQTT
* Visualización orientada al operador en Node-RED
* Análisis histórico en ThingSpeak

En otras palabras, el flujo es eficiente no porque una sola capa sea compleja, sino porque todas las capas están alineadas.

---

## Principales fortalezas del flujo de datos

Los aspectos más sólidos del flujo son:

* Formato estructurado de datos en el lado del PLC
* Selección eficiente de los bytes útiles antes de la transmisión
* Lógica de decodificación desplazada aguas arriba hacia TTN
* Gestión sencilla aguas abajo en Node-RED
* Separación entre dashboards en tiempo real y almacenamiento histórico
* Patrón repetible para varias estaciones de bombeo

---

## Principales limitaciones y compromisos

Un documento de portfolio realista también debe reconocer las limitaciones del flujo.

Entre los compromisos relevantes están:

* La estructura del payload puede variar entre estaciones, lo que aumenta el esfuerzo de mantenimiento del decodificador
* Las limitaciones de tamaño de payload en LoRaWAN obligan a seleccionar con cuidado los bytes transmitidos
* El flujo está pensado para supervisión, no para acciones de control rápidas
* El uso inicial de TTN introduce dependencia de un servicio de red externo
* El endurecimiento de seguridad puede ampliarse en futuras iteraciones, especialmente en torno al uso de redes públicas y a las protecciones de capa de aplicación

Estas limitaciones no debilitan el proyecto. Lo hacen más creíble.

## Conclusión

El flujo de datos de este proyecto se entiende mejor como una canalización de transformación por etapas.

El sistema no se limita a transmitir valores de sensores de un lugar a otro. Hace lo siguiente:

* Adquiere información del proceso
* La estandariza dentro del PLC
* La lee mediante Modbus RTU
* La comprime en un payload útil
* La transmite sobre LoRaWAN
* La decodifica en TTN
* La enruta por MQTT hacia Node-RED
* La visualiza para los operadores
* La almacena en ThingSpeak para análisis posterior

Esa lógica de extremo a extremo es una de las principales razones por las que este proyecto funciona bien como ejemplo técnico de portfolio.

---

## Qué viene después

Una vez entendido cómo se mueve la información a través del sistema, el siguiente paso lógico es documentar cómo se probó el piloto y qué resultados se observaron.

Continúa con: [`validation-and-results.md`](validation-and-results.md)

---

## Navegación

* Volver al [índice de documentación en castellano](README.md)
* Volver a [Hardware y comunicaciones](hardware-and-communications.md)
* Cambiar a la [versión en inglés](../en/data-flow.md)

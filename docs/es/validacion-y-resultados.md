# Validación y resultados

## Propósito de este documento

Este documento explica cómo se validó la solución y qué resultados se observaron durante la fase piloto.

En esta versión orientada a portfolio del proyecto, el objetivo no es presentar la validación como un benchmark formal de laboratorio con KPIs numéricos exhaustivos. En su lugar, se busca mostrar cómo se fue probando progresivamente la arquitectura, qué capacidades técnicas se demostraron y por qué el piloto puede considerarse exitoso desde una perspectiva de ingeniería.

---

## Filosofía de validación

El sistema se validó como un **piloto industrial funcional**, no como un despliegue completo a escala de producción.

Esa distinción es importante.

La pregunta clave no era si todas las futuras estaciones ya estaban desplegadas, sino si la arquitectura propuesta había demostrado ser técnicamente válida y operativamente útil en un contexto real de monitorización.

Por ello, la validación se centró en demostrar que la siguiente cadena funcionaba con la fiabilidad suficiente para el caso de uso previsto:

* Adquisición de señales de campo
* Estructuración de datos en el PLC
* Comunicación Modbus con el convertidor de telemetría
* Transmisión LoRaWAN hacia TTN
* Decodificación del payload y entrega del mensaje
* Monitorización con dashboards Node-RED en Araka
* Almacenamiento y análisis con ThingSpeak

<p align="center">
  <img src="/assets/images/validation/pilot-validation-scope.png" alt="Alcance de validación del piloto" width="500"/>
</p>
<p align="center"><em>Alcance de validación del piloto, centrado en la estación de Mandojana y en el entorno central de supervisión en Araka.</em></p>

## Contexto de validación del piloto

Aunque la arquitectura se concibió para una red más amplia de estaciones de bombeo, la validación implementada se llevó a cabo como piloto.

El piloto se centró en:

* **Mandojana** como caso de implementación del lado de estación
* **Araka** como punto central de recepción y supervisión

Este es uno de los aspectos más importantes que conviene comunicar con claridad en el portfolio.

No debe presentarse el proyecto como si todas las estaciones previstas ya se hubieran desplegado con el mismo nivel de madurez. El logro real es que se implementó y validó el patrón completo de monitorización sobre una configuración piloto representativa, demostrando que la arquitectura es funcional y ampliable.

---

## Qué se validó realmente

La validación no dependió de una única prueba final. Se construyó como una secuencia progresiva de comprobaciones a lo largo de toda la cadena de telemetría.

Los principales objetivos de validación fueron:

### 1. Validez de la adquisición en estación

El PLC debía adquirir y estructurar correctamente las variables monitorizadas de la estación.

### 2. Validez de la comunicación serie

El enlace RS-485 / Modbus RTU entre el PLC y el Dragino RS485-LN debía devolver los valores esperados.

### 3. Validez de la transmisión inalámbrica

El payload generado por Dragino debía llegar a TTN a través de la ruta LoRaWAN.

### 4. Validez de la decodificación

El payload recibido debía interpretarse correctamente para que la capa central de monitorización trabajara con variables significativas y no con bytes en bruto.

### 5. Validez del dashboard

Node-RED debía mostrar el estado de la estación de forma útil para la supervisión remota.

### 6. Validez del almacenamiento y análisis

ThingSpeak debía recibir y almacenar la telemetría procesada para permitir revisión histórica y futuras extensiones orientadas a alarmas.

### 7. Utilidad operativa

El sistema global debía demostrar que podía centralizar la supervisión y reducir la dependencia de una visibilidad exclusivamente local.

---

## Secuencia de validación

Una buena forma de entender los resultados es describir la validación por etapas.

### Etapa 1. Validación de conectividad y de la capa de red

Antes de la integración completa en campo, el proyecto validó la ruta Dragino → TTN.

Esto confirmó que:

* El dispositivo podía registrarse correctamente
* La comunicación uplink podía establecerse
* El enfoque LoRaWAN elegido era viable para el proyecto

### Etapa 2. Pruebas de telemetría aisladas

Antes de la integración final con el PLC se utilizaron pruebas con generador de señal.

Esto fue útil porque permitió aislar problemas y verificar que la cadena de transmisión se comportaba correctamente antes de añadir la complejidad de la adquisición completa de datos industriales.

Esta etapa confirmó que:

* El enlace de telemetría podía transportar las señales generadas
* Los problemas iniciales de configuración podían identificarse en un entorno controlado
* El proyecto podía avanzar hacia pruebas integradas con mayor confianza

### Etapa 3. Validación del prototipo PLC + Dragino

Una vez que las pruebas aisladas fueron satisfactorias, se probó el conjunto PLC + Dragino.

Esta etapa confirmó que:

* El PLC podía exponer la estructura de datos requerida
* El Dragino podía consultar el PLC mediante Modbus RTU
* El payload podía construirse a partir de los valores esperados de los registros
* La transmisión integrada desde el lado de estación era viable

### Etapa 4. Validación de la recepción en el lado central

Después se validó el lado de recepción a través de MQTT, la decodificación en TTN y, más adelante, la recepción basada en Node-RED.

Esta etapa confirmó que:

* El payload podía decodificarse correctamente
* Los valores podían enrutarse hacia la capa central de supervisión
* El cambio práctico hacia Node-RED como principal entorno de recepción era viable

### Etapa 5. Validación de la operación piloto

Por último, se probó la cadena completa desde estación hasta el centro dentro del contexto piloto entre Mandojana y Araka.

Este fue el paso de validación más importante porque demostró que la solución funcionaba como un sistema de monitorización de extremo a extremo y no como un conjunto desconectado de subsistemas.

---

## Criterios principales de validación

Desde un punto de vista de ingeniería, el piloto puede evaluarse mediante un conjunto de criterios prácticos.

### Funcionalidad de extremo a extremo

El criterio más importante era si la cadena completa de telemetría funcionaba desde la señal de campo hasta el dashboard.

### Interpretabilidad de los datos

No bastaba con que llegaran bytes. Las variables finales debían ser legibles y significativas para los operadores.

### Adecuación para monitorización

La solución debía soportar supervisión remota con intervalos de refresco apropiados para la monitorización operativa.

### Repetibilidad

La arquitectura debía ser apta para reutilizarse en estaciones adicionales.

### Calidad de integración

Las distintas capas debían trabajar juntas sin requerir intervención manual poco realista en cada paso.

Según estos criterios, el piloto puede considerarse exitoso.

---

## Resultados observados

## 1. La arquitectura demostró ser funcionalmente válida

El resultado global más sólido es que la arquitectura propuesta se validó como **totalmente funcional en condiciones piloto**.

El proyecto demostró que era posible:

* Adquirir datos de estación mediante hardware industrial basado en PLC
* Transmitir esos datos a través de una ruta de telemetría basada en LoRaWAN
* Centralizar la información entrante en Araka
* Visualizar el estado de la estación mediante dashboards Node-RED
* Ampliar la solución con ThingSpeak para almacenamiento y análisis

Esto es importante porque el proyecto no se quedó en el nivel de diseño. La arquitectura se puso realmente a prueba en un contexto operativo realista.

---

## 2. LoRa / LoRaWAN se confirmó como una elección de comunicación adecuada

Una de las principales hipótesis de diseño del proyecto era que una comunicación inalámbrica de largo alcance y bajo coste sería adecuada para estaciones de bombeo distribuidas geográficamente.

Los resultados del piloto respaldan esa decisión.

La cadena de comunicación a través de Dragino, TTIG y TTN demostró que el enfoque LoRaWAN seleccionado era viable para este caso de uso y estaba alineado con los requisitos de diseño originales de:

* Cobertura de largo alcance
* Bajo coste operativo
* Soporte para múltiples activos remotos
* Posibilidad de ampliación más allá del piloto

Este es un resultado importante porque valida no solo la implementación, sino también la decisión arquitectónica previa.

---

## 3. Node-RED demostró ser eficaz como capa operativa de monitorización

Otro resultado importante fue el uso exitoso de Node-RED como entorno central de monitorización.

Esto cobró especial importancia después de que el proyecto tuviera que adaptarse a la limitación práctica de no poder apoyarse en una ruta de recepción más directa basada en Ethernet desde el PLC en Araka.

La capa Node-RED demostró que podía:

* Recibir telemetría mediante MQTT
* Enrutar correctamente los valores decodificados
* Proporcionar dashboards en tiempo real
* Ofrecer visibilidad a nivel de estación en un formato que los operadores pueden utilizar con rapidez

<p align="center">
  <img src="/assets/images/validation/node-red-pilot-dashboard.png" alt="Dashboard piloto en Node-RED" width="900"/>
</p>
<p align="center"><em>Dashboard piloto utilizado en Araka para supervisar la telemetría entrante desde la configuración de estación validada.</em></p>

Desde el punto de vista de portfolio, este es uno de los resultados más valiosos, porque muestra que el proyecto supo adaptarse a restricciones reales de infraestructura sin perder funcionalidad.

---

## 4. La integración del hardware industrial se validó con éxito

La combinación de PLC Siemens, CM 1241, Dragino RS485-LN, TTIG y Orange Pi se validó como una pila coherente.

Esto es importante porque el proyecto dependía de que funcionaran conjuntamente varias capas hardware:

* Hardware industrial de adquisición
* Hardware de comunicación serie
* Hardware de telemetría inalámbrica
* Infraestructura de gateway y red
* Hardware central de procesamiento

El piloto mostró que esta integración heterogénea era viable y operativamente útil.

---

## 5. El piloto demostró visibilidad supervisora casi en tiempo real

Los resultados indican que la solución soporta **monitorización supervisora remota** con intervalos de actualización alineados con el ritmo previsto de aproximadamente **3 a 5 minutos**.

Este matiz es importante.

El proyecto no es un sistema de control rápido, y no debe presentarse como tal. Su éxito reside en permitir supervisión centralizada, mejorar la visibilidad y facilitar una respuesta operativa más rápida en comparación con una monitorización manual descentralizada.

Dentro de ese alcance previsto, el resultado del piloto es positivo.

---

## 6. ThingSpeak aportó valor histórico y analítico

El piloto también demostró que el sistema podía ampliarse más allá de la visibilidad inmediata en dashboards.

Al publicar los valores procesados en ThingSpeak, el proyecto ganó:

* Almacenamiento histórico
* Visualización de tendencias
* Capacidades adicionales de análisis
* Una vía para futuras alarmas y monitorización orientada a eventos

Esto significa que el piloto no se limitó a la visualización instantánea de telemetría. También estableció una base para análisis operativos más profundos.

## Ejemplo de resultado a nivel de estación

Una forma útil de hacer más concretos los resultados es explicar qué permitía ver el piloto en la práctica.

En el contexto de estación validado, los dashboards podían presentar variables como:

* Estado de funcionamiento de bomba
* Alarmas de falta de agua
* Estado de válvulas
* Valores de nivel de depósito y pozo
* Valores relacionados con el caudal
* Valores de proceso relacionados con el cloro

Esto es importante porque muestra que el sistema no se limitaba a transmitir un payload genérico. Estaba exponiendo información de proceso operativamente significativa.

---

## Resultados frente a los requisitos originales de diseño

Los resultados de validación se alinean bien con los principales requisitos de diseño definidos para el proyecto.

### Comunicación de largo alcance y bajo coste

Validado positivamente a través de la ruta piloto LoRaWAN.

### Uso de equipamiento industrial

Validado positivamente mediante una pila de adquisición y comunicación centrada en PLC.

### Supervisión central compatible con SCADA

Validado positivamente mediante dashboards Node-RED en Araka.

### Potencial arquitectónico multiestación

Validado parcialmente: la arquitectura se diseñó para varias estaciones, pero el piloto se validó funcionalmente sobre un subconjunto reducido.

### Tiempos de refresco intermedios

Validados positivamente para monitorización supervisora.

### Capacidad suficiente de señales

Validada positivamente en el caso de estación implementado, incluyendo valores digitales y analógicos.

### Robustez y escalabilidad

Validadas a nivel de piloto, con un potencial de ampliación claramente establecido, aunque todavía no desplegado por completo en todas las estaciones previstas.

---

## Qué significan estos resultados desde la perspectiva de portfolio

Desde el punto de vista de portfolio técnico, el resultado más importante no es una única métrica numérica.

Es que el proyecto demuestra la capacidad de:

* Definir requisitos de diseño realistas
* Elegir una arquitectura de comunicación adecuada
* Integrar hardware industrial con redes IoT
* Adaptar los planes de implementación a restricciones reales de despliegue
* Validar un sistema completo de monitorización de extremo a extremo en condiciones piloto
* Comunicar con claridad el valor de ingeniería de la solución

Eso convierte al proyecto en un ejemplo sólido de ingeniería práctica de sistemas OT/IoT.

---

## Qué no se demostró todavía de forma completa

Un documento de validación creíble también debe dejar claros los límites de los resultados observados.

El piloto **no** demostró de forma completa:

* El despliegue a gran escala en todas las estaciones previstas
* La operación productiva a largo plazo bajo todas las condiciones estacionales y ambientales
* Prestaciones de control en tiempo real estricto
* Un endurecimiento de ciberseguridad de nivel empresarial
* Un benchmarking cuantitativo exhaustivo, como estadísticas de pérdida de paquetes, medidas de disponibilidad o estudios prolongados de fiabilidad en campo

Mencionar esto mejora la credibilidad del portfolio porque evita exagerar lo que el piloto probó realmente.

---

## Principales fortalezas confirmadas por la validación

El piloto confirmó varias fortalezas importantes:

* La arquitectura funciona de extremo a extremo
* La pila de comunicación seleccionada es viable
* Node-RED es eficaz para supervisión centralizada
* La solución puede manejar variables industriales significativas
* El diseño es adecuado para futuras ampliaciones
* El sistema mejora la visibilidad remota frente a una supervisión manual descentralizada

---

## Principales riesgos pendientes y puntos abiertos

La validación también deja abiertas algunas cuestiones de ingeniería para trabajo futuro.

Entre las preocupaciones relevantes para una siguiente iteración están:

* Migrar desde un contexto piloto con servidor de red público hacia una configuración más orientada a producción, si fuera necesario
* Reforzar la ciberseguridad
* Extender el despliegue a estaciones adicionales
* Realizar validaciones de campo más largas
* Desarrollar una lógica de alarmas, analítica y automatización orientada a mantenimiento más profunda

No son signos de debilidad del proyecto. Son los siguientes pasos naturales después de un piloto exitoso.

## Conclusión

Los resultados de validación muestran que el proyecto alcanzó su objetivo técnico central: demostrar que un sistema distribuido de monitorización de estaciones de bombeo de agua basado en adquisición industrial mediante PLC, comunicación Modbus, telemetría LoRaWAN, mensajería MQTT, dashboards Node-RED y almacenamiento en ThingSpeak puede funcionar como una solución coherente de supervisión remota.

El piloto entre Mandojana y Araka demostró que la arquitectura no solo es conceptualmente sólida, sino también operativamente viable dentro del alcance previsto de la monitorización supervisora.

La conclusión de ingeniería más honesta es esta:

* El piloto validó con éxito la arquitectura
* La solución es funcionalmente útil
* El proyecto estableció un patrón repetible para futuros despliegues
* La ampliación de escala y el endurecimiento para producción siguen siendo los siguientes pasos lógicos

Eso hace que los resultados sean sólidos, creíbles y muy adecuados para un portfolio técnico.

---

## Qué viene después

Después de los resultados de validación, el siguiente documento debe centrarse en notas operativas prácticas, consideraciones de despliegue y detalles de implementación útiles para reproducir o ampliar el sistema.

Continúa con: [`notas-de-despliegue.md`](notas-de-despliegue.md)

---

## Navegación

* Volver al [índice de documentación en castellano](README.md)
* Volver a [flujo de datos](flujo-de-datos.md)
* Cambiar a la [versión en inglés](/docs/en/validation-and-results.md)

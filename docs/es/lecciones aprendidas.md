# Lecciones aprendidas

## Propósito de este documento

Este documento resume las principales lecciones de ingeniería extraídas del proyecto.

En un repositorio de portfolio, esta sección es especialmente importante porque muestra criterio, reflexión y madurez técnica. El objetivo no es solo decir que el sistema funcionó, sino explicar qué enseñó el proyecto sobre arquitectura, despliegue, integración, escalabilidad y toma de decisiones prácticas en un contexto industrial OT/IoT.

Este texto está redactado intencionadamente como un **documento de reflexión técnica**, no como un capítulo genérico de conclusiones.

---

## Por qué importa esta sección

Un proyecto como este es valioso no solo porque conecta dispositivos y dashboards, sino porque muestra cómo se comportan los sistemas de ingeniería reales cuando varias tecnologías deben trabajar juntas bajo restricciones prácticas.

Las lecciones más útiles no surgieron solo de la teoría. Surgieron de la implementación, de la iteración y de la adaptación del diseño a limitaciones operativas reales.

Por eso, esta sección se centra en aprendizajes que seguirían siendo relevantes aunque el sistema se ampliara a más estaciones o se rediseñara para un despliegue más orientado a producción.

## Lección 1. Una buena solución IoT industrial empieza con un problema bien definido

Una de las lecciones más sólidas del proyecto es que la pila tecnológica solo tiene sentido cuando el problema operativo está claramente definido desde el principio.

Este proyecto no consistía en “usar LoRaWAN” o en “hacer dashboards” de forma aislada. Consistía en resolver un problema concreto:

* Las estaciones de bombeo remotas eran difíciles de supervisar de forma continua
* La visibilidad solo local generaba fricción operativa
* La monitorización centralizada podía reducir desplazamientos innecesarios y mejorar la capacidad de respuesta

Las tecnologías fueron útiles porque daban respuesta a esa necesidad operativa.

### Por qué importa esto

En proyectos futuros, es importante plantear primero el problema en términos de:

* Qué necesita monitorizarse
* Con qué frecuencia debe actualizarse la información
* Qué necesitan ver los operadores
* Qué restricciones existen tanto en el lado de campo como en el lado central

Esa mentalidad orientada al problema evita complejidad innecesaria.

---

## Lección 2. Las arquitecturas por capas son más fáciles de construir, explicar y escalar

Una segunda lección clave es el valor de pensar en capas.

El proyecto funcionó bien porque no se trató como un único sistema opaco. En su lugar, se estructuró en capas:

* Adquisición en campo
* Comunicación serie industrial
* Conversión de telemetría
* Red LoRaWAN
* Entrega por MQTT
* Procesamiento para dashboards
* Almacenamiento y análisis

### Por qué fue importante

Esto hizo que la solución fuera más fácil de:

* Razonar durante el diseño
* Probar durante la implementación
* Diagnosticar durante el despliegue
* Comunicar en la documentación y en entrevistas
* Extender hacia estaciones adicionales

Cuando los sistemas se diseñan por capas, los fallos son más fáciles de aislar y las responsabilidades más fáciles de asignar.

---

## Lección 3. Estandarizar los datos desde el principio simplifica toda la canalización

Una de las lecciones técnicas más importantes del proyecto es que **la estructura de los datos importa tanto como la comunicación**.

La decisión de organizar las variables del lado PLC en un formato interno coherente fue extremadamente valiosa.

Esa estructura facilitó la alineación entre:

* El acceso a registros Modbus
* La construcción del payload en Dragino
* La lógica del decodificador en TTN
* La extracción de variables en Node-RED
* El mapeo de dashboards

### Idea principal

Si el proyecto hubiera tratado cada estación como un caso completamente personalizado desde el principio, el sistema habría sido mucho más difícil de mantener.

La lección es clara:

**estandarizar los datos antes de transmitirlos siempre que sea posible.**

Este es uno de los aprendizajes más transferibles de todo el proyecto.

---

## Lección 4. La integración OT/IoT es sobre todo un problema de alineación

A primera vista, un proyecto como este puede parecer un problema de conectividad. En la práctica, es más correcto decir que es un **problema de alineación entre varios mundos técnicos**.

El proyecto tuvo que alinear:

* Las señales de campo y su significado en proceso
* La organización de memoria del PLC
* El direccionamiento Modbus
* La selección de payload en Dragino
* Las suposiciones del decodificador en TTN
* La recepción de topics MQTT
* La interpretación del dashboard en Node-RED
* El mapeo de campos en ThingSpeak

### Qué enseña esto

La mayoría de los fallos de integración no vienen de un único error grave. Vienen de pequeños desajustes entre capas.

Por eso, los proyectos OT/IoT exitosos requieren una fuerte disciplina de configuración y definiciones claras de interfaz.

## Lección 5. Prototipar por fases ahorra tiempo después

Otra gran lección es el valor práctico de la validación por etapas.

La implementación no pasó directamente del concepto al despliegue final. En su lugar, distintas partes del sistema se probaron progresivamente:

* Validación del enlace inalámbrico
* Pruebas con generador de señal
* Pruebas del prototipo PLC + Dragino
* Desarrollo de recepción y decodificación
* Supervisión basada en Node-RED
* Validación piloto

### Por qué funcionó bien

Probar subsistemas de forma aislada redujo la ambigüedad.

Por ejemplo, usar un generador de señal antes de la integración completa con PLC ayudó a distinguir si un problema pertenecía a:

* La ruta de transmisión inalámbrica
* La lógica del convertidor
* El lado de datos del PLC

### Idea principal

En sistemas de telemetría distribuidos, **la validación progresiva de subsistemas es mucho más eficiente que intentar depurar toda la pila a la vez.**

---

## Lección 6. Las restricciones reales de la infraestructura pueden cambiar el mejor camino técnico

Una de las lecciones más valiosas surgió de la adaptación necesaria en Araka.

La lógica inicial de recepción evolucionó porque las condiciones prácticas de la infraestructura no permitían el enfoque más directo centrado en Ethernet dentro del entorno central.

Eso obligó al proyecto a cambiar hacia Node-RED como capa principal de recepción, procesamiento y visualización.

### Por qué importa esto

No fue un fallo de diseño. Fue una buena adaptación de ingeniería.

Una solución sólida no es la que sigue el primer plan de forma rígida. Es la que mantiene los objetivos del proyecto mientras se adapta inteligentemente a las condiciones reales.

### Idea principal

**La realidad del campo siempre tiene voto.**

La flexibilidad de diseño es tan importante como la corrección técnica.

---

## Lección 7. Node-RED puede ser mucho más que una herramienta simple de dashboards

Antes de la implementación, Node-RED podía verse como un complemento de visualización. En la práctica, se convirtió en un componente central de integración.

Resolvió varias necesidades al mismo tiempo:

* Recepción MQTT
* Gestión del payload decodificado
* Enrutado de variables
* Presentación en dashboards
* Visibilidad operativa centralizada

### Por qué es importante esta lección

El proyecto demuestra que las herramientas low-code pueden desempeñar un papel de ingeniería serio cuando se utilizan en el contexto adecuado.

Node-RED funcionó bien porque el objetivo era monitorización supervisora, iteración rápida e integración flexible, no control en tiempo real estricto.

### Idea principal

La selección de herramientas debe depender del papel operativo del sistema, no solo de si la herramienta parece o no lo bastante “industrial” a primera vista.

## Lección 8. LoRaWAN es muy adecuada para monitorización supervisora, pero no para todo

El proyecto refuerza una lección importante de arquitectura: la tecnología de comunicación debe ajustarse a los requisitos operativos.

LoRaWAN fue una buena elección aquí porque el caso de uso requería:

* Comunicación de largo alcance
* Bajo coste operativo
* Soporte para activos distribuidos geográficamente
* Tiempos de refresco intermedios en lugar de ciclos de control muy rápidos

### Qué enseña esto

LoRaWAN es muy eficaz cuando el problema encaja con sus fortalezas.

No es la tecnología correcta para cualquier caso industrial, especialmente no para aplicaciones que requieren:

* Alto caudal de datos
* Muy baja latencia
* Transmisión continua y densa de datos
* Control cerrado exigente

### Idea principal

Una tecnología puede ser una muy buena elección sin ser una elección universal.

Esa distinción mejora la calidad del diseño.

---

## Lección 9. El éxito del piloto no significa preparación total para producción

El piloto validó correctamente la arquitectura, pero también puso de relieve la diferencia entre un **piloto funcional** y un **sistema totalmente endurecido para producción**.

El piloto demostró:

* Funcionalidad de extremo a extremo
* Monitorización centralizada útil
* Comunicación de largo alcance viable
* Un patrón repetible en el lado de estación

Pero un despliegue productivo más amplio seguiría requiriendo trabajo adicional en áreas como:

* Disciplina de despliegue a gran escala
* Validación de campo durante periodos más largos
* Refuerzo adicional de la ciberseguridad
* Procedimientos de soporte operativo más formales
* Posiblemente un mayor control del servidor de red según los requisitos

### Idea principal

Un buen ingeniero debe ser capaz de decir ambas cosas:

* “el piloto funcionó”
* “todavía queda trabajo antes del endurecimiento para producción”

Ese equilibrio mejora la credibilidad.

---

## Lección 10. La documentación forma parte del trabajo de ingeniería, no es un trámite final

Otra lección clara es que los sistemas multicapa son difíciles de mantener sin documentación disciplinada.

Este proyecto depende de que varios artefactos se mantengan alineados:

* Mapa de variables del PLC
* Mapa de registros Modbus
* Configuración del Dragino
* Lógica del decodificador en TTN
* Expectativas sobre topics MQTT
* Diseño de flujos en Node-RED
* Mapeo de campos en ThingSpeak

### Por qué importa esto

Si estos elementos no se documentan con claridad, el sistema puede funcionar temporalmente, pero su ampliación y mantenimiento futuros se vuelven mucho más difíciles.

### Idea principal

En proyectos de integración de sistemas, **la documentación forma parte del propio despliegue**.

No es algo que deba dejarse para el final.

---

## Lección 11. La simplicidad suele ganar en proyectos de monitorización distribuida

Una lección especialmente práctica del proyecto es que la simplicidad genera fiabilidad.

La solución no intentó resolverlo todo a la vez. Se centró en un objetivo manejable:

* Adquirir variables significativas
* Transmitirlas de forma eficiente
* Decodificarlas con claridad
* Visualizarlas de forma comprensible
* Almacenarlas para su revisión posterior

### Por qué fue valioso

Intentar añadir demasiadas funciones demasiado pronto probablemente habría hecho el proyecto más frágil y más difícil de validar.

### Idea principal

En sistemas de monitorización remota, una arquitectura más pequeña pero coherente suele ser mejor que otra más grande pero inestable.

---

## Lección 12. La repetibilidad es un mejor objetivo de diseño que la optimización aislada

La arquitectura se concibió para más de una estación, aunque el piloto validó solo un subconjunto reducido.

Eso significa que el verdadero logro no es que una estación funcionara, sino que el proyecto estableció un **patrón repetible desde la estación hasta el entorno central**.

### Por qué importa esto

La repetibilidad es lo que hace valioso el diseño para el crecimiento futuro.

Significa que la ampliación puede abordarse reutilizando:

* El mismo patrón de lógica de adquisición
* La misma cadena de comunicaciones
* El mismo enfoque de decodificación
* El mismo modelo de organización de dashboards

### Idea principal

En proyectos industriales de portfolio, mostrar que un patrón puede repetirse suele ser más valioso que mostrar una optimización excesiva para una sola ubicación.

## Compromisos de diseño que quedaron claros

El proyecto también hizo más visibles varios compromisos de diseño.

### Robustez industrial frente a experimentación rápida

Usar una adquisición centrada en PLC mejoró el realismo y la adecuación al campo, pero también aumentó el esfuerzo de configuración frente a una canalización puramente software o simulada.

### Simplicidad centralizada frente a flexibilidad en campo

Estandarizar los datos de estación facilitó la ampliación, pero también exigió disciplina cuando la realidad local de cada estación era diferente.

### Rapidez en dashboards frente a endurecimiento empresarial

Node-RED permitió una monitorización rápida y efectiva, pero no equivale automáticamente a una solución SCADA empresarial endurecida.

### Comodidad de red pública frente a control de producción

TTN fue extremadamente útil para el piloto, pero un modelo de servidor de red más controlado puede ser preferible en algunos escenarios de producción.

Reconocer este tipo de compromisos es una señal de madurez en el diseño.

---

## Qué mantendría igual

Si el proyecto se repitiera con los mismos objetivos piloto, estas decisiones seguirían teniendo sentido:

* Usar una arquitectura por capas
* Estandarizar los datos pronto dentro del PLC
* Validar subsistemas de forma progresiva
* Usar LoRaWAN para telemetría supervisora de largo alcance
* Usar MQTT para una entrega limpia de mensajes
* Usar Node-RED para integración flexible y dashboards en el lado central
* Mantener el almacenamiento histórico como una preocupación separada de la monitorización en vivo

Estas decisiones son las que generaron gran parte de las fortalezas del proyecto.

---

## Qué mejoraría en una siguiente iteración

Una buena sección de lecciones aprendidas también debe mostrar qué podría mejorarse.

### 1. Versionado más sólido de la configuración

Rastrear de forma más formal la relación entre variables del PLC, mapas Modbus, estructuras de payload y decodificadores.

### 2. Mejor empaquetado de artefactos de despliegue

Conservar configuraciones de ejemplo, exportaciones y tablas de mapeo dentro de una estructura de repositorio más reutilizable.

### 3. Validación de campo más amplia

Extender la validación a más estaciones y durante periodos operativos más largos.

### 4. Refuerzo adicional de la ciberseguridad

Revisar con más profundidad el modelo de comunicación y acceso si el sistema evoluciona hacia un entorno productivo.

### 5. Lógica de alarmas y analítica más explícitas

Ampliar la capa de monitorización con mejor detección de eventos, más visión histórica y alertas orientadas a mantenimiento.

Son pasos naturales después de un piloto exitoso.

## Reflexión final

La lección más importante de este proyecto es que las soluciones útiles de ingeniería rara vez son el resultado de una sola elección tecnológica brillante.

Surgen de:

* Comprender con claridad el problema operativo
* Estructurar el sistema en capas manejables
* Mantener alineadas las interfaces
* Validar de forma progresiva
* Adaptarse cuando aparecen restricciones reales de infraestructura
* Documentar el sistema lo bastante bien como para poder ampliarlo después

Este proyecto es un buen ejemplo de ello.

Su valor no está solo en que produjo un piloto funcional, sino en que construyó un puente realista y repetible entre control industrial, telemetría de largo alcance, dashboards centralizados y análisis apoyado en la nube.

---

## Conclusión

Desde la perspectiva de portfolio, las lecciones aprendidas pueden resumirse de forma sencilla:

* Partir de una necesidad operativa real
* Diseñar por capas
* Estandarizar los datos desde el principio
* Probar de forma progresiva
* Adaptarse a la realidad del despliegue
* Documentar las interfaces con claridad
* Diferenciar entre éxito del piloto y preparación para producción
* Diseñar para la repetibilidad y no para la complejidad aislada

Estas lecciones van más allá de este proyecto concreto. También son principios útiles para futuros sistemas OT/IoT de monitorización.

---

## Navegación

* Volver al [índice de documentación en castellano](README.md)
* Volver a [Notas de despliegue](deployment-notes.md)
* Cambiar a la [versión en inglés](../en/lessons-learned.md)

# Notas de despliegue

## Propósito de este documento

Este documento recopila las consideraciones prácticas que importan a la hora de desplegar, reproducir o ampliar la solución de monitorización.

A diferencia del documento de implementación, que explica cómo se construyó el proyecto paso a paso, esta sección se centra en lo que un ingeniero debe tener en cuenta al llevar la arquitectura a una situación operativa: preparación del hardware, configuración de comunicaciones, consistencia de la configuración, comprobaciones de puesta en marcha y limitaciones operativas.

<p align="center">
  <img src="/assets/images/deployment/deployment-overview.png" alt="Vista general del despliegue" width="500"/>
</p>
<p align="center"><em>Vista práctica del despliegue de la solución de monitorización, desde el hardware de estación hasta la supervisión central.</em></p>

Este documento está redactado de forma intencionada como una **nota técnica orientada al despliegue**, no como un manual de instalación completo.

---

## Qué significa “despliegue” en este proyecto

En este proyecto, el despliegue no se limita a instalar software en una única máquina.

Implica coordinar varias capas al mismo tiempo:

* Hardware industrial en campo
* Configuración de la comunicación serie
* Registro y conectividad de dispositivos LoRaWAN
* Recepción central por MQTT
* Funcionamiento de dashboards en Node-RED
* Publicación y soporte de monitorización en ThingSpeak

Por eso, el despliegue debe entenderse como el proceso de conseguir que toda la cadena de telemetría funcione de manera operativa y estable desde la estación hasta el punto de supervisión central.

## Alcance del despliegue en el piloto

La visión general del proyecto contemplaba varias estaciones de bombeo, pero la validación práctica del despliegue se centró en una configuración piloto.

El contexto del despliegue puede resumirse así:

* **Lado de estación:** Mandojana
* **Lado de supervisión central:** Araka

Esto es importante al presentar el repositorio.

Las notas de despliegue deben describir un **patrón piloto validado** que puede repetirse y ampliarse, no afirmar que el despliegue completo multisitio se realizó en todas las estaciones.

---

## Lista de comprobación previa al despliegue

Antes de la instalación o de reproducir la solución, conviene revisar las siguientes cuestiones.

### Preparación del proceso y de la estación

* ¿Qué variables deben monitorizarse en la estación?
* ¿Son digitales, analógicas o una combinación de ambas?
* ¿Es compatible el número de señales locales con el PLC seleccionado y su configuración de expansión?
* ¿Es adecuado para el proceso el ritmo previsto de consulta y actualización?

### Preparación del hardware

* ¿Está el PLC instalado y alimentado correctamente?
* ¿Está disponible y configurado el módulo CM 1241?
* ¿Está instalado el Dragino RS485-LN con la antena y el cableado correctos?
* ¿Es adecuada la posición del gateway para una recepción LoRaWAN fiable?
* ¿Está disponible y estable la Orange Pi o un entorno central equivalente?

### Preparación de las comunicaciones

* ¿Están alineados los parámetros Modbus RTU entre el PLC y el Dragino?
* ¿Está el dispositivo final registrado correctamente en TTN?
* ¿Existe conectividad MQTT para la capa central de monitorización?
* ¿Están sincronizados el formato de carga útil y la lógica del decodificador?

### Preparación de la monitorización

* ¿Están preparados los flujos de Node-RED para los topics de los dispositivos entrantes?
* ¿Están los widgets del dashboard mapeados a las variables correctas?
* ¿Están definidos de forma coherente los canales y campos de ThingSpeak?

Esta lista de comprobación es útil porque la mayoría de fallos de despliegue en un sistema como este no provienen de un único error grande, sino de pequeños desajustes entre capas.

---

## Notas de despliegue del lado de estación

### Instalación y cableado del PLC

El PLC debe tratarse como el núcleo estructurado de adquisición de la estación.

Las consideraciones de despliegue incluyen:

* Conexión correcta de señales digitales y analógicas
* Tensión de alimentación correcta y condiciones industriales adecuadas
* Organización apropiada del cableado de campo
* Mapeo claro entre señales físicas y variables del PLC
* Suficiente capacidad de entradas y salidas para la estación seleccionada

Una lección de despliegue en este tipo de sistema es que la claridad de las señales importa tanto como la configuración de comunicaciones. Si el mapeo de entradas es inconsistente, la cadena de telemetría puede funcionar técnicamente y, aun así, entregar información de proceso engañosa.

### Instalación del CM 1241

El CM 1241 debe instalarse y parametrizarse de forma coherente con el modo de comunicación serie previsto.

Comprobaciones importantes en el despliegue:

* Baud rate
* Paridad
* Bits de datos
* Bits de parada
* Comportamiento de la estación como esclavo Modbus
* Consistencia con las definiciones de consulta del lado Dragino

Incluso pequeñas discrepancias en los parámetros serie pueden impedir una consulta correcta, por lo que esta etapa siempre debe verificarse antes de buscar problemas en LoRaWAN o MQTT.

### Consistencia de la estructura de datos del PLC

Uno de los requisitos de despliegue más importantes es mantener alineada la estructura de memoria del PLC con el resto del sistema.

Si la estación utiliza el modelo compartido de 4 palabras descrito en la documentación, esa estructura debe mantenerse consistente entre:

* Variables del PLC
* Direccionamiento Modbus
* Extracción de carga útil en Dragino
* Decodificador de carga útil en TTN
* Mapeo del dashboard en Node-RED

Esta es una de las reglas prácticas clave de todo el proyecto:

**si una capa cambia su estructura de datos, las capas dependientes también deben actualizarse.**

## Notas de despliegue de Dragino

El Dragino RS485-LN es uno de los elementos más sensibles del despliegue porque se sitúa justo entre las comunicaciones industriales y la telemetría LoRaWAN.

### Puntos prácticos de instalación

* Asegurar una alimentación estable
* Verificar la polaridad del cableado RS-485 y la corrección de los terminales
* Conectar adecuadamente la antena
* Confirmar que la ubicación física es adecuada para la transmisión por radio
* Mantener acceso local durante la puesta en marcha cuando sea posible

### Notas de configuración

Durante el despliegue, la configuración del Dragino debe revisarse con detalle:

* Definición de la consulta Modbus
* Intervalo de sondeo
* Bytes de respuesta seleccionados
* Tamaño de la carga útil
* Parámetros de unión y red LoRaWAN
* Comportamiento de downlink remoto, si se utiliza

### Riesgo de despliegue que conviene evitar

Un riesgo muy habitual en sistemas como este es asumir que una respuesta Modbus correcta implica automáticamente una carga útil LoRaWAN correcta.

No siempre es así.

La comprobación del despliegue debe validar ambas cosas:

1. Lectura correcta de registros desde el PLC
2. Selección y transmisión correcta de los bytes previstos

---

## Notas de despliegue del gateway y de LoRaWAN

### Ubicación del gateway

El gateway debe instalarse en una ubicación que permita una recepción radio fiable desde el nodo desplegado en la estación.

Consideraciones de despliegue:

* Distancia y condiciones de línea de visión
* Obstáculos físicos
* Atenuación en interior frente a exterior
* Estabilidad de la conectividad a internet hacia TTN

Aunque el proyecto utiliza una arquitectura LoRaWAN cómoda para un piloto, la cobertura radio debe tratarse igualmente como una cuestión operativa de despliegue y no como una simple suposición.

### Registro en TTN y gestión de dispositivos

Para el despliegue, cada dispositivo final debe estar claramente identificado y documentado.

Notas útiles a conservar:

* Nombre del dispositivo
* Nombre de la estación
* Contexto de la aplicación
* Versión del decodificador utilizada
* Estructura de topics esperada aguas abajo

Esto es especialmente importante cuando el proyecto crece desde una estación piloto a varios activos monitorizados.

### Control de versiones del decodificador

La lógica del decodificador en TTN debe tratarse como parte del sistema desplegado y no como un script informal.

Un buen hábito de despliegue es registrar:

* Qué versión del decodificador está activa
* Qué estructura de bytes espera
* A qué carga útil de estación corresponde

Sin esta disciplina, el mantenimiento de varias estaciones se vuelve mucho más difícil.

## Notas de despliegue del lado central

### Estabilidad del entorno Orange Pi

La Orange Pi 3 LTS desempeña un papel práctico pero importante en el despliegue porque aloja el entorno Node-RED.

Comprobaciones importantes:

* Disponibilidad continua de alimentación
* Entorno operativo estable
* Acceso de red para MQTT y servicios externos
* Arranque automático de servicios cuando sea posible
* Supervisión suficiente del estado del entorno de ejecución

La idea principal aquí es sencilla: los dashboards solo son útiles si el entorno central está disponible de forma continua.

### Despliegue de Node-RED

Node-RED debe tratarse como un componente operativo orientado a producción dentro del contexto piloto.

Notas útiles de despliegue:

* Verificar la suscripción a los topics MQTT correctos de TTN
* Validar el análisis de mensajes frente a la salida del decodificador activo
* Comprobar que cada variable llega al widget previsto
* Organizar los flujos con claridad por estación o por función
* Mantener una copia de seguridad o exportación de las definiciones de flujo

Este último punto es especialmente importante en la práctica. Incluso en un piloto de portfolio, los flujos exportados de Node-RED forman parte del sistema desplegable y deben conservarse como activos de configuración.

### Legibilidad del dashboard

Un despliegue no es satisfactorio solo porque aparezcan datos en una pantalla.

Los dashboards también deben ser:

* Fáciles de interpretar con rapidez
* Consistentes entre estaciones
* Explícitos respecto a unidades y significado de las variables
* Visualmente claros para alarmas y estados clave

Esto importa porque el valor operativo del sistema depende tanto de la usabilidad como de la conectividad.

---

## Notas de despliegue de ThingSpeak

ThingSpeak amplía el sistema desde la supervisión en vivo hacia el almacenamiento y el análisis histórico.

### Comprobaciones prácticas de despliegue

* Confirmar que existe el canal correcto para la estación o grupo de variables previsto
* Mapear los campos de forma coherente con los valores transmitidos
* Verificar la publicación correcta desde Node-RED
* Validar la legibilidad de los gráficos y la continuidad histórica
* Configurar alertas solo después de confirmar la estabilidad de los datos entrantes

### Disciplina en el mapeo de campos

Una regla de despliegue simple pero importante es mantener el mismo significado de cada campo a lo largo del tiempo.

Por ejemplo, si un campo se asigna al nivel del depósito, ese significado no debería cambiarse después de forma informal por otra variable distinta sin versionado ni documentación.

Eso mantiene el análisis histórico interpretable.

## Secuencia de puesta en marcha

Una secuencia práctica de puesta en marcha para este proyecto podría ser:

1. Verificar alimentación, cableado y mapeo de variables del PLC
2. Verificar la configuración serie del CM 1241
3. Verificar alimentación, antena y conectividad RS-485 del Dragino
4. Probar el sondeo Modbus de forma local
5. Verificar el registro del dispositivo en TTN y la llegada de uplinks
6. Verificar la corrección de la decodificación de la carga útil
7. Verificar la entrega MQTT a Node-RED
8. Verificar la actualización de dashboards en Node-RED
9. Verificar la publicación a ThingSpeak
10. Verificar la continuidad de la monitorización de extremo a extremo a lo largo del tiempo

Este orden importa porque va desde la estación hacia fuera y reduce la ambigüedad durante el diagnóstico.

---

## Notas de resolución de problemas

### Si no aparecen datos útiles en Node-RED

Posibles causas:

* No está llegando ningún uplink a TTN
* Las credenciales MQTT o la suscripción a topics son incorrectas
* Ha cambiado el formato de salida del decodificador
* El flujo está extrayendo una ruta de propiedad equivocada

### Si TTN recibe uplinks pero los valores son incorrectos

Posibles causas:

* Mapeo incorrecto de registros del PLC
* Dirección Modbus o orden de bytes erróneos
* Selección incorrecta de bytes en Dragino
* Lógica del decodificador desactualizada o no alineada

### Si falla el sondeo Modbus

Posibles causas:

* Problema de cableado RS-485
* Incompatibilidad en el baud rate
* Incompatibilidad en la paridad
* El PLC no está comportándose como esclavo del modo esperado
* Definición de consulta incorrecta en Dragino

### Si los dashboards se actualizan pero el almacenamiento histórico es incorrecto

Posibles causas:

* Mapeo incorrecto de campos en ThingSpeak
* Lógica parcial de publicación en Node-RED
* Reutilización de campos sin documentación

Este enfoque de resolución de problemas es útil porque ayuda al ingeniero a pensar por capas en lugar de ir probando al azar.

---

## Limitaciones operativas a tener en cuenta

La solución desplegada debe entenderse dentro de su alcance operativo previsto.

### Monitorización supervisora, no control rápido

El despliegue da soporte a la visibilidad remota y a la respuesta supervisora, con un comportamiento de refresco adecuado para monitorización operativa.

No debe tratarse como una plataforma para lazos de control en tiempo real muy exigentes.

### Contexto piloto y red pública

El uso de TTN es práctico y eficaz para una validación piloto, pero una futura versión orientada a producción podría requerir una estrategia de servidor de red más controlada en función de los requisitos operativos y de ciberseguridad.

### El crecimiento a varias estaciones exige disciplina

Escalar a más estaciones es viable, pero solo si el nombrado, la gestión de decodificadores, la organización de dashboards y el mapeo de variables se mantienen estructurados.

---

## Artefactos de documentación y configuración que conviene conservar

Un paquete de despliegue sólido debe preservar algo más que documentos narrativos.

Artefactos útiles:

* Mapa de variables del PLC
* Mapa de registros Modbus
* Notas de configuración del Dragino
* Definiciones de dispositivos y decodificadores en TTN
* Exportaciones de flujos de Node-RED
* Mapeo canal-campo de ThingSpeak
* Convenciones de nombre para estaciones
* Lista de comprobación de puesta en marcha y notas de prueba

Aunque el repositorio solo incluya ejemplos simplificados, mantener esta mentalidad mejora la calidad profesional del proyecto.

## Buenas prácticas de despliegue derivadas del proyecto

Algunas reglas prácticas resumen las lecciones más importantes del despliegue:

* Validar cada capa antes de pasar a la siguiente
* Mantener estructuras de datos consistentes entre PLC, Dragino, TTN y Node-RED
* Tratar la lógica del decodificador como un artefacto versionado de despliegue
* Organizar las estaciones con convenciones claras de nombre y mapeo
* Diseñar dashboards para claridad operativa y no solo para completitud técnica
* Documentar las decisiones de configuración mientras se despliega y no después de que aparezcan problemas

Son principios pequeños, pero marcan una gran diferencia en sistemas de telemetría distribuidos.

---

## Conclusión

El despliegue de esta solución de monitorización depende de mucho más que instalar unos pocos dispositivos. Requiere alineación entre hardware industrial, comunicación serie, conectividad LoRaWAN, enrutado de mensajes, dashboards y servicios de almacenamiento.

Lo que demuestra el piloto es que esa alineación puede lograrse en la práctica cuando el sistema se despliega con disciplina:

* Las señales de estación se mapean con claridad
* El PLC expone datos estructurados
* El Dragino envía la carga útil correcta
* TTN la decodifica de forma consistente
* Node-RED la presenta con claridad
* ThingSpeak la almacena de forma útil

Eso es lo que convierte la arquitectura en un sistema operativo de monitorización remota y no en un simple diseño conceptual.

---

## Qué viene después

Después de las notas de despliegue, el último documento debería centrarse en las reflexiones de ingeniería, los compromisos de diseño y las posibles mejoras futuras.

Continúa con: [`lecciones-aprendidas.md`](lecciones-aprendidas.md)

---

## Navegación

* Volver al [índice de documentación en castellano](README.md)
* Volver a [Validación y resultados](validacion-y-resultados.md)
* Cambiar a la [versión en inglés](docs/en/deployment-notes.md)

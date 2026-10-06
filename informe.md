Nombre completo: Hernández Huerta Ari Leonardo
Grupo: IDIA-222 Fecha: 05/10/2026

# Parte II. Aplicación al caso de Big Data
---
## Las 5 V´s aplicadas:
| V del Big Data | Explicación en el Sistema de Sensores | Ejemplo Concreto | ¿Presente en el CSV actual o Ampliación Futura? |
| :--- | :--- | :--- | :--- |
| **Volumen** | Se refiere a la enorme cantidad de datos masivos generados continuamente por las máquinas o sensores industriales. | La dataset actual recopila un total de **100,000 registros** de lecturas térmicas y mecánicas registradas durante un período de transmisión continua[cite: 3]. | **Presente en el CSV actual** (100,000 filas de datos estructurados de sensores)[cite: 3]. |
| **Velocidad** | Indica la rapidez con la que se producen, transmiten y deben procesarse los datos en tiempo real para tomar decisiones instantáneas. | Transmisión de datos segundo a segundo o milisegundo a milisegundo mediante streaming (protocolos MQTT/Kafka) para la detección en milisegundos de un sobrecalentamiento crítico (> 85 °C)[cite: 6]. | **Ampliación Futura** (el CSV actual es una captura estática histórica por intervalo fijo). |
| **Variedad** | Abarca la diversidad de tipos y fuentes de datos (estructurados, semiestructurados o no estructurados) dentro de la planta industrial. | Integración heterogénea de datos tabulares (temperatura y vibración) junto con imágenes termográficas de cámaras infrarrojas y archivos de audio de ruido de motores. | **Ampliación Futura** (el CSV actual contiene únicamente variables numéricas y categóricas estructuradas)[cite: 2]. |
| **Veracidad** | Corresponde a la exactitud, calidad y confiabilidad de los datos, identificando ruido, fallas de calibración o valores nulos. | Verificación de integridad en las lecturas de vibración (`vibracion_mm_s`) y temperatura (`temperatura_c`) para descartar picos anómalos o lecturas erróneas por fallas en el sensor[cite: 2]. | **Presente en el CSV actual** (el dataset se verifica para comprobar que no contiene valores nulos o lecturas atípicas sin formato). |
| **Valor** | Representa la utilidad de transformar los datos crudos en conocimiento accionable e impacto económico directo para la empresa. | Generación de modelos predictivos de mantenimiento preventivo para evitar paros no programados en las plantas (Planta_1 a Planta_4) al identificar fallas antes de que ocurran[cite: 2]. | **Ampliación Futura** (el CSV provee los datos crudos/descriptivos; la conversión en algoritmos predictivos es la etapa analítica de valor agregado)[cite: 2]. |
---
## Tipos de datos y procesamiento tradicional

### Clasificación de Tipos de Datos:
##### CSV de sensores: Dato estructurado
- Los datos están organizados en filas y columnas fijas con esquemas estrictos y tipos de datos definidos como números enteros, decimales y cadenas de texto; es más fácil de indexar y consultar a través de herramientas como Pandas o SQL

##### Mensaje JSON: Dato semiestructurado
- No sigue una estructura de tabla fija, o en general una organización rígida, pero usa etiquetas, claves y pares clave-valor, que organizan la información de forma **jerárquica** y flexible sin necesidad de un esquema relacional definido desde un principio.

##### Fotografía de máquina: Dato No Estructurado
- Consiste eun una **matriz** de pixeles sin un modelo de concepto u organización. Se requerirían algoritmos dedicados de visión por computadora o procesamiento de imágen.

##### Texto libre en reporte: Dato No estructurado
. Está redactado en lenguaje humano, natural, sin una estructura de tabla o etiquetas tan siquiera. La estructura sintáctica varía entre quién(es) lo interpreta(n), categorizarlo o "entenderlo" para una máquina requeriría el procesamiento de lenguaje natural (NLP)

### ¿Por qué 100000 registros no son Big Data por sí mismos?
1. Es un volumen muy bajo por sí mismo, de aprox. 4.4mb que cabe con baste y sobrante en la memoria RAM de cualquier computadora personal, servidor, o incluso en un teléfono celular moderno.

2. No requiere arquitecturas de cómputo distribuido ni bases de datos NoSQL masivas; macros de Python o incluso Excel pueden analizarlo casi al instante.

### Limitaciones de escalabilidad
En caso de que la cantidad de sensores (en el caso del Dataframe actual), o la frecuencia de muestreo de los mismos aumentase, podrían presentarse problemas como:

##### Cuellos de botella limitados en hardware:
Al crecer a gigabytes, terabytes, o aún mayores escalas, Pandas o diferentes herramientas presentarían problemas al cargar el archivo, y más aún al tener que procesarlo por completo por cada operación que se haga sobre los datos.

##### Lectura e ineficiencia I/O:
Por sí mismo, un CSV es un archivo de texto plano, con una lectura secuenciada que permite a la máquina entenderlo como una estructura de datos. Como se mencionó anteriormente, leer un determinado y específico campo de datos, la máquina debería recorrer todo el archivo por cada lectura, lo cual, por el solo peso del mismo escalado haría del proceso una operación demasiado lenta y pesada para la máquina. 

##### Compresión y optimización:
La gran cantidad de masa de datos se vuelve un problema para el almacenamiento disponible, y como hemos mencionado, su lectura también; algunas herramientas como ORC pueden reducir el tamaño hasta un 80% y acelera las lecturas *analíticas*, haciendo el proceso algo más digerible.

---
## Batch y Streaming

### Tipo de procesamiento empleado: Batch
Como se mencionó, el programa ejecutó procesamientos y operaciones sobre un archivo que *ya estaba guardado* por lo que son datosde tipo batch (históricos estáticos), no requirió de ningún flujo continuo ni respuesta en tiempo real.

### Enfoque para alertas: Streaming
Propiamente la premisa indica que se trata de una emisión de alerta *a pocos segundos de identificar una lectura mayor a detemrinado valor*, esto sugiere que se hace una evaluación sostenida sobre datos que no son estáticos, sino que **fluyen** y se **responde al instante** (o poco después) con una alerta. Estas son propiedades del streaming.

### enfoque para generar resumen: Batch
Durante el día se reunen las lecturas y la información tanto de las mismas como de las alertas emitidas en un compendio de datos *pasados*, en base a los cuales (estadística *descriptiva*) se realiza un análiis y un resumen o informe. Esta es la propiedad del Batch, emplear datos previamente contenidos para operar en base a ellos

---

## Lambda vs. Kappa

##### Caso A:
La empresa requiere combinar una ruta que recalcule el *historial por lotes* con otra que procese las mediciones *recientes rápidamente*
- Tenemos al menos una propiedad evidente de cada arquitectura Lambda y la Kappa; Lambda se caracteriza por trabajar, a partes separadas (rutas) el procesamiento de datos de historial (Batch) y por otro lado el procesamiento de datos en flujo (streaming), por lo que, al requerir ambas hileras de procesamiento, *Lambda* es la mejor opción.

##### Caso B:
La empresa quiere una sola lógica de procesamiento de eventos y conservar las mediciones para volver a procesarlas cuando sea necesario
- Lo ideal sería emplear la arquitectura *Kappa* ya que solo trabaja con un flujo de datos (streaming), y utilizar un registro de eventos distribuido con retenci´n a largo plazo o almacenamiento *inmutable*, dígase `Apache Kafka` o `Apache Pulsar` para la reconsulta y reprocesamiento posterior

![Diagrama de procesamiento hipotético en ambas arquitecturas](Lambda%20vs%20Kappa.png)

---

## Analítica descriptiva, predictiva y prescriptiva

##### Analítica descriptiva
1. Alertas por temperatura: un aprox. de 7% de los sensores emitieron una alerta por alta tempetatura (>85°C), aproximadamente, 7,000 observaciones entraron en el umbral indicado.
2. La temperatura más baja registrada fue de 45°C, la más alta fue de 104.99°C, con un promedio general de 66.64°C.

##### Analítica predictiva
P: ¿Con qué frecuencia e intensidad aumentarán los picos de sobrecalentamiento (>85°C) durante las horas de máxima producción intensiva?
DN: 
- Se necesitarían series temporales continuas y estacionalidad; datos de muestreo por segundo para analizar la duración de las incidencias térmicas
- Temperatura ambiente y refrigeración; sería necesario conocer métricas del entorno en que se operan las máquinas y el estado de los sistemas de refrigeraciín (asumiendo que haya en uso).
- Histórico de fallas por sobrecalentamiento; Concretamente un registro de *fechas y horas* en que se producen incidencias térmicas, y sobre todo, si conllevó a una falla o daño real en la máquina.

##### Analítica prescriptiva
Una acción viable ante un riesgo previsto sobre una indicendia de fallo de, por decir, el sistema de refrigeración, o un sobrecalentamiento por sí mismo (que no sugiera ser causa de un fallo de refrigeración), sería activar un sistema automático de enfriamiento *secundario* o de *reducción de carga operativa* en el momento en que la temperatura de un sensor alcance el umbral de alerta (>85°C) por más de cinco minutos continuos;

Para esto se requeriría de
- La validación de diferentes sensores: sensores adyacentes deberían rectificar el pico de temperatura para descartar un error de medición o un fallo del sensor
- Límite térmico del fabricante: Las especificaciones de un componente pueden confirmar si la temperatura se encuentra en un umbral teórico riesgoso.
- Impacto en la producción: En caso de reducir la carga operativa, sería necesario evaluar si su cese o reducción de operación provocaría pérdida o productividad de la empresa. 
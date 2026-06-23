## Lab 2: Preparación y modelado de datos logísticos en Power BI

Imagina que eres analista de operaciones en **RedEx Logistics**, una compañía que monitoriza envíos nacionales desde varias delegaciones regionales. La dirección necesita un modelo fiable en Power BI para analizar volúmenes, ingresos, estado de entrega y cumplimiento de SLA.  
En este laboratorio construirás la base analítica del modelo: conectarás múltiples orígenes, limpiarás datos en Power Query, aplicarás transformaciones avanzadas y diseñarás un **modelo estrella** listo para explotación.

En este ejercicio aprenderás a:
- Conectar múltiples orígenes (CSV y Excel) en Power BI Desktop.
- Organizar consultas de **staging**, dimensiones y hechos.
- Aplicar transformaciones clave en Power Query: tipado, limpieza, eliminación de duplicados y columnas derivadas.
- Usar **Append**, **Merge**, **Unpivot** y **Group By**.
- Diseñar un **Star Schema** con relaciones 1:N y dirección de filtro adecuada.
- Validar criterios para decidir qué lógica va en **Power Query** y qué lógica dejar para DAX.

**Duración estimada:** 45 minutos  
**Nivel:** Avanzado (Módulo 1)  
**Herramienta requerida:** Power BI Desktop

> **Nota:** Este laboratorio está pensado para practicarse en **Power BI Desktop**. Los archivos de datos han sido generados específicamente para este ejercicio y contienen pequeñas incidencias de calidad (duplicados, nulos y formatos inconsistentes) para simular un escenario real.

#### Archivos del laboratorio
Usa los siguientes archivos incluidos junto con este laboratorio:
- **envios_centro.csv**
- **envios_norte.csv**
- **envios_sur.csv**
- **dim_oficinas.csv**
- **dimensiones_logistica.xlsx** (hojas: **Clientes**, **SLA_Servicio**, **Calendario**)
- **volumen_servicio_ancho.xlsx** (hoja: **VolumenAncho**)
- **dim_servicio_base.csv**

### Escenario del caso
La organización recibe información operativa desde varias delegaciones. Actualmente, los responsables detectan tres problemas:
1. Hay archivos regionales separados y no existe una tabla consolidada de envíos.
2. Existen errores de calidad de datos que afectan a la integridad del modelo.
3. Los equipos de negocio quieren analizar rendimiento por oficina, cliente, servicio y fecha sin ambigüedades de relación.

Tu misión es crear un dataset preparado para análisis con la granularidad correcta: **un envío = una fila en la tabla de hechos**.

---

#### Tarea 1: Importar datos desde múltiples orígenes

El primer paso consiste en cargar todas las fuentes del laboratorio en Power BI Desktop y organizarlas con una estructura profesional desde el inicio.

- Abre **Power BI Desktop**.
- Selecciona **Obtener datos**.
- Importa los tres archivos CSV regionales:
  - **envios_centro.csv**
  - **envios_norte.csv**
  - **envios_sur.csv**
- Importa también:
  - **dim_oficinas.csv**
  - **dimensiones_logistica.xlsx** (selecciona las hojas **Clientes**, **SLA_Servicio** y **Calendario**)
  - **volumen_servicio_ancho.xlsx** (hoja **VolumenAncho**)
  - **dim_servicio_base.csv**
- En la ventana de navegación, selecciona **Transformar datos**.
- En Power Query, renombra las consultas con una convención clara. Usa esta sugerencia:
  - **stg_Envios_Centro**
  - **stg_Envios_Norte**
  - **stg_Envios_Sur**
  - **dim_Oficina**
  - **dim_Cliente**
  - **dim_SLA_Servicio**
  - **dim_Fecha**
  - **stg_VolumenServicioAncho**
  - **dim_Servicio_Base**

**Objetivo de la tarea:** dejar configurada una base de trabajo legible y mantenible.

---

#### Tarea 2: Limpiar y tipar las tablas de staging

Antes de combinar datos, es necesario garantizar que las tres tablas de envíos compartan estructura, tipos correctos y un nivel mínimo de calidad.

- Abre la consulta **stg_Envios_Centro**.
- Comprueba y corrige los tipos de datos:
  - **NumeroEnvio** → Texto
  - **FechaEnvio** → Fecha/Hora
  - **FechaEntrega** → Fecha/Hora
  - **CodigoOficina** → Texto
  - **IdCliente** → Texto
  - **Servicio** → Texto
  - **PesoKg** → Número decimal
  - **Bultos** → Número entero
  - **IngresosEUR** → Número decimal fijo o decimal
  - **EstadoEntrega** → Texto
  - **DistanciaKm** → Número entero
  - **CanalEntrada** → Texto
- Repite la misma revisión para **stg_Envios_Norte** y **stg_Envios_Sur**.
- En cada una de las tres consultas, aplica estas transformaciones:
  - **Quitar espacios** en la columna **Servicio**.
  - Convertir **Servicio** a formato consistente (por ejemplo, con mayúscula inicial).
  - Detectar y eliminar **duplicados exactos**.
  - Revisar los **nulos** en **PesoKg** e **IdCliente**.
- Para los registros con **PesoKg** vacío, reemplaza el valor por la **mediana del servicio** o por un valor razonable si prefieres una aproximación manual documentada.
- Para **IdCliente** vacío, reemplaza por **C010** para mantener la integridad del modelo durante la práctica.
- Añade una nueva columna personalizada llamada **OrigenArchivo** con el valor:
  - **Centro** en **stg_Envios_Centro**
  - **Norte** en **stg_Envios_Norte**
  - **Sur** en **stg_Envios_Sur**

ℹ️ **Pista:** si quieres mantener una arquitectura limpia, deja estas consultas como **staging** y realiza transformaciones reutilizables antes de la consolidación.

---

#### Tarea 3: Consolidar datos regionales con Append

Ahora unificarás los envíos regionales en una sola tabla base de hechos.

- En Power Query, selecciona **Anexar consultas como nuevas**.
- Anexa **stg_Envios_Centro**, **stg_Envios_Norte** y **stg_Envios_Sur**.
- Renombra la nueva consulta como **fact_Envios_Base**.
- Verifica que todas las columnas se han alineado correctamente.
- Comprueba la granularidad: cada fila debe representar **un envío**.
- Añade las siguientes columnas derivadas en **fact_Envios_Base**:
  - **FK_Fecha**: clave con formato `yyyymmdd` a partir de **FechaEnvio**.
  - **EsEntregaCompletada**: 1 si **FechaEntrega** tiene valor, en caso contrario 0.
  - **HorasTransito**: diferencia en horas entre **FechaEnvio** y **FechaEntrega**.

**Comprobación rápida:** tras eliminar duplicados y anexar correctamente, deberías quedarte con una tabla consolidada de aproximadamente **173 filas**.

---

#### Tarea 4: Enriquecer la tabla de hechos con Merge

A continuación, enriquecerás la tabla de hechos para incorporar atributos de negocio y preparar reglas operativas.

- En **fact_Envios_Base**, usa **Combinar consultas** con **dim_SLA_Servicio**.
- Relaciona ambas consultas por la columna **Servicio**.
- Elige un **Left Outer Join**.
- Expande al menos estas columnas desde **dim_SLA_Servicio**:
  - **SLA_Horas**
  - **TarifaBaseEUR**
  - **Prioridad**
- Crea una columna condicional llamada **CumpleSLA**:
  - Si **FechaEntrega** es nula → **En curso**
  - Si **HorasTransito** \<= **SLA_Horas** → **Sí**
  - En cualquier otro caso → **No**
- Revisa si esta lógica encaja mejor en Power Query o en DAX. Documenta tu criterio en una nota personal:
  - ¿La transformación depende del modelo completo?
  - ¿Se necesita durante la carga o solo para el análisis?

**Reflexión guiada:** esta tarea te ayuda a distinguir una transformación empresarial repetible en ETL de un cálculo contextual que podría dejarse para DAX.

---

#### Tarea 5: Transformación avanzada con Unpivot

La dirección también envía un archivo con volúmenes agregados por oficina en formato ancho. Debes convertirlo a formato analítico.

- Abre la consulta **stg_VolumenServicioAncho**.
- Mantén fija la columna **CodigoOficina**.
- Selecciona las columnas de servicio (**Carta**, **Paquete**, **Express**, **Palet**) y aplica **Despivotar columnas**.
- Renombra las columnas resultantes:
  - **Atributo** → **Servicio**
  - **Valor** → **VolumenEnvios**
- Renombra la consulta como **fact_VolumenServicio**.
- Comprueba que ahora cada fila representa una combinación **Oficina + Servicio**.

**Objetivo de la tarea:** reforzar el criterio de que el formato largo suele ser el adecuado para modelos analíticos y futuras medidas DAX.

---

#### Tarea 6: Crear una dimensión de servicio reutilizable

Ahora construirás una dimensión de servicio para reutilizarla en varias tablas y evitar inconsistencias.

- Duplica o, preferiblemente, crea una **referencia** de **dim_Servicio_Base**.
- Renombra la consulta resultante como **dim_Servicio**.
- Revisa que la columna **Servicio** contenga valores únicos.
- Si lo deseas, añade una clave índice llamada **ID_Servicio**.
- Comprueba que tanto **fact_Envios_Base** como **fact_VolumenServicio** usan exactamente los mismos nombres de servicio.

ℹ️ **Buena práctica:** usa referencias para centralizar la lógica y evitar mantener varios listados manuales.

---

#### Tarea 7: Validación con Group By

Antes de cargar el modelo, realizarás una verificación operativa agregando por oficina y semana.

- Crea una **referencia** de **fact_Envios_Base** y llámala **qa_Envios_Semana_Oficina**.
- Aplica **Agrupar por** usando:
  - **CodigoOficina**
  - **FK_Fecha** o, si prefieres, una semana derivada desde **FechaEnvio**
- Calcula al menos estas métricas:
  - **TotalEnvios** = recuento de filas
  - **IngresosTotales** = suma de **IngresosEUR**
- Marca esta consulta como una consulta de control (opcionalmente, puedes **deshabilitar su carga** si no la vas a explotar).

**Objetivo de la tarea:** practicar **Group By** y demostrar cómo Power Query puede servir también para validaciones previas al modelo.

---

#### Tarea 8: Diseñar el modelo estrella

Con los datos listos, construirás el modelo semántico base del laboratorio.

- Selecciona **Cerrar y aplicar**.
- Ve a la vista **Modelo**.
- Crea estas relaciones recomendadas:
  - **dim_Fecha[FK_Fecha]** → **fact_Envios_Base[FK_Fecha]** (1:N)
  - **dim_Oficina[CodigoOficina]** → **fact_Envios_Base[CodigoOficina]** (1:N)
  - **dim_Cliente[IdCliente]** → **fact_Envios_Base[IdCliente]** (1:N)
  - **dim_Servicio[Servicio]** → **fact_Envios_Base[Servicio]** (1:N)
  - **dim_Oficina[CodigoOficina]** → **fact_VolumenServicio[CodigoOficina]** (1:N)
  - **dim_Servicio[Servicio]** → **fact_VolumenServicio[Servicio]** (1:N)
- Configura la **dirección de filtro** como **unidireccional** siempre que sea posible.
- Comprueba que no existan duplicados en las claves de las dimensiones.
- Revisa visualmente que el diseño tenga forma de **estrella**, con dimensiones alrededor de las tablas de hechos.

**Pregunta de comprobación:** ¿qué riesgos aparecerían si configuraras relaciones bidireccionales sin necesidad o si dejaras duplicados en una dimensión?

---

#### Tarea 9: Verificación funcional mínima

Aunque el foco del módulo es preparación y modelado, conviene validar que el modelo responde como se espera.

- Crea una página de informe llamada **Validación del modelo**.
- Inserta una tabla o matriz con:
  - **Region** desde **dim_Oficina**
  - **Servicio** desde **dim_Servicio**
  - Recuento de **NumeroEnvio**
  - Suma de **IngresosEUR**
- Añade un segmentador por **Mes** usando **dim_Fecha**.
- Filtra por una región y comprueba que las métricas se comportan correctamente.
- Verifica que el filtrado de dimensiones impacta a ambas tablas de hechos cuando corresponde.

**Objetivo de la tarea:** confirmar que el modelo está listo para la siguiente fase del curso (DAX y explotación analítica).

---

#### Entregable esperado

Al finalizar el laboratorio deberías tener:
- Un conjunto de consultas organizado en **staging**, **dimensiones**, **hechos** y, opcionalmente, **QA**.
- Una tabla **fact_Envios_Base** consolidada y limpia.
- Una tabla **fact_VolumenServicio** en formato largo.
- Un modelo estrella con relaciones 1:N correctamente definidas.
- Un criterio claro sobre qué transformaciones resuelves en **Power Query** y cuáles dejarías para **DAX**.

---

#### Tarea opcional de extensión

Si terminas antes de tiempo, realiza una de estas actividades:
- Crea una consulta de referencia para detectar envíos con **IngresosEUR** por debajo de **TarifaBaseEUR**.
- Añade una clasificación de peso (**Ligero**, **Medio**, **Pesado**) en Power Query.
- Diseña una tabla puente hipotética si te pidieran analizar **clientes** y **servicios contratados** con una relación muchos a muchos.

---

#### Resumen

| Concepto | Punto clave |
|---|---|
| **Múltiples orígenes** | Power Query permite unificar archivos CSV y Excel en un único flujo ETL. |
| **Transformaciones clave** | Tipado, limpieza, reemplazo de nulos y deduplicación son la base de un modelo fiable. |
| **Transformaciones avanzadas** | Append, Merge, Group By y Unpivot convierten datos dispersos en estructuras analíticas. |
| **Star Schema** | Las dimensiones deben filtrar a los hechos mediante relaciones 1:N claras y mantenibles. |
| **Buenas prácticas** | Nombres descriptivos, consultas de staging y referencias mejoran rendimiento y gobierno del modelo. |
| **Power Query vs DAX** | Si una lógica no depende del contexto del modelo, normalmente conviene resolverla antes de cargar. |

#### Resultados de control sugeridos
Usa estas referencias como comprobación orientativa durante el laboratorio:
- Filas brutas combinadas antes de limpiar: **178**
- Duplicados exactos detectados: **5**
- Registros con **IdCliente** vacío en origen: **4**
- Registros con **PesoKg** vacío en origen: **3**
- Filas finales esperadas en **fact_Envios_Base** tras deduplicar: **173**

#### Recursos adicionales sugeridos para la práctica
- Documenta cada decisión de transformación en el panel **Pasos aplicados**.
- Deshabilita la carga de consultas intermedias si no forman parte del modelo final.
- Mantén el modelo listo para el siguiente módulo, donde construirás medidas DAX y KPIs.

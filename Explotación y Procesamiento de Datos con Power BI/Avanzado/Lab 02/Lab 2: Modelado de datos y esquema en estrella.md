## Laboratorio 2: Modelado de datos y esquema en estrella en Power BI

**ParcelCraft** es una empresa ficticia de logística que necesita convertir sus datos preparados en Power Query en un **modelo semántico robusto, mantenible y listo para análisis**.

En el laboratorio anterior transformaste el archivo **ParcelCraft_50k.csv** en una tabla de hechos de envíos y varias dimensiones. En esta segunda parte trabajarás principalmente en la **vista Modelo** de Power BI Desktop para organizar las tablas, crear y administrar relaciones, aplicar propiedades del modelo, definir jerarquías de negocio y dejar preparado el modelo para análisis.

---

### Objetivos de aprendizaje

**Tiempo estimado total: 45 minutos**

Al finalizar este laboratorio serás capaz de:
- Revisar y validar un **modelo en estrella** en Power BI.
- Crear pocos **layouts útiles** en la vista Modelo.
- Crear, editar y administrar **relaciones**.
- Configurar cardinalidad, dirección de filtro cruzado y estado activo de relaciones.
- Resolver una relación conceptual varios a varios mediante una **tabla puente**.
- Crear jerarquías geográficas y operativas.
- Configurar propiedades de tablas, columnas, relaciones y modelo.
- Ocultar columnas técnicas y mejorar la experiencia del usuario final.

---

## Antes de empezar

### Requisitos

Para completar este laboratorio necesitas:
- Power BI Desktop instalado.
- Conexión a Internet.
- Haber completado el **Laboratorio 1: Ingesta, preparación y carga de datos en Power Query**, o partir del archivo inicial.
- Conocimientos básicos de relaciones y modelado dimensional.

Tienes un archivo inicial y solución disponibles. Si vas a trabajar en el mismo archivo a lo largo de todos los laboratorios, recomiendo empezar a partir de este starter.
- [Archivo inicial (Starter)](https://github.com/quizeth/Labs/blob/main/Explotaci%C3%B3n%20y%20Procesamiento%20de%20Datos%20con%20Power%20BI/Avanzado/Lab%2002/Files/02-A%20Starter%20ParcelCraft.pbix)
- [Archivo final (Solución)](https://github.com/quizeth/Labs/blob/main/Explotaci%C3%B3n%20y%20Procesamiento%20de%20Datos%20con%20Power%20BI/Avanzado/Lab%2002/Files/02-A%20Soluci%C3%B3n%20ParcelCraft.pbix)

### Convenciones usadas en el laboratorio
- Cuando se indique seleccionar una vista, usa el panel izquierdo de Power BI Desktop:
  - **Vista Informe**
  - **Vista Tabla**
  - **Vista Modelo**
- Cuando se indique una opción de cinta, se mostrará como **pestaña > grupo > comando**.
- Los nombres de tablas, columnas y medidas deben escribirse exactamente como se indican, salvo que tu archivo tenga nombres equivalentes ligeramente distintos.
- En este laboratorio no se rehace la preparación de Power Query; el foco está en el **modelo semántico**.

---

## Ejercicio 1: Revisar el modelo cargado y confirmar el diseño en estrella

En este ejercicio revisarás las tablas cargadas desde Power Query e identificarás qué tabla actuará como hecho y cuáles actuarán como dimensiones.

### Tarea 1: Abrir el archivo de trabajo
- Abre **Power BI Desktop**.
- Abre el archivo `.pbix` generado al final del laboratorio anterior.
- Si Power BI solicita aplicar cambios pendientes de Power Query, selecciona **Aplicar cambios**.
- Ve a la **vista Modelo**.
- Revisa que aparecen las siguientes tablas:
  - `Cliente`
  - `Destino`
  - `Envíos`
  - `Oficina`
  - `Ruta`
  - `Servicio`

### Tarea 2: Identificar hechos y dimensiones
- Localiza la tabla `Envíos`.
- Confirma que contiene columnas transaccionales, métricas y claves, por ejemplo:
  - `EnvíoID`
  - `FechaEnvío`
  - `ClienteID`
  - `ServicioID`
  - `OficinaID`
  - `RutaID`
  - `Peso (KG)`
  - `Precio total`
  - `Intentos`
  - `EntregaTardia`
- Localiza las tablas de dimensión:
  - `Cliente`: atributos del cliente.
  - `Servicio`: atributos del servicio contratado.
  - `Oficina`: atributos de la oficina o hub operativo.
  - `Destino`: atributos geográficos del destino.
  - `Ruta`: atributos de la ruta o modo de transporte.
- Comprueba que estas tablas contienen campos descriptivos que permitirán analizar, filtrar o agrupar los envíos.

**Resultado esperado:** identificas `Envíos` como tabla de hechos y el resto de tablas como dimensiones. El modelo objetivo debe tener una estructura en estrella, con relaciones de uno a varios desde dimensiones hacia la tabla de hechos.

---

## Ejercicio 2: Crear layouts útiles en la vista Modelo

En este ejercicio crearás pocos layouts, pero con un propósito claro. La idea es facilitar la lectura, explicación y mantenimiento del modelo.

Trabajarás con dos layouts:
- `01 - Modelo estrella`: vista principal del modelo completo.
- `02 - Servicio y SLA`: vista de apoyo para revisar atributos de servicio y SLA.

> Buena práctica: en modelos reales, demasiados layouts pueden generar ruido. Es preferible tener pocos layouts bien diseñados y con un objetivo claro.

### Tarea 1: Crear el layout principal del modelo estrella
- En la **vista Modelo**, localiza las pestañas de layouts en la parte inferior.
- Crea un nuevo layout y nómbralo `01 - Modelo estrella`.
- Arrastra `Envíos` al centro del lienzo.
- Coloca las cinco tablas de dimensiones alrededor.
- Ajusta el ancho de las tablas para que se vean las claves y columnas principales.

### Tarea 2: Crear el layout de servicio y SLA
- Crea un segundo layout y nómbralo `02 - Servicio y SLA`.
- Añade solo las tablas necesarias para revisar la lógica de servicio:
  - `Envíos`
  - `Servicio`
- Coloca `Envíos` en el centro del lienzo.
- Coloca `Servicio` debajo o a un lado de `Envíos`.
- Ajusta el ancho de las tablas para que se vean las columnas relacionadas con servicio, prioridad y SLA.

> Nota: en el **Laboratorio 3** añadirás la tabla `Fecha` y podrás ampliar este layout si quieres revisar relaciones temporales.

### Tarea 3: Revisar la utilidad de los layouts
- Vuelve al layout `01 - Modelo estrella`.
- Comprueba que el modelo se entiende de un vistazo.
- Verifica que `Envíos` queda claramente identificada como tabla central.
- Cambia al layout `02 - Servicio y SLA`.
- Comprueba que contiene solo las tablas necesarias para revisar la lógica de servicio.
- Si una tabla no aporta valor a un layout, elimínala del layout, no necesariamente del modelo.

**Resultado esperado:** el modelo queda organizado en dos layouts útiles: uno para explicar el modelo estrella y otro para revisar atributos de servicio y SLA.

---

## Ejercicio 3: Crear y administrar relaciones en Power BI

En este ejercicio crearás manualmente las relaciones principales del modelo. En este archivo, la creación automática de relaciones está intencionalmente deshabilitada, por lo que Power BI no habrá generado relaciones al cargar las tablas.

### Tarea 1: Confirmar que el modelo no tiene relaciones automáticas
- En la **vista Modelo**, observa el lienzo del modelo.
- Comprueba que las tablas aparecen sin líneas de relación entre ellas.

> Nota: esto es esperado. En este archivo, la creación automática de relaciones está intencionalmente deshabilitada para que las relaciones se creen manualmente durante el laboratorio.

- En la cinta, selecciona **Modelado > Administrar relaciones**.
- Revisa la ventana **Administrar relaciones**.
- Confirma que no hay relaciones creadas o que no hay relaciones relevantes para el modelo.
- Si encuentras alguna relación no esperada, selecciónala y pulsa **Eliminar**.

### Tarea 2: Crear la relación Cliente-Envíos
- En la ventana **Administrar relaciones**, selecciona **Nuevo**.
- Configura la relación:
  - **Tabla 1:** `Cliente`
  - **Columna:** `ClienteID`
  - **Tabla 2:** `Envíos`
  - **Columna:** `ClienteID`
  - **Cardinalidad:** Uno a varios (`1:*`)
  - **Dirección de filtro cruzado:** Simple
  - **Hacer esta relación activa:** Activado
- Selecciona **Aceptar**.

### Tarea 3: Crear la relación Servicio-Envíos
- Selecciona **Nuevo**.
- Configura la relación:
  - **Tabla 1:** `Servicio`
  - **Columna:** `ServicioID`
  - **Tabla 2:** `Envíos`
  - **Columna:** `ServicioID`
  - **Cardinalidad:** Uno a varios (`1:*`)
  - **Dirección de filtro cruzado:** Simple
  - **Hacer esta relación activa:** Activado
- Selecciona **Aceptar**.

### Tarea 4: Crear la relación Oficina-Envíos
- Selecciona **Nuevo**.
- Configura la relación:
  - **Tabla 1:** `Oficina`
  - **Columna:** `OficinaID`
  - **Tabla 2:** `Envíos`
  - **Columna:** `OficinaID`
  - **Cardinalidad:** Uno a varios (`1:*`)
  - **Dirección de filtro cruzado:** Simple
  - **Hacer esta relación activa:** Activado
- Selecciona **Aceptar**.

### Tarea 5: Crear la relación Ruta-Envíos
- Selecciona **Nuevo**.
- Configura la relación:
  - **Tabla 1:** `Ruta`
  - **Columna:** `RutaID`
  - **Tabla 2:** `Envíos`
  - **Columna:** `RutaID`
  - **Cardinalidad:** Uno a varios (`1:*`)
  - **Dirección de filtro cruzado:** Simple
  - **Hacer esta relación activa:** Activado
- Selecciona **Aceptar**.

### Tarea 6: Crear la relación Destino-Envíos
- Selecciona **Nuevo**.
- Configura la relación:
  - **Tabla 1:** `Destino`
  - **Columna:** `DestinoID`
  - **Tabla 2:** `Envíos`
  - **Columna:** `DestinoID`
  - **Cardinalidad:** Uno a varios (`1:*`)
  - **Dirección de filtro cruzado:** Simple
  - **Hacer esta relación activa:** Activado
- Selecciona **Aceptar**.

> Nota: en un modelo productivo suele ser preferible relacionar tablas mediante claves numéricas o claves sustitutas estables cuando estén disponibles.

### Tarea 7: Validar las relaciones creadas
- Cierra la ventana **Administrar relaciones**.
- Vuelve a la **vista Modelo**.
- Comprueba que ahora aparecen líneas de relación entre las dimensiones y `Envíos`.
- Verifica que `Envíos` queda en el lado varios (`*`) de cada relación.
- Verifica que las dimensiones quedan en el lado uno (`1`) de cada relación.
- Comprueba que la dirección de filtro va desde las dimensiones hacia `Envíos`.

**Resultado esperado:** el modelo tiene relaciones activas desde cada dimensión hacia `Envíos`, con cardinalidad uno a varios y dirección de filtro cruzado simple.

---

## Ejercicio 4: Resolver una relación varios a varios con una tabla puente

En este ejercicio explorarás una relación conceptual de varios a varios entre `Cliente` y `Servicio` sin usar DAX.

En el modelo de ParcelCraft, un cliente puede utilizar varios servicios y un servicio puede ser utilizado por muchos clientes. Aunque esta relación existe en los datos, no conviene resolverla creando una relación directa varios a varios entre las dimensiones. En su lugar, crearás una **tabla puente** desde Power Query con las combinaciones únicas de cliente y servicio observadas en la tabla de hechos `Envíos`.

Este patrón permite representar la relación `Cliente` ↔ `Servicio` mediante dos relaciones de uno a varios (`1:*`):
- `Cliente` → `ClienteServicio`
- `Servicio` → `ClienteServicio`

### Tarea 1: Comprobar el escenario varios a varios
- Ve a la **vista Informe**.
- Añade una visualización de tabla.
- Agrega los campos `Cliente[Cliente]` y `Servicio[Servicio]`.
- Revisa el resultado.

La visualización no representa correctamente qué servicios están realmente asociados a cada cliente, porque `Cliente` y `Servicio` son dos dimensiones independientes.

> Observación: esto no significa que el modelo esté mal. En un modelo en estrella, las dimensiones normalmente filtran a la tabla de hechos, pero no se filtran directamente entre sí.

### Tarea 2: Crear la tabla puente en Power Query
- En la cinta **Inicio**, selecciona **Transformar datos**.
- En el Editor de Power Query, localiza la consulta `stg_ParcelCraft_Clean`.
- Haz clic derecho sobre `stg_ParcelCraft_Clean` y selecciona **Referencia**.
- Cambia el nombre de la nueva consulta a `ClienteServicio`.
- Mueve la tabla a la sección **Auxiliares**. **No** deshabilites la carga.
- Con la consulta `ClienteServicio` seleccionada, ve a **Inicio > Administrar columnas > Elegir columnas**.
- Selecciona únicamente estas columnas:
  - `ClienteID`
  - `ServicioID`
- Selecciona **Aceptar**.
- Selecciona ambas columnas: `ClienteID` y `ServicioID`.
- En la cinta, selecciona **Inicio > Quitar filas > Quitar duplicados**.
- Revisa que la consulta contiene una fila por cada combinación única de cliente y servicio.
- Ordena la columna `ClienteID` en orden ascendente.
- Selecciona **Cerrar y aplicar**.

**Resultado esperado:** el modelo contiene una nueva tabla cargada llamada `ClienteServicio`, con las columnas `ClienteID` y `ServicioID`.

### Tarea 3: Crear un layout para la tabla puente
- Ve a la **vista Modelo**.
- Crea un nuevo layout llamado `03 - Puente Cliente Servicio`.
- Añade al layout las tablas:
  - `Cliente`
  - `ClienteServicio`
  - `Servicio`
- Coloca `ClienteServicio` en el centro.
- Coloca `Cliente` a la izquierda.
- Coloca `Servicio` a la derecha.

### Tarea 4: Crear relaciones con la tabla puente
- En la cinta, selecciona **Modelado > Administrar relaciones**.
- Selecciona **Detección automática**.
- Si tu modelo está bien configurado, se habrán detectado estas dos nuevas relaciones:

| Tabla desde | Columna desde | Cardinalidad | Dirección de filtro cruzado | Tabla hasta | Columna hasta | Estado |
|---|---|---:|---|---|---|---|
| `ClienteServicio` | `ClienteID` | `*:1` | Simple | `Cliente` | `ClienteID` | Activo |
| `ClienteServicio` | `ServicioID` | `*:1` | Simple | `Servicio` | `ServicioID` | Activo |

- Selecciona **Aceptar**.
- Cierra la ventana **Administrar relaciones**.

> Importante: no crees una relación directa entre `Cliente` y `Servicio`. Tampoco conectes `ClienteServicio` con `Envíos` en este ejercicio.

### Tarea 5: Validar la tabla puente
- Vuelve a la vista de informe.
- Añade una nueva visualización de tabla.
- Agrega los campos:
  - `Cliente[Cliente]`
  - `ClienteServicio[ServicioID]`
- En el área de campos de la visualización, abre el menú desplegable de `ServicioID`.
- Cambia la agregación a **Recuento distintivo**.
- Revisa el resultado. Cada cliente debe mostrar el número de servicios realmente asociados en la tabla puente.

> Nota: en el conjunto de datos de prueba, es posible que siga habiendo 4 servicios por cliente.

- Añade otra visualización de tabla.
- Agrega los campos:
  - `Servicio[Servicio]`
  - `ClienteServicio[ClienteID]`
- En el menú desplegable de `ClienteID`, cambia la agregación a **Recuento distintivo**.
- Revisa el resultado. Cada servicio debe mostrar el número de clientes asociados en la tabla puente.

> Nota: en el conjunto de datos de prueba, es posible que siga habiendo 200 clientes por servicio.

**Resultado esperado:** puedes analizar asociaciones entre clientes y servicios usando la tabla puente.

---

## Ejercicio 5: Crear jerarquías de negocio

En este ejercicio crearás jerarquías para mejorar la navegación y el autoservicio en los informes.

### Tarea 1: Crear jerarquía geográfica de destino
- Ve a la **vista Modelo**.
- Expande la tabla `Destino`.
- Haz clic derecho sobre `Región (Destino)`.
- Selecciona **Crear jerarquía**.
- Cambia el nombre a `Geografía`.
- Añade las columnas en este orden:
  - `Región (Destino)`
  - `Ciudad (Destino)`
  - `Código Postal (Destino)`

### Tarea 2: Crear jerarquía operativa de oficina
- Expande la tabla `Oficina`.
- Crea una jerarquía llamada `Oficinas`.
- Añade las columnas en este orden:
  - `Región`
  - `Ciudad`
  - `Oficina`

### Tarea 3: Crear jerarquía de servicio
- Expande la tabla `Servicio`.
- Crea una jerarquía llamada `Servicios`.
- Añade las columnas en este orden:
  - `Prioridad`
  - `Servicio`

**Resultado esperado:** el modelo contiene jerarquías reutilizables para análisis geográfico, operativo y de servicio.

---

## Ejercicio 6: Configurar propiedades de tablas y columnas

En este ejercicio mejorarás la experiencia de usuario del modelo ocultando columnas técnicas, configurando formatos y añadiendo metadatos.

### Tarea 1: Ocultar columnas de claves técnicas
- En la **vista Modelo**, haz clic en el panel de Datos y selecciona **Expandir todo**.
- Oculta las columnas que no deberían usarse directamente en visualizaciones:
  - `ClienteID` en todas las tablas.
  - `DestinoID` en todas las tablas.
  - `OficinaID` en todas las tablas.
  - `RutaID` en todas las tablas.
  - `ServicioID` en todas las tablas.

Para ocultar una columna:
- Haz clic derecho sobre la columna.
- Selecciona **Ocultar en la vista de informe**.

> Buena práctica: las claves técnicas son esenciales para relaciones, pero generalmente no deben exponerse al usuario final.

### Tarea 2: Configurar formatos numéricos
- Selecciona `Envíos[Precio total]`.
- En **Herramientas de columna**, configura:
  - **Formato:** Moneda
  - **Decimales:** 2
  - **Símbolo:** € Español (España)
- Repite para:
  - `Precio base`
  - `Surplus fuel`

### Tarea 3: Configurar categorías de datos
- Selecciona `Destino[Ciudad (Destino)]`.
- En **Propiedades > Avanzado > Categoría de datos**, selecciona `Ciudad`.
- Selecciona `Destino[Código Postal (Destino)]`.
- Configura la categoría como `Código postal`.
- Selecciona `Destino[Región (Destino)]`.
- Configura la categoría como `País o región` si aplica a tu modelo.
- Repite los pasos para las columnas `Ciudad` y `Región` de la tabla `Oficina`.

### Tarea 4: Configurar resumen predeterminado
- Selecciona las columnas `Precio total`, `Peso (KG)` o `Intentos`.
- En **Propiedades > Avanzado > Resumir por**, configúralas:
  - `Precio total`: Suma
  - `Peso (KG)`: Suma o Promedio, según criterio analítico
  - `Intentos`: Suma o Promedio, según el análisis esperado

### Tarea 5: Añadir descripciones
- En la tabla `Envíos`, selecciona la columna `EntregaTardia`.
- En el panel **Propiedades**, localiza **Descripción**.
- Escribe: `Indica si la entrega se realizó después de la fecha y hora comprometida por SLA.`
- Añade descripciones a estas columnas:
  - `FechaCompromisoSLA`: Fecha y hora máxima esperada de entrega según el SLA.
  - `Precio total`: Importe total del envío en euros.
  - `Intentos`: Número de intentos de entrega registrados.

**Resultado esperado:** el modelo es más claro para usuarios finales: las columnas técnicas quedan ocultas, los formatos son consistentes y las columnas tienen metadatos útiles.

---

## Entregables

Al finalizar, debes tener un archivo `.pbix` con:
- Modelo en estrella con `Envíos` como tabla de hechos.
- Dimensiones relacionadas directamente con la tabla de hechos.
- Layouts creados en la vista Modelo:
  - `01 - Modelo estrella`
  - `02 - Servicio y SLA`
  - `03 - Puente Cliente Servicio`
- Relaciones principales activas con cardinalidad `1:*` y filtro cruzado simple.
- Tabla puente `ClienteServicio` con relaciones `1:*` hacia `Cliente` y `Servicio`.
- Jerarquías:
  - `Geografía`
  - `Oficinas`
  - `Servicios`
- Columnas técnicas ocultas en la vista de informe.
- Formatos, categorías de datos y resúmenes predeterminados configurados.
- Descripciones añadidas a columnas clave.

---

# Resumen

En este laboratorio has convertido las tablas preparadas en Power Query en un **modelo semántico profesional**. Has revisado el diseño en estrella, creado layouts en la vista Modelo, configurado relaciones principales, creado una tabla puente para resolver una relación conceptual varios a varios, creado jerarquías de negocio y ajustado propiedades de tablas, columnas y relaciones.

En el siguiente laboratorio crearás la tabla `Fecha`, configurarás calendarios, ordenarás columnas temporales y definirás relaciones activas e inactivas para fechas alternativas.

#### Recursos útiles
- Documentación oficial de modelado en Power BI: https://learn.microsoft.com/power-bi/transform-model/desktop-relationship-view
- Guía de esquema en estrella en Power BI: https://learn.microsoft.com/power-bi/guidance/star-schema
- Crear y administrar relaciones en Power BI: https://learn.microsoft.com/power-bi/transform-model/desktop-create-and-manage-relationships

#### 🎉 Fin del laboratorio

¡Enhorabuena! Has completado la segunda parte del laboratorio de ParcelCraft. Ahora tienes un modelo en estrella preparado para incorporar análisis temporal avanzado en el siguiente laboratorio. 👏

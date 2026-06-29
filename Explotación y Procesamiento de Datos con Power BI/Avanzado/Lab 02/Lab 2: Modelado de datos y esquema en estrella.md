# Laboratorio 2: Modelado y preparación del modelo semántico en Power BI

**ParcelCraft** es una empresa ficticia de logística que necesita convertir sus datos preparados en Power Query en un **modelo semántico robusto, mantenible y listo para análisis**.

En el laboratorio anterior transformaste un archivo CSV operativo llamado **ParcelCraft_50k.csv** en una tabla de hechos de envíos y varias dimensiones. En esta segunda parte trabajarás principalmente en la **vista Modelo** de Power BI Desktop para organizar las tablas, crear y administrar relaciones, aplicar propiedades del modelo, definir jerarquías y dejar preparado el modelo para la creación de medidas e informes.

El objetivo es pasar de un conjunto de tablas cargadas a un **modelo en estrella bien documentado**, con relaciones correctas, diseño visual claro y propiedades adecuadas para consumo por usuarios de negocio.

> ℹ️ Este laboratorio refleja una práctica frecuente en proyectos reales: después de preparar los datos en Power Query, el equipo de BI debe revisar el modelo semántico para garantizar rendimiento, claridad, consistencia y facilidad de uso.

## Objetivos de aprendizaje

**Tiempo estimado total: 45 minutos**

Al finalizar este laboratorio serás capaz de:

- Revisar y validar un **modelo en estrella** en Power BI.
- Crear pocos **layouts útiles** en la vista Modelo.
- Crear, editar y administrar **relaciones**.
- Configurar cardinalidad, dirección de filtro cruzado y relaciones activas/inactivas.
- Crear una tabla de fechas y marcarla como tabla de fechas.
- Crear jerarquías temporales, geográficas y operativas.
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

> [!NOTE]
> Tienes un archivo inicial y solución disponibles. Si vas a trabajar en el mismo archivo a lo largo de todos los laboratorios, recomiendo empezar a partir de este starter.
> - [Archivo inicial (Starter)]((https://github.com/quizeth/Labs/blob/main/Explotaci%C3%B3n%20y%20Procesamiento%20de%20Datos%20con%20Power%20BI/Avanzado/Lab%2002/Files/02-A%20Starter%20ParcelCraft.pbix))
> - [Archivo final (Solución)](https://github.com/quizeth/Labs/blob/main/Explotaci%C3%B3n%20y%20Procesamiento%20de%20Datos%20con%20Power%20BI/Avanzado/Lab%2002/Files/02-A%20Soluci%C3%B3n%20-%20ParcelCraft.pbix)
> 

### Convenciones usadas en el laboratorio

- Cuando se indique seleccionar una vista, usa el panel izquierdo de Power BI Desktop:
  - **Vista Informe**
  - **Vista Tabla**
  - **Vista Modelo**
- Cuando se indique una opción de cinta, se mostrará como **pestaña > grupo > comando**.
- Los nombres de tablas, columnas y medidas deben escribirse exactamente como se indican, salvo que tu archivo tenga nombres equivalentes ligeramente distintos.
- Si una columna no aparece, revisa que en el laboratorio anterior se haya cargado la tabla final y no solo las consultas staging.
- En este laboratorio no se rehace la preparación de Power Query; el foco está en el **modelo semántico**.

---

## Ejercicio 1: Revisar el modelo cargado y confirmar el diseño en estrella

En este ejercicio revisarás las tablas cargadas desde Power Query e identificarás qué tabla actuará como hecho y cuáles actuarán como dimensiones.

### Tarea 1: Abrir el archivo de trabajo

1. Abre **Power BI Desktop**.
2. Abre el archivo `.pbix` generado al final del laboratorio anterior.
3. Si Power BI solicita aplicar cambios pendientes de Power Query, selecciona **Aplicar cambios**.
4. Ve a la **vista Modelo**.
5. Revisa que aparecen las siguientes tablas:
   - `Cliente`
   - `Destino`
   - `Envíos`
   - `Oficina`
   - `Ruta`
   - `Servicio`

### Tarea 2: Identificar hechos y dimensiones

1. Localiza la tabla `Envíos`.
2. Confirma que contiene columnas transaccionales, métricas y claves, por ejemplo:
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
     
3. Localiza las tablas de dimensión:
   - `Cliente`: atributos del cliente.
   - `Servicio`: atributos del servicio contratado.
   - `Oficina`: atributos de la oficina o hub operativo.
   - `Destino`: atributos geográficos del destino.
   - `Ruta`: atributos de la ruta o modo de transporte.
4. Comprueba que estas tablas contienen campos descriptivos que permitirán analizar, filtrar o agrupar los envíos.
   
**Resultado esperado:** identificas `Envíos` como tabla de hechos y el resto de tablas como dimensiones. El modelo objetivo debe tener una estructura en estrella, con relaciones de uno a varios desde dimensiones hacia la tabla de hechos.

---

## Ejercicio 2: Crear layouts útiles en la vista Modelo

En este ejercicio crearás pocos layouts, pero con un propósito claro. La idea es facilitar la lectura, explicación y mantenimiento del modelo.

Trabajarás con dos layouts:

- `01 - Modelo de estrella`: vista principal del modelo completo.
- `02 - Fechas y SLA`: vista de apoyo para revisar la lógica temporal más adelante.

> Buena práctica: en modelos reales, demasiados layouts pueden generar ruido. Es preferible tener pocos layouts bien diseñados y con un objetivo claro.

### Tarea 1: Crear el layout principal del modelo estrella

1. En la **vista Modelo**, localiza las pestañas de layouts en la parte inferior.
2. Crea un nuevo layout y nómbralo `01 - Modelo estrella`
3. Arrastra `Envíos` al centro del lienzo.
4. Coloca las cinco tablas de dimensiones alrededor.

<img width="1175" height="867" alt="image" src="https://github.com/user-attachments/assets/1b858007-5447-4303-8b9f-7ead40ddc8bd" />


> Más adelante añadirás nuevas tablas al modelo. Deja espacio libre alrededor de `Envíos` para poder incorporarlas sin reorganizar todo el layout.

8. Ajusta el ancho de las tablas para que se vean las claves y columnas principales.

### Tarea 2: Crear el layout de fechas y SLA

1. Crea un segundo layout y nómbralo `02 - Fechas y SLA`.

3. Añade solo las tablas necesarias para revisar la lógica temporal:
   - `Envíos`.
   - `Servicio`.
4. Coloca `Envíos` en el centro del lienzo.
5. Coloca `Servicio` debajo o a un lado de Envíos.
6. Ajusta el ancho de las tablas para que se vean las columnas relacionadas con fechas, servicio y SLA.
   
> Nota: en un ejercicio posterior crearás la tabla Fecha. Cuando exista, volverás a este layout para revisar las relaciones temporales y la lógica de SLA.


### Tarea 3: Revisar la utilidad de los layouts

1. Vuelve al layout `01 - Modelo estrella`.
2. Comprueba que el modelo se entiende de un vistazo.
3. Verifica que `Envíos` queda claramente identificada como tabla central.
4. Cambia al layout `02 - Fechas y SLA.`
5. Comprueba que contiene solo las tablas necesarias para revisar la lógica temporal y de servicio.
6. Si una tabla no aporta valor a un layout, elimínala del layout, no necesariamente del modelo.

**Resultado esperado: **el modelo queda organizado en dos layouts útiles: uno para explicar el modelo estrella y otro para revisar, más adelante, fechas, SLA y relaciones temporales.

---
## Ejercicio 3: Crear y administrar relaciones en Power BI

En este ejercicio crearás manualmente las relaciones principales del modelo. En este archivo, la creación automática de relaciones está intencionalmente deshabilitada, por lo que Power BI no habrá generado relaciones al cargar las tablas. Esto permite construir el modelo de forma controlada y revisar explícitamente las columnas clave, la cardinalidad, la dirección de filtro cruzado y el estado activo de cada relación.

### Tarea 1: Confirmar que el modelo no tiene relaciones automáticas

1. En la **vista Modelo**, observa el lienzo del modelo.
2. Comprueba que las tablas aparecen sin líneas de relación entre ellas.

> Nota: Esto es esperado. En este archivo, la creación automática de relaciones está intencionalmente deshabilitada para que las relaciones se creen manualmente durante el laboratorio.

3. En la cinta, selecciona **Modelado > Administrar relaciones**.
4. Revisa la ventana **Administrar relaciones**.
5. Confirma que no hay relaciones creadas o que no hay relaciones relevantes para el modelo.
6. Si encuentras alguna relación no esperada, selecciónala y pulsa **Eliminar**.

### Tarea 2: Crear la relación Cliente-Envíos

1. En la ventana **Administrar relaciones**, selecciona **Nuevo**.
2. Configura la relación:
   - **Tabla 1:** `Cliente`
   - **Columna:** `ClienteID`
   - **Tabla 2:** `Envíos`
   - **Columna:** `ClienteID`
   - **Cardinalidad:** Uno a varios (`1:*`)
   - **Dirección de filtro cruzado:** Simple
   - **Hacer esta relación activa:** Activado
3. Selecciona **Aceptar**.

### Tarea 3: Crear la relación Servicio-Envíos

1. Selecciona **Nuevo**.
2. Configura la relación:
   - **Tabla 1:** `Servicio`
   - **Columna:** `ServicioID`
   - **Tabla 2:** `Envíos`
   - **Columna:** `ServicioID`
   - **Cardinalidad:** Uno a varios (`1:*`)
   - **Dirección de filtro cruzado:** Simple
   - **Hacer esta relación activa:** Activado
3. Selecciona **Aceptar**.

### Tarea 4: Crear la relación Oficina-Envíos

1. Selecciona **Nuevo**.
2. Configura la relación:
   - **Tabla 1:** `Oficina`
   - **Columna:** `OficinaID`
   - **Tabla 2:** `Envíos`
   - **Columna:** `OficinaID`
   - **Cardinalidad:** Uno a varios (`1:*`)
   - **Dirección de filtro cruzado:** Simple
   - **Hacer esta relación activa:** Activado
3. Selecciona **Aceptar**.

### Tarea 5: Crear la relación Ruta-Envíos

1. Selecciona **Nuevo**.
2. Configura la relación:
   - **Tabla 1:** `Ruta`
   - **Columna:** `RutaID`
   - **Tabla 2:** `Envíos`
   - **Columna:** `RutaID`
   - **Cardinalidad:** Uno a varios (`1:*`)
   - **Dirección de filtro cruzado:** Simple
   - **Hacer esta relación activa:** Activado
3. Selecciona **Aceptar**.

### Tarea 6: Crear la relación Destino-Envíos

1. Selecciona **Nuevo**.
2. Configura la relación:
   - **Tabla 1:** `Destino`
   - **Columna:** `DestinoID`
   - **Tabla 2:** `Envíos`
   - **Columna:** `DestinoID`
   - **Cardinalidad:** Uno a varios (`1:*`)
   - **Dirección de filtro cruzado:** Simple
   - **Hacer esta relación activa:** Activado
3. Selecciona **Aceptar**.

> Nota: en un modelo productivo suele ser preferible relacionar tablas mediante claves numéricas o claves sustitutas estables cuando estén disponibles; por eso utilizamos DestinoID en vez de quedarnos con el Código Postal, que según añadamos datos a la tabla puede dejar de ser una columna de valores únicos.

### Tarea 7: Validar las relaciones creadas

1. Cierra la ventana **Administrar relaciones**.
2. Vuelve a la **vista Modelo**.
3. Comprueba que ahora aparecen líneas de relación entre las dimensiones y `Envíos`.
4. Verifica que `Envíos` queda en el lado varios (`*`) de cada relación.
5. Verifica que las dimensiones quedan en el lado uno (`1`) de cada relación.
6. Comprueba que la dirección de filtro va desde las dimensiones hacia `Envíos`.

**Resultado esperado:** el modelo tiene relaciones activas desde cada dimensión hacia `Envíos`, con cardinalidad uno a varios y dirección de filtro cruzado simple.


---

## Ejercicio 4: Crear tabla de fechas y relaciones activas e inactivas

En este ejercicio crearás una dimensión de fechas para analizar envíos por fecha de envío, creación, entrega y compromiso SLA.

Power BI puede crear automáticamente tablas de fecha ocultas para cada columna de tipo fecha. Esto puede ser cómodo en modelos simples, pero en modelos semánticos profesionales suele ser preferible usar una **tabla de calendario explícita**, porque permite controlar las columnas temporales, las jerarquías, las relaciones y el comportamiento de filtrado.

### Tarea 1: Deshabilitar Fecha/Hora automática

1. En Power BI Desktop, selecciona **Archivo > Opciones y configuración > Opciones**.
2. En la ventana **Opciones**, revisa la sección **Archivo actual**.
3. Selecciona **Carga de datos**.
4. En la sección **Inteligencia de tiempo**, desactiva la opción `Fecha/Hora automática`.
5. Selecciona **Aceptar**.

> **Nota**: al deshabilitar **Fecha/Hora automática**, Power BI deja de crear tablas de fecha ocultas para cada columna de fecha del modelo. Esto reduce ruido, evita calendarios duplicados y favorece el uso de una única tabla de calendario controlada por el diseñador del modelo.


### Tarea 2: Crear una tabla calculada de fechas

1. Ve a la **vista Tabla** o **vista Modelo**.
2. En la cinta, selecciona **Modelado > Nueva tabla**.
3. Escribe la siguiente expresión DAX:

```DAX
Fecha =
VAR FechasBase =
    UNION(
        SELECTCOLUMNS(
            'Envíos',
            "FechaBase", DATEVALUE('Envíos'[FechaEnvío])
        ),
        SELECTCOLUMNS(
            FILTER('Envíos', NOT ISBLANK('Envíos'[FechaEntrega])),
            "FechaBase", DATEVALUE('Envíos'[FechaEntrega])
        ),
        SELECTCOLUMNS(
            FILTER('Envíos', NOT ISBLANK('Envíos'[FechaCompromisoSLA])),
            "FechaBase", DATEVALUE('Envíos'[FechaCompromisoSLA])
        )
    )
VAR FechaMinima =
    MINX(FechasBase, [FechaBase])
VAR FechaMaxima =
    MAXX(FechasBase, [FechaBase])
RETURN
ADDCOLUMNS(
    CALENDAR(FechaMinima, FechaMaxima),
    "Año", YEAR([Date]),
    "Número de mes", MONTH([Date]),
    "Mes", FORMAT([Date], "mmmm"),
    "Año mes", FORMAT([Date], "yyyy-MM"),
    "Trimestre", "Q" & FORMAT([Date], "Q"),
    "Día", DAY([Date]),
    "Día de la semana", FORMAT([Date], "dddd"),
    "Número día semana", WEEKDAY([Date], 2),
    "Semana ISO", WEEKNUM([Date], 21),
    "Año ISO", YEAR([Date] - WEEKDAY([Date], 2) + 4),
    "Año semana ISO",
        FORMAT(YEAR([Date] - WEEKDAY([Date], 2) + 4), "0000")
        & "-S"
        & FORMAT(WEEKNUM([Date], 21), "00"),
    "Inicio semana", [Date] - WEEKDAY([Date], 2) + 1,
    "Fin semana", [Date] - WEEKDAY([Date], 2) + 7
)
```
> Esta tabla de calendario se crea con DAX y cubre el intervalo entre la fecha mínima de envío y la fecha máxima disponible entre fecha de entrega y fecha de envío. Más adelante podrás ampliarla si necesitas analizar años futuros, objetivos o presupuestos.
>
> Es muy común crear una tabla de fechas a partir de una función como `CALENDAR()` o `CALENDARAUTO()` y añadir las columnas manualmente de forma individual. Sin embargo, este enfoque nos permite definir todo el esquema de la tabla de forma fácilmente repetible.

4. Cambia el nombre de la columna `Date` a: `Fecha`

> **Importante**: Cambia los metadatos de tablas y columnas en Power BI únicamente cuando estén creadas en DAX. Si vienen de Power Query, aplica todas las transformaciones allí.

### Tarea 3: Marcar la tabla como tabla de fechas

1. Selecciona la tabla `Fecha`.
2. En la cinta, selecciona **Herramientas de tabla > Marcar como tabla de fechas**.
3. Selecciona la columna `Fecha`.
4. Pulsa **Aceptar**.

Power BI validará que la columna seleccionada contiene valores de fecha válidos y puede actuar como columna principal de fechas.

> **Importante**: Marcar como tabla de fechas no crea una tabla nueva. Solo indica a Power BI que la tabla Fecha debe usarse como referencia temporal principal del modelo.

**Resultado esperado: **la tabla Fecha queda marcada como tabla de fechas del modelo.

### Tarea 4: Crear Calendarios basados en la tabla Fecha
En esta tarea crearás uno o dos calendarios sobre la tabla Fecha. Estos calendarios no sustituyen a la tabla Fecha; son definiciones temporales que indican a Power BI qué columnas representan año, trimestre, mes, semana o día.

> Nota: si no ves las opciones de Calendario, puede que necesites habilitar la característica de vista previa correspondiente en **Archivo > Opciones y configuración > Opciones > Características de versión preliminar**. El nombre puede variar según la versión de Power BI Desktop.

#### Crear un calendario gregoriano

1. Selecciona la tabla `Fecha`.
2. En la cinta **Herramientas de tabla**, busca la opción de **Opciones del calendario**.
3. Selecciona Nuevo calendario.
4. Asigna el nombre `Calendario Gregoriano`.
5. Configura las categorías temporales con estas columnas:

| Categoría   | Columna principal | Columnas asociadas                             |
| ----------- | ----------------- | ---------------------------------------------- |
| Año         | `Año`             | —                                              |
| Trimestre   | `Trimestre`       | —                                              |
| Mes         | `Año mes`         | —                                              |
| Mes del año | `Mes`             | `Número de mes`                                |
| Fecha       | `Fecha`           |  |

6. Selecciona **Validar datos**.
7. Si la validación no muestra errores, selecciona **Guardar y cerrar**.

> Nota: usa Año mes como columna principal de la categoría Mes porque identifica cada mes de forma única dentro de un año, por ejemplo 2023-01. La columna Mes, por sí sola, representa el nombre del mes y se repite cada año.

#### Crear un calendario ISO semanal

1. Manténte en la ventana **Opciones de calendario para Fecha**.
3. Selecciona **Nuevo calendario**.
4. En **Nombre del calendario**, escribe `Calendario ISO`.
5. En la pestaña **Categorías**, agrega las siguientes categorías:

| Categoría | Columna principal | Columnas asociadas                    |
| --------- | ----------------- | ------------------------------------- |
| Año       | Año ISO           | —                                     |
| Semana    | Año semana ISO    | Semana ISO, Inicio semana, Fin semana |
| Fecha     | Fecha             |    |

6. Selecciona **Validar datos**.
7. Si la validación no muestra errores, selecciona **Guardar y cerrar**.

> Importante: no asignes la misma columna a varias categorías dentro del mismo calendario. Si Power BI indica que una columna ya está en uso, elimina esa columna de las columnas asociadas o crea una columna alternativa específica para ese uso.

### Tarea 5: Crear relaciones con la tabla Fecha

En este modelo, la tabla `Envíos` contiene varias fechas de negocio. Usarás `FechaEnvío` como relación activa principal y crearás relaciones inactivas para el resto de fechas.

1. Ve a al diseño `02 - Fechas y SLA` en la **vista de modelo**.
2. Añade la tabla `Fecha` al lienzo.
3. Crea las siguientes relaciones entre `Fecha` y `Envíos`:

| Tabla 1 | Columna | Tabla 2 | Columna | Cardinalidad | Filtro cruzado | Activa |
|---|---|---|---|---|---|---|
| `Fecha` | `Fecha` | `Envíos` | `FechaEnvío` | Uno a varios (`1:*`) | Simple | **Sí** |
| `Fecha` | `Fecha` | `Envíos` | `FechaCreación` | Uno a varios (`1:*`) | Simple | No |
| `Fecha` | `Fecha` | `Envíos` | `FechaActualización` | Uno a varios (`1:*`) | Simple | No |
| `Fecha` | `Fecha` | `Envíos` | `FechaEntrega` | Uno a varios (`1:*`) | Simple | No |
| `Fecha` | `Fecha` | `Envíos` | `FechaCompromisoSLA` | Uno a varios (`1:*`) | Simple | No |


> Nota: Power BI solo puede mantener una relación activa directa entre `Fecha` y `Envíos` para evitar ambigüedad en los filtros. Las relaciones inactivas podrán utilizarse más adelante en medidas DAX mediante `USERELATIONSHIP`.

**Resultado esperado:** `FechaEnvío` queda como fecha principal del modelo y el resto de fechas queda disponible para análisis específicos mediante relaciones inactivas.

---

# Ejercicio 5: Resolver una relación varios a varios con una tabla puente

En este ejercicio explorarás una relación conceptual de varios a varios entre `Cliente` y `Servicio` sin usar DAX.

En el modelo de ParcelCraft, un cliente puede utilizar varios servicios y un servicio puede ser utilizado por muchos clientes. Aunque esta relación existe en los datos, no conviene resolverla creando una relación directa varios a varios entre las dimensiones. En su lugar, crearás una **tabla puente** desde Power Query con las combinaciones únicas de cliente y servicio observadas en la tabla de hechos `Envíos`.

Este patrón permite representar la relación `Cliente` ↔ `Servicio` mediante dos relaciones de uno a varios (`1:*`):

- `Cliente` → `Puente Cliente Servicio`
- `Servicio` → `Puente Cliente Servicio`

> Nota: este ejercicio es didáctico. El modelo principal seguirá usando `Envíos` como tabla de hechos para analizar métricas operativas y económicas. La tabla puente se usará para representar la asociación entre clientes y servicios sin crear una relación directa `varios a varios (*:*)` entre dimensiones.

---

## Tarea 1: Comprobar el escenario varios a varios

En esta tarea comprobarás por qué `Cliente` y `Servicio` forman una relación conceptual de varios a varios.

1. Ve a la **vista Informe**.
2. Añade una visualización de tabla.
4. Agrega los campos `Cliente[Cliente]` y `Servicio[Servicio]`.
6. Revisa el resultado.

La visualización no represente correctamente qué servicios están realmente asociados a cada cliente, porque `Cliente` y `Servicio` son dos dimensiones independientes.

> Observación: esto no significa que el modelo esté mal. En un modelo en estrella, las dimensiones normalmente filtran a la tabla de hechos, pero no se filtran directamente entre sí. Para representar una asociación directa entre dos dimensiones, puedes usar una tabla puente.

---

## Tarea 2: Crear la tabla puente en Power Query

En esta tarea crearás una tabla puente a partir de las combinaciones únicas de cliente y servicio presentes en `Envíos`.

1. En la cinta **Inicio**, selecciona **Transformar datos**.
2. En el Editor de Power Query, localiza la consulta `stg_ParcelCraft_Clean`.
3. Haz clic derecho sobre `stg_ParcelCraft_Clean` y selecciona **Referencia**.
4. Cambia el nombre de la nueva consulta a `ClienteServicio`.
5. Mueve la tabla a la sección Auxiliares. **No** deshabilites la carga.
6. Con la consulta `ClienteServicio` seleccionada, ve a **Inicio > Administrar columnas > Elegir columnas**.
7. Selecciona únicamente estas columnas:
   - `ClienteID`
   - `ServicioID`
8. Selecciona **Aceptar**.
9. Selecciona ambas columnas: `ClienteID` y `ServicioID`.
10. En la cinta, selecciona **Inicio > Quitar filas > Quitar duplicados**.
11. Revisa que la consulta contiene una fila por cada combinación única de cliente y servicio.
12. Ordena la columna **ClienteID** en orden ascendente.
13. Selecciona **Cerrar y aplicar**.

> Buena práctica: una tabla puente debe ser estrecha y contener solo las claves necesarias para resolver la relación. Si necesitas importes, pesos, fechas o recuentos de envíos, esos campos deben permanecer en la tabla de hechos `Envíos`.

**Resultado esperado:** el modelo contiene una nueva tabla cargada llamada `ClienteServicio`, con las columnas `ClienteID` y `ServicioID`.

---

## Tarea 3: Crear un layout para la tabla puente

1. Ve a la **vista Modelo**.
2. Crea un nuevo layout llamado `03 - Puente Cliente Servicio`.
4. Añade al layout las tablas:
   - `Cliente`
   - `ClienteServicio`
   - `Servicio`
5. Coloca `Puente Cliente Servicio` en el centro.
6. Coloca `Cliente` a la izquierda.
7. Coloca `Servicio` a la derecha.

**Resultado esperado:** el layout muestra claramente que la tabla puente se sitúa entre las dos dimensiones.

---

## Tarea 4: Crear relaciones con la tabla puente

1. En la cinta, selecciona **Modelado > Administrar relaciones**.
2. Selecciona **Detección automática**.
3. Si tu modelo está bien configurado, se habrán detectado estas dos nuevas relaciones:

| Tabla desde | Columna desde | Cardinalidad | Dirección de filtro cruzado | Tabla hasta | Columna hasta | Estado |
|---|---|---:|---|---|---|---|
| `ClienteServicio` | `ClienteID` | `*:1` | Simple | `Cliente` | `ClienteID` | Activo |
| `ClienteServicio` | `ServicioID` | `*:1` | Simple | `Servicio` | `ServicioID` | Activo |

7. Selecciona **Aceptar**.
8. Cierra la ventana **Administrar relaciones**.

> Importante: no crees una relación directa entre `Cliente` y `Servicio`. Tampoco conectes `ClienteServicio` con `Envíos` en este ejercicio. Aunque podría ser interesante tener múltiples filtros, el objetivo es resolver la relación conceptual entre dimensiones sin introducir rutas de filtro redundantes hacia la tabla de hechos.

9. Vuelve a la vista de informe.
10. Añade una nueva visualización de tabla.
11. Agrega los campos:
   - `Cliente[Cliente]`
   - `ClienteServicio[ServicioID]`
4. En el área de campos de la visualización, abre el menú desplegable de `ServicioID`.
5. Cambia la agregación a `Recuento distintivo`
6. Revisa el resultado. Cada cliente debe mostrar el número de servicios realmente asociados en la tabla puente.
> En el conjunto de datos de prueba, es posible que siga habiendo 4 servicios por cliente.

7. Añade otra visualización de tabla.
8. Agrega los campos:
   - `Servicio[Servicio]`
   - `Puente Cliente Servicio[ClienteID]`
9. En el menú desplegable de `ClienteID`, cambia la agregación a `Recuento distintivo`
19. Revisa el resultado. Cada servicio debe mostrar el número de clientes asociados en la tabla puente.
> En el conjunto de datos de prueba, es posible que siga habiendo 200 clientes por servicio.

**Resultado esperado:** puedes analizar asociaciones entre clientes y servicios usando la tabla puente.

---

## Ejercicio 6: Crear jerarquías

En este ejercicio crearás jerarquías para mejorar la navegación y el autoservicio en los informes.

### Tarea 1: Crear jerarquía de fechas

1. Ve a la vista de modelo.
2. En el panel **Datos**, expande la tabla `Fecha`.
3. Haz clic derecho sobre la columna `Año`.
4. Selecciona **Crear jerarquía**.
5. Cambia el nombre de la jerarquía a `Fechas`.
6. Arrastra las siguientes columnas dentro de la jerarquía, en este orden:
   - `Año`
   - `Trimestre`
   - `Mes`
   - `Fecha`

### Tarea 2: Crear jerarquía geográfica de destino

1. Expande la tabla `Destino`.
2. Haz clic derecho sobre `Región (Destino)`.
3. Selecciona **Crear jerarquía**.
4. Cambia el nombre a `Geografía`.

5. Añade las columnas en este orden:
   - `Región (Destino)`
   - `Ciudad (Destino)`
   - `Código Postal (Destino)`

### Tarea 3: Crear jerarquía operativa de oficina

1. Expande la tabla `Oficina`.
2. Crea una jerarquía llamada `Oficinas`.
3. Añade las columnas en este orden:
   - `Región`
   - `Ciudad`
   - `Oficina`

**Resultado esperado:** el modelo contiene jerarquías reutilizables para análisis temporal, geográfico, operativo y de servicio.

---

## Ejercicio 7: Configurar propiedades de tablas y columnas

En este ejercicio mejorarás la experiencia de usuario del modelo ocultando columnas técnicas, configurando formatos y añadiendo metadatos.

### Tarea 1: Ocultar columnas de claves técnicas

1. En la **vista Modelo**, haz clic en el panel de Datos y selecciona **Expandir todo**.
2. Oculta las columnas que no deberían usarse directamente en visualizaciones:
   - `ClienteID` (en todas las tablas)
   - `DestinoID` (en todas las tablas)
   - `OficinaID` (en todas las tablas)
   - `RutaID` (en todas las tablas)
   - `ServicioID` (en todas las tablas)
   Para ocultar una columna:
   - Haz clic derecho sobre la columna.
   - Selecciona **Ocultar en la vista de informe**.
3. En las dimensiones, conserva visibles las claves solo si aportan valor analítico. 

> Buena práctica: las claves técnicas son esenciales para relaciones, pero generalmente no deben exponerse al usuario final.

### Tarea 2: Configurar formatos numéricos

1. Selecciona `Envíos[Precio total]`.
2. En **Herramientas de columna**, configura:
   - **Formato:** Moneda
   - **Decimales:** 2
   - **Símbolo:** € Español (España)
3. Repite para:
   - `Precio base`
   - `Surplus fuel`

### Tarea 3: Configurar categorías de datos

1. Selecciona `Destino[Ciudad (Destino)]`.
2. En **Propiedades > Avanzado > Categoría de datos**, selecciona: `Ciudad`
3. Selecciona `Destino[Código Postal (Destino)]`.
4. Configura la categoría como `Código postal`.
5. Selecciona `Destino[Región (Destino)].
6. Configura la categoría como `País o región`.
7. Repite los pasos para las columnas `Ciudad`y `Región`de la tabla `Oficina`.


### Tarea 4: Configurar resumen predeterminado

1. Selecciona las columas `Precio total`, `Peso (KG)` o `Intentos`.
2. En la sección **Propiedades > Avanzado > Resumir por**, configúralas:
   - `Precio total`: Suma
   - `Peso (KG)`: Suma o Promedio, según criterio analítico
   - `Intentos`: Suma o Promedio, según el análisis esperado

### Tarea 5: Añadir descripciones

1. En la tabla `Envíos`, selecciona la columna `EntregaTardia`.
2. En el panel **Propiedades**, localiza **Descripción**.
3. Escribe:

```text
Indica si la entrega se realizó después de la fecha y hora comprometida por SLA.
```

4. Añade descripciones a estas columnas:
   - `FechaCompromisoSLA`: Fecha y hora máxima esperada de entrega según el SLA.
   - `Precio total`: Importe total del envío en euros.
   - `Intentos`: Número de intentos de entrega registrados.

**Resultado esperado:** el modelo es más claro para usuarios finales: las columnas técnicas quedan ocultas, los formatos son consistentes y las columnas tienen metadatos útiles.

---

## Entregables

Al finalizar, debes tener un archivo `.pbix` con:

1. Modelo en estrella con `Envíos` como tabla de hechos.
2. Dimensiones relacionadas directamente con la tabla de hechos.
3. Layouts creados en la vista Modelo:
   - `01 - Modelo estrella`
   - `02 - Fechas y logística`
   - `03 - Dimensiones`
4. Relaciones principales activas con cardinalidad `1:*` y filtro cruzado simple.
5. Tabla `Fecha` marcada como tabla de fechas.
6. Relaciones inactivas para fechas alternativas.
7. Jerarquías:
   - `Fechas`
   - `Geografía`
   - `Oficinas`
8. Columnas técnicas ocultas en la vista de informe.
9. Formatos, categorías de datos y resúmenes predeterminados configurados.
10. Descripciones añadidas a columnas clave.

---

## Resumen

En este laboratorio has convertido las tablas preparadas en Power Query en un **modelo semántico profesional**. Has revisado el diseño en estrella, creado layouts en la vista Modelo, configurado relaciones, añadido una tabla de fechas, creado jerarquías y ajustado propiedades de tablas, columnas y relaciones .

Estas tareas son fundamentales para construir informes Power BI escalables, fáciles de mantener y comprensibles para usuarios de negocio.

### Recursos útiles

- Documentación oficial de modelado en Power BI: https://learn.microsoft.com/power-bi/transform-model/desktop-relationship-view
- Guía de esquema en estrella en Power BI: https://learn.microsoft.com/power-bi/guidance/star-schema
- Crear y administrar relaciones en Power BI: https://learn.microsoft.com/power-bi/transform-model/desktop-create-and-manage-relationships
- Marcar una tabla como tabla de fechas: https://learn.microsoft.com/power-bi/transform-model/desktop-date-tables

### 🎉 Fin del laboratorio

¡Enhorabuena! Has completado la segunda parte del laboratorio de ParcelCraft. Ahora tienes un modelo semántico preparado para construir análisis de ingresos, entregas tardías, SLA, operaciones logísticas, rutas, clientes y servicios. 👏

# Laboratorio 3: Fundamentos de DAX y tabla de fechas en Power BI

**ParcelCraft** necesita empezar a construir cálculos analíticos reutilizables sobre el modelo semántico creado en los laboratorios anteriores.

En este laboratorio crearás tablas calculadas, columnas calculadas y medidas con DAX. También crearás y configurarás la tabla `Fecha`, sus calendarios y sus relaciones con `Envíos`, de forma que el modelo quede preparado para cálculos temporales y análisis operativo.

## Objetivos de aprendizaje

**Tiempo estimado total: 30-45 minutos**

Al finalizar este laboratorio serás capaz de:

- Crear tablas calculadas con DAX.
- Crear columnas calculadas con DAX.
- Crear y formatear medidas básicas.
- Crear y configurar una tabla de fechas.
- Marcar una tabla como tabla de fechas.
- Crear calendarios sobre la tabla `Fecha`.
- Crear relaciones activas e inactivas entre `Fecha` y `Envíos`.
- Validar cálculos DAX en visualizaciones.

---

## Antes de empezar

### Requisitos

Para completar este laboratorio necesitas:

- Power BI Desktop instalado.
- Haber completado el **Laboratorio 2: Modelado de datos y esquema en estrella en Power BI**.
- Un modelo con las tablas `Envíos`, `Cliente`, `Servicio`, `Oficina`, `Destino` y `Ruta`.
- Conocimientos básicos de relaciones en Power BI.

### Convenciones usadas en el laboratorio

- Cuando se indique una opción de cinta, se mostrará como **pestaña > grupo > comando**.
- Los nombres de tablas, columnas y medidas deben escribirse exactamente como se indican.
- Las expresiones DAX se escriben en la barra de fórmulas de Power BI Desktop.

---

## Ejercicio 1: Crear y configurar la tabla Fecha

En este ejercicio crearás una dimensión de fechas para analizar envíos por fecha de envío, creación, actualización, entrega y compromiso SLA.

Power BI puede crear automáticamente tablas de fecha ocultas para cada columna de tipo fecha. Esto puede ser cómodo en modelos simples, pero en modelos semánticos profesionales suele ser preferible usar una **tabla de calendario explícita**.

### Tarea 1: Deshabilitar Fecha/Hora automática

1. En Power BI Desktop, selecciona **Archivo > Opciones y configuración > Opciones**.
2. En **Archivo actual**, selecciona **Carga de datos**.
3. En la sección **Inteligencia de tiempo**, desactiva **Fecha/Hora automática**.
4. Selecciona **Aceptar**.

> Nota: al deshabilitar **Fecha/Hora automática**, Power BI deja de crear tablas de fecha ocultas para cada columna de fecha del modelo.

### Tarea 2: Crear una tabla calculada de fechas

1. Ve a la **vista Modelo**.
2. Selecciona **Modelado > Nueva tabla**.
3. Escribe la siguiente expresión DAX:

```DAX
Fecha =
VAR FechasBase =
    UNION(
        SELECTCOLUMNS('Envíos', "FechaBase", DATEVALUE('Envíos'[FechaEnvío])),
        SELECTCOLUMNS(FILTER('Envíos', NOT ISBLANK('Envíos'[FechaCreación])), "FechaBase", DATEVALUE('Envíos'[FechaCreación])),
        SELECTCOLUMNS(FILTER('Envíos', NOT ISBLANK('Envíos'[FechaActualización])), "FechaBase", DATEVALUE('Envíos'[FechaActualización])),
        SELECTCOLUMNS(FILTER('Envíos', NOT ISBLANK('Envíos'[FechaEntrega])), "FechaBase", DATEVALUE('Envíos'[FechaEntrega])),
        SELECTCOLUMNS(FILTER('Envíos', NOT ISBLANK('Envíos'[FechaCompromisoSLA])), "FechaBase", DATEVALUE('Envíos'[FechaCompromisoSLA]))
    )
VAR FechaMinima = MINX(FechasBase, [FechaBase])
VAR FechaMaxima = MAXX(FechasBase, [FechaBase])
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
    "Año semana ISO", FORMAT(YEAR([Date] - WEEKDAY([Date], 2) + 4), "0000") & "-S" & FORMAT(WEEKNUM([Date], 21), "00"),
    "Inicio semana", [Date] - WEEKDAY([Date], 2) + 1,
    "Fin semana", [Date] - WEEKDAY([Date], 2) + 7
)
```

4. Presiona **Entrar**.
5. Cambia el nombre de la columna `Date` a `Fecha`.

### Tarea 3: Marcar la tabla como tabla de fechas

1. Selecciona la tabla `Fecha`.
2. Selecciona **Herramientas de tabla > Marcar como tabla de fechas**.
3. Selecciona la columna `Fecha`.
4. Pulsa **Aceptar**.

> Importante: **Marcar como tabla de fechas** no crea una tabla nueva. Solo indica a Power BI que la tabla `Fecha` debe usarse como referencia temporal principal del modelo.

### Tarea 4: Crear calendarios basados en la tabla Fecha

Si tu versión de Power BI Desktop muestra **Opciones del calendario**, crea estos calendarios sobre la tabla `Fecha`.

#### Calendario Gregoriano

| Categoría | Columna principal | Columnas asociadas |
|---|---|---|
| Año | `Año` | — |
| Trimestre | `Trimestre` | — |
| Mes | `Año mes` | — |
| Mes del año | `Mes` | `Número de mes` |
| Fecha | `Fecha` | — |

#### Calendario ISO

| Categoría | Columna principal | Columnas asociadas |
|---|---|---|
| Año | `Año ISO` | — |
| Semana | `Año semana ISO` | `Semana ISO`, `Inicio semana`, `Fin semana` |
| Fecha | `Fecha` | — |

> Nota: no asignes la misma columna a varias categorías dentro del mismo calendario.

### Tarea 5: Ordenar columnas temporales

1. Selecciona `Fecha[Mes]`.
2. Selecciona **Herramientas de columna > Ordenar por columna**.
3. Elige `Número de mes`.
4. Selecciona `Fecha[Día de la semana]`.
5. Ordénala por `Número día semana`.

### Tarea 6: Crear relaciones con Envíos

Crea estas relaciones entre `Fecha` y `Envíos`:

| Tabla 1 | Columna | Tabla 2 | Columna | Cardinalidad | Filtro cruzado | Activa |
|---|---|---|---|---|---|---|
| `Fecha` | `Fecha` | `Envíos` | `FechaEnvío` | Uno a varios (`1:*`) | Simple | **Sí** |
| `Fecha` | `Fecha` | `Envíos` | `FechaCreación` | Uno a varios (`1:*`) | Simple | No |
| `Fecha` | `Fecha` | `Envíos` | `FechaActualización` | Uno a varios (`1:*`) | Simple | No |
| `Fecha` | `Fecha` | `Envíos` | `FechaEntrega` | Uno a varios (`1:*`) | Simple | No |
| `Fecha` | `Fecha` | `Envíos` | `FechaCompromisoSLA` | Uno a varios (`1:*`) | Simple | No |

**Resultado esperado:** `FechaEnvío` queda como fecha principal del modelo y el resto de fechas queda disponible mediante relaciones inactivas.

---

## Ejercicio 2: Crear tablas calculadas con DAX

### Tarea 1: Crear una tabla de medidas

1. Selecciona **Modelado > Nueva tabla**.
2. Escribe:

```DAX
Medidas =
ROW("Ocultar", 1)
```

3. Oculta la columna `Medidas[Ocultar]` en la vista de informe.

**Resultado esperado:** tienes una tabla para organizar medidas.

---

## Ejercicio 3: Crear medidas derivadas y columnas calculadas en la tabla Fecha

En este ejercicio crearás medidas derivadas para analizar margen, entregas y duración media. También crearás columnas calculadas en la tabla `Fecha`, donde sí tiene sentido almacenar atributos temporales que se usarán como filtros, segmentadores o agrupaciones.

> Buena práctica: usa **medidas** para cálculos agregados que deben responder al contexto de filtro. Usa **columnas calculadas** cuando necesites atributos persistidos para segmentar, clasificar, ordenar o crear jerarquías.

---
### Tarea 1: Crear medidas base de envíos e ingresos

Antes de crear medidas derivadas, necesitas algunas medidas base que serán reutilizadas por otros cálculos.

1. En la tabla `Medidas`, crea la siguiente medida:

```
Total envíos =
COUNTROWS('Envíos')
```
> Esta medida cuenta el número de filas de la tabla Envíos.
> Como cada fila representa un envío, `COUNTROWS('Envíos')` devuelve el número total de envíos dentro del contexto de filtro actual.
> Por ejemplo, si una visualización está filtrada por un servicio concreto, la medida devolverá únicamente el número de envíos de ese servicio.

2. Crea una segunda medida:
```
Ingresos =
SUM('Envíos'[Precio total])
```
> Esta medida suma el importe total de los envíos.
Como es una medida, responde al contexto de filtro actual: por servicio, cliente, oficina, destino, fecha o cualquier otro filtro aplicado en el informe.

3. Configura la medida `Total envíos`:
   - **Formato**: Número entero
   - **Separador de miles**: Toggle on
   - **Decimales**: 0
   - **Carpeta para mostrar**: Indicadores operativos

4. Configura la medida `Ingresos`:
   - **Formato**: Moneda
   - **Decimales**: 2
   - **Carpeta para mostrar**: Indicadores económicos


### Tarea 2: Crear una medida de margen estimado

1. En la tabla `Medidas`, crea la siguiente medida:

```
Margen estimado =
SUMX(
    'Envíos',
    'Envíos'[Precio total]
        - 'Envíos'[Precio base]
        - 'Envíos'[Surplus fuel]
)
```
> Esta medida calcula el margen estimado fila a fila y después suma el resultado.
> La función `SUMX` recorre cada fila de la tabla Envíos, calcula:
> `'Envíos'[Precio total] - 'Envíos'[Precio base] - 'Envíos'[Surplus fuel]`
> y finalmente suma todos esos resultados dentro del contexto de filtro actual.
> 
> La clave es que **no usamos `SUM('Envíos'[Precio total] - ...)`**, porque `SUM` solo agrega una columna. Como el cálculo combina varias columnas por fila, usamos `SUMX`.

2. Configura la medida:
  - **Formato**: Moneda
  - **Decimales**: 2
  - **Carpeta para mostrar**: Indicadores económicos

### Tarea 3: Crear medidas base de envíos e ingresos

1. En la tabla `Medidas`, crea la siguiente medida:
```
Margen medio por envío =
DIVIDE(
    [Margen estimado],
    [Total envíos]
)
```
> Esta medida calcula el margen medio por envío. Reutiliza dos medidas creadas previamente: `[Margen estimado]` como numerador y `[Total envíos]` como denominador.
> Se usa `DIVIDE` en lugar del operador / porque `DIVIDE` gestiona de forma segura divisiones entre cero o valores en blanco.

2. Configura la medida:
  - **Formato**: Moneda
  - **Decimales**: 2
  - **Carpeta para mostrar**: Indicadores económicos

### Tarea 4: Crear una medida de porcentaje de margen

1. En la tabla `Medidas`, crea la siguiente medida:
```
% margen estimado =
DIVIDE(
    [Margen estimado],
    [Ingresos]
)
```
> Esta medida calcula qué proporción de los ingresos queda como margen estimado. Reutiliza la medida `[Margen estimado]` y la compara contra la medida `[Ingresos]`.
> Al ser una medida, el porcentaje cambia automáticamente según el contexto de filtro de la visualización.

2. Configura la medida:
  - **Formato**: Porcentaje
  - **Decimales**: 2
  - **Carpeta para mostrar**: Indicadores económicos

### Tarea 5: Crear una medida de días medios hasta entrega

1. En la tabla `Medidas`, crea la siguiente medida:
```
Días medios hasta entrega =
AVERAGEX(
    FILTER(
        'Envíos',
        NOT ISBLANK('Envíos'[FechaEntrega])
    ),
    DATEDIFF(
        'Envíos'[FechaEnvío],
        'Envíos'[FechaEntrega],
        DAY
    )
)
```
> Esta medida calcula el número medio de días transcurridos entre la fecha de envío y la fecha de entrega. Primero, `FILTER` conserva únicamente los envíos que tienen `FechaEntrega`. Después, `AVERAGEX` recorre esos envíos, calcula la diferencia de días con `DATEDIFF` y devuelve el promedio.
> Se excluyen los envíos sin fecha de entrega para evitar que los registros pendientes distorsionen el promedio.

2. Configura la medida:
  - **Formato**: Número decimal
  - **Decimales**: 2
  - **Carpeta para mostrar**: Indicadores operativos

### Tarea 6: Crear una medida de días máximos hasta entrega

1. En la tabla `Medidas`, crea la siguiente medida:
```
Días máximos hasta entrega =
MAXX(
    FILTER(
        'Envíos',
        NOT ISBLANK('Envíos'[FechaEntrega])
    ),
    DATEDIFF(
        'Envíos'[FechaEnvío],
        'Envíos'[FechaEntrega],
        DAY
    )
)
```
> Esta medida identifica el mayor tiempo de entrega dentro del contexto seleccionado.`MAXX` recorre los envíos con fecha de entrega, calcula los días transcurridos y devuelve el valor máximo.
> Esta medida es útil para detectar casos extremos por servicio, oficina, destino o periodo.

2. Configura la medida:
  - **Formato**: Número entero
  - **Decimales**: 0
  - **Carpeta para mostrar**: Indicadores operativos

### Tarea 7: Crear una medida de envíos con entrega

1. En la tabla `Medidas`, crea la siguiente medida:
```
Envíos con entrega = 
CALCULATE(
    [Total envíos],
    NOT ISBLANK('Envíos'[FechaEntrega]))
```
> Esta medida cuenta únicamente los envíos que tienen fecha de entrega informada.`CALCULATE` evalúa `[Total envíos]` en un contexto de filtro modificado, conservando solo las filas donde FechaEntrega no está en blanco.

2. Configura la medida:
  - **Formato**: Número entero
  - **Decimales**: 2
  - **Carpeta para mostrar**: Indicadores operativos

### Tarea 8: Crear una medida de porcentaje de envíos entregados

1. En la tabla `Medidas`, crea la siguiente medida:
```
% envíos entregados =
DIVIDE(
    [Envíos con entrega],
    [Total envíos]
)
```
> Esta medida muestra qué porcentaje de envíos tiene fecha de entrega registrada. Reutiliza `[Envíos con entrega]` como numerador y `[Total envíos]` como denominador.

2. Configura la medida:
  - **Formato**: Porcentaje
  - **Decimales**: 2
  - **Carpeta para mostrar**: Indicadores operativos

---

## Ejercicio 4: Crear columnas calculadas con DAX
Ahora crearás columnas calculadas en la tabla Fecha. Estas columnas se almacenan en el modelo y son útiles como atributos temporales.

### Tarea 1: Crear una columna calculada de fin de semana en Fecha
1. Selecciona la tabla Fecha y crea una nueva columna:
```
Es fin de semana =
WEEKDAY(
    Fecha[Fecha],
    2
) >= 6
```
> Esta columna devuelve `TRUE` para sábados y domingos, y `FALSE` para el resto de días.
> El segundo argumento de `WEEKDAY` se establece en 2, por lo que la semana empieza en lunes:
lunes = 1, martes = 2, ..., domingo = 7.


### Tarea 2: Crear una columna calculada de tipo de día

1. En la tabla Fecha, crea esta columna:
```
Tipo de día =
IF(
    Fecha[Es fin de semana],
    "Fin de semana",
    "Laborable"
)
```
> Esta columna clasifica cada fecha como `Fin de semana` o `Laborable`.
> En este punto, Laborable significa únicamente que no es sábado ni domingo. No se están teniendo en cuenta festivos nacionales, autonómicos ni locales.


### Tarea 3: Crear una columna calculada de temporada operativa

1. En la tabla Fecha, crea esta columna:
```
Temporada operativa =
SWITCH(
    TRUE(),
    Fecha[Número de mes] IN { 11, 12 }, "Alta actividad",
    Fecha[Número de mes] IN { 7, 8 }, "Verano",
    Fecha[Número de mes] IN { 1, 2 }, "Inicio de año",
    "Actividad regular"
)
```
> Esta columna permite segmentar el análisis por periodos operativos aproximados.
> Por ejemplo, noviembre y diciembre se clasifican como `Alta actividad`, lo que puede ser útil para analizar campañas, picos logísticos o periodos de mayor demanda.

### Tarea 4: Crear una columna calculada de día hábil de semana

1. En la tabla Fecha, crea esta columna:
```
Día hábil semana = 
IF(
    Fecha[Es fin de semana],
    BLANK(),
    Fecha[Número día semana])
```
> Esta columna devuelve el número del día de la semana únicamente para días de lunes a viernes. Para sábados y domingos devuelve `BLANK()`.
> Puede ser útil para análisis operativos en los que quieras comparar lunes, martes, miércoles, jueves y viernes, excluyendo fines de semana.

---

## Ejercicio 5: Validar cálculos en visualizaciones

### Tarea 1: Crear una página de validación

1. Ve a la vista de informe y crea una página llamada `Validación de medidas`.
2. Añade una matriz.
3. En filas, coloca `Servicio[Servicio]`.
4. En valores, agrega todas las medidas:
   - `Total envíos`
   - `Ingresos`
   - `Margen estimado`
   - `Margen medio por envío`
   - `% margen estimado`
   - `Días medios hasta entrega`
   - `Días máximos hasta entrega`
   - `% envíos entregados`
  5. Añade una segmentación con `Fecha`[Tipo de día]
  6. Añade una segmentación con `Fecha`[Temporada operativa]
  7. Cambia los filtros y comprueba que todas las medidas responden al contexto seleccionado.

**Resultado esperado: ** has creado medidas derivadas para analizar margen, duración de entrega y entregas registradas. Además, has añadido columnas calculadas en la tabla Fecha para segmentar el análisis por tipo de día y temporada operativa.

---

## Entregables

Al finalizar, debes tener un archivo `.pbix` con:

1. Tabla `Fecha` creada y marcada como tabla de fechas.
2. Calendario Gregoriano y calendario ISO, si tu versión lo permite.
3. Relaciones activas e inactivas entre `Fecha` y `Envíos`.
4. Tabla calculada `Medidas`.
5. Medidas base:
   - `Total envíos`
   - `Ingresos`
6. Medidas económicas derivadas:
   - `Margen estimado`
   - `Margen medio por envío`
   - `% margen estimado`
7. Medidas operativas derivadas:
   - `Días medios hasta entrega`
   - `Días máximos hasta entrega`
   - `Envíos con entrega`
   - `% envíos entregados`
8. Columnas calculadas en la tabla `Fecha`:
   - `Es fin de semana`
   - `Tipo de día`
   - `Temporada operativa`
   - `Día hábil semana`
9. Medidas organizadas en carpetas de visualización:
   - `Indicadores económicos`
   - `Indicadores operativos`
10. Página `Validación medidas derivadas` con visualizaciones para comprobar que las medidas responden al contexto de filtro.

---

## Resumen

En este laboratorio has creado los primeros cálculos DAX del modelo ParcelCraft. Has trabajado con tablas calculadas, columnas calculadas y medidas, y has consolidado la tabla `Fecha`, necesaria para análisis temporal y para los cálculos avanzados del siguiente laboratorio.

### 🎉 Fin del laboratorio

¡Enhorabuena! Has completado el Laboratorio 3 de ParcelCraft. 👏

# Laboratorio 4: DAX avanzado, contexto de filtro e inteligencia de tiempo en Power BI

**ParcelCraft** ya cuenta con un modelo en estrella, una tabla `Fecha` y medidas base. En este laboratorio profundizarás en DAX avanzado para construir métricas reutilizables, medidas anidadas, cálculos con variables, funciones iteradoras, inteligencia de tiempo, relaciones inactivas con `USERELATIONSHIP()` y grupos de cálculo.

## Objetivos de aprendizaje

**Tiempo estimado total: 45 minutos**

Al finalizar este laboratorio serás capaz de:

- Crear medidas reutilizables y medidas anidadas.
- Usar variables DAX para mejorar legibilidad y rendimiento.
- Manipular el contexto de filtro con `CALCULATE`, `REMOVEFILTERS`, `KEEPFILTERS` y `ALLSELECTED`.
- Usar funciones iteradoras como `SUMX` y `AVERAGEX`.
- Crear medidas de inteligencia de tiempo.
- Usar `USERELATIONSHIP()` para activar relaciones inactivas en medidas.
- Crear grupos de cálculo.
- Validar cálculos avanzados en visualizaciones.

---

## Antes de empezar

### Requisitos

Para completar este laboratorio necesitas:

- Power BI Desktop instalado.
- Haber completado el **Laboratorio 3: Fundamentos de DAX y tabla de fechas en Power BI** o partir del archivo inicial.
- Un modelo con tabla `Fecha` marcada como tabla de fechas.
- Relaciones activas e inactivas entre `Fecha` y `Envíos`.
- Medidas base como `Total envíos`, `Ingresos`, `Envíos tardíos` y `% envíos tardíos`.
- Entorno con **grupos de cálculo habilitados**.

> [!NOTE]
> Tienes un archivo inicial y solución disponibles. Si vas a trabajar en el mismo archivo a lo largo de todos los laboratorios, recomiendo empezar a partir de este starter.
> - [Archivo inicial (Starter)](https://github.com/quizeth/Labs/blob/main/Explotaci%C3%B3n%20y%20Procesamiento%20de%20Datos%20con%20Power%20BI/Avanzado/Lab%2004/Files/04-A%20Starter%20ParcelCraft.pbix)
> - [Archivo final (Solución)](https://github.com/quizeth/Labs/blob/main/Explotaci%C3%B3n%20y%20Procesamiento%20de%20Datos%20con%20Power%20BI/Avanzado/Lab%2004/Files/04-A%20Soluci%C3%B3n%20ParcelCraft.pbix)

### Convenciones usadas en el laboratorio

- Cuando se indique una opción de cinta, se mostrará como **pestaña > grupo > comando**.
- Las medidas se crearán en la tabla `Medidas`, salvo que se indique lo contrario.
- Los nombres de tablas, columnas, medidas y grupos de cálculo deben escribirse exactamente como se indican.
- Si tu modelo tiene nombres ligeramente distintos, utiliza la columna equivalente.
- Antes de crear una medida nueva, comprueba que no existe ya una medida con el mismo nombre.

---

## Ejercicio 1: Crear medidas reutilizables y medidas anidadas

En este ejercicio crearás medidas pequeñas y reutilizables. Después crearás medidas nuevas que se apoyan en esas medidas base.

Este patrón es importante porque evita duplicar lógica. Por ejemplo, si una medida como `Total envíos` se usa en diez cálculos distintos, solo tendrás que corregirla en un lugar si cambia la definición de negocio.

### Tarea 1: Revisar las medidas base existentes

1. Ve a la **vista Modelo** o a la **vista Informe**.
2. En el panel **Datos**, expande la tabla `Medidas`.
3. Comprueba que existen estas medidas:
   - `Total envíos`
   - `Ingresos`
   - `Envíos tardíos`
   - `% envíos tardíos`
   - `Peso total KG`
   - `Promedio intentos`

> [!NOTE]
> Si alguna medida no existe, créala antes de continuar. Este laboratorio asume que esas medidas ya están disponibles desde el Laboratorio 3.

4. Crea una matriz rápida para validar las medidas base.
5. Añade `Servicio[Servicio]` a filas.
6. Añade `Total envíos` e `Ingresos` a valores.
7. Revisa que los valores cambian por servicio.

> Esta validación confirma que las medidas base responden al contexto de filtro del modelo. En DAX, una medida no devuelve siempre un único valor fijo: devuelve un valor calculado según los filtros activos en la visualización.

### Tarea 2: Crear medidas atómicas

En esta tarea crearás medidas sencillas que representan conceptos de negocio individuales. Estas medidas serán la base para cálculos más complejos.

1. En la tabla `Medidas`, crea esta medida:

```DAX
Total entregados =
CALCULATE(
    [Total envíos],
    Envíos[Estado] = "Delivered"
)
```

> `CALCULATE` evalúa la medida `[Total envíos]` en un contexto de filtro modificado.
> En este caso, el filtro adicional conserva únicamente las filas de `Envíos` cuyo estado es `Delivered`.
> El resultado será el número de envíos entregados dentro del contexto actual: por servicio, cliente, fecha, oficina o cualquier otro filtro activo.

2. Crea la siguiente medida:

```DAX
Total no entregados =
[Total envíos] - [Total entregados]
```

> Esta medida reutiliza dos medidas existentes.
> No vuelve a contar filas directamente en `Envíos`, sino que se apoya en `[Total envíos]` y `[Total entregados]`.
> Este enfoque hace que la lógica sea más clara y más fácil de mantener.

3. Crea esta medida:

```DAX
Ingresos por envío =
DIVIDE(
    [Ingresos],
    [Total envíos]
)
```

> `DIVIDE` es preferible al operador `/` porque controla divisiones entre cero o valores en blanco.

4. Crea esta medida:

```DAX
Peso medio por envío =
DIVIDE(
    [Peso total KG],
    [Total envíos]
)
```

5. Configura las medidas:
   - `Total entregados`: número entero, 0 decimales.
   - `Total no entregados`: número entero, 0 decimales.
   - `Ingresos por envío`: moneda, 2 decimales.
   - `Peso medio por envío`: número decimal, 2 decimales.
6. En el panel **Propiedades**, asigna estas medidas a la carpeta `Indicadores avanzados`.

### Tarea 3: Crear medidas anidadas

Ahora crearás medidas que dependen de las medidas anteriores.

1. Crea esta medida:

```DAX
% entregados =
DIVIDE(
    [Total entregados],
    [Total envíos]
)
```

2. Crea esta medida:

```DAX
% no entregados =
DIVIDE(
    [Total no entregados],
    [Total envíos]
)
```

3. Crea esta medida:

```DAX
Ingresos por KG =
DIVIDE(
    [Ingresos],
    [Peso total KG]
)
```

> Estas medidas son medidas anidadas porque reutilizan otras medidas.
> Este patrón evita repetir expresiones como `COUNTROWS(Envíos)` o `SUM(Envíos[Precio total])` en múltiples lugares.

4. Formatea:
   - `% entregados`: porcentaje, 2 decimales.
   - `% no entregados`: porcentaje, 2 decimales.
   - `Ingresos por KG`: moneda, 2 decimales.
5. Asigna las medidas a la carpeta `Indicadores avanzados`.

### Tarea 4: Validar medidas anidadas

1. Ve a la **vista Informe**.
2. Crea una nueva página llamada `DAX avanzado`.
3. Añade una matriz.
4. En filas, coloca `Servicio[Servicio]`.
5. En valores, añade:
   - `Total envíos`
   - `Total entregados`
   - `Total no entregados`
   - `% entregados`
   - `% no entregados`
   - `Ingresos por envío`
6. Revisa que `% entregados` y `% no entregados` tienen sentido para cada servicio.

**Resultado esperado:** tienes una primera capa de medidas reutilizables y medidas anidadas, organizadas y validadas en una matriz.

---

## Ejercicio 2: Construir una medida avanzada paso a paso con variables

En este ejercicio construirás una medida cada vez más compleja. El objetivo no es solo obtener el resultado final, sino entender cómo evolucionar una medida desde una versión simple hasta una versión robusta, legible y mantenible.

La medida final calculará un **índice operativo ponderado**. Este índice combinará tres ideas:

- Penalización por entregas tardías.
- Penalización por múltiples intentos de entrega.
- Peso relativo de los ingresos en el cálculo.

> [!NOTE]
> El índice es didáctico. No representa una regla oficial de ParcelCraft. El objetivo es practicar medidas anidadas, funciones iteradoras y múltiples variables.

### Tarea 1: Crear una primera versión simple del índice

1. En la tabla `Medidas`, crea esta medida:

```DAX
Índice operativo v1 =
DIVIDE(
    [Total entregados],
    [Total envíos]
)
```

> Esta primera versión mide únicamente la proporción de envíos entregados.
> Es fácil de entender, pero todavía no considera ingresos, entregas tardías ni intentos de entrega.

2. Formatea la medida como número decimal con 2 decimales.
3. Añade la medida a la matriz de validación por `Servicio[Servicio]`.

### Tarea 2: Añadir penalización por entregas tardías

1. Crea esta segunda versión:

```DAX
Índice operativo v2 =
VAR TotalEnvios = [Total envíos]
VAR EnviosTardios = [Envíos tardíos]
VAR EnviosNoTardios = TotalEnvios - EnviosTardios
RETURN
    DIVIDE(EnviosNoTardios, TotalEnvios)
```

> Esta versión usa variables para separar los componentes del cálculo.
> `TotalEnvios` guarda el total de envíos del contexto actual.
> `EnviosTardios` guarda los envíos tardíos.
> `EnviosNoTardios` calcula los envíos que no son tardíos.
> La medida devuelve la proporción de envíos no tardíos.

2. Formatea la medida como porcentaje con 2 decimales.
3. Añádela a la matriz junto a `% envíos tardíos`.
4. Comprueba que, cuando sube `% envíos tardíos`, baja `Índice operativo v2`.

### Tarea 3: Añadir penalización por intentos de entrega

1. Crea esta tercera versión:

```DAX
Índice operativo v3 =
VAR TotalEnvios = [Total envíos]
VAR EnviosTardios = [Envíos tardíos]
VAR EnviosNoTardios = TotalEnvios - EnviosTardios
VAR RatioNoTardios = DIVIDE(EnviosNoTardios, TotalEnvios)
VAR IntentosMedios = [Promedio intentos]
VAR PenalizacionIntentos =
    SWITCH(
        TRUE(),
        ISBLANK(IntentosMedios), BLANK(),
        IntentosMedios <= 1, 1,
        IntentosMedios <= 2, 0.95,
        IntentosMedios <= 3, 0.90,
        0.85
    )
RETURN
    RatioNoTardios * PenalizacionIntentos
```

> Esta versión añade una penalización por intentos medios.
> Si el promedio de intentos es bajo, la penalización es pequeña o inexistente.
> Si el promedio de intentos aumenta, el índice se reduce.
> `SWITCH(TRUE())` permite evaluar condiciones en orden, como si fuera una estructura `if / else if`.

2. Formatea la medida como número decimal con 2 decimales.
3. Añade la medida a la matriz.
4. Compara `Índice operativo v2` e `Índice operativo v3`.

### Tarea 4: Crear la versión final con múltiples variables e iteración

Ahora crearás una versión final que evalúa envío a envío. Esta versión combina variables, `SUMX`, condiciones y medidas base.

1. Crea esta medida:

```DAX
Índice operativo ponderado =
VAR IngresosTotales = [Ingresos]
VAR HayIngresos = NOT ISBLANK(IngresosTotales) && IngresosTotales <> 0
VAR ResultadoPonderado =
    SUMX(
        Envíos,
        VAR IngresoEnvio = Envíos[Precio total]
        VAR PesoIngreso = DIVIDE(IngresoEnvio, IngresosTotales)
        VAR EsTardio = Envíos[EntregaTardia] = TRUE()
        VAR IntentosEnvio = Envíos[Intentos]
        VAR FactorSLA = IF(EsTardio, 0.70, 1.00)
        VAR FactorIntentos =
            SWITCH(
                TRUE(),
                ISBLANK(IntentosEnvio), 1.00,
                IntentosEnvio <= 1, 1.00,
                IntentosEnvio = 2, 0.95,
                IntentosEnvio = 3, 0.90,
                0.85
            )
        VAR PuntuacionEnvio = FactorSLA * FactorIntentos
        RETURN
            PesoIngreso * PuntuacionEnvio
    )
RETURN
    IF(
        HayIngresos,
        ResultadoPonderado,
        BLANK()
    )
```

> Esta medida es más avanzada porque crea variables tanto fuera como dentro de `SUMX`.
> Las variables externas (`IngresosTotales`, `HayIngresos`, `ResultadoPonderado`) controlan el cálculo global del contexto actual.
> Las variables internas (`IngresoEnvio`, `PesoIngreso`, `EsTardio`, `IntentosEnvio`, `FactorSLA`, `FactorIntentos`, `PuntuacionEnvio`) se evalúan para cada fila de `Envíos` dentro de la iteración.
>
> El resultado es un índice ponderado por ingresos: los envíos con más ingresos tienen más influencia en el resultado final.

2. Formatea la medida como porcentaje con 2 decimales.
3. Asigna la medida a la carpeta `Indicadores avanzados`.
4. Añade la medida a la matriz junto a:
   - `Ingresos`
   - `% envíos tardíos`
   - `Promedio intentos`
   - `Índice operativo ponderado`
5. Filtra por `Servicio[Servicio]`, `Cliente[Segmento]` o `Fecha[Año]` y observa cómo cambia el índice.

### Tarea 5: Documentar la medida final

1. Selecciona la medida `Índice operativo ponderado`.
2. En el panel **Propiedades**, localiza **Descripción**.
3. Escribe:

```text
Índice didáctico que combina entregas tardías e intentos de entrega, ponderado por ingresos del envío dentro del contexto actual.
```

> Documentar medidas complejas es fundamental. Si una medida contiene muchas variables, condiciones o supuestos de negocio, añade una descripción funcional para que otros usuarios entiendan qué representa.

**Resultado esperado:** has aprendido a construir una medida avanzada de forma incremental, desde una definición simple hasta una versión final con múltiples variables e iteración fila a fila.

---

## Ejercicio 3: Modificar el contexto de filtro

En este ejercicio usarás `CALCULATE` para modificar el contexto de filtro. Esta es una de las capacidades más importantes de DAX.

Una medida siempre se evalúa dentro de un contexto de filtro. Ese contexto puede venir de filas de una matriz, segmentadores, filtros de página o relaciones del modelo. `CALCULATE` permite cambiar ese contexto antes de evaluar la expresión.

### Tarea 1: Crear ingresos ignorando el filtro de servicio

1. Crea esta medida en la tabla `Medidas`:

```DAX
Ingresos todos los servicios =
CALCULATE(
    [Ingresos],
    REMOVEFILTERS(Servicio)
)
```

> `REMOVEFILTERS(Servicio)` elimina los filtros activos sobre la tabla `Servicio`.
> Si colocas `Servicio[Servicio]` en una matriz, esta medida devolverá el mismo total para todas las filas, porque ignora el servicio de cada fila.
> Sin embargo, seguirá respetando otros filtros, como fecha, cliente, oficina o destino.

2. Configura la medida como moneda con 2 decimales.
3. Asígnala a la carpeta `Contexto de filtro`.

### Tarea 2: Crear participación de ingresos por servicio

1. Crea esta medida:

```DAX
% ingresos servicio =
DIVIDE(
    [Ingresos],
    [Ingresos todos los servicios]
)
```

2. Formatea la medida como porcentaje con 2 decimales.
3. Añade a una matriz:
   - Filas: `Servicio[Servicio]`
   - Valores: `Ingresos`, `Ingresos todos los servicios`, `% ingresos servicio`

### Tarea 3: Crear participación respetando selección visible

1. Crea esta medida:

```DAX
Ingresos selección visible =
CALCULATE(
    [Ingresos],
    ALLSELECTED(Servicio)
)
```

> `ALLSELECTED` conserva la selección hecha por el usuario en segmentadores o filtros externos, pero elimina el filtro de la fila actual de la matriz.
> Esto permite calcular porcentajes respecto al total visible, no necesariamente respecto al total absoluto del modelo.

2. Crea esta medida:

```DAX
% ingresos selección visible =
DIVIDE(
    [Ingresos],
    [Ingresos selección visible]
)
```

3. Formatea `% ingresos selección visible` como porcentaje con 2 decimales.
4. Añade un segmentador con `Servicio[Prioridad]`.
5. Filtra una prioridad y compara:
   - `% ingresos servicio`
   - `% ingresos selección visible`

### Tarea 4: Aplicar filtros adicionales con KEEPFILTERS

1. Crea esta medida:

```DAX
Ingresos servicios urgentes =
CALCULATE(
    [Ingresos],
    KEEPFILTERS(Servicio[Prioridad] IN { "Same Day", "Express" })
)
```

> `KEEPFILTERS` aplica el filtro indicado sin reemplazar por completo los filtros existentes sobre la misma columna.
> Esto es útil cuando quieres añadir una condición adicional, pero respetar el contexto que ya viene de la visualización.

2. Si tus valores de `Servicio[Prioridad]` no son `Same Day` o `Express`, ajusta los textos a los valores que existan en tu modelo.
3. Valida la medida en una tarjeta o matriz.

**Resultado esperado:** entiendes cómo cambia una medida al eliminar filtros, respetar selecciones visibles o añadir condiciones adicionales.

---

## Ejercicio 4: Usar funciones iteradoras

Las funciones iteradoras recorren una tabla fila a fila, evalúan una expresión y después agregan el resultado. Algunas funciones iteradoras comunes son `SUMX`, `AVERAGEX`, `MAXX`, `MINX` y `COUNTX`.

Usa iteradoras cuando el cálculo necesite combinar columnas de una fila antes de agregar. Por ejemplo, calcular un ingreso ajustado fila a fila y después sumarlo.

### Tarea 1: Crear ingresos ajustados por control de temperatura

1. Crea esta medida:

```DAX
Ingresos ajustados temperatura =
SUMX(
    Envíos,
    Envíos[Precio total]
        * IF(Envíos[ControlTemperatura] = TRUE(), 1.08, 1)
)
```

> `SUMX` recorre la tabla `Envíos`.
> Para cada fila, toma `Precio total` y lo multiplica por `1.08` si el envío requiere control de temperatura.
> Si no requiere control de temperatura, multiplica por `1`.
> Después suma todos los resultados.

2. Configura la medida como moneda con 2 decimales.
3. Asigna la medida a la carpeta `Iteradoras`.

> [!NOTE]
> El ajuste del 8% es una regla didáctica para practicar `SUMX`. No representa necesariamente una regla financiera real de ParcelCraft.

### Tarea 2: Crear peso medio ponderado por ingresos

1. Crea esta medida:

```DAX
Peso medio ponderado por ingresos =
DIVIDE(
    SUMX(
        Envíos,
        Envíos[Peso (KG)] * Envíos[Precio total]
    ),
    [Ingresos]
)
```

> Esta medida calcula una media ponderada.
> Los envíos con mayor ingreso tienen más peso en el resultado que los envíos con menor ingreso.

2. Configura la medida como número decimal con 2 decimales.
3. Asigna la medida a la carpeta `Iteradoras`.

### Tarea 3: Crear intentos ponderados por ingresos

1. Crea esta medida:

```DAX
Intentos ponderados por ingresos =
DIVIDE(
    SUMX(
        Envíos,
        Envíos[Intentos] * Envíos[Precio total]
    ),
    [Ingresos]
)
```

2. Configura la medida como número decimal con 2 decimales.
3. Asigna la medida a la carpeta `Iteradoras`.

### Tarea 4: Validar medidas iteradoras

1. Añade una matriz.
2. En filas, coloca `Servicio[Servicio]`.
3. En valores, coloca:
   - `Ingresos`
   - `Ingresos ajustados temperatura`
   - `Peso medio ponderado por ingresos`
   - `Intentos ponderados por ingresos`
   - `Índice operativo ponderado`
4. Añade un segmentador con `Cliente[Segmento]`, si existe.
5. Cambia el filtro y observa cómo se recalculan las medidas.

**Resultado esperado:** puedes comparar agregaciones simples con cálculos fila a fila usando iteradoras.

---

## Ejercicio 5: Crear medidas con USERELATIONSHIP()

En este modelo, la tabla `Fecha` está relacionada activamente con `Envíos[FechaEnvío]`. También existen relaciones inactivas con otras fechas de negocio, como `FechaCreación`, `FechaActualización`, `FechaEntrega` y `FechaCompromisoSLA`.

`USERELATIONSHIP()` permite activar temporalmente una relación inactiva dentro de una medida. Esto evita duplicar la tabla `Fecha` para cada rol temporal.

### Tarea 1: Crear medidas por fechas alternativas

1. Crea esta medida:

```DAX
Total envíos por fecha creación =
CALCULATE(
    [Total envíos],
    USERELATIONSHIP(Fecha[Fecha], Envíos[FechaCreación])
)
```

2. Crea esta medida:

```DAX
Total envíos por fecha actualización =
CALCULATE(
    [Total envíos],
    USERELATIONSHIP(Fecha[Fecha], Envíos[FechaActualización])
)
```

3. Crea esta medida:

```DAX
Total envíos por fecha entrega =
CALCULATE(
    [Total envíos],
    USERELATIONSHIP(Fecha[Fecha], Envíos[FechaEntrega])
)
```

4. Crea esta medida:

```DAX
Total envíos por fecha compromiso SLA =
CALCULATE(
    [Total envíos],
    USERELATIONSHIP(Fecha[Fecha], Envíos[FechaCompromisoSLA])
)
```

5. Asigna todas estas medidas a la carpeta `Roles de fecha`.

### Tarea 2: Comparar fechas en una visualización

1. Añade un gráfico de líneas.
2. En el eje X, coloca `Fecha[Año mes]`.
3. En valores, coloca:
   - `Total envíos`
   - `Total envíos por fecha creación`
   - `Total envíos por fecha entrega`
   - `Total envíos por fecha compromiso SLA`
4. Ordena el eje por `Fecha[Año mes]`.
5. Revisa las diferencias entre las líneas.

**Resultado esperado:** puedes analizar la misma tabla de hechos usando distintas fechas de negocio sin duplicar la tabla `Fecha`.

---

## Ejercicio 6: Inteligencia de tiempo

En este ejercicio crearás medidas temporales habituales: acumulado anual, año anterior, variación interanual y media móvil.

Las funciones de inteligencia de tiempo requieren una tabla de fechas correctamente configurada y marcada como tabla de fechas.

### Tarea 1: Crear ingresos acumulados YTD

1. Crea esta medida:

```DAX
Ingresos YTD =
TOTALYTD(
    [Ingresos],
    Fecha[Fecha]
)
```

> `TOTALYTD` acumula la medida `[Ingresos]` desde el inicio del año hasta la fecha máxima del contexto actual.

2. Configura la medida como moneda con 2 decimales.
3. Asigna la medida a la carpeta `Inteligencia de tiempo`.

### Tarea 2: Crear ingresos del año anterior

1. Crea esta medida:

```DAX
Ingresos año anterior =
CALCULATE(
    [Ingresos],
    SAMEPERIODLASTYEAR(Fecha[Fecha])
)
```

2. Configura la medida como moneda con 2 decimales.

### Tarea 3: Crear variación interanual

1. Crea esta medida:

```DAX
Variación ingresos YoY =
[Ingresos] - [Ingresos año anterior]
```

2. Crea esta medida:

```DAX
Variación ingresos YoY % =
DIVIDE(
    [Variación ingresos YoY],
    [Ingresos año anterior]
)
```

3. Formatea `Variación ingresos YoY` como moneda.
4. Formatea `Variación ingresos YoY %` como porcentaje con 2 decimales.

### Tarea 4: Crear media móvil de ingresos

1. Crea esta medida:

```DAX
Ingresos media móvil 3 meses =
AVERAGEX(
    DATESINPERIOD(
        Fecha[Fecha],
        MAX(Fecha[Fecha]),
        -3,
        MONTH
    ),
    [Ingresos]
)
```

2. Configura la medida como moneda con 2 decimales.

### Tarea 5: Validar inteligencia de tiempo

1. Añade una matriz.
2. En filas, coloca:
   - `Fecha[Año]`
   - `Fecha[Mes]`
3. En valores, coloca:
   - `Ingresos`
   - `Ingresos YTD`
   - `Ingresos año anterior`
   - `Variación ingresos YoY %`
   - `Ingresos media móvil 3 meses`
4. Expande la jerarquía de filas para ver meses dentro de años.
5. Revisa que `Ingresos YTD` se acumula dentro del año.

**Resultado esperado:** las medidas temporales responden correctamente al calendario del modelo.

---

## Ejercicio 7: Crear grupos de cálculo

Los grupos de cálculo permiten reducir el número de medidas repetitivas. En lugar de crear una medida YTD, año anterior y variación para cada métrica, puedes crear un grupo de cálculo que aplique una lógica común a cualquier medida seleccionada.

En este entorno, los grupos de cálculo están disponibles, por lo que crearás el grupo directamente en el modelo.

### Tarea 1: Crear el grupo de cálculo Tiempo

1. Ve a la **vista Modelo**.
2. En la cinta, selecciona la opción para crear un **grupo de cálculo**.
3. Crea un grupo llamado:

```text
cg Tiempo
```

4. Cambia el nombre de la columna del grupo a:

```text
Cálculo tiempo
```

> La columna del grupo será el campo que usarás en columnas, filas o segmentadores para elegir qué transformación temporal aplicar.

### Tarea 2: Crear elementos de cálculo

Crea los siguientes elementos de cálculo.

#### Actual

```DAX
SELECTEDMEASURE()
```

> `SELECTEDMEASURE()` representa la medida que el usuario colocó en la visualización.

#### YTD

```DAX
CALCULATE(
    SELECTEDMEASURE(),
    DATESYTD(Fecha[Fecha])
)
```

#### Año anterior

```DAX
CALCULATE(
    SELECTEDMEASURE(),
    SAMEPERIODLASTYEAR(Fecha[Fecha])
)
```

#### Variación YoY

```DAX
VAR ValorActual = SELECTEDMEASURE()
VAR ValorAnterior =
    CALCULATE(
        SELECTEDMEASURE(),
        SAMEPERIODLASTYEAR(Fecha[Fecha])
    )
RETURN
    ValorActual - ValorAnterior
```

#### Variación YoY %

```DAX
VAR ValorActual = SELECTEDMEASURE()
VAR ValorAnterior =
    CALCULATE(
        SELECTEDMEASURE(),
        SAMEPERIODLASTYEAR(Fecha[Fecha])
    )
RETURN
    DIVIDE(ValorActual - ValorAnterior, ValorAnterior)
```

### Tarea 3: Configurar formato dinámico

1. Selecciona el elemento `Variación YoY %`.
2. Configura su formato como porcentaje con 2 decimales.
3. Para el resto de elementos, conserva el formato de la medida seleccionada si la interfaz lo permite.

### Tarea 4: Validar el grupo de cálculo

1. Añade una matriz.
2. En filas, coloca `Fecha[Año]` y `Fecha[Mes]`.
3. En columnas, coloca `cg Tiempo[Cálculo tiempo]`.
4. En valores, coloca `Ingresos`.
5. Filtra el grupo de cálculo para mostrar solo:
   - `Actual`
   - `YTD`
   - `Año anterior`
   - `Variación YoY %`

**Resultado esperado:** una única medida, `Ingresos`, puede mostrarse con varias lógicas temporales mediante el grupo de cálculo.

---

## Ejercicio 8: Crear un grupo de cálculo para rol de fecha

Este patrón permite cambiar la fecha usada por una medida sin crear muchas medidas específicas.

> [!WARNING]
> Este patrón es avanzado. Úsalo con cuidado, especialmente si se combina con otros grupos de cálculo. Valida siempre los resultados con visualizaciones simples antes de usarlo en informes productivos.

### Tarea 1: Crear el grupo de cálculo Rol de fecha

1. Crea un grupo de cálculo llamado:

```text
cg Rol fecha
```

2. Cambia el nombre de la columna del grupo a:

```text
Rol fecha
```

### Tarea 2: Crear elementos de cálculo

#### Fecha de envío

```DAX
SELECTEDMEASURE()
```

> Este elemento no modifica la relación porque `FechaEnvío` ya es la relación activa.

#### Fecha de creación

```DAX
CALCULATE(
    SELECTEDMEASURE(),
    USERELATIONSHIP(Fecha[Fecha], Envíos[FechaCreación])
)
```

#### Fecha de actualización

```DAX
CALCULATE(
    SELECTEDMEASURE(),
    USERELATIONSHIP(Fecha[Fecha], Envíos[FechaActualización])
)
```

#### Fecha de entrega

```DAX
CALCULATE(
    SELECTEDMEASURE(),
    USERELATIONSHIP(Fecha[Fecha], Envíos[FechaEntrega])
)
```

#### Fecha compromiso SLA

```DAX
CALCULATE(
    SELECTEDMEASURE(),
    USERELATIONSHIP(Fecha[Fecha], Envíos[FechaCompromisoSLA])
)
```

### Tarea 3: Validar el grupo Rol de fecha

1. Añade una matriz.
2. En filas, coloca `Fecha[Año mes]`.
3. En columnas, coloca `cg Rol fecha[Rol fecha]`.
4. En valores, coloca `Total envíos`.
5. Compara los resultados por fecha de envío, creación, actualización, entrega y compromiso SLA.

**Resultado esperado:** puedes cambiar el rol de fecha aplicado a una medida mediante un grupo de cálculo.

---

## Ejercicio 9 (Opcional): Crear cálculos visuales

Los cálculos visuales permiten crear cálculos directamente dentro de una visualización. Son útiles para análisis rápidos, como acumulados visuales, comparaciones con el periodo anterior o medias móviles.

### Tarea 1: Crear un gráfico por año y mes

1. Añade un gráfico de columnas.
2. En el eje, coloca `Fecha[Año mes]`.
3. En valores, coloca `Ingresos`.
4. Ordena el eje cronológicamente.

### Tarea 2: Crear cálculo visual acumulado

1. Con el visual seleccionado, elige **Nuevo cálculo visual**.
2. Crea el cálculo:

```DAX
Ingresos acumulados visual =
RUNNINGSUM([Ingresos])
```

### Tarea 3: Crear cálculo visual respecto al periodo anterior

1. Crea otro cálculo visual:

```DAX
Variación visual anterior =
[Ingresos] - PREVIOUS([Ingresos])
```

### Tarea 4: Crear media móvil visual

1. Crea un tercer cálculo visual:

```DAX
Media móvil visual 3 =
MOVINGAVERAGE([Ingresos], 3)
```

**Resultado esperado:** puedes añadir cálculos de tendencia directamente en una visualización sin crear nuevas medidas de modelo.

---

## Entregables

Al finalizar, debes tener un archivo `.pbix` con:

1. Medidas reutilizables y anidadas.
2. Medida avanzada `Índice operativo ponderado`, construida con múltiples variables.
3. Medidas con variables para análisis SLA.
4. Medidas que modifican contexto de filtro.
5. Medidas con funciones iteradoras.
6. Medidas con `USERELATIONSHIP()` para fechas alternativas.
7. Medidas de inteligencia de tiempo:
   - `Ingresos YTD`
   - `Ingresos año anterior`
   - `Variación ingresos YoY`
   - `Variación ingresos YoY %`
   - `Ingresos media móvil 3 meses`
8. Grupo de cálculo `cg Tiempo`.
9. Grupo de cálculo `cg Rol fecha`.
10. Página de validación `DAX avanzado`.
11. Cálculos visuales opcionales.

---

## Resumen

En este laboratorio has trabajado con técnicas DAX avanzadas para crear una capa semántica más potente: medidas reutilizables, variables, contexto de filtro, iteradores, inteligencia de tiempo, relaciones inactivas y grupos de cálculo.

También has visto cómo construir una medida compleja de forma incremental. Este enfoque es muy útil en proyectos reales: primero se crea una versión simple, después se añaden reglas de negocio, finalmente se refactoriza con variables para que el cálculo sea comprensible y mantenible.

### 🎉 Fin del laboratorio

¡Enhorabuena! Has completado el Laboratorio 4 de ParcelCraft. 👏

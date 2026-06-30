# Laboratorio 4: DAX avanzado, contexto de evaluación e inteligencia de tiempo en Power BI

**ParcelCraft** ya cuenta con un modelo en estrella, una tabla `Fecha` y medidas base. En este laboratorio profundizarás en DAX avanzado siguiendo una progresión natural: primero analizarás el **contexto de evaluación**, después diseñarás medidas reutilizables, construirás medidas cada vez más complejas con variables, aplicarás funciones iteradoras e inteligencia de tiempo, usarás relaciones inactivas con `USERELATIONSHIP()` y, finalmente, centralizarás lógica de negocio mediante grupos de cálculo.

El objetivo no es solo crear medidas, sino aprender a razonar como un desarrollador DAX experimentado: entender qué filtros afectan a una medida, decidir cuándo modificar el contexto, separar la lógica en medidas base y medidas derivadas, validar resultados y reducir complejidad del modelo.

## Objetivos de aprendizaje

**Tiempo estimado total: 45 minutos**

Al finalizar este laboratorio serás capaz de:

- Comprender cómo el contexto de filtro, las relaciones y los visuales afectan al resultado de una medida DAX.
- Modificar el contexto de evaluación con funciones como `CALCULATE`, `REMOVEFILTERS`, `ALLSELECTED` y `KEEPFILTERS`.
- Diseñar medidas reutilizables mediante patrones de medidas base, medidas derivadas y variables.
- Crear cálculos avanzados con funciones iteradoras como `SUMX` y `AVERAGEX`.
- Implementar inteligencia de tiempo para acumulados, comparativas y análisis de tendencias.
- Usar `USERELATIONSHIP()` para analizar una tabla de hechos mediante fechas alternativas.
- Centralizar lógica de negocio con grupos de cálculo.
- Validar y depurar medidas avanzadas mediante visualizaciones de análisis.

---

## Antes de empezar

### Requisitos

Para completar este laboratorio necesitas:

- Power BI Desktop instalado.
- Haber completado el **Laboratorio 3: Fundamentos de DAX y tabla de fechas en Power BI** o partir del archivo inicial.
- Un modelo con tabla `Fecha` marcada como tabla de fechas.
- Relaciones activas e inactivas entre `Fecha` y `Envíos`.
- Medidas base como `Total envíos`, `Ingresos`, `Envíos tardíos`, `% envíos tardíos`, `Peso total KG` y `Promedio intentos`.
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
- En este laboratorio trabajarás principalmente en la **vista Informe**, la **vista Modelo** y el panel **Datos** en ambas vistas.

---

## Ejercicio 1: Analizar contexto de filtro y medidas base

En este ejercicio comprobarás cómo una misma medida devuelve resultados distintos según el contexto de filtro. Este comportamiento es fundamental para entender DAX.

Una medida no se calcula una sola vez para todo el modelo. Se evalúa cada vez que un visual la necesita, teniendo en cuenta filtros de filas, columnas, segmentadores, filtros de página y relaciones del modelo.

### Tarea 1: Crear una página de análisis de contexto

1. Ve a la **vista Informe**.
2. Crea una nueva página llamada:

```text
Contexto DAX
```

3. Añade una matriz.
4. En filas, coloca:
   - `Servicio[Servicio]`
5. En valores, coloca:
   - `Total envíos`
   - `Ingresos`
   - `% envíos tardíos`
6. Añade un segmentador con:
   - `Cliente[Segmento]`.
7. Añade otro segmentador con:
   - `Fecha[Año mes]`.

> Observa que las mismas medidas muestran un resultado diferente por cada servicio. Además, cambian cuando seleccionas un año o segmento de cliente. Esto ocurre porque el contexto de filtro cambia.

### Tarea 2: Crear una medida que ignore el filtro de servicio

1. En la tabla `Medidas`, crea esta medida:

```DAX
Ingresos todos los servicios =
CALCULATE(
    [Ingresos],
    REMOVEFILTERS(Servicio)
)
```

> `CALCULATE` evalúa una expresión en un contexto modificado.
> `REMOVEFILTERS(Servicio)` elimina los filtros que afectan a la tabla `Servicio`.
> Si la medida se coloca en una matriz por servicio, devolverá el mismo total para todos los servicios, porque ignora el filtro de la fila actual.
>
> La medida seguirá respetando otros filtros, como `Fecha[Año]`, `Cliente[Segmento]`, oficina o destino.

2. Configura la medida:
   - **Formato**: Moneda
   - **Decimales**: 2
   - **Carpeta para mostrar**: `Contexto de filtro`
3. Añade `Ingresos todos los servicios` a la matriz.
4. Comprueba que el valor se repite por cada servicio, pero cambia al usar los segmentadores.

### Tarea 3: Crear participación de ingresos por servicio

1. Crea esta medida:

```DAX
% ingresos servicio =
DIVIDE(
    [Ingresos],
    [Ingresos todos los servicios]
)
```

> Esta medida divide los ingresos del servicio actual entre los ingresos de todos los servicios.
> El numerador conserva el filtro de servicio; el denominador lo elimina.

2. Configura la medida:
   - **Formato**: Porcentaje
   - **Decimales**: 2
   - **Carpeta para mostrar**: `Contexto de filtro`
3. Añade la medida a la matriz.
4. Revisa que la suma de las participaciones de ingresos se aproxima al 100% cuando no hay filtros adicionales sobre `Servicio`.

**Resultado esperado:** comprendes que el resultado de una medida depende del contexto de filtro y que `CALCULATE` puede modificar ese contexto.



---

## Ejercicio 2: Comparar REMOVEFILTERS, ALLSELECTED y KEEPFILTERS

En este ejercicio compararás distintas formas de modificar o conservar filtros. Estas funciones son habituales en modelos empresariales para construir porcentajes, benchmarks y métricas comparativas.

### Tarea 1: Crear una medida que respete la selección visible

1. En la tabla `Medidas`, crea esta medida:

```DAX
Ingresos selección visible =
CALCULATE(
    [Ingresos],
    ALLSELECTED(Servicio)
)
```

> `ALLSELECTED(Servicio)` elimina el filtro de la fila actual de la matriz, pero conserva la selección que el usuario haya realizado mediante segmentadores o filtros externos.
> Esto permite calcular un total visible y no necesariamente el total absoluto del modelo.

2. Crea esta medida:

```DAX
% ingresos selección visible =
DIVIDE(
    [Ingresos],
    [Ingresos selección visible]
)
```

3. Configura `% ingresos selección visible` como porcentaje con 2 decimales.
4. Añade ambas medidas a la matriz.
5. Añade un segmentador con `Servicio[Prioridad]`.
6. Selecciona una prioridad y compara:
   - `% ingresos servicio`
   - `% ingresos selección visible`

> Si filtras una prioridad, `% ingresos servicio` compara contra todos los servicios, mientras que `% ingresos selección visible` compara contra los servicios visibles después de la selección.

### Tarea 2: Crear una medida con KEEPFILTERS

### Tarea 2: Crear una medida con KEEPFILTERS

1. Crea esta medida:

```DAX
Ingresos servicios urgentes =
CALCULATE(
    [Ingresos],
    KEEPFILTERS(
        Servicio[Prioridad] IN { "Critical", "High" }
    )
)
```

> `KEEPFILTERS` añade una condición sin reemplazar por completo los filtros existentes sobre la misma columna.
> Esto es útil cuando una medida debe aplicar una regla adicional, pero respetar las selecciones del usuario.
>
> En este modelo, los servicios urgentes no se identifican por el nombre del servicio, sino por la columna `Servicio[Prioridad]`.
> Según la tabla `Servicio`, los servicios de mayor urgencia son:
>
> - `Same Day`, con prioridad `Critical`.
> - `Express`, con prioridad `High`.
>
> Por eso la medida filtra `Servicio[Prioridad]` usando los valores `"Critical"` y `"High"`.

2. Configura la medida:
   - **Formato**: Moneda
   - **Decimales**: 2
   - **Carpeta para mostrar**: `Contexto de filtro`

3. Añade la medida a una tarjeta.

4. Añade un segmentador con `Servicio[Prioridad]`.

5. Cambia la selección del segmentador y observa el comportamiento de la medida.

> Si seleccionas `Critical` o `High`, la medida devolverá ingresos para esas prioridades.
> Si seleccionas `Normal` o `Low`, la medida debería devolver blanco o cero, porque `KEEPFILTERS` mantiene el filtro del usuario y lo intersecta con `{ "Critical", "High" }`.

**Resultado esperado:** la medida calcula los ingresos correspondientes a servicios urgentes, respetando los filtros activos del informe.

### Tarea 3: Interpretar el comportamiento del motor de cálculo

1. Selecciona la matriz creada en el ejercicio anterior.
2. Observa una fila concreta, por ejemplo un servicio específico.
3. Interpreta mentalmente el cálculo de `Ingresos`:
   - El visual filtra un servicio.
   - Los segmentadores filtran año y segmento.
   - Las relaciones propagan filtros hacia `Envíos`.
   - La medida suma `Envíos[Precio total]` dentro de ese contexto.
4. Interpreta ahora `Ingresos todos los servicios`:
   - El contexto de año y segmento sigue activo.
   - El filtro de servicio se elimina.
   - La medida suma ingresos de todos los servicios visibles bajo el resto de filtros.

**Resultado esperado:** puedes explicar por qué dos medidas similares devuelven valores distintos en la misma visualización.

---

## Ejercicio 3: Diseñar medidas reutilizables con measure branching

En este ejercicio usarás el patrón de **medidas base y medidas derivadas**. Este patrón facilita el mantenimiento porque las métricas complejas se construyen a partir de medidas pequeñas y validadas.

### Tarea 1: Revisar y crear medidas base

Antes de continuar con las medidas avanzadas, asegúrate de que existen las siguientes medidas base en la tabla `Medidas`.

1. Crea la medida `Envíos tardíos`:

```DAX
Envíos tardíos =
CALCULATE(
    [Total envíos],
    'Envíos'[Entrega tardía] = TRUE()
)
```

> Esta medida cuenta únicamente los envíos marcados como tardíos.
> `CALCULATE` evalúa `[Total envíos]` en un contexto de filtro modificado, conservando solo las filas donde `'Envíos'[Entrega tardía]` es `TRUE`.
>
> Esta medida depende de una medida que ya debe existir en el modelo:
> - `[Total envíos]`

2. Crea la medida `% envíos tardíos`:

```DAX
% envíos tardíos =
DIVIDE(
    [Envíos tardíos],
    [Total envíos]
)
```

> Esta medida calcula la proporción de envíos tardíos respecto al total de envíos.
> Se usa `DIVIDE` para evitar errores si el denominador es cero o está en blanco.
>
> Esta medida depende de dos medidas que ya deben existir en el modelo:
> - `[Envíos tardíos]`
> - `[Total envíos]`

3. Crea la medida `Peso total KG`:

```DAX
Peso total KG =
SUM('Envíos'[Peso (KG)])
```

> Esta medida suma el peso total de los envíos, expresado en kilogramos.
> Es útil para analizar volumen operativo por servicio, cliente, oficina, destino o periodo.

4. Crea la medida `Promedio intentos`:

```DAX
Promedio intentos =
AVERAGE('Envíos'[Intentos])
```

> Esta medida calcula el número medio de intentos de entrega.
> Puede ayudar a identificar servicios, destinos o segmentos con mayor dificultad operativa.

5. Configura las medidas:
   - `Envíos tardíos`
     - **Formato**: Número entero
     - **Decimales**: 0
     - **Carpeta para mostrar**: `Indicadores operativos`
   - `% envíos tardíos`
     - **Formato**: Porcentaje
     - **Decimales**: 2
     - **Carpeta para mostrar**: `Indicadores operativos`
   - `Peso total KG`
     - **Formato**: Número entero
     - **Decimales**: 0
     - **Carpeta para mostrar**: `Indicadores operativos`
   - `Promedio intentos`
     - **Formato**: Número decimal
     - **Decimales**: 2
     - **Carpeta para mostrar**: `Indicadores operativos`
``

### Tarea 2: Crear medidas atómicas

En esta tarea crearás medidas pequeñas y específicas que representan un único concepto de negocio. Estas medidas servirán como bloques reutilizables para construir cálculos más complejos en tareas posteriores.

1. Crea esta medida:

```DAX
Total entregados =
CALCULATE(
    [Total envíos],
    NOT ISBLANK('Envíos'[FechaEntrega])
)
```

> Esta medida cuenta los envíos que tienen una fecha de entrega informada.
> En este modelo, un envío se considera entregado cuando `Envíos[FechaEntrega]` no está en blanco.
> `CALCULATE` evalúa `[Total envíos]` en un contexto de filtro modificado, conservando únicamente las filas con fecha de entrega.

2. Crea esta medida:

```DAX
Total no entregados =
[Total envíos] - [Total entregados]
```

> Esta medida calcula cuántos envíos todavía no tienen fecha de entrega registrada.
> Reutiliza `[Total envíos]` y `[Total entregados]`, en lugar de volver a escribir la lógica de conteo.
> Este patrón facilita el mantenimiento porque si cambia la definición de `[Total entregados]`, esta medida se actualizará automáticamente.

3. Crea esta medida:

```DAX
Ingresos por envío =
DIVIDE(
    [Ingresos],
    [Total envíos]
)
```

> Esta medida calcula el ingreso medio por envío dentro del contexto de filtro actual.
> Por ejemplo, si la visualización está filtrada por servicio, cliente o año, el cálculo se realiza solo para ese subconjunto de envíos.
> Se usa `DIVIDE` en lugar del operador `/` porque gestiona de forma segura divisiones entre cero o valores en blanco.

4. Crea esta medida:

```DAX
Peso medio por envío =
DIVIDE(
    [Peso total KG],
    [Total envíos]
)
```

> Esta medida calcula el peso medio por envío.
> Reutiliza `[Peso total KG]` como numerador y `[Total envíos]` como denominador.
> Es útil para comparar servicios, rutas, destinos o segmentos de cliente con distintos perfiles de volumen operativo.

5. Configura las medidas:
   - `Total entregados`
     - **Formato**: Número entero
     - **Decimales**: 0
     - **Carpeta para mostrar**: `Indicadores operativos`
   - `Total no entregados`
     - **Formato**: Número entero
     - **Decimales**: 0
     - **Carpeta para mostrar**: `Indicadores operativos`
   - `Ingresos por envío`
     - **Formato**: Moneda
     - **Decimales**: 2
     - **Carpeta para mostrar**: `Indicadores económicos`
   - `Peso medio por envío`
     - **Formato**: Número decimal
     - **Decimales**: 2
     - **Carpeta para mostrar**: `Indicadores operativos`

### Tarea 3: Crear medidas derivadas

En esta tarea crearás medidas derivadas a partir de las medidas atómicas creadas anteriormente. Estas medidas no calculan directamente sobre columnas de la tabla `Envíos`, sino que reutilizan medidas ya existentes para construir indicadores más fáciles de mantener.

1. Crea esta medida:

```DAX
% entregados =
DIVIDE(
    [Total entregados],
    [Total envíos]
)
```

> Esta medida calcula qué proporción de los envíos del contexto actual tienen fecha de entrega informada.
> Reutiliza `[Total entregados]` como numerador y `[Total envíos]` como denominador.
> Por ejemplo, si la visualización está filtrada por un servicio concreto, el porcentaje se calculará solo para ese servicio.

2. Crea esta medida:

```DAX
% no entregados =
DIVIDE(
    [Total no entregados],
    [Total envíos]
)
```

> Esta medida calcula qué proporción de los envíos del contexto actual todavía no tienen fecha de entrega registrada.
> Reutiliza `[Total no entregados]` y `[Total envíos]`, por lo que hereda automáticamente cualquier cambio en la lógica de esas medidas base.
> Esta medida es complementaria a `% entregados`.

4. Formatea las medidas:
   - `% entregados`
     - **Formato**: Porcentaje
     - **Decimales**: 2
     - **Carpeta para mostrar**: `Indicadores operativos`
   - `% no entregados`
     - **Formato**: Porcentaje
     - **Decimales**: 2
     - **Carpeta para mostrar**: `Indicadores operativos`

> Estas medidas son derivadas porque reutilizan medidas existentes.
> Si cambia la definición de `[Total envíos]`, `[Total entregados]`, `[Total no entregados]`, `[Ingresos]` o `[Peso total KG]`, las medidas derivadas se actualizarán automáticamente.
> Este patrón se conoce como *measure branching* y ayuda a crear modelos más mantenibles.

### Tarea 4: Validar medidas derivadas

1. En la página `Contexto DAX`, añade una nueva matriz.
2. En filas, coloca `Servicio[Servicio]`.
3. En valores, añade:
   - `Total envíos`
   - `Total entregados`
   - `% entregados`
   - `Ingresos por envío`
   - `Ingresos por KG`
4. Filtra por mes y revisa que todas las medidas responden al contexto de filtro.

**Resultado esperado:** tienes una capa de medidas reutilizables, legible y mantenible.

---

## Ejercicio 4: Construir una medida compleja con variables

En este ejercicio crearás una medida de forma progresiva. Empezarás con una versión muy simple y terminarás con una medida más robusta que utiliza múltiples variables.

La medida final calculará un **índice operativo ponderado**. El índice combinará tres factores:

- penalización por entregas tardías;
- penalización por múltiples intentos de entrega;
- ponderación por ingresos.

> [!NOTE]
> Este índice es didáctico. Sirve para practicar patrones DAX avanzados y no representa una regla oficial de negocio de ParcelCraft.

### Tarea 1: Crear una versión inicial del índice

1. Crea esta medida:

```DAX
Índice operativo v1 =
DIVIDE(
    [Total entregados],
    [Total envíos]
)
```

> Esta primera versión solo mide la proporción de envíos entregados. Es sencilla, pero no considera retrasos, intentos ni valor económico.

2. Formatea la medida como porcentaje con 2 decimales.
3. Añádela a una matriz por `Servicio[Servicio]`.

### Tarea 2: Añadir penalización por entregas tardías

1. Crea esta medida:

```DAX
Índice operativo v2 =
VAR TotalEnvios = [Total envíos]
VAR EnviosTardios = [Envíos tardíos]
VAR EnviosEnPlazo = TotalEnvios - EnviosTardios
RETURN
    DIVIDE(
        EnviosEnPlazo,
        TotalEnvios
    )
```

> Esta versión usa variables para separar el cálculo en pasos más comprensibles. Primero obtiene el total, después los tardíos, después los envíos en plazo y finalmente calcula una proporción.

2. Formatea la medida como porcentaje con 2 decimales.
3. Añade la medida a la matriz.
4. Comprueba que los servicios con mayor `% envíos tardíos` tienen un índice menor.

### Tarea 3: Añadir penalización por intentos

1. Crea esta medida:

```DAX
Índice operativo v3 =
VAR TotalEnvios = [Total envíos]
VAR EnviosTardios = [Envíos tardíos]
VAR EnviosEnPlazo = TotalEnvios - EnviosTardios
VAR RatioEnPlazo = DIVIDE(EnviosEnPlazo, TotalEnvios)
VAR IntentosMedios = [Promedio intentos]
VAR PenalizacionIntentos =
    SWITCH(
        TRUE(),
        ISBLANK(IntentosMedios), BLANK(),
        IntentosMedios <= 1, 1.00,
        IntentosMedios <= 2, 0.95,
        IntentosMedios <= 3, 0.90,
        0.85
    )
RETURN
    RatioEnPlazo * PenalizacionIntentos
```

> `SWITCH(TRUE())` permite evaluar condiciones de forma ordenada. Esta técnica suele ser más clara que anidar muchos `IF`.

2. Formatea la medida como porcentaje con 2 decimales.
3. Añade `Promedio intentos` e `Índice operativo v3` a la matriz.
4. Revisa cómo el índice se reduce cuando aumentan los intentos medios.

### Tarea 4: Crear la versión final con múltiples variables e iteración

1. Crea esta medida:

```DAX
Índice operativo ponderado =
VAR IngresosTotales = [Ingresos]
VAR HayIngresos =
    NOT ISBLANK(IngresosTotales)
        && IngresosTotales <> 0
VAR ResultadoPonderado =
    SUMX(
    'Envíos',
        VAR IngresoEnvio = 'Envíos'[Precio total]
        VAR PesoIngreso = DIVIDE(IngresoEnvio, IngresosTotales)
        VAR EsTardio = 'Envíos'[Entrega tardía] = TRUE()
        VAR IntentosEnvio = 'Envíos'[Intentos]
        VAR FactorSLA =
            IF(
                EsTardio,
                0.70,
                1.00
            )
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

> Esta medida usa variables externas y variables internas.
> Las variables externas controlan el cálculo global del contexto actual.
> Las variables internas se evalúan para cada fila iterada por `SUMX`.
> El resultado pondera cada envío por su peso sobre los ingresos del contexto actual.

2. Formatea la medida como porcentaje con 2 decimales.
3. Asigna la medida a la carpeta `Indicadores operativos`.
4. Añade las medidas `Índice operativo v1`, `Índice operativo v2`, y `Índice operativo v3` a una carpeta `Índices operativos`
5. Oculta las medidas `Índice operativo v1`, `Índice operativo v2`, y `Índice operativo v3`
6. Elimina las medidas `v1`, `v2` y `v3` de la matriz.
7. Añade a una nueva matriz:
   - `Servicio[Servicio]`
   - `Ingresos`
   - `% envíos tardíos`
   - `Promedio intentos`
   - `Índice operativo ponderado`

### Tarea 5: Documentar la medida

1. Selecciona `Índice operativo ponderado`.
2. En el panel **Propiedades**, localiza **Descripción**.
3. Escribe:

```text
Índice didáctico que combina entregas tardías e intentos de entrega, ponderado por ingresos del envío dentro del contexto actual.
```

**Resultado esperado:** has construido una medida compleja de forma incremental y has usado variables para hacerla más legible y mantenible.

---

## Ejercicio 5: Usar funciones iteradoras

En este ejercicio practicarás funciones iteradoras adicionales. Las funciones iteradoras evalúan una expresión fila a fila y después agregan el resultado.

### Tarea 1: Crear ingresos ajustados por control de temperatura

1. Crea esta medida:

```DAX
Ingresos ajustados temperatura =
SUMX(
    'Envíos',
    'Envíos'[Precio total]
        * IF(
            'Envíos'[ControlTemperatura] = TRUE(),
            1.08,
            1
        )
)
```

> `SUMX` recorre la tabla `Envíos`. Para cada fila, calcula el precio ajustado y después suma los resultados.
>
> El factor 1,08 es una regla didáctica para simular un recargo del 8% en envíos con control de temperatura.

2. Formatea la medida como moneda con 2 decimales.
3. Asigna la medida a la carpeta `Iteradoras`.

### Tarea 2: Crear peso medio ponderado por ingresos

1. Crea esta medida:

```DAX
Peso medio ponderado por ingresos =
DIVIDE(
    SUMX(
        'Envíos',
         'Envíos'[Peso (KG)] *  'Envíos'[Precio total]
    ),
    [Ingresos]
)
```

> Esta medida calcula una media ponderada. Los envíos con mayor ingreso pesan más en el resultado final.

2. Formatea la medida como número decimal con 2 decimales.

### Tarea 3: Crear intentos ponderados por ingresos

1. Crea esta medida en la carpeta `Iteradoras`:

```DAX
Intentos ponderados por ingresos = 
DIVIDE(
    SUMX(
        'Envíos',
         'Envíos'[Intentos] *  'Envíos'[Precio total]
    ),
    [Ingresos]
)
```

2. Formatea la medida como número decimal con 2 decimales.

### Tarea 4: Validar iteradoras

1. Añade una matriz.
> Si te ves falto de espacio, duplica la página **Contexto DAX**, elimina las matrices existentes, y utilízala de ahora en adelante.

3. En filas, coloca `Servicio[Servicio]`.
4. En valores, coloca:
   - `Ingresos`
   - `Ingresos ajustados temperatura`
   - `Peso medio ponderado por ingresos`
   - `Intentos ponderados por ingresos`
   - `Índice operativo ponderado`
5. Añade un segmentador con `Fecha[Año]`.
6. Cambia el mes seleccionado y observa cómo se recalculan las medidas.

**Resultado esperado:** puedes comparar agregaciones simples con cálculos fila a fila usando iteradoras.

---

## Ejercicio 6: Crear medidas con USERELATIONSHIP()

En este ejercicio usarás relaciones inactivas para analizar la tabla `Envíos` por distintas fechas de negocio.

El modelo tiene una relación activa entre `Fecha[Fecha]` y `Envíos[FechaEnvío]`. También existen relaciones inactivas con `FechaCreación`, `FechaActualización`, `FechaEntrega` y `FechaCompromisoSLA`.

### Tarea 1: Crear medidas por fechas alternativas

1. Crea esta medida:

```DAX
Total envíos por fecha creación =
CALCULATE(
    [Total envíos],
    USERELATIONSHIP(Fecha[Fecha], 'Envíos'[FechaCreación])
)
```

2. Crea esta medida:

```DAX
Total envíos por fecha actualización =
CALCULATE(
    [Total envíos],
    USERELATIONSHIP(Fecha[Fecha], 'Envíos'[FechaActualización])
)
```

3. Crea esta medida:

```DAX
Total envíos por fecha entrega =
CALCULATE(
    [Total envíos],
    USERELATIONSHIP(Fecha[Fecha], 'Envíos'[FechaEntrega])
)
```

4. Crea esta medida:

```DAX
Total envíos por fecha compromiso SLA =
CALCULATE(
    [Total envíos],
    USERELATIONSHIP(Fecha[Fecha], 'Envíos'[FechaCompromisoSLA])
)
```

> `USERELATIONSHIP()` no crea una nueva relación. Activa temporalmente una relación inactiva mientras se evalúa la medida.

5. Asigna estas medidas a la carpeta `Roles de fecha`.

### Tarea 2: Comparar fechas en una visualización

1. Añade un gráfico de líneas.
2. En el eje X, coloca `Fecha[Año mes]`.
3. En valores, coloca:
   - `Total envíos por fecha creación`
   - `Total envíos por fecha entrega`
   - `Total envíos por fecha compromiso SLA`
4. Ordena el eje por `Fecha[Año mes]`.
5. En el panel **Filtros** > tarjeta **Año mes**, desmarca la opción **(En blanco)**.
6. Revisa las diferencias entre fechas.

**Resultado esperado:** puedes analizar la misma tabla de hechos usando distintas fechas sin duplicar la tabla `Fecha`.

---

## Ejercicio 7: Crear medidas de inteligencia de tiempo

En este ejercicio crearás medidas temporales para acumulados, comparativas y tendencias.

Las funciones de inteligencia de tiempo funcionan correctamente cuando la tabla `Fecha` está marcada como tabla de fechas y tiene una columna de fechas continua.

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

2. Formatea la medida como moneda con 2 decimales.
3. Asigna la medida a la carpeta `Inteligencia de tiempo`.

### Tarea 2: Crear ingresos del año anterior

1. Crea esta medida en la misma carpeta:

```DAX
Ingresos año anterior =
CALCULATE(
    [Ingresos],
    SAMEPERIODLASTYEAR(Fecha[Fecha])
)
```

> `SAMEPERIODLASTYEAR` desplaza el contexto de fechas un año hacia atrás.

2. Formatea la medida como moneda con 2 decimales.

### Tarea 3: Crear variación interanual

1. Crea esta medida en la misma carpeta:

```DAX
Variación ingresos YoY =
[Ingresos] - [Ingresos año anterior]
```

2. Crea esta medida en la misma carpeta
3. :

```DAX
Variación ingresos YoY % =
DIVIDE(
    [Variación ingresos YoY],
    [Ingresos año anterior]
)
```

3. Formatea `Variación ingresos YoY` como moneda con 2 decimales.
4. Formatea `Variación ingresos YoY %` como porcentaje con 2 decimales.

### Tarea 4: Crear media móvil de ingresos

1. Crea esta medida en la misma carpeta:

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

> `DATESINPERIOD` devuelve el conjunto de fechas de los últimos tres meses según el contexto actual. `AVERAGEX` evalúa `[Ingresos]` sobre ese conjunto y devuelve el promedio.

2. Formatea la medida como moneda con 2 decimales.

### Tarea 5: Validar inteligencia de tiempo

1. Añade una matriz.
2. En filas, coloca la jerarquía `Fecha[Fechas]`.
3. En valores, coloca:
   - `Ingresos`
   - `Ingresos YTD`
   - `Ingresos año anterior`
   - `Variación ingresos YoY %`
   - `Ingresos media móvil 3 meses`
4. Expande la jerarquía dos niveles para ver meses y trimestres dentro de años.
5. Revisa que `Ingresos YTD` se acumula dentro del año.

> El conjunto de datos de ejemplo solo tiene datos de 2023. Si quieres ver el análisis entero, vuelve a Power Query, importa la consulta [`ParcelCraft_2024_2025`](https://github.com/quizeth/Labs/blob/main/Explotaci%C3%B3n%20y%20Procesamiento%20de%20Datos%20con%20Power%20BI/Avanzado/Lab%2004/Files/ParcelCraft_2024_2025.csv) y anéxala a `stg_ParcelCraft_Raw`.
> **URL:** `https://raw.githubusercontent.com/quizeth/Labs/refs/heads/main/Explotaci%C3%B3n%20y%20Procesamiento%20de%20Datos%20con%20Power%20BI/Avanzado/Lab%2004/Files/ParcelCraft_2024_2025.csv`
> Si te da errores al aplicar los cambios, acéptalos y continúa sin resolverlos en este momento.

**Resultado esperado:** puedes construir acumulados, comparativas interanuales y tendencias temporales.

---

## Ejercicio 8: Validar y depurar cálculos avanzados

En este ejercicio aplicarás una técnica práctica de validación: comparar medidas simples con medidas más complejas en la misma visualización.

### Tarea 1: Crear una matriz de auditoría

1. Crea una nueva página llamada:

```text
Validación DAX avanzado
```

2. Añade una matriz.
3. En filas, coloca:
   - `Servicio[Servicio]`
4. En valores, coloca:
   - `Total envíos`
   - `Ingresos`
   - `% envíos tardíos`
   - `Promedio intentos`
   - `Índice operativo ponderado`

### Tarea 2: Añadir filtros de validación

1. Añade un segmentador con `Fecha[Año]`.
2. Añade un segmentador con `Cliente[Segmento]`.
3. Añade un segmentador con la jerarquía `Destino[Geografía]`.
4. Cambia los filtros y revisa si los índices evolucionan de forma coherente.

### Tarea 3: Interpretar resultados inesperados

Si una medida no muestra el resultado esperado, valida en este orden:

1. Comprueba que las relaciones del modelo están activas o inactivas según corresponda.
2. Comprueba si la medida está ignorando filtros mediante `REMOVEFILTERS`, `ALLSELECTED` o un grupo de cálculo.
3. Comprueba que las columnas usadas en condiciones contienen los valores esperados.
4. Divide la medida compleja en medidas temporales más pequeñas.
5. Usa una matriz con más campos de desglose para localizar dónde cambia el resultado.

**Resultado esperado:** puedes validar medidas avanzadas y diagnosticar problemas relacionados con contexto, filtros y relaciones.

---

## Entregables

Al finalizar, debes tener un archivo `.pbix` con:

1. Página `Contexto DAX`.
2. Medidas de modificación de contexto:
   - `Ingresos todos los servicios`
   - `% ingresos servicio`
   - `Ingresos selección visible`
   - `% ingresos selección visible`
   - `Ingresos servicios urgentes`
3. Medidas reutilizables y anidadas:
   - `Total entregados`
   - `Total no entregados`
   - `Ingresos por envío`
   - `Peso medio por envío`
   - `% entregados`
   - `% no entregados`
   - `Ingresos por KG`
4. Medida avanzada `Índice operativo ponderado`, construida con múltiples variables e iteración.
5. Medidas con funciones iteradoras:
   - `Ingresos ajustados temperatura`
   - `Peso medio ponderado por ingresos`
   - `Intentos ponderados por ingresos`
6. Medidas con `USERELATIONSHIP()` para fechas alternativas.
7. Medidas de inteligencia de tiempo:
   - `Ingresos YTD`
   - `Ingresos año anterior`
   - `Variación ingresos YoY`
   - `Variación ingresos YoY %`
   - `Ingresos media móvil 3 meses`
9. Página `Validación DAX avanzado`.
10. Medidas organizadas en carpetas de visualización.
11. Descripción funcional añadida a la medida `Índice operativo ponderado`.

---

## Resumen

En este laboratorio has trabajado con DAX desde la perspectiva de un desarrollador de modelos semánticos: primero analizaste el contexto de evaluación, después diseñaste medidas reutilizables, construiste una medida compleja con variables, aplicaste iteradoras, trabajaste con relaciones inactivas y creaste inteligencia de tiempo.

También has validado los resultados en visualizaciones para comprobar cómo las relaciones, los filtros y el contexto del visual influyen en cada medida.

### Recursos útiles

- Modificar contexto de filtro en DAX: https://microsoftlearning.github.io/PL-300-Microsoft-Power-BI-Data-Analyst/Instructions/Labs/05-modify-dax-filter-context.html
- Inteligencia de tiempo en DAX: https://microsoftlearning.github.io/PL-300-Microsoft-Power-BI-Data-Analyst/Instructions/Labs/06-use-dax-time-intelligence.html
- Funciones DAX: https://learn.microsoft.com/dax/

### 🎉 Fin del laboratorio

¡Enhorabuena! Has completado el Laboratorio 4 de ParcelCraft. Ahora puedes razonar sobre contexto, filtros y medidas avanzadas de forma mucho más sólida. 👏

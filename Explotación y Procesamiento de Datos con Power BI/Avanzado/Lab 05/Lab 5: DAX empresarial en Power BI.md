# Laboratorio 5: DAX empresarial en Power BI

**ParcelCraft** ya cuenta con un modelo en estrella, una tabla Fecha y medidas DAX avanzadas. En este laboratorio trabajarás como desarrollador de modelos semánticos: analizarás el comportamiento del modelo, centralizarás lógica de negocio con grupos de cálculo, validarás resultados con consultas DAX, revisarás metadatos con TMDL y aplicarás buenas prácticas de rendimiento y mantenibilidad.

El objetivo es pasar de crear medidas aisladas a diseñar una capa semántica más reutilizable, documentada, validada y preparada para escenarios empresariales.

## Objetivos de aprendizaje

**Tiempo estimado total: 45 minutos**

Al finalizar este laboratorio serás capaz de:

- Analizar cómo el contexto de filtro, las relaciones y los visuales afectan a las medidas DAX.
- Centralizar lógica reutilizable mediante grupos de cálculo y elementos de cálculo.
- Validar medidas y escenarios de negocio usando la Vista de consultas DAX.
- Explorar la definición del modelo semántico mediante TMDL.
- Identificar oportunidades de mejora en medidas DAX y patrones de modelado.
- Aplicar buenas prácticas para mejorar rendimiento, mantenibilidad y gobierno del modelo.
- Diagnosticar resultados inesperados combinando visualizaciones, consultas DAX y revisión del modelo.

---

## Antes de empezar

### Requisitos

Para completar este laboratorio necesitas:

- Power BI Desktop instalado.
- Haber completado el **Laboratorio 4: DAX avanzado, contexto de evaluación e inteligencia de tiempo en Power BI**, o partir de un archivo equivalente con las medidas y la tabla Fecha ya preparadas.

> **Nota:** algunas características como **grupos de cálculo**, **Vista de consultas DAX** o **Vista TMDL** pueden depender de la versión de Power BI Desktop y de características en vista previa. Si alguna opción no aparece, revisa **Archivo > Opciones y configuración > Opciones > Características en versión preliminar**, actívala si está disponible y reinicia Power BI Desktop.

> [!NOTE]
> Tienes un archivo inicial y solución disponibles. Si vas a trabajar en el mismo archivo a lo largo de todos los laboratorios, recomiendo empezar a partir de este starter.
> - [Archivo inicial (Starter)](https://github.com/quizeth/Labs/blob/main/Explotaci%C3%B3n%20y%20Procesamiento%20de%20Datos%20con%20Power%20BI/Avanzado/Lab%2005/Files/05-A%20Starter%20ParcelCraft.pbix)
> - [Archivo final (Solución)](https://github.com/quizeth/Labs/blob/main/Explotaci%C3%B3n%20y%20Procesamiento%20de%20Datos%20con%20Power%20BI/Avanzado/Lab%2005/Files/05-A%20Soluci%C3%B3n%20ParcelCraft.pbix)

### Convenciones usadas en el laboratorio

- Cuando se indique una opción de cinta, se mostrará como **pestaña > grupo > comando**.
- Las medidas se crearán en la tabla **Medidas**, salvo que se indique lo contrario.
- Los nombres de tablas, columnas, medidas y grupos de cálculo deben escribirse exactamente como se indican.
- Las expresiones DAX deben escribirse en la barra de fórmulas de Power BI Desktop.
- Las consultas DAX deben ejecutarse desde la **Vista de consultas DAX**.
- Los scripts TMDL se revisarán desde la **Vista TMDL**. No sustituyas objetos del modelo si no estás seguro del cambio.

---

## Ejercicio 1: Preparar una página de diagnóstico del modelo semántico

En este ejercicio crearás una página de análisis para comprobar cómo filtros, relaciones y visualizaciones afectan a las medidas. Esta página será la base para validar el resto del laboratorio.

Una medida DAX no se calcula una única vez. Se evalúa cada vez que un visual la necesita, dentro de un contexto de filtro formado por filas, columnas, segmentadores, filtros de página, relaciones del modelo y expresiones DAX como `CALCULATE()`.

### Tarea 1: Crear la página Diagnóstico modelo

- Ve a la **vista Informe**.
- Crea una nueva página.
- Cambia el nombre de la página a `Diagnóstico modelo`.
- Añade una matriz.
- En **Filas**, coloca:
  - Servicio[Servicio]
- En **Valores**, coloca:
  - Total envíos
  - Ingresos
  - Margen estimado
  - % margen estimado
  - % envíos entregados
- Añade una segmentación con:
  - Fecha[Año mes]
- Añade una segunda segmentación con:
  - Cliente[Segmento]
- Añade una tercera segmentación con:
  - Servicio[Prioridad]

Observa que la misma medida devuelve valores diferentes por cada servicio. Además, cambia al seleccionar un año, un segmento de cliente o una prioridad. Esto sucede porque cada selección modifica el contexto de filtro que llega a la tabla de hechos **Envíos** a través de las relaciones del modelo.

### Tarea 2: Crear y examinar medidas para observar el contexto de filtro

- En la tabla **Medidas**, crea la siguiente medida:

```DAX
% ingresos sobre todos los servicios =
DIVIDE(
    [Ingresos],
    [Ingresos todos los servicios]
)
```

Esta medida divide los ingresos del servicio actual entre los ingresos de todos los servicios bajo el resto de filtros activos. Es un ejemplo clásico de modificación de contexto: el numerador conserva el filtro de servicio y el denominador lo elimina.

- Configura la medida:
  - **Formato:** Porcentaje
  - **Decimales:** 2
  - **Carpeta para mostrar:** Diagnóstico de contexto

- Añade las medidas `Ingresos todos los servicios` y `% ingresos sobre todos los servicios` a la matriz.
- Cambia los segmentadores y observa qué filtros se respetan y cuáles no.
- Añade las medidas `Ingresos selección visible` y `% ingresos selección visible` a la matriz.
- Selecciona un valor en el segmentador **Servicio[Prioridad]** y compara las medidas.

**Resultado esperado:** puedes explicar por qué una medida compara contra todos los servicios y otra contra los servicios visibles después de aplicar selecciones externas.

---

## Ejercicio 2: Centralizar lógica de negocio con grupos de cálculo

En este ejercicio crearás un grupo de cálculo para aplicar transformaciones temporales reutilizables sobre medidas existentes. En lugar de crear medidas como **Ingresos YTD**, **Margen YTD**, **Total envíos YTD**, **Ingresos año anterior**, **Margen año anterior**, etc., crearás elementos de cálculo que se pueden aplicar a varias medidas.

Los grupos de cálculo son especialmente útiles cuando el modelo tiene muchas medidas base y el negocio solicita las mismas variantes de análisis: valor actual, acumulado anual, año anterior, variación y variación porcentual.

> **Importante:** los grupos de cálculo se aplican a medidas explícitas. Por eso es buena práctica evitar medidas implícitas creadas al arrastrar columnas numéricas directamente a un visual.

### Tarea 1: Crear el grupo de cálculo Escenario temporal

- Ve a la **vista Modelo**.
- En la cinta, selecciona **Modelado > Nuevo grupo de cálculo**.
- Si Power BI muestra un aviso para habilitar la propiedad que desaconseja medidas implícitas, acepta el cambio.
> Para configurar el grupo de cálculo, tenemos que trabajar en la pestaña **Modelo** del panel de datos.
- Cambia el nombre del grupo de cálculo a `Inteligencia de tiempo`.
- Cambia el nombre de la columna del grupo, si es necesario, a `Cálculo de tiempo`.

<img width="274" height="434" alt="image" src="https://github.com/user-attachments/assets/df161a16-3cff-4d61-971c-0086422f1d10" />

- Selecciona el **Elemento de cálculo** existente.
- En la barra de fórmulas, reemplaza el valor por esta expresión:

```DAX
Valor actual = SELECTEDMEASURE()
```

`SELECTEDMEASURE()` representa la medida que se esté evaluando en el visual. Si el usuario coloca **[Ingresos]**, el elemento devuelve ingresos; si coloca **[Margen estimado]**, devuelve margen; si coloca **[Total envíos]**, devuelve envíos.

### Tarea 2: Crear elementos de cálculo de inteligencia de tiempo

1. En el grupo de cálculo **Inteligencia de tiempo**, crea los siguientes elementos de cálculo. Para cada elemento, usa el nombre situado antes del signo igual como **nombre del elemento de cálculo** y pega como expresión el código situado después del signo igual.

```DAX
Valor actual =
SELECTEDMEASURE()
```

```DAX
YTD =
CALCULATE(
    SELECTEDMEASURE(),
    DATESYTD(Fecha[Fecha])
)
```

```DAX
Año anterior =
CALCULATE(
    SELECTEDMEASURE(),
    SAMEPERIODLASTYEAR(Fecha[Fecha])
)
```

```DAX
Variación YoY =
VAR ValorActual = SELECTEDMEASURE()
VAR ValorAnterior =
    CALCULATE(
        SELECTEDMEASURE(),
        SAMEPERIODLASTYEAR(Fecha[Fecha])
    )
RETURN
    ValorActual - ValorAnterior
```

```DAX
Variación YoY % =
VAR ValorActual = SELECTEDMEASURE()
VAR ValorAnterior =
    CALCULATE(
        SELECTEDMEASURE(),
        SAMEPERIODLASTYEAR(Fecha[Fecha])
    )
RETURN
    DIVIDE(
        ValorActual - ValorAnterior,
        ValorAnterior
    )
```

> `SELECTEDMEASURE()` representa la medida que se esté evaluando en el visual. De esta forma, los mismos elementos de cálculo pueden aplicarse a medidas como **Ingresos**, **Margen estimado** o **Total envíos** sin duplicar lógica DAX.

2. En el panel de datos, selecciona **Elementos de cálculo**.
3. Reordena los elementos:
   1. Valor actual
   2. Año anterior
   3. YTD
   4. Variación YoY
   5. Variación YoY %
      
### Tarea 3: Configurar formato dinámico de los elementos

1. Selecciona el elemento **Valor actual**.
2. En **Cadena de formato dinámico**, marca **Sí** y escribe:

```DAX
SELECTEDMEASUREFORMATSTRING()
```

- Repite para:
  - YTD
  - Año anterior
  - Variación YoY

Esto conserva el formato original de la medida seleccionada. Por ejemplo, si la medida base es moneda, el resultado se muestra como moneda; si es número entero, se muestra como número.

- Para **Variación YoY %**, usa:

```DAX
"0.00%"
```

### Tarea 4: Usar el grupo de cálculo en una visualización

- Vuelve a la página **Diagnóstico modelo**.
- Añade una nueva matriz.
- En **Filas**, coloca:
  - Fecha[Año]
  - Fecha[Año mes]
- En **Columnas**, coloca:
  - Inteligencia de tiempo[Cálculo de tiempo]
- En **Valores**, coloca:
  - Ingresos
- Ordena **Fecha[Año mes]** de forma ascendente.
- Filtra el visual para quitar valores en blanco, si aparecen.

La matriz debe mostrar los ingresos bajo distintos escenarios temporales sin haber creado una medida independiente para cada escenario.

- Duplica la matriz.
- Sustituye **Ingresos** por **Margen estimado**.
- Revisa que el mismo grupo de cálculo se aplica ahora sobre otra medida.
- Añade una segmentación con la columna Inteligencia de tiempo[Cálculo de tiempo].
- Juega con los filtros para aplicar solo algunas medidas del grupo de cálculo.

### Tarea 5: Evaluar cuándo usar grupos de cálculo

Lee las siguientes recomendaciones y compáralas con el modelo:

- Usa grupos de cálculo cuando una misma transformación se aplica a muchas medidas.
- Usa medidas tradicionales cuando el cálculo representa una métrica de negocio específica y no una transformación reutilizable.
- Evita crear grupos de cálculo para casos aislados que solo se aplican a una medida.
- Documenta bien los elementos de cálculo, porque modifican medidas de forma dinámica y pueden sorprender a usuarios si no se explican.

**Resultado esperado:** has creado un grupo de cálculo de inteligencia de tiempo y lo has aplicado a varias medidas sin duplicar lógica DAX.

---

## Ejercicio 3: Explorar y validar cálculos mediante consultas DAX

En este ejercicio usarás la Vista de consultas DAX para validar medidas y grupos de cálculo sin depender únicamente de visualizaciones. Esta vista permite ejecutar consultas ad hoc contra el modelo semántico y comprobar resultados de forma explícita.

### Tarea 1: Abrir la Vista de consultas DAX

- En el panel izquierdo de Power BI Desktop, selecciona la **Vista de consultas DAX**.
- Crea una nueva consulta.
- Escribe la siguiente consulta:

```DAX
EVALUATE
SUMMARIZECOLUMNS(
    Servicio[Servicio],
    "Total envíos", [Total envíos],
    "Ingresos", [Ingresos],
    "Margen estimado", [Margen estimado],
    "% margen estimado", [% margen estimado]
)
ORDER BY
    Servicio[Servicio]
```

- Ejecuta la consulta.

Esta consulta reproduce de forma controlada lo que haría una visualización por servicio. `SUMMARIZECOLUMNS()` crea grupos por **Servicio[Servicio]** y evalúa las medidas en el contexto de cada grupo.

### Tarea 2: Validar filtros temporales desde una consulta

- Crea una nueva consulta DAX.
- Escribe:

```DAX
EVALUATE
SUMMARIZECOLUMNS(
    Fecha[Año],
    Fecha[Año mes],
    "Ingresos", [Ingresos],
    "Ingresos selección visible", [Ingresos selección visible],
    "Total envíos", [Total envíos]
)
ORDER BY
    Fecha[Año],
    Fecha[Año mes]
```

- Ejecuta la consulta.
- Revisa si aparecen meses sin datos.
- Si aparecen filas en blanco o periodos fuera del rango esperado, vuelve al modelo y revisa relaciones, filtros y la tabla Fecha.

### Tarea 3: Probar medidas temporales sin crear visuales

- Crea una consulta con una medida temporal local usando `DEFINE MEASURE`:

```DAX
DEFINE
    MEASURE Medidas[Ingresos YTD prueba] =
        TOTALYTD(
            [Ingresos],
            Fecha[Fecha]
        )
EVALUATE
SUMMARIZECOLUMNS(
    Fecha[Año],
    Fecha[Año mes],
    "Ingresos", [Ingresos],
    "Ingresos YTD prueba", [Ingresos YTD prueba]
)
ORDER BY
    Fecha[Año],
    Fecha[Año mes]
```

`DEFINE MEASURE` permite crear medidas temporales dentro de la consulta. Son útiles para probar una expresión antes de incorporarla al modelo.

- Ejecuta la consulta.
- Comprueba que **Ingresos YTD prueba** acumula los ingresos dentro de cada año.

### Tarea 4: Validar el grupo de cálculo desde DAX

Para consultar un grupo de cálculo, puedes incluir su columna como agrupación.

- Crea una nueva consulta:

```DAX
EVALUATE
SUMMARIZECOLUMNS(
    Fecha[Año],
    Fecha[Año mes],
    'Inteligencia de tiempo'[Cálculo de tiempo],
    "Ingresos transformados", [Ingresos]
)
ORDER BY
    Fecha[Año],
    Fecha[Año mes],
    'Inteligencia de tiempo'[Cálculo de tiempo]
```

- Ejecuta la consulta.
- Revisa que para cada mes aparecen varios escenarios temporales.
- Si **Año anterior** o **Variación YoY %** devuelve blanco, comprueba si tu conjunto de datos contiene más de un año de información.

### Tarea 5: Crear una consulta de depuración de contexto

- Crea una nueva consulta:

```DAX
EVALUATE
SUMMARIZECOLUMNS(
    Servicio[Prioridad],
    Servicio[Servicio],
    Cliente[Segmento],
    "Ingresos", [Ingresos],
    "Ingresos todos los servicios", [Ingresos todos los servicios],
    "% ingresos sobre todos los servicios", [% ingresos sobre todos los servicios]
)
ORDER BY
    Servicio[Prioridad],
    Servicio[Servicio],
    Cliente[Segmento]
```

- Ejecuta la consulta.
- Interpreta una fila concreta:
  - **Servicio[Servicio]** filtra un servicio.
  - **Cliente[Segmento]** filtra un segmento.
  - Las relaciones propagan los filtros hacia **Envíos**.
  - **[Ingresos]** respeta ambos filtros.
  - **[Ingresos todos los servicios]** elimina los filtros de la tabla Servicio, pero conserva el filtro de Cliente.

**Resultado esperado:** puedes usar consultas DAX para validar medidas, depurar contexto y comprobar grupos de cálculo sin crear visuales adicionales.

---

## Ejercicio 4: Explorar la definición del modelo mediante TMDL

En este ejercicio usarás la Vista TMDL para inspeccionar la definición declarativa del modelo semántico. TMDL permite ver objetos como tablas, columnas, medidas, relaciones y grupos de cálculo en formato textual. Esto facilita documentación, revisión de cambios y colaboración entre desarrolladores.

> **Importante:** este ejercicio se centra en exploración y comprensión. Si no tienes experiencia con TMDL, no apliques cambios masivos al modelo. Usa la vista para inspeccionar objetos, copiar definiciones y entender cómo está representado el modelo.

### Tarea 1: Abrir la Vista TMDL

- En Power BI Desktop, selecciona la **Vista TMDL** en el panel izquierdo.
- Si la vista aparece vacía, selecciona objetos desde el panel **Datos** y arrástralos al editor TMDL.
- Arrastra la tabla **Medidas** al editor.

Power BI generará una representación textual de la tabla y sus medidas. Busca medidas como:

- Total envíos
- Ingresos
- Margen estimado
- Ingresos todos los servicios
- % ingresos selección visible

### Tarea 2: Interpretar una medida en TMDL

- Localiza la definición de la medida **Ingresos**.
- Identifica estos elementos:
  - Nombre de la medida.
  - Expresión DAX.
  - Cadena de formato.
  - Carpeta de visualización, si está definida.
  - Descripción, si existe.

Una medida que en la interfaz aparece como un único objeto se representa en TMDL como metadatos declarativos. Esto permite revisar cambios de forma más clara, comparar versiones y documentar el modelo.


### Tarea 3: Añadir documentación desde la interfaz y revisar TMDL

- Vuelve a la **vista Modelo**.
- Selecciona la medida: `% ingresos selección visible`

- En el panel **Propiedades**, localiza **Descripción**.
- Escribe:

```text
Calcula la participación de ingresos del contexto actual respecto al total de servicios visibles tras aplicar selecciones externas.
```

- Vuelve a la **Vista TMDL**.
- En el script de la tabla **Medidas**, selecciona **Vista previa**.
- Comprueba que la descripción aparece como propiedad de la medida.

**Resultado esperado:** puedes inspeccionar y entender cómo TMDL ayuda a documentar y gobernar cambios.

---

## Ejercicio 5: Optimizar rendimiento y mantenibilidad de medidas

En este ejercicio analizarás una medida existente y crearás una alternativa más mantenible. El objetivo no es perseguir microoptimizaciones, sino distinguir entre patrones que obligan a iterar fila a fila y patrones que pueden resolverse mediante agregaciones simples.

### Tarea 1: Crear medidas base de coste

1. En la tabla **Medidas**, crea esta medida:

```DAX
Coste base =
SUM('Envíos'[Precio base])
```

2. Crea esta medida:

```DAX
Coste fuel =
SUM('Envíos'[Surplus fuel])
```

3. Configura ambas medidas:
  - **Formato:** Moneda
  - **Decimales:** 2
  - **Carpeta para mostrar:** Indicadores económicos

Estas medidas convierten columnas de coste en medidas reutilizables. Esto facilita leer y mantener cálculos derivados.

### Tarea 2: Crear una versión optimizada del margen

La medida **Margen estimado** creada anteriormente suele tener esta forma:

```DAX
Margen estimado =
SUMX(
    'Envíos',
    'Envíos'[Precio total]
        - 'Envíos'[Precio base]
        - 'Envíos'[Surplus fuel]
)
```

`SUMX()` recorre fila a fila la tabla **Envíos**. En este caso, como el cálculo es lineal y solo resta columnas aditivas, puede expresarse con agregaciones simples.

1. Crea esta medida:

```DAX
Margen estimado optimizado =
[Ingresos] - [Coste base] - [Coste fuel]
```

2. Configura la medida:
  - **Formato:** Moneda
  - **Decimales:** 2
  - **Carpeta para mostrar:** Indicadores económicos

Esta versión es más legible y reutiliza medidas base. Además, evita escribir la lógica de suma de costes en varios lugares.

### Tarea 3: Validar equivalencia con una consulta DAX

1. Abre la **Vista de consultas DAX**.
2. Crea una nueva consulta:

```DAX
EVALUATE
SUMMARIZECOLUMNS(
    Servicio[Servicio],
    "Margen original", [Margen estimado],
    "Margen optimizado", [Margen estimado optimizado],
    "Diferencia", [Margen estimado] - [Margen estimado optimizado]
)
ORDER BY
    Servicio[Servicio]
```

3. Ejecuta la consulta.
4. Revisa la columna **Diferencia**.

Si la diferencia es cero o prácticamente cero para todos los servicios, la medida optimizada es equivalente para el modelo actual.

5. Elimina la medida `Margen estimado`.
6. Renombra la medida optimizada a `Margen estimado`.


### Tarea 4: Usar Analizador de rendimiento

- Ve a la página **Diagnóstico modelo**.
- En la cinta, selecciona **Vista > Analizador de rendimiento**.
- Selecciona **Iniciar grabación**.
- Actualiza los visuales de la página.
- Revisa qué visuales tardan más en ejecutarse.
- Expande uno de los visuales más lentos.
- Selecciona **Copiar consulta** si está disponible.
- Pega la consulta en un bloc de notas o en la Vista de consultas DAX para revisarla.

Al revisar una consulta generada por un visual, busca:

- Columnas usadas para agrupar.
- Medidas evaluadas.
- Filtros aplicados por segmentadores.
- Uso de tablas de dimensiones.
- Si el visual solicita más granularidad de la necesaria.

### Tarea 6: Aplicar criterios de optimización

Revisa tus medidas y aplica estas reglas:

- Reutiliza medidas base en lugar de repetir sumas o filtros.
- Usa `SUMX()` cuando el cálculo requiera evaluar una expresión fila a fila.
- Evita `SUMX()` si el cálculo puede resolverse con sumas simples de columnas aditivas.
- Usa variables para mejorar legibilidad y evitar repetir expresiones complejas.
- Evita eliminar filtros de tablas completas si basta con eliminar filtros de una columna específica.
- Prefiere modelos en estrella con relaciones simples antes que lógica DAX para corregir problemas de modelado.

**Resultado esperado:** has creado una versión optimizada de una medida, has validado equivalencia con DAX y has revisado el comportamiento de visuales con el Analizador de rendimiento.

---

## Ejercicio 6: Validar y diagnosticar la solución analítica

En este ejercicio construirás una página de auditoría para verificar que las medidas, los grupos de cálculo y el modelo responden de forma coherente a filtros de negocio.

### Tarea 1: Crear la página Validación semántica

- Crea una nueva página llamada: `Validación semántica`

- Añade una matriz.
- En **Filas**, coloca:
  - Destino[Geografía]
- En **Valores**, coloca:
  - Total envíos
  - Ingresos
  - Margen estimado
  - % margen estimado
- Expande la matriz para ver la región y ciudad.

### Tarea 2: Añadir segmentadores de validación

- Añade una segmentación con:
  - Fecha[Fechas]
- Añade una segmentación con:
  - Cliente[Segmento]
- Añade una segmentación con:
  - Servicio[Servicio]

Cambia los filtros y confirma que todas las medidas se recalculan de forma coherente.

### Tarea 3: Diagnosticar resultados inesperados

Si una medida no devuelve el resultado esperado, valida en este orden:

1. Comprueba que la relación correcta está activa.
2. Si estás usando fechas alternativas, comprueba si necesitas `USERELATIONSHIP()`.
3. Verifica que la tabla Fecha está marcada como tabla de fechas.
4. Comprueba si la medida elimina filtros con `REMOVEFILTERS()` o `ALLSELECTED()`.
5. Revisa si un grupo de cálculo está modificando la medida seleccionada.
6. Ejecuta una consulta DAX con `SUMMARIZECOLUMNS()` para aislar el cálculo.
7. Divide una medida compleja en medidas temporales más pequeñas.
8. Revisa si hay filtros de página, visual o segmentadores que no habías considerado.

### Tarea 4: Documentar decisiones de diseño

- Selecciona la medida **Margen estimado**.
- En **Propiedades > Descripción**, escribe:

```text
Calcula el margen como ingresos menos coste base y coste de fuel, reutilizando medidas base para mejorar legibilidad y mantenibilidad.
```

- Selecciona el grupo de cálculo **Inteligencia de tiempo**.
- Añade una descripción:

```text
Grupo de cálculo que centraliza escenarios temporales reutilizables como valor actual, YTD, año anterior y variación interanual.
```

**Resultado esperado:** tienes una página de validación para comprobar la precisión de medidas, el comportamiento de filtros y el efecto de los grupos de cálculo.

---

## Entregables

Al finalizar, debes tener un archivo `.pbix` con:

- Página **Diagnóstico modelo**.
- Medidas de diagnóstico de contexto.
- Grupo de cálculo **Inteligencia de tiempo** con columna **Cálculo de tiempo**.
- Elementos de cálculo temporales creados y con formato dinámico.
- Visuales aplicando el grupo de cálculo a **Ingresos** y **Margen estimado**.
- Consultas DAX de validación ejecutadas.
- Exploración del modelo en **Vista TMDL**.
- Medidas **Coste base**, **Coste fuel** y **Margen estimado** optimizada.
- Validación DAX de equivalencia del margen.
- Revisión de al menos un visual con **Analizador de rendimiento**.
- Página **Validación semántica** con matriz y segmentadores.
- Descripciones añadidas a medidas y grupo de cálculo clave.

---

## Resumen

En este laboratorio has trabajado DAX desde una perspectiva de modelo semántico empresarial. Has analizado contexto de filtro, creado medidas de diagnóstico, centralizado lógica temporal con grupos de cálculo, validado cálculos mediante consultas DAX, explorado la definición del modelo con TMDL y aplicado criterios de optimización y mantenibilidad.

La idea principal es que un buen modelo semántico no depende solo de medidas correctas, sino de medidas **comprensibles, reutilizables, documentadas, validadas y eficientes**.

## Recursos útiles

- Modificar contexto de filtro en DAX: https://microsoftlearning.github.io/PL-300-Microsoft-Power-BI-Data-Analyst/Instructions/Labs/05-modify-dax-filter-context.html
- Inteligencia de tiempo en DAX: https://microsoftlearning.github.io/PL-300-Microsoft-Power-BI-Data-Analyst/Instructions/Labs/06-use-dax-time-intelligence.html
- Grupos de cálculo en Power BI: https://learn.microsoft.com/power-bi/transform-model/calculation-groups
- Vista de consultas DAX en Power BI Desktop: https://learn.microsoft.com/power-bi/transform-model/dax-query-view
- Vista TMDL en Power BI Desktop: https://learn.microsoft.com/power-bi/transform-model/desktop-tmdl-view
- Funciones DAX: https://learn.microsoft.com/dax/

## 🎉 Fin del laboratorio

¡Enhorabuena! Has completado el Laboratorio 5 de ParcelCraft. Ahora puedes diseñar, validar, documentar y optimizar cálculos DAX con un enfoque más cercano al desarrollo profesional de modelos semánticos en Power BI. 👏

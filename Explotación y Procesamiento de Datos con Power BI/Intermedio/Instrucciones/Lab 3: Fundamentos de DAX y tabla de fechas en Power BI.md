# Laboratorio 3: Fundamentos de DAX y tabla de fechas en Power BI
  
**ParcelCraft** necesita empezar a construir cálculos analíticos reutilizables sobre el modelo semántico creado en los laboratorios anteriores.  
En este laboratorio crearás tablas calculadas, columnas calculadas y medidas con DAX. También crearás y configurarás la tabla **Fecha**, sus calendarios y sus relaciones con **Envíos**, de forma que el modelo quede preparado para cálculos temporales y análisis operativo.

---

## Objetivos de aprendizaje
  
**Tiempo estimado total: 60 minutos**  
Al finalizar este laboratorio serás capaz de:
- Crear tablas y columnas calculadas con DAX.
- Crear y formatear medidas básicas.
- Crear y configurar una tabla de fechas.

---

## Antes de empezar

### Requisitos
  
Para completar este laboratorio necesitas:
- Power BI Desktop instalado.
- Haber completado el **Laboratorio 2: Modelado de datos y esquema en estrella en Power BI**.
- Conocimientos básicos de relaciones en Power BI.

Tienes un archivo inicial y solución disponibles. Si vas a trabajar en el mismo archivo a lo largo de todos los laboratorios, recomiendo empezar a partir de este starter.
- [Archivo inicial (Starter)](https://github.com/quizeth/Labs/blob/main/Explotaci%C3%B3n%20y%20Procesamiento%20de%20Datos%20con%20Power%20BI/Intermedio/Files/03%20Starter%20ParcelCraft.pbix)
- [Archivo final (Solución)](https://github.com/quizeth/Labs/blob/main/Explotaci%C3%B3n%20y%20Procesamiento%20de%20Datos%20con%20Power%20BI/Intermedio/Files/03%20Soluci%C3%B3n%20ParcelCraft.pbix)

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

Nota: al deshabilitar **Fecha/Hora automática**, Power BI deja de crear tablas de fecha ocultas para cada columna de fecha del modelo.

### Tarea 2: Crear una tabla calculada de fechas

1. Ve a la **vista Modelo**.
2. Selecciona **Modelado > Nueva tabla**.
3. Escribe la siguiente expresión DAX:

```DAX
Fecha =
CALENDARAUTO ()
```

4. Presiona **Entrar**.
5. Cambia el nombre de la columna **Date** a **Fecha**.

Esta tabla crea automáticamente un calendario continuo a partir de las fechas presentes en el modelo. En este laboratorio, **FechaEnvío** será la fecha principal del modelo. Más adelante crearás relaciones inactivas con otras fechas relevantes, como **FechaCreación**, **FechaActualización**, **FechaEntrega** y **FechaCompromisoSLA**.

### Tarea 3: Crear columnas de calendario gregoriano

1. Selecciona la tabla **Fecha**.
2. En la cinta, selecciona **Modelado > Nueva columna**.
3. Crea la columna:

```DAX
Año = YEAR ( Fecha[Fecha] )
```

4. Crea la columna:

```DAX
Número de mes = MONTH ( Fecha[Fecha] )
```

5. Crea la columna:

```DAX
Mes = FORMAT ( Fecha[Fecha], "mmmm" )
```

6. Crea la columna:

```DAX
Año mes = FORMAT ( Fecha[Fecha], "yyyy-MM" )
```

7. Crea la columna:

```DAX
Trimestre = "Q" & FORMAT ( Fecha[Fecha], "Q" )
```

8. Crea la columna:

```DAX
Día = DAY ( Fecha[Fecha] )
```

9. Crea la columna:

```DAX
Día de la semana = FORMAT ( Fecha[Fecha], "dddd" )
```

10. Crea la columna:

```DAX
Número día semana = WEEKDAY ( Fecha[Fecha], 2 )
```

11. Crea la columna:
```DAX
Mes año = FORMAT(Fecha[Fecha], "mmm YYYY")
```

### Tarea 4: Marcar la tabla como tabla de fechas

1. Selecciona la tabla **Fecha**.
2. Selecciona **Herramientas de tabla > Marcar como tabla de fechas**.
3. Selecciona la columna **Fecha**.
4. Pulsa **Aceptar**.

Importante: **Marcar como tabla de fechas** no crea una tabla nueva. Solo indica a Power BI que la tabla **Fecha** debe usarse como referencia temporal principal del modelo.

### Tarea 6 (Opcional): Crear calendarios basados en la tabla Fecha
  
Si tu versión de Power BI Desktop muestra **Opciones del calendario**, crea estos calendarios sobre la tabla **Fecha**.
> Si no ves la opción, puedes activarla en **Archivo > Opciones y configuración > Opciones > Global > Características de versión preliminar** y activando la **Inteligencia de tiempo DAX mejorada**.

#### Calendario Gregoriano

<table>
<tr>
<th>Categoría</th>
<th>Columna principal</th>
<th>Columnas asociadas</th>
</tr>
<tr>
<td>Año</td>
<td>Año</td>
<td>—</td>
</tr>
<tr>
<td>Trimestre del año</td>
<td>Trimestre</td>
<td>—</td>
</tr>
<tr>
<td>Mes</td>
<td>Año mes</td>
<td>—</td>
</tr>
<tr>
<td>Mes del año</td>
<td>Mes</td>
<td>Número de mes</td>
</tr>
<tr>
<td>Fecha</td>
<td>Fecha</td>
<td>—</td>
</tr>
</table>

> Nota: no asignes la misma columna a varias categorías dentro del mismo calendario.

### Tarea 7: Ordenar columnas temporales

1. Selecciona **Fecha[Mes]**.
2. Selecciona **Herramientas de columna > Ordenar por columna**.
3. Elige **Número de mes**.
4. Selecciona **Fecha[Día de la semana]**.
5. Ordénala por **Número día semana**.

### Tarea 8: Crear relaciones con Envíos
  
Crea estas relaciones entre **Fecha** y **Envíos**:

<table>
<tr>
<th>Tabla 1</th>
<th>Columna</th>
<th>Tabla 2</th>
<th>Columna</th>
<th>Cardinalidad</th>
<th>Filtro cruzado</th>
<th>Activa</th>
</tr>
<tr>
<td>Fecha</td>
<td>Fecha</td>
<td>Envíos</td>
<td>FechaEnvío</td>
<td>Uno a varios (1:*)</td>
<td>Simple</td>
<td><strong>Sí</strong></td>
</tr>
<tr>
<td>Fecha</td>
<td>Fecha</td>
<td>Envíos</td>
<td>FechaCreación</td>
<td>Uno a varios (1:*)</td>
<td>Simple</td>
<td>No</td>
</tr>
<tr>
<td>Fecha</td>
<td>Fecha</td>
<td>Envíos</td>
<td>FechaActualización</td>
<td>Uno a varios (1:*)</td>
<td>Simple</td>
<td>No</td>
</tr>
<tr>
<td>Fecha</td>
<td>Fecha</td>
<td>Envíos</td>
<td>FechaEntrega</td>
<td>Uno a varios (1:*)</td>
<td>Simple</td>
<td>No</td>
</tr>
<tr>
<td>Fecha</td>
<td>Fecha</td>
<td>Envíos</td>
<td>FechaCompromisoSLA</td>
<td>Uno a varios (1:*)</td>
<td>Simple</td>
<td>No</td>
</tr>
</table>

**Resultado esperado:** **FechaEnvío** queda como fecha principal del modelo y el resto de fechas queda disponible mediante relaciones inactivas.


### Tarea 9: Validar la tabla de fechas
1. Ve a la vista de **Informe**.
2. Para agregar un visual de matriz a la nueva página del informe, en el panel **Visualizaciones**, seleccione el tipo de visual **Matriz**.
> Puedes colocar el cursor sobre cada icono para mostrar una información sobre herramientas que describa el tipo de visual.
3. En el panel **Datos**, dentro de la tabla **Fecha**, arrastre el campo **Año** al área **Filas**.
4. Arrastra el campo **Mes año** al área **Filas**, justo debajo de **Año**.
5. En la esquina superior derecha del visual de matriz (o en la parte inferior, según la ubicación del visual), selecciona el icono de doble flecha bifurcada, que expandirá todos los años un nivel hacia abajo.
<img width="590" height="193" alt="image" src="https://github.com/user-attachments/assets/30ea39ea-2942-41eb-821a-565efb87f70a" />
6. Observa que los años se expanden para mostrar los meses y que estos aparecen ordenados alfabéticamente en lugar de cronológicamente.
> De forma predeterminada, los valores de texto se ordenan alfabéticamente, los números se ordenan de menor a mayor y las fechas se ordenan de la más antigua a la más reciente.
7. Para personalizar el orden de clasificación del campo **Año mes**, cambia a la vista **Tabla**.
8. Agrega la columna **MesClave** a la tabla **Fecha**:
```DAX
MesClave = (YEAR('Fecha'[Fecha]) * 100) + MONTH(Fecha[Fecha])
```
> Esta fórmula calcula un valor numérico para cada combinación de año y mes.

9. En la vista **Tabla**, comprueba que la nueva columna contenga valores numéricos (por ejemplo, 201707 para julio de 2017, y así sucesivamente).
10. Vuelve a la vista **Informe**.
11. En el panel **Datos**, selecciona el campo **Año mes**.
12. En la cinta contextual **Herramientas de columna**, dentro del grupo **Ordenar**, selecciona **Ordenar por columna** y, a continuación, selecciona MonthKey.
12. En el visual de matriz, observa que los meses ahora aparecen ordenados cronológicamente.


## Ejercicio 2: Crear medidas calculadas con DAX
En este ejercicio crearás medidas derivadas para analizar margen, entregas y duración media. También crearás columnas calculadas en la tabla **Fecha**, donde sí tiene sentido almacenar atributos temporales que se usarán como filtros, segmentadores o agrupaciones.  

Buena práctica: usa **medidas** para cálculos agregados que deben responder al contexto de filtro. Usa **columnas calculadas** cuando necesites atributos persistidos para segmentar, clasificar, ordenar o crear jerarquías.

### Tarea 1: Crear una tabla de medidas

1. Selecciona **Modelado > Nueva tabla**.
2. Escribe:

```DAX
Medidas =
ROW ( "Ocultar", 1 )
```

3. Oculta la columna **Medidas[Ocultar]** en la vista de informe.

**Resultado esperado:** tienes una tabla para organizar medidas.

### Tarea 2: Crear medidas derivadas y columnas calculadas en la tabla Fecha
  
Antes de crear medidas derivadas, necesitas algunas medidas base que serán reutilizadas por otros cálculos.

1. En la tabla **Medidas**, crea la siguiente medida:

```DAX
Total envíos =
COUNTROWS ( 'Envíos' )
```

Esta medida cuenta el número de filas de la tabla **Envíos**. Como cada fila representa un envío, devuelve el número total de envíos dentro del contexto de filtro actual.

2. Crea una segunda medida:

```DAX
Ingresos =
SUM ( 'Envíos'[Precio total] )
```

Esta medida suma el importe total de los envíos y responde al contexto de filtro actual.

3. Configura la medida **Total envíos**:
   - **Formato**: Número entero
   - **Separador de miles**: Activado
   - **Decimales**: 0
   - **Carpeta para mostrar**: Medidas base
4. Configura la medida **Ingresos**:
   - **Formato**: Moneda
   - **Decimales**: 2
   - **Carpeta para mostrar**: Medidas base

5. En la tabla **Medidas**, crea la siguiente medida:

```DAX
Margen estimado =
SUMX (
    'Envíos',
    'Envíos'[Precio total]
        - 'Envíos'[Precio base]
        - 'Envíos'[Surplus fuel]
)
```

Esta medida calcula el margen estimado fila a fila y después suma el resultado. La clave es que no usamos `SUM('Envíos'[Precio total] - ...)`, porque **SUM** solo agrega una columna. Como el cálculo combina varias columnas por fila, usamos **SUMX**.

6. Configura la medida:
   - **Formato**: Moneda
   - **Decimales**: 2
   - **Carpeta para mostrar**: Indicadores económicos

7. En la tabla **Medidas**, crea la siguiente medida:

```DAX
Margen medio por envío =
DIVIDE (
    [Margen estimado],
    [Total envíos]
)
```

Esta medida calcula el margen medio por envío. Se usa **DIVIDE** en lugar del operador `/` porque gestiona de forma segura divisiones entre cero o valores en blanco.

8. Configura la medida:
   - **Formato**: Moneda
   - **Decimales**: 2
   - **Carpeta para mostrar**: Indicadores económicos

9. En la tabla **Medidas**, crea la siguiente medida:

```DAX
% margen estimado =
DIVIDE (
    [Margen estimado],
    [Ingresos]
)
```

Esta medida calcula qué proporción de los ingresos queda como margen estimado.

8. Configura la medida:
   - **Formato**: Porcentaje
   - **Decimales**: 2
   - **Carpeta para mostrar**: Indicadores económicos

9. En la tabla **Medidas**, crea la siguiente medida:

```DAX
Días medios hasta entrega =
AVERAGEX (
    FILTER (
        'Envíos',
        NOT ISBLANK ( 'Envíos'[FechaEntrega] )
    ),
    DATEDIFF (
        'Envíos'[FechaEnvío],
        'Envíos'[FechaEntrega],
        DAY
    )
)
```

Esta medida calcula el número medio de días transcurridos entre la fecha de envío y la fecha de entrega. Se excluyen los envíos sin fecha de entrega para evitar que los registros pendientes distorsionen el promedio.

10. Configura la medida:
   - **Formato**: Número decimal
   - **Decimales**: 2
   - **Carpeta para mostrar**: Indicadores operativos

11. En la tabla **Medidas**, crea la siguiente medida:

```DAX
Días máximos hasta entrega =
MAXX (
    FILTER (
        'Envíos',
        NOT ISBLANK ( 'Envíos'[FechaEntrega] )
    ),
    DATEDIFF (
        'Envíos'[FechaEnvío],
        'Envíos'[FechaEntrega],
        DAY
    )
)
```

Esta medida identifica el mayor tiempo de entrega dentro del contexto seleccionado.

12. Configura la medida:
   - **Formato**: Número entero
   - **Decimales**: 0
   - **Carpeta para mostrar**: Indicadores operativos

13. En la tabla **Medidas**, crea la siguiente medida:

```DAX
Total envíos entregados =
CALCULATE (
    [Total envíos],
    NOT ISBLANK ( 'Envíos'[FechaEntrega] )
)
```

Esta medida cuenta únicamente los envíos que tienen fecha de entrega informada.

14. Configura la medida:
   - **Formato**: Número entero
   - **Decimales**: 0
   - **Carpeta para mostrar**: Indicadores operativos

15. En la tabla **Medidas**, crea la siguiente medida:

```DAX
% envíos entregados =
DIVIDE (
    [Total envíos entregados],
    [Total envíos]
)
```

Esta medida muestra qué porcentaje de envíos tiene fecha de entrega registrada.

16. Configura la medida:
   - **Formato**: Porcentaje
   - **Decimales**: 2
   - **Carpeta para mostrar**: Indicadores operativos

17. En la tabla **Medidas**, crea la siguiente medida:

```DAX
Total no entregados = 
[Total envíos] - [Total envíos entregados]
```

Esta medida cuenta únicamente los envíos que no tienen fecha de entrega informada.

18. Configura la medida:
   - **Formato**: Número entero
   - **Decimales**: 0
   - **Carpeta para mostrar**: Indicadores operativos

19. En la tabla **Medidas**, crea la siguiente medida:

```DAX
% envíos no entregados = 
DIVIDE(
    [Total no entregados],
    [Total envíos]
)
```

Esta medida muestra qué porcentaje de envíos no tiene una fecha de entrega registrada.

20. Configura la medida:
   - **Formato**: Porcentaje
   - **Decimales**: 2
   - **Carpeta para mostrar**: Indicadores operativos

21. En la tabla **Medidas**, crea la siguiente medida:

```DAX
Total envíos tardíos = 
CALCULATE(
    [Total envíos],
    'Envíos'[Entrega tardía] = TRUE()
)
```

Esta medida muestra el total de envíos con fecha de entrega tardía.

22. Configura la medida:
   - **Formato**: Número entero
   - **Decimales**: 0
   - **Carpeta para mostrar**: Indicadores operativos

23. En la tabla **Medidas**, crea la siguiente medida:

```DAX
% envíos tardíos = 
DIVIDE(
    [Total envíos tardíos],
    [Total envíos]
)
```

Esta medida muestra qué porcentaje de envíos tiene fecha de entrega tardía.

24. Configura la medida:
   - **Formato**: Porcentaje
   - **Decimales**: 2
   - **Carpeta para mostrar**: Indicadores operativos

21. En la tabla **Medidas**, crea la siguiente medida:

```DAX
Total envíos tardíos = 
CALCULATE(
    [Total envíos],
    'Envíos'[Entrega tardía] = TRUE()
)
```

Esta medida muestra el total de envíos con fecha de entrega tardía.

22. Configura la medida:
   - **Formato**: Número entero
   - **Decimales**: 0
   - **Carpeta para mostrar**: Indicadores operativos

23. En la tabla **Medidas**, crea la siguiente medida:

```DAX
Promedio intentos = 
AVERAGE('Envíos'[Intentos])
```

Esta medida muestra el promedio de intentos de entrega.

24. Configura la medida:
   - **Formato**: Número decimal
   - **Decimales**: 2
   - **Carpeta para mostrar**: Indicadores operativos

25. En la tabla **Medidas**, crea la siguiente medida:

```DAX
Horas medias hasta entrega =
[Días medios hasta entrega] * 24
```

26. Configura la medida:
   - **Formato**: Número decimal
   - **Decimales**: 2
   - **Carpeta para mostrar**: Indicadores operativos

27. En la tabla **Medidas**, crea la siguiente medida:

```DAX
Horas SLA =
MAX('Servicio'[Horas SLA])
```

28. Configura la medida:
   - **Formato**: Número entero
   - **Decimales**: 0
   - **Carpeta para mostrar**: Indicadores operativos

### Ejercicio 3: Crear columnas calculadas adicionales con DAX 
  
Ahora crearás columnas calculadas en la tabla **Fecha**. Estas columnas se almacenan en el modelo y son útiles como atributos temporales.

1. Selecciona la tabla **Fecha** y crea una nueva columna:

```DAX
Es fin de semana =
WEEKDAY (
    Fecha[Fecha],
    2
) >= 6
```

Esta columna devuelve **TRUE** para sábados y domingos, y **FALSE** para el resto de días.

2. En la tabla **Fecha**, crea esta columna:

```DAX
Tipo de día =
IF (
    Fecha[Es fin de semana],
    "Fin de semana",
    "Laborable"
)
```

Esta columna clasifica cada fecha como **Fin de semana** o **Laborable**. No se están teniendo en cuenta festivos nacionales, autonómicos ni locales.

3. En la tabla **Fecha**, crea esta columna:

```DAX
Temporada operativa =
SWITCH (
    TRUE (),
    Fecha[Número de mes] IN { 11, 12 }, "Alta actividad",
    Fecha[Número de mes] IN { 7, 8 }, "Verano",
    Fecha[Número de mes] IN { 1, 2 }, "Inicio de año",
    "Actividad regular"
)
```

Esta columna permite segmentar el análisis por periodos operativos aproximados.

4. En la tabla **Fecha**, crea esta columna:

```DAX
Día hábil semana =
IF (
    Fecha[Es fin de semana],
    BLANK (),
    Fecha[Número día semana]
)
```

Esta columna devuelve el número del día de la semana únicamente para días de lunes a viernes. Para sábados y domingos devuelve **BLANK()**.

### Ejercicio 5: Validar cálculos en visualizaciones

### Tarea 1: Crear una página de validación

1. Ve a la vista de informe y crea una página llamada **Validación de medidas**.
2. Añade una matriz.
3. En filas, coloca **Servicio[Servicio]**.
4. En valores, agrega estas medidas:
   - **Total envíos**
   - **Ingresos**
   - **Margen estimado**
   - **Margen medio por envío**
   - **% margen estimado**
   - **Días medios hasta entrega**
   - **Días máximos hasta entrega**
   - **% envíos entregados**
5. Añade una segmentación con **Fecha[Tipo de día]**.
6. Añade una segmentación con **Fecha[Temporada operativa]**.
7. Cambia los filtros y comprueba que todas las medidas responden al contexto seleccionado.

**Resultado esperado:** has creado medidas derivadas para analizar margen, duración de entrega y entregas registradas. Además, has añadido columnas calculadas en la tabla **Fecha** para segmentar el análisis por tipo de día y temporada operativa.

---

## Entregables
  
Al finalizar, debes tener un archivo `.pbix` con:
- Tabla **Fecha** creada y marcada como tabla de fechas.
- Calendario Gregoriano y calendario ISO, si tu versión lo permite.
- Relaciones activas e inactivas entre **Fecha** y **Envíos**.
- Tabla calculada **Medidas**.
- Medidas base: **Total envíos** e **Ingresos**.
- Medidas económicas derivadas: **Margen estimado**, **Margen medio por envío** y **% margen estimado**.
- Medidas operativas derivadas: **Días medios hasta entrega**, **Días máximos hasta entrega**, **Envíos con entrega** y **% envíos entregados**.
- Columnas calculadas en la tabla **Fecha**: **Es fin de semana**, **Tipo de día**, **Temporada operativa** y **Día hábil semana**.
- Medidas organizadas en carpetas de visualización.
- Página **Validación de medidas** con visualizaciones para comprobar que las medidas responden al contexto de filtro.

---

## Resumen
  
En este laboratorio has creado los primeros cálculos DAX del modelo ParcelCraft. Has trabajado con tablas calculadas, columnas calculadas y medidas, y has consolidado la tabla **Fecha**, necesaria para análisis temporal y para los cálculos avanzados del siguiente laboratorio.

#### 🎉 Fin del laboratorio
  
¡Enhorabuena! Has completado el Laboratorio 3 de ParcelCraft. 👏

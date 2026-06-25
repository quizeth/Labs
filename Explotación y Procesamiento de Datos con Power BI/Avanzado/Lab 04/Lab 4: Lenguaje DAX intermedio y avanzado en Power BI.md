# Laboratorio 4: Lenguaje DAX intermedio y avanzado en Power BI
  
**ParcelCraft** ya dispone de un modelo semántico con estructura en estrella, una tabla de fechas y un calendario del modelo correctamente configurados. El siguiente paso es ampliar el modelo analítico mediante el uso de **DAX intermedio y avanzado**, creando medidas que combinen **variables**, **iteradores**, **funciones de tiempo**, **cálculos contextuales** y **medidas anidadas**.

En este laboratorio trabajarás principalmente en la **vista de tabla**, la **vista de modelo** y la **vista de informe**, creando medidas reutilizables que permitirán analizar con mayor precisión el rendimiento operativo y económico de ParcelCraft.

---

## Objetivos de aprendizaje
  
**Tiempo estimado total: 45 minutos**  
Al finalizar este laboratorio serás capaz de:
- Crear medidas base reutilizables con DAX.
- Utilizar variables para simplificar expresiones y mejorar la legibilidad.
- Aplicar iteradores para realizar cálculos fila a fila.
- Crear medidas con funciones de tiempo.
- Comprender el impacto del contexto de filtro en las medidas.
- Construir medidas anidadas a partir de otras medidas.
- Validar cálculos avanzados en objetos visuales.

Antes de comenzar, abre el archivo de Power BI que completaste en el laboratorio anterior, donde ya trabajaste con la tabla **Fecha** y con el modelo semántico de ParcelCraft.

> [!IMPORTANT]
> Tienes disponible un archivo inicial[Starter - ParcelCraft.pbix](https://github.com/quizeth/Labs/blob/main/Explotaci%C3%B3n%20y%20Procesamiento%20de%20Datos%20con%20Power%20BI/Avanzado/Lab%2004/Files/04%20Starter%20-%20ParcelCraft.pbix).

---

# Ejercicio

## Tarea 1. Crear medidas base
1. Ve a la **vista de tabla**.
2. Crea una nueva tabla vacía llamada **Medidas**.
```DAX
Medidas = 
```
4. Selecciona la tabla **Medidas**.
5. En la cinta **Modelado**, selecciona **Nueva medida**.
6. Crea las siguientes medidas:

```DAX
Ingresos totales =
SUM('Envíos'[Precio total])
```

```DAX
Precio base total =
SUM('Envíos'[Precio base])
```

```DAX
Recargo combustible total =
SUM('Envíos'[Surplus fuel])
```

```DAX
Envíos totales =
COUNTROWS('Envíos')
```

```DAX
Peso total kg =
SUM('Envíos'[Peso (KG)])
```
> [!NOTE]
> Estas medidas actuarán como base para el resto del laboratorio. En escenarios reales, una buena práctica consiste en definir primero medidas simples y reutilizables antes de construir cálculos más complejos.

## Tarea 2. Crear medidas con filtros y reutilización
1. Asegúrate de seguir trabajando en la tabla **Medidas**.
2. Crea las siguientes medidas:

```DAX
Envíos entregados =
CALCULATE(
    [Envíos totales],
    'Envíos'[Estado] = "Delivered"
)
```

```DAX
Envíos devueltos =
CALCULATE(
    [Envíos totales],
    'Envíos'[Estado] = "Returned"
)
```

```DAX
Envíos con incidencia = 
CALCULATE(
    [Envíos totales],
    'Envíos'[Incidencia] = "True"
)
```

```DAX
Ingresos totales de entregados =
CALCULATE(
    [Ingresos totales],
    'Envíos'[Estado] = "Delivered"
)
```

> [!NOTE]
> Observa que estas medidas ya reutilizan medidas previamente definidas, lo que simplifica el mantenimiento del modelo y reduce errores cuando el cálculo base cambia.

### Tarea 3. Utilizar variables DAX
1. Crea una nueva medida en la tabla **Medidas** con la siguiente expresión:

```DAX
Ingreso medio por envío =
VAR TotalIngresos = [Ingresos totales]
VAR TotalEnvios = [Envíos totales]
RETURN
    DIVIDE(TotalIngresos, TotalEnvios)
```

2. Crea otra medida:

```DAX
Recargo sobre base % =
VAR BaseTotal = [Precio base total]
VAR RecargoTotal = [Recargo combustible total]
RETURN
    DIVIDE(RecargoTotal, BaseTotal)
```

> [!NOTE]
> Las variables permiten almacenar resultados intermedios dentro de una expresión DAX. Esto mejora la legibilidad y evita repetir la misma lógica varias veces en una fórmula.

### Tarea 4. Crear medidas con iteradores

En esta tarea vas a utilizar funciones iteradoras para evaluar filas de la tabla **Envíos** y después agregar los resultados.

1. Crea las siguientes medidas en la tabla **Medidas**:

```DAX
Intentos totales =
SUM('Envíos'[Intentos])
```

```DAX
Intentos medios por envío =
AVERAGEX(
    Envíos,
    Envíos[Intentos]
)
```

```DAX
Tiempo medio de entrega (h) =
AVERAGEX(
    FILTER(
        'Envíos',
        'Envíos'[Estado] = "Delivered"
            && NOT ISBLANK('Envíos'[FechaEntrega])
    ),
    VAR Inicio = 'Envíos'[FechaCreación]
    VAR Fin = 'Envíos'[FechaEntrega]
    RETURN
        DATEDIFF(Inicio, Fin, HOUR)
)
```

```DAX
Ingreso medio ponderado por kg =
DIVIDE(
    [Ingresos totales],
    [Peso total kg]
)
```

> [!NOTA]
> En esta tarea has usado `AVERAGEX`, que recorre una tabla y evalúa una expresión para cada fila. Este patrón es muy útil cuando el cálculo depende de columnas que deben evaluarse una a una, como diferencias entre fechas o lógica condicional a nivel de envío.

### Tarea 5. Crear una medida de cumplimiento SLA con variables e iteradores

En ParcelCraft, el cumplimiento del SLA depende del tiempo real de entrega frente al número de horas comprometidas para cada envío.

1. Crea las siguientes medidas en la tabla **Medidas**:

```DAX
Envíos en SLA =
SUMX(
    FILTER(
        Envíos,
        Envíos[Estado entrega] = "Delivered"
            && NOT ISBLANK(Envíos[Entregado UTC])
    ),
    VAR HorasTranscurridas =
        DATEDIFF(
            Envíos[Pedido creado UTC],
            Envíos[Entregado UTC],
            HOUR
        )
    RETURN
        IF(HorasTranscurridas <= Envíos[SLA horas], 1, 0)
)
```

```DAX
Cumplimiento SLA % =
DIVIDE(
    [Envíos en SLA],
    [Envíos entregados]
)
```

> [!NOTE]
> Esta medida combina varios conceptos importantes:
> - `FILTER` para restringir el conjunto de filas.
> - `SUMX` para iterar sobre cada envío entregado.
> - `VAR` para almacenar el tiempo calculado.
> - `IF` para devolver un valor binario según se cumpla o no el SLA.


### Tarea 6. Crear medidas de tiempo

En esta tarea trabajarás con la tabla **Fecha** que creaste en el laboratorio anterior. Asegúrate de que está correctamente relacionada con **Envíos**.

- Crea las siguientes medidas en la tabla **Medidas**:

```DAX
Ingresos YTD =
TOTALYTD(
    [Ingresos totales],
    'Fecha'[Date]
)
```

```DAX
Ingresos año anterior =
CALCULATE(
    [Ingresos totales],
    DATEADD('Fecha'[Date], -1, YEAR)
)
```

```DAX
Variación ingresos =
[Ingresos totales] - [Ingresos año anterior]
```

```DAX
Variación ingresos % =
DIVIDE(
    [Variación ingresos],
    [Ingresos año anterior]
)
```

> [!NOTE]
> Estas medidas son un ejemplo claro de **medidas anidadas**, ya que se construyen a partir de otras medidas existentes. Este enfoque evita duplicidad de lógica y facilita enormemente la evolución del modelo.

### Tarea 7. Crear una medida contextual

En esta tarea vas a crear una medida cuyo valor cambie en función del contexto del objeto visual, pero que además compare ese contexto con el total general.

1. Crea la siguiente medida en la tabla **Medidas**:

```DAX
Contribución ingresos % =
DIVIDE(
    [Ingresos totales],
    CALCULATE(
        [Ingresos totales],
        ALL(Oficina)
    )
)
```

> [!NOTE]
> Esta medida mostrará qué porcentaje de los ingresos representa cada oficina respecto al total, incluso cuando el valor cambie según el filtro o desglose aplicado en el visual.

2. Crea una medida adicional:

```DAX
Ranking oficina por ingresos =
RANKX(
    ALL(Oficina[Oficina]),
    [Ingresos totales],
    ,
    DESC
)
```

> [!NOTE]
> Esta medida utiliza contexto de filtro, funciones de tabla y una evaluación relativa frente al conjunto completo de oficinas.

### Tarea 8. Validar las medidas en un visual
- Ve a la **vista de informe**.
- Inserta un objeto visual de **matriz**.
- Añade en las filas:
  - **Fecha[Calendario]**
- Añade en las columnas:
  - **Oficina[Oficina]**
- Añade en los valores las siguientes medidas:
  - **Ingresos totales**
  - **Ingreso medio por envío**
  - **Tiempo medio de entrega (h)**
  - **Cumplimiento SLA %**
  - **Ingresos YTD**
  - **Variación ingresos %**
  - **Contribución ingresos %**

- Comprueba cómo cambian los resultados al navegar entre niveles de la jerarquía de fechas.
- Inserta un **segmentador** con **Servicio[Servicio]**.
- Inserta otro **segmentador** con **Cliente[Cliente]**.
- Observa cómo el contexto de filtro afecta a todas las medidas.

### Tarea 9. Dar formato y organizar medidas

En esta tarea vas a mejorar la usabilidad del modelo configurando el formato adecuado de las medidas creadas.

##### Tabla Envíos

1. Configura las siguientes propiedades:
   | Medida                          | Formato         | Símbolo | Decimales |
|---------------------------------|----------------|--------|-----------|
| Ingresos totales                | Moneda         | €      | 2         |
| Precio base total               | Moneda         | €      | 2         |
| Recargo combustible total       | Moneda         | €      | 2         |
| Ingreso medio por envío         | Moneda         | €      | 2         |
| Ingreso medio ponderado por kg  | Moneda         | €      | 2         |
| Tiempo medio de entrega (h)     | Número decimal | —      | 1         |
| Recargo sobre base %            | Porcentaje     | —      | 2         |
| Cumplimiento SLA %              | Porcentaje     | —      | 2         |
| Variación ingresos %            | Porcentaje     | —      | 2         |
| Contribución ingresos %         | Porcentaje     | —      | 2         |
``

2. Ordena todas las medidas en carpetas.

---

## Resumen
  
En este laboratorio hemos ampliado el modelo semántico de ParcelCraft mediante la creación de **medidas DAX intermedias y avanzadas**. Hemos comenzado definiendo medidas base que sirven como fundamento para cálculos posteriores y, a continuación, hemos utilizado **variables** para simplificar expresiones y mejorar su legibilidad.

Después, hemos trabajado con **iteradores** para evaluar cálculos fila a fila, lo que nos ha permitido medir correctamente conceptos como los intentos medios por envío, el tiempo medio de entrega y el cumplimiento del SLA. También hemos creado medidas de **tiempo** apoyadas en la tabla **Fecha**, como ingresos acumulados, comparación con el año anterior y variación porcentual.

Finalmente, hemos introducido **cálculos contextuales** y **medidas anidadas**, demostrando cómo DAX permite construir lógica analítica reutilizable, robusta y escalable. Con esta base, ParcelCraft ya está preparado para desarrollar análisis más complejos y cuadros de mando con comportamiento contextual avanzado.

#### Recursos útiles
- Documentación de medidas DAX en Power BI: https://learn.microsoft.com/power-bi/transform-model/desktop-measures
- Funciones de tiempo en DAX: https://learn.microsoft.com/dax/time-intelligence-functions-dax
- Funciones iteradoras y de filtro en DAX: https://learn.microsoft.com/dax/filter-functions-dax
- Buenas prácticas de modelado semántico en Power BI: https://learn.microsoft.com/power-bi/guidance/

---
#### 🎉 Fin del laboratorio
  
¡Enhorabuena! Has completado este laboratorio y ya dispones de un conjunto sólido de medidas DAX intermedias y avanzadas para el modelo de ParcelCraft. Este paso es fundamental para construir análisis más expresivos, precisos y reutilizables dentro de Power BI.

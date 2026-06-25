# Laboratorio 3: Crear y gestionar calendarios en Power BI
  
**ParcelCraft** ya dispone de un modelo semántico con estructura en estrella y relaciones configuradas. El siguiente paso es incorporar una gestión moderna del tiempo utilizando los **calendarios de Power BI**, combinando tablas DAX con las nuevas capacidades de administración de calendarios.

En este laboratorio trabajarás principalmente en la **vista de datos** y en la **vista de modelo**, creando una tabla calculada de fechas, columnas derivadas, una relación con la tabla de hechos y el calendario del modelo.

---

## Objetivos de aprendizaje
  
**Tiempo estimado total: 20 minutos**  

Al finalizar este laboratorio serás capaz de:

- Comprender la diferencia entre Auto Date/Time, tabla de fechas y calendarios.
- Deshabilitar la inteligencia de tiempo automática.
- Crear una tabla de fechas con DAX.
- Registrar un calendario en el modelo.
- Relacionar correctamente el calendario con la tabla de hechos.
- Preparar el modelo para inteligencia temporal moderna.

Antes de comenzar, abre el archivo de Power BI del laboratorio anterior.

> [!IMPORTANT]
> Tienes disponible un archivo inicial [Starter - ParcelCraft.pbix](https://github.com/quizeth/Labs/blob/main/Explotaci%C3%B3n%20y%20Procesamiento%20de%20Datos%20con%20Power%20BI/Avanzado/Lab%2003/Files/03%20Starter%20-%20ParcelCraft.pbix).

----

# Ejercicio

## Tarea 1. Comprender y deshabilitar la inteligencia de tiempo automática
1. En Power BI Desktop, ve a **Archivo** > **Opciones y configuración** > **Opciones**.
2. En la sección **Archivo actual**, selecciona **Carga de datos**.
3. Localiza la opción **Fecha/hora automática**.

> [!NOTE]
> Power BI puede generar automáticamente tablas ocultas de fecha para determinadas columnas de tipo fecha. Esta funcionalidad puede resultar útil en modelos rápidos o exploratorios, pero no es recomendable en modelos analíticos profesionales porque:
> - Crea una tabla de fechas oculta por cada columna de fecha.
> - Duplica la lógica temporal dentro del modelo.
> - Aumenta el tamaño del modelo.
> - Limita el control sobre el calendario y sobre el comportamiento de la inteligencia temporal.

4. Desmarca la opción **Fecha/hora automática para este archivo**.
5. Desmarca también la opción **Fecha/hora automática para nuevos archivos**, si está habilitada.
6. Haz clic en **Aceptar**.

A partir de este momento, serás tú quien controle la lógica temporal del modelo mediante una única tabla de fechas centralizada.

## Tarea 2. Crear la tabla de fechas con DAX
1. Ve a la **vista de tabla**.
2. En la cinta **Modelado**, selecciona **Nueva tabla**.
3. En la barra de fórmulas, escribe la siguiente expresión DAX:

```DAX
Fecha = CALENDARAUTO()
```

4. Pulsa **Enter** para crear la tabla.
5. Observa en el panel de datos que la nueva tabla muestra el icono de tabla calculada.

> [!NOTE]
> La función **CALENDARAUTO** devuelve una tabla de una sola columna que contiene valores de fecha. El comportamiento automático examina todas las columnas de fecha del modelo para determinar los valores mínimo y máximo almacenados. Después, crea una fila para cada fecha dentro de ese rango y amplía el intervalo para asegurar que se incluyan años completos.  
> 
> Esta función puede recibir un argumento opcional que indica cuál es el último mes del año. Si se omite, se toma el valor **12**, lo que significa que diciembre es el último mes del año. Si, por ejemplo, se indicara **6**, junio pasaría a considerarse el último mes del año.

## Tarea 3. Crear columnas de calendario
1. Asegúrate de seguir en la **vista de tabla**.
2. Selecciona la tabla **Fecha**.
3. Crea una nueva columna con la siguiente expresión:

```DAX
Año = YEAR('Fecha'[Date])
```

4. Crea otra columna con la siguiente expresión:

```DAX
MesN = MONTH('Fecha'[Date])
```

5. Crea otra columna con la siguiente expresión:

```DAX
Mes = FORMAT('Fecha'[Date], "mmm YYY")
```

6. Crea otra columna con la siguiente expresión:

```DAX
Trimestre = "Q" & FORMAT('Fecha'[Date], "Q")
```

Estas columnas permitirán agrupar, ordenar y analizar los envíos por distintos niveles temporales.

## Tarea 4. Crear el calendario del modelo
1. Con la tabla **Fecha** seleccionada, accede a las **Opciones de calendario**.
2. En la ventana **Opciones de calendario para Fecha**, localiza la sección **Administrar calendarios**.
3. Haz clic en **+ Nuevo calendario**.
4. Nombra el nuevo calendario **Estándar**.
5. Configura el calendario utilizando la tabla **Fecha** y la columna **Date** como referencia principal.
6. Guarda la configuración.

Este paso registra la tabla de fechas como calendario del modelo y la prepara para trabajar con la administración moderna de calendarios en Power BI.

<img width="775" height="702" alt="image" src="https://github.com/user-attachments/assets/2fea152b-cd3b-4b97-a354-df42d0d1cf3e" />

## Tarea 5. Relacionar la tabla de fechas con Envíos
1. Ve a la **vista de modelo**.
2. Localiza la tabla **Fecha** y la tabla **Envíos**.
3. Arrastra el campo **Fecha[Date]** hacia el campo **Envíos[FechaCreación]**.
4. Verifica:
  - Cardinalidad: Uno a varios (1:*)
  - Dirección del filtro cruzado: Único
  - Asegúrate de que la relación queda activa.

Con esta relación, la tabla **Fecha** actuará como dimensión temporal del modelo en estrella.

5. En la cinta, selecciona **Administrar relaciones** y añade dos relaciones más.
   - **Fecha[Date]** > **Envíos[FechaActualización]**
   - **Fecha[Date]** > **Envíos[FechaEntrega]**
   - **Fecha[Date]** > **Envíos[FechaEnvío]**
   - **Fecha[Date]** > **Envíos[ÚltimaExtracción]**


   Todas estas relaciones deben tener las mismas propiedades:
   - Cardinalidad: Uno a varios (1:*)
   - Dirección del filtro cruzado: Único
   - Activar esta relación: **no marcado**

6. Oculta los campos de fechas la tabla **Envíos:**
   - Envíos[FechaActualización]
   - Envíos[FechaCreación]
   - Envíos[FechaEntrega]
   - Envíos[FechaEnvío]
   - Envíos[ÚltimaExtracción]

## Tarea 6. Marcar la tabla como tabla de fechas
1. Selecciona la tabla **Fecha**.
2, En la cinta **Herramientas de tabla**, selecciona **Opciones del calendario** > **Marcar como tabla de fechas**.
3. Elige la columna **Date** como columna de fecha.
4. Confirma la selección.

> [!NOTE]
> Aunque Power BI ya permite trabajar con calendarios del modelo, marcar explícitamente la tabla como tabla de fechas sigue siendo una buena práctica para mantener compatibilidad con algunos escenarios y expresiones DAX.

## Tarea 7. Configura las propiedades de la tabla de fecha
1. En la **vista de modelo**, haz clic derecho sobre la columna **Año**.
2. Selecciona **Crear jerarquía**.
3. Renombra la jerarquía como **Calendario**.
4. Agrega y ordena las columnas:
  - Año
  - Trimestre
  - Mes

5. Configura las siguientes propiedades:
  - **Año**
  - Tipo de datos: Número entero
  - **MesN**
  - Tipo de datos: Número entero
  - **Mes**
  - Ordenar por columna: **MesN**

 6. Oculta las siguientes columnas técnicas que no van a utilizarse en los informes:
  - Fecha[MesN]


## Resumen
  
En este laboratorio hemos adaptado el modelo semántico de ParcelCraft a la gestión moderna de fechas en Power BI. Primero, hemos revisado cómo funciona la inteligencia de tiempo automática y por qué conviene deshabilitarla en modelos analíticos profesionales. Después, hemos creado una tabla calculada con **DAX** utilizando la función **CALENDARAUTO** y la hemos enriquecido con columnas auxiliares para facilitar el análisis temporal.

También hemos registrado la tabla como calendario del modelo, la hemos relacionado con la tabla de hechos **Envíos**, la hemos marcado como tabla de fechas y hemos creado una jerarquía temporal reutilizable. Con esta base, el modelo queda preparado para desarrollar medidas de inteligencia temporal en futuros laboratorios.

#### Recursos útiles
- Documentación de tablas de fechas en Power BI: https://learn.microsoft.com/power-bi/transform-model/desktop-date-tables
- Documentación de fecha/hora automática: https://learn.microsoft.com/power-bi/transform-model/desktop-auto-date-time
- Diseño de tablas de fechas en Power BI: https://learn.microsoft.com/power-bi/guidance/model-date-tables

#### 🎉 Fin del laboratorio
  
¡Enhorabuena! Has completado este laboratorio y ya dispones de una tabla de fechas moderna y preparada para inteligencia temporal dentro del modelo de ParcelCraft. Este paso es clave para construir análisis temporales fiables, escalables y alineados con las prácticas actuales de Power BI.

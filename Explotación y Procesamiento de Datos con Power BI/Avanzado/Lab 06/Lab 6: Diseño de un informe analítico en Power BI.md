# Laboratorio 6: Diseño de un informe analítico en Power BI
  
**ParcelCraft** ya cuenta con un modelo en estrella y medidas DAX empresariales. En este laboratorio trabajarás como diseñador de informes analíticos: crearás un informe de tres páginas, aplicarás principios de diseño visual, seleccionarás objetos visuales adecuados, configurarás segmentaciones, filtros e interacciones, y prepararás una página de KPIs enfocada en una oficina concreta.  
El objetivo es pasar de un modelo semántico validado a una experiencia de análisis clara, navegable y preparada para usuarios de negocio.

## Objetivos de aprendizaje
  
**Tiempo estimado total: 60 minutos**  
Al finalizar este laboratorio serás capaz de:
- Crear un informe analítico de tres páginas para ParcelCraft.
- Aplicar principios de diseño efectivo y seleccionar visuales adecuados.
- Configurar segmentaciones, filtros, interacciones y exploración en profundidad.
- Preparar una vista de KPIs por oficina como base para escenarios posteriores con RLS.

---

## Antes de empezar

### Requisitos
  
Para completar este laboratorio necesitas:
- Power BI Desktop instalado.
- Haber completado el **Laboratorio 3: Fundamentos de DAX y tabla de fechas en Power BI**, o partir del archivo inicial.
- Descargarse los **archivos del laboratorio**, el logo y tema de ParcelCraft, disponibles en la carpeta **Files**.
- Conocimientos básicos de visualizaciones, segmentadores, filtros, interacciones y jerarquías en Power BI.  

> Tienes un archivo inicial y solución disponibles.
> 
> [Archivo inicial (Starter)](https://github.com/quizeth/Labs/blob/main/Explotaci%C3%B3n%20y%20Procesamiento%20de%20Datos%20con%20Power%20BI/Intermedio/Files/03%20Starter%20ParcelCraft.pbix)
> 
> [Archivo final (Solución)](https://github.com/quizeth/Labs/blob/main/Explotaci%C3%B3n%20y%20Procesamiento%20de%20Datos%20con%20Power%20BI/Intermedio/Files/03%20Soluci%C3%B3n%20ParcelCraft.pbix)


---

## Ejercicio 1: Crear la página Visión global
  
En este ejercicio crearás la primera página del informe. Esta página debe proporcionar una visión global del análisis y permitir que un usuario entienda rápidamente el comportamiento general de ParcelCraft.  

La página debe funcionar como una vista ejecutiva, por lo que los KPIs principales deben estar en la parte superior y los gráficos de análisis en la zona central. Además, al ser la página de aterrizaje, debe ser ligera para agilizar la carga del informe al abrirlo.

### Tarea 1: Abrir y definir la estructura del informe
1. Abre **Power BI Desktop**.
2. Abre el archivo .pbix generado al final del Laboratorio 3 o el archivo inicial del laboratorio.
3. En la **vista Informe**, elimina la página **Validación de medidas**.
4. Limpia todas las visualizaciones de la página restante.
3. Renombra la página existente como **Vision global**.
4. Haz clic derecho en el nombre de la página y selecciona **Establecer como página de aterrizaje**.
5. En la pestaña **Ver**, ve a **Temas > Explorar temas**.
6. Carga el tema **ParcelCraft_Theme**.
> Tienes el [archivo del tema](https://github.com/quizeth/Labs/blob/main/Explotaci%C3%B3n%20y%20Procesamiento%20de%20Datos%20con%20Power%20BI/Intermedio/Files/ParcelCraft_Theme.json) en la carpeta de Files de este laboratorio.


### Tarea 2: Configurar encabezado y diseño base
1. Ve a la página **Visión global**.
3. Añade una imagen desde **Insertar > Elementos > Imagen** a la esquina superior izquierda.
4. En el panel **Formato de imagen**, ve a la sección **Imagen**.
5. Vamos a añadir el logo de la empresa. Escoge el origen de la imagen:
   a. Si te has descargado el archivo, selecciona **Cargar imagen**. Después, en **Imagen > Examinar** selecciona y ábrela. 
    > Tienes el [archivo del logo](https://github.com/quizeth/Labs/blob/main/Explotaci%C3%B3n%20y%20Procesamiento%20de%20Datos%20con%20Power%20BI/Avanzado/Lab%2006/Files/ParcelCraft.png) en la carpeta de Files de este laboratorio.
   b. Si no te quieres descargar el archivo, selecciona **Escribir dirección URL** e introduce la dirección url `https://github.com/quizeth/Labs/blob/main/Explotaci%C3%B3n%20y%20Procesamiento%20de%20Datos%20con%20Power%20BI/Intermedio/Files/ParcelCraft%20logo.png?raw=true`.
4. Configura el logo:
  - **General > Propiedades > Tamaño:** 95 (alto) x 95 (ancho)
  - **General > Efectos > Fondo**: desactivado
  - **General > Efectos > Borde visual**: desactivado
> Inserta un cuadro de texto en la parte superior, a la derecha del logo.
5. Escribe el título: `Visión global de operaciones`.
6. Configura el título:
   - **Fuente**: Segoe UI Semibold
   - **Tamaño de fuente**: 32
7. Debajo del título, añade un subtítulo: `Seguimiento ejecutivo de envíos, ingresos, margen y cumplimiento`.
5. Configura el subtítulo:
   - **Fuente**: Segoe UI Semibold
   - **Tamaño de fuente**: 16
   - **Color de fuente**: Blanco, 50% más oscuro
   - Cursiva

<img width="1291" height="733" alt="image" src="https://github.com/user-attachments/assets/1b979681-5057-4307-bc77-e8c71a15efd4" />

### Tarea 3: Crear tarjetas de KPIs principales
> Buena práctica: la ubicación debe guiar la lectura. Primero se leen KPIs, después tendencias y comparativas, y finalmente filtros o detalles.

1. Inserta una visualización de tarjeta.
> Si tienes la nueva visualización de tarjeta (con el icono de un rayo), sigue los pasos tal y como están escritos.
> Si tienes la antigua (sin el icono), crea una tarjeta individual para cada métrica. Después, alinéalas en la misma fila y ajusta el tamaño para que tengan el mismo ancho y alto.

2. Asigna las siguientes medidas al campo **Valor**:
    - `Total envíos`
    - `Ingresos`
    - `% margen estimado`
    - `% envíos entregados`.

3. Ajusta la tarjeta para que cubra el ancho de todo el lienzo.
4. Sitúa la tarjeta debajo del título y subtítulo.
5. Ajusta los formatos de las tarjetas. Para ello, con la tarjeta seleccionada, ve a la segunda pestaña del **panel de visualizaciones** (**Dar formato a su objeto visual**, icono del **gráfico y el pincel**).
> A partir de ahora, cada vez que el laboratorio se refiera a una opción de formato, dirígete a esta pestaña.

6. Ve a **Objeto Visual > Llamada > Aplicar configuración a**.
7. En el menú desplegable **Tarjetas**, selecciona **Ingresos**.
8. Amplía la pestaña **Valor** y configura:
   - **Mostrar unidades**: Millones
   - **Posiciones decimales**: 2
9. Revisa que los formatos de moneda y porcentaje se muestran correctamente. Si no es el caso, repite los pasos anteriores para ajustar la configuración de cada medida.
   
**Resultado esperado:** la zona superior permite entender de inmediato el estado general del negocio.

<img width="1303" height="256" alt="image" src="https://github.com/user-attachments/assets/a0e07ded-e751-4ca5-8d26-cf278cd39292" />


### Tarea 4: Crear visuales globales de análisis
1. Inserta un gráfico de columnas agrupadas y de líneas.
2. Configura el gráfico:
  - **Eje X:** `'Fecha'[Mes año]`
  - **Eje Y:** `Total envíos`
  - **Eye Y de línea:** `% margen estimado`
3. En el panel de formato, ve a Ve a **General > Título**.
4. Cambia el título a `Evolución de envíos`.
5. Ajusta el tamaño y posición del gráfico para que ocupe la mitad izquierda del espacio restante.

<img width="1295" height="737" alt="image" src="https://github.com/user-attachments/assets/dd6f5543-f0bc-455f-958e-27ee7381464e" />

6. Inserta un gráfico de barras agrupadas.
7. Configura el gráfico:
  - **Eje X:** `'Oficina'[Oficinas]`
  - **Eje Y:** `Ingresos`
8. Ordena el visual de mayor a menor (orden descendente) por `Ingresos`.
9. Ajusta el tamaño y posición del gráfico para que ocupe la mitad superior del espacio restante.

<img width="1295" height="734" alt="image" src="https://github.com/user-attachments/assets/9c58c729-67d0-4987-88c3-222f7130bd61" />

10. Inserta un gráfico de anillos.
12. Configura el gráfico:
  - **Leyenda:** `'Cliente'[Segmento]`
  - **Valores:** `Total envíos`
13. Cambia el título a `Distribución por segmento de cliente`.
14. En el panel de formato,  ve a **Objeto visual > Segmentos > Borde** y configúralo:
  - **Borde:** habilitado
  - **Color:** Blanco
  - **Ancho (px)**: 2
15. Ajusta el tamaño y posición del gráfico para que ocupe la mitad izquierda del espacio restante.

<img width="1297" height="735" alt="image" src="https://github.com/user-attachments/assets/d7d6d044-72b3-40ef-98bb-3996dcca2a72" />

16. Copia el gráfico de anillas y pégalo a su derecha.
17. Configura el gráfico:
  - **Leyenda:** `'Servicio'[Segmento de servicio]`
  - **Valores:** `Total envíos`
> Si no encuentras el campo `Segmento de servicio`, sigue los siguientes pasos:
> 1. Abre Power Query (**Transformar datos** en la pestaña **Inicio** de la cinta superior).
> 2. Selecciona la consulta **Servicio**.
> 3. Selecciona el paso aplicado **Columnas de servicio seleccionadas**.
> 4. Asegúrate de que la columna **Segmento de servicio** esté marcada.
> 5. Aplica y cierra.

18. Cambia el título a `Distribución por segmento de servicio`. 

**Resultado esperado:** la página combina tendencia temporal, comparativa por oficina, y distribución por segmento de cliente y servicio.

<img width="1296" height="733" alt="image" src="https://github.com/user-attachments/assets/81fa5957-f721-4c14-9ce1-624ecb2b27ba" />

### Tarea 5: Añadir segmentaciones de visión global
1. Inserta una segmentación de datos con `'Fecha'[Año]`.
2. Inserta una segunda segmentación con `'Servicio'[Servicio]`.
4. Coloca las segmentaciones en la esquina superior derecha.
5. Mantén **Ctrl** para seleccionar ambas segmentaciones.
6. Configura ambas segmentaciones:
   -  **Configuración de la segmentación > Opciones > Estilo**: Menú desplegable.
   -  **Configuración de la segmentación > Opciones > Mostrar opción "Seleccionar todo"**: activado.
   -  **Valores > Fondo > Color**: Blanco
   -  Pestaña **General > Efectos > Fondo:** Desactivado
9. Selecciona el valor `2023` en **Año**.
10. Con la segmentación de **Año** seleccionada, abre el panel de **Filtros**.
11. Bajo la sección **Filtros de este objeto visual**, expande la tarjeta **Año**-
12. Configura **Tipo de filtro** > **Filtrado básico** como se muestra:
   <img width="203" height="456" alt="image" src="https://github.com/user-attachments/assets/50870a14-70ed-467f-801e-6afe032bd72f" />

**Resultado esperado:** la página permite filtrar la visión global por tiempo y servicio.

<img width="1301" height="738" alt="image" src="https://github.com/user-attachments/assets/a541c565-325c-47af-833a-11778621da77" />


## Ejercicio 2: Crear la página Detalle operativo
  
En este ejercicio crearás la segunda página del informe. Esta página debe proporcionar una visión más detallada de un ámbito concreto: el rendimiento operativo por oficina y servicio.

Para mantener la página clara y útil, trabajarás con una única matriz como objeto visual principal y con pocas segmentaciones de apoyo. El objetivo es que el usuario pueda comparar oficinas y servicios sin sobrecargar el informe con demasiados gráficos.


### Tarea 1: Configurar la página de detalle
1. Duplica la página **Visión global**.
2. Renombra la página como **Detalle operativo**.
3. Elimina los objetos visuales, dejando únicamente la sección del encabezado (logo, título, subtítulo y segmentaciones).
4. Sustituye el título por `Detalle operativo`.
5. Sustituye el subtítulo por `Comparativa de volumen e indicadores económicos`

<img width="1300" height="742" alt="image" src="https://github.com/user-attachments/assets/026b0034-73d3-4459-b221-80d30f60b6c7" />


#### Tarea 2: Crear matriz operativa
1. Inserta una visualización de matriz para que ocupe todo el lienzo.
2. Configura la matriz:
  - **Filas:** `'Oficina'[Oficinas]`
  - **Columnas:** `'Servicio'[Servicio]`
  - **Valores:** `Total envíos`
  - **Valores:** `Ingresos`
  - **Valores:** `% margen estimado`

3. Expande un nivel en la jerarquía para ver regiones y ciudades.
4. Configura los totales:
   - **Objeto visual** > **Subtotales de fila** > **Valores** > **Color de fondo**: `#ffeb99, color del tema 2, 60% más claro` (el amarillo más claro).
   - **Objeto visual** > **Total general de columna** > **Valores** > **Color de fondo**: `#ffeb99, color del tema 2, 60% más claro` (el amarillo más claro).
   - **Objeto visual** > **Total general de fila** > **Valores** > **Color de fondo**: `#ffeb99, color del tema 2, 60% más claro` (el amarillo más claro).

**Resultado esperado:** la matriz permite comparar oficinas y servicios a través de medidas clave del modelo semántico.

<img width="1297" height="743" alt="image" src="https://github.com/user-attachments/assets/6c2f89c4-a6fe-441f-8164-512251220d75" />


## Ejercicio 3: Crear la página KPIs Oficina
  
En este ejercicio crearás la tercera página del informe. Esta página debe mostrar KPIs de una oficina concreta y recrear el resultado que se vería filtrado mediante seguridad a nivel de fila (RLS).  En su lugar, aplicarás un filtro de página sobre `'Oficina'[Oficina]` para simular que el usuario solo puede ver la información de una oficina.

El objetivo es que la página sea sencilla, clara y orientada al seguimiento del cumplimiento SLA de una oficina concreta.


### Tarea 1: Crear la página de oficina

1. Duplica la página **Detalle operativo**.
2. Renombra la nueva página como **KPIs Oficina**.
3. Elimina la matriz de la página duplicada.
4. Mantén únicamente el encabezado con el logo, el título y las segmentaciones.
5. Cambia el título por: `KPIs de`.
6. En el menú contextual del título, selecciona **+ Valor**.
7. En el campo **¿Cómo se calcularía este valor?**, escribe **Oficina** y dale a enter.
8. Una vez te muestre algún resultado, en el campo **Asignación de un nombre al valor**, escribe # Oficina.
9. Guarda los cambios.
<img width="759" height="495" alt="image" src="https://github.com/user-attachments/assets/c247b935-6b90-4d5a-95f4-078ab5f476ba" />

10. Configura todo el texto del cuadro de texto:
   - **Fuente**: Segoe UI Semibold
   - **Tamaño de fuente**: 32
11. Ajusta el tamaño del cuadro de texto para que todo sea visible.

**Resultado esperado:** Esta página debe parecer parte del mismo informe. Mantén la misma estructura visual, colores, tipografías y espaciado usados en **Visión global** y **Detalle operativo**.
<img width="1296" height="732" alt="image" src="https://github.com/user-attachments/assets/4c9dca06-9781-4d21-9530-0a586011dc41" />


### Tarea 2: Aplicar filtro de página por oficina

1. Abre el panel **Filtros**.
2. Arrastra `'Oficina'[Oficina]` al área **Filtros de esta página**.
3. En el filtro de `'Oficina'[Oficina]`, marca la opción **Seleccionar todo**.
> El título debería cambiar de forma dinámica. Esta es una buena opción para escoger solo una oficina. Si queremos ver los datos agrupados de múltiples oficinas, necesitamos crear una medida de DAX para mostrar ese valor de forma dinámica.
5. En la tarjeta del filtro, selecciona una oficina concreta.
> Utiliza una oficina con datos suficientes para que los objetos visuales muestren información.

> **Importante**: este filtro no es RLS. Solo simula el resultado visual de una página limitada a una oficina. 

<img width="1338" height="596" alt="image" src="https://github.com/user-attachments/assets/875613b3-ca68-4cdb-8fbf-a34a8676d721" />


### Tarea 3: Actualizar cálculo de envíos tardíos
La medida `Total no entregados` que hemos creado en el laboratorio anterior incluye tanto los envíos con incidencia o devueltos como aquellos en tránsito. Vas a actualizar la medida base para que solo cuente los envíos con incidencia.

1. En la tabla medidas, selecciona la medida **Indicadores operativos** > **Total no entregados**.
2. Reemplaza la expresión por la siguiente:
```
Total envíos no entregados =
CALCULATE(
    [Total envíos],
    'Envíos'[Estado] IN {"Exception", "Returned"}
)
```
> Las medidas que utilicen el `[Total envíos no entregados]`, como el `[% envíos no entregados]`, se actualizarán automáticamente.

### Tarea 4: Crear KPIs de cumplimiento SLA

1. Inserta una visualización de tarjeta.
2. Asigna las siguientes medidas:
   - `% envíos entregados`
   - `% envíos tardíos`
   - `% envíos no entregados`
   - `Días medios hasta entrega`
   - `Promedio intentos`
3. Coloca la tarjeta debajo del encabezado.
4. Ajusta la tarjeta para que cubra el ancho principal del lienzo.
5. Configura el formato de la tarjeta siguiendo el mismo estilo usado en la página **Visión global**.
6. Revisa que `% envíos entregados` y `% envíos tardíos` se muestran con formato de porcentaje.
7. Revisa que `Días medios hasta entrega`, `Promedio intentos` e `Índice operativo ponderado` se muestran con un formato numérico legible.

**Resultado esperado:** la parte superior de la página muestra indicadores específicos de cumplimiento SLA para la oficina seleccionada.

<img width="1038" height="595" alt="image" src="https://github.com/user-attachments/assets/b5ccfadd-e5ff-4771-ad4f-c0ba91627d46" />


### Tarea 5: Crear visual de seguimiento de entregas

1. Inserta un gráfico de columnas 100% apiladas.
2. Configura el gráfico:
   - **Eje X:** `'Fecha'[Mes año]`
   - **Eje Y:** `Total no entregados`, `Total entregados`. 
3. En el panel de formato, ve a **General > Título**.
4. Cambia el título a `Seguimiento mensual de entregas`.
5. Coloca el visual debajo de las tarjetas de KPIs, ocupando la zona izquierda de la página.

**Resultado esperado:** el usuario puede comparar mensualmente el volumen de entregas y no entregas de la oficina seleccionada.

<img width="1037" height="598" alt="image" src="https://github.com/user-attachments/assets/35c8324b-7507-4070-b01f-f63f716a4d39" />


### Tarea 6: Comparar SLA comprometido y entrega real por servicio

1. Inserta un gráfico de barras agrupadas.
2. Configura el gráfico:
   - **Eje Y:** `'Servicio'[Servicio]`
   - **Eje X:** `Horas medias hasta entrega`
   - **Eje X:** `'Horas SLA`

5. Añade estas medidas a **Información sobre herramientas**:
   - `Total envíos`
   - `% envíos entregados`
   - `% envíos no entregados`
   - `% envíos tardíos`
   - `Promedio intentos`

6. En el panel de formato, ve a **General > Título**.
7. Cambia el título a `SLA comprometido vs horas reales`.
8. En el panel de formato, ve a **Objeto visual > Barras**.
9. Selecciona **Aplica configuración a > Serie >** `Horas SLA`.
10. Cambia el **Color** a `#FFCC00, color del tema 2`.
11. Coloca este visual a la derecha del gráfico `Composición mensual de entregas`.
12. Pasa el curso por encima del gráfico para ver las métricas añadidas en el cuadro contextual (información sobre herramientas).

**Resultado esperado:** el usuario puede comparar, por servicio, las horas reales medias hasta entrega frente al SLA comprometido y detectar servicios con mayor riesgo operativo.

<img width="1038" height="596" alt="image" src="https://github.com/user-attachments/assets/804d4415-9497-4da4-a405-04d9c4aa7092" />

---

### Entregables
  
Al finalizar, debes tener un archivo .pbix con:

- Tres páginas de informe:
  - **Visión global**
  - **Detalle operativo**
  - **KPIs Oficina**

- Diseño visual consistente con el tema y logo de ParcelCraft.

## Resumen
  
En este laboratorio has creado un informe analítico de tres páginas para ParcelCraft, partiendo del modelo semántico construido en los laboratorios anteriores.

Has diseñado una página de **Visión global** para el análisis ejecutivo, una página de **Detalle operativo** centrada en la comparación por servicio, y una página **KPIs Oficina** filtrada por una oficina concreta.

También has aplicado principios de diseño efectivo, usado segmentaciones y filtros, y creado medidas explícitas para comparar el SLA comprometido con las horas reales de entrega.

El resultado es un informe claro, consistente y preparado para ampliarse posteriormente con marcadores y seguridad a nivel de fila.

### Recursos útiles
- Tipos de visualizaciones en Power BI: [https://learn.microsoft.com/power-bi/visuals/power-bi-visualization-types-for-reports-and-q-and-a](https://learn.microsoft.com/power-bi/visuals/power-bi-visualization-types-for-reports-and-q-and-a)
- Agregar filtros a informes en Power BI: [https://learn.microsoft.com/power-bi/create-reports/power-bi-report-add-filter](https://learn.microsoft.com/power-bi/create-reports/power-bi-report-add-filter)
- Cambiar cómo interactúan los visuales en un informe: [https://learn.microsoft.com/power-bi/create-reports/service-reports-visual-interactions](https://learn.microsoft.com/power-bi/create-reports/service-reports-visual-interactions)
- Segmentaciones en Power BI: [https://learn.microsoft.com/power-bi/visuals/power-bi-visualization-slicers](https://learn.microsoft.com/power-bi/visuals/power-bi-visualization-slicers)
- Exploración en profundidad en Power BI: [https://learn.microsoft.com/power-bi/consumer/end-user-drill](https://learn.microsoft.com/power-bi/consumer/end-user-drill)
- Guía de diseño de informes en Power BI: [https://learn.microsoft.com/power-bi/create-reports/desktop-report-design](https://learn.microsoft.com/power-bi/create-reports/desktop-report-design)

---

### 🎉 Fin del laboratorio
  
¡Enhorabuena! Has completado el Laboratorio 6 de ParcelCraft. Ahora tienes un informe analítico de tres páginas, con diseño consistente, visuales adecuados, filtros interactivos, exploración en profundidad y una vista de oficina preparada para conectar con escenarios posteriores de seguridad a nivel de fila. 👏

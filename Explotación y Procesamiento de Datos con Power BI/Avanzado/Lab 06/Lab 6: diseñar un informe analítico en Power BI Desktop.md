# Laboratorio 6: Diseño de informes analíticos e interacción con filtros en Power BI
  
**ParcelCraft** ya cuenta con un modelo en estrella, una tabla `'Fecha'`, jerarquías de negocio, medidas DAX empresariales y un grupo de cálculo de inteligencia de tiempo. En este laboratorio trabajarás como diseñador de informes analíticos: crearás un informe de tres páginas, aplicarás principios de diseño visual, seleccionarás objetos visuales adecuados, configurarás segmentaciones, filtros e interacciones, y prepararás una página de KPIs enfocada en una oficina concreta.  
El objetivo es pasar de un modelo semántico validado a una experiencia de análisis clara, navegable y preparada para usuarios de negocio.

## Objetivos de aprendizaje
  
**Tiempo estimado total: 45 minutos**  
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
- Haber completado el **Laboratorio 5: DAX empresarial en Power BI**, o partir del archivo inicial.
- Descargarse los **archivos del laboratorio**, el logo y tema de ParcelCraft, disponibles en la carpeta **Files**.
- Conocimientos básicos de visualizaciones, segmentadores, filtros, interacciones y jerarquías en Power BI.  

> Tienes un archivo inicial y solución disponibles.
> [Archivo inicial (Starter)](https://github.com/quizeth/Labs/blob/main/Explotaci%C3%B3n%20y%20Procesamiento%20de%20Datos%20con%20Power%20BI/Intermedio/Files/03%20Starter%20ParcelCraft.pbix)
> [Archivo final (Solución)](https://github.com/quizeth/Labs/blob/main/Explotaci%C3%B3n%20y%20Procesamiento%20de%20Datos%20con%20Power%20BI/Intermedio/Files/03%20Soluci%C3%B3n%20ParcelCraft.pbix)


---

## Ejercicio 1: Crear la página Visión global
  
En este ejercicio crearás la primera página del informe. Esta página debe proporcionar una visión global del análisis y permitir que un usuario entienda rápidamente el comportamiento general de ParcelCraft.  

La página debe resumir volumen, ingresos, margen, cumplimiento y evolución temporal. Debe funcionar como una vista ejecutiva, por lo que los KPIs principales deben estar en la parte superior y los gráficos de análisis en la zona central. Además, al ser la página de aterrizaje, debe ser ligera para agilizar la carga del informe al abrirlo.

### Tarea 1: Abrir y definir la estructura del informe
1. Abre **Power BI Desktop**.
2. Abre el archivo .pbix generado al final del Laboratorio 5.
3. En la **vista Informe**, elimina las páginas existentes del Laboratorio 5.
> Como Power BI te obligará a mantener una, limpia todas las visualizaciones de esa página.

3. Renombra la página existente como **Vision global**.
4. Haz clic derecho en el nombre de la página y selecciona **Establecer como página de aterrizaje**.
5. En la pestaña **Ver**, ve a **Temas > Explorar temas**.
6. Carga el tema **ParcelCraft_Theme**.
> Tienes el [archivo del tema](https://github.com/quizeth/Labs/blob/main/Explotaci%C3%B3n%20y%20Procesamiento%20de%20Datos%20con%20Power%20BI/Avanzado/Lab%2006/Files/ParcelCraft_Theme.json) en la carpeta de Files de este laboratorio.


### Tarea 2: Configurar encabezado y diseño base
1. Ve a la página **Visión global**.
3. Añade una imagen a la esquina superior izquierda
4. Inserta el logo de la empresa. 
> Tienes el [archivo del logo](https://github.com/quizeth/Labs/blob/main/Explotaci%C3%B3n%20y%20Procesamiento%20de%20Datos%20con%20Power%20BI/Avanzado/Lab%2006/Files/ParcelCraft.png) en la carpeta de Files de este laboratorio. La URL es: `https://github.com/quizeth/Labs/blob/main/Explotaci%C3%B3n%20y%20Procesamiento%20de%20Datos%20con%20Power%20BI/Avanzado/Lab%2006/Files/ParcelCraft.png?raw=true`
4. Configura el logo:
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

<img width="1175" height="670" alt="image" src="https://github.com/user-attachments/assets/6419fd3b-d2b3-4121-9126-1e3a462767f4" />

> Buena práctica: la ubicación debe guiar la lectura. Primero se leen KPIs, después tendencias y comparativas, y finalmente filtros o detalles.

### Tarea 3: Crear tarjetas de KPIs principales
1. Inserta una visualización de tarjeta.
> Si tienes la nueva visualización de tarjeta (con el icono de un rayo), sigue los pasos tal y como están escritos.
> Si tienes la antigua (sin el icono), crea una tarjeta individual para cada métrica. Después, alinéalas en la misma fila y ajusta el tamaño para que tengan el mismo ancho y alto.

2. Asigna las siguientes medidas:
    - `Total envíos`
    - `Ingresos`
    - `Margen estimado`
    - `% margen estimado`
    - `% envíos entregados`.

3. Ajusta la tarjeta para que cubra el ancho de todo el lienzo.
4. Sitúa la tarjeta debajo del título y subtítulo.
5. Ajusta los formatos de las tarjetas. Para ello, con la tarjeta seleccionada, ve a la segunda pestaña del panel de visualizaciones (Dar formato a su objeto visual, icono del gráfico y el pincel.
> A partir de ahora, cada vez que el laboratorio se refiera a una opción de formato, dirígete a esta pestaña.

6. Ve a **Objeto Visual > Llamada > Aplicar configuración a**.
7. En el menú desplegable **Tarjetas**, selecciona **Ingresos**.
8. Amplía la pestaña **Valor** y configura:
   - **Mostrar unidades**: Millones
   - **Posiciones decimales**: 2
9. Revisa que los formatos de moneda y porcentaje se muestran correctamente. Si no es el caso, repite los pasos anteriores para ajustar la configuración de cada medida.
   
**Resultado esperado:** la zona superior permite entender de inmediato el estado general del negocio.

### Tarea 4: Crear visuales globales de análisis
1. Inserta un gráfico de columnas agrupadas y de líneas.
2. Configura el gráfico:
  - **Eje X:** `'Fecha'[Año mes]`
  - **Eje Y:** `Total envíos`
  - **Eye Y de línea:** `% margen estimado`
3. En el panel de formato, ve a Ve a **General > Título**.
4.  Cambia el título a `Evolución de envíos`.
5. En el objeto visual, selecciona los **tres puntos > Ordenar por** `'Fecha'[Año mes]` de forma ascendente.

6. Inserta un gráfico de barras agrupadas.
7. Configura el gráfico:
- **Eje X:** `'Oficina'[Oficinas]`
- **Eje Y:** `Ingresos`
8. Ordena el visual de mayor a menor (orden descendente) por `Ingresos`.
  
9. Inserta un gráfico circular.
13. Configura el gráfico:
- **Leyenda:** `'Cliente'[Segmento]`
- **Valores:** `Total envíos`
14. Cambia el título a `Distribución por segmento de cliente`.  

**Resultado esperado:** la página combina tendencia temporal, comparativa por oficina, y distribución por segmento de cliente.

<img width="1166" height="666" alt="image" src="https://github.com/user-attachments/assets/0d677760-15fc-4959-90be-4458f3a32c6e" />

### Tarea 5: Añadir segmentaciones de visión global
1. Inserta una segmentación de datos con `'Fecha'[Año]`.
2. Inserta una segunda segmentación con `'Servicio'[Servicio]`.
4. Coloca las segmentaciones en la esquina superior derecha.
7. Configura ambas segmentaciones:
   -  **Configuración de la segmentación > Opciones > Estilo**: Menú desplegable.
   -  **Configuración de la segmentación > Opciones > Mostrar opción "Seleccionar todo"**: activado.
   -  **Valores > Fondo > Color**: Blanco
   -  Pestaña **General > Efectos > Fondo:** Desactivado
9. Configura el **Año** como `2023`.
10. Con la segmentación de **Año** seleccionada, abre el panel de **Filtros**.
11. Bajo la sección **Filtros de este objeto visual**, expande la tarjeta **Año**-
12. Configura **Tipo de filtro** > **Filtrado básico** como se muestra:
   <img width="203" height="456" alt="image" src="https://github.com/user-attachments/assets/50870a14-70ed-467f-801e-6afe032bd72f" />

**Resultado esperado:** la página permite filtrar la visión global por tiempo y servicio.

<img width="1175" height="668" alt="image" src="https://github.com/user-attachments/assets/ce75ed63-9ee6-4ea9-83a7-0c22cd310525" />


## Ejercicio 2: Crear la página Detalle operativo
  
En este ejercicio crearás la segunda página del informe. Esta página debe proporcionar una visión más detallada de un ámbito concreto: el rendimiento operativo por oficina y servicio.  
Para mantener la página clara y útil, trabajarás con una única matriz como objeto visual principal y con pocas segmentaciones de apoyo.

El objetivo es que el usuario pueda comparar oficinas y servicios sin sobrecargar el informe con demasiados gráficos.


### Tarea 1: Configurar la página de detalle
1. Duplica la página **Visión global**.
2. Renombra la página como **Detalle operativo**.
3. Elimina los objetos visuales, dejando únicamente la sección del encabezado.
4. Inserta un cuadro de texto como encabezado.
5. Escribe el título: `Detalle operativo`.
6. Debajo, añade el subtítulo: `Comparativa de volumen, ingresos, margen y cumplimiento.`

<img width="1175" height="666" alt="image" src="https://github.com/user-attachments/assets/ded6b062-699e-427c-bb4a-07747fb5810c" />


#### Tarea 2: Crear matriz operativa
1. Inserta una visualización de matriz.
2. Configura la matriz:
- **Filas:** `'Oficina'[Oficinas]`
- **Columnas:** `'Servicio'[Servicio]`
- **Valores:** `Total envíos`
- **Valores:** `Ingresos`
- **Valores:** `% margen estimado`

4. Configura los totales:
   - **Objeto visual** > **Subtotales de fila** > **Valores** > **Color de fondo**: `#ffeb99, color del tema 2, 60% más claro` (el amarillo más claro).
   - **Objeto visual** > **Total general de columna** > **Valores** > **Color de fondo**: `#ffeb99, color del tema 2, 60% más claro` (el amarillo más claro).
   - **Objeto visual** > **Total general de fila** > **Valores** > **Color de fondo**: `#ffeb99, color del tema 2, 60% más claro` (el amarillo más claro).

**Resultado esperado:** la matriz permite comparar oficinas y servicios a través de medidas clave del modelo semántico.

<img width="1039" height="593" alt="image" src="https://github.com/user-attachments/assets/42df8973-9ae0-4d1e-8000-d4e200ecdcaa" />


## Ejercicio 3: Crear la página KPIs Oficina
  
En este ejercicio crearás la tercera página del informe. Esta página debe mostrar KPIs de una oficina concreta y recrear el resultado que más adelante se verá filtrado mediante RLS.

No implementarás seguridad a nivel de fila en este laboratorio. En su lugar, aplicarás un filtro de página sobre `'Oficina'[Oficina]` para simular que el usuario solo puede ver la información de una oficina.

El objetivo es que la página sea sencilla, clara y orientada al seguimiento del cumplimiento SLA de una oficina concreta.


### Tarea 1: Crear la página de oficina

1. Duplica la página **Detalle operativo**.
2. Renombra la nueva página como **KPIs Oficina**.
3. Elimina la matriz de la página duplicada.
4. Mantén únicamente el encabezado con el logo, el título y el subtítulo.
5. Cambia el título por: `KPIs de oficina`.
6. Cambia el subtítulo por: `El rendimiento de tu oficina de un vistazo`.
7. Comprueba que el encabezado mantiene el mismo estilo visual que las páginas anteriores.

> Esta página debe parecer parte del mismo informe. Mantén la misma estructura visual, colores, tipografías y espaciado usados en **Visión global** y **Detalle operativo**.


### Tarea 2: Aplicar filtro de página por oficina

1. Abre el panel **Filtros**.
2. Arrastra `'Oficina'[Oficina]` al área **Filtros de esta página**.
3. En el filtro de `'Oficina'[Oficina]`, selecciona una oficina concreta.
> Utiliza una oficina con datos suficientes para que los objetos visuales muestren información.

> **Importante**: este filtro no es RLS. Solo simula el resultado visual de una página limitada a una oficina. En un laboratorio posterior, este comportamiento se controlará desde la seguridad a nivel de fila.


### Tarea 3: Crear KPIs de cumplimiento SLA

1. Inserta una visualización de tarjeta.
2. Asigna las siguientes medidas:
   - `% envíos entregados`
   - `% envíos tardíos`
   - `Días medios hasta entrega`
   - `Promedio intentos`
   - `Índice operativo ponderado`
3. Coloca la tarjeta debajo del encabezado.
4. Ajusta la tarjeta para que cubra el ancho principal del lienzo.
5. Configura el formato de la tarjeta siguiendo el mismo estilo usado en la página **Visión global**.
6. Revisa que `% envíos entregados` y `% envíos tardíos` se muestran con formato de porcentaje.
7. Revisa que `Días medios hasta entrega`, `Promedio intentos` e `Índice operativo ponderado` se muestran con un formato numérico legible.
8. Confirma que los valores corresponden únicamente a la oficina seleccionada en el filtro de página.
9. Usa estos KPIs para evaluar la oficina desde una perspectiva operativa: entregas completadas, retrasos, tiempo medio de entrega, intentos y rendimiento operativo ponderado.

**Resultado esperado:** la parte superior de la página muestra indicadores específicos de cumplimiento SLA para la oficina seleccionada.


### Tarea 4: Crear visual de seguimiento de entregas

1. Inserta un gráfico de columnas 100% apiladas.
2. Configura el gráfico:
   - **Eje X:** `'Fecha'[Año mes]`
   - **Eje Y:** `Total no entregados`, `Total entregados`. 
3. En el panel de formato, ve a **General > Título**.
4. Cambia el título a `Seguimiento mensual de entregas`.
5. Ordena el visual por `'Fecha'[Año mes]` de forma ascendente.
6. Coloca el visual debajo de las tarjetas de KPIs, ocupando la zona izquierda de la página.

**Resultado esperado:** el usuario puede comparar mensualmente el volumen de entregas y no entregas de la oficina seleccionada.


### Tarea 5: Crear medidas sobre horas SLA

Como el modelo usa grupos de cálculo, evita usar medidas implícitas arrastrando directamente la columna `'Servicio'[SLAHoras]` a un visual. En su lugar, crea una medida explícita para representar el SLA comprometido en horas. También vas a crear una medida para comparar el SLA establecido con las horas reales de entrega.

1. Ve a la tabla `'Medidas'`.

2. Crea la medida `Horas medias hasta entrega`:

```DAX
Horas medias hasta entrega =
[Días medios hasta entrega] * 24
```

3. Crea la medida `Horas SLA`:

```DAX
Horas SLA =
MAX('Servicio'[Horas SLA])
```

4. Configura la medida `Horas medias hasta entrega`:
   - **Formato**: Número decimal
   - **Posiciones decimales**: 2
   - **Carpeta para mostrar**: Indicadores operativos

6. Configura la medida `Horas SLA`:
   - **Format**: Número entero
   - **Carpeta para mostrar**: Indicadores operativos


**Resultado esperado:** el modelo contiene dos medidas comparables en la misma unidad: `Horas medias hasta entrega` y `Horas SLA`.


### Tarea 6: Comparar SLA comprometido y entrega real por servicio

1. Inserta un gráfico de barras agrupadas.
2. Configura el gráfico:
   - **Eje Y:** `'Servicio'[Servicio]`
   - **Eje X:** `Horas medias hasta entrega`
   - **Eje X:** `'Horas SLA`

5. Añade estas medidas a **Información sobre herramientas**:
   - `Total envíos`
   - `% envíos entregados`
   - `% envíos entregados`
   - `% no entregados`
   - `Promedio intentos`
   - `Índice operativo ponderado`

6. En el panel de formato, ve a **General > Título**.
7. Cambia el título a `SLA comprometido vs horas reales`.
8. Ordena el visual de mayor a menor por `Horas medias hasta entrega`.
9. En el panel de formato, ve a **Objeto visual > Barras**.
10. Selecciona **Aplica configuración a > Serie >** `Horas SLA`.
11. Cambia el **Color** a `#FFCC00, color del tema 2`.
12. Coloca este visual a la derecha del gráfico `Composición mensual de entregas`.


**Resultado esperado:** el usuario puede comparar, por servicio, las horas reales medias hasta entrega frente al SLA comprometido y detectar servicios con mayor riesgo operativo.

<img width="1040" height="593" alt="image" src="https://github.com/user-attachments/assets/c8e0e71f-8980-4869-9a06-0c8457661f4d" />

---

### Entregables
  
Al finalizar, debes tener un archivo .pbix con:

- Tres páginas de informe:
  - **Visión global**
  - **Detalle operativo**
  - **KPIs Oficina**

- Visuales principales para analizar:
  - KPIs del negocio.
  - Comparativa por oficina y servicio.
  - Cumplimiento SLA de una oficina concreta.

- Filtros y segmentaciones configurados para año, servicio y oficina.

- Medidas explícitas creadas:
  - `Horas medias hasta entrega`
  - `Horas SLA`

- Diseño visual consistente con el tema y logo de ParcelCraft.

## Resumen
  
En este laboratorio has creado un informe analítico de tres páginas para ParcelCraft, partiendo del modelo semántico construido en los laboratorios anteriores.

Has diseñado una página de **Visión global** para el análisis ejecutivo, una página de **Detalle operativo** centrada en la comparación por oficina y servicio, y una página **KPIs Oficina** filtrada por una oficina concreta.

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

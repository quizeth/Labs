# Laboratorio 5: Análisis avanzado e inteligencia artificial en Power BI

## 1. Introducción

En este laboratorio continuarás ampliando el informe analítico de ParcelCraft que has construido en los laboratorios anteriores. Hasta ahora, el informe ya permite analizar el rendimiento operativo y comercial de la compañía mediante páginas de resumen, análisis temporal, segmentaciones, tooltips, marcadores, formato condicional y medidas DAX empresariales.

En esta nueva fase vas a incorporar capacidades analíticas avanzadas de Power BI Desktop para que el informe no solo muestre qué ha ocurrido, sino que también ayude a detectar comportamientos inesperados, explicar variaciones, comparar entidades a lo largo del tiempo y descubrir factores que influyen en los resultados del negocio.

El laboratorio está diseñado para completarse en aproximadamente 30 minutos. Trabajarás sobre el archivo `.pbix` ya preparado de ParcelCraft y añadirás nuevos objetos visuales y funcionalidades analíticas manteniendo la misma narrativa del informe: ayudar al equipo de operaciones y dirección a comprender mejor el rendimiento de envíos, ingresos, márgenes y entregas.

## 2. Objetivos de aprendizaje

Al finalizar este laboratorio serás capaz de:

- Aplicar detección de anomalías en una serie temporal para identificar comportamientos inesperados.
- Crear una técnica de discretización para segmentar valores numéricos en intervalos analíticos.
- Construir un gráfico de dispersión con eje temporal de reproducción para observar evolución por entidad.
- Utilizar la característica Analizar para explicar una subida, bajada o diferencia relevante.
- Crear un cálculo visual directamente dentro de un objeto visual.
- Incorporar un objeto visual de IA para obtener insights adicionales sobre una métrica de negocio.
- Interpretar los resultados analíticos en el contexto operativo de ParcelCraft.

Tiempo estimado total: 30 minutos.

## 3. Antes de empezar

### Requisitos

Para completar este laboratorio necesitas:

- Haber completado el laboratorio anterior de la serie ParcelCraft, correspondiente al diseño del informe analítico.
- Disponer del archivo `.pbix` actualizado con el modelo semántico validado.
- Tener Power BI Desktop instalado en el equipo.
- Tener creadas las páginas de informe utilizadas en los laboratorios anteriores.
- Tener disponibles las medidas DAX empresariales `Ingresos`, `Coste`, `Margen`, `% Margen`, `Pedidos` y `% Entregas a tiempo`.
- Tener conocimientos previos de visualizaciones, filtros, segmentaciones, tooltips, marcadores y formato condicional.

Si alguno de los nombres de tabla o campo de tu archivo difiere ligeramente, utiliza el campo equivalente que se haya creado en los laboratorios anteriores. En este laboratorio se usarán como referencia las tablas `Calendario`, `Envios`, `Clientes`, `Rutas`, `Servicios` y `Geografia`.

---

## 4. Desarrollo del laboratorio

## Ejercicio 1: Aplicar detección de anomalías en un gráfico de líneas

En este ejercicio vas a utilizar la página temporal del informe para detectar días o meses con comportamientos inesperados en los ingresos de ParcelCraft. Esta funcionalidad es útil para identificar picos de demanda, caídas de operación o cambios que requieren revisión por parte del equipo de negocio.

### Tarea 1: Preparar una página de análisis temporal

1. Abre el archivo `.pbix` de ParcelCraft en Power BI Desktop.
2. Ve a la página del informe llamada `Análisis temporal`.
3. Haz clic con el botón derecho sobre la pestaña de la página `Análisis temporal`.
4. Selecciona la opción `Duplicar página`.
5. Cambia el nombre de la nueva página a `Análisis avanzado`.
6. Selecciona todos los objetos visuales que no sean necesarios para este ejercicio.
7. Pulsa la tecla `Supr` para dejar espacio libre en el lienzo.

### Tarea 2: Crear el gráfico de líneas base

1. En el panel `Visualizaciones`, selecciona el objeto visual `Gráfico de líneas`.
2. Coloca el visual en la parte superior izquierda del lienzo.
3. Arrastra el campo `Calendario[Fecha]` al área `Eje X` del visual.
4. Arrastra la medida `Ingresos` al área `Eje Y` del visual.
5. Abre el panel `Formato visual`.
6. Expande la sección `Eje X`.
7. Cambia la opción `Tipo` a `Continuo`.
8. Cambia el título del visual a `Ingresos con detección de anomalías`.

### Tarea 3: Activar la detección de anomalías

1. Selecciona el gráfico de líneas que acabas de crear.
2. En el panel `Visualizaciones`, abre la pestaña `Análisis`.
3. Localiza la sección `Buscar anomalías`.
4. Activa el interruptor de `Buscar anomalías`.
5. Ajusta el valor de `Sensibilidad` a `70`.
6. Cambia el color de las anomalías a un color que contraste con la línea principal.
7. Activa la opción de mostrar el intervalo esperado si aparece disponible en tu versión de Power BI Desktop.

### Tarea 4: Consultar la explicación de una anomalía

1. Localiza un punto marcado como anomalía en el gráfico de líneas.
2. Haz clic sobre el punto marcado como anomalía.
3. Revisa el panel de explicación que aparece junto al visual.
4. Arrastra el campo `Servicios[Tipo de servicio]` al área `Explicar por`, si el panel solicita campos de explicación.
5. Arrastra el campo `Geografia[Zona]` al área `Explicar por`.
6. Revisa qué servicio o zona aparece asociado al comportamiento inesperado.
7. Anota mentalmente si la anomalía representa una subida o una caída respecto al rango esperado.

### Interpretación

La detección de anomalías permite que ParcelCraft identifique automáticamente periodos en los que los ingresos se comportan de forma distinta a lo esperado. Un pico puede indicar una campaña, una concentración de envíos urgentes o un evento estacional. Una caída puede apuntar a incidencias operativas, menor volumen de pedidos o problemas en una zona concreta.

### Resultado esperado

El usuario puede identificar comportamientos inesperados en una serie temporal del informe y consultar posibles explicaciones asociadas a servicios o zonas de ParcelCraft.

---

## Ejercicio 2: Trabajar con discretización de datos

En este ejercicio vas a crear intervalos para analizar el plazo de entrega de los envíos. La discretización ayuda a transformar un campo numérico continuo en grupos comprensibles, lo que facilita la lectura de patrones operativos.

### Tarea 1: Crear intervalos para días de entrega

1. Ve al panel `Datos` situado a la derecha de Power BI Desktop.
2. Localiza la tabla `Envios`.
3. Haz clic con el botón derecho sobre el campo `Envios[Dias entrega]`.
4. Selecciona la opción `Nuevo grupo`.
5. En la ventana `Grupos`, confirma que el tipo de grupo es `Discretización`.
6. Cambia el nombre del nuevo campo a `Tramo días entrega`.
7. Establece el tamaño de la discretización en `2`.
8. Haz clic en `Aceptar`.

### Tarea 2: Crear un visual con los tramos de entrega

1. En la página `Análisis avanzado`, selecciona un área libre en la parte central izquierda del lienzo.
2. En el panel `Visualizaciones`, selecciona el objeto visual `Gráfico de columnas agrupadas`.
3. Arrastra el campo `Envios[Tramo días entrega]` al área `Eje X`.
4. Arrastra la medida `Pedidos` al área `Eje Y`.
5. Arrastra la medida `% Entregas a tiempo` al área `Información sobre herramientas`.
6. Abre el panel `Formato visual`.
7. Cambia el título del visual a `Pedidos por tramo de días de entrega`.

### Tarea 3: Revisar la utilidad analítica de los tramos

1. Haz clic en una columna correspondiente a un tramo bajo de días de entrega.
2. Observa si el resto de visuales de la página se filtran por ese tramo.
3. Haz clic en una columna correspondiente a un tramo alto de días de entrega.
4. Revisa el valor de `% Entregas a tiempo` en la información sobre herramientas.
5. Compara visualmente qué tramos concentran más pedidos.
6. Haz clic en un área en blanco del lienzo para quitar la selección.

### Interpretación

La discretización permite convertir el número exacto de días de entrega en intervalos operativos. Para ParcelCraft, esto facilita detectar si la mayor parte de los pedidos se entrega en tramos cortos o si existe una concentración relevante en tramos más lentos que debería investigarse.

### Resultado esperado

El usuario puede segmentar los datos de entrega en intervalos claros y analizar la distribución de pedidos por tramo de días de entrega.

---

## Ejercicio 3: Crear un gráfico de dispersión con eje temporal de reproducción

En este ejercicio vas a crear un gráfico de dispersión animado para observar cómo evolucionan las rutas de ParcelCraft a lo largo del tiempo. El objetivo es comparar ingresos, margen y volumen de pedidos por ruta, viendo cómo cambian sus posiciones mes a mes.

### Tarea 1: Insertar el gráfico de dispersión

1. Selecciona un área libre en la parte superior derecha de la página `Análisis avanzado`.
2. En el panel `Visualizaciones`, selecciona el objeto visual `Gráfico de dispersión`.
3. Ajusta el tamaño del visual para que ocupe aproximadamente la mitad derecha superior del lienzo.
4. Arrastra la medida `Ingresos` al área `Eje X`.
5. Arrastra la medida `% Margen` al área `Eje Y`.
6. Arrastra la medida `Pedidos` al área `Tamaño`.
7. Arrastra el campo `Rutas[Ruta]` al área `Detalles`.

### Tarea 2: Configurar el eje temporal de reproducción

1. Arrastra el campo `Calendario[Mes Año]` al área `Eje de reproducción`.
2. Arrastra el campo `Servicios[Tipo de servicio]` al área `Leyenda`.
3. Abre el panel `Formato visual`.
4. Expande la sección `Título`.
5. Cambia el título del visual a `Evolución de rutas por ingresos, margen y pedidos`.
6. Expande la sección `Marcadores`.
7. Activa los marcadores si no aparecen visibles.

### Tarea 3: Reproducir la evolución temporal

1. Localiza el control de reproducción situado en la parte inferior del gráfico de dispersión.
2. Haz clic en el botón `Reproducir`.
3. Observa cómo se desplazan las rutas a medida que cambia el mes.
4. Pausa la reproducción en un mes donde una ruta aparezca alejada del resto.
5. Sitúa el puntero sobre la burbuja de esa ruta para ver sus valores.
6. Identifica si la ruta combina ingresos altos, margen bajo o volumen elevado de pedidos.

### Interpretación

El gráfico de dispersión con eje de reproducción permite observar la evolución dinámica de rutas. En ParcelCraft, una ruta que se desplaza hacia la derecha aumenta ingresos, una ruta que sube mejora margen y una burbuja que crece gana volumen de pedidos. Durante la reproducción conviene observar rutas que cambian de cuadrante, rutas que crecen de forma rápida y rutas que mantienen ingresos altos con margen bajo.

### Resultado esperado

El usuario puede analizar cómo evolucionan las rutas de ParcelCraft a lo largo del tiempo en un gráfico de dispersión animado.

---

## Ejercicio 4: Usar la característica Analizar

En este ejercicio vas a utilizar la característica `Analizar` para explicar automáticamente una variación relevante en los ingresos por mes. Esta funcionalidad permite al usuario de negocio obtener una explicación inicial sin construir manualmente un análisis completo.

### Tarea 1: Crear un visual adecuado para analizar variaciones

1. Selecciona un área libre en la parte inferior izquierda de la página `Análisis avanzado`.
2. En el panel `Visualizaciones`, selecciona el objeto visual `Gráfico de columnas agrupadas`.
3. Arrastra el campo `Calendario[Mes Año]` al área `Eje X`.
4. Arrastra la medida `Ingresos` al área `Eje Y`.
5. Abre el panel `Formato visual`.
6. Cambia el título del visual a `Ingresos mensuales para análisis automático`.
7. Ordena el visual por `Calendario[Mes Año]` en orden ascendente.

### Tarea 2: Ejecutar la característica Analizar

1. Localiza una columna en la que los ingresos sean claramente superiores al mes anterior.
2. Haz clic con el botón derecho sobre esa columna.
3. Selecciona la opción `Analizar` en el menú contextual.
4. Selecciona la opción `Explicar el aumento`.
5. Espera a que Power BI genere la ventana de insights.
6. Revisa el primer gráfico explicativo que aparece en la ventana.
7. Lee el texto descriptivo que resume los factores que más contribuyen al aumento.

### Tarea 3: Revisar el insight generado

1. Usa los iconos inferiores de la ventana de insights para cambiar entre los tipos de visual sugeridos.
2. Revisa si el aumento se explica por `Servicios[Tipo de servicio]`, `Geografia[Zona]` o `Clientes[Segmento]`.
3. Identifica la categoría con mayor contribución al cambio.
4. Haz clic en el botón para agregar el insight al informe si tu versión de Power BI Desktop lo permite.
5. Cierra la ventana de insights.
6. Haz clic en un área en blanco del lienzo para quitar cualquier selección activa.

### Interpretación

La característica `Analizar` responde a una pregunta de negocio concreta: qué factores explican que los ingresos hayan subido o bajado en un periodo determinado. En ParcelCraft, esto ayuda al usuario self-service a investigar cambios mensuales sin crear manualmente múltiples desgloses por servicio, zona o segmento de cliente.

### Resultado esperado

El usuario puede obtener una explicación automatizada de un cambio relevante en un visual del informe y relacionarla con dimensiones de negocio de ParcelCraft.

---

## Ejercicio 5: Crear cálculos visuales

En este ejercicio vas a crear una suma acumulada de ingresos directamente en un objeto visual. El cálculo se almacenará en el propio visual y será útil para comparar la evolución mensual con una lectura acumulada del rendimiento.

### Tarea 1: Crear un visual base para el cálculo visual

1. Selecciona un área libre en la parte inferior central de la página `Análisis avanzado`.
2. En el panel `Visualizaciones`, selecciona el objeto visual `Tabla`.
3. Arrastra el campo `Calendario[Mes Año]` al área `Columnas`.
4. Arrastra la medida `Ingresos` al área `Columnas`.
5. Abre el panel `Formato visual`.
6. Cambia el título del visual a `Ingresos mensuales y acumulados`.
7. Ordena la tabla por `Calendario[Mes Año]` en orden ascendente.

### Tarea 2: Agregar un nuevo cálculo visual

1. Selecciona la tabla `Ingresos mensuales y acumulados`.
2. Ve a la cinta superior de Power BI Desktop.
3. Haz clic en la opción `Nuevo cálculo visual`.
4. Espera a que se abra la ventana de edición de cálculos visuales.
5. Haz clic en la barra de fórmulas del cálculo visual.
6. Escribe la fórmula `Ingresos acumulados visual = RUNNINGSUM([Ingresos])`.
7. Pulsa `Intro` para validar el cálculo.

### Tarea 3: Revisar el resultado del cálculo visual

1. Comprueba que aparece una nueva columna llamada `Ingresos acumulados visual` en la tabla.
2. Revisa que el valor acumulado aumenta mes a mes.
3. Compara el valor de `Ingresos` con el valor de `Ingresos acumulados visual` para varios meses.
4. Cambia el formato de la nueva columna a moneda si Power BI no lo aplica automáticamente.
5. Cierra la ventana de edición de cálculos visuales.
6. Ajusta el ancho de las columnas de la tabla para que los valores sean legibles.

### Interpretación

Un cálculo visual es adecuado en este caso porque la suma acumulada se necesita únicamente para este visual y depende del orden visible por mes. No es necesario crear una nueva medida DAX global en el modelo semántico si el cálculo solo aporta valor dentro de esta tabla específica.

### Resultado esperado

El usuario puede crear y utilizar un cálculo visual de suma acumulada directamente en un objeto visual del informe.

---

## Ejercicio 6: Incorporar un objeto visual de IA

En este ejercicio vas a incorporar el visual `Influenciadores clave` para identificar qué factores explican que un envío llegue tarde. Este análisis responde a una pregunta realista para ParcelCraft: qué características aumentan la probabilidad de incumplir el compromiso de entrega.

### Tarea 1: Insertar el visual de influenciadores clave

1. Selecciona un área libre en la parte inferior derecha de la página `Análisis avanzado`.
2. En el panel `Visualizaciones`, selecciona el objeto visual `Influenciadores clave`.
3. Ajusta el tamaño del visual para que ocupe la esquina inferior derecha del lienzo.
4. Arrastra el campo `Envios[Entrega tardía]` al área `Analizar`.
5. En el desplegable del campo analizado, selecciona el valor `Sí`.
6. Arrastra el campo `Servicios[Tipo de servicio]` al área `Explicar por`.
7. Arrastra el campo `Geografia[Zona]` al área `Explicar por`.
8. Arrastra el campo `Clientes[Segmento]` al área `Explicar por`.

### Tarea 2: Añadir factores operativos al análisis

1. Arrastra el campo `Rutas[Ruta]` al área `Explicar por`.
2. Arrastra el campo `Envios[Tramo días entrega]` al área `Explicar por`.
3. Arrastra la medida `Pedidos` al área `Expandir por`, si el área aparece disponible en tu versión.
4. Abre el panel `Formato visual`.
5. Cambia el título del visual a `Factores que influyen en entregas tardías`.
6. Comprueba que la pestaña `Influenciadores clave` está seleccionada dentro del visual.

### Tarea 3: Interpretar los influenciadores clave

1. Lee la frase principal que aparece en el panel izquierdo del visual.
2. Haz clic en el primer influenciador de la lista.
3. Revisa el gráfico del panel derecho para comparar el valor seleccionado con el resto de valores.
4. Haz clic en la pestaña `Segmentos principales`.
5. Selecciona el primer segmento mostrado por el visual.
6. Revisa qué combinación de servicio, zona, ruta o tramo está asociada a mayor probabilidad de entrega tardía.
7. Vuelve a la pestaña `Influenciadores clave`.

### Interpretación

El visual de influenciadores clave ayuda a detectar patrones que explican una métrica de negocio. En ParcelCraft, permite identificar si las entregas tardías están más asociadas a determinados tipos de servicio, zonas geográficas, rutas o tramos de días de entrega. Este insight es especialmente útil para priorizar acciones operativas sin construir manualmente múltiples análisis cruzados.

### Resultado esperado

El usuario puede utilizar un objeto visual de IA para obtener insights adicionales sobre los factores que influyen en las entregas tardías de ParcelCraft.

---

## 5. Entregables

Al finalizar el laboratorio, el alumno debe guardar un archivo `.pbix` actualizado de ParcelCraft que incluya:

- Un análisis temporal con detección de anomalías aplicada a un gráfico de líneas.
- Una técnica de discretización aplicada al campo de días de entrega.
- Un gráfico de dispersión con eje temporal de reproducción.
- Un ejemplo funcional de uso de la característica `Analizar`.
- Un cálculo visual creado directamente en un objeto visual.
- Un objeto visual de IA integrado mediante `Influenciadores clave`.

Guarda el archivo con el nombre `ParcelCraft_Lab05_Analisis_Avanzado.pbix`.

---

## 6. Resumen

En este laboratorio has ampliado el informe de ParcelCraft con capacidades analíticas avanzadas de Power BI. El informe ya no se limita a presentar métricas y segmentaciones, sino que incorpora funcionalidades que ayudan a detectar anomalías, explicar cambios, observar evolución temporal y descubrir factores relevantes para la operación.

La detección de anomalías permite identificar comportamientos inesperados en los ingresos. La discretización facilita agrupar los días de entrega en tramos interpretables. El gráfico de dispersión con eje de reproducción muestra cómo evolucionan las rutas en el tiempo. La característica `Analizar` ayuda a explicar variaciones sin crear análisis manuales complejos. Los cálculos visuales permiten resolver necesidades concretas dentro de un visual. El visual de influenciadores clave aporta una capa de IA orientada a descubrir qué factores están asociados a entregas tardías.

Estas funcionalidades aportan valor al usuario de negocio porque reducen el esfuerzo necesario para encontrar patrones, aceleran la interpretación de resultados y convierten el informe en una herramienta más activa para la toma de decisiones.

---

## 7. Recursos útiles

- Detección de anomalías en Power BI: https://learn.microsoft.com/es-es/power-bi/visuals/power-bi-visualization-anomaly-detection
- Panel de análisis en Power BI: https://learn.microsoft.com/es-es/power-bi/transform-model/desktop-analytics-pane
- Agrupación y discretización en Power BI Desktop: https://learn.microsoft.com/es-es/power-bi/create-reports/desktop-grouping-and-binning
- Gráficos de dispersión, burbuja y puntos en Power BI: https://learn.microsoft.com/es-es/power-bi/visuals/power-bi-visualization-scatter
- Uso de la característica Analizar para explicar fluctuaciones: https://learn.microsoft.com/es-es/power-bi/explore-reports/end-user-analyze-visuals
- Cálculos visuales en Power BI Desktop: https://learn.microsoft.com/es-es/power-bi/transform-model/desktop-visual-calculations-overview
- Visual de influenciadores clave en Power BI: https://learn.microsoft.com/es-es/power-bi/visuals/power-bi-visualization-influencers
- Narración inteligente en Power BI: https://learn.microsoft.com/es-es/power-bi/visuals/power-bi-visualization-smart-narrative

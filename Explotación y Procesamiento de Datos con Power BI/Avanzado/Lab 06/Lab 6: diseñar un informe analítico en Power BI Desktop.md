# Laboratorio 6: Diseño de informes analíticos, visualizaciones e interacción con filtros en Power BI
  
**ParcelCraft** es una empresa ficticia de logística que necesita convertir su modelo semántico en un **informe analítico claro, consistente y preparado para exploración de negocio**.  
En laboratorios anteriores preparaste los datos, modelaste el esquema en estrella, configuraste relaciones, jerarquías y medidas base. En esta sexta parte trabajarás principalmente en la **vista Informe** de Power BI Desktop para diseñar un informe de tres páginas, seleccionar visualizaciones adecuadas, configurar filtros e interacciones, y preparar una página enfocada en una oficina concreta como anticipo de escenarios de seguridad a nivel de fila.

## Objetivos de aprendizaje
  
**Tiempo estimado total: 45 minutos**  
Al finalizar este laboratorio serás capaz de:
- Diseñar un **informe analítico de tres páginas** en Power BI.
- Aplicar principios de diseño efectivo: ubicación, equilibrio, contraste, proximidad y repetición.
- Seleccionar objetos visuales adecuados según la pregunta de negocio.
- Crear una página de visión global para seguimiento ejecutivo.
- Crear una página de detalle para análisis operativo de oficinas, servicios y SLA.
- Crear una página de KPIs filtrada por una oficina concreta.
- Configurar segmentaciones, filtros de visual, filtros de página y filtros de informe.
- Configurar interacciones entre visuales y controlar el filtro cruzado.
- Utilizar jerarquías y exploración en profundidad para navegar por niveles de detalle.
- Validar la experiencia de navegación y lectura del informe.

### Antes de empezar

## Requisitos
  
Para completar este laboratorio necesitas:
- Power BI Desktop instalado.
- Conexión a Internet.
- Haber completado los laboratorios anteriores de ParcelCraft, especialmente el modelado del esquema en estrella y la creación de jerarquías.
- Conocimientos básicos de objetos visuales, medidas DAX y filtros en Power BI.  
Puedes partir del archivo .pbix que hayas construido durante los laboratorios anteriores. Si necesitas comenzar desde el conjunto de datos original, utiliza el archivo CSV de ParcelCraft:
- [ParcelCraft_50k.csv](https://raw.githubusercontent.com/quizeth/Labs/refs/heads/main/Explotaci%C3%B3n%20y%20Procesamiento%20de%20Datos%20con%20Power%20BI/Avanzado/Lab%2001/Files/ParcelCraft_50k.csv)

## Convenciones usadas en el laboratorio
- Cuando se indique seleccionar una vista, usa el panel izquierdo de Power BI Desktop:
- **Vista Informe**
- **Vista Tabla**
- **Vista Modelo**
- Cuando se indique una opción de cinta, se mostrará como **pestaña > grupo > comando**.
- Los nombres de tablas, columnas y medidas deben escribirse exactamente como se indican, salvo que tu archivo tenga nombres equivalentes ligeramente distintos.
- En este laboratorio no se rehace la preparación de Power Query ni el modelado dimensional; el foco está en el **diseño del informe y la experiencia de análisis**.

## Ejercicio 1: Revisar el modelo y preparar las medidas del informe
  
En este ejercicio revisarás que el modelo contiene las tablas necesarias para crear el informe y crearás medidas base que se reutilizarán en las tres páginas.  
La idea es partir de un modelo consistente y evitar crear cálculos directamente dentro de cada objeto visual.

#### Tarea 1: Abrir el archivo de trabajo
- Abre **Power BI Desktop**.
- Abre el archivo .pbix generado al final del laboratorio anterior.
- Si Power BI solicita aplicar cambios pendientes de Power Query, selecciona **Aplicar cambios**.
- Ve a la **vista Modelo**.
- Revisa que aparecen las siguientes tablas:
- Cliente
- Destino
- Envíos
- Oficina
- Ruta
- Servicio
- Comprueba que las relaciones principales están activas desde las dimensiones hacia Envíos.
- Ve a la **vista Informe**.

#### Tarea 2: Revisar campos útiles para el informe
- En el panel de Datos, expande la tabla Envíos.
- Identifica las columnas que servirán para métricas y análisis temporal, por ejemplo:
- EnvíoID
- FechaEnvío
- Peso (KG)
- Precio total
- Intentos
- EntregaTardia
- FechaCompromisoSLA
- Expande la tabla Oficina.
- Identifica columnas operativas como:
- Región
- Ciudad
- Oficina
- Expande la tabla Servicio.
- Identifica columnas de servicio como:
- Prioridad
- Servicio
- SLAHoras
- Expande la tabla Destino.
- Identifica columnas geográficas como:
- Región (Destino)
- Ciudad (Destino)
- Código Postal (Destino)  
**Resultado esperado:** identificas los campos que se utilizarán para KPIs, segmentaciones, comparativas, jerarquías y detalle operativo.

#### Tarea 3: Crear medidas base
- En la **vista Informe** o **vista Modelo**, selecciona la tabla Envíos.
- En la cinta, selecciona **Modelado > Nueva medida**.
- Crea la medida Total Envíos:
```DAX
Total Envíos = COUNTROWS(Envíos)
```
- Crea la medida Ingresos Totales:
```DAX
Ingresos Totales = SUM(Envíos[Precio total])
```
- Crea la medida Peso Total:
```DAX
Peso Total = SUM(Envíos[Peso (KG)])
```
- Crea la medida Peso Medio:
```DAX
Peso Medio = AVERAGE(Envíos[Peso (KG)])
```
- Crea la medida Intentos Medios:
```DAX
Intentos Medios = AVERAGE(Envíos[Intentos])
```
- Crea la medida Entregas Tardías:
```DAX
Entregas Tardías =
CALCULATE(
    [Total Envíos],
    Envíos[EntregaTardia] = TRUE()
)
```
- Crea la medida % Entrega Tardía:
```DAX
% Entrega Tardía = DIVIDE([Entregas Tardías], [Total Envíos])
```
- Crea la medida Envíos a Tiempo:
```DAX
Envíos a Tiempo = [Total Envíos] - [Entregas Tardías]
```
- Crea la medida % Envíos a Tiempo:
```DAX
% Envíos a Tiempo = DIVIDE([Envíos a Tiempo], [Total Envíos])
```
- Selecciona Ingresos Totales y configura el formato como moneda con símbolo €.
- Selecciona % Entrega Tardía y % Envíos a Tiempo y configura el formato como porcentaje.
- Selecciona Peso Total, Peso Medio e Intentos Medios y configura los decimales según criterio analítico.  
**Resultado esperado:** el modelo contiene medidas reutilizables para analizar volumen, ingresos, peso, intentos y cumplimiento operativo.

### Ejercicio 2: Crear la página de visión global
  
En este ejercicio crearás la primera página del informe. Esta página debe proporcionar una visión global del análisis y permitir al usuario entender rápidamente el estado general de las operaciones de ParcelCraft.  
La página debe responder preguntas como:
- ¿Cuántos envíos se han gestionado?
- ¿Qué ingresos se han generado?
- ¿Cuál es el nivel de cumplimiento del SLA?
- ¿Cómo evoluciona la operación en el tiempo?
- ¿Qué oficinas, servicios o destinos concentran más actividad?

#### Tarea 1: Configurar la página principal
- En la **vista Informe**, selecciona la primera página.
- Cambia el nombre de la página a Visión global.
- En el panel de formato de página, confirma que el tamaño es 16:9.
- Inserta un cuadro de texto en la parte superior.
- Escribe el título: Visión global de operaciones.
- Debajo del título, añade un subtítulo: Seguimiento ejecutivo de envíos, ingresos y cumplimiento operativo.
- Usa un tamaño de fuente mayor para el título y un tamaño menor para el subtítulo.
- Deja espacio suficiente debajo del encabezado para colocar los KPIs principales.

#### Tarea 2: Crear tarjetas de KPIs globales
- Inserta una visualización de tarjeta.
- Asigna la medida Total Envíos.
- Inserta una segunda tarjeta y asigna Ingresos Totales.
- Inserta una tercera tarjeta y asigna % Envíos a Tiempo.
- Inserta una cuarta tarjeta y asigna % Entrega Tardía.
- Alinea las cuatro tarjetas en una misma fila.
- Ajusta el tamaño de las tarjetas para que tengan el mismo ancho y alto.
- Aplica un fondo claro y un borde sutil si mejora la lectura.
- Usa el mismo estilo de título, etiquetas y valores en todas las tarjetas.  
Buena práctica: los KPIs principales deben ubicarse en la zona superior porque es el primer punto de lectura del usuario.

#### Tarea 3: Crear visuales de tendencia y distribución
- Inserta un gráfico de líneas.
- Configura el gráfico:
- **Eje X:** Envíos[FechaEnvío]
- **Eje Y:** Total Envíos
- Cambia el título a Evolución de envíos.
- Si Power BI muestra la jerarquía automática de fecha, conserva los niveles Año, Trimestre, Mes y Día para permitir exploración temporal.
- Inserta un gráfico de columnas agrupadas.
- Configura el gráfico:
- **Eje X:** Oficina[Oficina]
- **Eje Y:** Ingresos Totales
- Cambia el título a Ingresos por oficina.
- Ordena el visual de mayor a menor por Ingresos Totales.
- Inserta un gráfico de dona.
- Configura el gráfico:
- **Leyenda:** Envíos[EntregaTardia]
- **Valores:** Total Envíos
- Cambia el título a Distribución de cumplimiento SLA.
- Inserta un gráfico de barras horizontales.
- Configura el gráfico:
- **Eje Y:** Servicio[Servicio]
- **Eje X:** Total Envíos
- Cambia el título a Volumen por servicio.  
**Resultado esperado:** la página muestra KPIs principales, evolución temporal, comparativa por oficina, cumplimiento de SLA y volumen por servicio.

#### Tarea 4: Añadir segmentaciones globales
- Inserta una segmentación de datos.
- Asigna el campo Envíos[FechaEnvío].
- Cambia el tipo de segmentación a rango, si está disponible.
- Inserta una segunda segmentación.
- Asigna el campo Oficina[Región].
- Inserta una tercera segmentación.
- Asigna el campo Servicio[Servicio].
- Coloca las segmentaciones cerca de la parte superior o lateral del informe.
- Asegúrate de que no compiten visualmente con los KPIs principales.
- Prueba las segmentaciones y confirma que todos los visuales de la página se actualizan.  
**Resultado esperado:** el usuario puede filtrar la visión global por fecha, región y servicio sin perder claridad visual.

### Ejercicio 3: Crear la página de detalle operativo
  
En este ejercicio crearás la segunda página del informe. Esta página debe proporcionar una visión más detallada de un ámbito concreto: el rendimiento operativo por oficina y servicio.  
El objetivo es permitir que el usuario compare oficinas, detecte diferencias de cumplimiento, revise peso, ingresos e intentos, y explore la operación por jerarquías.

#### Tarea 1: Crear la página de detalle
- Crea una nueva página.
- Cambia el nombre de la página a Detalle operativo.
- Inserta un cuadro de texto como encabezado.
- Escribe el título: Detalle operativo por oficina y servicio.
- Debajo, añade el subtítulo: Análisis comparativo de volumen, ingresos, intentos y cumplimiento SLA.
- Mantén el mismo estilo de encabezado utilizado en la página Visión global.

#### Tarea 2: Crear una matriz de análisis
- Inserta una visualización de matriz.
- Configura la matriz:
- **Filas:** Oficina[Región], Oficina[Ciudad], Oficina[Oficina]
- **Columnas:** Servicio[Servicio]
- **Valores:** Total Envíos
- **Valores:** Ingresos Totales
- **Valores:** Intentos Medios
- **Valores:** % Entrega Tardía
- Activa el ajuste de texto si los nombres de oficinas o servicios son largos.
- Aplica formato condicional sobre % Entrega Tardía.
- Usa un color más intenso para valores altos de entrega tardía.
- Ajusta el ancho de columnas para facilitar la lectura.
- Cambia el título del visual a Matriz operativa por oficina y servicio.

#### Tarea 3: Crear visuales de análisis comparativo
- Inserta un gráfico de dispersión.
- Configura el gráfico:
- **Eje X:** Intentos Medios
- **Eje Y:** % Entrega Tardía
- **Tamaño:** Total Envíos
- **Leyenda:** Servicio[Prioridad]
- **Detalles:** Oficina[Oficina]
- Cambia el título a Relación entre intentos y entregas tardías.
- Inserta un gráfico de columnas apiladas.
- Configura el gráfico:
- **Eje X:** Oficina[Oficina]
- **Eje Y:** Total Envíos
- **Leyenda:** Envíos[EntregaTardia]
- Cambia el título a Cumplimiento SLA por oficina.
- Inserta un gráfico de barras.
- Configura el gráfico:
- **Eje Y:** Servicio[Servicio]
- **Eje X:** Peso Total
- Cambia el título a Peso gestionado por servicio.  
**Resultado esperado:** la página permite comparar oficinas y servicios usando volumen, ingresos, intentos, peso y cumplimiento del SLA.

#### Tarea 4: Configurar filtros cruzados entre visuales
- Selecciona el gráfico de columnas apiladas Cumplimiento SLA por oficina.
- En la cinta, selecciona **Formato > Editar interacciones**.
- Configura la interacción del gráfico sobre la matriz como filtro.
- Configura la interacción del gráfico sobre el gráfico de dispersión como filtro.
- Configura la interacción del gráfico sobre el gráfico de barras como filtro.
- Selecciona el gráfico de dispersión.
- Configura su interacción sobre la matriz como filtro.
- Desactiva la interacción del gráfico de dispersión sobre las segmentaciones, si aparece disponible.
- Sal de **Editar interacciones**.
- Selecciona una oficina en el gráfico de columnas.
- Comprueba que la matriz se actualiza con el contexto de esa oficina.
- Selecciona un punto del gráfico de dispersión.
- Comprueba que el resto de visuales muestran el contexto seleccionado.  
Nota: no todos los visuales deben afectar a todos los demás. Una interacción bien configurada ayuda a explorar; una interacción excesiva puede confundir al usuario.

#### Tarea 5: Añadir segmentaciones de detalle
- Inserta una segmentación con Servicio[Prioridad].
- Inserta una segmentación con Servicio[Servicio].
- Inserta una segmentación con Oficina[Región].
- Coloca las segmentaciones en una zona consistente con la página anterior.
- Prueba cada segmentación.
- Confirma que las segmentaciones afectan a la matriz y a los gráficos de la página.  
**Resultado esperado:** la página de detalle permite explorar el rendimiento operativo por oficina, servicio, prioridad y región.

### Ejercicio 4: Configurar jerarquías y exploración en profundidad
  
En este ejercicio usarás jerarquías existentes o crearás jerarquías de apoyo para facilitar la navegación desde niveles agregados hasta niveles detallados.  
La exploración en profundidad debe permitir que el usuario empiece con una visión resumida y descienda gradualmente hasta ciudad, oficina o servicio.

#### Tarea 1: Validar jerarquías existentes
- Ve a la **vista Modelo**.
- Expande la tabla Oficina.
- Comprueba si existe la jerarquía Oficinas.
- Si no existe, crea una jerarquía llamada Oficinas.
- Añade las columnas en este orden:
- Región
- Ciudad
- Oficina
- Expande la tabla Servicio.
- Comprueba si existe la jerarquía Servicios.
- Si no existe, crea una jerarquía llamada Servicios.
- Añade las columnas en este orden:
- Prioridad
- Servicio
- Vuelve a la **vista Informe**.

#### Tarea 2: Crear visual con jerarquía operativa
- Ve a la página Detalle operativo.
- Inserta un gráfico de columnas agrupadas.
- Configura el gráfico:
- **Eje X:** jerarquía Oficinas
- **Eje Y:** Total Envíos
- Cambia el título a Exploración por jerarquía de oficinas.
- Selecciona el visual.
- Activa los controles de exploración en profundidad del visual.
- Usa la opción de bajar al siguiente nivel.
- Navega desde Región hasta Ciudad.
- Baja de Ciudad hasta Oficina.
- Usa la opción de subir nivel para volver a la vista agregada.
- Usa la opción de expandir todo al siguiente nivel para comparar todos los elementos con más detalle.  
**Resultado esperado:** el visual permite analizar los envíos desde región hasta oficina de forma interactiva.

#### Tarea 3: Crear visual con jerarquía de servicio
- Inserta un gráfico de barras horizontales.
- Configura el gráfico:
- **Eje Y:** jerarquía Servicios
- **Eje X:** Ingresos Totales
- Cambia el título a Exploración por jerarquía de servicio.
- Activa los controles de exploración del visual.
- Baja desde Prioridad hasta Servicio.
- Comprueba que el usuario puede identificar qué servicios generan más ingresos dentro de cada prioridad.
- Ajusta el tamaño del visual para que los nombres de servicio sean legibles.  
**Resultado esperado:** el usuario puede explorar ingresos desde un nivel agregado de prioridad hasta el detalle de servicio.

### Ejercicio 5: Crear la página de KPIs de una oficina concreta
  
En este ejercicio crearás la tercera página del informe. Esta página debe proporcionar KPIs sobre una oficina concreta y recrear el resultado que más adelante se podrá controlar mediante seguridad a nivel de fila.  
No implementarás RLS en este laboratorio. Solo simularás la experiencia aplicando filtros de página a una oficina específica.

#### Tarea 1: Crear la página de KPIs
- Crea una nueva página.
- Cambia el nombre de la página a KPIs Oficina.
- Inserta un cuadro de texto como encabezado.
- Escribe el título: KPIs de oficina.
- Debajo, añade el subtítulo: Vista enfocada en el rendimiento de una oficina concreta.
- Mantén el mismo estilo visual de títulos utilizado en las páginas anteriores.

#### Tarea 2: Aplicar filtro de página por oficina
- Inserta una segmentación de datos con Oficina[Oficina].
- Selecciona una oficina concreta.
- En el panel **Filtros**, arrastra Oficina[Oficina] al área **Filtros de esta página**.
- Selecciona la misma oficina elegida en la segmentación.
- Comprueba que todos los visuales de la página quedan filtrados por esa oficina.
- Si quieres simular una vista fija, oculta la segmentación o reduce su presencia visual.
- Si quieres permitir comparación manual, mantén visible la segmentación.
- Bloquea el filtro de página si quieres evitar cambios accidentales durante la revisión.  
Importante: esta página no implementa seguridad a nivel de fila. Solo recrea el resultado visual esperado para una oficina concreta.

#### Tarea 3: Crear KPIs de oficina
- Inserta cinco visuales de tarjeta en la parte superior de la página.
- Configura las tarjetas con estas medidas:
- Total Envíos
- Ingresos Totales
- Peso Medio
- Intentos Medios
- % Envíos a Tiempo
- Alinea las tarjetas en una misma fila.
- Usa el mismo tamaño, color, borde y estilo que en la página Visión global.
- Revisa que los valores cambian al modificar la oficina seleccionada.

#### Tarea 4: Crear visuales de seguimiento de oficina
- Inserta un gráfico de líneas.
- Configura el gráfico:
- **Eje X:** Envíos[FechaEnvío]
- **Eje Y:** Total Envíos
- Cambia el título a Evolución de envíos de la oficina.
- Inserta un gráfico de columnas.
- Configura el gráfico:
- **Eje X:** Servicio[Servicio]
- **Eje Y:** Ingresos Totales
- Cambia el título a Ingresos por servicio.
- Inserta un gráfico de dona.
- Configura el gráfico:
- **Leyenda:** Envíos[EntregaTardia]
- **Valores:** Total Envíos
- Cambia el título a Cumplimiento SLA de la oficina.
- Inserta una tabla de detalle.
- Agrega los campos:
- Envíos[EnvíoID]
- Envíos[FechaEnvío]
- Servicio[Servicio]
- Destino[Ciudad (Destino)]
- Envíos[Peso (KG)]
- Envíos[Precio total]
- Envíos[Intentos]
- Envíos[EntregaTardia]
- Coloca la tabla en la parte inferior de la página.
- Aplica formato a columnas numéricas y monetarias.  
**Resultado esperado:** la página muestra el rendimiento de una única oficina mediante KPIs, tendencia, distribución de cumplimiento y detalle de envíos.

### Ejercicio 6: Configurar filtros, panel de filtros y diseño efectivo
  
En este ejercicio revisarás el informe completo para asegurar que las segmentaciones, el panel de filtros, las interacciones y el diseño visual funcionan de forma coherente.  
También aplicarás explícitamente los principios de ubicación, equilibrio, contraste, proximidad y repetición.

#### Tarea 1: Configurar el panel de filtros
- Ve a la página Visión global.
- Selecciona el gráfico Distribución de cumplimiento SLA.
- En el panel **Filtros**, revisa el área **Filtros de este objeto visual**.
- Comprueba que el filtro de Envíos[EntregaTardia] está disponible para ese visual.
- Selecciona un espacio vacío de la página.
- En el área **Filtros de esta página**, agrega Servicio[Prioridad].
- Selecciona una prioridad de prueba.
- Comprueba que toda la página se actualiza.
- Borra la selección para volver a mostrar todos los datos.
- En el área **Filtros de todas las páginas**, agrega Envíos[FechaEnvío] si quieres probar un rango temporal global.
- Selecciona un rango temporal de prueba.
- Navega por las tres páginas y comprueba que el filtro se aplica a todo el informe.
- Borra el filtro global si no quieres dejarlo aplicado.
- Ve a la página KPIs Oficina.
- Comprueba que Oficina[Oficina] está aplicado en **Filtros de esta página**.  
Nota: utiliza segmentaciones para filtros frecuentes y visibles. Utiliza el panel de filtros para filtros técnicos, de página o de informe completo.

#### Tarea 2: Revisar principios de diseño efectivo
- Ve a la página Visión global.
- Revisa la ubicación de los elementos.
- Comprueba que los KPIs principales están en la zona superior.
- Verifica que los visuales de análisis ocupan la parte central.
- Revisa que las segmentaciones están situadas en una zona fácil de localizar.
- Revisa el equilibrio visual.
- Asegúrate de que no hay una zona con demasiados elementos y otra zona completamente vacía.
- Revisa el contraste.
- Usa colores más intensos solo para destacar información relevante, como entregas tardías o desviaciones de SLA.
- Revisa la proximidad.
- Mantén juntos los KPIs, agrupa los filtros y sitúa los detalles cerca de los visuales relacionados.
- Revisa la repetición.
- Usa el mismo estilo de tarjetas, títulos, subtítulos, bordes y colores en las tres páginas.
- Repite esta revisión en la página Detalle operativo.
- Repite esta revisión en la página KPIs Oficina.

#### Tarea 3: Validar la experiencia de navegación
- Ve a la página Visión global.
- Selecciona un rango de fechas en la segmentación.
- Comprueba que los KPIs y gráficos se actualizan.
- Selecciona una región o servicio.
- Verifica que el informe refleja únicamente el contexto seleccionado.
- Limpia los filtros de la página.
- Ve a la página Detalle operativo.
- Selecciona una oficina en el gráfico de columnas.
- Comprueba que la matriz y el gráfico de dispersión se filtran correctamente.
- Usa la jerarquía Oficinas para bajar un nivel.
- Sube de nuevo al nivel anterior.
- Verifica que la exploración en profundidad es comprensible.
- Ve a la página KPIs Oficina.
- Confirma que todos los visuales muestran datos de una única oficina.
- Selecciona un estado de cumplimiento en el gráfico de dona.
- Comprueba que la tabla inferior se actualiza según la selección.
- Limpia la selección del gráfico de dona.
- Guarda el archivo.  
**Resultado esperado:** el informe permite navegar, filtrar y explorar datos de forma clara y consistente.

### Entregables
  
Al finalizar, debes tener un archivo .pbix con:
- Informe de tres páginas:
- Visión global
- Detalle operativo
- KPIs Oficina
- Medidas base creadas:
- Total Envíos
- Ingresos Totales
- Peso Total
- Peso Medio
- Intentos Medios
- Entregas Tardías
- % Entrega Tardía
- Envíos a Tiempo
- % Envíos a Tiempo
- Página Visión global con KPIs, tendencia temporal, distribución de SLA y comparativas por oficina y servicio.
- Página Detalle operativo con matriz, gráficos comparativos, segmentaciones e interacciones configuradas.
- Página KPIs Oficina filtrada por una oficina concreta.
- Segmentaciones configuradas para fecha, región, servicio y prioridad.
- Filtros de visual, página e informe revisados desde el panel de filtros.
- Interacciones entre visuales configuradas mediante filtro cruzado.
- Jerarquías utilizadas para exploración en profundidad.
- Diseño visual consistente aplicando ubicación, equilibrio, contraste, proximidad y repetición.

## Resumen
  
En este laboratorio has convertido el modelo semántico de ParcelCraft en un **informe analítico profesional**. Has creado una página de visión global, una página de detalle operativo y una página de KPIs filtrada por una oficina concreta. También has seleccionado objetos visuales adecuados, configurado segmentaciones, usado el panel de filtros, ajustado interacciones entre visuales, trabajado con jerarquías y validado la experiencia de navegación.  
En el siguiente laboratorio podrás ampliar este trabajo incorporando técnicas de seguridad, publicación, colaboración o gobierno del informe según el recorrido definido para ParcelCraft.

##### Recursos útiles
- Documentación oficial de visualizaciones en Power BI: [https://learn.microsoft.com/power-bi/visuals/power-bi-visualization-types-for-reports-and-q-and-a](https://learn.microsoft.com/power-bi/visuals/power-bi-visualization-types-for-reports-and-q-and-a)
- Agregar filtros a informes en Power BI: [https://learn.microsoft.com/power-bi/create-reports/power-bi-report-add-filter](https://learn.microsoft.com/power-bi/create-reports/power-bi-report-add-filter)
- Cambiar la forma en que interactúan los visuales en un informe: [https://learn.microsoft.com/power-bi/create-reports/service-reports-visual-interactions](https://learn.microsoft.com/power-bi/create-reports/service-reports-visual-interactions)
- Exploración en profundidad en Power BI: [https://learn.microsoft.com/power-bi/consumer/end-user-drill](https://learn.microsoft.com/power-bi/consumer/end-user-drill)

##### 🎉 Fin del laboratorio
  
¡Enhorabuena! Has completado la sexta parte del laboratorio de ParcelCraft. Ahora tienes un informe analítico de tres páginas, con diseño consistente, filtros interactivos, exploración en profundidad y una vista preparada para analizar KPIs de una oficina concreta. 👏

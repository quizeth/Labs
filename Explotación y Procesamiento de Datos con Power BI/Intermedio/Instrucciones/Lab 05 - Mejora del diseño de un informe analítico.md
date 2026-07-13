## Laboratorio 5: Mejora de experiencia de usuario y análisis avanzado en Power BI

**ParcelCraft** ya cuenta con un informe analítico completo construido en el laboratorio anterior. El informe incluye tres páginas, un modelo semántico validado en estrella, medidas DAX empresariales y un diseño visual base consistente con el tema corporativo.

En este laboratorio trabajarás como diseñador de experiencia de usuario aplicada a BI. El objetivo es evolucionar el informe existente para que el usuario final pueda explorar la información con menos esfuerzo, acceder a detalles bajo demanda, interpretar indicadores con mayor rapidez y alternar vistas analíticas sin duplicar páginas innecesarias.

El objetivo es pasar de un informe correcto desde el punto de vista visual a una experiencia de análisis interactiva, guiada y preparada para escenarios de autoservicio.

### Objetivos de aprendizaje

**Tiempo estimado total: 30 minutos**

Al finalizar este laboratorio serás capaz de:

1. Configurar una página de obtención de detalles para navegar desde una visualización resumen hacia una vista filtrada automáticamente.
2. Crear páginas de información sobre herramientas personalizadas para mostrar contexto adicional sin cambiar de página.
3. Usar el panel de selección y marcadores para construir vistas alternativas dentro de una misma página.
4. Asociar botones a marcadores para mejorar la navegación y el storytelling del informe.
5. Aplicar formato condicional en matrices o tablas para destacar rendimiento, riesgos y patrones operativos.
6. Reforzar buenas prácticas de UX para reducir carga cognitiva y facilitar el análisis exploratorio.

### Antes de empezar

#### Requisitos

Para completar este laboratorio necesitas:

1. Power BI Desktop instalado.
2. Haber completado el **Laboratorio 4: Diseño de un informe analítico en Power BI**.
3. Partir del archivo .pbix final del Laboratorio 4 de ParcelCraft.
4. Mantener disponibles las páginas **Visión global**, **Detalle operativo** y **KPIs Oficina**.
5. Mantener cargado el tema corporativo **ParcelCraft_Theme**.
6. Mantener disponible el logo de ParcelCraft usado en el laboratorio anterior.
7. Disponer de las medidas **Total envíos**, **Ingresos**, **% margen estimado**, **% envíos entregados**, **% envíos tardíos**, **% envíos no entregados**, **Días medios hasta entrega**, **Promedio intentos** y **Horas medias hasta entrega**.

### Ejercicio 1: Implementar obtención de detalles (Drillthrough)

En este ejercicio crearás una página de detalle para una oficina concreta. Esta página permitirá que el usuario navegue desde una visualización resumen hacia una vista filtrada automáticamente por la oficina seleccionada.

La obtención de detalles debe usarse cuando el usuario necesita profundizar en una entidad concreta sin perder el contexto del análisis. En ParcelCraft, el caso más natural es analizar una oficina desde la página **Visión global** o **Detalle operativo**.

#### Tarea 1: Crear la página de detalle de oficina

1. En la parte inferior de Power BI Desktop, haz clic en el icono **+** para crear una nueva página.
2. Haz doble clic sobre el nombre de la página nueva.
3. Renombra la página como **Detalle oficina**.
4. Haz clic derecho sobre la pestaña **Detalle oficina**.
5. Selecciona **Ocultar página** para evitar que el usuario acceda directamente desde la navegación inferior.
6. Ve a la página **Detalle oficina**.
7. En la pestaña **Ver**, activa **Panel de filtros** si no está visible.

**Resultado esperado:** existe una página oculta que se utilizará únicamente como destino de obtención de detalles.

#### Tarea 2: Configurar el campo de drillthrough

1. Asegúrate de estar en la página **Detalle oficina**.
2. Haz clic en una zona vacía del lienzo para no tener ningún visual seleccionado.
3. En el panel **Visualizaciones**, localiza la sección **Obtención de detalles**.
4. Arrastra el campo `'Oficina'[Oficinas]` al área **Agregar campos de obtención de detalles aquí**.
5. Deja activada la opción **Mantener todos los filtros** para conservar el contexto aplicado desde la página origen.
6. Comprueba que Power BI ha creado automáticamente un botón de regreso en la esquina superior izquierda de la página.
7. Selecciona el botón de regreso y cambia su tamaño para que sea visible pero no dominante.

**Resultado esperado:** la página queda preparada para recibir automáticamente el filtro de una oficina seleccionada en otra página.

#### Tarea 3: Diseñar el encabezado de la página de detalle

1. Copia el logo, el título y el subtítulo desde la página **KPIs Oficina**.
2. Pega los elementos copiados en la página **Detalle oficina**.
3. Cambia el título por **Detalle de oficina**.
4. Selecciona el cuadro de texto del título.
5. En el menú contextual del cuadro de texto, selecciona **+ Valor**.
6. Busca el campo **Oficinas** y selecciona `'Oficina'[Oficinas]` como valor dinámico.
7. Cambia el subtítulo por **Análisis operativo filtrado desde la página origen**.
8. Alinea el encabezado con el mismo margen superior usado en las páginas anteriores.

**Resultado esperado:** la página mantiene el estilo visual de ParcelCraft y comunica claramente que el contenido está filtrado por una oficina.

#### Tarea 4: Crear visuales de análisis para la oficina seleccionada

1. Inserta una visualización de tarjeta debajo del encabezado.
2. Asigna a la tarjeta las medidas **Total envíos**, **Ingresos**, **% envíos entregados** y **% envíos tardíos**.
3. Ajusta la tarjeta para que ocupe el ancho principal del lienzo bajo el encabezado.
4. Inserta un gráfico de columnas agrupadas y de líneas debajo de la tarjeta.
5. Configura el gráfico con `'Fecha'[Mes año]` en **Eje X**, **Total envíos** en **Eje Y** y **% envíos tardíos** en **Eje Y de línea**.
6. Cambia el título del gráfico a **Evolución mensual de la oficina seleccionada** desde **General > Título**.
7. Inserta una tabla a la derecha del gráfico.
8. Configura la tabla con `'Servicio'[Servicio]`, **Total envíos**, **Ingresos**, **% envíos entregados** y **% envíos tardíos**.

**Resultado esperado:** la página permite revisar volumen, ingresos y cumplimiento operativo de la oficina seleccionada.

#### Tarea 5: Probar la obtención de detalles desde Visión global

1. Ve a la página **Visión global**.
2. Coloca el cursor sobre el gráfico de barras de **Ingresos por oficina**.
3. Haz clic derecho sobre una barra correspondiente a una oficina concreta.
4. En el menú contextual, selecciona **Obtención de detalles**.
5. Selecciona la página **Detalle oficina**.
6. Comprueba que la página se abre filtrada por la oficina seleccionada.
7. Haz clic en el botón de regreso para volver a **Visión global**.

**Resultado esperado:** el usuario puede navegar a una página filtrada automáticamente desde una visualización resumen.

### Ejercicio 2: Crear información sobre herramientas personalizada (Tooltips)

En este ejercicio crearás una página de información sobre herramientas para mostrar contexto operativo adicional al pasar el cursor por encima de un visual. Esta técnica mejora la experiencia de usuario porque evita cambios de página y reduce la necesidad de saturar el lienzo principal con demasiados indicadores.

La información sobre herramientas debe aportar contexto relevante y breve. No debe repetir exactamente lo que ya se ve en el visual principal.

#### Tarea 1: Crear la página tooltip

1. Haz clic en el icono **+** para crear una nueva página.
2. Renombra la página como **Tooltip oficina**.
3. Haz clic en una zona vacía del lienzo.
4. Abre el panel **Formato** de la página.
5. En **Información de página**, activa la opción **Permitir usar como información sobre herramientas**.
6. En **Configuración del lienzo**, cambia **Tipo** a **Información sobre herramientas**.
7. Haz clic derecho sobre la pestaña **Tooltip oficina**.
8. Selecciona **Ocultar página**.

**Resultado esperado:** existe una página oculta configurada como tooltip personalizado.

#### Tarea 2: Configurar el tamaño y el diseño base del tooltip

1. Asegúrate de estar en la página **Tooltip oficina**.
2. Haz clic en una zona vacía del lienzo.
3. En el panel **Formato**, abre **Configuración del lienzo**.
4. Comprueba que el tamaño corresponde a **Información sobre herramientas**.
5. En **Fondo de página**, establece el color **Blanco**.
6. En **Efectos**, activa **Fondo** si necesitas reforzar la legibilidad sobre visuales oscuros.
7. Inserta un cuadro de texto en la parte superior.
8. Escribe el título **Contexto operativo**.

**Resultado esperado:** la página tooltip tiene un tamaño compacto y una estructura legible para mostrarse al pasar el cursor.

#### Tarea 3: Añadir KPIs relevantes al tooltip

1. Inserta una visualización de tarjeta debajo del título.
2. Asigna a la tarjeta las medidas **Total envíos**, **Ingresos**, **% envíos entregados** y **% envíos tardíos**.
3. Reduce el tamaño de fuente de los valores desde **Objeto visual > Llamada > Valor** para que quepan en el lienzo.
4. Inserta una matriz debajo de la tarjeta.
5. Configura la matriz con `'Servicio'[Servicio]` en **Filas**.
6. Añade **Total envíos**, **% envíos entregados** y **% envíos tardíos** a **Valores**.
7. Desactiva el título de la matriz desde **General > Título**.
8. Ajusta la matriz para que no aparezca desplazamiento horizontal.

**Resultado esperado:** el tooltip muestra indicadores de contexto y una lectura rápida por servicio.

#### Tarea 4: Asociar el tooltip al gráfico de oficinas

1. Ve a la página **Visión global**.
2. Selecciona el gráfico de barras de **Ingresos por oficina**.
3. Abre el panel **Formato** del objeto visual.
4. Ve a **General > Información sobre herramientas**.
5. Cambia **Tipo** a **Página de informe**.
6. En **Página**, selecciona **Tooltip oficina**.
7. Pasa el cursor sobre una barra del gráfico.
8. Comprueba que se muestra el tooltip personalizado con los datos de la oficina sobre la que está situado el cursor.

**Resultado esperado:** el usuario obtiene contexto adicional sin cambiar de página.

### Ejercicio 3: Usar marcadores para navegación y storytelling

En este ejercicio crearás vistas alternativas dentro de la página **Detalle operativo**. El usuario podrá alternar entre una vista matricial y una vista gráfica mediante botones conectados a marcadores.

Esta técnica evita crear páginas duplicadas y ayuda a construir una experiencia de storytelling en la que el usuario decide cómo quiere consumir la información.

#### Tarea 1: Preparar el panel de selección y la vista base

1. Ve a la página **Detalle operativo**.
2. En la pestaña **Ver**, activa **Selección**.
3. En la pestaña **Ver**, activa **Marcadores**.
4. Selecciona la matriz operativa existente.
5. En el panel **Selección**, renombra la matriz como **Matriz detalle operativo**.
6. Ajusta la matriz para que ocupe la zona central principal del lienzo.
7. Comprueba que el encabezado y las segmentaciones quedan siempre visibles.

**Resultado esperado:** la página queda preparada para controlar la visibilidad de objetos mediante el panel de selección.

#### Tarea 2: Crear una vista gráfica alternativa

1. Inserta un gráfico de barras agrupadas en la misma zona que ocupa la matriz.
2. Configura el gráfico con `'Oficina'[Oficinas]` en **Eje Y** e **Ingresos** en **Eje X**.
3. Añade **% margen estimado** a **Información sobre herramientas**.
4. Cambia el título del gráfico a **Ingresos por oficina** desde **General > Título**.
5. Inserta un gráfico de columnas agrupadas a la derecha del gráfico de barras.
6. Configura el gráfico de columnas con `'Servicio'[Servicio]` en **Eje X** y **Total envíos** en **Eje Y**.
7. Cambia el título del gráfico de columnas a **Volumen por servicio**.
8. En el panel **Selección**, renombra los dos gráficos como **Vista grafica ingresos** y **Vista grafica servicios**.

**Resultado esperado:** existe una alternativa visual que permite analizar la misma página desde una perspectiva más gráfica.

#### Tarea 3: Crear marcadores para alternar vistas

1. En el panel **Selección**, deja visible **Matriz detalle operativo**.
2. En el panel **Selección**, oculta **Vista grafica ingresos** y **Vista grafica servicios** usando el icono del ojo.
3. En el panel **Marcadores**, selecciona **Agregar**.
4. Renombra el marcador como **Vista matriz**.
5. En el panel **Selección**, oculta **Matriz detalle operativo**.
6. En el panel **Selección**, muestra **Vista grafica ingresos** y **Vista grafica servicios**.
7. En el panel **Marcadores**, selecciona **Agregar**.
8. Renombra el marcador como **Vista graficos**.

**Resultado esperado:** hay dos marcadores que capturan estados visuales alternativos de la misma página.

#### Tarea 4: Ajustar las propiedades de los marcadores

1. En el panel **Marcadores**, abre las opciones del marcador **Vista matriz**.
2. Comprueba que la opción **Mostrar** está activada.
3. Desactiva la opción **Datos** para evitar que el marcador sobrescriba segmentaciones o filtros aplicados por el usuario.
4. Repite la misma configuración para el marcador **Vista graficos**.
5. Selecciona **Vista matriz**.
6. Verifica que solo se muestra la matriz.
7. Selecciona **Vista graficos**.
8. Verifica que solo se muestran los gráficos alternativos.

**Resultado esperado:** los marcadores cambian la vista sin modificar el contexto analítico elegido por el usuario.

#### Tarea 5: Crear botones de navegación entre vistas

1. Ve a **Insertar > Botones > En blanco**.
2. Coloca el botón debajo del encabezado y escribe el texto **Ver matriz**.
3. Con el botón seleccionado, activa **Acción** en el panel **Formato**.
4. Cambia **Tipo** a **Marcador**.
5. En **Marcador**, selecciona **Vista matriz**.
6. Duplica el botón y cambia el texto a **Ver gráficos**.
7. En el botón duplicado, cambia el marcador asociado a **Vista graficos**.
8. Prueba ambos botones con **Ctrl + clic** desde Power BI Desktop.

**Resultado esperado:** el usuario puede cambiar vistas dinámicamente mediante botones claros y consistentes.

### Ejercicio 4: Aplicar formato condicional

En este ejercicio aplicarás formato condicional a la matriz operativa para destacar rápidamente oficinas o servicios con mayor rendimiento, menor cumplimiento o mayor riesgo operativo.

El formato condicional debe utilizarse para ayudar a interpretar datos, no para decorar. En este laboratorio se aplicará sobre indicadores que ya tienen significado de negocio.

#### Tarea 1: Aplicar reglas de color al margen estimado

1. Ve a la página **Detalle operativo**.
2. Activa el marcador **Vista matriz** si la matriz no está visible.
3. Selecciona la matriz **Matriz detalle operativo**.
4. En el panel **Visualizaciones**, localiza el campo **% margen estimado** dentro de **Valores**.
5. Abre el menú del campo **% margen estimado**.
6. Selecciona **Formato condicional**.
7. Selecciona **Color de fondo**.
8. Configura reglas con rojo claro para valores inferiores a 0,15, amarillo claro para valores entre 0,15 y 0,25, y verde claro para valores superiores a 0,25.

**Resultado esperado:** la matriz destaca visualmente oficinas y servicios con margen bajo, medio o alto.

#### Tarea 2: Aplicar iconos al cumplimiento de entregas

1. Mantén seleccionada la matriz **Matriz detalle operativo**.
2. Arrastra **% envíos entregados** al área **Valores** si todavía no aparece en la matriz.
3. En el panel **Visualizaciones**, localiza el campo **% envíos entregados** dentro de **Valores**.
4. Abre el menú del campo **% envíos entregados**.
5. Selecciona **Formato condicional**.
6. Selecciona **Iconos**.
7. Configura un icono rojo descendente para valores inferiores a 0,90 y un icono amarillo horizontal para valores entre 0,90 y 0,96.
8. Configura un icono verde ascendente para valores iguales o superiores a 0,96.

**Resultado esperado:** el usuario identifica rápidamente oficinas y servicios con menor cumplimiento operativo.

#### Tarea 3: Aplicar barras de datos a ingresos

1. Mantén seleccionada la matriz **Matriz detalle operativo**.
2. En el panel **Visualizaciones**, localiza el campo **Ingresos** dentro de **Valores**.
3. Abre el menú del campo **Ingresos**.
4. Selecciona **Formato condicional**.
5. Selecciona **Barras de datos**.
6. Activa la opción **Mostrar solo barra** si quieres una lectura visual compacta.
7. Define el color de barra como **#FFCC00** para mantener coherencia con el tema de ParcelCraft.
8. Acepta la configuración y revisa que las barras permiten comparar magnitudes relativas.

**Resultado esperado:** los ingresos se comparan de forma visual sin necesidad de interpretar cada cifra de forma aislada.

#### Tarea 4: Revisar la legibilidad del formato condicional

1. Ordena la matriz por **% envíos entregados** de menor a mayor.
2. Revisa que los iconos permiten detectar los casos más críticos sin leer todos los valores.
3. Ordena la matriz por **Ingresos** de mayor a menor.
4. Revisa que las barras de datos mantienen una lectura clara del ranking económico.
5. Vuelve a ordenar la matriz por **Oficina** si prefieres mantener una lectura alfabética.
6. Comprueba que los colores de fondo no dificultan la lectura de los números.
7. Ajusta la intensidad de los colores si algún valor pierde contraste.

**Resultado esperado:** el usuario identifica patrones, rendimiento y riesgos operativos de forma rápida y consistente.

### Entregables

Al finalizar, debes tener un archivo .pbix con:

1. Las tres páginas principales del informe inicial: **Visión global**, **Detalle operativo** y **KPIs Oficina**.
2. Una página oculta llamada **Detalle oficina** configurada como destino de obtención de detalles.
3. Una página oculta llamada **Tooltip oficina** configurada como información sobre herramientas personalizada.
4. Botones en **Detalle operativo** para alternar entre **Vista matriz** y **Vista gráficos**.
5. Marcadores configurados para cambiar vistas sin sobrescribir filtros ni segmentaciones del usuario.
6. Formato condicional aplicado en la matriz operativa mediante reglas de color, iconos y barras de datos.
7. Diseño visual consistente con el tema, logo, tipografía, espaciado y jerarquía visual de ParcelCraft.

### Resumen

En este laboratorio has mejorado el informe analítico de ParcelCraft incorporando funcionalidades avanzadas de interacción y análisis exploratorio.

Has creado una página de **Detalle oficina** con obtención de detalles para que el usuario pueda profundizar desde una vista resumen hacia una vista filtrada automáticamente. También has diseñado un **tooltip personalizado** para ofrecer contexto adicional sin obligar al usuario a abandonar la página actual.

Después, has utilizado el **panel de selección**, **marcadores** y **botones** para construir vistas alternativas dentro de **Detalle operativo**, reforzando el storytelling y reduciendo la necesidad de duplicar páginas. Finalmente, has aplicado **formato condicional** para destacar patrones de margen, cumplimiento e ingresos en la matriz operativa.

El impacto principal sobre la UX es una experiencia más fluida, más guiada y menos cargada visualmente. El impacto principal sobre el análisis es una mayor capacidad de exploración, detección de excepciones y comprensión rápida del rendimiento operativo de ParcelCraft.

### Recursos adicionales

1. Obtención de detalles en informes de Power BI: https://learn.microsoft.com/power-bi/create-reports/desktop-drillthrough
2. Crear información sobre herramientas basada en páginas de informe: https://learn.microsoft.com/power-bi/create-reports/desktop-tooltips
3. Crear marcadores en Power BI: https://learn.microsoft.com/power-bi/create-reports/desktop-bookmarks
4. Crear botones en informes de Power BI: https://learn.microsoft.com/power-bi/create-reports/desktop-buttons
5. Panel de selección en Power BI: https://learn.microsoft.com/power-bi/create-reports/power-bi-report-add-visualizations#selection-pane
6. Formato condicional en tablas y matrices: https://learn.microsoft.com/power-bi/create-reports/desktop-conditional-table-formatting
7. Guía de diseño de informes en Power BI: https://learn.microsoft.com/power-bi/create-reports/desktop-report-design

#### 🎉 Fin del laboratorio

¡Enhorabuena! Has completado el Laboratorio 5 de ParcelCraft. Ahora tienes un informe más interactivo, más intuitivo y mejor preparado para usuarios de negocio que necesitan explorar información, detectar patrones y profundizar en el rendimiento operativo sin perder contexto. 👏

# Laboratorio 7: Mejora del diseño de un informe analítico

En este laboratorio trabajarás como diseñador de experiencia de usuario aplicada a BI. El objetivo es evolucionar el informe existente para que el usuario final pueda explorar la información con menos esfuerzo, acceder a detalles bajo demanda, interpretar indicadores con mayor rapidez y alternar vistas analíticas sin duplicar páginas innecesarias.

El objetivo es pasar de un informe correcto desde el punto de vista visual a una experiencia de análisis interactiva, guiada y preparada para escenarios de autoservicio.

## Objetivos de aprendizaje

**Tiempo estimado total: 30 minutos**

Al finalizar este laboratorio serás capaz de:
- Configurar una página de obtención de detalles para navegar desde una visualización resumen hacia una vista filtrada automáticamente.
- Crear páginas de información sobre herramientas personalizadas para mostrar contexto adicional sin cambiar de página.
- Usar el panel de selección y marcadores para construir vistas alternativas dentro de una misma página.
- Asociar botones a marcadores para mejorar la navegación y el storytelling del informe.
- Aplicar formato condicional en matrices o tablas para destacar rendimiento, riesgos y patrones operativos.
- Reforzar buenas prácticas de UX para reducir carga cognitiva y facilitar el análisis exploratorio.

---

## Antes de empezar

### Requisitos

Para completar este laboratorio necesitas:

1. Power BI Desktop instalado.
2. Haber completado el **Laboratorio 4: Diseño de un informe analítico en Power BI** o partir del archivo inicial.
4. Mantener disponibles las páginas **Visión global**, **Detalle operativo** y **KPIs Oficina**.

> Tienes un archivo inicial y solución disponibles.
> 
> [Archivo inicial (Starter)](https://github.com/quizeth/Labs/blob/main/Explotaci%C3%B3n%20y%20Procesamiento%20de%20Datos%20con%20Power%20BI/Intermedio/Files/05%20Starter%20ParcelCraft.pbix)
> 
> [Archivo final (Solución)](https://github.com/quizeth/Labs/blob/main/Explotaci%C3%B3n%20y%20Procesamiento%20de%20Datos%20con%20Power%20BI/Intermedio/Files/05%20Soluci%C3%B3n%20ParcelCraft.pbix)

---

## Ejercicio 1: Implementar obtención de detalles (Drillthrough)

En este ejercicio crearás una página de detalle para una oficina concreta. Esta página permitirá que el usuario navegue desde una visualización resumen hacia una vista filtrada automáticamente por la oficina seleccionada.

La obtención de detalles debe usarse cuando el usuario necesita profundizar en una entidad concreta sin perder el contexto del análisis. En ParcelCraft, el caso más natural es analizar una oficina desde la página **Visión global** o **Detalle operativo**.

### Tarea 1: Crear la página de detalle de oficina

1. En la parte inferior de Power BI Desktop, haz clic en el icono **+** para crear una nueva página.
2. Haz doble clic sobre el nombre de la página nueva.
3. Renombra la página como **Detalle región**.
4. Haz clic derecho sobre la pestaña **Detalle región**.
5. Selecciona **Ocultar página** para evitar que el usuario acceda directamente desde la navegación inferior.
6. Ve a la página **Detalle región**.
7. En la pestaña **Ver**, activa **Panel de filtros** si no está visible.

**Resultado esperado:** existe una página oculta que se utilizará únicamente como destino de obtención de detalles.

### Tarea 2: Configurar el campo de drillthrough

1. Asegúrate de estar en la página **Detalle región**.
2. Haz clic en una zona vacía del lienzo para no tener ningún visual seleccionado.
3. En el panel **Visualizaciones**, localiza la sección **Obtención de detalles**.
4. Arrastra el campo `'Oficina'[Región]` al área **Agregar campos de obtención de detalles aquí**.
5. Deja activada la opción **Mantener todos los filtros** para conservar el contexto aplicado desde la página origen.
6. Comprueba que Power BI ha creado automáticamente un botón de regreso en la esquina superior izquierda de la página.
7. Selecciona el botón de regreso y cambia su tamaño para que sea visible pero no dominante.
8. En el panel de formato, ve a **General > Efectos** y deshabilita el **Fondo**.

**Resultado esperado:** la página queda preparada para recibir automáticamente el filtro de una oficina seleccionada en otra página.

### Tarea 3: Diseñar el encabezado de la página de detalle

1. Copia el logo y el título desde la página **KPIs Oficina**.
2. Pega los elementos copiados en la página **Detalle región**.
3. Cambia el título por `Detalle de región`.
4. Selecciona el cuadro de texto del título.
5. En el menú contextual del cuadro de texto, selecciona **+ Valor**.
6. Busca el campo **Región** y selecciona `'Oficina'[Región]` como valor dinámico.
7. Cambia el subtítulo por `Análisis operativo filtrado desde la página origen`.
8. Alinea el encabezado con el mismo margen superior usado en las páginas anteriores.

**Resultado esperado:** la página mantiene el estilo visual de ParcelCraft y comunica claramente que el contenido está filtrado por una oficina.

### Tarea 4: Crear visuales de análisis para la oficina seleccionada

1. Inserta una visualización de tarjeta debajo del encabezado.
2. Asigna a la tarjeta las medidas `Total envíos`, `Ingresos`, `% envíos no entregados` y `% envíos tardíos`.
3. Ajusta la tarjeta para que ocupe el ancho principal del lienzo bajo el encabezado.
4. Inserta una tabla debajo de la tarjeta
5. Configura la tabla con los siguientes campos:
    - `'Servicio'[Servicio]`
    - `Total envíos`
    - `Ingresos`
    - `% envíos entregados`
    - `% envíos no entregados`
    - `% envíos tardíos`
    - `Días medios hasta entrega`
    - `Promedio intentos`
    - `% margen estimado`

**Resultado esperado:** la página permite revisar volumen, ingresos y cumplimiento operativo de la oficina seleccionada.

<img width="1554" height="882" alt="image" src="https://github.com/user-attachments/assets/67ea51ff-1d30-4c2d-bdeb-79d0c1f7d43b" />


### Tarea 5: Probar la obtención de detalles desde Visión global

1. Ve a la página **Visión global**.
2. Coloca el cursor sobre el gráfico de barras de **Ingresos por oficina**.
3. Haz clic derecho sobre una barra correspondiente a una oficina concreta.
4. En el menú contextual, selecciona **Obtención de detalles**.
5. Selecciona la página **Detalle oficina**.
6. Comprueba que la página se abre filtrada por la oficina seleccionada.
7. Haz clic en el botón de regreso para volver a **Visión global**.

**Resultado esperado:** el usuario puede navegar a una página filtrada automáticamente desde una visualización resumen.

## Ejercicio 2: Crear información sobre herramientas personalizada (Tooltips)

En este ejercicio crearás una página de información sobre herramientas para mostrar contexto operativo adicional al pasar el cursor por encima de un visual. Esta técnica mejora la experiencia de usuario porque evita cambios de página y reduce la necesidad de saturar el lienzo principal con demasiados indicadores.

La información sobre herramientas debe aportar contexto relevante y breve. No debe repetir exactamente lo que ya se ve en el visual principal.

### Tarea 1: Crear la página tooltip

1. Haz clic en el icono **+** para crear una nueva página.
2. Renombra la página como **Tooltip envíos**.
3. Haz clic en una zona vacía del lienzo.
4. Abre el panel **Formato** de la página.
5. En **Información de página**, activa la opción **Permitir usar como información sobre herramientas**.
6. En **Configuración del lienzo**, cambia **Tipo** a **Información sobre herramientas**.
7. Haz clic derecho sobre la pestaña **Tooltip oficina**.
8. Selecciona **Ocultar página**.

**Resultado esperado:** existe una página oculta configurada como tooltip personalizado.

### Tarea 2: Configurar el tamaño y el diseño base del tooltip

1. Asegúrate de estar en la página **Tooltip oficina**.
2. Haz clic en una zona vacía del lienzo.
3. En el panel **Formato**, abre **Configuración del lienzo**.
4. Comprueba que el tamaño corresponde a **Información sobre herramientas**.
5. En **Fondo del lienzo**, establece el color **Blanco**.
6. Inserta un cuadro de texto en la parte superior.
7. Escribe el título **Contexto operativo** y configúralo:
   - **Fuente:** Segoe UI Semibold
   - **Alineamiento**: Centro

**Resultado esperado:** la página tooltip tiene un tamaño compacto y una estructura legible para mostrarse al pasar el cursor.

### Tarea 3: Añadir KPIs relevantes al tooltip

1. Inserta una visualización de tarjeta debajo del título.
2. Asigna a la tarjeta las medidas `Total envíos` y `Margen medio por envío`.
3. Reduce el tamaño de fuente de los valores desde **Objeto visual > Llamada > Valor** a **12** para que quepan en el lienzo.
4. Reduce el tamaño de fuente de los valores desde **Objeto visual > Llamada > Etiqueta** a **8** para que quepan en el lienzo.
5. Reduce el espaciado de los márgenes desde **Objeto visual > Diseño de múltiples tarjetas > Presentación > Espaciado** y establece el relleno uniforme en **0 px**.
7. Inserta un **gráfico de treemap** debajo de la tarjeta.
8. Añade campos al gráfico:
   - **Categoría**: Oficina
   - **Valores**: Valores
9. Configura el gráfico:
    - Desactiva el título de la matriz desde **General > Título**.
    - Desactiva la leyenda desde **Objeto visual > Leyenda**.
    - Activa las etiquetas de datos desde **Objeto visual > Etiquetas de datos**.
10. En el panel de filtros, configura la tarjeta **Oficina** y selecciona **Aplicar filtro**:
    - **Tipo de filtro:** Top N
    - **Mostrar artículos**: Superior 3
    - **Por valor:** Total envíos

**Resultado esperado:** el tooltip muestra indicadores de contexto y una lectura rápida por servicio.

<img width="679" height="518" alt="image" src="https://github.com/user-attachments/assets/c6c4ed44-49f5-45c1-ae60-86025686b029" />


### Tarea 4: Asociar el tooltip al gráfico de oficinas

1. Asegúrate de estar en la página **Tooltip envíos**.
2. Haz clic en una zona vacía del lienzo para no tener ningún visual seleccionado.
3. En el panel **Visualizaciones**, localiza la sección **Información sobre erramientas**.
4. Arrastra el campo `Total envíos` al área **Agregar campos de obtención de detalles aquí**.
5. Deja activada la opción **Mantener todos los filtros** para conservar el contexto aplicado desde la página origen.
6. Ve a la página **Visión global**.
7. Pasa el cursor por encima de una barra del gráfico de barras de **Ingresos por región**.
8. Comprueba que se muestra el tooltip personalizado con los datos de la oficina sobre la que está situado el cursor.

**Resultado esperado:** el usuario obtiene contexto adicional sin cambiar de página.

<img width="1299" height="738" alt="image" src="https://github.com/user-attachments/assets/bf89d5d9-6f95-4bc3-b710-a13f1bdf2d8e" />


## Ejercicio 3: Usar marcadores para navegación y storytelling

En este ejercicio crearás vistas alternativas dentro de la página **Detalle operativo**. El usuario podrá alternar mostrar y ocultar un panel flotante de segmentaciones a través de botones conectados a marcadores.

Esta técnica evita crear páginas duplicadas y ayuda a construir una experiencia de storytelling en la que el usuario decide cómo quiere consumir la información.

### Tarea 1: Preparar el panel de selección y la vista base

1. Ve a la página **Detalle operativo**.
2. En la pestaña **Ver**, activa **Selección**.
3. En la pestaña **Ver**, activa **Marcadores**.
4. Selecciona la matriz operativa existente.
5. En el panel **Selección**, renombra la matriz como **Matriz detalle operativo**.
6. Comprueba que la matriz ocupa la zona central principal del lienzo.
7. Comprueba que el encabezado, el menú lateral y las segmentaciones superiores **Año** y **Servicio** quedan siempre visibles.

**Resultado esperado:** la página queda preparada para controlar la visibilidad de un panel flotante de segmentaciones mediante el panel de selección.

### Tarea 2: Crear un panel flotante de segmentaciones

1. Inserta una forma de **pestaña redondeada** en la zona central de la página, encima de la matriz.
2. Configura la forma en **Forma > Forma**:
     - **Forma**: Pestaña redondeada
     - **Esquinas redondeadas (px)**: 25
     - **Esquina superior derecha:** 0
4. Cambia el color de relleno en **Estilo > Rellenar** al azul corporativo **#003A8F, color del tema 1**.
5. En el panel **Selección**, renombra el rectángulo como **Panel fondo segmentaciones**.
6. Inserta seis segmentaciones dentro del rectángulo, distribuidas en dos filas y tres columnas.
7. Configura las segmentaciones con los campos **Oficina**, **Ciudad**, **Región**, **Tipo de cliente**, **Segmento de servicio** y **Rango de peso**.
8. Cambia el estilo de las segmentaciones para que tengan fondo blanco y formato de menú desplegable.
9. En el panel **Selección**, selecciona todas las segmentaciones y la forma manteniendo **Ctrl**.
10. Haz clic derecho en la selección y agrupa las segmentaciones en **Agrupar > Agrupar**.
11. Renombra el grupo como **Panel de segmentaciones**.

**Resultado esperado:** existe un panel flotante con un listado de segmentaciones que se muestra sobre la matriz sin cambiar la estructura principal de la página.

<img width="1633" height="727" alt="image" src="https://github.com/user-attachments/assets/1c138a52-24cb-47cd-8374-d4a67c275846" />


### Tarea 3: Añadir botones dentro del panel de segmentaciones

1. Ve a **Insertar > Botones > Aplicar todas las segmentaciones**.
2. Coloca el botón en la parte inferior central del panel flotante.
3. Añade un segundo botón **Insertar > Botones > Borrar todas las segmentaciones**.
7. En el panel **Selección**, añade los dos botones al grupo **Panel de segmentaciones**.

**Resultado esperado:** el panel de segmentaciones incluye controles claros para aplicar o borrar las selecciones realizadas por el usuario.

### Tarea 4: Crear marcadores para mostrar u ocultar el panel de segmentaciones

1. En el panel **Selección**, oculta el grupo **Panel de  segmentaciones** usando el icono del ojo.
3. En el panel **Marcadores**, selecciona **Agregar**.
4. Renombra el marcador como **Panel filtros oculto**.
5. En el panel **Selección**, muestra el grupo **Panel de  segmentaciones** usando el icono del ojo.
6. En el panel **Marcadores**, selecciona **Agregar**.
7. Renombra el marcador como **Panel filtros visible**.

**Resultado esperado:** hay dos marcadores que capturan los estados de visibilidad del panel flotante de segmentaciones.

<img width="1816" height="723" alt="image" src="https://github.com/user-attachments/assets/b1e6e02a-0ce1-4e81-94c0-2b8eac3a1ecf" />


### Tarea 5: Ajustar las propiedades de los marcadores

1. En el panel **Marcadores**, abre las opciones del marcador **Panel filtros oculto**.
2. Comprueba que la opción **Mostrar** está activada.
3. Desactiva la opción **Datos** para evitar que el marcador sobrescriba segmentaciones o filtros aplicados por el usuario.
4. Repite la misma configuración para el marcador **Panel filtros visible**.
5. Selecciona **Panel filtros oculto**.
6. Verifica que solo se muestra la matriz, junto con el encabezado, el menú lateral y las segmentaciones superiores.
7. Selecciona **Panel filtros visible**.
8. Verifica que el panel azul de segmentaciones aparece sobre la matriz sin ocultar el encabezado ni el menú lateral.

**Resultado esperado:** los marcadores muestran u ocultan el panel de segmentaciones sin modificar el contexto analítico elegido por el usuario.

### Tarea 6: Crear la imagen para abrir el panel de segmentaciones

1. Ve a **Insertar > Imagen**.
2. Selecciona la imagen **Icono botón filtros.png**.
3. Coloca la imagen en la esquina superior derecha de la página, junto a las segmentaciones superiores.
4. Ajusta el tamaño de la imagen para que funcione como icono de filtro y mantenga una proporción visual consistente con el diseño del informe.
5. Con la imagen seleccionada, activa **Acción** en el panel **Formato**.
6. Cambia **Tipo** a **Marcador**.
7. En **Marcador**, selecciona **Panel filtros visible**.
8. En el panel **Selección**, renombra la imagen como **Icono abrir filtros**.
9. Prueba la imagen con **Ctrl + clic** desde Power BI Desktop.

**Resultado esperado:** el usuario puede abrir el panel flotante de segmentaciones pulsando sobre la imagen del icono de filtro, integrada visualmente con el diseño del informe.

### Tarea 7: Crear la imagen para cerrar el panel de segmentaciones

1. Duplica la imagen **Icono abrir filtros**.
2. Coloca la imagen duplicada dentro del panel flotante, en la esquina superior derecha.
3. Ajusta el tamaño y la posición para que funcione como control de cierre del panel.
4. Con la imagen seleccionada, activa **Acción** en el panel **Formato**.
5. Cambia **Tipo** a **Marcador**.
6. En **Marcador**, selecciona **Panel filtros oculto**.
7. En el panel **Selección**, renombra la imagen como **Icono cerrar filtros**.
8. Asegúrate de que **Icono cerrar filtros** solo está visible cuando el panel de segmentaciones está visible.
9. Prueba las imágenes **Icono abrir filtros** e **Icono cerrar filtros** con **Ctrl + clic** desde Power BI Desktop.

**Resultado esperado:** el usuario puede abrir y cerrar el panel flotante de segmentaciones usando la imagen del icono de filtro como elemento interactivo, manteniendo siempre visible la vista principal del informe.### Tarea 6: Crear la imagen para abrir el panel de segmentaciones

1. Ve a **Insertar > Imagen**.
2. Selecciona la imagen **Icono botón filtros.png**.
3. Coloca la imagen en la esquina superior derecha de la página, junto a las segmentaciones superiores.
4. Ajusta el tamaño de la imagen para que funcione como icono de filtro y mantenga una proporción visual consistente con el diseño del informe.
5. Elimina el fondo de la imagen en **General > Fondo**.
6. Con la imagen seleccionada, activa **Acción** en el panel **Formato**.
7. Cambia **Tipo** a **Marcador**.
8. En **Marcador**, selecciona **Panel filtros visible**.
9. En el panel **Selección**, renombra la imagen como `Icono abrir filtros`.
10. Oculta el título de la imagen en **General > Título**.
11. Prueba la imagen con **Ctrl + clic** desde Power BI Desktop.

**Resultado esperado:** el usuario puede abrir el panel flotante de segmentaciones pulsando sobre la imagen del icono de filtro, integrada visualmente con el diseño del informe.

### Tarea 7: Crear la imagen para cerrar el panel de segmentaciones

1. Duplica la imagen **Icono abrir filtros**.
2. Coloca la imagen duplicada encima de la original.
3. Renombra la imagen como **Icono cerrar filtros**.
4. Con la imagen seleccionada, activa **Acción** en el panel **Formato**.
5. Cambia **Tipo** a **Marcador**.
6. En **Marcador**, selecciona **Panel filtros oculto**.
7. En el panel **Selección**, renombra la imagen como **Icono cerrar filtros**.
8. Selecciona el marcador **Panel filtros oculto** y oculta el botón **Icono cerrar filtros**.
9. Guarda los cambios en el marcador haciendo clic derecho en **Panel filtros oculto > Actualizar**
10. Repite los pasos para mostrar el **Icono cerrar filtros** y ocultar el de **abrir filtros** en el otro marcador.
11. Asegúrate de que **Icono cerrar filtros** solo está visible cuando el panel de segmentaciones está visible.
12. Prueba los botones **Icono abrir filtros** e **Icono cerrar filtros** con **Ctrl + clic** desde Power BI Desktop.

**Resultado esperado:** el usuario puede abrir y cerrar el panel flotante de segmentaciones usando la imagen del icono de filtro como elemento interactivo, manteniendo siempre visible la vista principal del informe.

<img width="1792" height="749" alt="image" src="https://github.com/user-attachments/assets/3c7a42cf-7389-4fe5-ad19-3acc150272e4" />


## Ejercicio 4: Aplicar formato condicional

En este ejercicio aplicarás formato condicional a la matriz operativa para destacar rápidamente oficinas o servicios con mayor rendimiento, menor cumplimiento o mayor riesgo operativo.

El formato condicional debe utilizarse para ayudar a interpretar datos, no para decorar. En este laboratorio se aplicará sobre indicadores que ya tienen significado de negocio.

### Tarea 1: Aplicar reglas de color al margen estimado

1. Ve a la página **Detalle operativo**.
2. Selecciona la matriz **Matriz detalle operativo**.
3. En el panel **Visualizaciones**, localiza el campo **% margen estimado** dentro de **Valores**.
5. Abre el menú del campo **% margen estimado**.
6. Selecciona **Formato condicional**.
7. Selecciona **Iconos**.
8. Configura las reglas:
<img width="949" height="672" alt="image" src="https://github.com/user-attachments/assets/f647cce2-b78b-409d-a18e-c51ab4b2454e" />


**Resultado esperado:** la matriz destaca visualmente oficinas y servicios con margen bajo, medio o alto.

<img width="1299" height="734" alt="image" src="https://github.com/user-attachments/assets/f61dd01a-25fa-446c-ab4d-9590eef1015a" />


### Tarea 2: Aplicar barras de datos a ingresos

1. Mantén seleccionada la matriz **Matriz detalle operativo**.
2. En el panel **Visualizaciones**, localiza el campo **Ingresos** dentro de **Valores**.
3. Abre el menú del campo **Ingresos**.
4. Selecciona **Formato condicional**.
5. Selecciona **Barras de datos**.
6. Configura el formato condicional:
   <img width="950" height="675" alt="image" src="https://github.com/user-attachments/assets/0d447893-1d78-4cc5-b3dd-9bccf43d298b" />


**Resultado esperado:** los ingresos se comparan de forma visual sin necesidad de interpretar cada cifra de forma aislada.

#### Tarea 2: Revisar la legibilidad del formato condicional

1. Ordena la matriz por **% margen estimado** de menor a mayor.
2. Revisa que los iconos permiten detectar los casos más críticos sin leer todos los valores.
3. Ordena la matriz por **Ingresos** de mayor a menor.
4. Revisa que las barras de datos mantienen una lectura clara del ranking económico.
5. Vuelve a ordenar la matriz por **Región** si prefieres mantener una lectura alfabética.
6. Comprueba que los colores de fondo no dificultan la lectura de los números.
7. Ajusta la intensidad de los colores si algún valor pierde contraste.

**Resultado esperado:** el usuario identifica patrones, rendimiento y riesgos operativos de forma rápida y consistente.

---

## Entregables

Al finalizar, debes tener un archivo .pbix con:

1. Las tres páginas principales del informe inicial: **Visión global**, **Detalle operativo** y **KPIs Oficina**.
2. Una página oculta llamada **Detalle región** configurada como destino de obtención de detalles.
3. Una página oculta llamada **Tooltip oficina** configurada como información sobre herramientas personalizada.
4. Botones en **Detalle operativo** para alternar entre **Vista matriz** y **Vista gráficos**.
5. Marcadores configurados para cambiar vistas sin sobrescribir filtros ni segmentaciones del usuario.
6. Formato condicional aplicado en la matriz operativa mediante reglas de color, iconos y barras de datos.
7. Diseño visual consistente con el tema, logo, tipografía, espaciado y jerarquía visual de ParcelCraft.

---

## Resumen

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

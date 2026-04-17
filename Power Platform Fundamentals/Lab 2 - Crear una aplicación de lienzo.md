# Laboratorio 2 - Crear una aplicación de lienzo en Power Apps

> [!IMPORTANT]
> Para hacer este laboratorio, es necesario haber completado el [Lab 0 - Configuración del entorno del laboratorio](https://github.com/quizeth/Labs/blob/main/Power%20Platform%20Fundamentals/Lab%200%20-%20Configuración%20del%20entorno.md?plain=1), INCLUYENDO la tarea **Tarea 2.3 - Importar una solución**.

> [!NOTE]
> Puedes descargar la solución de este ejercicio (sin datos) en la carpeta de Files: [EventManagement_1_0_0_1.zip](https://github.com/quizeth/Labs/blob/4607d0059627f14e1e45c5ddd92105701c50e2ea/Power%20Platform%20Fundamentals/Files/EventManagement_1_0_0_1.zip)

## Escenario
En este ejercicio, crearás una aplicación de lienzo para que los empleados puedan administrar los eventos y contactos de MS Learn.

Seguirás el siguiente plan para diseñar la aplicación de lienzo:
   - Crearás una aplicación de lienzo a partir de datos en la tabla Events.
   - Configurarás la visualización de eventos en la pantalla de navegación.
   - Realizarás cambios básicos en la aplicación.
   - Añadirás datos de la tabla Contacts a la aplicación.
   - Probarás la funcionalidad de la aplicación.


## Ejercicio 1: Crear una aplicación de lienzo

### Tarea 1.1 - Crear la aplicación
En este ejercicio, vas a crear una aplicación de lienzo a partir de tu tabla de dataverse Events.

1. Navega a https://make.powerapps.com.

2. Asegúrate de que estás en tu entorno de prácticas (`Dev One <NombreDeAlumno>`).

3. En el menú lateral, ve a la sección **Create**.

4. En la sección **Start from data**, selecciona **Dataverse**.

5. Selecciona la tabla **Events** y haz clic en **Create app**. Tras unos segundos, Power Apps automáticamente creará una aplicación de lienzo y te llevará al estudio de diseño de la app.
   

### Tarea 1.2 - Explorar la aplicación
A continuación, vamos a probar la aplicación. En la cinta superior derecha, haz clic en el botón **Preview the app (F5)**.

6. . Examina la aplicación:
   - A la izquierda, tienes una **galería** que lista todos los registros de la tabla **Events**.
   - En el centro y derecha, tienes un **formulario** para ver y editar cada registro.

7. Vamos a crear un nuevo registro. Para añadir un registro, selecciona **New** encima de la galería.

8. Rellena el formulario con los siguientes datos:
    - **Event Date**: Escoge una fecha y hora en el futuro.
    - **Event Name**: Email Worst Sign-off Practices
    - **Location**: Microsoft Teams
    - **Registration Required**: No
    - **Event details**: `Review honest yet office-inappropriate email sign offs, such as "Yours Insincerely".`
    - **Event Type**: Webinar
    - **Max Attendees**: 15

9. Selecciona el **botón de checkmark** para guardar el nuevo evento.

10. Selecciona el **botón de lápiz** para editar el evento.

11. Cambia el valor de **Registration Required** a **Yes**.

12. Selecciona el botón **x** para salir del modo edición sin guardar los cambios. (**Registration Required** debería mantenerse con valor **No**.)

13. En la esquina superior derecha, selecciona el botón circular **x** para salir de la pantalla de vista previa de la app.

14. En la cinta superior derecha, haz clic en el botón **Save**.

15. Nombra tu app `MS Learn Events` y selecciona **Save**.


## Ejercicio 2: Editar y personalizar la aplicación

### Tarea 2.1 - Editar un control

14. En el panel de la izquierda, asegúrate de que estás en la sección **Tree view** (primer botón).

15. Navega a **Events Screen** > **ScreenContainer1** > **BodyContainer1** > **SidebarContainer 1** > **RecordsGallery1**.

> [!NOTE]
> En el panel de la derecha puedes ver las propiedades de lo que hayas seleccionado. Ahora mismo, deberías ver las propiedades de la galería **RecordsGallery1**.

Vamos a empezar explorando cómo está configurada la galería, fijándonos solamente en los campos relevantes:
   - **Data source**: La tabla que está mostrando. Esta galería está mostrando registros (filas) de la tabla **Events**.
   - **Fields**: Los campos (columnas) que se utilizan en cada parte de la galería. Ahora mismo, se utiliza el **Event Name** en el título, la **Event Date** como subtítulo, y los **Event Details** como cuerpo.
   - **Layout**: El diseño de la galería (es decir, qué columnas muestra).

El resto de las propiedades definen cómo vemos la galería: los colores, tamaños, etc.

16. En **Fields**, selecciona el menú desplegable para el valor **Body1** y busca la columna **fab_location** (es posible que el prefijo **no** sea fab; lo importante es escoger la tabla Location).

17. Guarda la aplicación.
    

### Tarea 2.2 - Añadir un control
Ahora, vamos a añadir una etiqueta para mostrar la fecha de hoy.

17. En el panel de la izquierda, navega a la sección **Insert** (segundo botón.)

18. En el panel de la izquierda, selecciona **Text** para insertar una etiqueta de texto.

19. En el panel de la izquierda, vuelve a navegar a la sección **Tree view** (primer botón).

20. Arrastra la etiqueta a **Event Screen** > **Screen Container 1** > **TableNameContainer1**, <ins>debajo</ins> de la **TableNameLabel**.

21. Configura la etiqueta de texto en el panel de propiedades (panel de la derecha):
      - **Size and position**
           - **Flexible width**: On
      - **Style and theme**
           - **Font size**: 20
           - **Alignment**: _Derecha (tercer icono)_
           - **Color**: _Escoge el blanco dentro de la sección Standard colors_
           - **Font weight**: Semibold

Vamos a probar una forma más avanzada de personalizar el control usando Power Fx.

20. En la cinta superior, selecciona el menú desplegable en la zona superior izquierda y selecciona **Fill**.

21. En la barra de fórmulas a la derecha, introduce `TableNameLabel1.Fill`.

> [!NOTE]
> **¿Qué significa esto?**
> 
> Estamos estableciendo que el color de relleno de nuestra etiqueta sea el mismo que el de TableNameLabel1 (el encabezado de la página).

22. En la cinta superior, selecciona el menú desplegable en la zona superior izquierda y selecciona **Height**.

23. En la barra de fórmulas a la derecha, introduce `TableNameLabel1.Height`.

> [!NOTE]
> **¿Qué significa esto?**
> 
> Estamos estableciendo que la altura de nuestra etiqueta sea el mismo que el de TableNameLabel1 (el encabezado de la página).

24. En la cinta superior, selecciona el menú desplegable en la zona superior izquierda y selecciona **Text**.

25. En la barra de fórmulas a la derecha, introduce `Text(Today(), "dddd, mmmm d, yyyy")`.

> [!NOTE]
> **¿Qué significa esto?**
> 
> Estamos estableciendo que el texto de nuestra etiqueta sea la fecha de hoy en formato _día de la semana, mes, número de día, año_.

26. Guarda la aplicación.

### Tarea 2.3 - Crear una nueva pantalla
27. En el panel de la izquierda, navega a la sección **Data** (tercer botón.)

28. En el panel de la izquierda, selecciona **+ Add Data** para añadir más tablas a nuestra app.

29. Selecciona la tabla **Contacts** de **Microsoft Dataverse**.

30. En el panel de la izquierda, vuelve a navegar a la sección **Tree view** (primer botón).

31. Haz clic en **+ New Screen** para añadir una pantalla a la app.

> [!TIP]
> Si te cuesta navegar la lista de componentes de tu aplicación, puedes colapsar o minimizar cada sección seleccionando la flecha a la izquierda de su nombre.

32. Elige la plantilla **People**.

33. Renombra la pantalla a **Contacts screen**. (Puedes hacerlo tanto desde el panel de navegación como desde el de propiedades.)

34. En el menú de Tree View, elimina los controles **LblEmptyState1**, **iconEmptyState1** y **UserBrowseGallery1**.

35. Selecciona la galería **PeopleAddedGallery1** y configúrala en el menú de propiedades:
      - **Data Source**: Contacts
      - **Layout**: Title and subtitle on overlay
      - **Fields** (configurar _después_ de layout): Cambiar **Substitle 3** a `jobtitle`
      - **Wrap count**: 2

36. Con la galería **PeopleAddedGallery1** seleccionada, establece la propiedad **Items** a la siguiente expresión (usando la barra de fórmulas):
   `Search(
       Contacts,
       TextSearchBox1.Text,
       'Full Name'
   )`

> [!NOTE]
> **¿Qué significa esto?**
> 
> La propiedad _Items_ de una galería establece qué datos vemos en ella.
> La expresión filtra la tabla **Contacts**: coge el texto de la barra de búsqueda **TextSearchBox1** y muestra las coincidencias con el **Full Name**.
> Así, podemos buscar en la galería usando la barra de búsqueda.

37. Guarda la aplicación.


## Ejercicio 3: Añadir la aplicación a la solución Event Management
Una vez completada la app, vamos a añadirla al paquete de nuestra solución.

38. En la cinta superior derecha, haz clic en el botón **Preview the app (F5)**.

39. Explora los cambios. Cuando hayas terminado, en la esquina superior derecha, selecciona el botón circular **x** para salir de la pantalla de vista previa de la app.

40. En la cinta superior izquierda, haz clic en el botón **Back** para salir del diseñador. **Asegúrate de haber guardado tus cambios** antes de confirmar **Leave** en el cuadro emergente.

41. En el menú lateral de la izquierda, navega a **Solutions**.

42. Selecciona la solución **Event Management** para abrirla.

43. En la cinta superior, selecciona **Add existing** > **App** > **Canvas app**.

44. Marca la app **MS Learn Events** y haz clic en **Add**.

45. Para terminar, en el panel de **Objects**, asegúrate de estar en la sección **All**.

46. Una vez aquí, la barra de comandos, selecciona **Publish all customizations**.

---
## Reto: Ver detalles de contacto (Opcional)

Configura tu app para poder ver los detalles de un contacto al seleccionarlo en la galería de la pantalla **Contacts screen**.

<details>
<summary>¿Atascado? Aquí tienes algunas pistas:</summary>
   
   - Crea una nueva pantalla dedicada a ver los detalles de contacto.
   
   - Añade un formulario de presentación a esta pantalla.
   
   - Configura la propiedad `On Select` de los elementos de la galería para que redirijan a la pantalla de detalles con la selección correcta.
</details>

---
## 🎉 **¡Felicidades!** Has creado tu primera aplicación de lienzo. 🎉

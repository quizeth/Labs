# Laboratorio 3 - Crear una aplicación basada en modelo

> [!IMPORTANT]
> Para hacer este laboratorio, es necesario haber completado el [Lab 0 - Configuración del entorno del laboratorio](https://github.com/quizeth/Labs/blob/main/Power%20Platform%20Fundamentals/Lab%200%20-%20Configuración%20del%20entorno.md?plain=1), INCLUYENDO la tarea **Tarea 2.3 - Importar una solución**.

## Escenario
En este ejercicio, crearás una aplicación basada en modelo para que los usuarios puedan ver y administrar los datos sobre eventos, sesiones y ponentes.

Seguirás el siguiente plan para diseñar la aplicación basada en modelo:
- Crearás una aplicación basada en modelo a partir de tu modelo de datos.
- Configurarás un formulario de la tabla Events.
- Configurarás uan vista de la tabla Events.
- Probarás la funcionalidad de la aplicación.

## Ejercicio 1: Crear una aplicación basada en modelo

### Tarea 1.1 - Crear la aplicación
En este ejercicio, vas a crear una aplicación basada en modelo en nuestra solución de Event Management.

1. Navega a https://make.powerapps.com.

2. Asegúrate de que estás en tu entorno de prácticas (Dev One <NombreDeAlumno>).

3. En el menú lateral de la izquierda, navega a **Solutions**.
  
5. Selecciona la solución **Event Management** para abrirla.
  
7. En la cinta superior, selecciona **Create** > **App** > **Model-driven app**.

8. En el recuadro emergente, completa la información de tu nueva app y haz clic en **Create**:
     - **Name**: `Contoso Event Management`
     - **Description**: `Used to manage Events and Event Sessions`

   Tras unos segundos, Power Apps te llevará al estudio de diseño de la aplicación.

9. En el centro del lienzo, haz clic en el botón **+ Add page** y selecciona **Dataverse table**.

10. Selecciona las siguientes tablas:
      - **Contact**
      - **Event**
      - **Event Session**
      - **Session Registration**

11. Asegúrate de que la opción **Show in navigation** está marcada y haz clic en **Add**.
<img width="313" height="326" alt="image" src="https://github.com/user-attachments/assets/90d9a98c-9c05-479c-ace0-a90705412422" />

### Tarea 1.2: Editar y personalizar la aplicación
Ahora que hemos creado la aplicación, vamos a hacer algunos cambios a la manera en la que se presenta la app. Queremos tener dos grupos diferenciados: People y Events. Queremos tener la tabla Contacts en el grupo People, y todas las tablas de eventos en el grupo Events.

12. En el panel de la izquierda, bajo la sección **Navigation**, haz clic en la sección **New Group**.

13. A la derecha de la aplicación, haz clic en el icono <img width="15" height="15" alt="image" src="https://github.com/user-attachments/assets/82e226a6-517f-4dd6-8a18-5c35cafe3440" /> para ampliar el panel de **Propiedades** (en este momento, muestra las propiedades de **New Group**).

16. En este panel, cambia el **Title** a `People`.

17. En el panel **Pages** de la izquierda, haz clic en los **tres puntos** <img width="40" height="15" alt="image" src="https://github.com/user-attachments/assets/c60a1470-91fa-42f5-be1e-e2fbdda69989" /> del grupo **People**.

18. Selecciona **New Group**.

19. En el panel de **Propiedades** (a la derecha), cambia el **Title** a `Events`.

20. En el panel **Pages** de la izquierda, pasa el cursor por encima de **Events view**, selecciona los **tres puntos** <img width="40" height="15" alt="image" src="https://github.com/user-attachments/assets/c60a1470-91fa-42f5-be1e-e2fbdda69989" /> y haz clic en **Move down** para mover la tabla **Events** al grupo **Events**.

21. Repite este paso con las tablas **Session Registrations** y **Event Sessions**.

Al terminar, tu aplicación se debería ver de forma similar a esto:

<img width="658" height="326" alt="image" src="https://github.com/user-attachments/assets/c97cdad3-40e2-487b-ad85-c8f98410df9b" />


22. En la esquina superior derecha, haz clic en el botón de  **Guardar y Publicar** (segundo desde la derecha).

## Ejercicio 2: Editar los formularios y vistas de una aplicación basada en modelo
La aplicación basada en modelo utiliza formularios y vistas para presentar datos a los usuarios en la UI (interfaz de usuario). Vamos a hacer algunos cambios a estos elementos.

### Tarea 2.1 - Editar un formulario
Hay dos maneras principales de acceder al formulario para editarlo. Escoge ***una*** opción.

23a. Desde el editor de la aplicación basada en modelo, en el panel izquierdo **Pages**, pasa el cursor por encima de **Events form** y haz clic en el icono del lápiz.

23b. Alternativamente, desde la solución, navega a la tabla **Event**. Una vez ahí, haz clic en **Forms** (bajo Data Experiences).

<img width="264" height="239" alt="image" src="https://github.com/user-attachments/assets/d9828652-aaf3-44b0-ae2f-c9015b7cbfc1" />

Una vez aquí, selecciona el formulario de tipo **Main**.

24. En el lienzo, arrastra el campo **Owner** al encabezado.

25. Haz clic en la sección principal. En el panel de propiedades la derecha, bajo la sección **Formatting**, cambia el **Layout** a **2 column**.

    Automáticamente, al añadir una nueva columna, se añade una nueva sección vacía.

26. Selecciona la **New Section**.

27. En el panel de propiedades la derecha, cambia la **Label** a `Sessions` y el **Name** a `SessionsSC`.

28. En el mismo panel, marca la opción **Hide label**.

29. En los iconos de menú a la izquierda de la pantalla, selecciona el botón **Components**.
<img width="134" height="292" alt="image" src="https://github.com/user-attachments/assets/4d12df46-ab77-4196-ab91-2e7c5cdecfd3" />

30. Arrastra un control de **Subgrid** a la sección **Sessions** con esta configuración:
    - **Show related records**: Marcado
    - **Table**: Event Sessions (Event)
    - **Default view**: Active Event Sessions

31. Haz clic en **Done** para añadir el control.

32. Con la subgrid seleccionada, en el panel de propiedades la derecha, cambia la **Label** a `Event Sessions` y el **Name** a `SessionsSG`.

33. En la esquina superior derecha, haz clic en el botón de  **Guardar y Publicar** (segundo desde la derecha).

<img width="1269" height="622" alt="image" src="https://github.com/user-attachments/assets/d763243e-5dba-4402-b35a-5c96fd299c25" />


### Tarea 2.2 - Editar una vista

Hay dos maneras principales de acceder a una vista para editarlo. Escoge ***una*** opción.

34a. Desde el editor de la aplicación basada en modelo, en el panel izquierdo **Pages**, pasa el cursor por encima de **Events view** y haz clic en el icono del lápiz.

34b. Alternativamente, desde la solución, navega a la tabla **Event**. Una vez ahí, haz clic en **Views** (bajo Data Experiences).

Una vez aquí, selecciona la vista **Active Events** de tipo **Public View (default)**.

35. En el lienzo, haz clic el encabezado de la columna **Max Attendees** y selecciona **Remove**.

36. De la misma forma, elimina la columna **Registration Required** y **Event Details**.

37. Desde el panel **Table columns**, arrastra la columna **Owner** al lienzo. Sitúalo a la derecha de **Event Type**.

38. Haz clic en el encabezado de la columna **Event Date** y selecciona **Order to newer** para ordenar la tabla cronológicamente.

39. En el panel **Active Events** a la derecha, cambia el nombre a **My Active Events**.

40. En **Filter by ...**, haz clic en **Edit filters ...**.

41. En la ventana emergente, reemplaza el filtro existente por `Owner` `Equals current user` y haz clic en **Ok**.

<img width="561" height="204" alt="image" src="https://github.com/user-attachments/assets/dc5f1b35-31cd-43a7-9d0f-a38efb4ef29f" />

42. En la esquina superior derecha, haz clic en el botón de  **Guardar y Publicar** (segundo desde la derecha).

<img width="1574" height="605" alt="image" src="https://github.com/user-attachments/assets/139aaf6a-ddc6-4dff-b0f2-afc2aa049d0d" />


### Tarea 2.3 - Probar la aplicación actualizada

43. Vuelve al navegador de la solución, a la sección **All** o **Apps**.

44. Haz clic en los tres puntos a la derecha de la aplicación **Contoso Event Management** y selecciona **Play**.

45. Explora tu nueva aplicación. Asegúrate de navegar la tabla **Events** y examinar los cambios en la vista y formulario.

46. Utiliza la aplicación para añadir un nuevo registro a una de las tablas.


---
## 🎉 **¡Felicidades!** Has creado tu primera aplicación basada en modelo. 🎉

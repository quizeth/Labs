# Laboratorio 1 - Crear una aplicación de lienzo en Power Apps

> [!IMPORTANT]
> Para hacer este laboratorio, es necesario haber completado el [Lab 0 - Configuración del entorno del laboratorio](https://github.com/quizeth/Labs/blob/main/Power%20Platform%20Fundamentals/Lab%200%20-%20Configuración%20del%20entorno.md?plain=1), INCLUYENDO la tarea **Tarea 2.3 - Importar una solución**.

## Escenario
En este ejercicio, crearás una aplicación de lienzo para que los empleados puedan solicitar y administrar sus días libres.

Seguirás el siguiente plan para diseñar la aplicación de lienzo:


## Ejercicio 1: Crear una aplicación de lienzo
En este ejercicio, vas a verificar tu acceso a Power Apps.

### Tarea 1.1 - Crear la aplicación
1. Navega a https://make.powerapps.com.

2. Asegúrate de que estás en tu entorno de prácticas (Dev One <NombreDeAlumno>).

3. En el menú lateral, ve a la sección **Create**.

4. En la sección **Start from data**, selecciona **Dataverse**.

5. Selecciona la tabla **Events** y haz clic en **Create app**. Tras unos segundos, Power Apps automáticamente creará una aplicación de lienzo y te llevará al estudio de diseño de la app.

6. A continuación, vamos a probar la aplicación. En la cinta superior derecha, haz clic en el botón **Preview the app (F5)**.

7. Examina la aplicación:
   - A la izquierda, tienes una **galería** que lista todos los registros de la tabla **Events**.
   - En el centro y derecha, tienes un **formulario** para ver y editar cada registro.

8. Vamos a crear un nuevo registro. Para añadir un registro, selecciona **New** encima de la galería.

9. Rellena el formulario con los siguientes datos:
    - **Event Date**: Escoge una fecha y hora en el futuro.
    - **Event Name**: Email Worst Sign-off Practices
    - **Location**: Microsoft Teams
    - **Registration Required**: No
    - **Event details**: `Review honest yet office-inappropriate email sign offs, such as "Yours Insincerely".`
    - **Event Type**: Webinar
    - **Max Attendees**: 15

10. Select the **checkmark button** to save your event.

11. Select the **pencil button** to edit your event.

12. Change the **Registration Required** value to **Yes**.

13. Select the **x button** to undo the change.

14. On the top right corner, select the **X** button to leave the app preview screen.


## Ejercicio 2: Editar y personalizar la aplicación

### Añadir la aplicación a la solución Event Management
3. Entra en la solución que has creado o importado en el primer laboratorio, **Event Management**.

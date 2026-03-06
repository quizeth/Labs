# Laboratorio 1 - Crear un modelo de datos en Dataverse

> [!IMPORTANT]
> Para hacer este laboratorio, es necesario haber completado el [Lab 0 - Configuración del entorno del laboratorio](https://github.com/quizeth/Labs/blob/main/Power%20Platform%20Fundamentals/Lab%200%20-%20Configuración%20del%20entorno.md?plain=1).

## Escenario
En este ejercicio vas a crear un modelo de datos que se utilizará para almacenar datos sobre distintos tipos de eventos, inscripciones de eventos, ponentes y asistentes, y otros datos que Contoso necesita para administrar sus eventos.

## Ejercicio 1: Añadir componentes existentes a una solución
Primero, vamos a añadir la tabla **Accounts**. Esta es una tabla predefinida por el Common Data Model.

1. Navega a `https://make.powerapps.com`.

2. Asegúrate de que estás en tu entorno de prácticas (`Dev One <NombreDeAlumno>`).

3. Entra en la solución que has creado en el laboratorio anterior.

4. En la barra de comandos, selecciona **Add existing**.

5. En el menú deplegable que aparece, selecciona **Table**.

6. Selecciona la tabla **Account** (puedes utilizar el cuadro de búsqueda) y haz clic **Next**.

7. En la pantalla **Selected tables**, selecciona **Include all objects** y haz clic en **Add**.

<img width="1289" height="290" alt="image" src="https://github.com/user-attachments/assets/9507cad4-a199-438d-8610-32f4d822986b" />

Ahora que tenemos la tabla de **Accounts**, vamos a añadir la tabla **Contact**, otra tabla del Common Data Model.

8. En la barra de comandos, selecciona **Add existing**.

9. En el menú deplegable que aparece, selecciona **Table**.

10. Selecciona la tabla **Contact** (puedes utilizar el cuadro de búsqueda) y haz clic **Next**.

11. En la pantalla **Selected tables**, selecciona **Include all objects** y haz clic en **Add**.


## Ejercicio 2: Crear un modelo de datos con Copilot

### Tarea 2.1: Crear y editar la tabla Events

12. En la barra de comandos de la solución, selecciona **New**.
   
13. En el menú desplegable que aparece, selecciona **Table**.
   
14. En este segundo menú, selecciona **Tables**.
<img width="363" height="326" alt="image" src="https://github.com/user-attachments/assets/47a66b81-29ac-4d71-89c2-063a4b59e2be" />

Esta opción abre un diseñador donde puedes visualizar gráficamente las tablas que vayas creando o añadiendo a tu modelo de datos, así como las relaciones entre ellas.

15. Selecciona la opción **Describe new tables - Start with Copilot**.
<img width="247" height="250" alt="image" src="https://github.com/user-attachments/assets/9fd9adc5-43c0-4142-a713-c00d0b5562c1" />

16. En el recuadro **Describe the tables you want Copilot to build ...**, introduce el siguiente prompt:
   
    `Create a table for managing events. The table should identify the name of the event, event data, location, the maximum number of attendees, and event details.`

17. A la derecha del botón **Generate**, selecciona el botón de **configuración de la tabla** y establece las siguientes propiedades:
  * Table options: One table
  * Do NOT include relationships.

<img width="358" height="326" alt="image" src="https://github.com/user-attachments/assets/8d34dc8b-25e8-406c-8d77-7bc1355bbe76" />


18. Selecciona el botón **Generar**.

> [!CAUTION]
> Copilot solo debería crear UNA tabla llamada Events. Si crea más, necesitas ELIMINAR estas tablas, ya sea manualmente o pidiéndole a Copilot que elimine la(s)     tabla(s) adicionales. Esto es importante o los pasos siguientes del laboratorio se verán afectados.


Ahora que hemos creado la tabla, vamos a añadir algunas columnas adicionales. El primer paso será añadir una columna de Tipo de Evento. También añadiremos los Contacts que asistirán a nuestros eventos. Queremos añadir la tabla de Contact ya existente al modelo de datos y asociarla más adelante con Event Registrations.

19. En el panel de Copilot, a la derecha, en el campo **What would you like to do next?**, introduce el siguiente prompt:
   
    `Add a choice column named Event Type to Event table. Add a second choice column named Registration Required to Event table.`

> [!IMPORTANT]
> Tu tabla Event no tiene que ser exactamente igual que la de tus compañeros, pero necesita tener por lo menos las siguientes columnas:
> * Event Name
> * Event Date
> * Max Attendees
> * Location
> * Event type
> * Registration Required.
>
> Si no tienes estas columnas utiliza Copilot para añadirlas al modelo de datos.

### Tarea 2.2: Crear y editar la tabla Events
Dependiendo del tipo de evento, puede haber una o más sesiones. Para administrar las distintas sesiones, necesitamos definir qué es cada sesión y el evento con el que está asociada. Para ello, vamos a crear una tabla de Event Sessions usando Copilot.

20. En el panel de Copilot, a la derecha, en el campo **What would you like to do next?**, introduce el siguiente prompt:
   
    `Add a new table called Event Session.`

Es probable que Copilot cree dos tablas, Event Session y Event Speaker. Como nuestros contactos (de la tabla Contacts) van a ser nuestros ponentes, vamos a eliminar esta segunda tabla.

21. Si es necesario,  en el campo **What would you like to do next?**, introduce el siguiente prompt:
   
    `Remove the Event Speaker table.`

22. En el campo **What would you like to do next?**, introduce el siguiente prompt:
   
    `Add a new text column to the Event Session table called Session Description.`

El siguiente paso va a ser crear una última tabla llamada Session Registrations. Esta tabla se usará para administrar los individuos que se inscriban en sesiones específicas.

23. En el campo **What would you like to do next?**, introduce el siguiente prompt:
   
    `Add a new table called Session Registrations.`

Es probable que Copilot cree varias tablas: Session Registration, Session, o Participant (o variaciones de dichas tablas). Como nuestros contactos (de la tabla Contacts) pueden ser participantes, vamos a eliminar la tabla Participant. Cualquier otra tabla que se haya creado es innecesaria así que también las eliminaremos.

24. Si es necesario,  en el campo **What would you like to do next?**, introduce el siguiente prompt y complétalo con cualquier tabla adicional que necesites eliminar:
   
    `Remove the Participant table.`

En ocasiones, se añade una columna de Participant Name a la tabla de Session Registration. Necesitamos eliminarla ya que puede causar problemas más adelante cuando intentemos guardar el modelo de datos. (Vamos a reemplazarla con una columna distinta más tarde.)

25. Si es necesario,  en el campo **What would you like to do next?**, introduce el siguiente prompt:
   
    `Remove the Participant Name column from the Session Registration table.`

El siguiente paso es renombrar la columna primaria de la tabla Session Registration. La columna primaria es el nombre de cada registro (fila) que utilizaremos para identificarlo en búsquedas, vistas, y registros relacionados.

26. En el campo **What would you like to do next?**, introduce el siguiente prompt:
   
    `Rename the Primary Column to Registration Name in Session Registration table.`

27. En el campo **What would you like to do next?**, introduce el siguiente prompt:
   
    `Add a text column to the Session registration table called Special Instructions.`

> [!IMPORTANT]
> Tu tabla Session Registration no tiene que ser exactamente igual que la de tus compañeros, pero necesita tener por lo menos las siguientes columnas:
> * Registration Name
> * Session Date
> * Special Instructions
>
> Si no tienes estas columnas utiliza Copilot para añadirlas al modelo de datos.


<img width="710" height="499" alt="image" src="https://github.com/user-attachments/assets/90320b35-21f5-4d80-a5dd-01409d2fd7e6" />


    
### Tarea 2.3: Añadir tablas existentes al modelo de datos y crear relaciones entre tablas
Ahora, vamos a traernos la tabla de Contact. Una vez incluida en el modelo, vamos a crear relaciones entre nuestras distintas tablas.
28. En la barra de comandos, selecciona **Existing table**.

2. Selecciona la tabla **Contact** (puedes utilizar el cuadro de búsqueda) y haz clic en **Add Selected**.

Como las personas registradas en la tabla Contact pueden ser ponentes en las sesiones, vamos a crear una relación entre las tablas Contact y Event Session.

29. En la barra de comandos, selecciona **Create relationships**.

30. Configura la relación:
   * **Relationship Type:** One-to-many
   * **One:** Contact
   * **Many:** Event Session
   * **Display name:** `Speaker`

<img width="325" height="664" alt="image" src="https://github.com/user-attachments/assets/4f1202a5-a32d-494b-88c6-f31ebadc5989" />


31. Selecciona **Done**.

Como las personas registradas en la tabla Contact pueden asistir alas sesiones, vamos a crear una relación entre las tablas Contact y Session Registration.

32. En la barra de comandos, selecciona **Create relationships**.

33. Configura la relación:
   * **Relationship Type:** One-to-many
   * **One:** Contact
   * **Many:** Session Registration
   * **Display name:** `Participant`

<img width="326" height="695" alt="image" src="https://github.com/user-attachments/assets/e5d6c102-08a7-4acb-a173-00dbaa4d507c" />


34. Selecciona **Done**.

Un solo evento puede tener múltiples sesiones asociadas, así que vamos a crear una relación entre las tablas Event y Event Session.

35. En la barra de comandos, selecciona **Create relationships**.

36. Configura la relación:
   * **Relationship Type:** One-to-many
   * **One:** Event
   * **Many:** Event Session
   * **Display name:** `Event`

<img width="326" height="659" alt="image" src="https://github.com/user-attachments/assets/1b510805-f972-442e-a73a-abd614c3cd7e" />


37. Selecciona **Done**.

Por último, los participantes se registran en Event Sessions, así que crearemos una relación entre las tablas Event Session y Session Registration.

38. En la barra de comandos, selecciona **Create relationships**.

39. Configura la relación:
   * **Relationship Type:** One-to-many
   * **One:** Event Session
   * **Many:** Session Registrations
   * **Display name:** `Event Session`

<img width="325" height="688" alt="image" src="https://github.com/user-attachments/assets/4ffdf773-e961-4900-938a-c24cf402d877" />


37. Selecciona **Done**.

Tu nuevo modelo de datos debería parecerse a la siguiente imagen:
<img width="624" height="471" alt="image" src="https://github.com/user-attachments/assets/d7e032b5-07c2-4d4e-b7f2-83a0d6bb478a" />

40. Selecciona **Save and exit**.


## Ejercicio 3: Editar tablas y columnas manualmente

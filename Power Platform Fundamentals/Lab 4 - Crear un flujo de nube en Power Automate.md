# Laboratorio 4 - Crear un flujo de nube en Power Automate

> [!IMPORTANT]
> Para hacer este laboratorio, es necesario haber completado el [Lab 0 - Configuración del entorno del laboratorio](https://github.com/quizeth/Labs/blob/main/Power%20Platform%20Fundamentals/Lab%200%20-%20Configuración%20del%20entorno.md?plain=1), INCLUYENDO la tarea **Tarea 2.3 - Importar una solución**.

> [!NOTE]
> Puedes descargar la solución de este ejercicio (sin datos) en la carpeta de Files: [EventManagement_1_0_0_3.zip](https://github.com/quizeth/Labs/blob/4607d0059627f14e1e45c5ddd92105701c50e2ea/Power%20Platform%20Fundamentals/Files/EventManagement_1_0_0_3.zip)
>
> 
## Escenario
En este ejercicio crearás un flujo de nube para automatizar un proceso de aprobación. Configurarás un flujo desencadenado por eventos (automatizado) usando distintas acciones y controles para reducir la carga de trabajo de este equipo de mantenimiento.

Seguirás el siguiente plan para diseñar el flujo:

- Crearás una columna para guardar el estado de aprobación de cada evento.
- Crearás un flujo de aprobación.
- Configurarás su desencadenador y acciones de distintas formas.
- Probarás la funcionalidad del flujo.

## Ejercicio 0: Crea una columna de Approval Status
En Contoso, han decidido establecer un proceso de aprobación para decidir qué eventos se llevarán a cabo. Para reflejar el estado de cada propuesta, vamos a añadir una columna a la tabla Event en preparación para el resto del laboratorio. También la añadiremos a la vista y formulario de los eventos.

1. Navega a https://make.powerapps.com.

2. Asegúrate de que estás en tu entorno de prácticas (`Dev One <NombreDeAlumno>`).

3. En el menú lateral de la izquierda, navega a **Solutions**.

4. Selecciona la solución **Event Management** para abrirla.

5. Selecciona la tabla **Event**.

6. En la cinta superior, selecciona **New** > **Column**.

7. Configura la columna con estas propiedades:
   - **Display name**: `Approval Status`
   - **Data type**: `Choice` > `Choice`
   - **Required**: `Business required`
   - **Sync with global choice?**: `No`
   - **Choices**: _Tres entradas con los siguientes valores_


        | Label  | Value |
        | --- | --- |
        | Approved  | 0  |
        | Rejected  | 1  |
        | Pending  | 2  |

  - **Default choice**: `Pending`

8. Selecciona **Save** para guardar la nueva columna.

9. Desde la tabla, haz clic en **Forms** (bajo Data Experiences).

10. Selecciona el formulario de tipo **Main**.

11. Desde el panel de la izquierda **Table columns**, arrastra la columna **Approval Status** al encabezado, a la derecha del **Owner**.

<img width="1220" height="139" alt="image" src="https://github.com/user-attachments/assets/0d09fdc3-a81d-46ca-afe7-fa656c5d9123" />

12. En la esquina superior derecha, haz clic en el botón de  **Guardar y Publicar**.

13. Vuelve a la página de la tabla y haz clic en **Views** (bajo Data Experiences).

14. Abre la vista **My Active Events**.

15. Elimina la columna **Owner**.

16. Arrastra la columna **Approval Status** al lienzo.
<img width="996" height="472" alt="image" src="https://github.com/user-attachments/assets/54226377-de0b-492e-8151-dcb7d20cd9cd" />


17. En la esquina superior derecha, haz clic en el botón de  **Guardar y Publicar**.


## Ejercicio 1: Crea un flujo de aprobación en Power Automate

### Tarea 1.1 - Crea un flujo automatizado
18. En una nueva pestaña, navega a Power Automate: `https://make.powerautomate.com`.
  
19. En Power Automate, asegúrate de que estás en el entorno **`Dev One <NúmeroDeAlumno>`**.

20. En el menú a la izquierda, ve a la pestaña **Create**.

21. Selecciona **Automated cloud flow** y configura tu nuevo flujo:
   - **Flow name**: `Event approval`
   - **Choose your flow's trigger**: `When a row is added, modified or deleted` (Microsoft Dataverse).

<img width="418" height="245" alt="image" src="https://github.com/user-attachments/assets/dad67f8f-ad72-44e2-940c-b2322ab7240f" />



22. Selecciona **Create**. Esto te llevará al diseñador de tu flujo.



### Tarea 1.2 - Configura el desencadenador
> [!NOTE]
> <img width="269" height="163" alt="image" src="https://github.com/user-attachments/assets/1c594629-23fe-4622-950d-9c690e3ca350" />
>
>
> El paso del desencadenador muestra un mensaje de **Invalid parameters**. Esto es normal y significa que necesitamos configurar el paso con la información necesaria para cumplir su función.

23. Selecciona el paso **When a row is added, modified or deleted**. Esto abrirá el panel de propiedades a la izquierda.

24. En el panel **Parámetros**, establece la siguiente configuración:
   - **Change type:** `Added or Modified`
   - **Table name**: `Events`
   - **Scope**: `Organization` (para que se ejecute para todos los usuarios)

> [!NOTE]
> <img width="375" height="326" alt="image" src="https://github.com/user-attachments/assets/79bbed67-4a75-428a-a5b9-0ee0d1178845" />
>
>
> Si no estás conectado a Dataverse, vamos a establecer esta conexión ahora. Si ya tienes una conexión establecida y **no** quieres cambiar la que estás utilizando, puedes pasar a la siguiente tarea.

25. En el panel **Parámetros**, haz clic en **Change connection reference**.

26. Selecciona **Add New**.

27. Configura la conexión:
    - **Connection name**: `Dataverse`
    - **Authentication Type**: Oauth
  
28. Haz clic en el botón **Sign in**.


29. Selecciona la cuenta de Microsoft 365 que estés usando.



### Tarea 1.3 - Configura una acción con Copilot
Solamente queremos que pasen por el proceso de aprobación las propuestas de eventos grandes. Vamos a añadir una condición para revisar este coste.

30. En el panel de **Copilot** a la derecha, introduce el siguiente prompt: `Add a condition to see if the Max Attendees is equal or higer than 50.`

[!TIP]
> Si no quieres o no puedes usar Copilot, añade una acción de **Control** > **Condition**.

31. Selecciona la acción de condición que se acaba de crear para configurar una expresión.
   
32. En la primera caja (_Choose a value_), haz clic en el campo y selecciona el botón del rayo para abrir las opciones de contenido dinámico.
<img width="15" height="15" alt="image" src="https://github.com/user-attachments/assets/0222330f-64d5-43b6-b3ac-78bb22049944" />


33. Busca y selecciona `Max Attendees`.

34. En la segunda caja, establece el operador a **is greater or equal to**.

35. En la tercera caja (_Choose a value_), escribe `50`.

36. Pasa el cursor por encima del nombre de la condición (`Condition`), haz clic, y cámbialo a `Check event size`.

<img width="616" height="359" alt="image" src="https://github.com/user-attachments/assets/e292b425-2270-4367-812a-f8afa097d52d" />


### Tarea 1.4 - Configura una acción manualmente
Ahora que tenemos nuestra condición, vamos a configurar qué ocurre cuando la condición no se cumple --es decir, cuando no hace falta que la solicitud pase por un proceso de aprobación.

37. Dentro de la condición, bajo la rama **False**, haz clic en el icono +. 
<img width="298" height="114" alt="image" src="https://github.com/user-attachments/assets/522e42b0-269b-49fb-9e2b-bfa5f6e0ce84" />


38. En la barra de búsqueda, escribe `update a row` y selecciona la acción **Update a row** del conector **Microsoft Dataverse**.
<img width="370" height="95" alt="image" src="https://github.com/user-attachments/assets/74ef1dcd-8a9d-4bfd-b839-796eddc8870d" />


39. Para renombrar la acción, pasa el cursor por encima del nombre por defecto (`Update a row`), selecciónalo, y sustitúyelo por `Approve requests under event size`.
    
40. Configura la acción:
    - **Table name**: Events
    - **Row ID**: Inserta el contenido dinámico `Event`.
    - **Advanced parameters**: `Approved`.

<img width="616" height="398" alt="image" src="https://github.com/user-attachments/assets/dedeeee9-3054-4c3f-9bbe-abd4144f9fc1" />




### Tarea 1.5 - Configura una aprobación
Por otra parte, vamos a configurar qué ocurre cuando el coste de la solicitud supere el límite establecido.

41. Dentro de la condición, bajo la rama **True**, haz clic en el icono +.
<img width="311" height="130" alt="image" src="https://github.com/user-attachments/assets/4ec2cb51-627a-4ab4-9505-cdffed327ca8" />


42. En la barra de búsqueda, escribe `approval` y selecciona la acción **Start and wait for an approval** del conector **Standard approvals**.
<img width="376" height="87" alt="image" src="https://github.com/user-attachments/assets/627d2765-2c4d-41bc-8661-fda163f2d54e" />

43. Configura la acción:
    - **Approval type**: Approve/Reject - First to respond
    - **Title**: Escribe `New Event Approval`.
    - **Assigned To**: _Escribe tu email._
    - **Details**: _Sustituye los valores entre [] por el contenido dinámico correspondiente._
      
    `The event proposal [Event Name] is awaiting your approval.`


    `Details: [Event Details]  -  [Location]`
    
    `Event type: [Event Type]`

    `Max attendees: [Max Attendees]`

<img width="594" height="530" alt="image" src="https://github.com/user-attachments/assets/c92c6259-5cb8-43ee-a6fb-3d1b5086fd7e" />



44. Añade una nueva acción bajo la acción **Start and wait for an approval** haciendo clic en el icono + .
    
45. Selecciona el control **Control**.

46. Escoge la acción **Condition**.

47. Configura la nueva condición:
    - **Nombre de la acción**: `Check approval outcome`
    - **Choose a value** (primera caja): Inserta el contenido dinámico `Outcome` del paso **Start and wait for an approval**.
    - **Operador**: is equal to
    - **Choose a value** (segunda caja): `Approve`
   
<img width="584" height="326" alt="image" src="https://github.com/user-attachments/assets/b53075ac-800f-42e6-ad70-07fe49632916" />

48. Haz clic derecho en el paso **Approve requests under budget** y selecciona **Copy action**.

49. Bajo la rama **True** de la condición **Check approval outcome**, haz clic derecho en el icono <img width="15" height="15" alt="image" src="https://github.com/user-attachments/assets/0128f54b-784f-4735-ba6e-5fc462e5a414" /> y selecciona **Paste an action** > **Paste an action**.

50. Cambia el nombre de la nueva acción a `Approve request`.

51. Repite los pasos anteriores para copiar la acción y pegarla bajo la rama **False** de la condición **Check approval outcome**.

52. Cambia el nombre de la nueva acción a `Reject request`.

53. Cambia el campo **Approval Status** a **Rejected**.


## Ejercicio 2: Guarda y prueba un flujo de nube
54. En la cinta superior derecha, haz clic en el botón **Save**.
> [!NOTE]
> Power Automate valida automáticamente tu flujo al guardarlo. Si hay cualquier error, te aparecerán las notificaciones y explicacioens apropiadas en este momento.

Para probar el flujo, necesitamos al menos un ejemplo. Para ello, vamos a utilizar la aplicación de Maintenance Requests.

55. En una nueva pestaña, abre la solución **Event Management**.

56. Ejecuta la app **Contoso Event Management**.

57. Ve a la pestaña **Events**.

59. En la cinta superior, selecciona **+ New** para crear una nuevo evento.

60. Crea un evento de prueba utilizando los datos que quieras.
    - Incluye un **Max Attendees** con valor **por debajo de los 50**.
    - Asegúrate de que **Approval Status** se mantenga con valor **Pending**.

61. En la cinta superior, selecciona **Save & Close**
    
62. Tras unos segundos, actualiza la página. La columna **Approved** de tu solicitud automáticamente habrá cambiado a **Yes**.

> [!NOTE]
> Esto también puedes verlo desde Power Automate. En la página de información de tu flujo, puedes ver el historial de ejecuciones.
> Ten en cuenta que la primera ejecución suele tardar un poco más de lo usual.

63. Crea una segunda solicitud de prueba utilizando los datos que quieras
    - Incluye un **Max Attendees** con valor **por encima de los 50**.
    - Asegúrate de que **Approval Status** se mantenga con valor **Pending**.

64. Tras unos segundos, comprueba tu email, Teams y/o sección de Approvals en Power Automate para ver la nueva solicitud de aprobación.

65. Escoge tu respuesta (**Approve** o **Reject**, no reasignes la solicitud), incluye un breve comentario, y confirma tu aprobación

66. Tras unos segundos, actualiza la página de la app **Maintenace Requests**. La columna **Approved** de tu solicitud automáticamente habrá cambiado de acuerdo a tu respuesta.

<img width="1517" height="393" alt="image" src="https://github.com/user-attachments/assets/28a5817b-e4aa-4087-b8d9-fbf61c1b719e" />


67. Para terminar, vuelve a Power Automate o Power Apps y navega a la solución **Event Management**.

68. En la cinta superior, selecciona **Add existing** > **Automation** > **Cloud flow**.

69. En la pestaña **Outside Dataverse**, selecciona el flujo **Event approval** que acabas de crear y haz clic en **Add**.



---
## 🎉 **¡Felicidades!** Has creado tu primer flujo de nube en Power Automate. 🎉

# Laboratorio 1 - Crear un flujo de nube en Power Automate

> [!IMPORTANT]
> Para hacer este laboratorio, es necesario haber completado el [Lab 0 - Configuración del entorno del laboratorio](https://github.com/quizeth/Labs/blob/main/Automatización%20en%20Power%20Platform/Lab%200%20-%20Configuración%20del%20entorno.md).

## Escenario
Ahti's es el equipo de mantenimiento de una empresa. Su equipo tiene una solución para poder almacenar y administrar las solicitudes de mantenimiento que reciben. Sin embargo, algunas de estas peticiones tienen un coste elevado y requieren un proceso de aprobación para determinar si son apropiadas o no. Ahti's quiere reducir el trabajo repetitivo que involucran estas aprobaciones y automatizar el proceso, pero también necesita que la decisión final de aprobar o no una solicitud quede en manos de la persona responsable.

## Ejercicio 1: Crea un flujo de aprobación en Power Automate

### Tarea 1.1 - Crea un flujo automatizado
1. En Power Automate, asegúrate de que estás en el entorno **Dev One <NúmeroDeAlumno>**.

2. En el menú a la izquierda, ve a la pestaña **Create**.

3. Selecciona **Automated cloud flow** y configura tu nuevo flujo:
   - **Flow name**: `Maintenance expense approval`
   - **Choose your flow's trigger**: `When a row is added, modified or deleted` (Microsoft Dataverse).

  <img width="509" height="326" alt="image" src="https://github.com/user-attachments/assets/08f811d8-00d6-4236-82ec-fd638d39552f" />

4. Selecciona **Create**. Esto te llevará al diseñador de tu flujo.



### Tarea 1.2 - Configura el desencadenador
> [!NOTE]
> <img width="269" height="163" alt="image" src="https://github.com/user-attachments/assets/1c594629-23fe-4622-950d-9c690e3ca350" />
>
>
> El paso del desencadenador muestra un mensaje de **Invalid parameters**. Esto es normal y significa que necesitamos configurar el paso con la información necesaria para cumplir su función.

5. Selecciona el paso **When a row is added, modified or deleted**. Esto abrirá el panel de propiedades a la izquierda.

6. En el panel **Parámetros**, establece la siguiente configuración:
   - **Change type:** `Added or Modified`
   - **Table name**: `Maintenance Requests`
   - **Scope**: `Organization` (para que se ejecute para todos los usuarios)

> [!NOTE]
> <img width="375" height="326" alt="image" src="https://github.com/user-attachments/assets/79bbed67-4a75-428a-a5b9-0ee0d1178845" />
>
>
> Si no estás conectado a Dataverse, vamos a establecer esta conexión ahora. Si ya tienes una conexión establecida y **no** quieres cambiar la que estás utilizando, puedes pasar a la siguiente tarea.

7. En el panel **Parámetros**, haz clic en **Change connection reference**.

8. Selecciona **Add New**.

9. Configura la conexión:
    - **Connection name**: `Dataverse`
    - **Authentication Type**: Oauth
  
10. Haz clic en el botón **Sign in**.

11. Selecciona la cuenta de Microsoft 365 que estés usando.



### Tarea 1.3 - Configura una acción con Copilot
Solamente queremos que pasen por el proceso de aprobación las solicitudes con un alto coste estimado. Vamos a añadir una condición para revisar este coste.

12. En el panel de **Copilot** a la derecha, introduce el siguiente prompt: `Add a condition to see if the Estimated Cost is equal or higer than 1000.`

13. Selecciona la acción de condición que se acaba de crear para configurar una expresión.
   
14. En la primera caja (_Choose a value_), haz clic en el campo y selecciona el botón <img width="15" height="15" alt="image" src="https://github.com/user-attachments/assets/0222330f-64d5-43b6-b3ac-78bb22049944" /> para abrir las opciones de contenido dinámico.

15. Busca y selecciona **Estimated Cost**.

16. En la segunda caja, establece el operador a **is greater or equal to**.

17. En la tercera caja (_Choose a value_), escribe 1000.

<img width="454" height="326" alt="image" src="https://github.com/user-attachments/assets/6522d3db-e40b-4be7-b27d-dba08b6d8d1a" />



### Tarea 1.4 - Configura una acción manualmente
Ahora que tenemos nuestra condición, vamos a configurar qué ocurre cuando la condición no se cumple --es decir, cuando no hace falta que la solicitud pase por un proceso de aprobación.

18. Dentro de la condición, bajo la rama **False**, haz clic en el icono <img width="15" height="15" alt="image" src="https://github.com/user-attachments/assets/0128f54b-784f-4735-ba6e-5fc462e5a414" />
<img width="298" height="114" alt="image" src="https://github.com/user-attachments/assets/522e42b0-269b-49fb-9e2b-bfa5f6e0ce84" />

19. En la barra de búsqueda, escribe `update a row` y selecciona la acción **Update a row** del conector **Microsoft Dataverse**.
<img width="370" height="95" alt="image" src="https://github.com/user-attachments/assets/74ef1dcd-8a9d-4bfd-b839-796eddc8870d" />

20. Para renombrar la acción, pasa el cursor por encima del nombre por defecto (**Update a row**), selecciónalo, y sustitúyelo por `Approve requests under budget`.
    
21. Configura la acción:
    - **Table name**: Maintenance Requests
    - **Row ID**: Inserta el contenido dinámico `Maintenance Request`.
    - **Advanced parameters**: Marca **Approved**.
    - **Approved**: Yes

<img width="305" height="326" alt="image" src="https://github.com/user-attachments/assets/1024a043-992b-4e7f-9635-6f583f119ae5" />



### Tarea 1.5 - Configura una aprobación
Por otra parte, vamos a configurar qué ocurre cuando el coste de la solicitud supere el límite establecido.

22. Dentro de la condición, bajo la rama **True**, haz clic en el icono <img width="15" height="15" alt="image" src="https://github.com/user-attachments/assets/0128f54b-784f-4735-ba6e-5fc462e5a414" />.
<img width="311" height="130" alt="image" src="https://github.com/user-attachments/assets/4ec2cb51-627a-4ab4-9505-cdffed327ca8" />

23. En la barra de búsqueda, escribe `approval` y selecciona la acción **Start and wait for an approval** del conector **Standard approvals**.
<img width="376" height="87" alt="image" src="https://github.com/user-attachments/assets/627d2765-2c4d-41bc-8661-fda163f2d54e" />

24. Configura la acción:
    - **Approval type**: Approve/Reject - First to respond
    - **Title**: Escribe `Expense Approval - `.  A continuación, inserta el contenido dinámico <img width="72" height="20" alt="image" src="https://github.com/user-attachments/assets/98b48864-c21d-4aa7-b62c-db1032e46c0f" />.
    - **Assigned To**: _Escribe tu email._
    - **Details**: 
    `The maintenance request` <img width="87" height="20" alt="image" src="https://github.com/user-attachments/assets/1726b53e-ce52-4eaf-abb9-28c6fdae7333" /> `awaits your approval.`

    `The estimated cost is` <img width="120" height="20" alt="image" src="https://github.com/user-attachments/assets/f50db4ac-c4a9-40a4-9a03-9627282c3ed1" />`.`
    
    `The maintenance request is` <img width="72" height="20" alt="image" src="https://github.com/user-attachments/assets/98b48864-c21d-4aa7-b62c-db1032e46c0f" />` priority.`

<img width="321" height="326" alt="image" src="https://github.com/user-attachments/assets/89efcd55-9c24-4624-af3e-931436fef25f" />

25. Añade una nueva acción bajo la acción **Start and wait for an approval** haciendo clic en el icono <img width="15" height="15" alt="image" src="https://github.com/user-attachments/assets/0128f54b-784f-4735-ba6e-5fc462e5a414" />.

26. Selecciona el control **Control**.

27. Escoge la acción **Condition**.

28. Configura la nueva condición:
    - **Nombre de la acción**: `Check approval outcome`
    - **Choose a value** (primera caja): Inserta el contenido dinámico <img width="94" height="20" alt="image" src="https://github.com/user-attachments/assets/6941a599-2a61-4402-83d4-f84767fde677" /> del paso **Start and wait for an approval**.
    - **Operador**: is equal to
    - **Choose a value** (segunda caja): `Approve`
   
<img width="584" height="326" alt="image" src="https://github.com/user-attachments/assets/b53075ac-800f-42e6-ad70-07fe49632916" />

29. Haz clic derecho en el paso **Approve requests under budget** y selecciona **Copy action**.

30. Bajo la rama **True** de la condición **Check approval outcome**, haz clic derecho en el icono <img width="15" height="15" alt="image" src="https://github.com/user-attachments/assets/0128f54b-784f-4735-ba6e-5fc462e5a414" /> y selecciona **Paste an action** > **Paste an action**.

31. Cambia el nombre de la nueva acción a `Approve request over budget`.

32. Repite los pasos anteriores para copiar la acción y pegarla bajo la rama **False** de la condición **Check approval outcome**.

33. Cambia el nombre de la nueva acción a `Reject request over budget`.

34. Cambia el campo **Approved** a **No**.


## Ejercicio 2: Guarda y prueba un flujo de nube
35. En la cinta superior derecha, haz clic en el botón **Save**.
> [!NOTE]
> Power Automate valida automáticamente tu flujo al guardarlo. Si hay cualquier error, te aparecerán las notificaciones y explicacioens apropiadas en este momento.

Para probar el flujo, necesitamos al menos un ejemplo. Para ello, vamos a utilizar la aplicación de Maintenance Requests.

36. En una nueva pestaña, abre la solución **Maintenance Requests**.

37. Ejecuta la app **Maintenance Management**.

38. En la pestaña **Maintenance Requests**, en la cinta superior, selecciona **+ New** para crear una nueva solicitud.

39. Crea una solicitud de prueba utilizando los datos que quieras.
    - Incluye un **Estimated Cost** con valor **por debajo de los 1000$**.
    - Asegúrate de que **Approved** se mantenga con valor **No**.

40. En la cinta superior, selecciona **Save & Close**
    
41. Tras unos segundos, actualiza la página. La columna **Approved** de tu solicitud automáticamente habrá cambiado a **Yes**.

> [!NOTE]
> Esto también puedes verlo desde Power Automate. En la página de información de tu flujo, puedes ver el historial de ejecuciones.

42. Crea una segunda solicitud de prueba utilizando los datos que quieras
    - Incluye un **Estimated Cost** con valor **por encima de los 1000$**.
    - Asegúrate de que **Approved** se mantenga con valor **No**.

43. Tras unos segundos, comprueba tu email, Teams y/o sección de Approvals en Power Automate para ver la nueva solicitud de aprobación.

44. Escoge tu respuesta (**Approve** o **Reject**, no reasignes la solicitud), incluye un breve comentario, y confirma tu aprobación

45. Tras unos segundos, actualiza la página de la app **Maintenace Requests**. La columna **Approved** de tu solicitud automáticamente habrá cambiado de acuerdo a tu respuesta.


---
## 🎉 **¡Felicidades!** Has creado tu primer flujo de nube en Power Automate. 🎉

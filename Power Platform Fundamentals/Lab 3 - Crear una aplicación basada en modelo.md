# Laboratorio 3 - Crear una aplicación basada en modelo

> [!IMPORTANT]
> Para hacer este laboratorio, es necesario haber completado el [Lab 0 - Configuración del entorno del laboratorio](https://github.com/quizeth/Labs/blob/main/Power%20Platform%20Fundamentals/Lab%200%20-%20Configuración%20del%20entorno.md?plain=1), INCLUYENDO la tarea **Tarea 2.3 - Importar una solución**.

## Escenario
En este ejercicio, crearás una aplicación basada en modelo para que los usuarios puedan ver y administrar los datos sobre eventos, sesiones y ponentes.

Seguirás el siguiente plan para diseñar la aplicación basada en modelo:


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

13. A la derecha de la aplicación, haz clic en el icono <img width="15" height="15" alt="image" src="https://github.com/user-attachments/assets/82e226a6-517f-4dd6-8a18-5c35cafe3440" />
para ampliar el panel de **Propiedades** (en este momento, muestra las propiedades de **New Group**).

14. En este panel, cambia el **Title** a `People`.

15. En el panel **Pages** de la izquierda, haz clic en los **tres puntos** <img width="40" height="15" alt="image" src="https://github.com/user-attachments/assets/c60a1470-91fa-42f5-be1e-e2fbdda69989" /> del grupo **People**.

16. Selecciona **New Group**.

17. En el panel de **Propiedades** (a la derecha), cambia el **Title** a `Events`.

18. En el panel **Pages** de la izquierda, pasa el cursor por encima de **Events view**, selecciona los **tres puntos** <img width="40" height="15" alt="image" src="https://github.com/user-attachments/assets/c60a1470-91fa-42f5-be1e-e2fbdda69989" />, y haz clic en **Move down** para mover la tabla **Events** al grupo **Events**.

19. Repite este paso con las tablas **Session Registrations** y **Event Sessions**.

Al terminar, tu aplicación se debería ver de forma similar a esto:

<img width="658" height="326" alt="image" src="https://github.com/user-attachments/assets/c97cdad3-40e2-487b-ad85-c8f98410df9b" />



## Ejercicio 2: Editar los formularios y vistas de una aplicación basada en modelo
La aplicación basada en modelo utiliza formularios y vistas para presentar datos a los usuarios en la UI (interfaz de usuario). Vamos a hacer algunos cambios a estos elementos.

### Tarea 2.1 - Editar u


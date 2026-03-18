# Laboratorio 0 - Preparar el entorno del laboratorio

> [!IMPORTANT]
> En este curso, utilizarás un usuario de Microsoft 365 con las licencias de Power Platform necesarias para completar este curso.
Este usuario será eliminado una semana después de finalizar el curso.
>
> Microsoft 365 y Power Platform están evolucionando constantemente. Las instrucciones en este documento pueden diferir de la experiencia real con las herramientas.

## Escenario
Contoso Consulting es una organización de servicios profesionales especializada en consultoría de IT e IA. A lo largo del año, ofrecen muchos eventos diferentes a sus clientes. Algunos de estos son eventos tipo feria comercial, donde invitan a muchos socios para que presenten información sobre nuevos productos, tendencias del mercado y servicios. Otros se realizan durante el año y son webinars breves utilizados para proporcionar información sobre productos individuales.

Contoso desea utilizar Power Platform para crear una solución de gestión de eventos que les permita administrar los diferentes eventos que organizan durante el año.

En este ejercicio crearás un entorno y una solución que se utilizará para la Administración del Ciclo de Vida de las Aplicaciones (ALM) y para agrupar todas las aplicaciones, sitios y flujos que se creen, de modo que puedan administrarse y transportarse fácilmente.

## Ejercicio 1: Acceso a Microsoft Power Platform
En este ejercicio, vas a verificar tu acceso a Power Apps.

### Tarea 1.1 - Entrar en Power Apps
1. En una pestaña nueva de tu navegador, navega a `https://make.powerapps.com`.
> [!NOTE]
> Si ya utilizas Power Apps con tu propia cuenta, abre la pestaña en modo incógnito o utiliza otro navegador.

2. Inicia sesión con las credenciales de Microsoft 365 que te ha proporcionado tu instructor.

3. Opcionalmente, selecciona **Sí** para mantener la sesión conectada en este navegador.

> [!WARNING]
> Las instrucciones de los laboratorios asumen que estás trabajando en Power Platform en inglés. Si trabajas en español, ten en cuenta que algunos términos pueden cambiar significativamente.
>
> Puedes cambiar el idioma seleccionando el icono de **Configuración** <img width="13" height="15" alt="image" src="https://github.com/user-attachments/assets/464d1353-b0ec-4fed-8202-e93f2a10eb04" /> > **Configuración de Power Apps** > **Lenguaje**.

<img width="300" height="326" alt="image" src="https://github.com/user-attachments/assets/e1903cf4-fa3a-49e9-bdc8-ef1f7d510f48" />
<img width="625" height="326" alt="image" src="https://github.com/user-attachments/assets/295281c9-2eb3-400a-a0c1-3e50f3e96fb4" />


## Ejercicio 2: Crear un entorno
En este ejercicio, vas a crear un entorno y una solución para el proyecto de Contoso Consulting.

### Tarea 2.1 - Crear un entorno
1. En una pestaña nueva de tu navegador, navega a `https://admin.powerplatform.microsoft.com`.
   
2. En el menú lateral a la izquierda, selecciona **Manage**.

3. En la cinta superior, selecciona **New**.

4. Configura el nuevo entorno con estas propiedades (deja el resto con los valores por defecto):
   - **Name:** Dev One <NúmeroDeAlumno>. **Ejemplo: Dev One Alumno01**
   - **Type:** Developer
   - **Language:** English (United States)
   - **Currency:** USD ($)
   - **Deploy sample apps and data?:** Yes
<img width="116" height="326" alt="image" src="https://github.com/user-attachments/assets/b684e4c1-cf39-4e04-be08-02e9cdd0233f" />
<img width="118" height="326" alt="image" src="https://github.com/user-attachments/assets/264c0011-540a-4e5b-9666-c4edff705f12" />


5. Haz clic en **Save**.

Cuando actualices la página, tu entorno de práctica debería aparecer en la lista de entornos. Es posible que el aprovisionamiento del entorno tarde unos minutos.

### Tarea 2.2 - Crear una solución
6. Navega a `https://make.powerapps.com`.
   
7. En la cinta superior, asegúrate de que estás trabajando en tu nuevo entorno. Si no es el caso, haz clic en el nombre para abrir la lista de entornos disponibles y seleccioan el tuyo.
<img width="300" height="326" alt="image" src="https://github.com/user-attachments/assets/7c6063b5-69f7-4072-96e4-f1a8f372a430" />

8. En el menú lateral de la izquierda, navega a **Solutions**.
   
9. En la barra de comandos, selecciona **+ New Solution**.
   
10. Configura la nueva solución con estas propiedades:
   * **Display Name:** Event Management
   * **Name:** EventManagement
   * **Publisher**: EventMSLearn
   * **Version**: _Usa el valor por defecto_
   * **Set as your preferred solution:** Marcado
  > [!WARNING]
  > Este publisher todavía no existe, así que primero tenemos que crearlo.
  > 
  > Bajo el menú desplegable, selecciona **+ New Publisher**. Configura el nuevo editor (publisher) con estas propiedades:
  > 
  > * **Display name:** EventMSLearn
  > 
  > * **Name:** EventMSLearn
  > 
  > * **Prefix:** mslearn
  > 
  > * **Choice value prefix:** _Usa el valor por defecto_

11. Selecciona **Create** para crear la solución.


### Tarea 2.3 - Importar una solución (OPCIONAL)
Si **NO** vas a completar el Lab 1 antes de continuar con el resto de ejercicios, necesitas importar una solución con el modelo de datos que utilizaremos para crear aplicaciones y flujos automatizados.

> [!IMPORTANT]
> Las tablas **no** incluyen datos de ejemplo, ya que estos no se almacenan en la solución.

12. Dentro de este repositorio, ve a la carpeta **Files** y descarga el archivo zip **Event Management 1.0.0.0**.
    
14. Si es necesario, en el menú lateral de la izquierda, navega a **Solutions**. (Si ya estás dentro de una solución, dale al botón <img width="20" height="20" alt="image" src="https://github.com/user-attachments/assets/11ef5f3e-8811-4228-9070-1bf69fa6078c" /> para volver atrás.)

15. En la barra de comandos, selecciona **Import solution**.

16. Haz clic **Browse** y selecciona el archivo zip de la solución **Event Management 1.0.0.0**.

17. Select **Next**.

18. Select **Import** and wait for the solution to import.

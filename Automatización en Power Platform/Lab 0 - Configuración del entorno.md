# Laboratorio 0 - Preparar el entorno del laboratorio

> [!IMPORTANT]
> En este curso, utilizarás un usuario de Microsoft 365 con las licencias de Power Platform necesarias para completar este curso.
Este usuario será eliminado una semana después de finalizar el curso.
>
> Microsoft 365 y Power Platform están evolucionando constantemente. Las instrucciones en este documento pueden diferir de la experiencia real con las herramientas.

## Escenario
En este ejercicio crearás un entorno e importarás una solución que incluya todos los datos y aplicaciones necesarios para hacer nuestros flujos y agentes. Vamos a utilizar dos soluciones: Ahti's, un equipo de mantenimiento, y Contoso Coffee, una distribuidora de máquinas cafeteras de alta gama.

## Ejercicio 1: Acceso a Microsoft Power Platform
En este ejercicio, vas a verificar tu acceso a Power Automate.

1. En una pestaña nueva de tu navegador, navega a `https://make.powerautomate.com`.
> [!NOTE]
> Si ya utilizas Power Automate con tu propia cuenta, abre la pestaña en modo incógnito o utiliza otro navegador.

2. Inicia sesión con las credenciales de Microsoft 365 que te ha proporcionado tu instructor.

3. Opcionalmente, selecciona **Sí** para mantener la sesión conectada en este navegador.

> [!WARNING]
> Las instrucciones de los laboratorios asumen que estás trabajando en Power Platform en inglés. Si trabajas en español, ten en cuenta que algunos términos pueden cambiar significativamente.
>
> Puedes cambiar el idioma seleccionando el icono de **Configuración** <img width="13" height="15" alt="image" src="https://github.com/user-attachments/assets/464d1353-b0ec-4fed-8202-e93f2a10eb04" /> > **Configuración de Power Automate** > **Lenguaje**.

<img width="300" height="326" alt="image" src="https://github.com/user-attachments/assets/e1903cf4-fa3a-49e9-bdc8-ef1f7d510f48" />
<img width="625" height="326" alt="image" src="https://github.com/user-attachments/assets/295281c9-2eb3-400a-a0c1-3e50f3e96fb4" />


## Ejercicio 2: Crear un entorno
4. En una pestaña nueva de tu navegador, navega a `https://admin.powerplatform.microsoft.com`.
   
5. En el menú lateral a la izquierda, selecciona **Manage**.

6. En la cinta superior, selecciona **New**.

7. Configura el nuevo entorno con estas propiedades (deja el resto con los valores por defecto):
   - **Name:** Dev One <NúmeroDeAlumno>. **Ejemplo: Dev One Alumno01**
   - **Type:** Developer
   - **Language:** English (United States)
   - **Currency:** USD ($)
   - **Deploy sample apps and data?:** Yes
<img width="116" height="326" alt="image" src="https://github.com/user-attachments/assets/b684e4c1-cf39-4e04-be08-02e9cdd0233f" />
<img width="118" height="326" alt="image" src="https://github.com/user-attachments/assets/264c0011-540a-4e5b-9666-c4edff705f12" />


8. Haz clic en **Save**.

Cuando actualices la página, tu entorno de práctica debería aparecer en la lista de entornos. Es posible que el aprovisionamiento del entorno tarde unos minutos.

## Ejercicio 3: Importar soluciones

> [!IMPORTANT]
> Las tablas que importes están vacías y **no** incluyen datos de ejemplo, ya que estos no se almacenan en la solución.

### Tarea 3.1 - Importar la solución Maintenance Management

9. Dentro de este repositorio, ve a la carpeta **Files** y descarga el archivo zip **Maintenance Management 1.0.0.1**.
    
10. Vuelve a Power Automate. En el menú lateral de la izquierda, navega a **Solutions**. 

11. En la barra de comandos, selecciona **Import solution**.

12. Haz clic **Browse** y selecciona el archivo zip de la solución **Maintenance Management 1.0.0.1**.

13. Selecciona **Next**.

14. Selecciona **Import** y espera a que la solución se importe. Esto puede tardar unos minutos.


### Tarea 3.2 - Explora la solución Maintenance Management

15. Una vez la solución se haya importado correctamente, en esta misma pestaña **Solutions**, haz clic en el nombre de la solución **Maintenance Management** para abrirla.
    
16. En la cinta superior, selecciona **Publish all customizations** y espera a que se complete la publicación.
    
17. Explora los componentes de esta solución como desees para familiarizarte con ellos:
    - Tres tablas **Contact**, **Location** y **Maintenance Request**.
    - Una aplicación de Power Apps llamada **Maintenance Management**. (Puedes ejecutarla seleccionándola y haciendo clic en <img width="8" height="12" alt="image" src="https://github.com/user-attachments/assets/0509b7ed-3778-4100-9f06-5c16e44b7323" /> **Play**).


### Tarea 3.3 - Importar la solución Agents in a Day

18. Dentro de este repositorio, ve a la carpeta **Files** y descarga el archivo zip **AgentsinaDay_1_0_0_13_DoNotUnzip**.
    
19. Vuelve a Power Automate. En el menú lateral de la izquierda, navega a **Solutions**. 

20. En la barra de comandos, selecciona **Import solution**.

21. Haz clic **Browse** y selecciona el archivo zip de la solución **AgentsinaDay_1_0_0_13_DoNotUnzip**.

22. Selecciona **Next**.

23. Selecciona **Import** y espera a que la solución se importe. Esto puede tardar unos minutos.


### Tarea 3.4 - Explora la solución Agents in a Day

24. Una vez la solución se haya importado correctamente, en esta misma pestaña **Solutions**, haz clic en el nombre de la solución **Agents in a Day** para abrirla.
    
25. En la cinta superior, selecciona **Publish all customizations** y espera a que se complete la publicación.
    
26. Explora los componentes de esta solución como desees para familiarizarte con ellos.


---
## 🎉 **¡Felicidades!** Ya estás preparado para continuar con los siguientes laboratorios. 🎉

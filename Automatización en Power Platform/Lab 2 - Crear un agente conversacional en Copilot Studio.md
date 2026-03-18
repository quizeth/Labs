# Laboratorio 1 - Crear un agente conversacional en Copilot Studio

> [!IMPORTANT]
> Para hacer este laboratorio, es necesario haber completado el [Lab 0 - Configuración del entorno del laboratorio](https://github.com/quizeth/Labs/blob/main/Automatización%20en%20Power%20Platform/Lab%200%20-%20Configuración%20del%20entorno.md).
>
> Si es necesario, puedes registrarte para una prueba de Copilot Studio con una cuenta profesional o educativa aquí: [Copilot Studio trial sign-up](https://go.microsoft.com/fwlink/?LinkId=2107702).

## Escenario
Contoso Coffee es un distribuidor internacional de máquinas de café de alta gama. La organización quiere crear un agente personalizado que estandarice el proceso de servicio al cliente para facilitar la comprensión de su política de devoluciones. Actualmente, Contoso recibe preguntas a través de varios canales (email, teléfono, chat...). La empresa busca reducir el esfuerzo manual que requiere responder todas estas preguntas.


## Ejercicio 1: Prepara los datos de tu tabla
Antes de empezar a construir un agente, vamos a explorar la app de pedidos de máquinas y simular la experiencia de un usuario comprando cafeteras.
1. Ve a Copilot Studio: `https://copilotstudio.microsoft.com`

2. En Copilot Studio, asegúrate de que estás en el entorno **Dev One <NúmeroDeAlumno>**.

3. En el menú a la izquierda, haz clic en las elipsis (. . .) y selecciona **Solutions**.

<img width="388" height="326" alt="image" src="https://github.com/user-attachments/assets/4f0c49b2-4eaa-49c5-a358-bdcfcd44f1f8" />

4. Abre la solución **Agents in a Day**.

5. Desde el menú de la izquierda, navega a la pestaña **Apps**.

6. Selecciona las elipsis (. . .) junto a **Machine Order App** y haz clic en **Play** para ejecutar la aplicación.

> [!NOTE]
> Es posible que necesites habilitar la conexión con **Office 365 Users**. Si es el caso, selecciona **Allow** en la ventana emergente al abrir la aplicación.

7. Selecciona la caja de comparación bajo una de las máquinas de la que querrías hacer un pedido. Después, haz clic en el botón **Compare items** en la esquina inferior derecha de la app para navegar a la pantalla de comparación.
<img width="575" height="326" alt="image" src="https://github.com/user-attachments/assets/6c0c69bb-2cb5-4a74-aa6f-8b0479648e14" />

8. Desde la pantalla de comparación, haz clic en el botón **Submit machine request** en la esquina inferior derecha para iniciar el pedido de la máquina.

9. Una vez se ha enviado el pedido de la máquina, se añade un registro a la tabla de pedidos. En la pantalla de Success, haz clic en **OK** para volver a la lista de máquinas.

10. Repite los pasos 7 a 9 un par de veces más para añadir múltiples registros a la tabla de pedidos.

11. Vuelve a la pestaña con tu solución **Agents in a Day** y selecciona **Tables** en el menú de navegación de la izquierda para ver las tablas disponibles en la solución.

12. Desde la página **Tablas**, selecciona la tabla **Machine Order** para ver los registros que has creado anteriormente.
    - En la página de la tabla **Machine Order**, puedes ver una lista de los registros que has creado en la parte inferior de tu pantalla en la sección **Machine Order columns and data**.
    - Para ver las columnas Order ID y Order Status, es posible que necesites añadirlas. Para esto, haz clic en el botón **more v** para abrir el menú desplegable y selecciona las columnas **Order ID** y **Order Status** antes de hacer clic en **Save**.

<img width="784" height="695" alt="image" src="https://github.com/user-attachments/assets/bbe1d4a6-110e-4e4c-8f93-cddf8316288a" />

## Ejercicio 2: Crear un agente en Copilot Studio
Ahora que conoces mejor el proceso de pedidos de Contoso Coffee y tienes datos de ejemplo para trabajar, vas a crear un agente diseñado para mejorar y ampliar este proceso.

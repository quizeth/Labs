# Laboratorio 2 - Crear un agente conversacional en Copilot Studio

> [!IMPORTANT]
> Para hacer este laboratorio, es necesario haber completado el [Lab 0 - Configuración del entorno del laboratorio](https://github.com/quizeth/Labs/blob/main/Automatización%20en%20Power%20Platform/Lab%200%20-%20Configuración%20del%20entorno.md).
>
> Si es necesario, puedes registrarte para una prueba de Copilot Studio con una cuenta profesional o educativa aquí: [Copilot Studio trial sign-up](https://go.microsoft.com/fwlink/?LinkId=2107702).

## Escenario
Contoso Coffee es un distribuidor internacional de máquinas de café de alta gama. La organización quiere crear un agente personalizado que estandarice el proceso de servicio al cliente para facilitar la comprensión de su política de devoluciones. Actualmente, Contoso recibe preguntas a través de varios canales (email, teléfono, chat...). La empresa busca reducir el esfuerzo manual que requiere responder todas estas preguntas.


## Ejercicio 1: Prepara los datos
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

<img width="367" height="326" alt="image" src="https://github.com/user-attachments/assets/bbe1d4a6-110e-4e4c-8f93-cddf8316288a" />

## Ejercicio 2: Crear un agente en Copilot Studio
Ahora que conoces mejor el proceso de pedidos de Contoso Coffee y tienes datos de ejemplo para trabajar, vas a crear un agente diseñado para mejorar y ampliar este proceso.

### Tarea 2.1: Crear un agente
13. Desde la solución **Agents in a Day**, haz clic en el botón **+ New** > **Agent** en la cinta superior.

Esto te llevará al diseñador del agente. La interfaz está diseñada para trabajar mediante una conversación usando lenguaje natural y personalizar la información básica del agente como su nombre, tono, instrucciones, y datos de _grounding_.

14. Escribe el siguiente prompt en la caja **What would you like to build**? y haz clic en **Send**:
    `An intelligent agent for the company Contoso Coffee that helps customers with machine orders. Let's name it Contoso Support Agent. Contoso Support Agent should only reveal information from provided sources, stick to products in the Contoso domain, and only answer questions that pertain to public information that's available for Contoso Coffee. Contoso Support Agent should interact in a friendly and professional manner, using clear language, helpful tone, and light emojis when appropriate. It should feel approachable, smart, and a bit curious like a knowledgeable companion.`

> [!NOTE]
> Este prompt proporciona instrucciones paso a paso sobre cómo se espera que responda el agente. Al proporcionar esta información ahora, el agente estará preconfigurado en base a estas especificaciones en vez de tener que proporcionar todos estos valores de forma manual en el diseñador del agente.


Tras enviar el prompt, Copilot toma unos momentos para ayudarte a crear el agente desde cero, incluyendo todos los detalles del agente que pueda desde tu prompt. Cuando el agente haya terminado de aprovisionarse, examina qué ha configurado Copilot y asegúrate de que coincide con las instrucciones del prompt.

> [!NOTE]
> Cuando Copilot construye nuestro agente, la creación del **Contoso Support Agent** puede variar entre usuarios.

15. Tras unos momentos, deberías tener un agente bien preparado para asistir con los pedidos de máquinas de **Contoso Coffee**. Antes de finalizar el agente, necesitas verificar que esté incluido en la solución correcta. Para esto, en la esquina superior derecha, selecciona la opción **Settings**.

16. Desde aquí, selecciona la pestaña **Advanced** y navega para ver la opción **View Solutions**.
   
17. Confirma que la solución sea **Agents in a Day**.

18. Vuelve al diseñador del agente y explora sus funcionalidades y características.

## Ejercicio 3: Añade fuentes de conocimiento
Muchas de las máquinas de café que se devuelven a Contoso están dañadas o no vienen en su caja original. Te interesa entrenar al agente en los requisitos de una devolución válida para que quede clar en qué condición deberían estar los productos para poder aceptar su devolución. Para esto, vamos a añadir varias fuentes de conocimiento específicas a nuestro agente.

19. Desde la página de nuestro agente, navega a la pestaña **Knowledge** en la cinta superior.

20. Selecciona el botón **+ Add knowledge** para añadir una nueva fuente de conocimiento.

21. En la ventana emergente **Add Knowledge**, selecciona **Dataverse**.

22. En la barra de búsqueda, busca y selecciona la tabla **Machine Order**.

23. Haz clic en el botón **Add to agent** en la esquina inferior derecha.

24. Selecciona el **Name** de la tabla Machine Orders para abrir configuración adicional disponible cuando añadimos tablas de Dataverse.

25. En el menú de configuración adicional, selecciona la pestaña **Preview** para ver todos los datos a los que tiene acceso el agente. El agente puede hacer referencia a todas estas filas y columnas.

> [!WARNING]
> Tras unos momentos, la tabla Machine Orders de Dataverse estará disponible y enlazada a tu agente. Es posible que tarde unos minutos en cargar.
>
> La columna **Status** informa de cuándo está lista una fuente de conocimiento. Actualiza la página y espera hasta que la fuente esté marcada como **Ready**.

Ahora que tienes datos internos proporcionados a través de una tabla de Dataverse, vamos a importar datos externos en un PDF.

26. Dentro de este repositorio, ve a la carpeta Files y descarga el archivo pdf **Contoso Coffee Official Return Policy**.

27. En la página de tu agente en Copilot Studio, vuelve a la página **Knowledge** y haz clic en el botón **+ Add knowledge** para añadir otra fuente.

28. En la ventana emergente, haz clic en el texto **select to browse** para abrir una ventana de explorador de archivos. También puedes arrastrar directamente el archivo desde tu escritorio.

29. En la ventana emergente, selecciona el archivo descargado **Contoso Coffee Official Return Policy**.

30. Proporciona uhn **Name** y **Description** para la fuente de conocimiento. Esto ayuda a que tanto el agente como los co-creadores entiendan el propósito de esta fuente.

31. Selecciona **Add to agent** para añadir el PDF al agente.

Tras unos momentos, el archivo estará disponible y enlazado a tu agente. Es posible que tarde unos minutos en cargar.

Una vez todas las fuentes de conocimiento estén listas, puedes probar las funcionalidades del agente en el panel **Test** a la derecha. Cuando el modo de orquestación está establecido en **Generative**, el agente puede utilizar las fuentes de conocimiento directamente y fundamentar sus respuestas en los datos de estas fuentes.

32. Introduce el siguiente prompt en el panel de tests (**reemplaza** `<your email>` con el email del usuario que estés utilizando) y examina la respuesta:
   `What orders were requested by <your email>?`

33. Abre un nuevo chat en el panel de tests e introduce el siguiente prompt y examina la respuesta:
   `Are customers required to return items in their original packaging?`


## Ejercicio 4: Añade una herramienta al agente
En este ejercicio añadirás tu primera herramienta al agente. Vas a usar la herramienta **prompt** para atender a usuarios que están frustrados y requieren una respuesta más empática de lo que típicamente está indicado en las instrucciones del agente. 

Las herramientas de prompt son respuestas personalizadas que se generan en circunstancias específicas. En este caso, el mensaje personalizado dará una respuesta más empática y dirigirá al usuario a recursos como la página web de contoso.

34. En la cinta superior del agente, selecciona la pestaña **Tools**.

35. Haz clic en el botón **+ Add a tool**.

36. En la ventana emergente **Add Tool**, selecciona el botón **Prompt** en la sección **Create New**.

    Tras crear la nueva herramienta de prompt, la plataforma te redirigirá a la página de configuración del prompt. Esta página tiene dos partes principales: las instrucciones y la respuesta del modelo.

37. Selecciona el nombre del prompt en la zona superior izquierda y renómbralo a `Customer Issue Prompt`.

38. En la sección **Instrucciones**, selecciona el botón **+ Add content** para añadir una entrada de texto.

39. En el menú **Input** que aparece, selecciona la opción **Text**.

40. Establece el **Name** en `Customer Issue`.

41. Añade el siguiente prompt al campo **Sample data**: `I'm having issues with my AirPot not heating to the desired temperature.`

42. Haz clic en **Close** para añadir la entrada a las instrucciones.

Ahora que tienes un input configurado, puedes crear un set de instrucciones que utilice esta entrada como contenido dinámico, sustituyendo la burbuja de **Customer Issue** con el prompt del usuario.

43. Actualiza la sección **Instructions** con el siguiente texto: `Using [Customer Issue], provide a thoughtful and empathetic response to show your concern for their issue. Make sure to direct them to the Contoso Coffee website that includes hardware malfunction documentation.`

44. Para probar la herramienta, haz clic en el botón **Test**. Esto generará una respuesta del modelo usando los datos de ejemplo que hemos proporcionado.

45. Para guardar el prompt, haz clic en **Save**.

46. De vuelta en la ventana de **Add Tool**, selecciona **Add and configure**. La herramienta ahora se muestra en la página **Tools** del agente.

47. Por último, vas a modificar las instrucciones del agente para que sepa cuándo usar este prompt. Navega a la página **Overview** del agente y haz clic en el botón del lápiz **Edit** en la sección **Instructions**.

48. Para añadir el **Customer Issue Prompt** directamente desde las instrucciones del agente, escribie `/` para abrir el menú de contenido dinámico y seleccionar el **Customer Issue Prompt**.

49. En la sección **Instructions**, añade el siguiente texto al final:
    `When a user presents an issue with a machine: 
    - Use the [Customer Issue Prompt]`
   
<img width="485" height="326" alt="image" src="https://github.com/user-attachments/assets/8b470fce-b9da-4d2c-b9c5-a1efbfa10610" />

50. Para probar nuestros cambios, introduce el siguiente prompt en el panel de test a la derecha: `I'm having issues with my Travel Brew 100 machine.`

51. Si el agente te pregunta cuál es el problema, responde con un prompt similar a: `It's not turning on after I press the power button.`

Deberías recibir una respuesta empática personalizada según los problemas que estés teniendo con la máquina. 


---
## 🎉 **¡Felicidades!** Has creado tu primer agente en Copilot Studio. 🎉

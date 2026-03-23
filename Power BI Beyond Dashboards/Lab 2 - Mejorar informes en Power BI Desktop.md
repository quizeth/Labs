- Audiencia: Analista
- Tipo de informe: Informe analítico
- Requisitos de UI:
	- El informe se consumirá principalmente desde ordenadores de escritorio, pero también hace falta una versión adaptada para dispositivos móviles
	- El método de entrada principal será teclado y ratón
	- No hay un tema corporativo
- Requisitos de UX:
	- Capacidad de filtrar 

## Requisitos
Para completar este laboratorio, necesitas cumplir los siguientes requisitos:
- Tener Power BI Desktop instalado.
- Descargar los siguientes archivos de la carpeta **Files**.
	- Sales Analysis - Starter
	- adventure works logo

## Ejercicio 1: Mejorar el diseño del informe

### Tarea 1.1: Crear página plantilla
Antes de nada, vamos a crear una nueva página. Esta va a ser la plantilla que utilizaremos para que todas las páginas del informe sigan la misma estructura.

1. En la cinta inferior, haz clic en el símbolo de + para añadir una nueva página.

2. Renombra la página a **Template**.

3. Haz clic derecho en la pestaña de la página y selecciona **Hide**.

4. En el panel de visualizaciones, haz clic en el segundo botón **Format your report page**.

5. En la sección **Canvas background**, selecciona **White, 10% darker** (el primer gris en la esquina superior izquierda).

6. Establece la **Transparency** en **0%**.

7. En la cinta superior, ve a la pestaña **Insert**.

8. Selecciona **Image**.

9. En el panel derecho **Format image**, bajo la sección **Style**, ve al área **Image**. Aquí, haz clic en **Browse...**.

10. Escoge el archivo **adventure works logo**.

11. En el mismo panel, bajo la pestaña **General**, configura las **Properties** con los siguientes valores:
	- Size > **Height**: 75
	- Size > **Width**: 200
	- Position > **Horizontal**: 10
	- Position > **Vertical**: 10
	
12. En la cinta superior,  haz clic en **Text box** para añadir un cuadro de texto.

13. En el cuadro de texto, escribe `Sales Analysis`.

14. Configura las propiedades del texto en la ventana emergente:
	- **Fuente**: Segoe UI
	- **Tamaño**: 32

15. En el panel derecho **Format text box**, configura las **Properties** con los siguientes valores:
	- Size > **Height**: 75
	- Size > **Width**: 400
	- Position > **Horizontal**: 230
	- Position > **Vertical**: 10

16. En el panel derecho, bajo la sección **Effects**, haz clic en **Background** y cambia el botón a **Off**.

### Tarea 1.2: Pasar las páginas existentes a la plantilla
17. En la cinta inferior de páginas, haz clic derecho en la página **Template** y selecciona **Duplicate**.

18. Ve a la página **Overview**.

19. Selecciona y copia las dos segmentaciones **Year** y **Region**.

20. Copia ambas segmentaciones en la página **Duplicate of Template**. Cuando te pregunte si quieres sincronizar las segmentaciones, selecciona **Sync**.

21. Con ambas segmentaciones seleccionadas, haz clic en la pestaña **Format your visual** del panel de **Visualizations** (segundo botón).

22. Bajo **Slicer settings**, en la sección **Options**, selecciona el **Style** Dropdown.

23. Dentro de la pestaña de formato, ve a la pestaña **General**.

24. Configura las **Properties** con los siguientes valores:
	- Size > **Height**: 75
	- Size > **Width**: 180
	- Position > **Horizontal**: 1090
	- Position > **Vertical**: 10

25. En el panel derecho, bajo la sección **Effects**, haz clic en **Background** y cambia el botón a **Off**.

26. Selecciona una de las segmentaciones únicamente y establece la  Position > **Horizontal** en 900. 

27. En la cinta superior,  haz clic en **Shapes** para añadir un **Rectangle**.

28. En el panel **Format shape**, configura las **Properties** con los siguientes valores:
	- Size > **Height**: 350
	- Size > **Width**: 1150
	- Position > **Horizontal**: 65
	- Position > **Vertical**: 95

29. Vuelve a la página **Overview**.

30. Copia el gráfico de barras y líneas **Sales and Profit Margin by Month** y pégalo en la página **Duplicate of Template**.

28. En el panel **Format visual **, configura las **Properties** con los siguientes valores:
	- Size > **Height**: 330
	- Size > **Width**: 1150
	- Position > **Horizontal**: 65
	- Position > **Vertical**: 95

29. En la cinta superior, ve a la pestaña **View**.

30. Haz clic en **Selection** para abrir el panel.

31. Arrastra el objeto **Sales and Profit Margin by Month** arriba del todo de la lista.
## Ejercicio 2: Mejorar la navegación e interactividad del informe
## Ejercicio 3: Añadir los elementos finales

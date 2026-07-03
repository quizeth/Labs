## Laboratorio 2: Configurar un modelo semántico en Power BI
  
**ParcelCraft** ya dispone de un modelo inicial preparado en Power Query a partir del archivo **ParcelCraft_50k.csv**.  
En el laboratorio anterior separaste una tabla plana logística en una tabla de hechos llamada **Envíos** y varias dimensiones: **Cliente**, **Servicio**, **Oficina**, **Destino** y **Ruta**.  
El objetivo de este laboratorio es convertir esas consultas cargadas en un **modelo semántico analítico**, configurando relaciones, propiedades de tablas y columnas, jerarquías, formato de campos y una primera medida rápida.

Durante el laboratorio trabajarás principalmente en las vistas **Modelo** y **Datos** de Power BI Desktop. También revisarás cómo el diseño del modelo afecta directamente a la experiencia de creación de informes: filtros que se propagan correctamente, campos legibles para negocio, columnas ocultas, categorías de datos y agregaciones predeterminadas.

ℹ️ El enfoque está alineado con prácticas habituales de modelado en Power BI: primero se prepara el modelo físico, después se configuran relaciones y propiedades semánticas, y finalmente se deja el conjunto de datos listo para crear cálculos DAX y visualizaciones fiables.

## Objetivos de aprendizaje
  
**Tiempo estimado total: 45-60 minutos**  
Al finalizar este laboratorio serás capaz de:
- Crear y validar relaciones entre hechos y dimensiones.
- Configurar cardinalidad, dirección de filtro y relaciones activas.
- Mejorar la usabilidad del modelo mediante propiedades de tablas y columnas.
- Ocultar campos técnicos y definir formatos, agregaciones y categorías de datos.
- Crear jerarquías de análisis para navegación y drill-down.
- Crear una medida rápida y validar su comportamiento en informes.
- Aplicar buenas prácticas para mantener un modelo semántico claro y escalable.

---

## Antes de empezar

### Requisitos
  
Para completar el laboratorio necesitas:
- Power BI Desktop instalado.
- Conocimientos básicos de modelo en estrella y relaciones uno a muchos.
- **Empezar desde el archivo inicial**.

> Tienes un archivo inicial y solución disponibles.
> - [Archivo inicial (Starter)](https://github.com/quizeth/Labs/blob/main/Explotaci%C3%B3n%20y%20Procesamiento%20de%20Datos%20con%20Power%20BI/Intermedio/Files/02%20Starter%20-%20ParcelCraft.pbix)
> - [Archivo final (Solución)](https://github.com/quizeth/Labs/blob/main/Explotaci%C3%B3n%20y%20Procesamiento%20de%20Datos%20con%20Power%20BI/Intermedio/Files/02%20Soluci%C3%B3n%20-%20ParcelCraft.pbix)

### Convenciones usadas en el laboratorio
- Cuando se indique un campo con el formato **Tabla | Columna**, la primera parte representa la tabla y la segunda parte representa la columna.
- Cuando se indique una opción de cinta, se mostrará como **pestaña > grupo > comando**.
- Si algún nombre difiere ligeramente en tu archivo, usa la columna equivalente creada durante el Lab 1.
- En este laboratorio no se crearán transformaciones nuevas en Power Query, salvo que se indique expresamente.
- El objetivo es configurar el **modelo semántico**, no rehacer la ingesta ni la limpieza de datos.

---

## Ejercicio 1: Revisar el modelo importado
  
En este ejercicio revisarás el modelo inicial del laboratorio y lo compararás con el modelo construido al finalizar el Laboratorio 1. Aunque ambos modelos parten del mismo dataset de **ParcelCraft**, el archivo inicial de este laboratorio puede incluir una estructura más preparada para trabajar en la vista de modelo: tablas cargadas, nombres funcionales, columnas clave disponibles y algunas decisiones de organización ya aplicadas.

El objetivo de esta exploración no es modificar todavía el modelo, sino entender qué elementos están listos, qué elementos faltan y qué ajustes vas a realizar durante el laboratorio.


### Tarea 1: Abrir el archivo de Power BI

1. Abre **Power BI Desktop**.
2. Abre el archivo inicial del Laboratorio 2:
   - [**02 Starter - ParcelCraft.pbix** ](https://github.com/quizeth/Labs/blob/main/Explotaci%C3%B3n%20y%20Procesamiento%20de%20Datos%20con%20Power%20BI/Intermedio/Files/02%20Starter%20-%20ParcelCraft.pbix)
3. Si aparece una advertencia indicando que hay cambios pendientes, selecciona **Aplicar cambios**.
4. Espera a que finalice la carga del modelo.
5. Cambia a la vista **Modelo**.

### Tarea 2: Revisar las tablas cargadas

1. En el panel **Datos**, comprueba que aparecen las siguientes tablas principales:
   - **Envíos**
   - **Cliente**
   - **Servicio**
   - **Oficina**
   - **Destino**
   - **Ruta**

> En el Laboratorio 1 creaste algunas de estas tablas desde una tabla plana usando Power Query. En este laboratorio partirás de un modelo ya preparado para centrarse en la configuración semántica: relaciones, propiedades, formatos, jerarquías y experiencia de usuario.

### Tarea 3: Comparar este modelo con la solución del Laboratorio 1

1. Revisa la estructura del modelo actual y compárala con el resultado esperado del Laboratorio 1.
2. Identifica qué elementos ya están disponibles:
   - una tabla de hechos llamada **Envíos**;
   - dimensiones separadas para cliente, servicio, oficina, destino y ruta;
   - columnas identificadoras como **ClienteID**, **ServicioID**, **OficinaID**, **DestinoID** y **RutaID**;
   - columnas descriptivas para análisis, como cliente, servicio, región, ciudad, estado, peso, precio e intentos.

> El modelo final del Laboratorio 1 estaba centrado en la **preparación de datos**. Este modelo inicial del Laboratorio 2 está orientado a la **configuración semántica**. Por eso, aunque las tablas puedan parecer similares, el foco cambia: ahora debes comprobar si esas tablas se comportan correctamente como un modelo analítico en Power BI.

### Tarea 4: Identificar el problema de un modelo sin relaciones

1. Cambia a la vista **Informe**.
2. Crea un visual de **Tabla**.
3. Agrega el campo **Servicio | Servicio**.
4. Agrega el campo **Envíos | Precio total**.
5. Observa el resultado.

Si el modelo no tiene relaciones correctamente configuradas, Power BI no puede propagar el filtro desde **Servicio** hacia **Envíos**. El resultado suele ser que el total de ingresos se repite para cada servicio, porque la tabla de dimensión no está filtrando la tabla de hechos.

### Tarea 5: Documentar las diferencias observadas

1. Anota qué tablas están presentes en el modelo.
2. Anota si existen relaciones ya creadas.
3. Anota si las relaciones, en caso de existir, parecen correctas.
4. Anota qué columnas técnicas siguen visibles en el panel **Datos**.
5. Anota qué campos necesitarán formato o configuración adicional.

Esta revisión inicial te ayudará a entender qué parte del trabajo procede de Power Query y qué parte pertenece al modelo semántico. En Power BI, un modelo no está terminado cuando los datos cargan correctamente: también debe ser comprensible, navegable y fiable para los usuarios que crearán informes.

**Resultado esperado:** has identificado que el archivo inicial del Lab 2 parte de un modelo más preparado que la tabla plana original, pero todavía requiere configuración semántica para funcionar correctamente en análisis e informes.


## Ejercicio 2: Crear relaciones del modelo en estrella
  
En este ejercicio crearás relaciones desde las dimensiones hacia la tabla de hechos **Envíos**. El diseño objetivo es un modelo en estrella: las dimensiones filtran la tabla de hechos, y la tabla de hechos concentra las métricas transaccionales.

### Tarea 1: Abrir el administrador de relaciones

1. Cambia a la vista **Modelo**.
2. En la cinta, selecciona **Inicio > Administrar relaciones**.
3. Revisa si existen relaciones detectadas automáticamente.
4. Si hay relaciones incorrectas o ambiguas, elimínalas antes de continuar.
5. Reorganiza las tablas para que la tabla `Envíos` esté en el centro, con el resto de dimensiones alrededor.
<img width="1034" height="710" alt="image" src="https://github.com/user-attachments/assets/4219200e-a26a-4087-8755-e829ca5efe4c" />

7. Selecciona **Nuevo** para crear la primera relación.

### Tarea 2: Crear la relación Cliente-Envíos

1. Configura una relación con estos valores:

<table>
<tr>
<th>Propiedad</th>
<th>Valor</th>
</tr>
<tr>
<td>Tabla principal</td>
<td>Cliente</td>
</tr>
<tr>
<td>Columna principal</td>
<td>ClienteID</td>
</tr>
<tr>
<td>Tabla relacionada</td>
<td>Envíos</td>
</tr>
<tr>
<td>Columna relacionada</td>
<td>ClienteID</td>
</tr>
<tr>
<td>Cardinalidad</td>
<td>Uno a varios (1:*)</td>
</tr>
<tr>
<td>Dirección de filtro cruzado</td>
<td>Única</td>
</tr>
<tr>
<td>Activar esta relación</td>
<td>Sí</td>
</tr>
</table>

2. Selecciona **Aceptar**.

La tabla **Cliente** debe contener una fila por cliente, mientras que **Envíos** puede contener muchos envíos para un mismo cliente. Por eso la cardinalidad correcta es **uno a varios**.

### Tarea 3: Crear la relación Servicio-Envíos

1. En **Administrar relaciones**, selecciona **Nuevo**.
2. Crea la relación:
   - **Servicio | ServicioID** con **Envíos | ServicioID**.
   - Cardinalidad: **Uno a varios (1:*)**.
   - Dirección de filtro cruzado: **Única**.
   - Relación activa: **Sí**.
3. Selecciona **Aceptar**.

### Tarea 4: Crear la relación Oficina-Envíos

1. En **Administrar relaciones**, selecciona **Nuevo**.
2. Crea la relación:
   - **Oficina | OficinaID** con **Envíos | OficinaID**.
   - Cardinalidad: **Uno a varios (1:*)**.
   - Dirección de filtro cruzado: **Única**.
   - Relación activa: **Sí**.
3. Selecciona **Aceptar**.

### Tarea 5: Crear la relación Destino-Envíos

1. En **Administrar relaciones**, selecciona **Nuevo**.
2. Crea la relación:
   - **Destino | DestinoID** con **Envíos | DestinoID**.
   - Cardinalidad: **Uno a varios (1:*)**.
   - Dirección de filtro cruzado: **Única**.
   - Relación activa: **Sí**.
3. Selecciona **Aceptar**.

Si en tu tabla **Envíos** todavía no existe la columna **DestinoID**, vuelve al Lab 1 y completa la combinación con la dimensión **Destino** antes de continuar.

### Tarea 6: Crear la relación Ruta-Envíos

1. En **Administrar relaciones**, selecciona **Nuevo**.
2. Crea la relación:
   - **Ruta | RutaID** con **Envíos | RutaID**.
   - Cardinalidad: **Uno a varios (1:*)**.
   - Dirección de filtro cruzado: **Única**.
   - Relación activa: **Sí**.
3. Selecciona **Aceptar**.
4. Cierra la ventana **Administrar relaciones**.

### Tarea 7: Validar visualmente el diagrama

1. En la vista **Modelo**, organiza las tablas así:
   - **Envíos** en el centro.
   - Dimensiones alrededor: **Cliente**, **Servicio**, **Oficina**, **Destino**, **Ruta**.
2. Comprueba que todas las líneas de relación llegan a **Envíos**.
3. Comprueba que el extremo **1** está en las dimensiones y el extremo **\*** está en **Envíos**.
4. Comprueba que la flecha de filtro avanza desde cada dimensión hacia **Envíos**.

**Resultado esperado:** el modelo muestra una estrella limpia, con la tabla de hechos en el centro y relaciones 1:* desde las dimensiones.

## Ejercicio 3: Validar propagación de filtros
  
En este ejercicio crearás visualizaciones simples para confirmar que las relaciones funcionan correctamente.

### Tarea 1: Crear una tabla de validación por servicio

1. Cambia a la vista **Informe**.
2. Crea una visualización de **Tabla**.
3. Agrega estos campos:
   - **Servicio | Servicio**
   - **Envíos | Precio total**
   - **Envíos | EnvíoID**
4. En el panel de campos del visual, cambia la agregación de **EnvíoID** a **Recuento**.
5. Comprueba que cada servicio muestra un importe y un recuento diferente.

### Tarea 2: Crear un segmentador de cliente

1. Agrega un visual de **Segmentación de datos**.
2. Usa el campo **Cliente | Segmento**.
3. Selecciona un segmento.
4. Observa que la tabla por servicio se filtra automáticamente.

### Tarea 3: Validar por destino

1. Agrega una segunda visualización de **Tabla**.
2. Agrega:
   - **Destino | Región (Destino)**
   - **Destino | Ciudad (Destino)**
   - **Envíos | Precio total**
3. Comprueba que los importes se desglosan por región y ciudad.

**Resultado esperado:** las dimensiones filtran correctamente los registros de la tabla **Envíos**.

## Ejercicio 4: Configurar propiedades de tablas y columnas
  
En este ejercicio mejorarás la usabilidad del modelo ocultando columnas técnicas, cambiando formatos y definiendo agregaciones predeterminadas.

### Tarea 1: Ocultar columnas clave en dimensiones

1. Cambia a la vista **Modelo**.
2. Selecciona la tabla **Cliente**.
3. Selecciona la columna **ClienteID**.
4. En el panel **Propiedades**, activa **Ocultar en vista de informe**.
5. Repite el proceso con:
   - **Servicio | ServicioID**
   - **Oficina | OficinaID**
   - **Destino | DestinoID**
   - **Ruta | RutaID**

Ocultar claves técnicas reduce ruido para el usuario de negocio. Las columnas siguen existiendo y mantienen las relaciones, pero no aparecen como campos disponibles para crear visuales.

### Tarea 2: Ocultar columnas técnicas en la tabla de hechos

1. En la tabla **Envíos**, oculta estas columnas:
   - **ClienteID**
   - **ServicioID**
   - **OficinaID**
   - **DestinoID**
   - **RutaID**
2. Mantén visibles las columnas analíticas, como **Estado**, **Peso (KG)**, **Precio total**, **Intentos** y **EntregaTardia**.

### Tarea 3: Configurar formatos numéricos

1. Selecciona **Envíos | Precio base**.
2. En la cinta **Herramientas de columna**, configura:
   - Formato: **Moneda**.
   - Decimales: **2**.
   - Símbolo: **€ Euro (123 €)** 
3. Repite para:
   - **Envíos | Surplus fuel**
   - **Envíos | Precio total**

### Tarea 4: Configurar resumen predeterminado

1. Selecciona **Envíos | Precio total**.
2. En **Herramientas de columna**, revisa que **Resumen predeterminado** sea **Suma**.
3. Selecciona **Envíos | Peso (KG)** y cambia el resumen predeterminado a **Promedio**.
4. Selecciona **Envíos | Intentos** y cambia el resumen predeterminado a **Suma** o **Promedio**, según el análisis esperado.
5. Selecciona la columna **Servicio | Horas SLA******y asegúrate de que su resumen predeterminado sea **No resumir**.

### Tarea 5: Configurar categorías de datos

1. Selecciona **Destino | Ciudad (Destino)**.
2. En **Herramientas de columna > Categoría de datos**, selecciona **Ciudad**.
3. Selecciona **Destino | Código Postal (Destino)** y asigna **Código postal**.
4. Selecciona **Oficina | Ciudad** y asigna **Ciudad**.
5. Selecciona **Oficina | Región** y asigna **Estado o provincia** si aplica a tu configuración regional.

**Resultado esperado:** el modelo es más claro, las columnas técnicas quedan ocultas y los campos numéricos tienen formatos adecuados.

## Ejercicio 5: Crear jerarquías de análisis
  
En este ejercicio crearás jerarquías que faciliten el análisis drill-down en informes.

### Tarea 1: Crear una jerarquía geográfica de destino

1. En el panel **Datos**, dentro de la tabla **Destino**, haz clic derecho en **Región (Destino)**.
2. Selecciona **Crear jerarquía**.
3. Cambia el nombre de la jerarquía a **Destinos**.
4. Agrega a la jerarquía, en este orden:
   - **Región (Destino)**
   - **Ciudad (Destino)**
   - **Código Postal (Destino)**

### Tarea 2: Crear una jerarquía de oficina

1. En la tabla **Oficina**, haz clic derecho en **Región**.
2. Selecciona **Crear jerarquía**.
3. Cambia el nombre a **Oficinas**.
4. Agrega:
   - **Región**
   - **Ciudad**
   - **Oficina**

### Tarea 3: Probar una jerarquía en un visual

1. Cambia a la vista **Informe**.
2. Crea un gráfico de columnas agrupadas.
3. En el eje X, agrega **Destino | Destinos**.
4. En valores, agrega **Envíos | Precio total**.
5. Usa los botones de exploración para bajar de región a ciudad y código postal.

**Resultado esperado:** puedes navegar por niveles jerárquicos sin tener que añadir manualmente cada columna al visual.

## Ejercicio 6: Crear una medida rápida
  
En este ejercicio crearás una medida rápida para obtener el valor medio de precio por envío. Más adelante, en el Lab 3, crearás medidas DAX manuales con mayor control.

### Tarea 1: Crear una medida rápida de precio medio

1. En la vista **Informe**, selecciona la tabla **Envíos** en el panel **Datos**.
2. En la cinta, selecciona **Inicio > Medida rápida**.
3. En **Cálculo**, selecciona **División**.
4. Configura el cálculo usando:
   - Numerador: **Suma de Envíos | Precio total**.
   - Denominador: **Recuento de Envíos | EnvíoID**.
5. Cambia el nombre de la medida generada a **Precio medio por envío**.
6. Formatea la medida como moneda con 2 decimales.

### Tarea 2: Probar la medida rápida

1. Crea una visualización de **Tarjeta**.
2. Agrega la medida **Precio medio por envío**.
3. Crea una tabla con:
   - **Servicio | Servicio**
   - **Precio medio por envío**
4. Comprueba que el valor cambia por servicio.

**Resultado esperado:** tienes una primera medida creada desde la interfaz, útil para validar el modelo. En el próximo laboratorio crearás medidas equivalentes y más robustas con DAX.

## Ejercicio 7: Configurar una relación muchos a muchos con tabla puente
  
En este ejercicio crearás una tabla puente para analizar servicios mediante etiquetas operativas. Este patrón permite que un mismo servicio pertenezca a varias categorías. Por ejemplo, **Same Day** puede clasificarse como **Premium**, **Prioridad crítica** y **Entrega rápida** al mismo tiempo.

El objetivo es practicar un escenario frecuente de modelado: una dimensión, en este caso **Servicio**, necesita analizarse mediante clasificaciones flexibles que no forman una jerarquía estricta.

### Tarea 1: Crear una tabla de etiquetas de servicio

1. En la vista **Inicio**, selecciona **Introducir datos**.
2. Crea una tabla llamada **ServicioEtiqueta** con estas columnas:

| ServicioID | EtiquetaServicio |
|---|---|
| SAM | Premium |
| SAM | Prioridad crítica |
| SAM | Entrega rápida |
| EXP | Premium |
| EXP | Alta prioridad |
| EXP | Entrega rápida |
| STD | Estándar |
| STD | Prioridad normal |
| ECO | Estándar |
| ECO | Bajo coste |
| ECO | Baja prioridad |

3. Selecciona **Cargar**.

Esta tabla actúa como tabla puente entre los servicios y sus etiquetas. Observa que algunos servicios aparecen más de una vez porque pueden tener varias etiquetas asociadas.

### Tarea 2: Crear una tabla de etiquetas únicas

1. En la cinta **Modelado**, selecciona **Nueva tabla**.
2. Escribe la siguiente expresión DAX:

```DAX
Etiqueta = DISTINCT ( ServicioEtiqueta[EtiquetaServicio] )
```

3. Presiona **Enter**.

La tabla **Etiqueta** contiene una fila por cada etiqueta disponible. Esta tabla se usará como dimensión para crear segmentadores y filtrar el análisis.

### Tarea 3: Crear la relación Etiqueta-ServicioEtiqueta

1. Cambia a la vista **Modelo**.
2. Crea una relación entre:
   - **Etiqueta | EtiquetaServicio**
   - **ServicioEtiqueta | EtiquetaServicio**
3. Configura la relación con estos valores:
   - Cardinalidad: **Uno a varios (1:*)**.
   - Dirección de filtro cruzado: **Única**.
   - Relación activa: **Sí**.
4. Selecciona **Aceptar**.

Con esta relación, una etiqueta puede filtrar las filas correspondientes de la tabla puente **ServicioEtiqueta**.

### Tarea 4: Crear la relación Servicio-ServicioEtiqueta

1. Crea una relación entre:
   - **Servicio | ServicioID**
   - **ServicioEtiqueta | ServicioID**
2. Configura la relación con estos valores:
   - Cardinalidad: **Uno a varios (1:*)**.
   - Dirección de filtro cruzado: **Ambas**.
   - Relación activa: **Sí**.
3. Selecciona **Aceptar**.

En este caso se usa dirección de filtro **Ambas** para que el filtro pueda viajar desde **Etiqueta** hacia **ServicioEtiqueta**, después hacia **Servicio** y finalmente hacia **Envíos** mediante la relación existente entre **Servicio** y **Envíos**.

La ruta de filtro esperada es:

```text
Etiqueta → ServicioEtiqueta → Servicio → Envíos
```

### Tarea 5: Validar las relaciones del modelo

1. Comprueba que el modelo contiene estas relaciones:
   - **Etiqueta 1:* ServicioEtiqueta**
   - **Servicio 1:* ServicioEtiqueta**
   - **Servicio 1:* Envíos**
2. Comprueba que la relación entre **Servicio** y **ServicioEtiqueta** tiene dirección de filtro **Ambas**.
3. Comprueba que la relación entre **Servicio** y **Envíos** sigue teniendo dirección de filtro **Única**, desde **Servicio** hacia **Envíos**.

No cambies la relación principal entre **Servicio** y **Envíos** a bidireccional. En un modelo en estrella, las dimensiones deben filtrar la tabla de hechos, no al revés, salvo que exista una necesidad específica.

### Tarea 6: Validar el análisis por etiqueta

1. Cambia a la vista **Informe**.
2. Crea un segmentador usando el campo **Etiqueta | EtiquetaServicio**.
3. Crea una visualización de **Tabla**.
4. Agrega estos campos:
   - **Servicio | Servicio**
   - **Servicio | Prioridad**
   - **Envíos | Precio total**
5. Selecciona la etiqueta **Premium** en el segmentador.
6. Comprueba que la tabla muestra únicamente los servicios asociados a esa etiqueta, por ejemplo:
   - **Same Day**
   - **Express**
7. Selecciona la etiqueta **Estándar**.
8. Comprueba que la tabla muestra los servicios asociados a esa etiqueta, por ejemplo:
   - **Standard**
   - **Economy**

### Tarea 7: Interpretar el resultado

1. Observa que un mismo servicio puede aparecer en varias etiquetas.
2. Observa que una misma etiqueta puede agrupar varios servicios.
3. Comprueba que el importe de **Precio total** cambia al seleccionar distintas etiquetas.

Este comportamiento confirma que la tabla puente está funcionando correctamente. El segmentador de etiquetas no filtra directamente la tabla **Envíos**; filtra primero la tabla puente, después la dimensión **Servicio** y finalmente la tabla de hechos **Envíos**.

**Resultado esperado:** puedes filtrar los envíos por etiquetas operativas de servicio mediante una tabla puente, manteniendo el modelo principal en estrella.

## Ejercicio 8: Revisar buenas prácticas del modelo
  
En este ejercicio realizarás una revisión final del modelo semántico.

### Tarea 1: Revisar nombres visibles

1. Comprueba que los nombres de columnas son comprensibles para negocio.
2. Evita nombres técnicos innecesarios en la vista de informe.
3. Revisa que las medidas o campos generados automáticamente tengan nombres claros.

### Tarea 2: Revisar relaciones

1. Comprueba que las relaciones principales del modelo son:
   - **Cliente 1:* Envíos**
   - **Servicio 1:* Envíos**
   - **Oficina 1:* Envíos**
   - **Destino 1:* Envíos**
   - **Ruta 1:* Envíos**
2. Comprueba que no hay relaciones inactivas inesperadas.
3. Comprueba que no hay relaciones bidireccionales innecesarias entre dimensiones y hechos.


---
## Entregables

Al finalizar, debes tener un archivo `.pbix` con:
1. Modelo en estrella configurado.
2. Propiedades, formatos y jerarquías aplicadas.
3. Medida rápida creada.
4. Tabla puente de etiquetas de servicio.
5. Modelo listo para cálculos DAX.

    
---
## Resumen
En este laboratorio has configurado el modelo semántico de ParcelCraft. Has creado relaciones, validado la propagación de filtros, ocultado columnas técnicas, aplicado formatos, definido jerarquías y creado una primera medida rápida. Con esto, el modelo queda preparado para el siguiente paso: crear cálculos DAX reutilizables y métricas de negocio.

### Recursos útiles
- Documentación oficial de modelado en Power BI: https://learn.microsoft.com/power-bi/guidance/star-schema
- Relaciones de modelo en Power BI Desktop: https://learn.microsoft.com/power-bi/transform-model/desktop-relationships-understand
- Crear y administrar relaciones: https://learn.microsoft.com/power-bi/transform-model/desktop-create-and-manage-relationships
- Medidas rápidas en Power BI Desktop: https://learn.microsoft.com/power-bi/transform-model/desktop-quick-measures
  
### 🎉 Fin del laboratorio
¡Enhorabuena! Has completado la configuración del modelo semántico de ParcelCraft. Ya tienes una base sólida para crear medidas DAX, columnas calculadas y análisis avanzados en el próximo laboratorio. 👏

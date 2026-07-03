# Laboratorio 6: Diseño de informes analíticos e interacción con filtros en Power BI
  
**ParcelCraft** ya cuenta con un modelo en estrella, una tabla `'Fecha'`, jerarquías de negocio, medidas DAX empresariales y un grupo de cálculo de inteligencia de tiempo. En este laboratorio trabajarás como diseñador de informes analíticos: crearás un informe de tres páginas, aplicarás principios de diseño visual, seleccionarás objetos visuales adecuados, configurarás segmentaciones, filtros e interacciones, y prepararás una página de KPIs enfocada en una oficina concreta.  
El objetivo es pasar de un modelo semántico validado a una experiencia de análisis clara, navegable y preparada para usuarios de negocio.

## Objetivos de aprendizaje
  
**Tiempo estimado total: 45 minutos**  
Al finalizar este laboratorio serás capaz de:
- Crear un informe analítico de tres páginas para ParcelCraft.
- Aplicar principios de diseño efectivo y seleccionar visuales adecuados.
- Configurar segmentaciones, filtros, interacciones y exploración en profundidad.
- Preparar una vista de KPIs por oficina como base para escenarios posteriores con RLS.

---

## Antes de empezar

### Requisitos
  
Para completar este laboratorio necesitas:
- Power BI Desktop instalado.
- Haber completado el **Laboratorio 5: DAX empresarial en Power BI**, o partir del archivo inicial.
- Descargarse los **archivos del laboratorio**, el logo y tema de ParcelCraft, disponibles en la carpeta **Files**.
- Conocimientos básicos de visualizaciones, segmentadores, filtros, interacciones y jerarquías en Power BI.  

> Tienes un archivo inicial y solución disponibles.
> [Archivo inicial (Starter)](https://github.com/quizeth/Labs/blob/main/Explotaci%C3%B3n%20y%20Procesamiento%20de%20Datos%20con%20Power%20BI/Intermedio/Files/03%20Starter%20ParcelCraft.pbix)
> [Archivo final (Solución)](https://github.com/quizeth/Labs/blob/main/Explotaci%C3%B3n%20y%20Procesamiento%20de%20Datos%20con%20Power%20BI/Intermedio/Files/03%20Soluci%C3%B3n%20ParcelCraft.pbix)


---

## Ejercicio 1: Crear la página Visión global
  
En este ejercicio crearás la primera página del informe. Esta página debe proporcionar una visión global del análisis y permitir que un usuario entienda rápidamente el comportamiento general de ParcelCraft.  

La página debe resumir volumen, ingresos, margen, cumplimiento y evolución temporal. Debe funcionar como una vista ejecutiva, por lo que los KPIs principales deben estar en la parte superior y los gráficos de análisis en la zona central. Además, al ser la página de aterrizaje, debe ser ligera para agilizar la carga del informe al abrirlo.

### Tarea 1: Abrir y definir la estructura del informe
1. Abre **Power BI Desktop**.
2. Abre el archivo .pbix generado al final del Laboratorio 5.
3. En la **vista Informe**, elimina las páginas existentes del Laboratorio 5.
> Como Power BI te obligará a mantener una, limpia todas las visualizaciones de esa página.

3. Renombra la página existente como **Vision global**.
4. Haz clic derecho en el nombre de la página y selecciona **Establecer como página de aterrizaje**.
5. En la pestaña **Ver**, ve a **Temas > Explorar temas**.
6. Carga el tema **ParcelCraft_Theme**.
> Tienes el [archivo del tema](https://github.com/quizeth/Labs/blob/main/Explotaci%C3%B3n%20y%20Procesamiento%20de%20Datos%20con%20Power%20BI/Avanzado/Lab%2006/Files/ParcelCraft_Theme.json) en la carpeta de Files de este laboratorio.


### Tarea 2: Configurar encabezado y diseño base
1. Ve a la página **Visión global**.
3. Añade una imagen a la esquina superior izquierda
4. Inserta el logo de la empresa. 
> Tienes el [archivo del logo](https://github.com/quizeth/Labs/blob/main/Explotaci%C3%B3n%20y%20Procesamiento%20de%20Datos%20con%20Power%20BI/Avanzado/Lab%2006/Files/ParcelCraft.png) en la carpeta de Files de este laboratorio. La URL es: `https://github.com/quizeth/Labs/blob/main/Explotaci%C3%B3n%20y%20Procesamiento%20de%20Datos%20con%20Power%20BI/Avanzado/Lab%2006/Files/ParcelCraft.png?raw=true`
4. Configura el logo:
  - **General > Efectos > Fondo**: desactivado
  - **General > Efectos > Borde visual**: desactivado
> Inserta un cuadro de texto en la parte superior, a la derecha del logo.
5. Escribe el título: `Visión global de operaciones`.
6. Configura el título:
   - **Fuente**: Segoe UI Semibold
   - **Tamaño de fuente**: 32
7. Debajo del título, añade un subtítulo: `Seguimiento ejecutivo de envíos, ingresos, margen y cumplimiento`.
5. Configura el subtítulo:
   - **Fuente**: Segoe UI Semibold
   - **Tamaño de fuente**: 16
   - **Color de fuente**: Blanco, 50% más oscuro
   - Cursiva

<img width="1175" height="670" alt="image" src="https://github.com/user-attachments/assets/6419fd3b-d2b3-4121-9126-1e3a462767f4" />

> Buena práctica: la ubicación debe guiar la lectura. Primero se leen KPIs, después tendencias y comparativas, y finalmente filtros o detalles.

### Tarea 3: Crear tarjetas de KPIs principales
1. Inserta una visualización de tarjeta.
> Si tienes la nueva visualización de tarjeta (con el icono de un rayo), sigue los pasos tal y como están escritos.
> Si tienes la antigua (sin el icono), crea una tarjeta individual para cada métrica. Después, alinéalas en la misma fila y ajusta el tamaño para que tengan el mismo ancho y alto.

2. Asigna las siguientes medidas:
    - `Total envíos`
    - `Ingresos`
    - `Margen estimado`
    - `% margen estimado`
    - `% envíos entregados`.

3. Ajusta la tarjeta para que cubra el ancho de todo el lienzo.
4. Sitúa la tarjeta debajo del título y subtítulo.
5. Ajusta los formatos de las tarjetas. Para ello, con la tarjeta seleccionada, ve a la segunda pestaña del panel de visualizaciones (Dar formato a su objeto visual, icono del gráfico y el pincel.
> A partir de ahora, cada vez que el laboratorio se refiera a una opción de formato, dirígete a esta pestaña.

6. Ve a **Objeto Visual > Llamada > Aplicar configuración a**.
7. En el menú desplegable **Tarjetas**, selecciona **Ingresos**.
8. Amplía la pestaña **Valor** y configura:
   - **Mostrar unidades**: Millones
   - **Posiciones decimales**: 2
9. Revisa que los formatos de moneda y porcentaje se muestran correctamente. Si no es el caso, repite los pasos anteriores para ajustar la configuración de cada medida.
   
**Resultado esperado:** la zona superior permite entender de inmediato el estado general del negocio.

### Tarea 4: Crear visuales globales de análisis
1. Inserta un gráfico de columnas agrupadas y de líneas.
2. Configura el gráfico:
  - **Eje X:** `'Fecha'[Año mes]`
  - **Eje Y:** `Total envíos`
  - **Eye Y de línea:** `% margen estimado`
3. En el panel de formato, ve a Ve a **General > Título**.
4.  Cambia el título a `Evolución de envíos`.
5. En el objeto visual, selecciona los **tres puntos > Ordenar por** `'Fecha'[Año mes]` de forma ascendente.

6. Inserta un gráfico de barras agrupadas.
7. Configura el gráfico:
- **Eje X:** `'Oficina'[Oficinas]`
- **Eje Y:** `Ingresos`
8. Ordena el visual de mayor a menor (orden descendente) por `Ingresos`.
  
9. Inserta un gráfico circular.
13. Configura el gráfico:
- **Leyenda:** `'Cliente'[Segmento]`
- **Valores:** `Total envíos`
14. Cambia el título a `Distribución por segmento de cliente`.  

**Resultado esperado:** la página combina tendencia temporal, comparativa por oficina, y distribución por segmento de cliente.

<img width="1166" height="666" alt="image" src="https://github.com/user-attachments/assets/0d677760-15fc-4959-90be-4458f3a32c6e" />

### Tarea 5: Añadir segmentaciones de visión global
1. Inserta una segmentación de datos con `'Fecha'[Año]`.
2. Inserta una segunda segmentación con `'Servicio'[Servicio]`.
4. Coloca las segmentaciones en la esquina superior derecha.
7. Configura ambas segmentaciones:
   -  **Configuración de la segmentación > Opciones > Estilo**: Menú desplegable.
   -  **Configuración de la segmentación > Opciones > Mostrar opción "Seleccionar todo"**: activado.
   -  **Valores > Fondo > Color**: Blanco
   -  Pestaña **General > Efectos > Fondo:** Desactivado
9. Configura el **Año** como `2023`.
10. Con la segmentación de **Año** seleccionada, abre el panel de **Filtros**.
11. Bajo la sección **Filtros de este objeto visual**, expande la tarjeta **Año**-
12. Configura **Tipo de filtro** > **Filtrado básico** como se muestra:
   <img width="203" height="456" alt="image" src="https://github.com/user-attachments/assets/50870a14-70ed-467f-801e-6afe032bd72f" />

**Resultado esperado:** la página permite filtrar la visión global por tiempo y servicio.

<img width="1175" height="668" alt="image" src="https://github.com/user-attachments/assets/ce75ed63-9ee6-4ea9-83a7-0c22cd310525" />


## Ejercicio 6: Crear la página Detalle operativo
  
En este ejercicio crearás la segunda página del informe. Esta página debe proporcionar una visión más detallada de un ámbito concreto: el rendimiento operativo por oficina y servicio.  

El objetivo es que el usuario pueda comparar oficinas, detectar diferencias por servicio, revisar ingresos, margen y cumplimiento, y explorar la información con mayor granularidad.

### Tarea 1: Configurar la página de detalle
1. Duplica la página **Visión global**.
2. Renombra la página como **Detalle operativo**.
3. Elimina los objetos visuales, dejando únicamente la sección del encabezado.
4. Inserta un cuadro de texto como encabezado.
5. Escribe el título: `Detalle operativo`.
6. Debajo, añade el subtítulo: `Comparativa de volumen, ingresos, margen y cumplimiento por dimensión operativa.`

<img width="1175" height="666" alt="image" src="https://github.com/user-attachments/assets/ded6b062-699e-427c-bb4a-07747fb5810c" />


#### Tarea 2: Crear matriz operativa
1. Inserta una visualización de matriz.
2. Configura la matriz:
- **Filas:** `'Oficina'[Oficinas]`
- **Columnas:** `'Servicio'[Servicio]`
- **Valores:** `Total envíos`
- **Valores:** `Ingresos`
- **Valores:** `Margen estimado`
- **Valores:** `% margen estimado`
- **Valores:** `% envíos entregados`
3. Activa el ajuste de texto si los nombres son largos.
4. Aplica formato condicional sobre `% margen estimado`.
5. Usa un color más favorable para porcentajes altos y uno más intenso para porcentajes bajos.
6. Aplica formato condicional sobre `% envíos entregados` si quieres destacar oficinas con menor cumplimiento.
7. Cambia el título del visual a Matriz operativa por oficina y servicio.  
**Resultado esperado:** la matriz permite comparar oficinas y servicios a través de medidas clave del modelo semántico.

#### Tarea 3: Crear visuales comparativos de detalle
1. Inserta un gráfico de dispersión.
2. Configura el gráfico:
- **Eje X:** `% margen estimado`
- **Eje Y:** `% envíos entregados`
- **Tamaño:** `Total envíos`
- **Leyenda:** `'Servicio'[Prioridad]`
- **Detalles:** `'Oficina'[Oficina]`
3. Cambia el título a Relación entre margen y cumplimiento.
4. Inserta un gráfico de columnas apiladas.
5. Configura el gráfico:
- **Eje X:** `'Oficina'[Oficina]`
- **Eje Y:** `Total envíos`
- **Leyenda:** `'Servicio'[Prioridad]`
6. Cambia el título a Volumen por oficina y prioridad.
7. Inserta un gráfico de barras.
8. Configura el gráfico:
- **Eje Y:** `'Servicio'[Servicio]`
- **Eje X:** `Margen estimado`
9. Cambia el título a Margen por servicio.
10. Inserta una tarjeta adicional con `Ingresos selección visible`.
11. Cambia el título de la tarjeta a Ingresos de la selección visible.  
**Resultado esperado:** la página permite analizar relaciones entre margen, cumplimiento, volumen y prioridad.

#### Tarea 4: Configurar interacciones entre visuales
1. Selecciona el gráfico **Volumen por oficina y prioridad**.
2. En la cinta, selecciona **Formato > Editar interacciones**.
3. Configura la interacción sobre la matriz como filtro.
4. Configura la interacción sobre el gráfico de dispersión como filtro.
5. Configura la interacción sobre el gráfico de barras como filtro.
6. Selecciona el gráfico de dispersión **Relación entre margen y cumplimiento**.
7. Configura su interacción sobre la matriz como filtro.
8. Configura su interacción sobre la tarjeta de `Ingresos selección visible` como filtro.
9. Desactiva la interacción sobre segmentaciones, si aparece disponible.
10. Sal de **Editar interacciones**.
11. Selecciona una oficina en el gráfico de columnas.
12. Comprueba que los demás visuales se actualizan con el contexto seleccionado.
13. Selecciona un punto del gráfico de dispersión.
14. Comprueba que la matriz y la tarjeta muestran el contexto de ese punto.  
Nota: una interacción bien configurada ayuda a explorar. Una interacción excesiva puede producir lecturas confusas, especialmente si todos los visuales filtran a todos los demás.

#### Tarea 5: Añadir segmentaciones de detalle
1. Inserta una segmentación con `'Servicio'[Servicio]`.
2. Inserta una segmentación con `'Servicio'[Prioridad]`.
3. Inserta una segmentación con `'Cliente'[Segmento]`.
4. Inserta una segmentación con la jerarquía **Geografía** de la tabla `'Destino'`, si está disponible.
5. Coloca las segmentaciones en una zona consistente con la página anterior.
6. Prueba cada segmentación.
7. Confirma que la matriz, el gráfico de dispersión y los gráficos comparativos se actualizan correctamente.  
**Resultado esperado:** la página de detalle permite explorar el rendimiento operativo por oficina, servicio, prioridad, cliente y destino.

### Ejercicio 4: Configurar jerarquías y exploración en profundidad
  
En este ejercicio utilizarás jerarquías de negocio para facilitar la navegación desde niveles agregados hasta niveles detallados.  
La exploración en profundidad es útil cuando el usuario no quiere ver todos los detalles de golpe, sino comenzar por una lectura general y descender gradualmente hasta ciudad, oficina, prioridad, servicio o geografía.

#### Tarea 1: Validar jerarquías disponibles
1. Ve a la **vista Modelo**.
2. Expande la tabla `'Oficina'`.
3. Comprueba si existe la jerarquía **Oficinas**.
4. Si no existe, crea una jerarquía llamada Oficinas.
5. Añade las columnas en este orden:
- `'Oficina'[Región]`
- `'Oficina'[Ciudad]`
- `'Oficina'[Oficina]`
6. Expande la tabla `'Servicio'`.
7. Comprueba si existe la jerarquía **Servicios**.
8. Si no existe, crea una jerarquía llamada Servicios.
9. Añade las columnas en este orden:
- `'Servicio'[Prioridad]`
- `'Servicio'[Servicio]`
10. Expande la tabla `'Destino'`.
11. Comprueba si existe la jerarquía **Geografía**.
12. Si no existe, crea una jerarquía llamada Geografía.
13. Añade las columnas disponibles en orden de mayor a menor nivel, por ejemplo:
- `'Destino'[Región (Destino)]`
- `'Destino'[Ciudad (Destino)]`
- `'Destino'[Código Postal (Destino)]`
14. Vuelve a la **vista Informe**.

#### Tarea 2: Crear visual de exploración por oficinas
1. Ve a la página **Detalle operativo**.
2. Inserta un gráfico de columnas agrupadas.
3. Configura el gráfico:
- **Eje X:** jerarquía **Oficinas** de la tabla `'Oficina'`
- **Eje Y:** `Total envíos`
4. Cambia el título a Exploración por jerarquía de oficinas.
5. Selecciona el visual.
6. Activa los controles de exploración en profundidad.
7. Usa la opción de bajar al siguiente nivel.
8. Navega desde `'Oficina'[Región]` hasta `'Oficina'[Ciudad]`.
9. Baja desde `'Oficina'[Ciudad]` hasta `'Oficina'[Oficina]`.
10. Usa la opción de subir nivel para volver a la vista agregada.
11. Usa la opción de expandir todo al siguiente nivel para comparar todos los elementos con más detalle.  
**Resultado esperado:** el usuario puede analizar los envíos desde región hasta oficina de forma interactiva.

#### Tarea 3: Crear visual de exploración por servicio y geografía
1. Inserta un gráfico de barras horizontales.
2. Configura el gráfico:
- **Eje Y:** jerarquía **Servicios** de la tabla `'Servicio'`
- **Eje X:** `Ingresos`
3. Cambia el título a Exploración por jerarquía de servicio.
4. Activa los controles de exploración del visual.
5. Baja desde `'Servicio'[Prioridad]` hasta `'Servicio'[Servicio]`.
6. Comprueba que el usuario puede identificar qué servicios generan más ingresos dentro de cada prioridad.
7. Inserta un segundo gráfico de barras o mapa, según los campos disponibles.
8. Configura el visual:
- **Eje o ubicación:** jerarquía **Geografía** de la tabla `'Destino'`
- **Valores:** `Total envíos`
9. Cambia el título a Exploración geográfica de envíos.
10. Activa la exploración si el visual lo permite.
11. Ajusta el tamaño del visual para que los nombres sean legibles.  
**Resultado esperado:** el usuario puede explorar ingresos por servicio y volumen por geografía usando jerarquías reutilizables del modelo.

### Ejercicio 5: Crear la página KPIs Oficina
  
En este ejercicio crearás la tercera página del informe. Esta página debe proporcionar KPIs sobre una oficina concreta y recrear lo que más tarde se verá filtrado mediante RLS.  
No implementarás seguridad a nivel de fila en este laboratorio. Solo aplicarás un filtro de página para simular que el usuario accede a una vista limitada a su oficina.

#### Tarea 1: Configurar la página KPIs Oficina
1. Ve a la página **KPIs Oficina**.
2. Inserta un cuadro de texto como encabezado.
3. Escribe el título: KPIs de oficina.
4. Debajo, añade el subtítulo: Vista enfocada en el rendimiento de una oficina concreta.
5. Mantén el mismo estilo de títulos, subtítulos y colores utilizado en las páginas anteriores.
6. Reserva la zona superior para tarjetas de KPIs.
7. Reserva la zona central para gráficos de seguimiento.
8. Reserva la parte inferior para una tabla de detalle.

#### Tarea 2: Aplicar filtro de página por oficina
1. Inserta una segmentación de datos con `'Oficina'[Oficina]`.
2. Selecciona una oficina concreta.
3. En el panel **Filtros**, arrastra `'Oficina'[Oficina]` al área **Filtros de esta página**.
4. Selecciona la misma oficina elegida en la segmentación.
5. Comprueba que todos los visuales de la página quedan filtrados por esa oficina.
6. Si quieres simular una vista fija, oculta la segmentación o reduce su presencia visual.
7. Si quieres permitir comparación manual, mantén visible la segmentación.
8. Bloquea el filtro de página si quieres evitar cambios accidentales durante la revisión.  
Importante: esta página no implementa RLS. Solo recrea el resultado visual esperado cuando el contexto del usuario queda limitado a una oficina.

#### Tarea 3: Crear KPIs de oficina
1. Inserta cinco visuales de tarjeta en la parte superior de la página.
2. Configura las tarjetas con estas medidas:
- `Total envíos`
- `Ingresos`
- `Margen estimado`
- `% margen estimado`
- `% envíos entregados`
3. Alinea las tarjetas en una misma fila.
4. Usa el mismo tamaño, color, borde y estilo que en la página **Visión global**.
5. Comprueba que los valores corresponden a la oficina seleccionada.
6. Cambia temporalmente la oficina seleccionada y confirma que los KPIs se recalculan.
7. Vuelve a seleccionar la oficina que usarás como ejemplo final.

#### Tarea 4: Crear visuales de seguimiento de oficina
1. Inserta un gráfico de líneas.
2. Configura el gráfico:
- **Eje X:** `'Fecha'[Año mes]`
- **Eje Y:** `Total envíos`
3. Cambia el título a Evolución de envíos de la oficina.
4. Inserta un gráfico de columnas.
5. Configura el gráfico:
- **Eje X:** `'Servicio'[Servicio]`
- **Eje Y:** `Ingresos`
6. Cambia el título a Ingresos por servicio.
7. Inserta un gráfico de barras.
8. Configura el gráfico:
- **Eje Y:** `'Servicio'[Servicio]`
- **Eje X:** `Margen estimado`
9. Cambia el título a Margen por servicio.
10. Inserta un gráfico de dona.
11. Configura el gráfico:
- **Leyenda:** `'Servicio'[Prioridad]`
- **Valores:** `Total envíos`
12. Cambia el título a Distribución por prioridad.
13. Inserta una tabla de detalle.
14. Agrega los campos:
- `'Envíos'[EnvíoID]`
- `'Fecha'[Fecha]`
- `'Servicio'[Servicio]`
- `'Cliente'[Segmento]`
- `'Destino'[Ciudad (Destino)]`
- `'Envíos'[Peso (KG)]`
- `'Envíos'[Precio total]`
15. Coloca la tabla en la parte inferior de la página.
16. Aplica formato a columnas numéricas y monetarias.  
**Resultado esperado:** la página muestra KPIs, tendencia, comparativas y detalle de envíos para una única oficina.

### Ejercicio 6: Aplicar diseño efectivo, filtros y validación final
  
En este ejercicio revisarás el informe completo para asegurar que cumple criterios de diseño, navegación y análisis.  
También configurarás el panel de filtros y validarás que segmentaciones, interacciones y jerarquías se comportan de forma coherente.

#### Tarea 1: Aplicar principios de diseño efectivo
1. Ve a la página **Visión global**.
2. Revisa la ubicación de los elementos.
3. Comprueba que los KPIs principales están en la parte superior.
4. Verifica que los visuales de análisis ocupan la zona central.
5. Comprueba que las segmentaciones están en una zona fácil de localizar.
6. Revisa el equilibrio visual.
7. Asegúrate de que no hay zonas saturadas de visuales y otras completamente vacías.
8. Revisa el contraste.
9. Usa colores intensos solo para destacar información relevante.
10. Evita usar demasiados colores distintos en una misma página.
11. Revisa la proximidad.
12. Agrupa filtros con filtros, KPIs con KPIs y detalles con detalles.
13. Revisa la repetición.
14. Usa el mismo estilo de tarjetas, títulos, subtítulos, bordes, colores y espaciado en las tres páginas.
15. Repite esta revisión en **Detalle operativo**.
16. Repite esta revisión en **KPIs Oficina**.

#### Tarea 2: Configurar el panel de filtros
1. Ve a la página **Visión global**.
2. Selecciona el gráfico **Distribución por prioridad de servicio**.
3. En el panel **Filtros**, revisa el área **Filtros de este objeto visual**.
4. Comprueba que el filtro de `'Servicio'[Prioridad]` está disponible para ese visual.
5. Selecciona un espacio vacío de la página.
6. En el área **Filtros de esta página**, agrega `'Cliente'[Segmento]`.
7. Selecciona un segmento de prueba.
8. Comprueba que toda la página se actualiza.
9. Borra la selección para volver a mostrar todos los datos.
10. En el área **Filtros de todas las páginas**, agrega `'Fecha'[Año]` si quieres probar un rango temporal global.
11. Selecciona un año de prueba.
12. Navega por las tres páginas y comprueba que el filtro se aplica a todo el informe.
13. Borra el filtro global si no quieres dejarlo aplicado.
14. Ve a la página **KPIs Oficina**.
15. Comprueba que `'Oficina'[Oficina]` está aplicado en **Filtros de esta página**.  
Nota: utiliza segmentaciones para filtros frecuentes y visibles. Utiliza el panel de filtros para filtros técnicos, de página o de informe completo.

#### Tarea 3: Validar navegación e interacciones
1. Ve a la página **Visión global**.
2. Selecciona un valor en `'Fecha'[Año mes]`.
3. Comprueba que KPIs y gráficos se actualizan.
4. Selecciona una región o prioridad.
5. Verifica que el informe refleja únicamente el contexto seleccionado.
6. Limpia los filtros de la página.
7. Ve a la página **Detalle operativo**.
8. Selecciona una oficina en el gráfico de columnas.
9. Comprueba que la matriz y el gráfico de dispersión se filtran correctamente.
10. Usa la jerarquía **Oficinas** para bajar un nivel.
11. Sube de nuevo al nivel anterior.
12. Verifica que la exploración en profundidad es comprensible.
13. Ve a la página **KPIs Oficina**.
14. Confirma que todos los visuales muestran datos de una única oficina.
15. Selecciona una prioridad en el gráfico de dona.
16. Comprueba que la tabla inferior se actualiza según la selección.
17. Limpia la selección del gráfico de dona.
18. Guarda el archivo.  
**Resultado esperado:** el informe permite navegar, filtrar y explorar datos de forma clara, consistente y alineada con el modelo semántico creado en laboratorios anteriores.

### Entregables
  
Al finalizar, debes tener un archivo .pbix con:
- Informe de tres páginas analíticas:
- Visión global
- Detalle operativo
- KPIs Oficina
- Página **Visión global** con KPIs principales, tendencia temporal, comparativas por oficina, servicio y prioridad.
- Página **Detalle operativo** con matriz operativa, gráfico de dispersión, gráficos comparativos, segmentaciones e interacciones configuradas.
- Página **KPIs Oficina** filtrada por una oficina concreta.
- Uso de medidas existentes del modelo semántico:
- `Total envíos`
- `Ingresos`
- `Margen estimado`
- `% margen estimado`
- `% envíos entregados`
- `Ingresos selección visible`
- Segmentaciones configuradas con `'Fecha'`, `'Cliente'`, `'Servicio'` y `'Oficina'`.
- Panel de filtros revisado con filtros de visual, página e informe.
- Interacciones entre visuales configuradas mediante **Editar interacciones**.
- Jerarquías utilizadas para exploración en profundidad:
- Oficinas
- Servicios
- Geografía
- Diseño visual consistente aplicando ubicación, equilibrio, contraste, proximidad y repetición.
- Página de KPIs preparada para explicar posteriormente el efecto de seguridad a nivel de fila.

### Resumen
  
En este laboratorio has transformado el modelo semántico empresarial de ParcelCraft en un **informe analítico de negocio**. Has creado una página de visión global, una página de detalle operativo y una página de KPIs filtrada por una oficina concreta. También has aplicado principios de diseño efectivo, seleccionado objetos visuales adecuados, configurado segmentaciones, usado el panel de filtros, ajustado interacciones entre visuales y utilizado jerarquías para exploración en profundidad.  
La idea principal es que un buen informe no solo muestra métricas correctas, sino que organiza la información para que el usuario pueda entender, filtrar, comparar y actuar con confianza.

### Recursos útiles
- Tipos de visualizaciones en Power BI: [https://learn.microsoft.com/power-bi/visuals/power-bi-visualization-types-for-reports-and-q-and-a](https://learn.microsoft.com/power-bi/visuals/power-bi-visualization-types-for-reports-and-q-and-a)
- Agregar filtros a informes en Power BI: [https://learn.microsoft.com/power-bi/create-reports/power-bi-report-add-filter](https://learn.microsoft.com/power-bi/create-reports/power-bi-report-add-filter)
- Cambiar cómo interactúan los visuales en un informe: [https://learn.microsoft.com/power-bi/create-reports/service-reports-visual-interactions](https://learn.microsoft.com/power-bi/create-reports/service-reports-visual-interactions)
- Segmentaciones en Power BI: [https://learn.microsoft.com/power-bi/visuals/power-bi-visualization-slicers](https://learn.microsoft.com/power-bi/visuals/power-bi-visualization-slicers)
- Exploración en profundidad en Power BI: [https://learn.microsoft.com/power-bi/consumer/end-user-drill](https://learn.microsoft.com/power-bi/consumer/end-user-drill)
- Guía de diseño de informes en Power BI: [https://learn.microsoft.com/power-bi/create-reports/desktop-report-design](https://learn.microsoft.com/power-bi/create-reports/desktop-report-design)

### 🎉 Fin del laboratorio
  
¡Enhorabuena! Has completado el Laboratorio 6 de ParcelCraft. Ahora tienes un informe analítico de tres páginas, con diseño consistente, visuales adecuados, filtros interactivos, exploración en profundidad y una vista de oficina preparada para conectar con escenarios posteriores de seguridad a nivel de fila. 👏

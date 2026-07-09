## Laboratorio 2: Modelado de datos y esquema en estrella en Power BI

**ParcelCraft** es una empresa ficticia de logística que necesita convertir sus datos preparados en Power Query en un **modelo semántico robusto, mantenible y listo para análisis**.

En el laboratorio anterior transformaste el archivo **ParcelCraft_50k.csv** en una tabla de hechos de envíos y varias dimensiones. En esta segunda parte trabajarás principalmente en la **vista Modelo** de Power BI Desktop para organizar las tablas, crear y administrar relaciones, aplicar propiedades del modelo, definir jerarquías de negocio y dejar preparado el modelo para análisis.

---

### Objetivos de aprendizaje

**Tiempo estimado total: 45 minutos**

Al finalizar este laboratorio serás capaz de:
- Revisar y validar un **modelo en estrella** en Power BI.
- Crear pocos **layouts útiles** en la vista Modelo.
- Crear, editar y administrar **relaciones**.
- Configurar cardinalidad, dirección de filtro cruzado y estado activo de relaciones.
- Resolver una relación conceptual varios a varios mediante una **tabla puente**.
- Crear jerarquías geográficas y operativas.
- Configurar propiedades de tablas, columnas, relaciones y modelo.
- Ocultar columnas técnicas y mejorar la experiencia del usuario final.

---

## Antes de empezar

### Requisitos

Para completar este laboratorio necesitas:
- Power BI Desktop instalado.
- Conexión a Internet.
- Haber completado el **Laboratorio 1: Ingesta, preparación y carga de datos en Power Query**, o partir del archivo inicial.
- Conocimientos básicos de relaciones y modelado dimensional.

Tienes un archivo inicial y solución disponibles. Si vas a trabajar en el mismo archivo a lo largo de todos los laboratorios, recomiendo empezar a partir de este starter.
- [Archivo inicial (Starter)](https://github.com/quizeth/Labs/blob/main/Explotaci%C3%B3n%20y%20Procesamiento%20de%20Datos%20con%20Power%20BI/Avanzado/Lab%2002/Files/02-A%20Starter%20ParcelCraft.pbix)
- [Archivo final (Solución)](https://github.com/quizeth/Labs/blob/main/Explotaci%C3%B3n%20y%20Procesamiento%20de%20Datos%20con%20Power%20BI/Avanzado/Lab%2002/Files/02-A%20Soluci%C3%B3n%20ParcelCraft.pbix)

### Convenciones usadas en el laboratorio
- Cuando se indique seleccionar una vista, usa el panel izquierdo de Power BI Desktop:
  - **Vista Informe**
  - **Vista Tabla**
  - **Vista Modelo**
- Cuando se indique una opción de cinta, se mostrará como **pestaña > grupo > comando**.
- Los nombres de tablas, columnas y medidas deben escribirse exactamente como se indican, salvo que tu archivo tenga nombres equivalentes ligeramente distintos.
- En este laboratorio no se rehace la preparación de Power Query; el foco está en el **modelo semántico**.

---

## Ejercicio 1: Revisar el modelo cargado y confirmar el diseño en estrella

En este ejercicio revisarás las tablas cargadas desde Power Query e identificarás qué tabla actuará como hecho y cuáles actuarán como dimensiones.

### Tarea 1: Abrir el archivo de trabajo
1. Abre **Power BI Desktop**.
2. Abre el archivo `.pbix` generado al final del laboratorio anterior.
3. Si Power BI solicita aplicar cambios pendientes de Power Query, selecciona **Aplicar cambios**.
4. Ve a la **vista Modelo**.
    Revisa que aparecen las siguientes tablas:
  - `Cliente`
  - `Destino`
  - `Envíos`
  - `Oficina`
  - `Ruta`
  - `Servicio`

### Tarea 2: Identificar hechos y dimensiones
1. Localiza la tabla `Envíos`.
2. Confirma que contiene columnas transaccionales, métricas y claves, por ejemplo:
  - `EnvíoID`
  - `FechaEnvío`
  - `ClienteID`
  - `ServicioID`
  - `OficinaID`
  - `RutaID`
  - `Peso (KG)`
  - `Precio total`
  - `Intentos`
  - `EntregaTardia`
3. Localiza las tablas de dimensión:
  - `Cliente`: atributos del cliente.
  - `Servicio`: atributos del servicio contratado.
  - `Oficina`: atributos de la oficina o hub operativo.
  - `Destino`: atributos geográficos del destino.
  - `Ruta`: atributos de la ruta o modo de transporte.
4. Comprueba que estas tablas contienen campos descriptivos que permitirán analizar, filtrar o agrupar los envíos.

**Resultado esperado:** identificas `Envíos` como tabla de hechos y el resto de tablas como dimensiones. El modelo objetivo debe tener una estructura en estrella, con relaciones de uno a varios desde dimensiones hacia la tabla de hechos.

---

## Ejercicio 2: Crear layouts útiles en la vista Modelo

En este ejercicio crearás pocos layouts, pero con un propósito claro. La idea es facilitar la lectura, explicación y mantenimiento del modelo.

Trabajarás con dos layouts:
- `01 - Modelo estrella`: vista principal del modelo completo.
- `02 - Servicio y SLA`: vista de apoyo para revisar atributos de servicio y SLA.

> Buena práctica: en modelos reales, demasiados layouts pueden generar ruido. Es preferible tener pocos layouts bien diseñados y con un objetivo claro.

### Tarea 1: Crear el layout principal del modelo estrella
1. En la **vista Modelo**, localiza las pestañas de layouts en la parte inferior.
2. Crea un nuevo layout y nómbralo `01 - Modelo estrella`.
3. Arrastra `Envíos` al centro del lienzo.
4. Coloca las cinco tablas de dimensiones alrededor.
5. Ajusta el ancho de las tablas para que se vean las claves y columnas principales.

### Tarea 2: Crear el layout de servicio y SLA
1. Crea un segundo layout y nómbralo `02 - Servicio y SLA`.
2. Añade solo las tablas necesarias para revisar la lógica de servicio:
  - `Envíos`
  - `Servicio`
3. Coloca `Envíos` en el centro del lienzo.
4. Coloca `Servicio` debajo o a un lado de `Envíos`.
5. Ajusta el ancho de las tablas para que se vean las columnas relacionadas con servicio, prioridad y SLA.

> Nota: en el **Laboratorio 3** añadirás la tabla `Fecha` y podrás ampliar este layout si quieres revisar relaciones temporales.

### Tarea 3: Revisar la utilidad de los layouts
1. Vuelve al layout `01 - Modelo estrella`.
2. Comprueba que el modelo se entiende de un vistazo.
3. Verifica que `Envíos` queda claramente identificada como tabla central.
4. Cambia al layout `02 - Servicio y SLA`.
5. Comprueba que contiene solo las tablas necesarias para revisar la lógica de servicio.
6. Si una tabla no aporta valor a un layout, elimínala del layout, no necesariamente del modelo.

**Resultado esperado:** el modelo queda organizado en dos layouts útiles: uno para explicar el modelo estrella y otro para revisar atributos de servicio y SLA.

---

## Ejercicio 3: Crear y administrar relaciones en Power BI

En este ejercicio crearás manualmente las relaciones principales del modelo. En este archivo, la creación automática de relaciones está intencionalmente deshabilitada, por lo que Power BI no habrá generado relaciones al cargar las tablas.

### Tarea 1: Confirmar que el modelo no tiene relaciones automáticas
1. En la **vista Modelo**, observa el lienzo del modelo.
2. Comprueba que las tablas aparecen sin líneas de relación entre ellas.

> Nota: esto es esperado. En este archivo, la creación automática de relaciones está intencionalmente deshabilitada para que las relaciones se creen manualmente durante el laboratorio.

3. En la cinta, selecciona **Modelado > Administrar relaciones**.
4. Revisa la ventana **Administrar relaciones**.
5. Confirma que no hay relaciones creadas o que no hay relaciones relevantes para el modelo.
6. Si encuentras alguna relación no esperada, selecciónala y pulsa **Eliminar**.

### Tarea 2: Crear la relación Cliente-Envíos
1. En la ventana **Administrar relaciones**, selecciona **Nuevo**.
2. Configura la relación:
  - **Tabla 1:** `Cliente`
  - **Columna:** `ClienteID`
  - **Tabla 2:** `Envíos`
  - **Columna:** `ClienteID`
  - **Cardinalidad:** Uno a varios (`1:*`)
  - **Dirección de filtro cruzado:** Simple
  - **Hacer esta relación activa:** Activado
3. Selecciona **Aceptar**.

### Tarea 3: Crear la relación Servicio-Envíos
4. Selecciona **Nuevo**.
5. Configura la relación:
  - **Tabla 1:** `Servicio`
  - **Columna:** `ServicioID`
  - **Tabla 2:** `Envíos`
  - **Columna:** `ServicioID`
  - **Cardinalidad:** Uno a varios (`1:*`)
  - **Dirección de filtro cruzado:** Simple
  - **Hacer esta relación activa:** Activado
6. Selecciona **Aceptar**.

### Tarea 4: Crear la relación Oficina-Envíos
1. Selecciona **Nuevo**.
2. Configura la relación:
  - **Tabla 1:** `Oficina`
  - **Columna:** `OficinaID`
  - **Tabla 2:** `Envíos`
  - **Columna:** `OficinaID`
  - **Cardinalidad:** Uno a varios (`1:*`)
  - **Dirección de filtro cruzado:** Simple
  - **Hacer esta relación activa:** Activado
3. Selecciona **Aceptar**.

### Tarea 5: Crear la relación Ruta-Envíos
1. Selecciona **Nuevo**.
2. Configura la relación:
  - **Tabla 1:** `Ruta`
  - **Columna:** `RutaID`
  - **Tabla 2:** `Envíos`
  - **Columna:** `RutaID`
  - **Cardinalidad:** Uno a varios (`1:*`)
  - **Dirección de filtro cruzado:** Simple
  - **Hacer esta relación activa:** Activado
3. Selecciona **Aceptar**.

### Tarea 6: Crear la relación Destino-Envíos
1. Selecciona **Nuevo**.
2. Configura la relación:
  - **Tabla 1:** `Destino`
  - **Columna:** `DestinoID`
  - **Tabla 2:** `Envíos`
  - **Columna:** `DestinoID`
  - **Cardinalidad:** Uno a varios (`1:*`)
  - **Dirección de filtro cruzado:** Simple
  - **Hacer esta relación activa:** Activado
3. Selecciona **Aceptar**.

> Nota: en un modelo productivo suele ser preferible relacionar tablas mediante claves numéricas o claves sustitutas estables cuando estén disponibles.

### Tarea 7: Validar las relaciones creadas
1. Cierra la ventana **Administrar relaciones**.
2. Vuelve a la **vista Modelo**.
3. Comprueba que ahora aparecen líneas de relación entre las dimensiones y `Envíos`.
4. Verifica que `Envíos` queda en el lado varios (`*`) de cada relación.
5. Verifica que las dimensiones quedan en el lado uno (`1`) de cada relación.
5. Comprueba que la dirección de filtro va desde las dimensiones hacia `Envíos`.

**Resultado esperado:** el modelo tiene relaciones activas desde cada dimensión hacia `Envíos`, con cardinalidad uno a varios y dirección de filtro cruzado simple.

---

## Ejercicio 4: Resolver una relación varios a varios con una tabla puente

En este ejercicio explorarás una relación conceptual de varios a varios entre `Cliente` y `Servicio` sin usar DAX.

En el modelo de ParcelCraft, un cliente puede utilizar varios servicios y un servicio puede ser utilizado por muchos clientes. Aunque esta relación existe en los datos, no conviene resolverla creando una relación directa varios a varios entre las dimensiones. En su lugar, crearás una **tabla puente** desde Power Query con las combinaciones únicas de cliente y servicio observadas en la tabla de hechos `Envíos`.

Este patrón permite representar la relación `Cliente` ↔ `Servicio` mediante dos relaciones de uno a varios (`1:*`):
- `Cliente` → `ClienteServicio`
- `Servicio` → `ClienteServicio`

### Tarea 1: Comprobar el escenario varios a varios
1. Ve a la **vista Informe**.
2. Añade una visualización de tabla.
3. Agrega los campos `Cliente[Cliente]` y `Servicio[Servicio]`.
4. Revisa el resultado.

La visualización no representa correctamente qué servicios están realmente asociados a cada cliente, porque `Cliente` y `Servicio` son dos dimensiones independientes.

> Observación: esto no significa que el modelo esté mal. En un modelo en estrella, las dimensiones normalmente filtran a la tabla de hechos, pero no se filtran directamente entre sí.

### Tarea 2: Crear la tabla puente en Power Query
1. En la cinta **Inicio**, selecciona **Transformar datos**.
2. En el Editor de Power Query, localiza la consulta `Envíos`.
3. Haz clic derecho sobre `Envíos` y selecciona **Referencia**.
4. Cambia el nombre de la nueva consulta a `ClienteServicio`.
5. Mueve la tabla a la sección **Auxiliares**. **No** deshabilites la carga.
6. Con la consulta `ClienteServicio` seleccionada, ve a **Inicio > Administrar columnas > Elegir columnas**.
7. Selecciona únicamente estas columnas:
  - `ClienteID`
  - `ServicioID`
8. Selecciona **Aceptar**.
9. Selecciona ambas columnas: `ClienteID` y `ServicioID`.
10. En la cinta, selecciona **Inicio > Quitar filas > Quitar duplicados**.
11. Revisa que la consulta contiene una fila por cada combinación única de cliente y servicio.
12. Ordena la columna `ClienteID` en orden ascendente.
13. Selecciona **Cerrar y aplicar**.

**Resultado esperado:** el modelo contiene una nueva tabla cargada llamada `ClienteServicio`, con las columnas `ClienteID` y `ServicioID`.

### Tarea 3: Crear un layout para la tabla puente
1. Ve a la **vista Modelo**.
2. Crea un nuevo layout llamado `03 - Puente Cliente Servicio`.
3. Añade al layout las tablas:
  - `Cliente`
  - `ClienteServicio`
  - `Servicio`
4. Coloca `ClienteServicio` en el centro.
5. Coloca `Cliente` a la izquierda.
6. Coloca `Servicio` a la derecha.

### Tarea 4: Crear relaciones con la tabla puente
1. En la cinta, selecciona **Modelado > Administrar relaciones**.
2. Selecciona **Detección automática**.
3. Si tu modelo está bien configurado, se habrán detectado estas dos nuevas relaciones:

| Tabla desde | Columna desde | Cardinalidad | Dirección de filtro cruzado | Tabla hasta | Columna hasta | Estado |
|---|---|---:|---|---|---|---|
| `ClienteServicio` | `ClienteID` | `*:1` | Simple | `Cliente` | `ClienteID` | Activo |
| `ClienteServicio` | `ServicioID` | `*:1` | Simple | `Servicio` | `ServicioID` | Activo |

- Selecciona **Aceptar**.
- Cierra la ventana **Administrar relaciones**.

> Importante: no crees una relación directa entre `Cliente` y `Servicio`. Tampoco conectes `ClienteServicio` con `Envíos` en este ejercicio.

### Tarea 5: Validar la tabla puente
1. Vuelve a la vista de informe.
2. Añade una nueva visualización de tabla.
3. Agrega los campos:
  - `Cliente[Cliente]`
  - `ClienteServicio[ServicioID]`
4. En el área de campos de la visualización, abre el menú desplegable de `ServicioID`.
5. Cambia la agregación a **Recuento distintivo**.
6. Revisa el resultado. Cada cliente debe mostrar el número de servicios realmente asociados en la tabla puente.

> Nota: en el conjunto de datos de prueba, es posible que siga habiendo 4 servicios por cliente.

1. Añade otra visualización de tabla.
2. Agrega los campos:
  - `Servicio[Servicio]`
  - `ClienteServicio[ClienteID]`
3. En el menú desplegable de `ClienteID`, cambia la agregación a **Recuento distintivo**.
4. Revisa el resultado. Cada servicio debe mostrar el número de clientes asociados en la tabla puente.

> Nota: en el conjunto de datos de prueba, es posible que siga habiendo 200 clientes por servicio.

**Resultado esperado:** puedes analizar asociaciones entre clientes y servicios usando la tabla puente.

---

## Ejercicio 5: Crear jerarquías de negocio

En este ejercicio crearás jerarquías para mejorar la navegación y el autoservicio en los informes.

### Tarea 1: Crear jerarquía geográfica de destino
1. Ve a la **vista Modelo**.
2. Expande la tabla `Destino`.
3. Haz clic derecho sobre `Región (Destino)`.
4. Selecciona **Crear jerarquía**.
5. Cambia el nombre a `Geografía`.
6. Añade las columnas en este orden:
  - `Región (Destino)`
  - `Ciudad (Destino)`
  - `Código Postal (Destino)`

### Tarea 2: Crear jerarquía operativa de oficina
1. Expande la tabla `Oficina`.
2. Crea una jerarquía llamada `Oficinas`.
3. Añade las columnas en este orden:
  - `Región`
  - `Ciudad`
  - `Oficina`

### Tarea 3: Crear jerarquía de servicio
1. Expande la tabla `Servicio`.
2. Crea una jerarquía llamada `Servicios`.
3. Añade las columnas en este orden:
  - `Segmento de servicio`
  - `Servicio`

**Resultado esperado:** el modelo contiene jerarquías reutilizables para análisis geográfico, operativo y de servicio.

---

## Ejercicio 6: Configurar propiedades de tablas y columnas

En este ejercicio mejorarás la experiencia de usuario del modelo ocultando columnas técnicas, configurando formatos y añadiendo metadatos.

### Tarea 1: Ocultar columnas de claves técnicas
1. En la **vista Modelo**, haz clic en el panel de Datos y selecciona **Expandir todo**.
2. Oculta las columnas que no deberían usarse directamente en visualizaciones:
  - `ClienteID` en todas las tablas.
  - `DestinoID` en todas las tablas.
  - `OficinaID` en todas las tablas.
  - `RutaID` en todas las tablas.
  - `ServicioID` en todas las tablas.

Para ocultar una columna:
3. Haz clic derecho sobre la columna.
4. Selecciona **Ocultar en la vista de informe**.

> Buena práctica: las claves técnicas son esenciales para relaciones, pero generalmente no deben exponerse al usuario final.

### Tarea 2: Configurar formatos numéricos
1. Selecciona `Envíos[Precio total]`.
2. En **Herramientas de columna**, configura:
  - **Formato:** Moneda
  - **Decimales:** 2
  - **Símbolo:** € Español (España)
3. Repite para:
  - `Precio base`
  - `Surplus fuel`

### Tarea 3: Configurar categorías de datos
1. Selecciona `Destino[Ciudad (Destino)]`.
2. En **Propiedades > Avanzado > Categoría de datos**, selecciona `Ciudad`.
3. Selecciona `Destino[Código Postal (Destino)]`.
4. Configura la categoría como `Código postal`.
5. Selecciona `Destino[Región (Destino)]`.
6. Configura la categoría como `País o región` si aplica a tu modelo.
7. Repite los pasos para las columnas `Ciudad` y `Región` de la tabla `Oficina`.

### Tarea 4: Configurar resumen predeterminado
1. Selecciona las columnas `Precio total`, `Peso (KG)` o `Intentos`.
2. En **Propiedades > Avanzado > Resumir por**, configúralas:
  - `Peso (KG)`: Promedio
  - `Intentos`: Promedio

### Tarea 5: Añadir descripciones
1. En la tabla `Envíos`, selecciona la columna `Entrega tardia`.
2. En el panel **Propiedades**, localiza **Descripción**.
3. Escribe: `Indica si la entrega se realizó después de la fecha y hora comprometida por SLA.`
4. Añade descripciones a estas columnas:
  - `FechaCompromisoSLA`: Fecha y hora máxima esperada de entrega según el SLA.
  - `Precio total`: Importe total del envío en euros.
  - `Intentos`: Número de intentos de entrega registrados.

**Resultado esperado:** el modelo es más claro para usuarios finales: las columnas técnicas quedan ocultas, los formatos son consistentes y las columnas tienen metadatos útiles.

---

## Entregables

Al finalizar, debes tener un archivo `.pbix` con:
- Modelo en estrella con `Envíos` como tabla de hechos.
- Dimensiones relacionadas directamente con la tabla de hechos.
- Layouts creados en la vista Modelo:
  - `01 - Modelo estrella`
  - `02 - Servicio y SLA`
  - `03 - Puente Cliente Servicio`
- Relaciones principales activas con cardinalidad `1:*` y filtro cruzado simple.
- Tabla puente `ClienteServicio` con relaciones `1:*` hacia `Cliente` y `Servicio`.
- Jerarquías:
  - `Geografía`
  - `Oficinas`
  - `Servicios`
- Columnas técnicas ocultas en la vista de informe.
- Formatos, categorías de datos y resúmenes predeterminados configurados.
- Descripciones añadidas a columnas clave.

---

# Resumen

En este laboratorio has convertido las tablas preparadas en Power Query en un **modelo semántico profesional**. Has revisado el diseño en estrella, creado layouts en la vista Modelo, configurado relaciones principales, creado una tabla puente para resolver una relación conceptual varios a varios, creado jerarquías de negocio y ajustado propiedades de tablas, columnas y relaciones.

En el siguiente laboratorio crearás la tabla `Fecha`, configurarás calendarios, ordenarás columnas temporales y definirás relaciones activas e inactivas para fechas alternativas.

#### Recursos útiles
- Documentación oficial de modelado en Power BI: https://learn.microsoft.com/power-bi/transform-model/desktop-relationship-view
- Guía de esquema en estrella en Power BI: https://learn.microsoft.com/power-bi/guidance/star-schema
- Crear y administrar relaciones en Power BI: https://learn.microsoft.com/power-bi/transform-model/desktop-create-and-manage-relationships

#### 🎉 Fin del laboratorio

¡Enhorabuena! Has completado la segunda parte del laboratorio de ParcelCraft. Ahora tienes un modelo en estrella preparado para incorporar análisis temporal avanzado en el siguiente laboratorio. 👏

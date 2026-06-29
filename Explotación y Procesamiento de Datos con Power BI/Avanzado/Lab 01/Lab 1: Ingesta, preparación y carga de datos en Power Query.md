# Laboratorio 1: Ingesta, preparación y carga de datos en Power Query

**ParcelCraft** es una empresa ficticia de logística que necesita preparar sus datos operativos para análisis en Power BI.

El equipo de BI recibe un archivo CSV llamado **`ParcelCraft_50k.csv`** con información de envíos, clientes, servicios, ubicaciones, transportistas, fechas, costes e incidencias en una única **tabla plana**.

El objetivo del laboratorio es usar **Power Query** para transformar esa fuente en un modelo analítico limpio y mantenible. Para ello, trabajarás con parámetros, limpieza de datos, creación de columnas, detección de duplicados y organización de consultas.

A partir del CSV original, construirás un **modelo en estrella** compuesto por una tabla de hechos de envíos y varias dimensiones, como fecha, cliente, servicio, ubicación, transportista y estado.

Al finalizar, tendrás un modelo preparado para analizar trazabilidad, rendimiento operativo, entregas tardías e incidencias logísticas.

> ℹ️ El enfoque del laboratorio está diseñado para reflejar una práctica frecuente en entornos reales: la capa analítica recibe una **tabla plana “silver/gold”** desde Snowflake o SAP y el equipo de BI debe volver a separar hechos y dimensiones para conseguir un modelo mantenible, rápido y predecible.

## Objetivos de aprendizaje

**Tiempo estimado total: 45 minutos**

Al finalizar este laboratorio serás capaz de:

- Importar un archivo CSV operativo mediante una **fuente parametrizada**.
- Cambiar la **ruta o URL de origen** sin rehacer las transformaciones aplicadas.
- Limpiar, perfilar y tipar datos con **Power Query**.
- Crear columnas de negocio mediante **columnas personalizadas** y **columnas a partir de ejemplos**.
- Identificar y filtrar **duplicados** usando múltiples columnas clave.
- Transformar una **tabla plana** en un **modelo en estrella**.
- Crear una **tabla de hechos** de envíos y dimensiones reutilizables.
- Aplicar operaciones de **dinamización** y **anulación de dinamización**.
- Combinar y anexar consultas para enriquecer o reconstruir datos.
- Organizar consultas, deshabilitar cargas auxiliares y aplicar buenas prácticas.
- Revisar código **M** y comentar pasos.

---
## Antes de empezar

### Requisitos

Para completar el laboratorio necesitas:

- Power BI Desktop instalado.
- Conexión a Internet.
- Conocimientos básicos de Power Query.
- Comprensión básica de modelos relacionales y modelo de estrella.

> [!NOTE]
> Tienes un archivo inicial y solución disponibles. Si vas a trabajar en el mismo archivo a lo largo de todos los laboratorios, recomiendo empezar a partir de este starter.
> - [Archivo inicial (Starter)](https://github.com/quizeth/Labs/blob/main/Explotaci%C3%B3n%20y%20Procesamiento%20de%20Datos%20con%20Power%20BI/Avanzado/Lab%2001/Files/01-A%20Starter%20-%20ParcelCraft.pbix)
> - [Archivo final (Solución)](https://github.com/quizeth/Labs/blob/main/Explotaci%C3%B3n%20y%20Procesamiento%20de%20Datos%20con%20Power%20BI/Avanzado/Lab%2001/Files/01-A%20Soluci%C3%B3n%20-%20ParcelCraft.pbix)
> 

### Convenciones usadas en el laboratorio

- Cuando se indique seleccionar una consulta, hazlo desde el panel **Consultas** del Editor de Power Query.
- Cuando se indique una opción de cinta, se mostrará como **pestaña > grupo > comando**.
- Los nombres de consultas, columnas y parámetros deben escribirse exactamente como se indica, salvo que el archivo tenga nombres equivalentes ligeramente distintos.
- Si Power Query detecta nombres de columnas diferentes a los esperados, usa la columna equivalente según el perfil de datos. Por ejemplo, una columna de fecha de envío puede llamarse `ShipDate`, `ShipmentDate`, `ShippingDate` o similar.

---
## Ejercicio 1: Conectar al archivo CSV mediante parámetro

En este ejercicio crearás un parámetro para almacenar la URL del archivo CSV y lo usarás como origen de datos. Esto facilitará cambiar la fuente posteriormente sin editar todos los pasos de la consulta.

### Tarea 1: Crear un archivo de Power BI

1. Abre **Power BI Desktop**.
2. Crea un informe en blanco.
3. En la cinta **Inicio**, selecciona **Transformar datos**.
4. Se abrirá el **Editor de Power Query**.

### Tarea 2: Crear un parámetro para la URL

1. En el Editor de Power Query, selecciona **Inicio > Administrar parámetros > Parámetro nuevo**.
2. Configura el parámetro con estos valores:

   | Propiedad | Valor |
   |---|---|
   | Nombre | `pRutaArchivo` |
   | Descripción | `URL o ruta del archivo ParcelCraft_50k.csv` |
   | Requerido | Activado |
   | Tipo | Texto |
   | Valores sugeridos | Cualquier valor |
   | Valor actual | `https://raw.githubusercontent.com/quizeth/Labs/refs/heads/main/Explotaci%C3%B3n%20y%20Procesamiento%20de%20Datos%20con%20Power%20BI/Avanzado/Lab%2001/Files/ParcelCraft_50k.csv` |

3. Selecciona **Aceptar**.

### Tarea 3: Conectar usando el parámetro

1. En Power Query, selecciona **Inicio > Nueva fuente > Consulta en blanco**.
2. Selecciona **Siguiente** para crear la consulta.
3. En el panel **Configuración de consulta**, cambia el nombre de la consulta a `stg_ParcelCraft_Raw`.
4. En la barra de fórmulas, escribe la siguiente expresión:

```powerquery
= Csv.Document(
    Web.Contents(pRutaArchivo),
    [Delimiter = ",", Encoding = 65001, QuoteStyle = QuoteStyle.Csv]
)
```

4. Presiona **Enter**.
5. Si no ha identificado automáticamente los encabezados y tipos de datos de cada columna, en la cinta, selecciona **Transformar > Usar la primera fila como encabezados**.
6. Revisa visualmente los nombres y tipos de columnas.
7. En **Vista > Calidad de columnas**, activa:
   - Calidad de columnas;
   - Distribución de columnas;
   - Perfil de columnas.

> Nota: Si Power Query muestra el perfil basado solo en las primeras 1.000 filas, cambia el perfil a **Basado en todo el conjunto de datos** desde la parte inferior de la ventana.

### Tarea 4: Cambiar la fuente de datos usando el parámetro

1. En el panel de consultas, selecciona el parámetro `pRutaArchivo`.
2. Copia la URL actual en un bloc de notas.
3. Modifica temporalmente el valor del parámetro agregando un carácter al final, por ejemplo `x`.
4. Selecciona la consulta `stg_ParcelCraft_Raw`.
5. Observa que Power Query muestra un error de conexión.
6. Vuelve al parámetro y restaura la URL original.
7. Selecciona de nuevo `stg_ParcelCraft_Raw` y comprueba que los datos vuelven a cargarse.

**Resultado esperado:** la consulta depende del parámetro y no de una URL escrita directamente dentro de varios pasos.

---

## Ejercicio 2: Perfilado, limpieza inicial y buenas prácticas

En este ejercicio prepararás una consulta base limpia que servirá como punto de partida para el modelo de estrella.

### Tarea 1: Crear una consulta staging

1. Haz clic derecho en `stg_ParcelCraft_Raw`.
2. Selecciona **Referencia**.
3. Cambia el nombre de la nueva consulta a `stg_ParcelCraft_Clean`.

> Importante: Usa **Referencia** y no **Duplicar**. Referenciar una consulta reduce mantenimiento porque los cambios del origen se heredan en las consultas dependientes.

### Tarea 2: Aplicar tipos de datos

1. Selecciona `stg_ParcelCraft_Clean`.
2. Revisa los tipos de datos detectados automáticamente.
3. Cambia manualmente los tipos según corresponda:
   - columnas de fecha: **Fecha** o **Fecha/Hora**;
   - columnas de importes: **Número decimal fijo**,
   - columnas de costes, pesos o distancias: **Número decimal**;
   - columnas de cantidades: **Número entero**;
   - identificadores: **Texto** o **Número entero**, según su naturaleza;
   - categorías, estados, ciudades, países, transportistas: **Texto**.
4. Cambia el nombre del paso aplicado a `Tipos definidos`.
   1. En **Pasos aplicados**, haz clic derecho sobre el paso.
   2. Selecciona **Cambiar nombre**.
   3. Escribe el nuevo nombre.

### Tarea 3: Renombrar columnas

En esta tarea renombrarás las columnas técnicas del archivo original para que sean más comprensibles para el usuario de negocio.

Los nombres originales proceden del sistema operativo de envíos y usan una convención técnica en mayúsculas, por ejemplo `SHIPMENT_ID`, `CUSTOMER_CODE` o `TOTAL_PRICE_EUR`. En Power BI es recomendable usar nombres más descriptivos, legibles y consistentes.

---

#### Opción A: Renombrar columnas manualmente

1. Selecciona la consulta `stg_ParcelCraft_Clean`.
2. En la vista previa de datos, localiza la columna `SHIPMENT_ID`.
3. Haz doble clic sobre el encabezado de la columna.
4. Cambia el nombre a `EnvíoID`.
5. Repite el proceso con las siguientes columnas:
      - SHIPMENT_DATE → FechaEnvío
      - ORDER_CREATED_TS_UTC → FechaCreación
      - LAST_EVENT_TS_UTC → FechaActualización
      - DELIVERED_TS_UTC → FechaEntrega
      - OFFICE_CODE → OficinaID
      - OFFICE_NAME → Oficina
      - CITY → Ciudad
      - REGION → Región
      - CUSTOMER_CODE → ClienteID
      - CUSTOMER_NAME → Cliente
      - SERVICE_CODE → ServicioID
      - SERVICE_NAME → Servicio
      - TOTAL_PRICE_EUR → Precio total
      - DELIVERY_STATUS → Estado
      - ATTEMPT_COUNT → Intentos

6. En el panel Pasos aplicados, renombra el paso como `Columnas renombradas`.


#### Opción B: Renombrar mediante M

También puedes renombrar todas las columnas en un único paso usando el Editor avanzado.

1. Selecciona la consulta `stg_ParcelCraft_Clean`.
2. En la cinta, selecciona Inicio > Editor avanzado.
3. Localiza el paso anterior al renombrado y añade una coma al final. En este caso, el paso anterior se llama `#"Tipos definidos"`.
4. Agrega el siguiente paso después de ese paso:
   ```
   #"Columnas renombradas" =
    Table.RenameColumns(
        #"Tipos definidos",
        {
            {"SHIPMENT_ID", "EnvíoID"},
            {"SHIPMENT_DATE", "FechaEnvío"},
            {"ORDER_CREATED_TS_UTC", "FechaCreación"},
            {"LAST_EVENT_TS_UTC", "FechaActualización"},
            {"DELIVERED_TS_UTC", "FechaEntrega"},
            {"OFFICE_CODE", "OficinaID"},
            {"OFFICE_NAME", "Oficina"},
            {"CITY", "Ciudad"},
            {"REGION", "Región"},
            {"HUB_TYPE", "Tipo de hub"},
            {"CUSTOMER_CODE", "ClienteID"},
            {"CUSTOMER_NAME", "Cliente"},
            {"CUSTOMER_SEGMENT", "Segmento"},
            {"CUSTOMER_TYPE", "Tipo de cliente"},
            {"PREFERRED_SERVICE_CODE", "Servicio preferido"},
            {"SERVICE_CODE", "ServicioID"},
            {"SERVICE_NAME", "Servicio"},
            {"PRIORITY_LEVEL", "Prioridad"},
            {"SLA_HOURS", "Horas SLA"},
            {"TEMPERATURE_CONTROL_FLAG", "ControlTemperatura"},
            {"DEST_POSTAL_CODE", "Código Postal (Destino)"},
            {"DEST_CITY", "Ciudad (Destino)"},
            {"DEST_REGION", "Región (Destino)"},
            {"ROUTE_CODE", "RutaID"},
            {"CARRIER_MODE", "Modo de transporte"},
            {"WEIGHT_KG", "Peso (KG)"},
            {"BASE_PRICE_EUR", "Precio base"},
            {"FUEL_SURCHARGE_EUR", "Surplus fuel"},
            {"TOTAL_PRICE_EUR", "Precio total"},
            {"DELIVERY_STATUS", "Estado"},
            {"ATTEMPT_COUNT", "Intentos"},
            {"EXTRACT_TS_UTC", "ÚltimaExtracción"}
        }
    )
   ```
5. Asegúrate de que la sección final `in` devuelve el nuevo paso:
   ```
   in
    #"Columnas renombradas"
   ```
**Resultado esperado**: ahora las columnas de la consulta tienen nombres funcionales y más adecuados para construir el modelo analítico.


### Tarea 4: Corregir errores de calidad
En la columna de Intentos se ha añadido un 0 al final. Como resultado, el número de intentos que se muestra no es real.

1. Ve a la columna **Intentos**.
2. Selecciona **Transformar** > **Extraer** > **Primeros caracteres**.
3. En **Recuento**, escribe 1 y selecciona **Aceptar**.
4. Renombra el paso aplicado como `Eliminado 0 final en intentos`.
5. Cambia el tipo de datos de **Intentos** a **Número entero**.

### Tarea 5: Crear columnas personalizadas

Crea indicadores de negocio que se usarán después en la tabla de hechos.

1. Selecciona **Agregar columna > Columna personalizada**.
2. Renombra la columna a `FechaCompromisoSLA`.
3. Configura la nueva columna con estos valores:

   - **Nombre de nueva columna:** `FechaCompromisoSLA`
   - **Fórmula de columna personalizada:**

   ```powerquery
   [FechaCreación] + #duration(0, [Horas SLA], 0, 0)
   ```

4. Selecciona **Aceptar**.
5. Renombra el paso aplicado a `FechaCompromisoSLA agregada`.
6. Cambia el tipo de datos de `FechaCompromisoSLA`a **Fecha/Hora**.

> La función `#duration(días, horas, minutos, segundos)` permite sumar una duración a una fecha u hora. En este caso, se suman 0 días, las horas indicadas en Horas SLA, 0 minutos y 0 segundos.

1. Selecciona **Agregar columna > Columna condicional**.
2. Crea una segunda columna llamada `Entrega tardía`.
3. Configura la nueva columna con estos valores:
   - Si **FechaEntrega** es igual a **null**, entonces **null**
   - O si **FechaEntrega** es posterior a **FechaCompromisoSla**, entonces **true**
   - De lo contrario, **false**

> Recuerda cambiar el tipo de valor de la segunda condición haciendo click en el icono y seleccionando **Seleccionar una columna**.

4. Selecciona **Aceptar**.
5. Renombra el paso aplicado a `Entrega tardía agregada`.
6. Cambia el tipo de datos de `Entrega tardía`a **Verdadero/Falso**.

**Resultado esperado**: la columna FechaCompromisoSLA representa la fecha y hora máxima esperada de entrega según el SLA, y EntregaTardia indica si el envío fue entregado fuera de plazo.


### Tarea 6: Crear una columna desde ejemplos

1. Selecciona **Agregar columna > Columna a partir de ejemplos > A partir de todas las columnas**.
2. Crea una columna llamada `Segmento de servicio`.
3. Escribe ejemplos basados en el contenido de columnas como tipo de servicio, prioridad o método de envío. Por ejemplo:
   - si el servicio contiene `Express` o `Urgente`, escribe `Premium`;
   - si contiene `Standard` o `Economy`, escribe `Estándar`;
4. Revisa la fórmula que Power Query genera automáticamente.
5. Selecciona **Aceptar**.
6. 5. Renombra el paso aplicado a `Segmento de servicio agregada`.


**Resultado esperado:** tendrás una consulta limpia, tipada y enriquecida con columnas calculadas.

### Tarea 7: Crear una columna para limpiar datos

1. Selecciona **Agregar columna > Columna condicional**.
2. Crea una segunda columna llamada `Rango de peso`.
3. Configura la nueva columna con estos valores:
      - Si `WEIGHT_BAND_RAW` no está vacío, devolver WEIGHT_BAND_RAW.
      - Si `Peso (KG)` es menor o igual que 1, entonces `0-1 kg`.
      - Si `Peso (KG)` es menor o igual que 5, entonces `1-5 kg`.
      - Si `Peso (KG)` es menor o igual que 10, entonces `5-10 kg`.
      - De lo contrario, `10+ kg`.

4. Selecciona **Aceptar**.
5. Renombra el paso aplicado a `Rango de peso agregada`.
6. Cambia el tipo de datos de `Rango de peso`a **Texto**.

---

## Ejercicio 3: Convertir tabla plana en modelo de estrella

En este ejercicio separarás la tabla plana en una tabla de hechos y varias dimensiones.

### Diseño objetivo

El modelo resultante tendrá esta estructura lógica:

- `Envíos`: tabla de hechos con métricas e identificadores.
- `Cliente`: información del cliente.
- `Servicio`: información del producto, servicio o tipo de envío.
- `Oficina`: información de cada oficina operativa.
- `Destino`: información geográfica del destino del envío.
- `Ruta`: información sobre el método de envío o canal logístico.

### Tarea 1: Crear la tabla de hechos

1. Haz clic derecho en `stg_ParcelCraft_Clean`.
2. Selecciona **Referencia**.
3. Cambia el nombre de la nueva consulta a `Envíos`.
4. Ve a **Inicio > Administrar columnas > Elegir columnas**.
5. En la ventana **Elegir columnas**, desactiva todas las columnas y selecciona únicamente las columnas  necesarias para análisis transaccional:

   - `EnvíoID`
   - `FechaEnvío`
   - `FechaCreación`
   - `FechaActualización`
   - `FechaEntrega`
   - `FechaCompromisoSLA`
   - `OficinaID`
   - `ClienteID`
   - `ServicioID`
   - `RutaID`
   - `Estado`
   - `Modo de transporte`
   - `Horas SLA`
   - `ControlTemperatura`
   - `Peso (KG)`
   - `Rango de peso`
   - `Precio base`
   - `Surplus fuel`
   - `Precio total`
   - `Intentos`
   - `EntregaTardia`

6. Comprueba que los tipos de datos son correctos.
7. Renombra el paso aplicado como `Columnas de hechos seleccionadas`.

### Tarea 2: Crear la dimensión `Cliente`

1. Haz clic derecho en `stg_ParcelCraft_Clean`.
2. Selecciona **Referencia**.
3. Cambia el nombre a `Cliente`.
4. Conserva las columnas relacionadas con clientes:
   - `ClienteID`;
   - `Cliente`;
   - `Segmento`;
   - `Industria`;
   - `Servicio preferido`.
5. Renombra el paso aplicado como `Columnas de cliente seleccionadas`.
6. Selecciona la columna que identifica de forma única a un cliente, `ClienteID`
7. En la cinta, selecciona **Inicio > Quitar filas > Quitar duplicados**.
8. Ordena de forma ascendente por el identificador de cliente.
9. Renombra el paso aplicado como `Filas ordenadas por ClienteID`.

### Tarea 3: Crear la dimensión `Servicio`

1. Crea una referencia desde `stg_ParcelCraft_Clean`.
2. Cambia el nombre a `Servicio`.
3. Conserva las columnas relacionadas con servicios:
   - `ServicioID`
   - `Servicio`
   - `Prioridad`
   - `Horas SLA`

5. Renombra el paso aplicado como `Columnas de servicio seleccionadas`.
6. Selecciona la columna que identifica de forma única a un cliente, `ServicioID`
7. En la cinta, selecciona **Inicio > Quitar filas > Quitar duplicados**.
8. Ordena de forma ascendente por `Horas SLA`.
9. Renombra el paso aplicado como `Filas ordenadas por SLA`.

### Tarea 4: Crear la dimensión `Oficina`

1. Crea una referencia desde `stg_ParcelCraft_Clean`.
2. Cambia el nombre a `Oficina`.
3. Conserva las columnas relacionadas con ubicación de oficina y destino:
   - `OficinaID`
   - `Oficina`
   - `Ciudad`
   - `Región`
   - `Tipo de hub`

4. Renombra el paso aplicado como `Columnas de oficina seleccionadas`.
5. Selecciona la columna que identifica de forma única a una oficina, `OficinaID`
6. En la cinta, selecciona **Inicio > Quitar filas > Quitar duplicados**.
7. Selecciona la columna `Región`.
8. En la cinta, selecciona **Inicio > Ordenar ascendente**.
9. Después, selecciona la columna `OficinaID`.
10. Mantén pulsada la tecla **Ctrl** y selecciona **Ordenar ascendente**.
11. Comprueba que los datos quedan ordenados primero por `Región` y, dentro de cada región, por `OficinaID`.
12. Renombra el paso aplicado como `Filas ordenadas por región y oficina`.

### Tarea 5: Crear la dimensión `Destino`
1. Crea una referencia desde `stg_ParcelCraft_Clean`.
2. Cambia el nombre a `Destino`.
3. Conserva las columnas relacionadas con el destino del envío:
   - `Código Postal (Destino)`
   - `Ciudad (Destino)`
   - `Región (Destino)`
4. Renombra el paso aplicado como `Columnas de destino seleccionadas`.
5. Selecciona la columna que identifica de forma única a un destino, `Código Postal (Destino)`
6. En la cinta, selecciona **Inicio > Quitar filas > Quitar duplicados**.
7. Ordena primero por `Región (Destino)` y después por `Ciudad (Destino)`:
   1. Selecciona la columna `Región (Destino)`.
   2. En la cinta, selecciona **Inicio** > **Ordenar ascendente**.
   3. Selecciona la columna `Ciudad (Destino)`.
   4. Mantén pulsada la tecla **Ctrl** y selecciona **Ordenar ascendente**.
8. Renombra el paso aplicado como `Filas ordenadas por región y CP`.
9. Añade una clave artificial para la dimensión. En la cinta, selecciona **Agregar columna **> **Columna de índice** > **Desde 1**.
    
> La columna `DestinoID` funcionará como clave sustituta de la dimensión. Esto evita relacionar la tabla de hechos usando varias columnas de texto como ciudad, región y código postal. Aunque ahora mismo cada código postal es único, esto lo hace más sostenible ya que `DestinoID` se mantiene única incluso si más adelante se añaden múltiples valores por código postal, como direcciones concretas.

10. Cambia el nombre de la nueva columna a `DestinoID`.
11. Renombra el paso aplicado como `Clave de destino agregada`.
12. Mueve la columna `DestinoID` al principio de la tabla.

> Más adelante, añadiremos la nueva columna de clave a la tabla de hechos `Envíos`.

### Tarea 6: Crear la dimensión `Ruta`

1. Crea una referencia desde `stg_ParcelCraft_Clean`.
2. Cambia el nombre a `Ruta`.
3. Conserva las columnas relacionadas con ruta y transporte:
   - `RutaID`
   - `Modo de transporte`

4. Renombra el paso aplicado como `Columnas de ruta seleccionadas`.
5. Selecciona la columna que identifica de forma única a una ruta, `RutaID`.
6. En la cinta, selecciona **Inicio > Quitar filas > Quitar duplicados**.
7. Selecciona la columna `RutaID`.
8. En la cinta, selecciona **Inicio > Ordenar ascendente**.
12. Renombra el paso aplicado como `Filas ordenadas por ruta`.

### Tarea 7: Ordena las consultas

1. En el panel de consulta, crea los siguientes grupos:
     - `Staging`
     - `Hechos`
     - `Dimensiones`
     - `Auxiliares`

2. Mueve las consultas a sus grupos correspondientes:
     - `Staging`: `pRutaArchivo`, `stg_ParcelCraft_Raw`, `stg_ParcelCraft_Clean`
     - `Hechos`: `Envíos`
     - `Dimensiones`: `Cliente`, `Servicio`, `Ubicación`, `Destino`, `Ruta`
     - `Auxiliares`: vacía por ahora

**Resultado esperado:** tendrás una consulta de hechos y varias dimensiones listas para cargar al modelo.

---

## Ejercicio 4: Dinamizar y anular dinamización de columnas

En este ejercicio practicarás transformaciones de pivot y unpivot para preparar datos analíticos.

### Tarea 1: Crear una consulta auxiliar de métricas
Vamos a crear una consulta auxiliar que se usará para practicar transformaciones de análisis, como anular dinamización y dinamizar columnas, a partir de métricas operativas y económicas del envío.

1. Haz clic derecho en `stg_ParcelCraft_Clean`.
2. Selecciona **Referencia**.
3. Cambia el nombre a `aux_MetricasPorServicio`.
4. Mueve la consulta al grupo `Auxiliares`.
5. Conserva columnas categóricas relacionadas con el servicio, por ejemplo:
   - `ServicioID`
   - `Servicio`
   - `Prioridad`

6. Conserva varias columnas numéricas que puedan analizarse como métricas:

   - `Horas SLA`
   - `Peso (KG)`
   - `Precio base`
   - `Surplus fuel`
   - `Precio total`
   - `Intentos`

7. Renombra el paso aplicado como `Columnas de métricas seleccionadas`.

### Tarea 2: Anular dinamización de columnas

En esta tarea convertirás varias columnas numéricas en pares atributo-valor. Esto permite analizar distintas métricas bajo una misma estructura.

1. Selecciona la consulta `aux_MetricasPorServicio`.
2. Selecciona las columnas numéricas:

   - `Horas SLA`
   - `Peso (KG)`
   - `Precio base`
   - `Surplus fuel`
   - `Precio total`
   - `Intentos`

3. En la cinta, selecciona **Transformar > Anular dinamización de columnas**.
4. Power Query generará dos nuevas columnas:

   - `Atributo`
   - `Valor`

5. Renombra el paso aplicado como: `Métricas anuladas`
6. Cambia el nombre de las columnas resultantes:

   - `Atributo` a `Metrica`
   - `Valor` a `ValorMetrica`

5. Renombra el paso aplicado como: `Métricas renombradas`

### Tarea 3: Dinamizar columnas

1. Crea una referencia de `aux_MetricasPorServicio`.
2. Cambia el nombre a `aux_MetricasPivot`.
3. Selecciona la columna `Metrica`.
4. Filtra la columna y quédate solo con el `Peso (KG)` y `Precio total`.
5. Selecciona **Transformar > Columna dinámica**.
6. En **Columna de valores**, selecciona `ValorMetrica`.
7. En **Opciones avanzadas**, elige **Suma**.
8. Revisa el resultado: cada métrica debe convertirse en una columna.
9. 5. Renombra el paso aplicado como: `Métricas dinamizadas`
10. Ajusta el tipo de datos de las nuevas columnas:
   - `Peso (KG)`: Número entero
   - `Precio total`: Número decimal fijo

> Este patrón es útil para pasar de formatos anchos a largos y de formatos largos a anchos según las necesidades de visualización y modelado.

---

## Ejercicio 5: Combinar y anexar tablas

En este ejercicio usarás consultas auxiliares para practicar combinaciones (merges) y anexos (appends).

### Tarea 1: Crear una tabla de tarifas por segmento

1. Selecciona **Inicio > Especificar datos**.
2. Crea una tabla llamada `map_TarifasServicio` con dos columnas:

| Servicio | FactorTarifa |
|---|---:|
| Same Day | 1,50 |
| Express | 1,25 |
| Standard | 1,00 |
| Economy | 0,85 |

3. Selecciona **Aceptar**.
4. Mueve la consulta al grupo **Auxiliares**.
5. Comprueba que `FactorTarifa` es número decimal.

### Tarea 2: Combinar consultas de servicio y tarifa

1. Selecciona `Servicio`.
2. Selecciona **Inicio > Combinar consultas**.
   - Tabla principal: `Servicio`.
   - Tabla secundaria: `map_TarifasServicio`.
   - Selecciona la columna `Prioridad` en ambas tablas.
   - Tipo de combinación: **Externa izquierda**.
8. Expande la columna resultante y conserva solo `FactorTarifa`. Desmarca la opción **Usar el nombre de columna original como prefijo**.
9. Crea una columna personalizada llamada `ImporteAjustado`:

### Tarea 3: Añadir DestinoID a tabla de hechos

1. Selecciona `Envíos`
2. Selecciona **Inicio > Combinar consultas**.
   - Tabla principal: `Servicio`.
   - Tabla secundaria: `Destino`.
   - Selecciona la columna `Código Postal (Destino)` en ambas tablas.
   - Tipo de combinación: **Externa izquierda**.
8. Expande la columna resultante y conserva solo `DestinoID`. Desmarca la opción **Usar el nombre de columna original como prefijo**.

### Tarea 4: Importar el archivo de duplicados para anexar

En esta tarea anexarás el archivo `ParcelCraft_50_dups.csv` a la consulta `stg_ParcelCraft_Raw` para simular una nueva carga operativa con registros duplicados. El archivo tiene la misma estructura técnica original del CSV de ParcelCraft. Al anexar los nuevos datos antes de aplicar la limpieza, las transformaciones de `stg_ParcelCraft_Clean` se aplicarán una sola vez sobre el conjunto combinado.

1. En Power Query, selecciona **Inicio > Nuevo origen > Web**.
2. Configura la conexión:
  - **Dirección URL:** `https://raw.githubusercontent.com/quizeth/Labs/refs/heads/main/Explotaci%C3%B3n%20y%20Procesamiento%20de%20Datos%20con%20Power%20BI/Avanzado/Lab%2001/Files/ParcelCraft_50_dups.csv`
  - **Tipo de autenticación:** Anónima
3. Selecciona **Siguiente**, revisa que la tabla se esté importando correctamente, y selecciona **Transform**.
4. Renombra la consulta a `stg_ParcelCraft_dups_Raw`.
5. Mueve la consulta al grupo **Staging**.
6. Selecciona `stg_ParcelCraft_dups_Raw` como la tabla para anexar y haz clic en **Aceptar**.

### Tarea 5: Anexa las consultas raw y valida el resultado

1. Selecciona la consulta `stg_ParcelCraft_Raw`.
2. Selecciona **Inicio > Anexar consultas**.
3. Renombra el paso aplicado como `Raw con duplicados anexados`.
4. Para validar que las consultas se han anexado correctamente, examina la distribución de columnas de `EnvíoID` en la tabla `stg_ParcelCraft_Clean` y observa como ha cambiado.

<img width="1173" height="276" alt="image" src="https://github.com/user-attachments/assets/9c93d180-4047-4749-a596-c6e5dcf95d4e" />

---

## Ejercicio 6: Organización de consultas y control de carga

En este ejercicio organizarás las consultas según su propósito y deshabilitarás la carga de consultas auxiliares.

### Tarea 1: Deshabilitar carga de consultas

1. Haz clic derecho en cada consulta staging y auxiliar. desactiva **Habilitar carga**.:
   - `stg_ParcelCraft_Raw`
   - `stg_ParcelCraft_dups_Raw`
   - `stg_ParcelCraft_Clean`
   - `aux_MetricasPorServicio`
   - `aux_MetricasPivot`
   - `map_TarifasServicio`
   - `qa_DuplicadosNegocio`
     
2. Si Power BI advierte que otras consultas dependen de ella, acepta mantener las dependencias.
3. Guarda el archivo y aplica los cambios. Verás que las tablas con la carga deshabilitada no aparecen en el panel de datos en Power BI.
   
> Buena práctica: carga al modelo solo las tablas que necesitas para reportar o relacionar. Las consultas staging y auxiliares deben permanecer como preparación interna.

---

## Ejercicio 7 (Opcional, recomendado): Identificar y filtrar duplicados por múltiples columnas

En este ejercicio detectarás duplicados de negocio basados en una combinación de columnas relevantes del conjunto de datos de ParcelCraft.

El objetivo no es eliminar filas vacías ni errores, sino identificar posibles registros repetidos del mismo envío y, si procede, conservar una versión única para análisis.

### Tarea 1: Crear consulta de auditoría de duplicados

1. Haz clic derecho en `stg_ParcelCraft_Clean`.
2. Selecciona **Referencia**.
3. Cambia el nombre a `qa_DuplicadosNegocio`.
4. Conserva las columnas que definen la unicidad lógica de un envío:
   - `EnvíoID`
   - `FechaEnvío`
   - `FechaCreación`
   - `OficinaID`
   - `ClienteID`
   - `ServicioID`
   - `Código Postal (Destino)`

5. Conserva también la columna `ÚltimaExtracción`.
> La columna `ÚltimaExtracción` no se usa para identificar duplicados, pero sí puede usarse después para conservar el registro más reciente.

6. Renombra el paso aplicado como `Columnas de auditoría seleccionadas`.

### Tarea 3: Agrupar para detectar duplicados
1. Selecciona **Transformar > Agrupar por**.
2. En la ventana **Agrupar por**, selecciona **Avanzado**.
3. Agrega agrupaciones para las todas las columnas, excepto `ÚltimaExtracción`.
4. Crea una nueva columna con esta configuración:
  - **Nombre de nueva columna**: `ConteoFilas`
  - **Operación**: Recuento de filas

5. Agrega las siguientes columnas de agrupación (todas excepto `ÚltimaExtracción`) y selecciona **Aceptar**:.
   - `EnvíoID`
   - `FechaEnvío`
   - `FechaCreación`
   - `OficinaID`
   - `ClienteID`
   - `ServicioID`
   - `Código Postal (Destino)`

6. Selecciona el menú desplegable de filtros (icono de la flecha hacia abajo) en el encabezado de la columna **ConteoFilas**.
7. Haz clic en **Filtro de número > **Mayor que**
8. Configura el filtro para conservar filas en las que "ConteoFilas" **es mayor que 1** y selecciona Aceptar.
9. Renombra el paso aplicado como `Duplicados filtrados`.

> Si esta consulta devuelve filas, significa que existen registros repetidos para la misma combinación lógica de envío, cliente, servicio, oficina y destino.

### Tarea 4: Eliminar duplicados en la tabla limpia

En esta tarea eliminarás los duplicados directamente sobre la consulta `stg_ParcelCraft_Clean`, sin crear una consulta adicional. El objetivo es conservar únicamente el registro más reciente de cada envío lógico.

> Importante: este enfoque modifica directamente la consulta limpia que usarás como base para el modelo. Si quieres conservar una versión de auditoría, crea antes una referencia o duplica la consulta.

1. Selecciona la consulta `stg_ParcelCraft_Clean`
2. Ordena la columna `ÚltimaExtracción` en orden descendente.

> Esto coloca primero el registro más reciente de cada posible duplicado.

3. Renombra el paso aplicado como `Filas ordenadas por última extracción`.
4. Selecciona las columnas que definen la unicidad lógica de un envío:
   - `EnvíoID`
   - `FechaEnvío`
   - `FechaCreación`
   - `OficinaID`
   - `ClienteID`
   - `ServicioID`
   - `Código Postal (Destino)`

5. Con esas columnas seleccionadas, ve a la cinta y selecciona **Inicio > Quitar filas > Quitar duplicados**
6. Actualiza la  vista previa de la consulta.
7. Vuelve a `qa_DuplicadosNegocio` y pulsa **Actualizar vista previa**.
8. Si los duplicados se han eliminado correctamente, la consulta debería estar vacía. Si quitas el filtro de `ConteoFilas`, verás que ahora el único valor de la columna es **1**.

> Al ordenar primero por ÚltimaExtracción descendente, Power Query conserva la primera aparición de cada combinación duplicada. Como la primera aparición es la más reciente, se mantiene el registro más actualizado.

---

## Ejercicio 8: (Opcional, avanzado) Sección avanzada de M y comentarios en pasos aplicados

En este ejercicio revisarás el código M generado por Power Query y añadirás comentarios para documentar la lógica de transformación aplicada sobre el conjunto de datos de ParcelCraft.

### Tarea 1: Abrir el Editor avanzado

1. Selecciona `stg_ParcelCraft_Clean`.
2. En la cinta, selecciona **Inicio > Editor avanzado**.
3. Revisa la estructura general del código M. Observa que cada transformación aplicada desde la interfaz de Power Query se convierte en un paso dentro del bloque `let`:

```powerquery
let
    Origen = ...,
    #"Tipos definidos" = ...,
    #"Columnas renombradas" = ...
in
    #"Duplicados quitados"
```
4. Identifica los pasos principales de la consulta, por ejemplo:
   - origen de datos;
   - cambio de tipos;
   - creación de columna condicional;
   - creación de columnas personalizadas;
   - renombrado de columnas;
   - ordenación por ÚltimaExtracción;
   - eliminación de duplicados. 


### Tarea 2: Añadir comentarios en M

En esta tarea añadirás comentarios al código M para documentar qué hace cada bloque de transformación.

1. Dentro del **Editor avanzado**, localiza los pasos principales.
2. Añade comentarios a cada paso describiendo qué hacen.
   - Añade comentarios de una línea usando `//`.
   - Añade comentarios de bloque (múltiples líneas) usando `/* . . . */`.

<details>
  <summary>Ejemplo de código comentado</summary>
  
   ```
   let
    // Paso 1: origen de datos ya anexado con el archivo principal y el archivo de duplicados
    Source = stg_ParcelCraft_Raw_Anexada,

    /*
       Paso 2: definición explícita de tipos de datos.
       Este paso es importante antes de crear columnas calculadas,
       especialmente para fechas, horas SLA, importes y pesos.
    */
    #"Tipos definidos" =
        Table.TransformColumnTypes(
            Source,
            {
                {"SHIPMENT_ID", type text},
                {"SHIPMENT_DATE", type date},
                {"ORDER_CREATED_TS_UTC", type datetimezone},
                {"LAST_EVENT_TS_UTC", type datetimezone},
                {"DELIVERED_TS_UTC", type datetimezone},
                {"OFFICE_CODE", type text},
                {"CUSTOMER_CODE", type text},
                {"SERVICE_CODE", type text},
                {"SLA_HOURS", Int64.Type},
                {"DEST_POSTAL_CODE", Int64.Type},
                {"WEIGHT_KG", type number},
                {"BASE_PRICE_EUR", type number},
                {"FUEL_SURCHARGE_EUR", type number},
                {"TOTAL_PRICE_EUR", type number},
                {"ATTEMPT_COUNT", Int64.Type},
                {"EXTRACT_TS_UTC", type datetimezone}
            }
        ),

    // Paso 3: cálculo de la fecha compromiso según la fecha de creación y las horas SLA
    #"Fecha compromiso SLA agregada" =
        Table.AddColumn(
            #"Tipos definidos",
            "FechaCompromisoSLA",
            each [ORDER_CREATED_TS_UTC] + #duration(0, [SLA_HOURS], 0, 0),
            type datetimezone
        ),

    // Paso 4: indicador lógico para identificar entregas fuera del SLA
    #"Entrega tardía agregada" =
        Table.AddColumn(
            #"Fecha compromiso SLA agregada",
            "EntregaTardia",
            each
                if [DELIVERED_TS_UTC] = null then null
                else if [DELIVERED_TS_UTC] > [FechaCompromisoSLA] then true
                else false,
            type logical
        ),

    /*
       Paso 5: renombrado de columnas técnicas.
       Este paso convierte nombres de sistema en nombres comprensibles
       para usuarios de negocio.
    */
    #"Columnas renombradas" =
        Table.RenameColumns(
            #"Entrega tardía agregada",
            {
                {"SHIPMENT_ID", "EnvíoID"},
                {"SHIPMENT_DATE", "FechaEnvío"},
                {"ORDER_CREATED_TS_UTC", "FechaCreación"},
                {"LAST_EVENT_TS_UTC", "FechaActualización"},
                {"DELIVERED_TS_UTC", "FechaEntrega"},
                {"OFFICE_CODE", "OficinaID"},
                {"OFFICE_NAME", "Oficina"},
                {"CITY", "Ciudad"},
                {"REGION", "Región"},
                {"HUB_TYPE", "Tipo de hub"},
                {"CUSTOMER_CODE", "ClienteID"},
                {"CUSTOMER_NAME", "Cliente"},
                {"CUSTOMER_SEGMENT", "Segmento"},
                {"CUSTOMER_TYPE", "Tipo de cliente"},
                {"PREFERRED_SERVICE_CODE", "Servicio preferido"},
                {"SERVICE_CODE", "ServicioID"},
                {"SERVICE_NAME", "Nombre del servicio"},
                {"PRIORITY_LEVEL", "Prioridad"},
                {"SLA_HOURS", "Horas SLA"},
                {"TEMPERATURE_CONTROL_FLAG", "ControlTemperatura"},
                {"DEST_POSTAL_CODE", "Código Postal (Destino)"},
                {"DEST_CITY", "Ciudad (Destino)"},
                {"DEST_REGION", "Región (Destino)"},
                {"ROUTE_CODE", "RutaID"},
                {"CARRIER_MODE", "Modo de transporte"},
                {"WEIGHT_KG", "Peso (KG)"},
                {"WEIGHT_BAND_RAW", "BandaPesoRaw"},
                {"BASE_PRICE_EUR", "Precio base"},
                {"FUEL_SURCHARGE_EUR", "Surplus fuel"},
                {"TOTAL_PRICE_EUR", "Precio total"},
                {"DELIVERY_STATUS", "Estado"},
                {"ATTEMPT_COUNT", "Intentos"},
                {"EXTRACT_TS_UTC", "ÚltimaExtracción"}
            }
        )
in
    #"Columnas renombradas"
   ```
</details>

> Nota: no es necesario que tu consulta tenga exactamente los mismos pasos. El objetivo es entender cómo se documentan las transformaciones y cómo Power Query traduce las acciones de la interfaz a lenguaje M.

3. Vuelve al panel de Pasos Aplicados y observa como puedes ver los comentarios como descripciones tipo tooltip (al pasar el cursor por encima).

### Tarea 3: Crear una función M para auditar duplicados

En esta tarea crearás una función reutilizable en M que reciba una tabla y una lista de columnas clave, y devuelva únicamente las combinaciones duplicadas.

Esta función permitirá auditar duplicados en cualquier consulta sin tener que repetir manualmente los pasos de agrupación.

---

## Entregables

Al finalizar, debes tener un archivo `.pbix` con:

1. Parámetro `pRutaArchivo`.
2. Consultas organizadas en grupos.
3. Una consulta staging limpia.
4. Una tabla de hechos.
5. Al menos cinco dimensiones.
6. Consultas auxiliares con carga deshabilitada.
7. Una consulta de auditoría de duplicados.
8. Uso de combinación y anexo de consultas.
9. Uso de dinamización y anulación de dinamización.
10. Al menos una columna personalizada y una columna desde ejemplos.

---

# Resumen
En este laboratorio has creado un flujo completo de preparación de datos en Power Query: desde la ingesta parametrizada de un CSV web hasta la construcción de un modelo de estrella preparado para análisis. También has practicado transformaciones intermedias y avanzadas, técnicas de auditoría, organización profesional de consultas y fundamentos de M.

### Recursos útiles
- Documentación oficial de Power Query: https://learn.microsoft.com/power-query/
- Mejores prácticas de modelado en Power BI: https://learn.microsoft.com/power-bi/guidance/star-schema
- Funciones del lenguaje M: https://learn.microsoft.com/powerquery-m/
- Guía de transformación de datos en Power BI: https://learn.microsoft.com/power-bi/transform-model/

### 🎉 Fin del laboratorio
¡Enhorabuena! Has completado este laboratorio y ya tienes una base sólida en preprocesamiento de datos con Power Query. Este es un paso clave para construir modelos analíticos robustos y escalables. ¡Nos vemos en el siguiente lab! 👏


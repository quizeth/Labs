# Laboratorio 1: Preprocesamiento de datos en Power Query

**ParcelCraft** es una empresa ficticia de logística y mensajería que necesita mejorar la calidad de sus datos operativos para responder tres preguntas de negocio:

1. ¿Qué oficinas están generando más incidencias de entrega?
2. ¿Qué servicios presentan mayor riesgo de incumplimiento del SLA?
3. ¿Cómo preparar un modelo de datos fiable para análisis de trazabilidad, entregas y optimización operativa?

El equipo de datos recibe diariamente una **tabla plana** exportada desde **Snowflake** que consolida información procedente de **SAP** y del sistema operativo de envíos. El extracto llega en formato CSV, con atributos de envíos, oficinas, clientes, servicios y trazabilidad en una sola tabla.

El problema es que esta extracción no está lista para análisis: contiene columnas repetidas, campos técnicos, algunos valores vacíos y registros duplicados del mismo envío debidos a reprocesos de carga. Tu objetivo es usar **Power Query** para limpiar la fuente, mantener únicamente el último registro válido por envío y **reconstruir un modelo en estrella** con una tabla de hechos y dimensiones reutilizables.

Además, durante el laboratorio simularás un escenario muy común en producción: **cambiar la fuente de datos de una consulta ya existente y transformada**, pasando del extracto `v1` al extracto `v2` sin perder la lógica aplicada.

> ℹ️
>  El enfoque del laboratorio está diseñado para reflejar una práctica frecuente en entornos reales: la capa analítica recibe una **tabla plana “silver/gold”** desde Snowflake o SAP y el equipo de BI debe volver a separar hechos y dimensiones para conseguir un modelo mantenible, rápido y predecible.

---

## Objetivos de aprendizaje

**Tiempo estimado total: 45 minutos**

Al finalizar este laboratorio serás capaz de:

- Importar una **tabla plana corporativa** procedente de Snowflake/SAP.
- Limpiar y tipar datos con **Power Query**.
- Resolver duplicados conservando el **registro más reciente por `SHIPMENT_ID`**.
- Crear columnas de negocio y normalizar valores incompletos.
- Separar una tabla plana en un **modelo en estrella**.
- Cambiar la **fuente de datos** de una consulta ya cargada y transformada sin rehacer el trabajo.

> [!IMPORTANT]
>
> Para hacer este ejercicio, necesitas haber completado el [Lab 0: Preparación del laboratorio](https://github.com/quizeth/Labs/blob/main/Explotaci%C3%B3n%20y%20Procesamiento%20de%20Datos%20con%20Power%20BI/Avanzado/Lab%200%3A%20Preparaci%C3%B3n%20del%20laboratorio.md).

---
# Ejercicio

## Tarea 1. Cargar la tabla plana

1. Abre Power BI Desktop y crea un nuevo informe en blanco.
2. Guarda el informe en tu carpeta de trabajo (ej. `C:\Labs\ParcelCraft'`) bajo el nombre **Análisis de ParcelCraft**.
3. Desde la cinta, selecciona **Obtener datos** > **Texto o CSV**.
4. En la **Ruta de acceso al archivo**, examina y selecciona el archivo `ParcelCraft_Flat_v1`.
5. Haz clic en **Siguiente**.
6. Selecciona la flecha para desplegar el menú en la esquina inferior derecha y selecciona **Transform**.
7. Renombra la consulta a `ParcelCraft_Flat`.

## Tarea 2. Crear parámetro de ruta al archivo

1. Desde Power Query, en el panel de **Pasos Aplicados**, selecciona el paso **Origen** de tu consulta `ParcelCraft_Flat`.
2. Desde la barra de fórmulas de M, copia la ruta de acceso del archivo.

<img width="1615" height="238" alt="image" src="https://github.com/user-attachments/assets/a51878f9-3abd-4357-910e-f8758915af41" />

4.  Ve a **Administrar Parámetros** > **Parámetro Nuevo**.
5. Crea un parámetro llamado `pFilePath`:
   - **Tipo**: Texto
   - **Valor actual**: Ruta del archivo `ParcelCraft_Flat_v1.csv`.

<img width="609" height="662" alt="image" src="https://github.com/user-attachments/assets/70c093c2-1c51-4480-b7ef-236e646b7d94" />

6. Vuelve al paso **Origen** de tu consulta `ParcelCraft_Flat`.
7. Sustituye la ruta de acceso del archivo por `pFilePath`.

<img width="705" height="38" alt="image" src="https://github.com/user-attachments/assets/0ad173dd-8b9e-4ee9-a6a8-46f17e028018" />

> La consulta no cambiará en este momento. Sin embargo, cambiar la ruta de acceso de un valor fijo a un paráetro en Power Query permite reutilizar la misma consulta con distintos archivos o ubicaciones sin tener que editar el código manualmente. Además, facilita el mantenimiento y hace el modelo más flexible y dinámico.

## Tarea 3. Perfilado inicial

Explora la consulta `ParcelCraft_Flat` usando las opciones de vista previa de datos y tu familiaridad con los datos:

<img width="344" height="117" alt="image" src="https://github.com/user-attachments/assets/0bd4b6fe-9cf1-467e-9823-74de9168ef63" />

- `OFFICE_*`, `CUSTOMER_*` y `SERVICE_*` serán dimensiones.
- `EXTRACT_TS_UTC` sirve para conservar el último registro por envío.
- Hay varios duplicados, un `ATTEMPT_COUNT` vacío y un `WEIGHT_BAND_RAW` vacío.

## Tarea 4. Crear la consulta limpia de negocio
1. Haz una **referencia** sobre `ParcelCraft_Flat`.
2. Renómbrala como `ParcelCraft_Flat_CLEAN`.
3. Selecciona **Elegir columnas**.
4. Desmarca las siguientes columnas, ya que no aportan valor analítico en este escenario.
   -  `SAP_DOCUMENT_NO`
   -  `SNOWFLAKE_BATCH_ID`
   -  `SOURCE_SYSTEM`
   -  `SOURCE_PLATFORM`

## Tarea 5. Limpiar y transformar la consulta
Ahora, aplica las siguientes transformaciones usando la interfaz:

1. **Reemplaza valores** en `ATTEMPT_COUNT` `null` por `0`.
2. **Ordena** `EXTRACT_TS_UTC` de más reciente a más antiguo (descendente).
3. **Quita duplicados** de la columna `SHIPMENT_ID`.  

> ℹ️ Recuerda que puedes navegar la tabla fácilmente mediante la opción **Ir a columna** de la pestaña **Vista**.


## Tarea 6. Agregar columnas personalizadas

1. **Agrega columna personalizada** para recalcular `WeightBand` si `WEIGHT_BAND_RAW` está vacío.
  - Ve a **Agregar columna > Columna personalizada**.
  - Renombra la nueva columna como `Weight Band`.
  - Configura la lógica:
    - Si `WEIGHT_BAND_RAW` es nulo o está vacío → recalcular `WeightBand` basándote en `WEIGHT_KG`.
    - En caso contrario → usar el valor de `WEIGHT_BAND_RAW`.
    
    ```powerquery
    if [WEIGHT_BAND_RAW] <> null and Text.Trim([WEIGHT_BAND_RAW]) <> "" then
        [WEIGHT_BAND_RAW]
    else if [WEIGHT_KG] <= 1 then
        "0-1 kg"
    else if [WEIGHT_KG] <= 5 then
        "1-5 kg"
    else if [WEIGHT_KG] <= 10 then
        "5-10 kg"
    else
        "10+ kg"
    ```
<img width="705" height="448" alt="image" src="https://github.com/user-attachments/assets/388a7e2e-2d2f-4f5f-af22-6ef94e743c64" />

2. **Agregar columna condicional** para marcar qué envíos han tenido una incidencia:
   - Ve a **Agregar columna > Columna condicional**.
   - Nombra a la columna `Has Incidence`.
   - Configura las condiciones:
     - Si `DELIVERY_STATUS` es `Exception` → `true`
     - Si `DELIVERY_STATUS` es `Returned` → `true`
     - De lo contrario → `false`
       
<img width="930" height="448" alt="image" src="https://github.com/user-attachments/assets/98f64d63-8750-46fd-aa79-ecb45fbb86c9" />

## Tarea 7. Mejorar la legibilidad del modelo

Revisa todos los campos de la consulta y ajústalos para mejorar la claridad y consistencia del modelo:
  - Renombra las columnas.
  - Verifica que cada columna tenga el **tipo de dato correcto**:
    - Fechas (`*_TS_UTC`, `*_DATE`)
    - Enteros (`ATTEMPT_COUNT`)
    - Decimales fijos (divisas) (`BASE_PRICE_EUR`)
    - Booleanos (true/false) (`HasIncident`)
    - Texto
  - **Presta especial atención a columnas creadas en pasos anteriores:**
  - Renombra los pasos de la consulta.

Utiliza las siguientes convenciones:

#### Convenciones generales

| Aspecto                  | Recomendación                                                                 |
|--------------------------|------------------------------------------------------------------------------|
| Visualización vs modelo | - Reporting: nombres claros y legibles (CamelCase) <br> - Modelo: compactos, sin espacios |
| Estilo de nombres        | Usa **CamelCase** (ej. `DestinationRegion`).                                 |
| Claridad                 | Prefiere nombres descriptivos y evita abreviaturas ambiguas.                |
| Orden de pasos           | Aplica renombres **al final** del flujo para facilitar el seguimiento.       |
| Tipos de datos           | Revisa siempre tipos tras transformaciones o columnas nuevas.               |
| Redundancia              | Elimina columnas innecesarias si ya tienes una versión derivada.            |
| Organización             | Ordena columnas: IDs → fechas → atributos → métricas.                       |


  - `WeightBand` → texto
  - `HasIncident` → booleano
  - `DeliverySpeedCategory` → texto categórico

## Tarea 8. Cambiar la fuente de datos de una consulta ya cargada y transformada
1. Abre el parámetro `pFilePath`.
2. Cambia el valor de `pFilePath` a la ruta de `ParcelCraft_Flat_v2.csv`.

> ℹ️ Si has guardado ambos archivos en la misma carpeta, solo tienes que cambiar el nombre.

4. Actualiza la vista previa.
5. Verifica que toda la transformación se mantiene.
6. Comprueba que el número de filas pasa de **40** a **46**.

## Tarea 9. Separar la tabla plana en hechos y dimensiones

### 1. Organizar las consultas
1. En el panel de consultas, haz clic derecho y crea los grupos:
  - `Fact`
  - `Dim`
  - `Staging`
2. Mueve las consultas existentes (`ParcelCraft_Flat` y `pFilePath`) a `Staging`.

### 2. Crear la tabla de hechos fct_shipments
1. Haz una referencia sobre `ParcelCraft_Flat`.
2. Renombra la consulta a `Envíos`.
## Tarea 9. Separar la tabla plana en hechos y dimensiones

### 1 Organizar las consultas
En el panel de consultas:

- Crea los grupos:
  - Fact
  - Dim
  - Staging
- Mueve la consulta base `ParcelCraft_Flat` al grupo **Staging**

### 2 Crear la tabla de hechos `Envíos`
1. Haz una **referencia** sobre `ParcelCraft_Flat`
2. Renombra la nueva consulta a **Envíos**
3. Conserva exactamente estas columnas:
   - `EnvíoID` (`SHIPMENT_ID`)
   - `FechaEnvío` (`SHIPMENT_DATE`)
   - `FechaCreación` (`ORDER_CREATED_TS_UTC`)
   - `FechaActualización` (`LAST_EVENT_TS_UTC`)
   - `FechaEntrega` (`DELIVERED_TS_UTC`)
   - `OficinaID` (`OFFICE_CODE`)
   - `ClienteID` (`CUSTOMER_CODE`)
   - `ServicioID` (`SERVICE_CODE`)
   - `Código Postal (Destino)` (`DEST_POSTAL_CODE`)
   - `RutaID` (`ROUTE_CODE`)
   - `Modo de transporte` (`CARRIER_MODE`)
   - `Peso (KG)` (`WEIGHT_KG`)
   - `Rango de peso` (`WEIGHT_BAND_RAW`)
   - `Precio base` (`BASE_PRICE_EUR`)
   - `Surplus fuel` (`FUEL_SURCHARGE_EUR`)
   - `Precio total` (`TOTAL_PRICE_EUR`)
   - `Estado` (`DELIVERY_STATUS`)
   - `Intentos` (`ATTEMPT_COUNT`)
   - `Incidencia` (`HAS_INCIDENT_RAW`)
   - `ÚltimaExtracción` (`EXTRACT_TS_UTC`)

4. Comprueba que el grano es:
   - **1 fila = 1 envío**
5. Mueve la consulta al grupo **Fact**

### 3 Crear las dimensiones
Crea las siguientes tablas a partir de una referencia a `ParcelCraft_Flat`:

#### `Oficina`
- Columnas:
  - `OficinaID` (`OFFICE_CODE`)
  - `Oficina` (`OFFICE_NAME`)
  - `Ciudad` (`CITY`)
  - `Región` (`REGION`)
  - `Tipo de hub` (`HUB_TYPE`)
- Quita duplicados por `OficinaID`
- Mueve a **Dim**

#### `Cliente`
- Columnas:
  - `ClienteID` (`CUSTOMER_CODE`)
  - `Cliente` (`CUSTOMER_NAME`)
  - `Segmento` (`CUSTOMER_SEGMENT`)
  - `Tipo de cliente` (`CUSTOMER_TYPE`)
  - `Servicio preferido` (`PREFERRED_SERVICE_CODE`)
- Quita duplicados por `ClienteID`
- Mueve a **Dim**

#### `Servicio`
- Columnas:
  - `ServicioID` (`SERVICE_CODE`)
  - `Nombre del servicio` (`SERVICE_NAME`)
  - `Prioridad` (`PRIORITY_LEVEL`)
  - `Horas SLA` (`SLA_HOURS`)
- Quita duplicados por `ServicioID`
- Mueve a **Dim**

#### `Destino`
- Renombra la consulta a **Destino**
- Conserva exactamente estas columnas:
  - `Código Postal (Destino)` (`DEST_POSTAL_CODE`)
  - `Ciudad (Destino)` (`DEST_CITY`)
  - `Región (Destino)` (`DEST_REGION`)
- Quita duplicados por `Código Postal (Destino)`
- Mueve a **Dim**

### 5 Deshabilitar carga de staging
Deshabilita la carga en:

- `ParcelCraft_Flat`
- `pFilePath`, si no lo está ya deshabilitada.

<img width="178" height="317" alt="image" src="https://github.com/user-attachments/assets/3b4471a3-c9b0-4231-9601-4aa4d2ba60ec" />

---

# Resumen
En este laboratorio hemos trabajado con una tabla plana de datos operativos de ParcelCraft y la hemos transformado en un modelo analítico útil mediante Power Query. Hemos aprendido a cargar datos desde un CSV, parametrizar la fuente para hacerla reutilizable, limpiar y tipar información, eliminar duplicados manteniendo el registro más reciente por envío, y enriquecer los datos con columnas calculadas. Finalmente, hemos reorganizado la información en un modelo en estrella separando hechos y dimensiones, mejorando la calidad y mantenibilidad del modelo.

### Recursos útiles
- Documentación oficial de Power Query: https://learn.microsoft.com/power-query/
- Mejores prácticas de modelado en Power BI: https://learn.microsoft.com/power-bi/guidance/star-schema
- Funciones del lenguaje M: https://learn.microsoft.com/powerquery-m/
- Guía de transformación de datos en Power BI: https://learn.microsoft.com/power-bi/transform-model/

### 🎉 Fin del laboratorio
¡Enhorabuena! Has completado este laboratorio y ya tienes una base sólida en preprocesamiento de datos con Power Query. Este es un paso clave para construir modelos analíticos robustos y escalables. ¡Nos vemos en el siguiente lab! 👏


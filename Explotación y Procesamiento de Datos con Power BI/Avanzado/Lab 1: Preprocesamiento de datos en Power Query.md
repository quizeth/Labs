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

## Tarea 2. Crear parámetro de ruta al archivo

1. Desde Power Query, en el panel de **Pasos Aplicados**, selecciona el paso **Origen** de tu consulta `ParcelCraft_Flat_v1`.
2. Desde la barra de fórmulas de M, copia la ruta de acceso del archivo.

<img width="1615" height="238" alt="image" src="https://github.com/user-attachments/assets/a51878f9-3abd-4357-910e-f8758915af41" />

4.  Ve a **Administrar Parámetros** > **Parámetro Nuevo**.
5. Crea un parámetro llamado `pFilePath`:
   - **Tipo**: Texto
   - **Valor actual**: Ruta del archivo `ParcelCraft_Flat_v1.csv`.

<img width="609" height="662" alt="image" src="https://github.com/user-attachments/assets/70c093c2-1c51-4480-b7ef-236e646b7d94" />

6. Vuelve al paso **Origen** de tu consulta `ParcelCraft_Flat_v1`.
7. Sustituye la ruta de acceso del archivo por `pFilePath`.

<img width="705" height="38" alt="image" src="https://github.com/user-attachments/assets/0ad173dd-8b9e-4ee9-a6a8-46f17e028018" />

> La consulta no cambiará en este momento. Sin embargo, cambiar la ruta de acceso de un valor fijo a un paráetro en Power Query permite reutilizar la misma consulta con distintos archivos o ubicaciones sin tener que editar el código manualmente. Además, facilita el mantenimiento y hace el modelo más flexible y dinámico.

## Tarea 3. Perfilado inicial

Explora la consulta `ParcelCraft_Flat_v1` usando las opciones de vista previa de datos y tu familiaridad con los datos:

<img width="344" height="117" alt="image" src="https://github.com/user-attachments/assets/0bd4b6fe-9cf1-467e-9823-74de9168ef63" />

- `OFFICE_*`, `CUSTOMER_*` y `SERVICE_*` serán dimensiones.
- `EXTRACT_TS_UTC` sirve para conservar el último registro por envío.
- Hay varios duplicados, un `ATTEMPT_COUNT` vacío y un `WEIGHT_BAND_RAW` vacío.

## Tarea 4. Crear la consulta limpia de negocio
1. Haz una **referencia** sobre `ParcelCraft_Flat_v1`.
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


### Tarea 6. Agregar columnas personalizadas

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
   - Configura las condiciones:
     - Si `DELIVERY_STATUS` es `Exception` → `true`
     - Si `DELIVERY_STATUS` es `Returned` → `true`
     - De lo contrario → `false`
       
<img width="931" height="449" alt="image" src="https://github.com/user-attachments/assets/3767b88a-48b6-474c-b173-814b869632cc" />

### Tarea 5. Mejorar la legibilidad del modelo

Revisa todos los campos de la consulta y ajústalos para mejorar la claridad y consistencia del modelo:
  - Renombra las columnas.
  - Verifica que cada columna tenga el **tipo de dato correcto**:
    - Fechas → `datetime` (`*_TS_UTC`, `*_DATE`)
    - Numéricos decimales → `decimal number` (`WEIGHT_KG`, precios)
    - Enteros → `whole number` (`ATTEMPT_COUNT`)
    - Booleanos → `true/false` (`HasIncident`)
    - Texto → columnas descriptivas
  - **Presta especial atención a columnas creadas en pasos anteriores:**

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

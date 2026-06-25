# Laboratorio 1: Preprocesamiento de datos en Power Query

ParcelCraft es una empresa ficticia del sector logístico que gestiona operaciones de transporte y entrega a nivel nacional. El equipo de analítica recibe regularmente datasets operacionales en formato plano provenientes de múltiples sistemas.

Estos datasets contienen información de envíos, oficinas, clientes y servicios en una única tabla. Aunque esto simplifica la ingestión, dificulta el análisis y el rendimiento en herramientas analíticas como Power BI.

El objetivo de este laboratorio es transformar un dataset plano en una estructura analítica optimizada siguiendo el modelo estrella. El dataset contiene aproximadamente 50.000 registros de envíos con las siguientes características:

- Cada fila representa un evento de envío
- Existe redundancia dimensional
- Existen duplicados por reprocesos
- Contiene datos operativos como estado, intentos y tiempos

> ℹ️
>  El enfoque del laboratorio está diseñado para reflejar una práctica frecuente en entornos reales: la capa analítica recibe una **tabla plana “silver/gold”** desde Snowflake o SAP y el equipo de BI debe volver a separar hechos y dimensiones para conseguir un modelo mantenible, rápido y predecible.


---

## Objetivos de aprendizaje

**Duración estimada: 45 minutos**

Al completar este laboratorio serás capaz de:

- Conectarte a datos mediante fuente web
- Analizar y perfilar un dataset plano
- Limpiar y transformar datos en Power Query
- Gestionar duplicados manteniendo registros válidos
- Crear columnas derivadas
- Construir un modelo en estrella

> [!IMPORTANT]
> Tienes disponible un archivo inicial [Starter.pbix](https://github.com/quizeth/Labs/blob/main/Explotaci%C3%B3n%20y%20Procesamiento%20de%20Datos%20con%20Power%20BI/Intermedio/Files/01%20Starter%20-%20ParcelCraft.pbix) y solución [Solution.pbix](https://github.com/quizeth/Labs/blob/main/Explotaci%C3%B3n%20y%20Procesamiento%20de%20Datos%20con%20Power%20BI/Intermedio/Files/01%20Soluci%C3%B3n%20-%20ParcelCraft.pbix).

---

# Ejercicio

## Tarea 1. Carga de datos

1. Abrir Power BI Desktop y crea un nuevo informe en blanco.
2. Guarda el informe en tu carpeta de trabajo (ej. `C:\Labs\ParcelCraft'`) bajo el nombre **Análisis ParcelCraft**.
3. Desde la cinta, selecciona **Obtener datos** > **Web**.
4. Configura la conexión:
     **Dirección URL:** `https://raw.githubusercontent.com/quizeth/Labs/refs/heads/main/Explotaci%C3%B3n%20y%20Procesamiento%20de%20Datos%20con%20Power%20BI/Intermedio/Files/ParcelCraft_50k.csv`
     **Tipo de autenticación**: Anónima
6. Selecciona la flecha para desplegar el menú en la esquina inferior derecha y selecciona **Transform**.
7. Selecciona la consulta **Consulta** en el panel de la izquierda y renómbrala a **ParcelCraft_RAW**.

---

## Tarea 2. Perfilado inicial del dataset

En esta fase, explora el dataset sin transformarlo. El objetivo es entender su estructura, calidad y posibles problemas.

---

### Explora la vista previa de datos

1. Ve a la pestaña **Vista** y activa las herramientas de perfilado y vista previa de datos:
    - Calidad de columna  
    - Distribución de columna  
    - Perfil de columnas  

| Vista | Qué muestra | Para qué sirve |
|------|------------|---------------|
| Calidad de columna | % valores válidos, nulos y errores | Detectar problemas de calidad |
| Distribución de columna | Valores únicos y frecuencia | Identificar dimensiones y cardinalidad |
| Perfil de columnas | Estadísticas (mín, máx, media) | Analizar métricas y outliers |

**Nota:** Empieza revisando columnas clave como `WEIGHT_KG`, `TOTAL_PRICE_EUR` o `ATTEMPT_COUNT`.

---

### Identifica la clave principal

Columna: `SHIPMENT_ID`

- Representa cada envío
- Puede aparecer repetida (múltiples estados del mismo envío)

**Qué comprobar:**

- Recuento total vs valores únicos
- Presencia de duplicados

**Nota:** No es única todavía; necesitarás deduplicar más adelante.

---

### Localiza la columna de control de versiones

Columna: `EXTRACT_TS_UTC`

| Propiedad | Descripción |
|----------|-------------|
| Tipo | Timestamp |
| Función | Indica cuándo se generó el registro |

**Uso:**
- Identifica el último estado del envío
- Ordena de más reciente a más antiguo

**Regla clave:** quédate siempre con el mayor `EXTRACT_TS_UTC`.

---

### Identifica las dimensiones

Busca columnas descriptivas (no métricas):

| Grupo | Ejemplo | Qué representa |
|------|--------|----------------|
| OFFICE_* | OFFICE_CODE, CITY | Ubicación |
| CUSTOMER_* | CUSTOMER_NAME | Cliente |
| SERVICE_* | SERVICE_NAME | Tipo de servicio |
| REGION | REGION | Nivel geográfico |

**Cómo detectarlas:**
- Pocos valores únicos
- Se repiten muchas veces
- Describen entidades

**Ejemplo:** `SERVICE_CODE` → pocos valores (STD, EXP, etc.)

---

### Revisa los problemas de calidad

#### 1. Duplicados de envíos

- Columna: `SHIPMENT_ID`
- Causa: reprocesos, actualizaciones de estado

**Impacto:**
- Métricas infladas (doble conteo)

---

#### 2. Valores nulos

Revisa especialmente:

- `ATTEMPT_COUNT`
- `DELIVERED_TS_UTC`
- `WEIGHT_BAND_RAW`

| Tipo | Interpretación | Acción |
|------|---------------|--------|
| Null en intentos | No registrado | Sustituir por 0 |
| Null en entrega | No entregado | Mantener |

---

#### 3. Inconsistencias en WEIGHT_BAND_RAW

Problema:
- Valores vacíos o nulos

Valor esperado:
- "0-1 kg", "1-5 kg", "5-10 kg", "10+ kg"

**Solución:**
- Recalcular usando `WEIGHT_KG`

---

Después del perfilado, debes tener claro:

- Qué columna identifica cada envío
- Cómo obtener el estado más reciente
- Qué columnas son dimensiones
- Qué problemas debes corregir

> En proyectos reales, este paso es crítico antes de cualquier transformación.
---

### Tarea 3. Crear la consulta limpia de negocio
1. Haz clic derecho en la consulta `ParcelCraft_RAW` y selecciona **Referencia**.
2. Renombra la nueva consulta como como `ParcelCraft_CLEAN`.
   
> Al crear una consulta mediante **Referencia**, no duplicas los datos físicamente. En su lugar, creas una nueva consulta que **hereda todos los pasos de la consulta original** hasta ese momento. Esto te permite:
>
> - Reutilizar la lógica ya existente sin modificar la consulta base  
> - Crear variantes (como versiones limpias, hechos o dimensiones) de forma controlada  
> - Mantener una arquitectura ordenada (staging → transformación → modelo)  
>
> Cualquier cambio en la consulta original se propagará automáticamente a las consultas referenciadas, lo que facilita el mantenimiento del modelo.

4. En la cinta, selecciona **Elegir columnas**.
5. Desmarca las siguientes columnas, ya que no aportan valor analítico en este escenario y haz clic en **Aceptar**.
   -  `SAP_DOCUMENT_NO`
   -  `SNOWFLAKE_BATCH_ID`
   -  `SOURCE_SYSTEM`
   -  `SOURCE_PLATFORM`

<img width="356" height="633" alt="image" src="https://github.com/user-attachments/assets/73ea7010-bb23-4084-819b-6586d962dbf2" />

---

## Tarea 4. Limpieza de datos
Ahora, aplica las siguientes transformaciones usando la interfaz:

1. **Reemplaza valores** en `ATTEMPT_COUNT` `null` por `0`.
2. **Ordena** `EXTRACT_TS_UTC` de más reciente a más antiguo (descendente).
3. **Quita duplicados** de la columna `SHIPMENT_ID`.  

> ℹ️ Recuerda que puedes navegar la tabla fácilmente mediante la opción **Ir a columna** de la pestaña **Vista**.


---

## Tarea 5. Agregar columnas personalizadas

1. **Agrega una columna personalizada** para recalcular el rango de peso si `WEIGHT_BAND_RAW` está vacío.
  - Ve a **Agregar columna > Columna personalizada**.
  - Renombra la nueva columna como `Rango de peso`.
  - Configura la lógica:
    - Si `WEIGHT_BAND_RAW` es nulo o está vacío → recalcula `Rango de peso` basándote en `WEIGHT_KG`.
    - En caso contrario → usa el valor de `WEIGHT_BAND_RAW`.

    
    ```powerquery
    if [WEIGHT_BAND_RAW] <> null and Text.Trim([WEIGHT_BAND_RAW]) <> "" then
    [WEIGHT_BAND_RAW]
    else if [WEIGHT_KG] <= 1 then "0-1 kg"
    else if [WEIGHT_KG] <= 5 then "1-5 kg"
    else if [WEIGHT_KG] <= 10 then "5-10 kg"
    else "10+ kg"
    ```

<img width="705" height="454" alt="image" src="https://github.com/user-attachments/assets/446c45dd-c6cd-4a0d-a578-cbd4d4d92c7f" />


2. **Agrega una columna condicional** para marcar qué envíos han tenido una incidencia:
   - Ve a **Agregar columna > Columna condicional**.
   - Nombra a la columna `Incidencia`.
   - Configura las condiciones:
     - Si `DELIVERY_STATUS` es `Exception` → `true`
     - Si `DELIVERY_STATUS` es `Returned` → `true`
     - De lo contrario → `false`
       
<img width="932" height="450" alt="image" src="https://github.com/user-attachments/assets/40bdbfe8-6355-44f4-8cf1-dc1fffa530f9" />

---

## Tarea 6. Mejorar la legibilidad del modelo

Revisa todos los campos de la consulta y ajústalos para mejorar la claridad y consistencia del modelo. **Presta especial atención a columnas creadas en pasos anteriores**:
1. Renombra las columnas.

  > Si no quieres renombrar todas las columnas de forma manual, aquí tienes las instrucciones para poder renombrarlas de una rápidamente mediante M:

<details>
<summary>-- Cómo renombrar todas las columnas con M --</summary>

1. Renombra una o varias columnas de forma manual para practicar.

2. Selecciona el último paso aplicado **Columnas con nombre cambiado**

<img width="242" height="199" alt="image" src="https://github.com/user-attachments/assets/50248b81-2a3f-45ad-85aa-1005e541d88b" />

3. Selecciona la fórmula de M en la barra encima de la vista previa de la consulta.

<img width="1478" height="212" alt="image" src="https://github.com/user-attachments/assets/f1f4f252-6c23-4b8e-bbe1-faa2aaf952c5" />

4. Sustituye la fórmula por la siguiente:
```
= Table.RenameColumns(#"Columna condicional agregada",{{"SHIPMENT_ID", "EnvíoID"}, {"SHIPMENT_DATE", "FechaEnvío"}, {"ORDER_CREATED_TS_UTC", "FechaCreación"}, {"LAST_EVENT_TS_UTC", "FechaActualización"}, {"DELIVERED_TS_UTC", "FechaEntrega"}, {"OFFICE_CODE", "OficinaID"}, {"OFFICE_NAME", "Oficina"}, {"CITY", "Ciudad"}, {"REGION", "Región"}, {"HUB_TYPE", "Tipo de hub"}, {"CUSTOMER_CODE", "ClienteID"}, {"CUSTOMER_NAME", "Cliente"}, {"CUSTOMER_SEGMENT", "Segmento"}, {"CUSTOMER_TYPE", "Tipo de cliente"}, {"PREFERRED_SERVICE_CODE", "Servicio preferido"}, {"SERVICE_CODE", "ServicioID"}, {"SERVICE_NAME", "Nombre del servicio"}, {"PRIORITY_LEVEL", "Prioridad"}, {"SLA_HOURS", "Horas SLA"}, {"TEMPERATURE_CONTROL_FLAG", "ControlTemperatura"}, {"DEST_POSTAL_CODE", "Código Postal (Destino)"}, {"DEST_CITY", "Ciudad (Destino)"}, {"DEST_REGION", "Región (Destino)"}, {"ROUTE_CODE", "RutaID"}, {"CARRIER_MODE", "Modo de transporte"}, {"WEIGHT_KG", "Peso (KG)"}, {"BASE_PRICE_EUR", "Precio base"}, {"FUEL_SURCHARGE_EUR", "Surplus fuel"}, {"TOTAL_PRICE_EUR", "Precio total"}, {"DELIVERY_STATUS", "Estado"}, {"ATTEMPT_COUNT", "Intentos"}, {"EXTRACT_TS_UTC", "ÚltimaExtracción"}})
```
<img width="1486" height="306" alt="image" src="https://github.com/user-attachments/assets/6b42c58e-faec-4f35-aa71-a2f7034744fe" />

</details>

  
2. Verifica que cada columna tenga el **tipo de dato correcto**:
    - Fechas (`*_TS_UTC`, `*_DATE`)
    - Enteros (`ATTEMPT_COUNT`)
    - Decimales fijos (divisas) (`BASE_PRICE_EUR`)
    - Booleanos (true/false) (`Incidencia`)
    - Texto
3. Renombra los pasos de la consulta.

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


---

## Tarea 6. Construir el modelo estrella
Antes de nada, vamos a organizar las consultas.

1. Haz clic derecho en el panel de consultas y crea tres grupos:
   - `Staging`
   - `Fact`
   - `Dim`
2. Mueve las dos consultas existentes a `Staging`.
3. Haz una **referencia** sobre `ParcelCraft_CLEAN`
2. Renombra la nueva consulta a **Envíos**. Esta será nuestra tabla de hechos.
3. Conserva exactamente estas columnas:
   - `EnvíoID` (`SHIPMENT_ID`)
   - `FechaEnvío` (`SHIPMENT_DATE`)
   - `FechaCreación` (`ORDER_CREATED_TS_UTC`)
   - `FechaActualización` (`LAST_EVENT_TS_UTC`)
   - `FechaEntrega` (`DELIVERED_TS_UTC`)
   - `OficinaID` (`OFFICE_CODE`)
   - `ClienteID` (`CUSTOMER_CODE`)
   - `ServicioID` (`SERVICE_CODE`)
   - `Peso (KG)` (`WEIGHT_KG`)
   - `Rango de peso` (`WEIGHT_BAND_RAW`)
   - `Precio base` (`BASE_PRICE_EUR`)
   - `Surplus fuel` (`FUEL_SURCHARGE_EUR`)
   - `Precio total` (`TOTAL_PRICE_EUR`)
   - `Estado` (`DELIVERY_STATUS`)
   - `Intentos` (`ATTEMPT_COUNT`)
   - `Incidencia` (`HAS_INCIDENT_RAW`)
5. Mueve la consulta al grupo **Fact**.
6. Crea las siguientes tablas a partir de una referencia a `ParcelCraft_CLEAN`:
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

7. En el panel de consultas, haz clic derecho en `ParcelCraft_RAW` y `ParcelCraft_CLEAN` y deshabilita la carga de ambas consultas.

<img width="177" height="308" alt="image" src="https://github.com/user-attachments/assets/223573ab-a71a-49a7-825b-30b1e9d8389c" />

8. Para terminar, cierra y aplica los cambios. Tras unos segundos, verás tu nuevo modelo de datos en Power BI.

---
# Resumen
En este laboratorio hemos trabajado con una tabla plana de datos operativos de ParcelCraft y la hemos transformado en un modelo analítico útil mediante Power Query. Hemos aprendido a cargar datos desde un CSV en un sitio web, limpiar y tipar información, eliminar duplicados manteniendo el registro más reciente por envío, y enriquecer los datos con columnas calculadas. Finalmente, hemos reorganizado la información en un modelo en estrella separando hechos y dimensiones, mejorando la calidad y mantenibilidad del modelo.

### Recursos útiles
- Mejores prácticas de modelado en Power BI: https://learn.microsoft.com/power-bi/guidance/star-schema
- Guía de transformación de datos en Power BI: https://learn.microsoft.com/power-bi/transform-model/
- - Documentación oficial de Power Query: https://learn.microsoft.com/power-query/

### 🎉 Fin del laboratorio
¡Enhorabuena! Has completado este laboratorio y ya tienes una base sólida en preprocesamiento de datos con Power Query. Este es un paso clave para construir modelos analíticos robustos y escalables. ¡Nos vemos en el siguiente lab! 👏

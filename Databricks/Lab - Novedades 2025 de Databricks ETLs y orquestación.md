# Laboratorio — Lakehouse Ops: Modernización de ETLs con Databricks

## Objetivo del laboratorio

Modernizar la operación de una arquitectura medallón existente usando Databricks como plano principal de ejecución, validación, observabilidad y orquestación interna.

La opción principal del laboratorio usa una fuente real:

```text
Azure SQL Database / databricksdemodb / SalesLT
  → Conector Databricks: Add data → Ingest data from SQL Server
  → databricksdemos.bronze
  → Validación
  → Silver / Gold
  → Observabilidad
  → Databricks Job
  → ADF como disparador externo
  → DevOps/Terraform como despliegue repetible
```

> **Nota arquitectónica:** En arquitecturas modernas la ingesta también puede ejecutarse desde Databricks mediante Lakeflow Connect. ADF no desaparece: mantiene su rol como disparador externo, coordinador cross-platform y punto de integración con procesos corporativos.

Si la conexión a Azure SQL no funciona durante la sesión, se usará un plan B con datos demo generados dentro de Databricks, manteniendo los mismos nombres y estructuras de tabla que la ingesta principal.

---

## Agenda del laboratorio

**Duración estimada:** 70 minutos

- Preparación del entorno serverless
- Capacidades modernas de Databricks aplicadas en el laboratorio
- Ingesta desde Azure SQL SalesLT con conector Databricks
- Validación de tablas Bronze
- Validación, observabilidad y controles operativos
- Modernización Silver/Gold
- Crear Job multitarea preparado para ADF
- Buenas prácticas de despliegue con Azure DevOps y Terraform
- Validación final y cierre arquitectónico
- Plan B con datos demo dentro de Databricks

---

## Mapa del laboratorio contra el objetivo de la sesión

Este laboratorio cubre los tres contenidos principales de la sesión:

1. **Capacidades modernas de Databricks**
   - SQL Warehouse Serverless
   - Serverless compute for notebooks
   - Conector gestionado hacia Azure SQL
   - Unity Catalog como plano de gobierno
   - Databricks Jobs / Lakeflow Jobs como DAG operativo

2. **Integración de Databricks Jobs orquestados mediante ADF**
   - ADF invoca un único Job principal
   - Databricks gestiona dependencias internas
   - Databricks gestiona validaciones, retries, alertas y observabilidad

3. **Despliegue automatizado con Azure DevOps y Terraform**
   - Qué artefactos se versionan en Git
   - Qué parte se despliega como código
   - Qué variables cambian por entorno
   - Cómo se promociona dev → test → prod

---

# 1. Preparación del entorno serverless

## 1.1 Variables del laboratorio

Valores usados durante el laboratorio:

```text
catalog=databricksdemos
bronze_schema=bronze
silver_schema=silver
gold_schema=gold
ops_schema=ops
process_date=2025-01-15
env=dev
source_system=saleslt
```

Datos de conexión a Azure SQL:

```text
Connection name: DemoDB
Host: databricksdemosql.database.windows.net
Port: 1433
Database: databricksdemodb
Schema: SalesLT
Username: databricks_lab_user
Password: <password_de_laboratorio>
```

> **Recomendación de seguridad:** La contraseña de laboratorio se compartirá por un canal seguro durante la sesión. No debe incluirse en notebooks, repositorios ni documentación versionada.

## 1.2 Crear SQL Warehouse Serverless

El SQL Warehouse se usará para consultas desde **SQL Editor**.

1. Ir a **Compute → SQL warehouses**.
2. Pulsar **Create SQL warehouse**.
3. Configurar:

```text
Name: wh_lakehouse_ops_lab
Cluster size: 2X-Small
Min clusters: 1
Max clusters: 1
Auto stop: 10 minutes
Type: Serverless
```

4. Pulsar **Create**.
5. Esperar a que el estado sea **Running**.

## 1.3 Validar el SQL Warehouse desde SQL Editor

1. Ir a **SQL Editor**.
2. Si aparece la pantalla inicial, pulsar **SQL Query**.
3. Ejecutar:

```sql
SHOW CATALOGS;
```

4. Pulsar **Run all**.
5. Si aparece la ventana **Attach to an existing compute resource**, seleccionar:

```text
Compute type: SQL Warehouse
Warehouse: wh_lakehouse_ops_lab
```

6. Pulsar **Attach and run**.

**Resultado esperado:** La query se ejecuta correctamente y el warehouse queda asociado al SQL Editor.

## 1.4 Crear schemas del laboratorio

Desde SQL Editor, ejecutar:

```sql
CREATE SCHEMA IF NOT EXISTS databricksdemos.bronze;
CREATE SCHEMA IF NOT EXISTS databricksdemos.silver;
CREATE SCHEMA IF NOT EXISTS databricksdemos.gold;
CREATE SCHEMA IF NOT EXISTS databricksdemos.ops;
```

Validar:

```sql
SHOW SCHEMAS IN databricksdemos;
```

**Resultado esperado:**

```text
bronze
silver
gold
ops
```

También puede aparecer:

```text
default
```

## 1.5 Preparar notebook con Serverless compute

1. Ir a **Workspace → Create → Notebook**.
2. Crear un notebook Python llamado `lakehouse_ops_transformations`.
3. En el selector de compute del notebook, elegir **Serverless**.
4. Ejecutar:

```python
spark.version
```

**Resultado esperado:** La celda devuelve la versión de Spark asociada al compute serverless.

---

# 2. Capacidades modernas de Databricks aplicadas en el laboratorio

## 2.1 Objetivo

Identificar qué capacidades actuales de la plataforma se están aplicando en el flujo práctico.

Durante el laboratorio se usarán estos patrones:

```text
SQL Warehouse Serverless
  → consultas, validaciones y exploración SQL

Serverless compute for notebooks
  → transformaciones PySpark sin gestionar clusters interactivos

Conector Databricks hacia Azure SQL
  → ingesta gestionada desde SalesLT hacia Bronze

Unity Catalog
  → catálogo, schemas, tablas gobernadas y separación bronze/silver/gold/ops

Databricks Jobs / Lakeflow Jobs
  → DAG multitarea con dependencias explícitas

ops schema
  → resultados de calidad y métricas operativas

ADF como disparador externo
  → ADF invoca el Job principal; Databricks gobierna el DAG interno

DevOps/Terraform
  → versionado y despliegue repetible del patrón
```

> **Nota:** En arquitecturas modernas la ingesta también puede ejecutarse desde Databricks mediante Lakeflow Connect. Esto refuerza el patrón donde Databricks gestiona ingesta, transformación, validación y observabilidad internas, mientras ADF mantiene triggers y coordinación externa.

---

# 3. Ingesta desde Azure SQL SalesLT con conector Databricks

## 3.1 Objetivo

Ingerir tablas reales desde Azure SQL Database hacia la capa Bronze de Databricks usando el asistente visual:

```text
Add data → Ingest data from SQL Server
```

Tablas origen:

```text
databricksdemodb.SalesLT.Customer
databricksdemodb.SalesLT.Product
databricksdemodb.SalesLT.SalesOrderHeader
databricksdemodb.SalesLT.SalesOrderDetail
```

Tablas destino esperadas:

```text
databricksdemos.bronze.saleslt_customer_raw
databricksdemos.bronze.saleslt_product_raw
databricksdemos.bronze.saleslt_sales_order_header_raw
databricksdemos.bronze.saleslt_sales_order_detail_raw
```

## 3.2 Crear conexión

En Databricks:

```text
Add data → Ingest data from SQL Server
```

En la pantalla de conexión:

```text
Auth Type: Username and password
Connection name: DemoDB
Host: databricksdemosql.database.windows.net
Port: 1433
Username: databricks_lab_user
Password: <password_de_laboratorio>
```

Si la interfaz solicita la base de datos en este paso:

```text
Database: databricksdemodb
```

Pulsar **Authenticate and create connection**.

## 3.3 Configurar pipeline de ingesta

En **Ingestion setup**, seleccionar:

```text
Query based capture
```

Nombre del pipeline:

```text
lakehouse_ops_saleslt_ingest
```

Event log location:

```text
Catalog: databricksdemos
Schema: ops
```

Pulsar **Create pipeline and continue**.

## 3.4 Seleccionar tablas origen

En **Source**, seleccionar únicamente:

```text
Customer
Product
SalesOrderHeader
SalesOrderDetail
```

No seleccionar tablas adicionales para mantener el laboratorio acotado.

## 3.5 Configurar cada tabla

Configurar las tablas así:

```text
Customer
  Destination name: saleslt_customer_raw
  Cursor column: ModifiedDate
  Primary key(s): CustomerID

Product
  Destination name: saleslt_product_raw
  Cursor column: ModifiedDate
  Primary key(s): ProductID

SalesOrderHeader
  Destination name: saleslt_sales_order_header_raw
  Cursor column: ModifiedDate
  Primary key(s): SalesOrderID
```

Si `ModifiedDate` no aparece para `SalesOrderHeader`, usar:

```text
Cursor column: OrderDate
```

```text
SalesOrderDetail
  Destination name: saleslt_sales_order_detail_raw
  Cursor column: ModifiedDate
  Primary key(s): SalesOrderID, SalesOrderDetailID
```

Si la interfaz solo permite una primary key en `SalesOrderDetail`, usar:

```text
Primary key(s): SalesOrderDetailID
```

## 3.6 Seleccionar destino

En **Destination**, seleccionar:

```text
Catalog: databricksdemos
Schema: bronze
```

Pulsar **Save and continue**.

## 3.7 Schedules y notificaciones

En **Schedules and notifications**:

```text
Schedule: opcional
Success: desmarcado
Failure: marcado
Email recipient: <tu_email>
```

Pulsar **Save and run pipeline**.

---

# 4. Validación de tablas Bronze

## 4.1 Validar tablas desde SQL Editor

Desde SQL Editor con `wh_lakehouse_ops_lab`, ejecutar:

```sql
SHOW TABLES IN databricksdemos.bronze;
```

**Resultado esperado:**

```text
saleslt_customer_raw
saleslt_product_raw
saleslt_sales_order_header_raw
saleslt_sales_order_detail_raw
```

Si se dejaron nombres por defecto en el asistente, pueden aparecer nombres como:

```text
Customer
Product
SalesOrderHeader
SalesOrderDetail
```

> Si aparecen nombres por defecto, renombrar o ajustar las referencias antes de continuar. El resto del laboratorio asume los nombres `saleslt_*_raw`.

## 4.2 Validar muestras

```sql
SELECT *
FROM databricksdemos.bronze.saleslt_customer_raw
LIMIT 10;

SELECT *
FROM databricksdemos.bronze.saleslt_product_raw
LIMIT 10;

SELECT *
FROM databricksdemos.bronze.saleslt_sales_order_header_raw
LIMIT 10;

SELECT *
FROM databricksdemos.bronze.saleslt_sales_order_detail_raw
LIMIT 10;
```

## 4.3 Validar conteos

```sql
SELECT COUNT(*) AS customers_count
FROM databricksdemos.bronze.saleslt_customer_raw;

SELECT COUNT(*) AS products_count
FROM databricksdemos.bronze.saleslt_product_raw;

SELECT COUNT(*) AS order_headers_count
FROM databricksdemos.bronze.saleslt_sales_order_header_raw;

SELECT COUNT(*) AS order_details_count
FROM databricksdemos.bronze.saleslt_sales_order_detail_raw;
```

## 4.4 Nota sobre el schema ops

Si esta consulta devuelve vacío en este punto:

```sql
SHOW TABLES IN databricksdemos.ops;
```

no es un error.

El schema `ops` empezará a contener tablas cuando se ejecuten los pasos de validación y observabilidad, que crearán objetos como:

```text
quality_results
validation_summary
pipeline_run_summary
```

---

# 5. Revisión del patrón de validación Bronze → Silver

## 5.1 Objetivo

En esta sección se revisa el patrón de validación que controla el paso de Bronze a Silver desde una perspectiva operativa.

El objetivo no es sustituir un framework de validación existente, sino asegurar que el resultado de las validaciones queda:

- Persistido
- Consultable
- Trazable por ejecución
- Integrado con Databricks Jobs
- Disponible para diagnóstico operativo

El patrón operativo es:

```text
Bronze
  → validate_bronze_to_silver
    → Silver
```

La validación debe responder a estas preguntas:

- ¿Los datos son válidos para avanzar a Silver?
- ¿Qué reglas han fallado?
- ¿Qué métrica se observó?
- ¿Cuál era el umbral esperado?
- ¿Dónde queda registrada la evidencia?
- ¿Debe detenerse el pipeline?

## 5.2 Tabla operacional de resultados

Los resultados de validación se registrarán en:

```text
databricksdemos.ops.quality_results
```

Campos mínimos recomendados:

```text
rule_id
rule_description
table_name
metric_value
threshold_value
status
severity
env
source_pattern
process_date
created_at
```

Estos campos permiten consultar el estado de calidad por:

- Fecha de proceso
- Tabla
- Regla
- Fuente
- Entorno
- Estado PASS/FAIL
- Severidad CRITICAL/WARNING

También se generará una tabla resumen del gate:

```text
databricksdemos.ops.validation_summary
```

## 5.3 Notebook — Gate operativo Bronze → Silver

En Databricks, crear un notebook Python con este nombre:

```text
validate_bronze_to_silver
```

Este ejemplo valida las tablas Bronze procedentes de Azure SQL SalesLT:

```text
databricksdemos.bronze.saleslt_customer_raw
databricksdemos.bronze.saleslt_product_raw
databricksdemos.bronze.saleslt_sales_order_header_raw
databricksdemos.bronze.saleslt_sales_order_detail_raw
```

Código:

```python
from pyspark.sql import functions as F

# ---------------------------------------------------------------------
# 1. Parámetros básicos de ejecución
# ---------------------------------------------------------------------
# Este bloque permite ejecutar el notebook tanto dentro de un Databricks Job
# como manualmente durante el laboratorio. Si dbutils no está disponible,
# se usan valores por defecto para no romper pruebas manuales.
try:
    dbutils.widgets.text("env", "dev")
    dbutils.widgets.text("catalog", "databricksdemos")
    dbutils.widgets.text("process_date", "2025-01-15")
    dbutils.widgets.text("source_system", "saleslt")

    env = dbutils.widgets.get("env")
    catalog = dbutils.widgets.get("catalog")
    process_date = dbutils.widgets.get("process_date")
    source_pattern = dbutils.widgets.get("source_system")
except Exception:
    catalog = "databricksdemos"
    env = "dev"
    process_date = "2025-01-15"
    source_pattern = "saleslt"

# Si es True, el notebook fallará cuando existan reglas críticas fallidas.
# Este comportamiento convierte la validación en un gate operativo.
fail_on_error = True

# ---------------------------------------------------------------------
# 2. Preparar schema operacional
# ---------------------------------------------------------------------
spark.sql(f"CREATE SCHEMA IF NOT EXISTS {catalog}.ops")
spark.sql(f"USE CATALOG {catalog}")

# ---------------------------------------------------------------------
# 3. Definir tablas Bronze que se van a validar
# ---------------------------------------------------------------------
source_tables = {
    "customer": f"{catalog}.bronze.saleslt_customer_raw",
    "product": f"{catalog}.bronze.saleslt_product_raw",
    "order_header": f"{catalog}.bronze.saleslt_sales_order_header_raw",
    "order_detail": f"{catalog}.bronze.saleslt_sales_order_detail_raw"
}

# ---------------------------------------------------------------------
# 4. Comprobar que las tablas esperadas existen
# ---------------------------------------------------------------------
missing_tables = []
for logical_name, table_name in source_tables.items():
    if not spark.catalog.tableExists(table_name):
        missing_tables.append(table_name)

if len(missing_tables) > 0:
    raise Exception(
        "Missing expected Bronze tables: " + ", ".join(missing_tables)
    )

# ---------------------------------------------------------------------
# 5. Función auxiliar para construir resultados de calidad
# ---------------------------------------------------------------------
def result(rule_id, table_name, rule_description, metric_value, threshold_value, passed, severity):
    """
    Devuelve una tupla estándar con el resultado de una regla de calidad.
    severity:
        CRITICAL: la regla bloquea el paso a Silver.
        WARNING: la regla informa, pero no bloquea.
    """
    return (
        rule_id,
        table_name,
        rule_description,
        float(metric_value),
        float(threshold_value),
        "PASS" if passed else "FAIL",
        severity
    )

results = []

# ---------------------------------------------------------------------
# 6. Regla crítica: cada tabla debe tener filas
# ---------------------------------------------------------------------
for logical_name, table_name in source_tables.items():
    row_count = spark.table(table_name).count()
    results.append(
        result(
            rule_id=f"Q-COUNT-{logical_name.upper()}",
            table_name=table_name,
            rule_description=f"{logical_name} must have rows",
            metric_value=row_count,
            threshold_value=1,
            passed=row_count >= 1,
            severity="CRITICAL"
        )
    )

# ---------------------------------------------------------------------
# 7. Regla crítica: las claves principales no deben ser nulas
# ---------------------------------------------------------------------
customer_nulls = (
    spark.table(source_tables["customer"])
    .filter(F.col("CustomerID").isNull())
    .count()
)

product_nulls = (
    spark.table(source_tables["product"])
    .filter(F.col("ProductID").isNull())
    .count()
)

header_nulls = (
    spark.table(source_tables["order_header"])
    .filter(F.col("SalesOrderID").isNull())
    .count()
)

detail_nulls = (
    spark.table(source_tables["order_detail"])
    .filter(F.col("SalesOrderDetailID").isNull())
    .count()
)

results.append(
    result(
        rule_id="Q-PK-CUSTOMER",
        table_name=source_tables["customer"],
        rule_description="CustomerID must not be null",
        metric_value=customer_nulls,
        threshold_value=0,
        passed=customer_nulls == 0,
        severity="CRITICAL"
    )
)

results.append(
    result(
        rule_id="Q-PK-PRODUCT",
        table_name=source_tables["product"],
        rule_description="ProductID must not be null",
        metric_value=product_nulls,
        threshold_value=0,
        passed=product_nulls == 0,
        severity="CRITICAL"
    )
)

results.append(
    result(
        rule_id="Q-PK-HEADER",
        table_name=source_tables["order_header"],
        rule_description="SalesOrderID must not be null",
        metric_value=header_nulls,
        threshold_value=0,
        passed=header_nulls == 0,
        severity="CRITICAL"
    )
)

results.append(
    result(
        rule_id="Q-PK-DETAIL",
        table_name=source_tables["order_detail"],
        rule_description="SalesOrderDetailID must not be null",
        metric_value=detail_nulls,
        threshold_value=0,
        passed=detail_nulls == 0,
        severity="CRITICAL"
    )
)

# ---------------------------------------------------------------------
# 8. Regla informativa: volumen mínimo de líneas de pedido
# ---------------------------------------------------------------------
# Esta regla es únicamente demostrativa; según el volumen cargado puede
# quedar PASS o FAIL. No bloquea el paso a Silver/Gold.
order_detail_count = spark.table(source_tables["order_detail"]).count()
results.append(
    result(
        rule_id="Q-VOLUME-ORDER-DETAIL",
        table_name=source_tables["order_detail"],
        rule_description="Order detail should have at least 10 rows",
        metric_value=order_detail_count,
        threshold_value=10,
        passed=order_detail_count >= 10,
        severity="WARNING"
    )
)

# ---------------------------------------------------------------------
# 9. Convertir resultados a DataFrame
# ---------------------------------------------------------------------
quality_df = (
    spark.createDataFrame(
        results,
        [
            "rule_id",
            "table_name",
            "rule_description",
            "metric_value",
            "threshold_value",
            "status",
            "severity"
        ]
    )
    .withColumn("env", F.lit(env))
    .withColumn("source_pattern", F.lit(source_pattern))
    .withColumn("process_date", F.to_date(F.lit(process_date)))
    .withColumn("created_at", F.current_timestamp())
)

# ---------------------------------------------------------------------
# 10. Preparar tabla operacional quality_results
# ---------------------------------------------------------------------
quality_table = f"{catalog}.ops.quality_results"
spark.sql(f"""
CREATE TABLE IF NOT EXISTS {quality_table} (
    rule_id STRING,
    table_name STRING,
    rule_description STRING,
    metric_value DOUBLE,
    threshold_value DOUBLE,
    status STRING,
    severity STRING,
    env STRING,
    source_pattern STRING,
    process_date DATE,
    created_at TIMESTAMP
)
USING DELTA
""")

existing_quality_columns = [
    field.name.lower()
    for field in spark.table(quality_table).schema.fields
]

if "severity" not in existing_quality_columns:
    spark.sql(f"""
    ALTER TABLE {quality_table}
    ADD COLUMNS (
        severity STRING
    )
    """)

quality_df = quality_df.select(
    "rule_id",
    "table_name",
    "rule_description",
    "metric_value",
    "threshold_value",
    "status",
    "severity",
    "env",
    "source_pattern",
    "process_date",
    "created_at"
)

# ---------------------------------------------------------------------
# 11. Persistir resultados en quality_results
# ---------------------------------------------------------------------
quality_df.write.format("delta").mode("append").option("mergeSchema", "true").saveAsTable(
    quality_table
)

# ---------------------------------------------------------------------
# 12. Mostrar resultados de calidad
# ---------------------------------------------------------------------
display(quality_df)

# ---------------------------------------------------------------------
# 13. Calcular reglas fallidas por severidad
# ---------------------------------------------------------------------
failed_critical_rules = (
    quality_df
    .filter((F.col("status") == "FAIL") & (F.col("severity") == "CRITICAL"))
    .count()
)

failed_warning_rules = (
    quality_df
    .filter((F.col("status") == "FAIL") & (F.col("severity") == "WARNING"))
    .count()
)

# ---------------------------------------------------------------------
# 14. Crear resumen operativo de la validación
# ---------------------------------------------------------------------
summary_df = (
    spark.createDataFrame(
        [
            (
                "validate_bronze_to_silver",
                source_pattern,
                failed_critical_rules,
                failed_warning_rules
            )
        ],
        [
            "step_name",
            "source_pattern",
            "failed_critical_rules",
            "failed_warning_rules"
        ]
    )
    .withColumn("env", F.lit(env))
    .withColumn("process_date", F.to_date(F.lit(process_date)))
    .withColumn("created_at", F.current_timestamp())
)

# ---------------------------------------------------------------------
# 15. Preparar tabla operacional validation_summary
# ---------------------------------------------------------------------
summary_table = f"{catalog}.ops.validation_summary"
spark.sql(f"""
CREATE TABLE IF NOT EXISTS {summary_table} (
    step_name STRING,
    source_pattern STRING,
    failed_critical_rules BIGINT,
    failed_warning_rules BIGINT,
    env STRING,
    process_date DATE,
    created_at TIMESTAMP
)
USING DELTA
""")

summary_df = summary_df.select(
    "step_name",
    "source_pattern",
    "failed_critical_rules",
    "failed_warning_rules",
    "env",
    "process_date",
    "created_at"
)

# ---------------------------------------------------------------------
# 16. Persistir resumen operativo
# ---------------------------------------------------------------------
summary_df.write.format("delta").mode("append").option("mergeSchema", "true").saveAsTable(
    summary_table
)

display(summary_df)

# ---------------------------------------------------------------------
# 17. Gate operativo Bronze → Silver
# ---------------------------------------------------------------------
# Si hay reglas críticas fallidas, este notebook falla.
# En un Databricks Job, esto detiene las tareas dependientes.
# Por tanto, build_silver_gold no debería ejecutarse.
if failed_critical_rules > 0 and fail_on_error:
    raise Exception(
        f"Bronze to Silver validation failed. "
        f"Critical failed rules: {failed_critical_rules}. "
        f"Check {catalog}.ops.quality_results."
    )
```

## 5.4 Validar resultados de calidad

Ejecutar desde SQL Editor:

```sql
SELECT
  process_date,
  source_pattern,
  table_name,
  rule_id,
  rule_description,
  metric_value,
  threshold_value,
  status,
  severity,
  created_at
FROM databricksdemos.ops.quality_results
WHERE process_date = DATE('2025-01-15')
ORDER BY created_at DESC, rule_id;
```

**Resultado esperado:** Las reglas aparecen con status `PASS` o `FAIL` y severity `CRITICAL` o `WARNING`.

Resumen por estado y severidad:

```sql
SELECT
  status,
  severity,
  COUNT(*) AS rules_count
FROM databricksdemos.ops.quality_results
WHERE process_date = DATE('2025-01-15')
GROUP BY status, severity
ORDER BY severity, status;
```

**Resultado esperado si no hay errores críticos:**

```text
CRITICAL PASS > 0
CRITICAL FAIL = 0
```

> La regla `Q-VOLUME-ORDER-DETAIL` es únicamente demostrativa; según el volumen cargado puede quedar `PASS` o `FAIL`.

## 5.5 Criterio operativo para continuar a Silver

Esta sección define cómo debe comportarse el flujo después de ejecutar las validaciones Bronze → Silver.

Consulta de resumen del gate:

```sql
SELECT
  process_date,
  step_name,
  source_pattern,
  failed_critical_rules,
  failed_warning_rules,
  created_at
FROM databricksdemos.ops.validation_summary
WHERE process_date = DATE('2025-01-15')
ORDER BY created_at DESC;
```

Criterio de decisión:

```text
failed_critical_rules = 0
  → continuar con build_silver_gold

failed_critical_rules > 0
  → detener el flujo antes de Silver/Gold
  → revisar databricksdemos.ops.quality_results
```

En el Job multitarea, este criterio se implementa haciendo que el notebook `validate_bronze_to_silver` falle si hay reglas críticas en estado `FAIL`.

```text
ingest_saleslt
  → validate_bronze_to_silver
    → build_silver_gold
```

Si `validate_bronze_to_silver` falla, Databricks Jobs detiene las tareas dependientes.

## 5.6 Consulta de diagnóstico

Usar esta consulta para analizar fallos concretos:

```sql
SELECT
  process_date,
  source_pattern,
  table_name,
  rule_id,
  rule_description,
  metric_value,
  threshold_value,
  status,
  severity,
  created_at
FROM databricksdemos.ops.quality_results
WHERE status = 'FAIL'
  AND process_date = DATE('2025-01-15')
ORDER BY severity, created_at DESC, rule_id;
```

Interpretación:

```text
CRITICAL FAIL
  → regla bloqueante
  → no debe continuar a Silver/Gold

WARNING FAIL
  → regla informativa
  → revisar, pero no bloquea por defecto
```

## 5.7 Comprobaciones individuales

Revisar estos puntos antes de continuar:

- [ ] Existe `databricksdemos.ops.quality_results`
- [ ] Existe `databricksdemos.ops.validation_summary`
- [ ] Las reglas tienen `process_date` correcto
- [ ] Las reglas tienen `source_pattern = saleslt`
- [ ] Las reglas tienen `severity = CRITICAL` o `WARNING`
- [ ] `failed_critical_rules = 0`
- [ ] El notebook falla si `fail_on_error=True` y hay reglas críticas en `FAIL`

---

# 6. Build Silver/Gold como tarea operativa

## 6.1 Objetivo

El objetivo de esta sección es convertir una transformación Silver/Gold conocida en una tarea operativa dentro del DAG de Databricks.

La tarea `build_silver_gold` solo debe ejecutarse si `validate_bronze_to_silver` finaliza correctamente.

```text
ingest_saleslt
  → validate_bronze_to_silver
    → build_silver_gold
```

El foco de esta sección está en:

- Ejecutar Silver/Gold como tarea independiente del Job
- Mantener salidas gobernadas en Unity Catalog
- Publicar vistas Gold estables para consumo
- Registrar métricas operativas de salida
- Preparar el paso para operación, observabilidad y despliegue como código

## 6.2 Resultado esperado

La tarea `build_silver_gold` debe producir:

```text
Silver
  databricksdemos.silver.customers
  databricksdemos.silver.products
  databricksdemos.silver.orders

Gold
  databricksdemos.gold.sales_daily
  databricksdemos.gold.customer_activity

Vistas Gold
  databricksdemos.gold.v_powerbi_sales_daily
  databricksdemos.gold.v_powerbi_customer_activity

Observabilidad
  databricksdemos.ops.pipeline_run_summary
```

## 6.3 Puntos de diseño a revisar

Antes de ejecutar el código, revisar el patrón operativo que representa esta tarea:

- La tarea lee desde Bronze validado.
- La normalización se concentra en Silver.
- Los agregados de consumo se publican en Gold.
- Power BI debería consumir vistas o tablas Gold, no tablas Bronze.
- La tarea escribe métricas de salida en `ops.pipeline_run_summary`.
- La tarea queda preparada para ejecutarse dentro de Databricks Jobs.

Aspectos a considerar para producción:

- La escritura `overwrite` es válida para el laboratorio.
- En producción podría sustituirse por `MERGE`, cargas incrementales o particionado.
- Los nombres de catálogo y entorno deberían llegar como parámetros del Job.
- Las vistas Gold funcionan como contrato estable de consumo.
- Las métricas en `ops` permiten diagnóstico sin revisar logs manualmente.

## 6.4 Código de la tarea build_silver_gold

Crear un notebook Python con este nombre:

```text
build_silver_gold
```

Código:

```python
from pyspark.sql import functions as F

# ---------------------------------------------------------------------
# 1. Configuración básica
# ---------------------------------------------------------------------
# Este bloque permite ejecutar el notebook tanto dentro de un Databricks Job
# como manualmente durante el laboratorio. Si dbutils no está disponible,
# se usan valores por defecto para no romper pruebas manuales.
try:
    dbutils.widgets.text("env", "dev")
    dbutils.widgets.text("catalog", "databricksdemos")
    dbutils.widgets.text("process_date", "2025-01-15")
    dbutils.widgets.text("source_system", "saleslt")

    env = dbutils.widgets.get("env")
    catalog = dbutils.widgets.get("catalog")
    process_date = dbutils.widgets.get("process_date")
    source_pattern = dbutils.widgets.get("source_system")
except Exception:
    catalog = "databricksdemos"
    env = "dev"
    process_date = "2025-01-15"
    source_pattern = "saleslt"

spark.sql(f"CREATE SCHEMA IF NOT EXISTS {catalog}.silver")
spark.sql(f"CREATE SCHEMA IF NOT EXISTS {catalog}.gold")
spark.sql(f"CREATE SCHEMA IF NOT EXISTS {catalog}.ops")
spark.sql(f"USE CATALOG {catalog}")

# ---------------------------------------------------------------------
# 2. Tablas Bronze de entrada
# ---------------------------------------------------------------------
customer_raw = f"{catalog}.bronze.saleslt_customer_raw"
product_raw = f"{catalog}.bronze.saleslt_product_raw"
header_raw = f"{catalog}.bronze.saleslt_sales_order_header_raw"
detail_raw = f"{catalog}.bronze.saleslt_sales_order_detail_raw"

required_tables = [
    customer_raw,
    product_raw,
    header_raw,
    detail_raw
]

missing_tables = []
for table_name in required_tables:
    if not spark.catalog.tableExists(table_name):
        missing_tables.append(table_name)

if len(missing_tables) > 0:
    raise Exception(
        "Missing expected Bronze tables: " + ", ".join(missing_tables)
    )

# ---------------------------------------------------------------------
# 3. Silver customers
# ---------------------------------------------------------------------
customers_silver = (
    spark.table(customer_raw)
    .select(
        F.col("CustomerID").cast("string").alias("customer_id"),
        F.concat_ws(" ", F.col("FirstName"), F.col("LastName")).alias("customer_name"),
        F.lit("N/A").alias("country"),
        F.lit("saleslt").alias("segment"),
        F.to_date("ModifiedDate").alias("created_at"),
        F.lit("azure_sql_saleslt").alias("source_system"),
        F.lit(env).alias("env"),
        F.to_date(F.lit(process_date)).alias("process_date"),
        F.current_timestamp().alias("ingestion_ts")
    )
    .dropDuplicates(["customer_id"])
)

# ---------------------------------------------------------------------
# 4. Silver products
# ---------------------------------------------------------------------
products_silver = (
    spark.table(product_raw)
    .select(
        F.col("ProductID").cast("string").alias("product_id"),
        F.col("Name").cast("string").alias("product_name"),
        F.coalesce(
            F.col("ProductCategoryID").cast("string"),
            F.lit("unknown")
        ).alias("category"),
        F.lit("azure_sql_saleslt").alias("source_system"),
        F.lit(env).alias("env"),
        F.to_date(F.lit(process_date)).alias("process_date"),
        F.current_timestamp().alias("ingestion_ts")
    )
    .dropDuplicates(["product_id"])
)

# ---------------------------------------------------------------------
# 5. Silver orders
# ---------------------------------------------------------------------
orders_silver = (
    spark.table(header_raw).alias("h")
    .join(
        spark.table(detail_raw).alias("d"),
        F.col("h.SalesOrderID") == F.col("d.SalesOrderID"),
        "inner"
    )
    .select(
        F.concat(
            F.col("h.SalesOrderID").cast("string"),
            F.lit("-"),
            F.col("d.SalesOrderDetailID").cast("string")
        ).alias("order_id"),
        F.col("h.CustomerID").cast("string").alias("customer_id"),
        F.col("d.ProductID").cast("string").alias("product_id"),
        F.col("d.OrderQty").cast("int").alias("quantity"),
        F.col("d.LineTotal").cast("double").alias("net_amount"),
        F.to_date("h.OrderDate").alias("order_date"),
        F.lit("azure_sql_saleslt").alias("source_system"),
        F.lit(env).alias("env"),
        F.to_date(F.lit(process_date)).alias("process_date"),
        F.current_timestamp().alias("ingestion_ts")
    )
    .dropDuplicates(["order_id"])
)

# ---------------------------------------------------------------------
# 6. Escritura Silver
# ---------------------------------------------------------------------
customers_silver.write.format("delta").mode("overwrite").option("overwriteSchema", "true").saveAsTable(
    f"{catalog}.silver.customers"
)

products_silver.write.format("delta").mode("overwrite").option("overwriteSchema", "true").saveAsTable(
    f"{catalog}.silver.products"
)

orders_silver.write.format("delta").mode("overwrite").option("overwriteSchema", "true").saveAsTable(
    f"{catalog}.silver.orders"
)

# ---------------------------------------------------------------------
# 7. Gold sales_daily
# ---------------------------------------------------------------------
sales_daily = (
    orders_silver.alias("o")
    .join(
        customers_silver.alias("c"),
        F.col("o.customer_id") == F.col("c.customer_id"),
        "left"
    )
    .join(
        products_silver.alias("p"),
        F.col("o.product_id") == F.col("p.product_id"),
        "left"
    )
    .groupBy(
        F.col("o.order_date").alias("order_date"),
        F.col("c.country").alias("country"),
        F.col("c.segment").alias("segment"),
        F.col("p.category").alias("category")
    )
    .agg(
        F.countDistinct("o.order_id").alias("orders_count"),
        F.countDistinct("o.customer_id").alias("customers_count"),
        F.sum("o.quantity").alias("units_sold"),
        F.round(F.sum("o.net_amount"), 2).alias("net_sales")
    )
    .withColumn("process_date", F.to_date(F.lit(process_date)))
    .withColumn("env", F.lit(env))
    .withColumn("gold_created_at", F.current_timestamp())
)

# ---------------------------------------------------------------------
# 8. Gold customer_activity
# ---------------------------------------------------------------------
customer_activity = (
    orders_silver
    .groupBy("customer_id")
    .agg(
        F.countDistinct("order_id").alias("orders_count"),
        F.round(F.sum("net_amount"), 2).alias("customer_sales")
    )
    .join(customers_silver, "customer_id", "left")
    .select(
        "customer_id",
        "customer_name",
        "country",
        "segment",
        "orders_count",
        "customer_sales"
    )
    .withColumn("process_date", F.to_date(F.lit(process_date)))
    .withColumn("env", F.lit(env))
    .withColumn("gold_created_at", F.current_timestamp())
)

# ---------------------------------------------------------------------
# 9. Escritura Gold
# ---------------------------------------------------------------------
sales_daily.write.format("delta").mode("overwrite").option("overwriteSchema", "true").saveAsTable(
    f"{catalog}.gold.sales_daily"
)

customer_activity.write.format("delta").mode("overwrite").option("overwriteSchema", "true").saveAsTable(
    f"{catalog}.gold.customer_activity"
)

# ---------------------------------------------------------------------
# 10. Vistas Gold para consumo
# ---------------------------------------------------------------------
spark.sql(f"""
CREATE OR REPLACE VIEW {catalog}.gold.v_powerbi_sales_daily AS
SELECT
  order_date,
  country,
  segment,
  category,
  orders_count,
  customers_count,
  units_sold,
  net_sales
FROM {catalog}.gold.sales_daily
""")

spark.sql(f"""
CREATE OR REPLACE VIEW {catalog}.gold.v_powerbi_customer_activity AS
SELECT
  customer_id,
  customer_name,
  country,
  segment,
  orders_count,
  customer_sales
FROM {catalog}.gold.customer_activity
""")

# ---------------------------------------------------------------------
# 11. Registro operativo de salidas
# ---------------------------------------------------------------------
summary_rows = [
    ("silver.customers", customers_silver.count(), "silver"),
    ("silver.products", products_silver.count(), "silver"),
    ("silver.orders", orders_silver.count(), "silver"),
    ("gold.sales_daily", sales_daily.count(), "gold"),
    ("gold.customer_activity", customer_activity.count(), "gold")
]

summary_df = (
    spark.createDataFrame(summary_rows, ["table_name", "row_count", "layer"])
    .withColumn("env", F.lit(env))
    .withColumn("source_pattern", F.lit(source_pattern))
    .withColumn("process_date", F.to_date(F.lit(process_date)))
    .withColumn("created_at", F.current_timestamp())
)

summary_table = f"{catalog}.ops.pipeline_run_summary"
spark.sql(f"""
CREATE TABLE IF NOT EXISTS {summary_table} (
    table_name STRING,
    row_count BIGINT,
    layer STRING,
    env STRING,
    source_pattern STRING,
    process_date DATE,
    created_at TIMESTAMP
)
USING DELTA
""")

summary_df = summary_df.select(
    "table_name",
    "row_count",
    "layer",
    "env",
    "source_pattern",
    "process_date",
    "created_at"
)

summary_df.write.format("delta").mode("append").option("mergeSchema", "true").saveAsTable(
    summary_table
)

display(summary_df)
```

## 6.5 Validar salidas

Ejecutar desde SQL Editor:

```sql
SELECT COUNT(*) AS customers_count
FROM databricksdemos.silver.customers;

SELECT COUNT(*) AS products_count
FROM databricksdemos.silver.products;

SELECT COUNT(*) AS orders_count
FROM databricksdemos.silver.orders;

SELECT *
FROM databricksdemos.gold.v_powerbi_sales_daily;

SELECT *
FROM databricksdemos.gold.v_powerbi_customer_activity;
```

## 6.6 Validar métricas operativas

```sql
SELECT
  layer,
  table_name,
  process_date,
  source_pattern,
  SUM(row_count) AS rows_written,
  MAX(created_at) AS last_update
FROM databricksdemos.ops.pipeline_run_summary
WHERE process_date = DATE('2025-01-15')
GROUP BY layer, table_name, process_date, source_pattern
ORDER BY layer, table_name;
```

**Resultado esperado:** Aparecen conteos para las tablas Silver y Gold generadas.

## 6.7 Checklist de revisión técnica

Antes de considerar esta tarea preparada para producción, revisar:

- [ ] La tarea no depende de variables hardcodeadas de entorno
- [ ] La tarea solo se ejecuta después de `validate_bronze_to_silver`
- [ ] Las salidas Silver/Gold son idempotentes o tienen estrategia incremental definida
- [ ] Las vistas Gold son estables para consumo
- [ ] Los conteos de salida quedan registrados en `ops.pipeline_run_summary`
- [ ] El Job tiene timeout, retries y alertas definidos
- [ ] El despliegue de la tarea está versionado en Git

## 6.8 Lectura operativa

Esta sección no busca enseñar a construir Silver/Gold desde cero.

El valor de la sección está en el patrón operativo:

```text
Transformación conocida
  → tarea independiente en Databricks Jobs
  → salida gobernada
  → vistas estables
  → métricas de ejecución
  → preparada para DevOps/Terraform
```

---

# 7. Diseñar el Databricks Job que ADF invocará

## 7.1 Objetivo

Convertir el flujo del laboratorio en un DAG operativo dentro de Databricks.

El objetivo no es ejecutar notebooks de forma aislada, sino definir una unidad de orquestación interna que pueda ser invocada desde ADF.

DAG principal:

```text
ingest_saleslt
  → validate_bronze_to_silver
    → build_silver_gold
```

Cambio arquitectónico principal:

```text
ADF deja de controlar cada paso interno del ETL.
ADF invoca un único Databricks Job.
Databricks gestiona dependencias internas, validaciones, retries y observabilidad.
```

## 7.2 Responsabilidad de cada tarea

```text
ingest_saleslt
  Ejecuta la ingesta desde Azure SQL SalesLT hacia Bronze.
  En el laboratorio corresponde al pipeline creado con Add data → Ingest data from SQL Server.

validate_bronze_to_silver
  Ejecuta las reglas críticas de calidad.
  Si hay reglas CRITICAL en FAIL, la tarea falla.
  Si la tarea falla, build_silver_gold no se ejecuta.

build_silver_gold
  Construye Silver, Gold y vistas de consumo.
  Registra métricas en databricksdemos.ops.pipeline_run_summary.
```

## 7.3 Crear y configurar el Job desde la UI

Ir al menú lateral de Databricks:

```text
Jobs & Pipelines
```

Pulsar **Create job**.

Nombre del Job:

```text
dbrx-lakehouse-ops-saleslt-dev
```

Crear las tareas:

```text
ingest_saleslt
  Tipo: Pipeline / Ingestion pipeline
  Pipeline: lakehouse_ops_saleslt_ingest

validate_bronze_to_silver
  Tipo: notebook
  Compute: Serverless
  Notebook: validate_bronze_to_silver

build_silver_gold
  Tipo: notebook
  Compute: Serverless
  Notebook: build_silver_gold
```

Verifica el DAG principal:

```text
ingest_saleslt → validate_bronze_to_silver → build_silver_gold
```

## 7.4 Parámetros del Job

Configurar parámetros a nivel de Job:

```text
env=dev
catalog=databricksdemos
process_date=2025-01-15
source_system=saleslt
```

Estos parámetros forman el contrato de ejecución entre ADF y Databricks.

ADF no necesita conocer la estructura interna del DAG. Solo necesita invocar el Job con los parámetros adecuados.

## 7.5 Uso de parámetros en notebooks

Los notebooks deben leer los parámetros del Job en lugar de depender exclusivamente de valores fijos.

Ejemplo recomendado:

```python
try:
    dbutils.widgets.text("env", "dev")
    dbutils.widgets.text("catalog", "databricksdemos")
    dbutils.widgets.text("process_date", "2025-01-15")
    dbutils.widgets.text("source_system", "saleslt")

    env = dbutils.widgets.get("env")
    catalog = dbutils.widgets.get("catalog")
    process_date = dbutils.widgets.get("process_date")
    source_system = dbutils.widgets.get("source_system")
except Exception:
    env = "dev"
    catalog = "databricksdemos"
    process_date = "2025-01-15"
    source_system = "saleslt"
```

> En esta versión, los notebooks están parametrizados con fallback a valores por defecto para no romper las validaciones y pruebas manuales del laboratorio.

## 7.6 Retries y comportamiento ante fallo

Configuración recomendada:

```text
ingest_saleslt
  retries: 1
  motivo: puede fallar por conectividad transitoria

validate_bronze_to_silver
  retries: 0
  motivo: si falla por calidad de dato, reintentar no suele corregir el dato

build_silver_gold
  retries: 1
  motivo: puede fallar por errores transitorios de ejecución
```

Comportamiento esperado:

```text
Si ingest_saleslt falla:
  validate_bronze_to_silver no se ejecuta
  el Job falla

Si validate_bronze_to_silver falla:
  build_silver_gold no se ejecuta
  el Job falla

Si build_silver_gold falla:
  el Job falla
```

## 7.7 Alertas del Job

Configurar la salud del job en **Metric thresholds > Duration Warning**:

```text
Metric: Run duration
Warning threshold: 30m
```

Configurar una notificación del job:

```text
On failure, duration warning: <tu_email>
```

Para un entorno de producción, el destinatario debería ser un grupo operativo, no una persona individual.

La validación funcional no necesita una alerta independiente en este laboratorio. Si `validate_bronze_to_silver` falla, el Job falla y las alertas técnicas existentes del Job pueden notificar el incidente.

## 7.8 Cómo lo invocaría ADF

En la arquitectura objetivo:

```text
ADF no ejecuta cada notebook individual.
ADF invoca un único Databricks Job.
Databricks gestiona dependencias internas, retries, validaciones y observabilidad.
```

Parámetros que ADF enviaría al Job:

```json
{
  "env": "dev",
  "catalog": "databricksdemos",
  "process_date": "2025-01-15",
  "source_system": "saleslt"
}
```

Resultado esperado:

```text
ADF recibe un único estado del Job:
success o failure.

El detalle operativo vive en Databricks:
runs, task logs, quality_results, validation_summary y pipeline_run_summary.
```

## 7.9 Criterio de diseño

Esta sección representa el cambio arquitectónico principal del laboratorio.

Antes:

```text
ADF Pipeline
 ├── Ingesta
 ├── Validación
 ├── Silver
 └── Gold
```

Después:

```text
ADF Pipeline
  └── Ejecuta Databricks Job
        ├── ingest_saleslt
        ├── validate_bronze_to_silver
        └── build_silver_gold
```

Criterio recomendado:

```text
ADF mantiene triggers, dependencias externas y coordinación cross-platform.
Databricks Jobs gestiona el DAG interno del ETL.
```

## 7.10 Preparación para DevOps y Terraform

Este Job no debería quedar como configuración manual en producción.

Los siguientes elementos deberían versionarse y desplegarse como código:

- Nombre del Job
- Tareas
- Dependencias
- Parámetros
- Retries
- Timeouts
- Alertas
- Permisos

Esta sección prepara la transición hacia el siguiente bloque del laboratorio: buenas prácticas de despliegue con Azure DevOps y Terraform.

## 7.11 Comprobaciones individuales

Antes de continuar, validar:

- [ ] Existe el Job `dbrx-lakehouse-ops-saleslt-dev`
- [ ] El DAG principal está definido con dependencias explícitas
- [ ] `validate_bronze_to_silver` depende de `ingest_saleslt`
- [ ] `build_silver_gold` depende de `validate_bronze_to_silver`
- [ ] `validate_bronze_to_silver` tiene retries = 0
- [ ] `build_silver_gold` tiene retries = 1
- [ ] El Job tiene parámetros `env`, `catalog`, `process_date` y `source_system`
- [ ] El Job tiene alerta de fallo configurada
- [ ] El Job puede ser invocado como unidad única desde ADF

---

# 8. Buenas prácticas de despliegue con Azure DevOps y Terraform

## 8.1 Objetivo

Revisar cómo llevar el patrón del laboratorio a un flujo de despliegue robusto, mantenible y repetible usando prácticas que el equipo ya aplica con Azure DevOps y Terraform.

Esta sección no busca enseñar Azure DevOps ni Terraform desde cero. El foco está en **buenas prácticas específicas para Databricks**:

- Versionar artefactos Databricks de forma consistente
- Separar configuración por entorno
- Evitar cambios manuales en producción
- Promocionar de dev → test → prod con control
- Validar Jobs, notebooks y Terraform antes del despliegue
- Gestionar permisos, alertas y parámetros como parte del ciclo de vida

No se ejecutará `terraform apply` en vivo durante el laboratorio. La actividad consiste en revisar el patrón y contrastarlo con la forma actual de trabajo del equipo.

## 8.2 Qué debería quedar versionado

En un escenario real, este flujo debería estar representado en Git con artefactos claros y revisables.

Estructura recomendada:

```text
databricks-lakehouse-ops/
├── notebooks/
│   ├── 01_validate_bronze.py
│   └── 02_build_silver_gold.py
├── jobs/
│   └── lakehouse_ops_saleslt_job.json
├── infra/
│   ├── main.tf
│   ├── variables.tf
│   ├── outputs.tf
│   └── env/
│       ├── dev.tfvars
│       ├── test.tfvars
│       └── prod.tfvars
├── pipelines/
│   └── azure-pipelines.yml
└── docs/
    └── runbook_lakehouse_ops.md
```

Buenas prácticas:

- Mantener notebooks, Jobs y configuración en repositorios claros.
- Separar definición lógica del Job de los valores concretos por entorno.
- Evitar IDs hardcodeados cuando puedan resolverse por variables u outputs.
- Documentar cómo se opera, reintenta y monitoriza el flujo.

## 8.3 Configuración por entorno

Los valores que cambian entre entornos no deberían vivir dentro del código del notebook.

Variables típicas por entorno:

```text
env
catalog
source_system
job_name
warehouse_id
notification_emails
permissions_group
connection_name
sql_database_name
```

Ejemplo conceptual:

```text
dev
  catalog=databricksdemos_dev
  job_name=dbrx-lakehouse-ops-saleslt-dev
  notification_emails=data-dev@example.com

test
  catalog=databricksdemos_test
  job_name=dbrx-lakehouse-ops-saleslt-test
  notification_emails=data-test@example.com

prod
  catalog=databricksdemos_prod
  job_name=dbrx-lakehouse-ops-saleslt-prod
  notification_emails=data-ops@example.com
```

Buenas prácticas:

- Los notebooks reciben parámetros; no contienen nombres de entorno fijos.
- Los Jobs definen parámetros estándar: `env`, `catalog`, `process_date`, `source_system`.
- Terraform o Azure DevOps inyectan valores por entorno.
- Las credenciales no se almacenan en Git.

## 8.4 Qué debería gestionar Terraform

Terraform debería encargarse de los recursos y configuraciones que deben ser reproducibles.

Recursos candidatos:

- Databricks Jobs
- Parámetros del Job
- Dependencias entre tareas
- Timeouts y retries
- Notificaciones
- Permisos del Job
- Permisos sobre schemas o tablas si aplica
- SQL Warehouses si el modelo operativo lo permite
- Secret scopes si aplica
- Cluster policies o configuración de compute si aplica

Buenas prácticas:

- No crear Jobs manuales en producción.
- No cambiar retries, alertas o dependencias directamente en la UI de producción.
- Usar pull requests para revisar cambios de Job.
- Ejecutar `terraform plan` antes de aplicar.
- Separar state por entorno.
- Restringir quién puede aplicar en producción.

## 8.5 Qué debería gestionar Azure DevOps

Azure DevOps debería coordinar el ciclo de vida del despliegue.

Flujo recomendado:

```text
Pull request
  → lint / validaciones estáticas
  → validación de notebooks
  → validación JSON del Job
  → terraform fmt
  → terraform validate
  → terraform plan
  → aprobación
  → despliegue en dev/test/prod
```

Buenas prácticas:

- Usar ramas protegidas.
- Exigir pull request para cambios en Jobs o Terraform.
- Separar pipelines de validación y despliegue.
- Usar aprobaciones manuales para producción.
- Publicar el plan de Terraform como artefacto revisable.
- No guardar secretos en variables visibles del pipeline.
- Usar variable groups o integración con Key Vault donde aplique.

## 8.6 Checklist antes de desplegar un Job Databricks

Antes de promocionar un Job a otro entorno, revisar:

- [ ] El Job tiene nombre estándar por entorno
- [ ] Las tareas tienen nombres claros y estables
- [ ] Las dependencias del DAG son explícitas
- [ ] Los parámetros están definidos a nivel de Job
- [ ] No hay valores de entorno hardcodeados en notebooks
- [ ] `validate_bronze_to_silver` falla si hay reglas críticas `FAIL`
- [ ] Las tareas tienen retries adecuados
- [ ] Las tareas tienen timeout definido
- [ ] Las alertas de fallo están configuradas
- [ ] La observabilidad escribe en `ops.quality_results` y `ops.pipeline_run_summary`
- [ ] El owner del Job está definido
- [ ] Los permisos están alineados con el grupo correcto
- [ ] El plan de Terraform ha sido revisado

## 8.7 Buenas prácticas para notebooks

Los notebooks del flujo deberían tratarse como artefactos de ingeniería, no como scripts manuales aislados.

Recomendaciones:

- Parametrizar `env`, `catalog`, `process_date` y `source_system`.
- Evitar rutas o nombres de catálogo hardcodeados.
- Separar validación y transformación.
- Escribir salidas en tablas gobernadas.
- Registrar métricas operativas en `ops`.
- Hacer que los errores críticos fallen explícitamente el notebook.
- Mantener notebooks idempotentes cuando sea posible.
- Evitar lógica oculta en notebooks master monolíticos.

## 8.8 Buenas prácticas para Jobs

El Job debe representar el DAG operativo, no esconder todo dentro de un notebook maestro.

DAG recomendado:

```text
ingest_saleslt
  → validate_bronze_to_silver
    → build_silver_gold
```

Buenas prácticas:

- Una tarea por responsabilidad.
- Retries solo donde tenga sentido.
- Validaciones sin retry si el fallo depende del dato.
- Timeouts explícitos.
- Alertas por fallo y por duración.
- Parámetros comunes a nivel de Job.
- Logs y resultados consultables en Databricks.
- ADF invoca el Job, no cada notebook individual.

## 8.9 Cómo encaja ADF en el despliegue

ADF no desaparece del patrón. Su rol se simplifica.

ADF debería encargarse de:

- Triggers corporativos
- Dependencias externas
- Coordinación cross-platform
- Invocación del Databricks Job principal

Databricks debería encargarse de:

- Ingesta gestionada cuando aplique, por ejemplo mediante Lakeflow Connect
- Dependencias internas del ETL
- Validación de calidad
- Retries por tarea
- Observabilidad
- Linaje y gobierno
- Ejecución Silver/Gold

Parámetros que ADF enviaría al Job:

```json
{
  "env": "dev",
  "catalog": "databricksdemos",
  "process_date": "2025-01-15",
  "source_system": "saleslt"
}
```

Buenas prácticas:

- ADF no debe duplicar la lógica del DAG interno.
- ADF no debería decidir si se ejecuta Silver o Gold si esa dependencia ya está en Databricks.
- ADF debe recibir estado claro del Job: `success`, `failure`, `duration`, `run_id`.
- ADF puede seguir siendo el punto de coordinación con sistemas externos.

## 8.10 Actividad breve de revisión

Preguntas para discusión:

- ¿Qué parte del flujo actual del cliente debería quedarse en ADF?
- ¿Qué parte debería moverse a Databricks Jobs?
- ¿Qué variables deberían estar en Terraform o Azure DevOps?
- ¿Qué validaciones deberían bloquear el paso a Silver/Gold?
- ¿Qué alertas necesita operaciones y cuáles necesita negocio?

<details>
<summary>Respuesta</summary>

Un patrón consensuado para evolucionar un job master actual hacia un Databricks Job gobernado, observable y desplegable con DevOps/Terraform.

</details>

---

# 9. Validación final y cierre arquitectónico

## 9.1 Observabilidad

Desde SQL Editor:

```sql
SELECT
  status,
  COUNT(*) AS rules_count
FROM databricksdemos.ops.quality_results
GROUP BY status;

SELECT
  layer,
  table_name,
  process_date,
  SUM(row_count) AS rows_written,
  MAX(created_at) AS last_update
FROM databricksdemos.ops.pipeline_run_summary
GROUP BY layer, table_name, process_date
ORDER BY layer, table_name;
```

## 9.2 Consulta de alerta funcional

```sql
SELECT
  COUNT(*) AS failed_rules
FROM databricksdemos.ops.quality_results
WHERE status = 'FAIL'
  AND severity = 'CRITICAL'
  AND process_date = DATE('2025-01-15');
```

Condición sugerida:

```text
failed_rules > 0
```

## 9.3 Antes y después

Antes:

```text
ADF Pipeline
 ├── Ingesta
 ├── Validación
 ├── Silver
 └── Gold
```

Después:

```text
ADF Pipeline
  └── Ejecuta Databricks Job
        ├── ingest_saleslt
        ├── validate_bronze_to_silver
        └── build_silver_gold
```

## 9.4 Mensaje final

La fuente puede cambiar.

Puede ser Azure SQL, Storage, SAP, APIs o datos demo.

El patrón operativo permanece:

- Ingesta gobernada
- Validación automatizada
- Transformación Silver/Gold
- Observabilidad en `ops`
- Orquestación interna en Databricks Jobs
- ADF como disparador externo
- DevOps y Terraform como mecanismo de despliegue repetible

🎉 ¡Enhorabuena! Has completado el laboratorio con éxito.

A lo largo de este recorrido has puesto en práctica conceptos clave de una plataforma moderna de datos en Databricks:

✅ Ingesta de datos en capa Bronze  
✅ Transformaciones hacia Silver y Gold  
✅ Orquestación mediante Jobs y DAGs  
✅ Gobierno y seguridad con Unity Catalog  
✅ Observabilidad, calidad de datos y trazabilidad  
✅ Automatización y preparación para despliegues mediante DevOps/Terraform

Más importante aún, has seguido un enfoque alineado con las buenas prácticas de ingeniería de datos modernas: pipelines gobernados, reproducibles, escalables y preparados para entornos productivos.

El siguiente paso es aplicar este patrón sobre tus propios casos de uso, incorporando fuentes reales, reglas de negocio y ciclos de despliegue automatizados.

**¡Buen trabajo y gracias por participar en el laboratorio!**

---

# 10. Plan B — Datos demo dentro de Databricks

Usar esta sección solo si la conexión a Azure SQL no funciona durante la sesión.

## 10.1 Objetivo

Generar datos equivalentes dentro de Databricks para poder continuar el laboratorio sin depender de la conexión externa.

El Plan B genera exactamente los mismos nombres de tabla esperados por las validaciones y transformaciones principales:

```text
databricksdemos.bronze.saleslt_customer_raw
databricksdemos.bronze.saleslt_product_raw
databricksdemos.bronze.saleslt_sales_order_header_raw
databricksdemos.bronze.saleslt_sales_order_detail_raw
```

Esto permite continuar ejecutando sin cambios:

```text
validate_bronze_to_silver
build_silver_gold
```

## 10.2 Código de fallback

Ejecutar en notebook Python con Serverless compute:

```python
from pyspark.sql import functions as F
from pyspark.sql import types as T

# ---------------------------------------------------------------------
# 1. Configuración básica
# ---------------------------------------------------------------------
try:
    dbutils.widgets.text("env", "dev")
    dbutils.widgets.text("catalog", "databricksdemos")
    dbutils.widgets.text("process_date", "2025-01-15")
    dbutils.widgets.text("source_system", "saleslt")

    env = dbutils.widgets.get("env")
    catalog = dbutils.widgets.get("catalog")
    process_date = dbutils.widgets.get("process_date")
    source_system = dbutils.widgets.get("source_system")
except Exception:
    catalog = "databricksdemos"
    env = "dev"
    process_date = "2025-01-15"
    source_system = "saleslt"

spark.sql(f"CREATE SCHEMA IF NOT EXISTS {catalog}.bronze")
spark.sql(f"CREATE SCHEMA IF NOT EXISTS {catalog}.ops")
spark.sql(f"USE CATALOG {catalog}")

# ---------------------------------------------------------------------
# 2. Customer compatible con SalesLT.Customer
# ---------------------------------------------------------------------
customer_rows = [
    ("C001", "Ana", "García", "2025-01-15T10:00:00Z"),
    ("C002", "João", "Silva", "2025-01-15T10:10:00Z"),
    ("C003", "Marie", "Dubois", "2025-01-15T10:20:00Z"),
    ("C004", "Luca", "Rossi", "2025-01-15T10:30:00Z")
]

customer_schema = T.StructType([
    T.StructField("CustomerID", T.StringType(), False),
    T.StructField("FirstName", T.StringType(), False),
    T.StructField("LastName", T.StringType(), False),
    T.StructField("ModifiedDate", T.StringType(), False)
])

customer_df = spark.createDataFrame(customer_rows, customer_schema)

# ---------------------------------------------------------------------
# 3. Product compatible con SalesLT.Product
# ---------------------------------------------------------------------
product_rows = [
    ("P001", "Sensor IoT", "hardware", "2025-01-15T10:00:00Z"),
    ("P002", "Gateway Edge", "hardware", "2025-01-15T10:05:00Z"),
    ("P003", "Analytics Pack", "software", "2025-01-15T10:10:00Z")
]

product_schema = T.StructType([
    T.StructField("ProductID", T.StringType(), False),
    T.StructField("Name", T.StringType(), False),
    T.StructField("ProductCategoryID", T.StringType(), True),
    T.StructField("ModifiedDate", T.StringType(), False)
])

product_df = spark.createDataFrame(product_rows, product_schema)

# ---------------------------------------------------------------------
# 4. SalesOrderHeader compatible con SalesLT.SalesOrderHeader
# ---------------------------------------------------------------------
header_rows = [
    ("SO001", "C001", "2025-01-15", "2025-01-15T11:00:00Z"),
    ("SO002", "C002", "2025-01-15", "2025-01-15T11:15:00Z"),
    ("SO003", "C003", "2025-01-16", "2025-01-16T09:00:00Z"),
    ("SO004", "C001", "2025-01-16", "2025-01-16T09:30:00Z")
]

header_schema = T.StructType([
    T.StructField("SalesOrderID", T.StringType(), False),
    T.StructField("CustomerID", T.StringType(), False),
    T.StructField("OrderDate", T.StringType(), False),
    T.StructField("ModifiedDate", T.StringType(), False)
])

header_df = spark.createDataFrame(header_rows, header_schema)

# ---------------------------------------------------------------------
# 5. SalesOrderDetail compatible con SalesLT.SalesOrderDetail
# ---------------------------------------------------------------------
detail_rows = [
    ("SO001", "D001", "P001", 2, 240.0, "2025-01-15T11:00:00Z"),
    ("SO001", "D002", "P003", 1, 75.0, "2025-01-15T11:01:00Z"),
    ("SO002", "D003", "P003", 1, 75.0, "2025-01-15T11:15:00Z"),
    ("SO003", "D004", "P002", 1, 310.0, "2025-01-16T09:00:00Z"),
    ("SO004", "D005", "P003", 3, 225.0, "2025-01-16T09:30:00Z")
]

detail_schema = T.StructType([
    T.StructField("SalesOrderID", T.StringType(), False),
    T.StructField("SalesOrderDetailID", T.StringType(), False),
    T.StructField("ProductID", T.StringType(), False),
    T.StructField("OrderQty", T.IntegerType(), False),
    T.StructField("LineTotal", T.DoubleType(), False),
    T.StructField("ModifiedDate", T.StringType(), False)
])

detail_df = spark.createDataFrame(detail_rows, detail_schema)

# ---------------------------------------------------------------------
# 6. Función auxiliar de metadatos de ingesta
# ---------------------------------------------------------------------
def add_ingestion_metadata(df, source_name):
    return (
        df
        .withColumn("_source", F.lit(source_name))
        .withColumn("_env", F.lit(env))
        .withColumn("_process_date", F.to_date(F.lit(process_date)))
        .withColumn("_ingestion_ts", F.current_timestamp())
    )

# ---------------------------------------------------------------------
# 7. Escritura de tablas Bronze compatibles con el flujo principal
# ---------------------------------------------------------------------
add_ingestion_metadata(customer_df, "demo_saleslt_customer") \
    .write.format("delta") \
    .mode("overwrite") \
    .option("overwriteSchema", "true") \
    .saveAsTable(f"{catalog}.bronze.saleslt_customer_raw")

add_ingestion_metadata(product_df, "demo_saleslt_product") \
    .write.format("delta") \
    .mode("overwrite") \
    .option("overwriteSchema", "true") \
    .saveAsTable(f"{catalog}.bronze.saleslt_product_raw")

add_ingestion_metadata(header_df, "demo_saleslt_sales_order_header") \
    .write.format("delta") \
    .mode("overwrite") \
    .option("overwriteSchema", "true") \
    .saveAsTable(f"{catalog}.bronze.saleslt_sales_order_header_raw")

add_ingestion_metadata(detail_df, "demo_saleslt_sales_order_detail") \
    .write.format("delta") \
    .mode("overwrite") \
    .option("overwriteSchema", "true") \
    .saveAsTable(f"{catalog}.bronze.saleslt_sales_order_detail_raw")

# ---------------------------------------------------------------------
# 8. Validación rápida
# ---------------------------------------------------------------------
display(spark.sql(f"SHOW TABLES IN {catalog}.bronze"))
```

## 10.3 Validar que el Plan B permite continuar

Después de ejecutar el Plan B, validar:

```sql
SHOW TABLES IN databricksdemos.bronze;
```

Debe mostrar:

```text
saleslt_customer_raw
saleslt_product_raw
saleslt_sales_order_header_raw
saleslt_sales_order_detail_raw
```

A partir de este punto, continuar con:

```text
5.3 Notebook — Gate operativo Bronze → Silver
6.4 Código de la tarea build_silver_gold
7. Diseñar el Databricks Job que ADF invocará
```

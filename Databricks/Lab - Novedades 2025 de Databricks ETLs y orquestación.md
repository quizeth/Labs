# Lakehouse Ops Lab: Modernización de ETLs con Databricks


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
```

Si la conexión a Azure SQL no funciona durante la sesión, se usará un plan B con datos demo generados dentro de Databricks.


## Agenda del laboratorio

**Duración estimada:** 60 minutos

1. Preparación del entorno
2. Ingesta desde Azure SQL SalesLT con conector Databricks
3. Validación de tablas Bronze
4. Validación y observabilidad
5. Modernización Silver/Gold
6. Crear Job multitarea
7. Validación final y cierre


---

# Variables del laboratorio

Valores usados durante el laboratorio:

```text
catalog=databricksdemos
bronze_schema=bronze
silver_schema=silver
gold_schema=gold
ops_schema=ops
process_date=2025-01-15
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

> Recomendación de seguridad: la contraseña de laboratorio debe compartirse por un canal seguro durante la sesión. No se recomienda dejar contraseñas reales escritas en material distribuible.

---

# 3. Preparación del entorno

## 3.1 SQL Warehouse y cluster

Para este laboratorio se usarán **dos tipos de compute** en Databricks:

```text
SQL Warehouse
  → Para validar tablas desde SQL Editor.

Cluster interactivo
  → Para ejecutar notebooks Python del laboratorio.
```

---

### 3.1.1 Crear SQL Warehouse

El SQL Warehouse se usará para ejecutar consultas SQL rápidas desde **SQL Editor**, por ejemplo:

```sql
SHOW TABLES IN databricksdemos.bronze;
```

Pasos:

1. Ir a:

```text
Compute
  → SQL warehouses
```

2. Pulsar:

```text
Create SQL warehouse
```

3. Configurar:

```text
Name: wh_lakehouse_ops_lab
Cluster size: 2X-Small
Min clusters: 1
Max clusters: 1
Auto stop: 10 minutes
Type: Serverless si está disponible
```

Si `Serverless` no está disponible, usar el tipo permitido por el workspace, por ejemplo:

```text
Type: Pro
```

4. Pulsar:

```text
Create
```

5. Esperar a que el estado sea:

```text
Running
```

---

### 3.1.2 Validar el SQL Warehouse desde SQL Editor

El SQL Warehouse se usará para ejecutar consultas desde **SQL Editor**.

La primera vez que abras una consulta SQL o pulses `Run`, Databricks puede pedirte que adjuntes la query a un recurso de cómputo existente.

---

#### Paso 1 — Abrir SQL Editor

En el menú lateral izquierdo, ir a:

```text
SQL Editor
```

Si aparece la pantalla inicial del SQL Editor, pulsar:

```text
SQL Query
```

Esto abrirá una nueva pestaña de consulta SQL.

---

#### Paso 2 — Escribir la consulta de validación

Pegar esta consulta en el editor:

```sql
SHOW SCHEMAS IN databricksdemos;
```

---

#### Paso 3 — Ejecutar la consulta

Pulsar:

```text
Run all
```

o:

```text
Run
```

---

#### Paso 4 — Adjuntar la query al SQL Warehouse

La primera vez puede aparecer una ventana con el título:

```text
Attach to an existing compute resource
```

En esa ventana:

1. En **Compute type**, dejar seleccionada la opción:

```text
SQL Warehouse
```

2. En el desplegable de warehouses, seleccionar:

```text
wh_lakehouse_ops_lab
```

3. Comprobar que el resumen muestra algo similar a:

```text
Size: 2X-Small
Type: Serverless
```

4. Pulsar:

```text
Attach and run
```

Esto adjunta la query al SQL Warehouse y ejecuta la consulta.

---

#### Paso 5 — Revisar el resultado

Resultado esperado si los schemas ya existen:

```text
default
```

En el siguiente paso, se crearán los schemas `bronze`, `ops`, `silver` o `gold` desde un notebook Python conectado al cluster.

---

#### Alternativa sin SQL Warehouse

Si se prefiere evitar el SQL Warehouse, las mismas validaciones se pueden hacer desde un notebook Python conectado al cluster interactivo.

Ejemplo:

```python
display(spark.sql("SHOW SCHEMAS IN databricksdemos"))
```

Y más adelante:

```python
display(spark.sql("SHOW TABLES IN databricksdemos.bronze"))
```

---

### 3.1.3 Compute para notebooks y validaciones

Para este laboratorio usaremos dos opciones serverless de Databricks:

```text
SQL Warehouse Serverless
  → Para SQL Editor y validaciones SQL.

Serverless compute for notebooks
  → Para notebooks Python / PySpark.
```

---

#### Compute 1 — SQL Warehouse

El SQL Warehouse creado para el laboratorio es:

```text
wh_lakehouse_ops_lab
```

Se usará para:

```text
Ejecutar consultas desde SQL Editor
Crear schemas con SQL
Validar tablas Bronze
Consultar conteos
Revisar tablas Silver y Gold
Ejecutar consultas de observabilidad
```

Ejemplos de consultas desde SQL Editor:

```sql
SHOW TABLES IN databricksdemos.bronze;
```

```sql
SELECT COUNT(*) AS customers_count
FROM databricksdemos.bronze.saleslt_customer_raw;
```

---

#### Compute 2 — Serverless compute para notebooks

Para ejecutar notebooks Python o PySpark, usar **Serverless compute for notebooks**.

Al crear o abrir un notebook Python:

1. Ir a:

```text
Workspace
  → Create
  → Notebook
```

2. Elegir lenguaje:

```text
Python
```

3. En el selector de compute del notebook, seleccionar:

```text
Serverless
```

4. Ejecutar una celda de validación:

```python
spark.version
```

Resultado esperado:

```text
La celda devuelve la versión de Spark asociada al compute serverless.
```

---

#### Cuándo usar cada compute

Usar **SQL Warehouse** para validaciones SQL rápidas:

```sql
SHOW TABLES IN databricksdemos.bronze;
```

```sql
SELECT *
FROM databricksdemos.bronze.saleslt_customer_raw
LIMIT 10;
```

Usar **Serverless compute for notebooks** para ejecutar notebooks Python / PySpark:

```python
display(spark.sql("SHOW TABLES IN databricksdemos.bronze"))
```

```python
display(
    spark.sql("""
        SELECT *
        FROM databricksdemos.bronze.saleslt_customer_raw
        LIMIT 10
    """)
)
```

---

#### Flujo de ejecución del laboratorio

La ejecución principal queda organizada así:

```text
Ingesta visual desde Azure SQL SalesLT
  → Add data → Ingest data from SQL Server

Validaciones rápidas
  → SQL Editor + wh_lakehouse_ops_lab

Notebooks de validación, Silver/Gold y observabilidad
  → Serverless compute for notebooks
```

---

## 3.2 Crear schemas necesarios

Abrir un notebook Python conectado al cluster y ejecutar:

```python
spark.sql("CREATE SCHEMA IF NOT EXISTS databricksdemos.bronze")
spark.sql("CREATE SCHEMA IF NOT EXISTS databricksdemos.silver")
spark.sql("CREATE SCHEMA IF NOT EXISTS databricksdemos.gold")
spark.sql("CREATE SCHEMA IF NOT EXISTS databricksdemos.ops")

display(spark.sql("SHOW SCHEMAS IN databricksdemos"))
```

Resultado esperado:

```text
bronze
silver
gold
ops
```

También pueden aparecer otros schemas como:

```text
default
```

---

# 4. Ingesta principal — Azure SQL SalesLT con conector Databricks

## 4.1 Objetivo

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

---

## 4.2 Abrir el asistente

En Databricks:

```text
Add data
  → Ingest data from SQL Server
```

---

## 4.3 Crear conexión con Azure SQL

En la pantalla **Create a connection to SQL Server**, seleccionar:

```text
Auth Type: Username and password
```

Configurar:

```text
Connection name: DemoDB
Host: databricksdemosql.database.windows.net
Port: 1433
Username: databricks_lab_user
Password: <password_de_laboratorio>
```

Si la pantalla solicita base de datos en este paso, usar:

```text
Database: databricksdemodb
```

Pulsar:

```text
Authenticate and create connection
```

Resultado esperado:

```text
La conexión se crea correctamente y el asistente avanza al paso de configuración de ingesta.
```

---

# 5. Configurar pipeline de ingesta

En el paso **Ingestion setup**, seleccionar:

```text
Query based capture
```

Para este laboratorio, `Query based capture` es suficiente y evita configuración adicional de CDC en Azure SQL.

## 5.1 Nombre del pipeline

Usar:

```text
Lab_SalesLT_Ingest
```

## 5.2 Event log location

Seleccionar:

```text
Catalog: databricksdemos
Schema: ops
```

Si el schema `ops` no aparece, crearlo desde un notebook:

```python
spark.sql("CREATE SCHEMA IF NOT EXISTS databricksdemos.ops")
```

O desde la propia interfaz con:

```text
+ Create schema
```

Nombre del schema:

```text
ops
```

## 5.3 Continuar

Pulsar:

```text
Create pipeline and continue
```

---

# 6. Seleccionar tablas origen

En la pantalla **Source**, seleccionar únicamente estas tablas:

```text
Customer
Product
SalesOrderHeader
SalesOrderDetail
```

No seleccionar tablas adicionales para mantener el laboratorio acotado.

Tablas no necesarias para este laboratorio:

```text
CustomerAddress
ProductCategory
ProductDescription
ProductModel
ProductModelProductDescription
vGetAllCategories
```

---

# 7. Configurar cada tabla

Para que el botón `Next` se active, cada tabla seleccionada debe tener configurados:

```text
Destination name
Cursor column
Primary key(s)
```

## 7.1 Tabla `Customer`

Seleccionar:

```text
databricksdemodb.SalesLT.Customer
```

Configurar:

```text
Destination name: saleslt_customer_raw
Cursor column: ModifiedDate
Primary key(s): CustomerID
```

---

## 7.2 Tabla `Product`

Seleccionar:

```text
databricksdemodb.SalesLT.Product
```

Configurar:

```text
Destination name: saleslt_product_raw
Cursor column: ModifiedDate
Primary key(s): ProductID
```

---

## 7.3 Tabla `SalesOrderHeader`

Seleccionar:

```text
databricksdemodb.SalesLT.SalesOrderHeader
```

Configurar:

```text
Destination name: saleslt_sales_order_header_raw
Cursor column: ModifiedDate
Primary key(s): SalesOrderID
```

Si `ModifiedDate` no aparece disponible, usar:

```text
Cursor column: OrderDate
```

---

## 7.4 Tabla `SalesOrderDetail`

Seleccionar:

```text
databricksdemodb.SalesLT.SalesOrderDetail
```

Configurar:

```text
Destination name: saleslt_sales_order_detail_raw
Cursor column: ModifiedDate
Primary key(s): SalesOrderID, SalesOrderDetailID
```

Si la interfaz solo permite una clave, seleccionar:

```text
Primary key(s): SalesOrderDetailID
```

---

# 8. Seleccionar destino en Databricks

En la pantalla **Destination**, seleccionar:

```text
Catalog: databricksdemos
Schema: bronze
```

Si el schema `bronze` no existe, crearlo desde un notebook:

```python
spark.sql("CREATE SCHEMA IF NOT EXISTS databricksdemos.bronze")
```

Después seleccionar:

```text
databricksdemos.bronze
```

Pulsar:

```text
Save and continue
```

Resultado esperado:

```text
Las tablas de Azure SQL SalesLT se cargarán en la capa Bronze de Databricks.
```

---

# 9. Configurar schedules y notificaciones

En la pantalla **Schedules and notifications**, revisar:

## 9.1 Schedule

Para el laboratorio, el schedule no es obligatorio.

Si aparece un schedule diario como:

```text
Every 1 hour
```

puede sustituirse por un schedule con menos frecuencia (`Every 1 day`) o eliminarse si no se desea ejecución automática.

Lo importante para la sesión es ejecutar manualmente el pipeline.

## 9.2 Notificaciones

Configurar una notificación de fallo:

```text
Email recipient: <tu email>
Success: desmarcado
Failure: marcado
```

## 9.3 Ejecutar pipeline

Pulsar:

```text
Save and run pipeline
```

---

# 10. Validar la ingesta desde notebook Python

## 10.1 Crear notebook de validación

En Databricks:

```text
Workspace
  → Create
  → Notebook
```

Nombre sugerido:

```text
validate_saleslt_ingestion
```

Lenguaje:

```text
Python
```
---

---

## 10.2 Validar tablas creadas en Bronze

Ejecutar:

```python
display(spark.sql("SHOW TABLES IN databricksdemos.bronze"))
```

Resultado esperado:

```text
saleslt_customer_raw
saleslt_product_raw
saleslt_sales_order_header_raw
saleslt_sales_order_detail_raw
```

En ese caso, ajustar las consultas siguientes con los nombres reales.

---

## 10.3 Validar muestra de datos

Ejecutar:

```python
display(
    spark.sql("""
        SELECT *
        FROM databricksdemos.bronze.saleslt_customer_raw
        LIMIT 10
    """)
)
```

```python
display(
    spark.sql("""
        SELECT *
        FROM databricksdemos.bronze.saleslt_product_raw
        LIMIT 10
    """)
)
```

```python
display(
    spark.sql("""
        SELECT *
        FROM databricksdemos.bronze.saleslt_sales_order_header_raw
        LIMIT 10
    """)
)
```

```python
display(
    spark.sql("""
        SELECT *
        FROM databricksdemos.bronze.saleslt_sales_order_detail_raw
        LIMIT 10
    """)
)
```

---

## 10.4 Validar conteos

Ejecutar:

```python
display(
    spark.sql("""
        SELECT COUNT(*) AS customers_count
        FROM databricksdemos.bronze.saleslt_customer_raw
    """)
)
```

```python
display(
    spark.sql("""
        SELECT COUNT(*) AS products_count
        FROM databricksdemos.bronze.saleslt_product_raw
    """)
)
```

```python
display(
    spark.sql("""
        SELECT COUNT(*) AS order_headers_count
        FROM databricksdemos.bronze.saleslt_sales_order_header_raw
    """)
)
```

```python
display(
    spark.sql("""
        SELECT COUNT(*) AS order_details_count
        FROM databricksdemos.bronze.saleslt_sales_order_detail_raw
    """)
)
```

---

## 10.5 Validar event log location

El event log se configuró en:

```text
databricksdemos.ops
```

Para revisar las tablas creadas en `ops`, ejecutar:

```python
display(spark.sql("SHOW TABLES IN databricksdemos.ops"))
```

`ops` empezará a tener tablas cuando se ejecute la parte de validación y observabilidad del laboratorio.

---

# 11. Plan B — Ingesta demo dentro de Databricks

> [!WARNING]
Usar esta sección solo si la conexión a Azure SQL no funciona durante la sesión.

## 11.1 Objetivo

Generar datos equivalentes dentro de Databricks para poder continuar el laboratorio sin depender de la conexión externa.

Tablas creadas:

```text
databricksdemos.bronze.events_raw
databricksdemos.bronze.customers_raw
databricksdemos.bronze.orders_raw
databricksdemos.bronze.products_raw
```

## 11.2 Código de fallback

```python
from pyspark.sql import functions as F
from pyspark.sql import types as T

env = "dev"
catalog = "databricksdemos"
process_date = "2025-01-15"

spark.sql(f"CREATE SCHEMA IF NOT EXISTS {catalog}.bronze")
spark.sql(f"CREATE SCHEMA IF NOT EXISTS {catalog}.ops")
spark.sql(f"USE CATALOG {catalog}")

def add_ingestion_metadata(df, source_name):
    return (
        df
        .withColumn("_source", F.lit(source_name))
        .withColumn("_env", F.lit(env))
        .withColumn("_process_date", F.to_date(F.lit(process_date)))
        .withColumn("_ingestion_ts", F.current_timestamp())
    )

events_rows = [
    ("evt-001", "C001", "page_view", "2025-01-15T10:00:00Z", 0.0),
    ("evt-002", "C001", "purchase", "2025-01-15T10:04:00Z", 120.5),
    ("evt-003", "C002", "purchase", "2025-01-15T11:15:00Z", 75.0),
    ("evt-004", "C003", "page_view", "2025-01-15T12:20:00Z", 0.0)
]

events_schema = T.StructType([
    T.StructField("event_id", T.StringType(), False),
    T.StructField("customer_id", T.StringType(), False),
    T.StructField("event_type", T.StringType(), False),
    T.StructField("event_ts", T.StringType(), False),
    T.StructField("amount", T.DoubleType(), False)
])

events_df = spark.createDataFrame(events_rows, events_schema)

customers_rows = [
    ("C001", "Ana García", "ES", "retail", "2024-12-01"),
    ("C002", "João Silva", "PT", "retail", "2024-12-05"),
    ("C003", "Marie Dubois", "FR", "enterprise", "2024-12-07"),
    ("C004", "Luca Rossi", "IT", "enterprise", "2024-12-10")
]

customers_schema = T.StructType([
    T.StructField("customer_id", T.StringType(), False),
    T.StructField("customer_name", T.StringType(), False),
    T.StructField("country", T.StringType(), False),
    T.StructField("segment", T.StringType(), False),
    T.StructField("created_at", T.StringType(), False)
])

customers_df = spark.createDataFrame(customers_rows, customers_schema)

orders_rows = [
    ("O001", "C001", "P001", 2, 240.0, "2025-01-15"),
    ("O002", "C002", "P003", 1, 75.0, "2025-01-15"),
    ("O003", "C003", "P002", 1, 310.0, "2025-01-16"),
    ("O004", "C001", "P003", 3, 225.0, "2025-01-16")
]

orders_schema = T.StructType([
    T.StructField("order_id", T.StringType(), False),
    T.StructField("customer_id", T.StringType(), False),
    T.StructField("product_id", T.StringType(), False),
    T.StructField("quantity", T.IntegerType(), False),
    T.StructField("net_amount", T.DoubleType(), False),
    T.StructField("order_date", T.StringType(), False)
])

orders_df = spark.createDataFrame(orders_rows, orders_schema)

products_rows = [
    ("P001", "Sensor IoT", "hardware"),
    ("P002", "Gateway Edge", "hardware"),
    ("P003", "Analytics Pack", "software")
]

products_schema = T.StructType([
    T.StructField("product_id", T.StringType(), False),
    T.StructField("product_name", T.StringType(), False),
    T.StructField("category", T.StringType(), False)
])

products_df = spark.createDataFrame(products_rows, products_schema)

add_ingestion_metadata(events_df, "demo_events").write.format("delta").mode("overwrite").option("overwriteSchema", "true").saveAsTable(f"{catalog}.bronze.events_raw")
add_ingestion_metadata(customers_df, "demo_customers").write.format("delta").mode("overwrite").option("overwriteSchema", "true").saveAsTable(f"{catalog}.bronze.customers_raw")
add_ingestion_metadata(orders_df, "demo_orders").write.format("delta").mode("overwrite").option("overwriteSchema", "true").saveAsTable(f"{catalog}.bronze.orders_raw")
add_ingestion_metadata(products_df, "demo_products").write.format("delta").mode("overwrite").option("overwriteSchema", "true").saveAsTable(f"{catalog}.bronze.products_raw")

display(spark.sql(f"SHOW TABLES IN {catalog}.bronze"))
```

---

# 12. Notebook — Validación y observabilidad

## 12.1 Objetivo

Validar que existen datos en Bronze y registrar resultados en una tabla operacional.

Este notebook detecta automáticamente si el origen fue:

```text
Azure SQL SalesLT
```

o:

```text
Plan B demo
```

Tabla de resultados:

```text
databricksdemos.ops.quality_results
```

## 12.2 Código completo

```python
# Databricks notebook source
# MAGIC %md
# MAGIC # Validación y observabilidad

# COMMAND ----------

from pyspark.sql import functions as F

# COMMAND ----------

dbutils.widgets.text("env", "dev")
dbutils.widgets.text("catalog", "databricksdemos")
dbutils.widgets.text("process_date", "2025-01-15")
dbutils.widgets.dropdown("fail_on_error", "true", ["true", "false"])

env = dbutils.widgets.get("env")
catalog = dbutils.widgets.get("catalog")
process_date = dbutils.widgets.get("process_date")
fail_on_error = dbutils.widgets.get("fail_on_error").lower() == "true"

spark.sql(f"CREATE SCHEMA IF NOT EXISTS {catalog}.ops")
spark.sql(f"USE CATALOG {catalog}")

# COMMAND ----------

def table_exists(full_name):
    try:
        spark.table(full_name).limit(1).count()
        return True
    except Exception:
        return False


def result(rule_id, table_name, rule_description, metric_value, threshold_value, passed):
    return (
        rule_id,
        table_name,
        rule_description,
        float(metric_value),
        float(threshold_value),
        "PASS" if passed else "FAIL"
    )

# COMMAND ----------

saleslt_tables = {
    "customer": f"{catalog}.bronze.saleslt_customer_raw",
    "product": f"{catalog}.bronze.saleslt_product_raw",
    "order_header": f"{catalog}.bronze.saleslt_sales_order_header_raw",
    "order_detail": f"{catalog}.bronze.saleslt_sales_order_detail_raw"
}

demo_tables = {
    "events": f"{catalog}.bronze.events_raw",
    "customers": f"{catalog}.bronze.customers_raw",
    "orders": f"{catalog}.bronze.orders_raw",
    "products": f"{catalog}.bronze.products_raw"
}

using_saleslt = all(table_exists(name) for name in saleslt_tables.values())
using_demo = all(table_exists(name) for name in demo_tables.values())

if not using_saleslt and not using_demo:
    raise Exception("No se han encontrado tablas Bronze esperadas para SalesLT ni para Plan B demo.")

source_pattern = "saleslt" if using_saleslt else "demo"

# COMMAND ----------

results = []

tables_to_validate = saleslt_tables if using_saleslt else demo_tables

for logical_name, table_name in tables_to_validate.items():
    row_count = spark.table(table_name).count()
    results.append(
        result(
            f"Q-COUNT-{logical_name.upper()}",
            table_name,
            f"{logical_name} must have rows",
            row_count,
            1,
            row_count >= 1
        )
    )

if using_saleslt:
    customer_nulls = spark.table(saleslt_tables["customer"]).filter(F.col("CustomerID").isNull()).count()
    product_nulls = spark.table(saleslt_tables["product"]).filter(F.col("ProductID").isNull()).count()
    header_nulls = spark.table(saleslt_tables["order_header"]).filter(F.col("SalesOrderID").isNull()).count()
    detail_nulls = spark.table(saleslt_tables["order_detail"]).filter(F.col("SalesOrderDetailID").isNull()).count()

    results.append(result("Q-PK-CUSTOMER", saleslt_tables["customer"], "CustomerID must not be null", customer_nulls, 0, customer_nulls == 0))
    results.append(result("Q-PK-PRODUCT", saleslt_tables["product"], "ProductID must not be null", product_nulls, 0, product_nulls == 0))
    results.append(result("Q-PK-HEADER", saleslt_tables["order_header"], "SalesOrderID must not be null", header_nulls, 0, header_nulls == 0))
    results.append(result("Q-PK-DETAIL", saleslt_tables["order_detail"], "SalesOrderDetailID must not be null", detail_nulls, 0, detail_nulls == 0))

else:
    event_nulls = spark.table(demo_tables["events"]).filter(F.col("event_id").isNull()).count()
    customer_nulls = spark.table(demo_tables["customers"]).filter(F.col("customer_id").isNull()).count()
    order_nulls = spark.table(demo_tables["orders"]).filter(F.col("order_id").isNull()).count()
    product_nulls = spark.table(demo_tables["products"]).filter(F.col("product_id").isNull()).count()

    results.append(result("Q-PK-EVENT", demo_tables["events"], "event_id must not be null", event_nulls, 0, event_nulls == 0))
    results.append(result("Q-PK-CUSTOMER", demo_tables["customers"], "customer_id must not be null", customer_nulls, 0, customer_nulls == 0))
    results.append(result("Q-PK-ORDER", demo_tables["orders"], "order_id must not be null", order_nulls, 0, order_nulls == 0))
    results.append(result("Q-PK-PRODUCT", demo_tables["products"], "product_id must not be null", product_nulls, 0, product_nulls == 0))

# COMMAND ----------

quality_df = (
    spark.createDataFrame(
        results,
        ["rule_id", "table_name", "rule_description", "metric_value", "threshold_value", "status"]
    )
    .withColumn("env", F.lit(env))
    .withColumn("source_pattern", F.lit(source_pattern))
    .withColumn("process_date", F.to_date(F.lit(process_date)))
    .withColumn("created_at", F.current_timestamp())
)

quality_df.write.format("delta").mode("append").saveAsTable(f"{catalog}.ops.quality_results")

display(quality_df)

failed_rules = quality_df.filter(F.col("status") == "FAIL").count()

if failed_rules > 0 and fail_on_error:
    raise Exception(f"Quality validation failed with {failed_rules} failed rule(s).")
```

---

# 13. Notebook — Modernización Silver/Gold

## 13.1 Objetivo

Crear tablas Silver y Gold con un modelo común independientemente de si Bronze viene de:

```text
Azure SQL SalesLT
```

o de:

```text
Plan B demo
```

Tablas creadas:

```text
databricksdemos.silver.customers
databricksdemos.silver.products
databricksdemos.silver.orders
databricksdemos.gold.sales_daily
databricksdemos.gold.customer_activity
```

Vistas creadas:

```text
databricksdemos.gold.v_powerbi_sales_daily
databricksdemos.gold.v_powerbi_customer_activity
```

## 13.2 Código completo

```python
# Databricks notebook source
# MAGIC %md
# MAGIC # Modernización Silver/Gold

# COMMAND ----------

from pyspark.sql import functions as F

# COMMAND ----------

dbutils.widgets.text("env", "dev")
dbutils.widgets.text("catalog", "databricksdemos")
dbutils.widgets.text("process_date", "2025-01-15")

env = dbutils.widgets.get("env")
catalog = dbutils.widgets.get("catalog")
process_date = dbutils.widgets.get("process_date")

spark.sql(f"CREATE SCHEMA IF NOT EXISTS {catalog}.silver")
spark.sql(f"CREATE SCHEMA IF NOT EXISTS {catalog}.gold")
spark.sql(f"CREATE SCHEMA IF NOT EXISTS {catalog}.ops")
spark.sql(f"USE CATALOG {catalog}")

# COMMAND ----------

def table_exists(full_name):
    try:
        spark.table(full_name).limit(1).count()
        return True
    except Exception:
        return False

saleslt_customer = f"{catalog}.bronze.saleslt_customer_raw"
saleslt_product = f"{catalog}.bronze.saleslt_product_raw"
saleslt_header = f"{catalog}.bronze.saleslt_sales_order_header_raw"
saleslt_detail = f"{catalog}.bronze.saleslt_sales_order_detail_raw"

using_saleslt = all(table_exists(t) for t in [saleslt_customer, saleslt_product, saleslt_header, saleslt_detail])

# COMMAND ----------

if using_saleslt:
    customers_silver = (
        spark.table(saleslt_customer)
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

    products_silver = (
        spark.table(saleslt_product)
        .select(
            F.col("ProductID").cast("string").alias("product_id"),
            F.col("Name").cast("string").alias("product_name"),
            F.coalesce(F.col("ProductCategoryID").cast("string"), F.lit("unknown")).alias("category"),
            F.lit("azure_sql_saleslt").alias("source_system"),
            F.lit(env).alias("env"),
            F.to_date(F.lit(process_date)).alias("process_date"),
            F.current_timestamp().alias("ingestion_ts")
        )
        .dropDuplicates(["product_id"])
    )

    orders_silver = (
        spark.table(saleslt_header).alias("h")
        .join(
            spark.table(saleslt_detail).alias("d"),
            F.col("h.SalesOrderID") == F.col("d.SalesOrderID"),
            "inner"
        )
        .select(
            F.concat(F.col("h.SalesOrderID").cast("string"), F.lit("-"), F.col("d.SalesOrderDetailID").cast("string")).alias("order_id"),
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

else:
    customers_silver = (
        spark.table(f"{catalog}.bronze.customers_raw")
        .select(
            F.col("customer_id").cast("string").alias("customer_id"),
            F.col("customer_name").cast("string").alias("customer_name"),
            F.col("country").cast("string").alias("country"),
            F.col("segment").cast("string").alias("segment"),
            F.to_date("created_at").alias("created_at"),
            F.col("_source").alias("source_system"),
            F.col("_env").alias("env"),
            F.col("_process_date").alias("process_date"),
            F.col("_ingestion_ts").alias("ingestion_ts")
        )
        .dropDuplicates(["customer_id"])
    )

    products_silver = (
        spark.table(f"{catalog}.bronze.products_raw")
        .select(
            F.col("product_id").cast("string").alias("product_id"),
            F.col("product_name").cast("string").alias("product_name"),
            F.col("category").cast("string").alias("category"),
            F.col("_source").alias("source_system"),
            F.col("_env").alias("env"),
            F.col("_process_date").alias("process_date"),
            F.col("_ingestion_ts").alias("ingestion_ts")
        )
        .dropDuplicates(["product_id"])
    )

    orders_silver = (
        spark.table(f"{catalog}.bronze.orders_raw")
        .select(
            F.col("order_id").cast("string").alias("order_id"),
            F.col("customer_id").cast("string").alias("customer_id"),
            F.col("product_id").cast("string").alias("product_id"),
            F.col("quantity").cast("int").alias("quantity"),
            F.col("net_amount").cast("double").alias("net_amount"),
            F.to_date("order_date").alias("order_date"),
            F.col("_source").alias("source_system"),
            F.col("_env").alias("env"),
            F.col("_process_date").alias("process_date"),
            F.col("_ingestion_ts").alias("ingestion_ts")
        )
        .dropDuplicates(["order_id"])
    )

# COMMAND ----------

customers_silver.write.format("delta").mode("overwrite").option("overwriteSchema", "true").saveAsTable(f"{catalog}.silver.customers")
products_silver.write.format("delta").mode("overwrite").option("overwriteSchema", "true").saveAsTable(f"{catalog}.silver.products")
orders_silver.write.format("delta").mode("overwrite").option("overwriteSchema", "true").saveAsTable(f"{catalog}.silver.orders")

# COMMAND ----------

sales_daily = (
    orders_silver.alias("o")
    .join(customers_silver.alias("c"), F.col("o.customer_id") == F.col("c.customer_id"), "left")
    .join(products_silver.alias("p"), F.col("o.product_id") == F.col("p.product_id"), "left")
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

# COMMAND ----------

sales_daily.write.format("delta").mode("overwrite").option("overwriteSchema", "true").saveAsTable(f"{catalog}.gold.sales_daily")
customer_activity.write.format("delta").mode("overwrite").option("overwriteSchema", "true").saveAsTable(f"{catalog}.gold.customer_activity")

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

# COMMAND ----------

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
    .withColumn("process_date", F.to_date(F.lit(process_date)))
    .withColumn("created_at", F.current_timestamp())
)

summary_df.write.format("delta").mode("append").saveAsTable(f"{catalog}.ops.pipeline_run_summary")

display(summary_df)
```

---

# 14. Validaciones finales desde notebook Python

Ejecutar:

```python
display(spark.sql("SELECT * FROM databricksdemos.gold.v_powerbi_sales_daily"))
```

```python
display(spark.sql("SELECT * FROM databricksdemos.gold.v_powerbi_customer_activity"))
```

Validar observabilidad:

```python
display(
    spark.sql("""
        SELECT
          layer,
          table_name,
          process_date,
          SUM(row_count) AS rows_written,
          MAX(created_at) AS last_update
        FROM databricksdemos.ops.pipeline_run_summary
        GROUP BY layer, table_name, process_date
        ORDER BY layer, table_name
    """)
)
```

```python
display(
    spark.sql("""
        SELECT
          status,
          COUNT(*) AS rules_count
        FROM databricksdemos.ops.quality_results
        GROUP BY status
    """)
)
```

---

# 15. Crear Databricks Job multitarea

## 15.1 DAG objetivo

```text
ingesta_saleslt_o_demo
  → validacion_observabilidad
    → modernizacion_silver_gold
```

## 15.2 Crear desde UI

En Databricks:

```text
Workflows
  → Create job
```

Nombre:

```text
dbrx-lakehouse-ops-lab-dev
```

Crear tareas:

```text
validacion_observabilidad
modernizacion_silver_gold
```

La tarea de ingesta puede ser:

```text
pipeline lakehouse_ops_saleslt_ingest
```

si se usó Azure SQL, o:

```text
notebook Plan B demo
```

si se usó fallback.

Configurar dependencias:

```text
ingesta_saleslt_o_demo → validacion_observabilidad → modernizacion_silver_gold
```

## 15.3 Parámetros recomendados

```text
env=dev
catalog=databricksdemos
process_date=2025-01-15
```

## 15.4 Alertas mínimas

Configurar:

```text
On failure: <email_instructora>
Duration warning threshold: 1800 seconds
Max concurrent runs: 1
```

---

# 16. Cierre del laboratorio

## 16.1 Resultado final

Con la opción principal, se habrá demostrado:

```text
Azure SQL SalesLT
  → Conector Databricks
  → bronze
  → validación
  → silver / gold
  → observabilidad
  → Databricks Job
```

Con el plan B, se habrá demostrado el mismo patrón operativo con datos generados dentro de Databricks.

## 16.2 Mensaje final

```text
La fuente puede cambiar.

Puede ser Azure SQL, Storage, SAP Delta o datos demo.

El patrón operativo permanece:

- ingesta gobernada;
- validación;
- transformación Silver/Gold;
- observabilidad;
- orquestación en Databricks Jobs;
- ADF como disparador externo o coordinador cross-platform.
```

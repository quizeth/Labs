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
- Limpiar y tipar datos con **Power Query** usando código M reutilizable.
- Resolver duplicados conservando el **registro más reciente por `SHIPMENT_ID`**.
- Crear columnas de negocio y normalizar valores incompletos.
- Separar una tabla plana en un **modelo en estrella** (`fct_shipments` + dimensiones).
- Cambiar la **fuente de datos** de una consulta ya cargada y transformada sin rehacer el trabajo.
- Aplicar buenas prácticas de Power Query: staging, referencias, nombres descriptivos y deshabilitar carga intermedia.

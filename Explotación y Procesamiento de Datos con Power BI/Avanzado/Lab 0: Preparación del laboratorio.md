# Laboratorio 0: Preparación del laboratorio

## Requisitos previos

- Power BI Desktop instalado.
- Conocimientos de Power Query a nivel intermedio.
- Permisos para guardar archivos locales.
- Crear la carpeta de trabajo sugerida: `C:\Labs\ParcelCraft\`

---

## Archivos del laboratorio

Guarda los siguientes archivos en tu carpeta local de trabajo:

- `ParcelCraft_Fuente_Plana_Snowflake_SAP_v1.csv`
- `ParcelCraft_Fuente_Plana_Snowflake_SAP_v2.csv`

### Descripción funcional de la fuente

La tabla plana contiene:

- Datos del envío: `SHIPMENT_ID`, `SHIPMENT_DATE`, `DELIVERY_STATUS`, `ATTEMPT_COUNT`.
- Datos de oficina repetidos por fila: `OFFICE_CODE`, `OFFICE_NAME`, `CITY`, `REGION`, `HUB_TYPE`.
- Datos de cliente repetidos: `CUSTOMER_CODE`, `CUSTOMER_NAME`, `CUSTOMER_SEGMENT`, `CUSTOMER_TYPE`, `PREFERRED_SERVICE_CODE`.
- Datos de servicio repetidos: `SERVICE_CODE`, `SERVICE_NAME`, `PRIORITY_LEVEL`, `SLA_HOURS`.
- Datos técnicos de extracción: `SOURCE_SYSTEM`, `SOURCE_PLATFORM`, `SAP_DOCUMENT_NO`, `SNOWFLAKE_BATCH_ID`, `EXTRACT_TS_UTC`.

# Importación de datos (5 % del examen)

Las rutas para incorporar datos a Databricks (sección 3 del PDF): carga desde la UI, ingesta desde S3 con `COPY INTO`, Auto Loader, Delta Sharing (ahora OpenSharing), ingesta mediante API y Marketplace.


## Contenido de la sección

| Archivo | Tema | Prioridad |
| :--- | :--- | :--- |
| [01-importing-data-overview.md](./01-importing-data-overview.md) | Carga desde la UI, S3 con `COPY INTO`, Auto Loader, Delta Sharing, Zerobus Ingest, Marketplace | Alta |


## Conceptos clave

| Concepto | Por qué importa |
| :--- | :--- |
| **Carga de archivos desde la UI** | CSV / TSV / JSON / Avro / Parquet / texto, hasta 10 archivos y menos de 2 GB, en una tabla Delta administrada |
| **`COPY INTO`** | Carga por lotes idempotente desde S3 o un volumen; omite los archivos ya cargados |
| **Auto Loader** | Ingesta de archivos incremental y exactamente una vez; millones de archivos; `STREAM read_files` en SQL |
| **Delta Sharing (OpenSharing)** | Uso compartido en vivo y de solo lectura entre organizaciones y plataformas; sin copia |
| **Zerobus Ingest** | API basada en envío (push) que escribe registros en tablas Delta de Unity Catalog |
| **Marketplace** | Productos de datos entregados como catálogos compartidos |

## Recursos relacionados

- [Ingesta de datos desde almacenamiento de objetos en la nube](https://docs.databricks.com/aws/en/ingestion/cloud-object-storage/)
- [¿Qué es OpenSharing?](https://docs.databricks.com/aws/en/delta-sharing/)
- [Preguntas de práctica — Importación de datos](../resources/practice-questions/08-importing-data.md)

> [!note]
> El examen de Data Analyst evalúa el *qué* y el *cuándo* de cada ruta, no la configuración detallada. A diferencia de versiones anteriores de esta guía, Auto Loader **sí** está dentro del alcance: se menciona en la guía del examen.

---

**[← Anterior: Protección de datos](../07-securing-data/README.md) | [↑ Volver a Data Analyst Associate](../README.md) | [Siguiente: Modelado de datos con Databricks SQL →](../09-data-modeling-with-databricks-sql/README.md)**

# Ejecución de consultas con Databricks SQL y SQL warehouses (20 % del examen)

Es el dominio más extenso del temario de octubre de 2025 (sección 4 del PDF). Abarca el SQL warehouse que ejecuta cada consulta, el editor de SQL y Databricks Assistant, el SQL que se escribe (joins, operaciones de conjuntos, agregaciones, ordenamiento y filtrado), la creación de tablas y vistas, el time travel y las consultas federadas.

## Contenido de la sección

| Archivo | Tema | Prioridad |
| :--- | :--- | :--- |
| [01-sql-warehouses.md](./01-sql-warehouses.md) | Función de un SQL warehouse; serverless frente a pro y classic; tamaño, escalado, auto stop | Alta |
| [02-query-editor.md](./02-query-editor.md) | Editor de SQL, parámetros con nombre, Databricks Assistant (ahora Genie Code), historial de consultas | Alta |
| [03-joins-and-set-operations.md](./03-joins-and-set-operations.md) | Tipos de join, claves compuestas, `UNION` / `UNION ALL` / `INTERSECT` / `EXCEPT` | Alta |
| [04-aggregations-grouping.md](./04-aggregations-grouping.md) | `COUNT`, `AVG`, `approx_count_distinct`, estadísticas de resumen, `GROUP BY`, ordenamiento y filtrado | Alta |
| [05-window-functions.md](./05-window-functions.md) | Clasificación, `LAG`/`LEAD`, totales acumulados, `QUALIFY` | Media |
| [06-creating-tables-and-time-travel.md](./06-creating-tables-and-time-travel.md) | Tablas administradas y externas, CTAS con `read_files`, `CREATE OR REPLACE`, time travel | Alta |
| [07-materialized-views-and-streaming-tables.md](./07-materialized-views-and-streaming-tables.md) | Vistas materializadas frente a tablas de streaming y vistas dinámicas | Alta |
| [08-federated-queries.md](./08-federated-queries.md) | Lakehouse Federation: unir una tabla Delta con una base de datos externa | Media |


## Conceptos clave

| Concepto | Por qué importa |
| :--- | :--- |
| **SQL warehouse** | El cómputo detrás del editor de SQL, los paneles, las alertas, Genie y las herramientas de BI; ejecuta Photon |
| **Serverless frente a pro y classic** | Serverless se inicia en segundos y dispone de IWM; pro y classic tardan unos 4 minutos; la federación requiere pro o serverless |
| **Tamaño frente a escalado** | Tamaño = workers por clúster (latencia); clústeres máximos = concurrencia |
| **Databricks Assistant (Genie Code)** | `/explain`, `/fix`, `/optimize` ayudan a escribir y depurar SQL |
| **`UNION` frente a `UNION ALL`** | `UNION` elimina duplicados; `UNION ALL` los conserva |
| **`approx_count_distinct`** | Estimación rápida de valores distintos mediante HyperLogLog++ |
| **`CREATE OR REPLACE TABLE`** | Conserva el historial y los permisos; `DROP` + `CREATE` los pierde |
| **Vista materializada frente a tabla de streaming** | Una MV precalcula una consulta; una ST añade filas nuevas de una fuente de solo adición |
| **Time travel** | `VERSION AS OF` / `TIMESTAMP AS OF`; limitado por `VACUUM` |

## Recursos relacionados

- [Documentación de Databricks SQL](https://docs.databricks.com/aws/en/sql/)
- [Hoja de referencia de funciones SQL (compartida)](../../../shared/cheat-sheets/sql-functions.md)
- [Ejemplos de código de funciones de ventana (compartidos)](../../../shared/code-examples/sql/window_functions.md)
- [Preguntas de práctica: Ejecución de consultas](../resources/practice-questions/01-executing-queries.md)

---

**[↑ Volver a Data Analyst Associate](../README.md) | [Siguiente: Creación de paneles y visualizaciones →](../02-creating-dashboards-and-visualizations/README.md)** *(primer dominio: sin anterior)*

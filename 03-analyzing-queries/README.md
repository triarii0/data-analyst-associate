# Análisis de consultas (15 % del examen)

Cómo encontrar, explicar y corregir consultas lentas o incorrectas: Photon, el historial de consultas, el query profile, la caché, el historial de Delta Lake, el liquid clustering y errores comunes de SQL (sección 5 del PDF).

> [!note]
> Hasta octubre de 2026 esta carpeta contenía joins, agregaciones, funciones de ventana y parámetros de consulta. Esas competencias pertenecen a otras secciones de la guía del examen, por lo que se trasladaron a [Ejecución de consultas](../01-executing-queries-databricks-sql-warehouses/README.md) y [Creación de dashboards y visualizaciones](../02-creating-dashboards-and-visualizations/README.md).


## Contenido de la sección

| Archivo | Tema | Prioridad |
| :--- | :--- | :--- |
| [01-photon-and-query-performance.md](./01-photon-and-query-performance.md) | Características y límites de Photon, historial de consultas, `system.query.history`, query profile, performance insights, cachés | Alta |
| [02-delta-history-and-liquid-clustering.md](./02-delta-history-and-liquid-clustering.md) | `DESCRIBE HISTORY`, comparación de versiones, `RESTORE`, liquid clustering, `OPTIMIZE` | Alta |
| [03-fixing-queries.md](./03-fixing-queries.md) | Errores comunes de agrupación, NULL, join y filtrado, y sus correcciones | Alta |


## Conceptos clave

| Concepto | Por qué importa |
| :--- | :--- |
| **Photon** | Motor vectorizado, predeterminado en los SQL warehouses; las UDFs recurren a Spark |
| **Query history** | Filtrar por usuario, fecha, compute, estado; `system.query.history` para SQL |
| **Query profile** | Filas, tiempo y memoria por operador; revela exploding joins y full table scans |
| **Result caches** | Local (por clúster) y remota (serverless, compartida, sobrevive a reinicios); se invalidan con cambios en las tablas |
| **`DESCRIBE HISTORY`** | Quién cambió una tabla, cómo y cuándo |
| **Liquid clustering** | `CLUSTER BY` en hasta cuatro columnas de filtrado; reemplaza el particionamiento y `ZORDER` |

## Recursos relacionados

- [Hoja de referencia de optimización del rendimiento (compartida)](../../../shared/cheat-sheets/performance-optimization.md)
- [Hoja de referencia de comandos de Delta Lake (compartida)](../../../shared/cheat-sheets/delta-lake-commands.md)
- [Preguntas de práctica: análisis de consultas](../resources/practice-questions/03-analyzing-queries.md)

---

**[← Anterior: Creación de dashboards](../02-creating-dashboards-and-visualizations/README.md) | [↑ Volver a Data Analyst Associate](../README.md) | [Siguiente: Desarrollo de AI/BI Genie Spaces →](../04-developing-sharing-maintaining-genie-spaces/README.md)**

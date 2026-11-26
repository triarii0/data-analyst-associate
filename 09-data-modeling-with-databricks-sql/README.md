# Modelado de datos con Databricks SQL (5 % del examen)

Modelos estándar del sector —estrella, copo de nieve y Data Vault— y cómo se alinean con la arquitectura Medallion (sección 8 del PDF), además de vistas y vistas materializadas para dar forma a los datos.


## Contenido de la sección

| Archivo | Tema | Prioridad |
| :--- | :--- | :--- |
| [01-data-modeling-overview.md](./01-data-modeling-overview.md) | Medallion, estrella frente a copo de nieve, Data Vault, correspondencia modelo-capa, vistas frente a MV | Alta |


## Conceptos clave

| Concepto | Por qué importa |
| :--- | :--- |
| **Medallion (Bronze / Silver / Gold)** | Capas de múltiples saltos (multi-hop): los analistas suelen consultar Gold |
| **Esquema estrella** | Tabla de hechos central + tablas de dimensiones; la forma canónica y fácil de usar para analistas |
| **Esquema copo de nieve** | Estrella con tablas de dimensiones normalizadas: más uniones, más flexibilidad |
| **Data Vault** | Hubs, links, satellites; optimizado para escritura; normalmente en Silver, con esquemas estrella en Gold |
| **Vista estándar** | Consulta guardada ligera; se ejecuta al leer |
| **Vista materializada** | Resultado precalculado, actualizado según una programación o ante cambios: consume almacenamiento y ahorra cómputo al leer |
| **Actualización incremental de MV** | Cuando el origen es Delta y la consulta puede expresarse de forma incremental, Databricks SQL actualiza solo las filas cambiadas: más económico que un recálculo completo |
| **Tabla de streaming** | Añade filas nuevas desde orígenes de solo inserción en cada actualización; consulte [Vistas materializadas y tablas de streaming](../01-executing-queries-databricks-sql-warehouses/07-materialized-views-and-streaming-tables.md) |

## Recursos relacionados

- [Arquitectura Medallion (compartido)](../../../shared/fundamentals/medallion-architecture.md)
- [Guía rápida de funciones SQL (compartido)](../../../shared/cheat-sheets/sql-functions.md)
- [Preguntas de práctica — Modelado de datos](../resources/practice-questions/09-data-modeling.md)

---

**[← Anterior: Importación de datos](../08-importing-data/README.md) | [↑ Volver a Data Analyst Associate](../README.md)**

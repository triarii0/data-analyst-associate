
# Gestión de datos (8 % del examen)

La gestión de datos de Unity Catalog desde la perspectiva del analista (sección 2 del PDF): descubrimiento y certificación de conjuntos de datos, etiquetas y linaje en Catalog Explorer, limpieza de datos con SQL y las tablas y esquemas subyacentes.

## Contenido de la sección

| Archivo | Tema | Prioridad |
| :--- | :--- | :--- |
| [01-tables-schemas.md](./01-tables-schemas.md) | `CREATE / ALTER / DROP` de tablas y esquemas; administradas frente a externas | Media |
| [02-discovering-tagging-and-cleaning-data.md](./02-discovering-tagging-and-cleaning-data.md) | Conjuntos de datos certificados, etiquetas, linaje y limpieza de datos con SQL | Alta |


## Conceptos clave

| Concepto | Por qué es importante |
| :--- | :--- |
| **Tablas administradas frente a externas** | Administradas: UC posee el almacenamiento; externas: UC posee los metadatos y tú el almacenamiento |
| **`CREATE OR REPLACE TABLE`** | Reemplazar una tabla Delta conservando su historial y sus permisos concedidos |
| **Datos certificados** | `system.certification_status` = `certified`; buscar `certificationStatus:certified` |
| **Etiquetas** | Se requiere `APPLY TAG`; etiquetas de columna mediante `ALTER TABLE ... ALTER COLUMN ... SET TAGS` |
| **Limpieza** | `DELETE ... WHERE`, `COALESCE`, `try_cast`, `DISTINCT`, `QUALIFY ROW_NUMBER() = 1` |
| **`CREATE TABLE AS SELECT` (CTAS)** | Materializar el resultado de una consulta como una nueva tabla Delta |
| **Operaciones `ALTER TABLE`** | Renombrar columnas, cambiar tipos (de forma limitada), añadir comentarios, establecer propiedades |
| **Semántica de `DROP`** | `DROP TABLE` en administradas = elimina los datos; en externas = elimina solo los metadatos |

## Recursos relacionados

- [Hoja de referencia de Delta Lake (compartida)](../../../shared/cheat-sheets/delta-lake-commands.md)
- [Hoja de referencia de Unity Catalog (compartida)](../../../shared/cheat-sheets/unity-catalog-quick-ref.md)
- [Preguntas de práctica — Gestión de datos](../resources/practice-questions/06-managing-data.md)

---

**[← Anterior: Comprender la plataforma de Databricks](../05-understanding-databricks-platform/README.md) | [↑ Volver a Data Analyst Associate](../README.md) | [Siguiente: Proteger los datos →](../07-securing-data/README.md)**

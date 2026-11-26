# Protección de datos (8 % del examen)

Roles, privilegios y propiedad de Unity Catalog; el espacio de nombres de tres niveles, incluidos los volúmenes; filtros de filas, máscaras de columna y vistas dinámicas; permisos de objetos del workspace; y protección de PII (sección 9 del PDF).


## Contenido de la sección

| Archivo | Tema | Prioridad |
| :--- | :--- | :--- |
| [01-access-control.md](./01-access-control.md) | Autenticación, ACL de objetos del workspace, `GRANT` / `REVOKE`, herencia de privilegios | Alta |
| [02-ownership-fine-grained-access-and-pii.md](./02-ownership-fine-grained-access-and-pii.md) | Roles de administrador, propiedad y `MANAGE`, espacio de nombres y volúmenes, filtros de filas, máscaras de columna, ABAC, lista de verificación de PII | Alta |


## Conceptos clave

| Concepto | Por qué importa |
| :--- | :--- |
| **Propietario frente a receptor (grantee)** | Los propietarios tienen todos los privilegios y pueden otorgarlos; los receptores solo tienen lo que se les otorgó |
| **`MANAGE`** | Gestionar privilegios, transferir la propiedad o eliminar sin ser propietario |
| **Herencia** | Los permisos otorgados a nivel de catálogo y esquema se aplican a los elementos secundarios actuales y futuros |
| **No hay `DENY` en Unity Catalog** | `DENY` solo existe para el `hive_metastore` heredado; use `REVOKE` |
| **Filtro de filas** | UDF de SQL que devuelve TRUE/FALSE por fila y se asocia a la tabla |
| **Máscara de columna** | UDF de SQL que devuelve el valor original o uno enmascarado, una por columna |
| **Políticas ABAC** | Reglas basadas en etiquetas aplicadas a muchas tablas; recomendación de Databricks a escala |
| **Volúmenes** | Objetos de tercer nivel para archivos: `/Volumes/<catalog>/<schema>/<volume>/` |

## Recursos relacionados

- [Guía rápida de Unity Catalog (compartido)](../../../shared/cheat-sheets/unity-catalog-quick-ref.md)
- [Documentación sobre filtros de filas y máscaras de columna](https://docs.databricks.com/aws/en/data-governance/unity-catalog/filters-and-masks/)
- [Preguntas de práctica — Protección de datos](../resources/practice-questions/07-securing-data.md)

---

**[← Anterior: Gestión de datos](../06-managing-data/README.md) | [↑ Volver a Data Analyst Associate](../README.md) | [Siguiente: Importación de datos →](../08-importing-data/README.md)**

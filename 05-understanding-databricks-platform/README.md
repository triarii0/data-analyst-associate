# Comprensión de Databricks Data + AI Platform (11 % del examen)

Los componentes principales de la plataforma, cómo Unity Catalog organiza los datos, qué muestra Catalog Explorer (tablas certificadas y linaje), Databricks Marketplace y cómo se conectan los analistas desde herramientas externas (sección 1 del PDF).

> [!note] Actualización de la plataforma (oct. de 2026)
> El PDF de la guía del examen denomina este dominio «Understanding of Databricks Data Intelligence Platform». La [página de certificación](https://www.databricks.com/learn/certification/data-analyst-associate) indica ahora **«Understanding of Databricks Data + AI Platform»**, en línea con la documentación actual. El peso (11 %) no ha cambiado.


## Contenido de la sección

| Archivo | Tema | Prioridad |
| :--- | :--- | :--- |
| [01-unity-catalog.md](./01-unity-catalog.md) | Arquitectura de Unity Catalog para analistas: espacio de nombres de tres niveles, catálogos, esquemas y tablas | Alta |
| [02-connections.md](./02-connections.md) | Configuración de conexiones JDBC / ODBC / Power BI / Tableau; autenticación OAuth y PAT | Media |
| [03-platform-components-and-marketplace.md](./03-platform-components-and-marketplace.md) | Componentes principales (nombres del examen y nombres actuales), Catalog Explorer, tablas certificadas, linaje y Marketplace | Alta |


## Conceptos clave

| Concepto | Por qué importa |
| :--- | :--- |
| **Espacio de nombres de tres niveles** | `catalog.schema.object`: todos los objetos de UC se direccionan así |
| **Catálogo frente a metastore** | Un workspace está vinculado a un metastore; el metastore contiene muchos catálogos |
| **Data + AI Platform** | Nombre actual de lo que el PDF denomina Data Intelligence Platform |
| **Delta Live Tables → Lakeflow Spark Declarative Pipelines** | El mismo producto de ETL declarativo; cambió de nombre el 12 de noviembre de 2025 |
| **Tablas certificadas** | `system.certification_status` = `certified` muestra una marca de verificación en Catalog Explorer |
| **Marketplace** | Productos de datos e IA entregados en vivo con Delta Sharing (ahora OpenSharing) |
| **Opciones de conexión para herramientas de BI** | OAuth para usuarios humanos; PAT u OAuth de service principal para cuentas de servicio |

## Recursos relacionados

- [Fundamentos de Unity Catalog (compartido)](../../../shared/fundamentals/unity-catalog-basics.md)
- [Guía rápida de Unity Catalog (compartido)](../../../shared/cheat-sheets/unity-catalog-quick-ref.md)
- [Arquitectura de la plataforma (compartido)](../../../shared/fundamentals/platform-architecture.md)
- [Documentación de integraciones de Databricks](https://docs.databricks.com/aws/en/integrations/)
- [Preguntas de práctica — Plataforma](../resources/practice-questions/05-databricks-platform.md)

---

**[← Anterior: Desarrollo de AI/BI Genie Spaces](../04-developing-sharing-maintaining-genie-spaces/README.md) | [↑ Volver a Data Analyst Associate](../README.md) | [Siguiente: Gestión de datos →](../06-managing-data/README.md)**

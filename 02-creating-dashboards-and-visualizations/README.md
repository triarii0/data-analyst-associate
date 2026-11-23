# Creación de paneles y visualizaciones (16 % del examen)

AI/BI dashboards, visualizaciones en cuadernos y en el editor de SQL, parámetros, uso compartido e inserción, actualizaciones programadas y alertas. El segundo dominio más extenso del temario de octubre de 2025 (sección 6 del PDF, titulada "Trabajar con paneles y visualizaciones en Databricks").


## Contenido de la sección

| Archivo | Tema | Prioridad |
| :--- | :--- | :--- |
| [01-dashboards.md](./01-dashboards.md) | Anatomía de AI/BI dashboards: pestaña Data, conjuntos de datos, páginas, widgets, filtros | Alta |
| [02-visualizations.md](./02-visualizations.md) | Creación de visualizaciones en cuadernos y en el editor de SQL; elección del tipo de gráfico | Alta |
| [03-alerts-scheduling.md](./03-alerts-scheduling.md) | Alertas de SQL (condición, umbral, destino), programaciones de paneles y suscripciones | Alta |
| [04-sharing-collaboration.md](./04-sharing-collaboration.md) | Niveles de permisos, credenciales de publicación, usuarios de la cuenta, inserción básica y externa | Alta |
| [05-parameters.md](./05-parameters.md) | Marcadores de parámetros con nombre, parámetros de panel, pruebas y sintaxis mustache heredada | Alta |


## Conceptos clave

| Concepto | Por qué importa |
| :--- | :--- |
| **AI/BI dashboards** | El producto actual de creación de paneles (antes Lakeview); ya no se pueden crear paneles heredados |
| **Conjunto de datos frente a widget** | Los conjuntos de datos están en la pestaña Data; los widgets (visualizaciones, filtros, texto, imágenes) se sitúan en las páginas |
| **Marcadores de parámetros con nombre** | `:name` en las consultas de conjuntos de datos; `{{ }}` solo se usa en el editor heredado |
| **Permisos de datos compartidos frente a individuales** | Si las consultas se ejecutan como el publicador o como cada espectador |
| **Inserción** | Básica (los espectadores inician sesión) frente a usuarios externos (principal de servicio) |
| **Programaciones y suscripciones** | Actualizar según una programación y enviar instantáneas PDF/PNG por correo electrónico, Slack o Teams |
| **Alertas de SQL** | Consulta + condición + programación + destino de notificación; estados OK, TRIGGERED, ERROR |

## Recursos relacionados

- [Documentación de AI/BI dashboards](https://docs.databricks.com/aws/en/dashboards/)
- [Documentación de alertas de Databricks SQL](https://docs.databricks.com/aws/en/sql/user/alerts/)
- [Preguntas de práctica — Paneles y visualizaciones](../resources/practice-questions/02-dashboards-visualizations.md)

---

**[← Anterior: Ejecución de consultas](../01-executing-queries-databricks-sql-warehouses/README.md) | [↑ Volver a Data Analyst Associate](../README.md) | [Siguiente: Análisis de consultas →](../03-analyzing-queries/README.md)**

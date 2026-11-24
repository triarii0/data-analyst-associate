# Desarrollo, uso compartido y mantenimiento de Genie Spaces de AI/BI (12 % del examen)

> [!important]
> **Novedad del esquema de octubre de 2025.** AI/BI Genie es la superficie de analítica en lenguaje natural que se sitúa sobre Unity Catalog. Los analistas seleccionan y curan un Genie space sobre un pequeño conjunto de tablas de UC; los usuarios empresariales formulan preguntas en lenguaje cotidiano y Genie genera SQL de solo lectura, lo ejecuta en un SQL warehouse y devuelve tablas, gráficos y texto.

> [!warning] Cambio de nombre del producto: Genie Spaces pasan a ser Genie Agents (8 de julio de 2026)
> Databricks renombró **Genie Spaces** a **Genie Agents** el 8 de julio de 2026. El nombre cambió en toda la documentación; las capacidades no. La guía del examen de octubre de 2025 (y, por tanto, el examen) sigue diciendo **Genie spaces**, y la API REST sigue usando `/api/2.0/genie/spaces/...`. Esta sección usa ambos nombres: "Genie space" al citar el examen y "Genie Agent" al describir la interfaz actual.

## Contenido de la sección

| Archivo | Tema | Prioridad |
| :--- | :--- | :--- |
| [01-genie-spaces-overview.md](./01-genie-spaces-overview.md) | Qué es un Genie Agent, cómo construye el contexto, requisitos, permisos, modo Chat frente a modo Agent | Alta |
| [02-tuning-genie-spaces.md](./02-tuning-genie-spaces.md) | Instrucciones de texto, SQL de ejemplo, funciones SQL, activos de confianza, almacén de conocimiento | Alta |
| [03-benchmarks-monitoring-and-sharing.md](./03-benchmarks-monitoring-and-sharing.md) | Benchmarks y puntuación, la pestaña Monitor, comentarios de usuarios, uso compartido y niveles de permiso | Alta |
| [04-genie-api-and-integrations.md](./04-genie-api-and-integrations.md) | Conversation API, SDK de Python, Databricks Apps, MCP, herramientas de agente | Media (examen: "integraciones externas con aplicaciones"; el detalle de la API es territorio del examen de GenAI) |


## Conceptos clave

| Concepto | Por qué importa |
| :--- | :--- |
| **Genie Agent** (antes Genie space) | Una interfaz de chat específica de un dominio sobre datos seleccionados de UC —tablas, vistas, vistas de métricas— más instrucciones, ejemplos y funciones |
| **Contexto** | Genie selecciona para cada pregunta los metadatos de UC, las entradas del almacén de conocimiento, el SQL de ejemplo, las funciones SQL, las instrucciones y el historial reciente de chat más relevantes |
| **Activos de confianza** | Consultas de ejemplo parametrizadas y funciones SQL de UC cuya lógica exacta verificó un autor: las respuestas que los usan se marcan como verificadas |
| **Almacén de conocimiento** | Definiciones semánticas propias del agente —descripciones, sinónimos, correspondencia de entidades, relaciones de unión, expresiones SQL— que nunca sobrescriben los metadatos de UC |
| **Instrucciones de texto** | Reglas empresariales en texto plano; la guía oficial indica usarlas *solo como último recurso* después de las expresiones SQL y el SQL de ejemplo |
| **Benchmarks** | Preguntas de prueba con SQL de referencia que se usan solo para puntuar la precisión, no para mejorar el contexto de Genie |
| **Permisos** | Genie se ejecuta en el warehouse del autor, pero cada consulta se comprueba con los permisos de UC del *usuario que pregunta*: los filtros de filas y las máscaras de columnas se aplican por usuario |
| **Modo Chat frente a modo Agent** | El modo Chat responde con una consulta SQL sobre datos estructurados; el modo Agent planifica una investigación en varios pasos y devuelve un informe con citas |

## Recursos relacionados

- [Documentación de Genie Agents (oficial)](https://docs.databricks.com/aws/en/genie-agents/)
- [Conceptos básicos de Unity Catalog (compartido)](../../../shared/fundamentals/unity-catalog-basics.md)
- [Hoja de referencia rápida de Unity Catalog (compartido)](../../../shared/cheat-sheets/unity-catalog-quick-ref.md)
- [Preguntas de práctica — Genie](../resources/practice-questions/04-genie-agents.md)

> [!note]
> Los datos de esta sección se contrastaron con la documentación de Genie Agents de Databricks el **26 de octubre de 2026**. Genie cambia rápidamente: consulta las [notas de versión](https://docs.databricks.com/aws/en/release-notes/product/) antes de confiar en un límite o en una etiqueta de la interfaz.

---

**[← Anterior: Análisis de consultas](../03-analyzing-queries/README.md) | [↑ Volver a Data Analyst Associate](../README.md) | [Siguiente: Descripción de la plataforma de Databricks →](../05-understanding-databricks-platform/README.md)**

---
tags: [exam-tips, data-analyst-associate, certification]
---

# Consejos y estrategias para el examen

Verificado con la guía del examen de octubre de 2025, la página de certificación y la documentación de Databricks el 26 de octubre de 2026.

## Formato del examen

| Elemento | Valor |
| :--- | :--- |
| Preguntas | 45 preguntas de opción múltiple con puntaje (más posibles preguntas sin puntaje) |
| Duración | 90 minutos |
| Puntaje de aprobación | No se publica: Databricks no publica los puntajes de aprobación |
| Código en las preguntas | SQL, compatible con ANSI |
| Modalidad | En línea con supervisión o en centro de pruebas |
| Tarifa | $200 |
| Repeticiones | Espere 14 días entre intentos; cada intento tiene el costo completo |
| Vigencia | 2 años; para recertificar, realice el examen vigente |

Fuentes: [guía del examen en PDF](https://www.databricks.com/sites/default/files/2025-10/databricks-certified-data-analyst-associate-oct-2025.pdf), [página de certificación](https://www.databricks.com/learn/certification/data-analyst-associate), [preguntas frecuentes sobre certificación](https://www.databricks.com/learn/certification/faq).

## Gestión del tiempo

| Tiempo | Actividad |
| :--- | :--- |
| 0–60 min | Primer recorrido (aprox. 1,3 min por pregunta) |
| 60–80 min | Revisión de las preguntas marcadas |
| 80–90 min | Comprobación final y envío |

### Estrategia para las preguntas

1. **Lea con atención** — vigile palabras como "BEST", "MOST", "NOT" y "EXCEPT"
2. **Identifique la restricción** — actualidad, costo, quién debe ver qué, copia frente a sin copia
3. **Marque y avance** — no dedique más de 2 minutos a una pregunta
4. **Elimine opciones** — reduzca a dos alternativas y luego decida

## Prioridad de los temas según su peso

| Prioridad | Dominio | Peso |
| :---: | :--- | :---: |
| 1 | Ejecución de consultas con Databricks SQL y SQL warehouses | 20 % |
| 2 | Creación de paneles y visualizaciones en Databricks | 16 % |
| 3 | Análisis de consultas | 15 % |
| 4 | Desarrollo, uso compartido y mantenimiento de AI/BI Genie Spaces | 12 % |
| 5 | Comprensión de Databricks Data + AI Platform | 11 % |
| 6 | Gestión de datos | 8 % |
| 7 | Protección de datos | 8 % |
| 8 | Importación de datos | 5 % |
| 9 | Modelado de datos con Databricks SQL | 5 % |

## Temas clave que debe dominar

### Conocimiento imprescindible

- [ ] Qué hace un SQL warehouse; serverless frente a pro frente a clásico (IWM solo está disponible en serverless; la federación requiere pro o serverless)
- [ ] Valores predeterminados de apagado automático: 10 minutos (serverless), 45 minutos (pro, clásico)
- [ ] Combinaciones (joins), incluidas semi/anti y claves compuestas, y `UNION` frente a `UNION ALL`
- [ ] `approx_count_distinct`, `GROUP BY` frente a `HAVING`, tratamiento de valores NULL
- [ ] Vista materializada frente a tabla de streaming frente a vista (dinámica)
- [ ] Viaje en el tiempo (`VERSION AS OF`, `TIMESTAMP AS OF`) y por qué `VACUUM` lo invalida
- [ ] Historial de consultas, perfil de consultas, Photon, caché de resultados y liquid clustering
- [ ] Paneles AI/BI: páginas, conjuntos de datos, widgets, programaciones, uso compartido e inserción
- [ ] Parámetros: marcadores con nombre `:name` (`{{ }}` heredado solo en el editor SQL heredado)
- [ ] Alertas SQL: consulta + condición + programación + destino
- [ ] Genie Spaces (ahora Genie Agents): curación, recursos de confianza, benchmarks y permisos
- [ ] Unity Catalog: namespace, privilegios, propiedad, filtros de filas y máscaras de columnas

### Conocimiento recomendado

- [ ] Databricks Assistant (ahora Genie Code): `/explain`, `/fix`, `/optimize`
- [ ] Lakehouse Federation: conexión → catálogo externo → consulta
- [ ] Catalog Explorer: tablas certificadas, etiquetas y linaje
- [ ] Importación: límites de carga desde la interfaz, `COPY INTO` (idempotente), Auto Loader, Delta Sharing y Marketplace
- [ ] Modelado de datos: estrella, copo de nieve, Data Vault y correspondencia con medallion

## Errores habituales del examen

| Tema | Trampa | Respuesta correcta |
| :--- | :--- | :--- |
| Tamaño del SQL warehouse frente a clústeres | "Un tamaño mayor soluciona las colas" | El tamaño reduce la latencia; más clústeres aumentan la concurrencia |
| Apagado automático | "El valor predeterminado es 120 minutos" | 10 minutos en serverless, 45 minutos en pro/clásico |
| Parámetros | `{{param}}` en un panel | Los paneles usan `:param`; mustache solo funciona en el editor heredado |
| `UNION` | Conserva los duplicados | `UNION` elimina los duplicados; `UNION ALL` los conserva |
| QUALIFY | Confundirla con HAVING | HAVING filtra grupos; QUALIFY filtra resultados de funciones de ventana |
| DROP TABLE | Igual para tablas administradas y externas | Administrada: se eliminan datos y metadatos; externa: solo metadatos |
| Viaje en el tiempo | El historial equivale a versiones legibles | `VACUUM` puede eliminar archivos que una versión antigua necesita |
| Tabla de streaming frente a MV | MV para archivos que llegan de forma continua | Tabla de streaming para cargas incrementales de solo adición; MV para consultas precalculadas |
| Filtros de filas en paneles | "Los filtros de filas no funcionan en los paneles" | Publique con permisos de datos individuales |
| `DENY` | "Use DENY en Unity Catalog" | Unity Catalog no tiene DENY; use `REVOKE` |
| Genie | "Los benchmarks mejoran las respuestas" | Los benchmarks solo evalúan la exactitud |
| Delta Sharing | Copia los datos | Comparte datos en vivo y de solo lectura sin copiarlos |

## Cifras de referencia rápida

| Elemento | Valor |
| :--- | :--- |
| Inicio de un SQL warehouse serverless | Aprox. de 2 a 6 segundos |
| Inicio de pro / clásico | Aprox. 4 minutos |
| Guía de concurrencia | Aprox. 1 clúster por cada 10 consultas simultáneas |
| Vigencia de una entrada de la caché de resultados | Hasta 24 horas (se invalida cuando cambian las tablas) |
| Retención del historial de tablas (`logRetentionDuration`) | 30 días |
| Umbral de `VACUUM` (`deletedFileRetentionDuration`) | 7 días |
| Carga desde la interfaz | Hasta 10 archivos, con un total inferior a 2 GB |
| Claves de liquid clustering | Hasta 4 |
| Etiquetas por objeto | Hasta 50 |
| Suscriptores de un panel por programación | Hasta 100 |

## Productos renombrados (nombre del examen → nombre actual)

| La guía del examen indica | Nombre actual (oct. de 2026) |
| :--- | :--- |
| Data Intelligence Platform | Data + AI Platform |
| Delta Live Tables | Lakeflow Spark Declarative Pipelines (Nov 12, 2025) |
| Databricks Assistant | Genie Code (Mar 11, 2026) |
| Genie spaces | Genie Agents (Jul 8, 2026) |
| Delta Sharing | OpenSharing (Jun 10, 2026) |

Responda con el término del examen; reconozca el nuevo nombre.

## Práctica con ejemplos reales

Use [Databricks Free Edition](https://docs.databricks.com/aws/en/getting-started/free-edition) — incluye computación serverless, el editor SQL, paneles y Genie. Community Edition ha sido retirada; Free Edition la reemplazó.

## Si no aprueba

- Revise su informe de resultados por dominio
- Comience por Ejecución de consultas (20 %) y Paneles (16 %)
- Resuelva las [preguntas de práctica](./practice-questions/README.md) correspondientes
- Espere 14 días antes de repetir el examen

[← Volver a recursos](./README.md)

---
title: Revisión final — Data Analyst Associate
type: final-review
tags:
  - data-analyst-associate
  - final-review
  - exam-morning
status: published
---

# Revisión final — Data Analyst Associate (repaso exprés de 20 minutos la mañana del examen)

> [!important]
> Repaso exprés para la mañana del examen. Las líneas que le resulten desconocidas indican el dominio que debe repasar más adelante.

## Datos que aparecen *con más frecuencia* y se aprenden en 2 minutos

- **Tres tipos de SQL warehouse**: serverless (inicio en 2–6 s, IWM, valor predeterminado de la interfaz), pro (Predictive I/O, inicio de ~4 min), clásico (inicio de ~4 min); Photon en todos; la federación requiere pro o serverless
- **Cachés de resultados**: local (por clúster) y remota (solo serverless, compartida entre SQL warehouses, persiste tras reinicios); se invalidan cuando cambian las tablas
- **AI/BI Dashboards** es el producto *actual* de creación de paneles (reemplaza a "Databricks SQL Dashboards")
- **AI/BI Genie Spaces** (renombrados como Genie Agents el 8 de julio de 2026; el examen sigue diciendo "spaces") generan SQL de solo lectura a partir de lenguaje natural y aplican los permisos de UC por usuario solicitante (los filtros de filas y las máscaras de columnas se aplican automáticamente)
- **Prioridades de curación de Genie** (según la práctica recomendada oficial): primero las expresiones SQL, después las consultas SQL de ejemplo, las instrucciones de texto como último recurso y los sinónimos como mecanismo complementario
- **`COPY INTO` es idempotente** (miles de archivos); Auto Loader para millones; la carga desde la interfaz admite CSV/TSV/JSON/Parquet/Avro/texto (límite de 10 archivos / 2 GB)
- **Lakehouse Federation** = consulta de solo lectura de bases de datos externas mediante un catálogo externo
- **Parámetros**: marcadores con nombre `:name`; `{{ }}` (mustache) solo funciona en el editor SQL heredado
- **No existe `DENY` en Unity Catalog** (solo en el `hive_metastore` heredado): elimine el acceso con **`REVOKE`**; las concesiones en un catálogo o esquema se aplican a los objetos actuales y futuros
- **Filtros de filas y máscaras de columnas** = UDF de SQL asociadas a una columna o tabla, aplicadas en tiempo de consulta
- **Vista materializada**: almacena el resultado y lo actualiza; **vista estándar**: se ejecuta al leerla
- El **esquema estrella** es el estándar en BI; el copo de nieve cuando importa la normalización

## Preguntas rápidas por dominio (5 minutos; 9 dominios)

### 01 — Ejecución de consultas con Databricks SQL y SQL warehouses (20 %)

- El **tamaño** del SQL warehouse determina la latencia; los **clústeres máximos** determinan la concurrencia (aprox. 1 clúster por cada 10 consultas simultáneas); apagado automático: 10 min en serverless / 45 min en pro y clásico
- Databricks Assistant (ahora **Genie Code**): `/explain`, `/fix` (diagnosticar error), `/optimize`
- `UNION` elimina los duplicados y `UNION ALL` los conserva; combine por todas las columnas clave
- `approx_count_distinct` (HLL++, ~5 %); perfil de datos del notebook / `dbutils.data.summarize` para estadísticas descriptivas
- **MV**: precalcula una consulta; **tabla de streaming**: agrega filas nuevas (`STREAM read_files`); **vista dinámica**: filtra por usuario en tiempo de lectura
- `CREATE OR REPLACE TABLE` conserva el historial y las concesiones; viaje en el tiempo con `VERSION AS OF` / `TIMESTAMP AS OF`, limitado por `VACUUM`
- Combinación federada: conexión → catálogo externo → consulta en un SQL warehouse pro o serverless

### 02 — Creación de paneles y visualizaciones (16 %)

- Un panel AI/BI = pestaña Data (varios conjuntos de datos) + páginas + widgets (visualizaciones, filtros, texto e imágenes)
- Parámetros: `:name`, selección múltiple `array_contains(:p, col)`, rango de fechas `:p.min` / `:p.max`; pruébelos escribiendo un valor y ejecutando
- **Alertas SQL**: consulta + condición (valor/agregación, operador, umbral) + programación + destino (creado por un administrador); estados OK / TRIGGERED / ERROR
- Publique con permisos de datos **compartidos** (credenciales del publicador, valor predeterminado) o **individuales**; los usuarios de la cuenta obtienen CAN RUN; la inserción externa usa una entidad de servicio

### 03 — Análisis de consultas (15 %)

- **Photon**: motor vectorizado predeterminado en los SQL warehouses; las UDF recurren a Spark; compruebe el % de Photon en el perfil de consultas
- **Historial de consultas** (filtros: usuario, fecha, cómputo, estado...) → **perfil de consultas** (filas/tiempo/memoria por operador; combinaciones que multiplican filas, escaneos completos) → conclusiones de rendimiento
- `DESCRIBE HISTORY` audita las escrituras; el viaje en el tiempo + `EXCEPT` permite comparar versiones
- **Liquid clustering**: `CLUSTER BY` sobre hasta 4 columnas de filtrado; reemplaza el particionamiento y `ZORDER`; `OPTIMIZE` lo aplica
- Casos clásicos de corrección de consultas: falta de `GROUP BY`, agregación en `WHERE` (→ `HAVING`), `= NULL` (→ `IS NULL`), combinación externa anulada por `WHERE`

### 04 — Desarrollo, uso compartido y mantenimiento de AI/BI Genie Spaces (12 %)

- Un Genie Space consta de datos de Unity Catalog curados (procure usar cinco tablas o menos; máximo 50) + base de conocimiento + SQL de ejemplo + funciones SQL + instrucciones, sobre un SQL warehouse pro o serverless
- Genie genera SQL → lo ejecuta en un SQL Warehouse → devuelve resultados. NO es que "el modelo lea los datos"
- **Genie respeta los permisos de Unity Catalog**: las máscaras de columnas y los filtros de filas se aplican por usuario; los consumidores necesitan `SELECT`, pero no permisos sobre el SQL warehouse
- **Recursos de confianza** = SQL de ejemplo parametrizado (`:param`) + funciones SQL de Unity Catalog → respuestas verificadas
- **Uso compartido**: `CAN MANAGE` / `CAN EDIT` / `CAN RUN` / `CAN VIEW`; distribución mediante enlace, inserción en iframe o integraciones con aplicaciones (Genie API)
- **Mantenimiento**: pestaña Monitor (preguntas y comentarios) → curar → actualizar metadatos de Unity Catalog → volver a ejecutar benchmarks (el modo Chat compara conjuntos de resultados; las columnas adicionales se consideran incorrectas)
- Las consultas generadas se atribuyen al usuario final en el historial de consultas y en la pestaña Monitor

### 05 — Comprensión de Databricks Data + AI Platform (11 %)

- Nombres del examen → actualidad: Delta Live Tables → Lakeflow Spark Declarative Pipelines; Data Intelligence Engine = DatabricksIQ; Mosaic AI bajo Agent Bricks
- Catalog Explorer: tablas administradas frente a externas, insignias de certificado/obsoleto (`system.certification_status`), pestaña Lineage (requiere `BROWSE`)
- Marketplace: conjuntos de datos, modelos, notebooks y aplicaciones; se entregan como catálogos compartidos mediante Delta Sharing (OpenSharing)

### 06 — Gestión de datos (8 %)

- Certifique con **Assign certification** o `SET TAG ON ...`; encuentre los objetos con `certificationStatus:certified`
- Etiquete con **Add tags** o `ALTER TABLE ... ALTER COLUMN ... SET TAGS`; requiere `APPLY TAG`
- Limpie con `DELETE ... WHERE`, `COALESCE`, `try_cast`, `DISTINCT`, `QUALIFY ROW_NUMBER() = 1`

### 07 — Protección de datos (8 %)

- `catalog.schema.table|view|volume`; los archivos de los volúmenes están en `/Volumes/<catalog>/<schema>/<volume>/`
- El propietario tiene todos los privilegios; `MANAGE` permite administrar sin ser propietario; el administrador de metastore es opcional; los datos de producción deben ser propiedad de grupos
- El filtro de filas (filas) y la máscara de columnas (valores) son UDF de SQL en la tabla; `is_account_group_member()` para grupos de Unity Catalog

### 08 — Importación de datos (5 %)

- Carga desde la interfaz: ≤ 10 archivos / < 2 GB → tabla Delta administrada; carga en volúmenes: cualquier formato, 5 GB por archivo
- S3 mediante ubicaciones externas o volúmenes; `COPY INTO` (idempotente) frente a Auto Loader (exactly-once, millones de archivos)
- Delta Sharing (OpenSharing): en vivo, de solo lectura y sin copia; Zerobus Ingest para envío por API; Marketplace para datos de terceros

### 09 — Modelado de datos con Databricks SQL (5 %)

- Bronze (sin procesar) / Silver (validado y limpio) / Gold (dimensional, medidas): los analistas consultan principalmente Gold
- Estrella = tabla de hechos + dimensiones desnormalizadas; copo de nieve = dimensiones normalizadas; Data Vault = hubs, links y satellites
- Data Vault / 3NF suelen estar en Silver; los esquemas estrella de Kimball, en Gold

## Recordatorios de trampas habituales

| Trampa | Respuesta correcta |
| :--- | :--- |
| "Sin copiar datos" + fuente externa que no es Delta | Lakehouse Federation |
| "Preguntas en lenguaje natural sobre tablas de Unity Catalog" | AI/BI Genie Spaces |
| "Genie ve PII que el usuario no debería ver" | Falso: los permisos de Unity Catalog se aplican al SQL de Genie |
| "Notificación por umbral en unos minutos" | Alerta SQL con destino webhook |
| "El panel tarda demasiado en cargar" | Vista materializada como tabla subyacente |
| "Primer mecanismo de curación para Genie" | Expresión SQL (no una instrucción de texto) |
| "Los benchmarks mejoran las respuestas de Genie" | Falso: los benchmarks solo evalúan la exactitud |
| "Oculte la columna para que nadie pueda leerla" | Ocultarla solo la elimina del contexto de Genie: use permisos de Unity Catalog o máscaras de columnas |
| "Si vuelvo a ejecutar la carga, ¿se duplicarán los datos?" | `COPY INTO` es idempotente: no |

## Examen de hoy: presupuesto de tiempo de 90 minutos

- **45 preguntas ÷ 90 minutos = 2 min por pregunta**: siga avanzando
- Recorra cada caso rápidamente para identificar la restricción (¿un solo usuario? ¿umbral? ¿costo? ¿actualidad?): eso determina la respuesta
- En las preguntas sobre la filosofía de Databricks, suele acertar la opción "más administrada / más gobernada"
- Atención a los productos renombrados: responda con el término del examen ("Delta Live Tables", "Databricks Assistant", "Genie spaces", "Delta Sharing"); hoy son Lakeflow Spark Declarative Pipelines, Genie Code, Genie Agents y OpenSharing

## Coma. Hidrátese. Respire.

El trabajo está hecho. Confíe en su preparación. Vaya a aprobarlo.

---

**[← Volver a recursos](./README.md)** | **[↑ Volver a Data Analyst Associate](../README.md)**

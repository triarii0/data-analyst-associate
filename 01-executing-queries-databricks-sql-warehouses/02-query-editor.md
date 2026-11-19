# Editor de consultas, asistente y ejecución

## 1. El Editor de Consultas SQL (SQL Editor)

El **SQL Editor** es el entorno de desarrollo integrado en **Databricks SQL** donde los analistas escriben, prueban, parametrizan y comparten consultas SQL contra tablas gestionadas en el Lakehouse.

### Funcionalidades principales que evalúa el examen:

* **Explorador de Esquemas (Schema Browser):** Permite navegar por la jerarquía de **Unity Catalog** (`Catálogo` -> `Esquema/Base de datos` -> `Tabla/Vista`), previsualizar muestras de datos, ver tipos de columnas e insertar nombres directamente en el editor.
* **Pestañas múltiples y autoguardado:** Permite trabajar en varias consultas a la vez y mantiene un historial de versiones para recuperar cambios anteriores.
* **Selector de Contexto:** En la parte superior del editor debes elegir:
  1. El **SQL Warehouse** que ejecutará la consulta.
  2. El **Catálogo** y el **Esquema** por defecto (evita tener que escribir siempre rutas completas de 3 niveles `catalogo.esquema.tabla`).
* **Límite de resultados en pantalla:** Por defecto, la interfaz web muestra hasta **1,000 filas** (o un límite configurable de descarga como 64,000/100,000 filas en CSV/Excel) para no saturar el navegador, aunque la consulta completa se procese en el warehouse.

### Funciones del editor

**Resaltado de sintaxis**: codificación automática de colores para las palabras clave de SQL

```sql
SELECT      -- Blue (keyword)
    id,     -- White (identifier)
    name,   -- White (identifier)
    100     -- Green (number)
FROM users;
```

**Autocompletado**: pulsa Ctrl+Space para ver sugerencias

```sql
SELECT * FROM sales WHERE [Ctrl+Space]
-- Suggests: date_created, status, amount, customer_id, etc.
```

**Plantillas de consultas**: patrones predefinidos para tareas habituales

### Editor de múltiples sentencias

Ejecuta varias sentencias SQL en secuencia:

```sql
-- Statement 1: Create a temporary view
CREATE TEMPORARY VIEW active_users AS
SELECT * FROM users WHERE status = 'active';

-- Statement 2: Query the view
SELECT COUNT(*) as active_count FROM active_users;

-- Statement 3: Clean up (optional)
DROP VIEW active_users;
```

### Comentarios

```sql
-- Single-line comment
/*
   Multi-line comment
   Can span multiple lines
   Useful for documentation
*/

SELECT
    id,           -- Customer ID
    name,         -- Full name
    email         /* Email address
                     Primary contact */
FROM customers;
```

Ejecutar consultas

1. **Ejecutar la consulta completa**: Ctrl+Enter o Cmd+Enter
2. **Ejecutar el texto seleccionado**: selecciona un bloque de código + Ctrl+Enter
3. **Ejecutar la línea**: sitúa el cursor en la línea + Ctrl+Enter
4. **Detener la ejecución**: haz clic en el botón Detener o pulsa Ctrl+Alt+C

### Información de ejecución

```sql
-- View query execution details
SELECT * FROM system.query.history
WHERE statement_id = 'abc123'
LIMIT 1;

-- Returns columns such as executed_by, compute.warehouse_id, total_duration_ms,
--          produced_rows, start_time, end_time
```


## 2. Parámetros de Consulta (Query Parameters)

Los parámetros permiten hacer consultas dinámicas e interactivas sin modificar el código SQL subyacente. Son fundamentales tanto en el editor como al crear **AI/BI Dashboards** y **Alertas**.

* **Sintaxis:** Se definen usando dobles llaves `{{ nombre_parametro }}` (o sintaxis de parámetros nombrados `:nombre_parametro` en versiones más recientes de Databricks SQL).
* **Tipos de parámetros:** Texto (*Text*), Número (*Number*), Fecha (*Date*), Rango de fechas (*Date Range*), Lista desplegable (*Dropdown List*) y Lista desplegable basada en consulta (*Query Based Dropdown List*).
* **Seguridad y rendimiento:**
  * Los valores introducidos por el usuario se inyectan de forma segura (previenen *SQL Injection*).
  * Al cambiar un parámetro en un dashboard, se vuelve a ejecutar la consulta usando el nuevo valor.

> **Pregunta típica de examen:** *¿Cuál es la ventaja de usar un "Query Based Dropdown List" frente a una lista estática?*
> **Respuesta:** Los valores disponibles en el filtro se actualizan dinámicamente a partir de los datos reales de una tabla (por ejemplo, nuevos productos o regiones añadidos recientemente) sin intervención manual.

```sql
SELECT *
FROM sales
WHERE region = :region             -- String: Databricks adds the quotes
  AND order_date >= :start_date;   -- Date
```


## 3. Databricks Assistant (Genie Code)

**Databricks Assistant** es un asistente conversacional integrado directamente en el SQL Editor, Notebooks y Catalog Explorer que utiliza los metadatos de **Unity Catalog** para entender el contexto de tus datos.

### Casos de uso clave en el examen:

1. **Generación de código SQL (Text-to-SQL):** Escribes una instrucción en lenguaje natural (ej. *"Muéstrame las ventas totales por región del último trimestre"*) y genera la consulta usando los nombres exactos de tus tablas y columnas.
2. **Explicación de consultas complejas:** Seleccionas una consulta larga (con múltiples CTEs o *Window Functions*) y le pides que explique qué hace paso a paso.
3. **Depuración y corrección de errores (*Fix Error*):** Cuando una consulta falla por un error de sintaxis, tipo de dato o nombre de columna inexistente, aparece un botón para que el asistente diagnostique el fallo y proponga la corrección exacta.
4. **Optimización de consultas:** Puede sugerir mejoras de rendimiento (como filtrar particiones antes de hacer un `JOIN` o evitar `SELECT *`).

> **Nota de privacidad/gobernanza:** Databricks Assistant respeta los permisos de **Unity Catalog**; solo sugiere tablas y metadatos a los que el usuario tiene acceso autorizado.

| Comando/acción               | Qué hace                                                                           |
| :---------------------------- | :---------------------------------------------------------------------------------- |
| `/explain`                    | Explica el código de una celda o de una consulta, paso a paso                      |
| `/fix`                        | Propone correcciones de errores de código en una vista de diferencias (diff)       |
| **Diagnose error**            | Botón de una celda fallida; ejecuta`/fix` automáticamente                         |
| **Quick Fix**                 | Sugiere una corrección de una línea para errores simples («Aceptar y ejecutar») |
| `/optimize`                   | Sugiere mejoras de rendimiento para consultas SQL                                   |
| `/doc`                        | Añade comentarios al código                                                       |
| `/prettify`                   | Formatea SQL para mejorar su legibilidad                                            |
| `/findTables`, `/findQueries` | Busca tablas o consultas relevantes usando metadatos de Unity Catalog               |


## 4. Ciclo de Ejecución de una Consulta y Caché

Comprender qué ocurre desde que pulsas **Run** hasta que ves los resultados es uno de los temas más preguntados en la certificación.

### Fases de ejecución (visibles en el *Query Profile*):

1. **Queued (En cola):** La consulta espera recursos disponibles en el SQL Warehouse. *(Si este tiempo es alto, falta escalado horizontal / Multi-cluster)*.
2. **Compiling / Planning (Compilación):** El optimizador **Catalyst** analiza la sintaxis, verifica permisos en Unity Catalog y genera el plan lógico y físico de ejecución.
3. **Executing (Ejecución):** Los nodos ejecutores leen los datos de almacenamiento, aplican filtros/agregaciones usando el motor **Photon** y devuelven el resultado.
4. **Fetching Results (Recuperación):** El driver envía los resultados finales a la interfaz del usuario o herramienta BI.

### Niveles de Caché en Databricks SQL (¡Muy importante!):

Databricks utiliza **dos mecanismos distintos de caché** para acelerar ejecuciones repetidas:


| Tipo de Caché                       | Dónde se almacena                                                   | Cuándo se activa                                                                                                           | Cuándo se invalida                                                                           |
| :----------------------------------- | :------------------------------------------------------------------- | :-------------------------------------------------------------------------------------------------------------------------- | :-------------------------------------------------------------------------------------------- |
| **Query Result Cache**               | En el plano de control / memoria del servicio SQL                    | Cuando se ejecuta **exactamente la misma consulta SQL** (mismo texto, mismos parámetros) por el mismo o distinto usuario. | Si los datos subyacentes de alguna tabla cambiaron o pasan **24 horas**.                       |
| **Disk Cache** *(antes Delta Cache)* | En los discos **SSD locales** de los nodos trabajadores del Warehouse | Cuando diferentes consultas leen los **mismos archivos Parquet/Delta** desde el almacenamiento en la nube (S3, ADLS, GCS).   | Si los archivos subyacentes se modifican/eliminan o si el SQL Warehouse se **detiene (Stop)**. |

> **Regla de oro para el examen:**
>
> * Si ejecutas la **misma consulta** dos veces seguidas sin cambios en los datos: actúa la **Query Result Cache** (tiempo de ejecución casi 0 ms).
> * Si detienes y reinicias el SQL Warehouse: la **Disk Cache** se pierde porque los discos SSD efímeros se liberan, pero la **Query Result Cache** sigue disponible si los datos no cambiaron.



## 5. Diagnóstico con el Query Profile

El **Query Profile** es la herramienta visual de depuración accesible desde el *Query History* o debajo de los resultados en el SQL Editor. Sirve para identificar cuellos de botella en consultas lentas:

* **Metrics clave a vigilar:**
  * **Bytes spilled to disk (Desbordamiento a disco):** Indica que la operación (ej. un `JOIN` o `GROUP BY` masivo) superó la memoria RAM disponible y tuvo que escribir datos temporales en disco, degradando drásticamente el rendimiento. **Solución:** Aumentar el tamaño del clúster (*Scale Up*).
  * **Data Skew (Sesgo de datos):** Ocurre cuando una sola tarea (*Task*) tarda mucho más que el resto porque una clave de partición o `JOIN` concentra la mayoría de los registros (ej. valores `NULL`).
  * **Pruning (Poda de archivos/particiones):** Muestra cuántos archivos se omitieron gracias a las estadísticas de Delta Lake (`Data Skipping`) o particiones, evitando leer toda la tabla.
  * **Photon Acceleration:** Muestra qué porcentaje de los operadores de la consulta fueron ejecutados por el motor vectorizado Photon frente a la JVM tradicional.

### Sugerencias de optimización

```sql
-- Query plan analysis
EXPLAIN SELECT * FROM sales WHERE status = 'active';

-- Output:
--   Filter [status#1 = 'active']
--   └─ Scan parquet [id#0, status#1, amount#2]
--      PushedFilters: [Status = 'active']
--      Metrics: 150 rows, 2.3 MB
```

### Consejos de rendimiento

Problema                 | Causa                                                           | Solución                                                            |
| ------------------------ | --------------------------------------------------------------- | -------------------------------------------------------------------- |
| Consulta lenta           | Escaneo completo de la tabla                                    | Añadir un filtro WHERE                                              |
| Unión lenta             | Join que dispara el número de filas o falta de clave de unión | Revisar el número de filas por operador en el perfil de la consulta |
| Error OOM                | Demasiados datos en memoria                                     | Añadir LIMIT, particionar, muestrear                                |
| Tiempo de espera agotado | Consulta demasiado compleja                                     | Dividir en pasos, materializar resultados intermedios                |


## 6. Compartir, Programar y Permisos en el Editor

* **Permisos de una consulta (ACL):**
  * **Can Run:** El usuario puede ver la consulta, cambiar parámetros y ejecutarla usando su propio warehouse o el asignado.
  * **Can Edit:** Permite modificar el código SQL, guardar cambios y cambiar el título/descripción.
  * **Can Manage:** Control total, incluido cambiar permisos y eliminar la consulta.
* **Credenciales de ejecución (*Run as*):**
  * **Run as Viewer (Ejecutar como espectador):** La consulta se ejecuta usando los permisos de Unity Catalog de la persona que la está viendo. *(Opción recomendada por seguridad)*.
  * **Run as Owner (Ejecutar como propietario):** La consulta se ejecuta con los permisos de quien creó la consulta, permitiendo que usuarios sin acceso directo a las tablas base vean resultados agregados.
* **Query Schedules (Programación):** Permite configurar ejecuciones periódicas (cada hora, día, semana) para mantener actualizada la caché de resultados o alimentar procesos posteriores.

### Compartir consultas

```yaml
Niveles de acceso:
  Owner: Acceso completo de edición/eliminación
  Editor: Puede modificar y ejecutar
  Viewer: Solo puede ver y ejecutar

Compartir con:
  - Usuarios individuales
  - Grupos
  - Equipos
  - Todos (toda la organización)
```

### Historial de consultas

#### Navegación: Editor SQL → barra lateral Historial

```sql
-- Query history stored in system table
SELECT
    statement_id,
    executed_by,
    statement_text,
    statement_type,      -- SELECT, INSERT, UPDATE, etc.
    total_duration_ms,
    produced_rows,
    execution_status,
    error_message,
    compute.warehouse_id,
    start_time,
    end_time
FROM system.query.history
WHERE start_time > CURRENT_TIMESTAMP - INTERVAL 7 DAYS
ORDER BY start_time DESC;
```


## 7. Notebook frente a editor de consultas

### Editor de consultas

- **Ideal para**: consultas SQL ad hoc, análisis rápidos
- **Interfaz**: basada en web, centrada en SQL
- **Resultados**: visualizaciones, gráficos
- **Uso compartido**: enlaces de consulta compartibles
- **Colaboración**: comentarios en las consultas

```text
Editor de consultas
├─ Un solo archivo = consultas SQL
├─ Visualización automática
├─ Ejecución rápida
└─ Gestión de resultados
```

### Notebook SQL

- **Ideal para**: flujos de trabajo complejos, documentación
- **Compatibilidad**: celdas SQL + Python
- **Control**: más granular, paso a paso
- **Control de versiones**: posible integración con Git
- **Formato**: admite celdas Markdown

```text
Notebook SQL
├─ Varias celdas
├─ SQL + Markdown + Python
├─ Documentación enriquecida
└─ Historial de versiones
```


## 8. Ejecución de consultas mediante API

### Python: SqlAlchemy

```python
from sqlalchemy import create_engine, text

engine = create_engine(
    "databricks:///?http_path=/sql/1.0/warehouses/abc123&"
    "catalog=main&schema=analytics"
)

with engine.connect() as conn:
    result = conn.execute(text("""
        SELECT * FROM sales
        WHERE amount > :min_amount
    """), min_amount=1000)

    for row in result:
        print(row)
```

### API REST

```bash
# Execute SQL query via REST API

curl -X POST \
  https://dbc-abc123.cloud.databricks.com/api/2.0/sql/statements \
  -H "Authorization: Bearer dapi..." \
  -d '{
    "warehouse_id": "abc123",
    "statement": "SELECT COUNT(*) FROM sales"
  }'

# Response includes execution_id for polling
# {"statement_id": "xyz789", "state": "RUNNING", ...}

```


## 9. Preguntas Tipo Examen (Autoevaluación)

**Pregunta 1:** *Un analista ejecuta una consulta compleja que tarda 45 segundos. Inmediatamente después, un compañero ejecuta exactamente la misma consulta sin cambiar ningún parámetro ni actualizar las tablas subyacentes, y obtiene el resultado en menos de 1 segundo. ¿Qué mecanismo explica esta mejora?*

* **Respuesta:** **Query Result Cache**, ya que el texto de la consulta es idéntico y los datos subyacentes no han sufrido modificaciones.

**Pregunta 2:** *Un equipo de marketing necesita ver un informe de ventas agregadas por país, pero por políticas de cumplimiento no tienen permisos `SELECT` sobre la tabla detallada de clientes (`sales.transactions`). ¿Cómo puede el analista compartir la consulta para que puedan ejecutarla de forma segura?*

* **Respuesta:** Compartir la consulta con permiso **Can Run** y configurarla con la opción **Run as Owner** (Ejecutar como propietario).

**Pregunta 3:** *Al revisar el Query Profile de una consulta con múltiples `JOIN`, notas que el tiempo de ejecución es alto debido a una gran cantidad de "Bytes spilled to disk". ¿Qué indica esta métrica?*

* **Respuesta:** Los nodos trabajadores del SQL Warehouse no tienen suficiente memoria RAM para procesar la operación en memoria y están escribiendo datos intermedios en el disco local.

**Pregunta 4:** *Un analista recibe un error de sintaxis al escribir una función de ventana compleja en el SQL Editor y no recuerda el nombre exacto de dos columnas en una tabla de Unity Catalog. ¿Qué herramienta integrada debe usar para resolverlo rápidamente sin salir del editor?*

* **Respuesta:** Databricks Assistant, utilizando su capacidad de corrección de errores (Fix error) y consulta de metadatos contextuales de Unity Catalog.


## 10. Problemas y errores comunes

### Resultados de la consulta truncados

**Escenario:** El editor de consultas muestra resultados parciales en conjuntos de resultados grandes.
**Solución:** El editor limita las filas mostradas (1000 por defecto). Usa `LIMIT` para controlar la salida o exporta los resultados completos a un archivo.


## 11. Consejos para el examen

- La sintaxis de parámetros es un marcador con nombre, `:parameter_name`; `{{ }}` solo funciona en el editor heredado
- Databricks Assistant (ahora Genie Code): `/explain` explica, `/fix` repara errores, `/optimize` ajusta el rendimiento
- Ten en cuenta que Ctrl+Enter (Cmd+Enter en macOS) ejecuta la consulta completa o el texto seleccionado
- Las cachés de resultados conservan entradas hasta 24 horas y se invalidan cuando cambian las tablas subyacentes
- El historial de consultas de toda la cuenta está en la tabla del sistema `system.query.history` (accesible por defecto para administradores)


## 12. Puntos clave

- **Editor de consultas**: IDE web para desarrollo SQL con visualización
- **Parámetros**: consultas dinámicas mediante marcadores con nombre (`:parameter_name`)
- **Asistente**: `/explain`, `/fix`, `/optimize` — ahora denominado Genie Code
- **Autocompletado**: sugerencias de tablas, columnas y funciones
- **Ejecución**: Ctrl+Enter ejecuta la consulta y los resultados se visualizan automáticamente
- **Historial de consultas**: interfaz por workspace; `system.query.history` para análisis SQL
- **Visualización**: sugerida automáticamente según la forma de los resultados (tabla, gráfico, etc.)
- **Uso compartido**: consultas compartibles con controles de acceso detallados
- **API**: programable con Python SqlAlchemy, la API REST o la CLI


## 13. Temas relacionados

- [SQL Essentials](../../../shared/fundamentals/sql-essentials.md) - Conceptos esenciales de SQL usados en el editor de consultas
- [SQL Functions Cheat Sheet](../../../shared/cheat-sheets/sql-functions.md) - Guía rápida de funciones SQL habituales
- [Dashboard and Query Parameters](../02-creating-dashboards-and-visualizations/05-parameters.md) - Análisis detallado de las consultas parametrizadas
- [Photon and Query Performance](../03-analyzing-queries/01-photon-and-query-performance.md) - Historial de consultas, perfil de consulta y caché


## 14. Documentación oficial

- [Escribir consultas y explorar datos en el editor de SQL](https://docs.databricks.com/aws/en/sql/user/sql-editor/)
- [Trabajar con parámetros de consulta](https://docs.databricks.com/aws/en/sql/user/queries/query-parameters)
- [Sintaxis de parámetros mustache (heredada)](https://docs.databricks.com/aws/en/sql/user/sql-editor/mustache-parameters)
- [Obtener ayuda para programar con Genie Code](https://docs.databricks.com/aws/en/notebooks/code-assistant)

---

**[← Anterior: SQL Warehouses](./01-sql-warehouses.md) | [↑ Volver a la sección](./README.md) | [Siguiente: JOIN y operaciones de conjuntos →](./03-joins-and-set-operations.md)**

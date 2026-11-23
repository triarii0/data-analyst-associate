# Parámetros de paneles y consultas
---

## 1. ¿Qué son los Parámetros de Consulta (Query Parameters)?

Los **Parámetros de Consulta** permiten insertar valores dinámicos dentro de una sentencia SQL en tiempo de ejecución sin necesidad de modificar el código subyacente. Actúan como marcadores de posición (*placeholders*) que se sustituyen de forma segura antes de compilar y ejecutar la consulta en el SQL Warehouse.

```mermaid
flowchart LR
    U["<b>Usuario / Viewer</b><br/>Selecciona valor en UI<br/>(ej. Region = 'EMEA')"] --> P["<b>Parámetro de Consulta</b><br/>(:region o {{ region }})"]
    P --> W["<b>SQL Warehouse</b><br/>Ejecuta WHERE region = 'EMEA'<br/>(Poda de archivos / Data Skipping)"]
    W --> R["<b>Resultado Filtrado</b><br/>Devuelve solo las filas<br/>relevantes al panel"]

    style U fill:#2b5c8f,color:#fff
    style P fill:#ef6c00,color:#fff
    style W fill:#2e7d32,color:#fff
    style R fill:#6a1b9a,color:#fff
```

### Dos sintaxis soportadas en Databricks SQL (¡Atención en el examen!)
1. **Parámetros Nombrados (`:nombre_parametro`):**
   * Es la sintaxis moderna recomendada en Databricks SQL y **AI/BI Dashboards**.
   * Funciona como parámetros enlazados (*bind parameters*), protegiendo automáticamente contra inyección SQL y manejando tipos de datos de forma nativa.
   ```sql
   SELECT order_id, customer_id, amount
   FROM sales.gold.orders
   WHERE region = :selected_region
     AND order_date >= :start_date;
   ```
2. **Sintaxis Mustache (`{{ nombre_parametro }}`):**
   * Es la sintaxis clásica del SQL Editor y paneles heredados (*Legacy Dashboards*), aún soportada para compatibilidad.
   ```sql
   SELECT order_id, customer_id, amount
   FROM sales.gold.orders
   WHERE region = '{{ selected_region }}'
     AND order_date >= '{{ start_date }}';
   ```


## 2. Tipos de Parámetros Disponibles

Al definir un parámetro en el SQL Editor o en la pestaña `Data` de un AI/BI Dashboard, puedes elegir su tipo de dato y comportamiento:

| Tipo de Parámetro | Descripción y comportamiento | Ejemplo de uso |
| :--- | :--- | :--- |
| **Text (Texto)** | Entrada de texto libre. En sintaxis Mustache requiere comillas simples `'{{ param }}'`; en sintaxis nombrada `:param` **no** lleva comillas. | Buscar por código de producto o correo. |
| **Number (Número)** | Acepta valores enteros o decimales. | Filtrar pedidos con `amount > :min_amount`. |
| **Date / Date and Time** | Selector de calendario para una fecha u hora exacta. | `WHERE event_date = :fecha_corte` |
| **Date Range (Rango de fechas)** | Genera automáticamente dos sub-parámetros: `.start` y `.end` (ej. `:rango.start` y `:rango.end` o `{{ rango.start }}`). | `WHERE order_date BETWEEN :rango.start AND :rango.end` |
| **Dropdown List (Lista estática)** | Lista desplegable definida manualmente por el analista (una opción por línea). | Estados fijos: `'PENDING'`, `'SHIPPED'`, `'DELIVERED'`. |
| **Query Based Dropdown List** | Lista desplegable dinámica alimentada por el resultado de **otra consulta SQL**. | Lista actualizada de países o categorías activas en la tabla de dimensiones. |


## 3. Parámetros vs. Filtros de Dashboard (¡Diferencia Crítica de Examen!)

En un **AI/BI Dashboard**, existen dos formas de permitir que el usuario filtre los datos. El examen evalúa cuándo elegir cada una según el **volumen de datos** y el **rendimiento**:

| Criterio | Parámetros de Consulta (*Dataset Parameters*) | Filtros de Dashboard (*Canvas Filters*) |
| :--- | :--- | :--- |
| **Dónde se aplican** | **En el SQL Warehouse** (dentro de la consulta SQL, antes de traer datos). | **En el navegador / caché del panel** (sobre el resultado ya devuelto por la consulta). |
| **Impacto en cómputo** | Cada cambio de valor lanza una **nueva ejecución SQL** (si no está en Query Result Cache). | **Instantáneo (0 coste de cómputo)** mientras los datos ya estén cargados en el dataset. |
| **Volumen de datos ideal** | **Tablas masivas** (cientos de millones o billones de filas) donde necesitas *Partition Pruning* / *Data Skipping*. | **Datasets pequeños o medianos** (ej. < 100,000 filas preagregadas) para exploración rápida. |
| **Uso con `GROUP BY` complejo** | Permite filtrar filas **antes** de realizar agregaciones pesadas (`WHERE`). | Filtra las filas **después** de que la consulta SQL ya haya agregado los datos. |
| **Afectar múltiples datasets** | Un mismo widget de filtro en el *Canvas* puede vincularse simultáneamente a parámetros de **múltiples datasets** distintos. | Un filtro de campo (*Field Filter*) puede aplicarse a varios datasets si comparten el nombre/tipo de columna. |


## 4. Selección Múltiple (*Multi-select*) en Parámetros

Cuando necesitas permitir que el usuario elija **varios valores a la vez** (ej. `Region IN ('EMEA', 'AMER')`):

* **Con sintaxis nombrada (`:param`):** Se utiliza la función `ARRAY_CONTAINS(:param, columna)` o `columna IN (:param)` (cuando el parámetro está configurado como multiselección).
  ```sql
  SELECT *
  FROM sales.gold.orders
  WHERE ARRAY_CONTAINS(:selected_regions, region);
  ```
* **Con sintaxis Mustache (`{{ param }}`):** Al marcar la casilla *Allow multiple values*, Databricks envuelve cada valor seleccionado entre comillas simples y los separa por comas automáticamente, por lo que en el SQL se escribe **sin comillas externas**:
  ```sql
  SELECT *
  FROM sales.gold.orders
  WHERE region IN ({{ selected_regions }});
  ```

## 5. Herencia: sintaxis mustache

> [!warning] Solo heredado
> Los parámetros mustache funcionan **solo en el editor de SQL heredado**. AI/BI dashboards no los admite, y una consulta mustache copiada en un cuaderno, en el conjunto de datos de un panel o en un Genie Agent debe convertirse a marcadores con nombre antes de ejecutarse. Databricks recomienda usar marcadores con nombre en todas las consultas nuevas.

| Heredado (mustache) | Actual (marcador con nombre) |
| :--- | :--- |
| `WHERE region = '{{ region }}'` | `WHERE region = :region` |
| `WHERE order_date = '{{ date_param }}'` | `WHERE order_date = :date_param` |
| `WHERE status IN ({{ statuses }})` (selección múltiple con una opción de comillas) | `WHERE array_contains(:statuses, status)` |

Los tipos de widget heredados eran Text, Number, Dropdown List, Query-Based Dropdown List y Date and Time.


## 6. Limitaciones y Trampas de Examen

1. **Alertas SQL y Parámetros:**
   * Las **Alertas en Databricks SQL NO admiten consultas con parámetros** (porque se ejecutan en segundo plano sin intervención humana). Si intentas crear una alerta sobre una consulta parametrizada, Databricks mostrará un aviso/error.
2. **Query Result Cache con Parámetros:**
   * La caché de resultados almacena un resultado distinto **por cada combinación única de valores de parámetros**. Si un usuario vuelve a seleccionar `Region = 'EMEA'` y `Year = 2026` dentro de las 24 horas siguientes (sin cambios en las tablas), el resultado se sirve desde la **Query Result Cache** al instante.
3. **Valores por defecto al publicar:**
   * Antes de publicar un AI/BI Dashboard, debes dejar seleccionados unos **valores por defecto** coherentes en los parámetros para que, al abrir el panel por primera vez, todos los gráficos carguen correctamente sin errores de parámetro vacío.
4. **Seguridad frente a SQL Injection:**
   * Los parámetros tipados (`Number`, `Date`, `Dropdown List` y parámetros nombrados `:param`) impiden que un usuario escriba código SQL malicioso dentro de una caja de texto.


## 7. Preguntas Tipo Examen (Autoevaluación)

**Pregunta 1:** *Un AI/BI Dashboard consulta una tabla transaccional de 2,000 millones de filas particionada por `country` y `event_date`. Actualmente, el dataset trae todos los países y luego usa un filtro visual en el Canvas, lo que provoca lentitud y errores de límite de filas. ¿Cómo debes rediseñar el panel para optimizarlo?*
* **Respuesta:** Reemplazar el filtro visual del Canvas por **Parámetros de Consulta (`:country` y `:date_range`) en la cláusula `WHERE` del Dataset**, de modo que el SQL Warehouse aplique *Data Skipping* y solo lea las particiones necesarias.

**Pregunta 2:** *Tienes un dashboard con 4 gráficos diferentes que provienen de 4 datasets SQL distintos (`ventas`, `devoluciones`, `marketing`, `inventario`). Todos necesitan filtrarse por el mismo selector de `Departamento` en la parte superior del panel. ¿Qué debes configurar?*
* **Respuesta:** Crear un parámetro `:departamento` en cada uno de los 4 datasets y **vincular todos ellos a un único widget de filtro/parámetro** en la pestaña *Canvas*.

**Pregunta 3:** *Un analista intenta configurar una Alerta SQL programada cada hora utilizando una consulta existente que tiene el parámetro `:umbral_ventas`. Sin embargo, la interfaz no le permite completar la alerta. ¿Por qué?*
* **Respuesta:** Porque las **Alertas de Databricks SQL no soportan consultas con parámetros**; el umbral debe definirse como un valor fijo en la consulta o dentro de la propia condición de disparo de la alerta.

**Pregunta 4:** *El equipo de ventas añade nuevos representantes comerciales cada semana a la tabla `dim_sales_reps`. Quieres que el filtro de representante comercial en tu consulta muestre automáticamente los nuevos nombres sin tener que editar manualmente la consulta. ¿Qué tipo de parámetro debes elegir?*
* **Respuesta:** Un **Query Based Dropdown List** (lista desplegable basada en consulta) ejecutando `SELECT DISTINCT rep_name FROM dim_sales_reps ORDER BY 1`.


## 8. Herencia: sintaxis mustache

> [!warning] Solo heredado
> Los parámetros mustache funcionan **solo en el editor de SQL heredado**. AI/BI dashboards no los admite, y una consulta mustache copiada en un cuaderno, en el conjunto de datos de un panel o en un Genie Agent debe convertirse a marcadores con nombre antes de ejecutarse. Databricks recomienda usar marcadores con nombre en todas las consultas nuevas.

| Heredado (mustache) | Actual (marcador con nombre) |
| :--- | :--- |
| `WHERE region = '{{ region }}'` | `WHERE region = :region` |
| `WHERE order_date = '{{ date_param }}'` | `WHERE order_date = :date_param` |
| `WHERE status IN ({{ statuses }})` (selección múltiple con una opción de comillas) | `WHERE array_contains(:statuses, status)` |

Los tipos de widget heredados eran Text, Number, Dropdown List, Query-Based Dropdown List y Date and Time.


## 9. Casos de uso

- **Filtrado autoservicio**: un selector de región y un rango de fechas en un panel de ventas.
- **Selector de métrica**: un parámetro `IDENTIFIER(:metric_column)` permite a los espectadores elegir qué medida representar en el gráfico.
- **Migración**: convertir las consultas heredadas `{{ }}` antes de trasladarlas a paneles o a Genie.


## 10. Problemas y errores comunes

### La consulta copiada del editor heredado falla

**Escenario:** Una consulta con `'{{ region }}'` produce un error en el conjunto de datos de un panel.
**Solución:** conviértala a `:region` y elimine las comillas.

### La selección múltiple no devuelve resultados

**Escenario:** `WHERE category IN (:categories)` no devuelve filas cuando se seleccionan varios valores.
**Solución:** un parámetro de selección múltiple es una matriz. Use `array_contains(:categories, category)`.


## 11. Consejos para el examen

- La sintaxis actual es `:name`; `{{ name }}` solo se usa en el editor heredado
- Multi-select → `array_contains(:param, col)`; date range → `:param.min` / `:param.max`
- Pruebe escribiendo un valor debajo del nombre del parámetro y ejecutando; ese valor pasa a ser el predeterminado
- Prefiera los filtros de campo para el filtrado simple y en cascada


## 12. Conclusiones clave

- **Marcadores con nombre** en todas partes: editor de SQL, paneles, cuadernos, Genie
- Los valores **String** se entrecomillan automáticamente
- Los **widgets de filtro** pasan las elecciones del espectador a los parámetros
- La sintaxis **mustache heredada** debe convertirse antes de usarla fuera del editor heredado


## 13. Temas relacionados

- [Editor de consultas, asistente y ejecución](../01-executing-queries-databricks-sql-warehouses/02-query-editor.md) - Parámetros en el editor de SQL
- [AI/BI dashboards y diseño de paneles](./01-dashboards.md) - Conjuntos de datos, páginas y filtros
- [Ajuste de Genie Agents](../04-developing-sharing-maintaining-genie-spaces/02-tuning-genie-spaces.md) - Consultas de ejemplo parametrizadas en Genie


## 14. Documentación oficial

- [Trabajar con parámetros de consulta](https://docs.databricks.com/aws/en/sql/user/queries/query-parameters)
- [Trabajar con parámetros de panel](https://docs.databricks.com/aws/en/dashboards/parameters)
- [Sintaxis de parámetros mustache (heredada)](https://docs.databricks.com/aws/en/sql/user/sql-editor/mustache-parameters)

---

**[← Anterior: Uso compartido y colaboración](./04-sharing-collaboration.md) | [↑ Volver a la sección](./README.md)**

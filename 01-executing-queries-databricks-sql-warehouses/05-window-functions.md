# Funciones de ventana y análisis

## 1. ¿Qué es una Función de Ventana (Window Function)?
Una **función de ventana** realiza un cálculo sobre un conjunto de filas relacionadas con la fila actual (llamado *ventana* o *partición*), **sin colapsar ni agrupar las filas** como ocurre con `GROUP BY`. Cada fila conserva su identidad y detalle individual.

### Anatomía de la sintaxis `OVER()`

```sql
funcion_ventana() OVER (
    PARTITION BY columna_grupo      -- 1. Divide los datos en grupos independientes (opcional)
    ORDER BY columna_orden ASC/DESC -- 2. Ordena las filas dentro de cada partición (opcional/obligatorio según función)
    ROWS BETWEEN ... AND ...        -- 3. Define el marco deslizante de filas (Window Frame, opcional)
)
```

```mermaid
flowchart LR
    T["Tabla completa"] --> P["<b>1. PARTITION BY</b><br/>Divide en grupos aislados"]
    P --> O["<b>2. ORDER BY</b><br/>Ordena filas dentro de cada grupo"]
    O --> F["<b>3. WINDOW FRAME</b><br/>Delimita qué filas entran en el cálculo"]
    F --> R["<b>4. RESULTADO</b><br/>Mantiene el mismo número de filas"]

    style P fill:#2b5c8f,color:#fff
    style O fill:#2e7d32,color:#fff
    style F fill:#ef6c00,color:#fff
    style R fill:#6a1b9a,color:#fff
```

> **Regla de ejecución SQL:** Las funciones de ventana se evalúan **después** de `FROM`, `WHERE`, `GROUP BY` y `HAVING`, pero **antes** del `ORDER BY` y `LIMIT` finales. Por eso **no puedes poner una función de ventana directamente en el `WHERE`**; debes envolverla en una **CTE** (`WITH`) o usar la cláusula **`QUALIFY`**.


## 2. Categorías de Funciones de Ventana

### A. Funciones de Ranking (Clasificación) — ¡Clásico de examen!
Requieren obligatoriamente `ORDER BY` dentro de `OVER()`. La diferencia crítica entre ellas radica en **cómo manejan los empates**:

| Ventas (`amount`) | `ROW_NUMBER()` | `RANK()` | `DENSE_RANK()` | `NTILE(2)` |
| :---: | :---: | :---: | :---: | :---: |
| 500 | **1** | **1** | **1** | **1** |
| 400 *(empate)* | **2** | **2** | **2** | **1** |
| 400 *(empate)* | **3** | **2** | **2** | **2** |
| 300 | **4** | **4** *(salta el 3)* | **3** *(no salta)* | **2** |

* **`ROW_NUMBER()`:** Asigna un número secuencial único (`1, 2, 3, 4...`). En caso de empate, asigna números distintos de forma arbitraria (a menos que añadas un criterio de desempate en `ORDER BY`). **Ideal para deduplicar registros.**
* **`RANK()`:** Asigna el mismo rango a los empates y **deja huecos** en la numeración siguiente (`1, 2, 2, 4`).
* **`DENSE_RANK()`:** Asigna el mismo rango a los empates **sin dejar huecos** (`1, 2, 2, 3`).
* **`NTILE(n)`:** Divide las filas ordenadas en `n` cubos (*buckets*) del tamaño más equilibrado posible (ej. `NTILE(4)` calcula cuartiles).

ROW_NUMBER
Asigna un número secuencial único independientemente de los empates:

```sql
-- Rank employees by salary (no ties)
SELECT
    employee_id,
    employee_name,
    salary,
    ROW_NUMBER() OVER (ORDER BY salary DESC) as rank
FROM employees;

-- Result:
-- emp1, Alice, $100k, 1
-- emp2, Bob, $95k, 2
-- emp3, Charlie, $95k, 3 ← Note: Both get unique rows
-- emp4, Diana, $90k, 4
```

**Patrón: los N primeros de cada categoría**

```sql
SELECT employee_id, employee_name, department, salary
FROM (
    SELECT
        *,
        ROW_NUMBER() OVER (
            PARTITION BY department
            ORDER BY salary DESC
        ) as rank_in_dept
    FROM employees
)
WHERE rank_in_dept <= 3;  -- Top 3 per department
```

### RANK
Asigna el mismo rango a los empates y omite números:

```sql
-- Rank employees by salary (with ties)
SELECT
    employee_id,
    employee_name,
    salary,
    RANK() OVER (ORDER BY salary DESC) as rank
FROM employees;

-- Result:
-- emp1, Alice, $100k, 1
-- emp2, Bob, $95k, 2
-- emp3, Charlie, $95k, 2 ← Tie gets same rank
-- emp4, Diana, $90k, 4  ← Next rank skips
```

### DENSE_RANK
Asigna el mismo rango a los empates, sin huecos:

```sql
-- Rank with no gaps
SELECT
    employee_id,
    employee_name,
    salary,
    DENSE_RANK() OVER (ORDER BY salary DESC) as dense_rank
FROM employees;

-- Result:
-- emp1, Alice, $100k, 1
-- emp2, Bob, $95k, 2
-- emp3, Charlie, $95k, 2 ← Tie
-- emp4, Diana, $90k, 3  ← No gap
```


### B. Funciones Analíticas / de Desplazamiento (Offset Functions)
Permiten acceder a valores de otras filas sin necesidad de hacer un `SELF JOIN`:

* **`LAG(columna, offset, default)`:** Accede al valor de una **fila anterior** dentro de la partición (ej. ventas del mes anterior para calcular crecimiento MoM).
* **`LEAD(columna, offset, default)`:** Accede al valor de una **fila siguiente** dentro de la partición.
  * *Parámetros:* `offset` por defecto es `1`; `default` es el valor devuelto si no existe fila previa/siguiente (por defecto `NULL`).
* **`FIRST_VALUE(columna)`:** Devuelve el primer valor del marco de ventana (*Window Frame*).
* **`LAST_VALUE(columna)`:** Devuelve el último valor del marco de ventana. *(¡Cuidado! Lee la sección 3 sobre el marco por defecto de `LAST_VALUE`)*.

```sql
-- Ejemplo: Comparar ventas diarias con el día anterior por tienda
SELECT
    store_id,
    sale_date,
    revenue,
    LAG(revenue, 1, 0) OVER (PARTITION BY store_id ORDER BY sale_date) AS prev_day_revenue,
    revenue - LAG(revenue, 1, 0) OVER (PARTITION BY store_id ORDER BY sale_date) AS daily_diff
FROM daily_sales;
```

### LAG: fila anterior

```sql
-- Compare to previous month
SELECT
    month,
    revenue,
    LAG(revenue) OVER (ORDER BY month) as prev_month_revenue,
    revenue - LAG(revenue) OVER (ORDER BY month) as month_over_month,
    ROUND(
        (revenue - LAG(revenue) OVER (ORDER BY month)) /
        LAG(revenue) OVER (ORDER BY month) * 100, 2
    ) as pct_change
FROM monthly_sales
ORDER BY month;

-- Result:
-- 2024-01: $100k, NULL,       NULL,      NULL
-- 2024-02: $110k, $100k,      $10k,      10.00%
-- 2024-03: $95k,  $110k,      -$15k,     -13.64%
```

### LEAD: fila siguiente

```sql
-- Preview next month's revenue
SELECT
    month,
    revenue,
    LEAD(revenue) OVER (ORDER BY month) as next_month_revenue
FROM monthly_sales;

-- Useful for: forecasting, planning, finding drops
```

### Desplazamiento personalizado

```sql
-- Get value from 3 months ago
SELECT
    month,
    revenue,
    LAG(revenue, 3) OVER (ORDER BY month) as revenue_3mo_ago,
    revenue - LAG(revenue, 3) OVER (ORDER BY month) as vs_3mo_ago
FROM monthly_sales
ORDER BY month;

-- LAG(column, offset, default_if_null)
SELECT
    month,
    revenue,
    LAG(revenue, 1, 0) OVER (ORDER BY month) as prev_revenue
FROM monthly_sales;
-- If no previous row, use 0 instead of NULL
```


### C. Funciones de Agregación como Ventana
Cualquier función agregada estándar (`SUM`, `AVG`, `COUNT`, `MIN`, `MAX`) funciona como ventana al añadir `OVER(...)`:

* **`SUM(ventas) OVER (PARTITION BY region)`:** Devuelve el total de ventas de toda la región repetido en cada fila (útil para calcular `% sobre el total`: `ventas / SUM(ventas) OVER (PARTITION BY region)`).
* **`SUM(ventas) OVER (PARTITION BY region ORDER BY fecha)`:** Calcula un **total acumulado (*Running Total*)** ordenado por fecha.

### Totales acumulados

```sql
-- Cumulative sales by date
SELECT
    sale_date,
    daily_revenue,
    SUM(daily_revenue) OVER (
        ORDER BY sale_date
        ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
    ) as cumulative_revenue
FROM daily_sales
ORDER BY sale_date;

-- Result:
-- 2024-01-01: $100k, $100k
-- 2024-01-02: $110k, $210k
-- 2024-01-03: $95k, $305k
```

### Media móvil

```sql
-- 7-day moving average
SELECT
    sale_date,
    daily_revenue,
    AVG(daily_revenue) OVER (
        ORDER BY sale_date
        ROWS BETWEEN 6 PRECEDING AND CURRENT ROW
    ) as avg_7day
FROM daily_sales
ORDER BY sale_date;

-- Window includes 7 days: 6 before + current row
```

### Agregados por partición

```sql
-- Revenue by product, compared to category average
SELECT
    product_id,
    product_name,
    category,
    revenue,
    AVG(revenue) OVER (PARTITION BY category) as category_avg,
    revenue - AVG(revenue) OVER (PARTITION BY category) as vs_avg
FROM products;

-- Shows each product vs its category average
```


## 3. Marcos de Ventana (Window Frames): `ROWS` vs `RANGE`
Un *Window Frame* define exactamente qué subconjunto de filas dentro de la partición se incluye en el cálculo respecto a la fila actual.

### Límites disponibles:
* `UNBOUNDED PRECEDING`: Desde la primera fila de la partición.
* `N PRECEDING`: `N` filas (o unidades de valor) antes de la fila actual.
* `CURRENT ROW`: La fila actual.
* `N FOLLOWING`: `N` filas (o unidades de valor) después de la fila actual.
* `UNBOUNDED FOLLOWING`: Hasta la última fila de la partición.

### Diferencia clave entre `ROWS` y `RANGE`:
* **`ROWS BETWEEN ...`:** Cuenta **posiciones físicas de filas** exactas (ej. `ROWS BETWEEN 2 PRECEDING AND CURRENT ROW` siempre toma exactamente 3 filas: la actual y las 2 anteriores).
* **`RANGE BETWEEN ...`:** Agrupa filas según el **valor de la columna del `ORDER BY`**. Si hay empates en el valor ordenado, `RANGE` incluye **todas las filas empatadas** en el mismo cálculo.

> **Trampa famosa de examen con el marco por defecto:**  
> Cuando incluyes `ORDER BY` dentro de `OVER(...)` sin especificar marco, Databricks aplica por defecto:  
> **`RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW`**  
> *Consecuencia:* Si usas `LAST_VALUE(col) OVER (ORDER BY fecha)`, ¡devolverá el valor de la **fila actual** y no el último de toda la partición! Para obtener el último valor real de toda la partición debes especificar:  
> `ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING`.


## 4. Cláusula `QUALIFY` (Exclusiva y muy valorada en Databricks SQL)
Históricamente, para filtrar por el resultado de una función de ventana (por ejemplo, quedarse solo con la fila `rn = 1` para deduplicar) había que crear una subconsulta o CTE. Databricks SQL soporta **`QUALIFY`**, que filtra directamente el resultado de las funciones de ventana (igual que `HAVING` filtra el `GROUP BY`):

```sql
-- Deduplicar clientes quedándose con el registro más reciente en una sola consulta limpia
SELECT
    customer_id,
    email,
    updated_at
FROM customers_raw
QUALIFY ROW_NUMBER() OVER (PARTITION BY customer_id ORDER BY updated_at DESC) = 1;
```


## 5. Cláusula `WINDOW` (Reutilización de definiciones)
Si vas a aplicar varias funciones de ventana con exactamente la misma partición y orden, puedes definirla una sola vez al final de la consulta (antes de `ORDER BY`) para mantener el código limpio:

```sql
SELECT
    product_id,
    sale_date,
    amount,
    SUM(amount) OVER w AS running_total,
    AVG(amount) OVER w AS running_avg
FROM sales
WINDOW w AS (PARTITION BY product_id ORDER BY sale_date ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW);
```


## 6. Preguntas Tipo Examen (Autoevaluación)

**Pregunta 1:** *Un analista quiere obtener los 3 productos más vendidos de cada categoría. Si dos productos empatan en el tercer puesto por tener exactamente las mismas ventas, ambos deben aparecer en el resultado (devolviendo 4 productos para esa categoría). ¿Qué función de ventana debe utilizar?*

* **Respuesta:** **`DENSE_RANK()`** o **`RANK()`** filtrando por `<= 3` (ambas asignan el rango `3` a los dos productos empatados, a diferencia de `ROW_NUMBER()` que excluiría uno arbitrariamente).

**Pregunta 2:** *Tienes una tabla de logs de sensores con registros duplicados para el mismo `sensor_id` y `timestamp`. Necesitas eliminar los duplicados conservando exactamente una fila por combinación en la consulta más concisa posible. ¿Qué combinación de función y cláusula debes usar?*

* **Respuesta:** **`ROW_NUMBER()`** junto con la cláusula **`QUALIFY`** (`QUALIFY ROW_NUMBER() OVER (PARTITION BY sensor_id, timestamp ORDER BY ingestion_time DESC) = 1`).

**Pregunta 3:** *Un analista escribe `SELECT fecha, ventas, AVG(ventas) OVER (ORDER BY fecha ROWS BETWEEN 6 PRECEDING AND CURRENT ROW) AS media_movil FROM ventas_diarias;`. ¿Qué calcula exactamente la columna `media_movil`?*

* **Respuesta:** Una **media móvil de 7 días** (la fila actual más las 6 filas físicas anteriores ordenadas por fecha).

**Pregunta 4:** *¿Por qué la siguiente consulta arroja un error de sintaxis/análisis? `SELECT emp_id, salary, RANK() OVER (ORDER BY salary DESC) AS rnk FROM employees WHERE rnk <= 5;`*

* **Respuesta:** Porque las funciones de ventana se evalúan **después** de la cláusula `WHERE` (y los alias del `SELECT` tampoco existen en el `WHERE`). Se soluciona cambiando `WHERE rnk <= 5` por **`QUALIFY RANK() OVER (ORDER BY salary DESC) <= 5`** o usando una CTE.


## 7. Problemas y errores comunes

### La función de ventana devuelve resultados incorrectos

**Escenario:** `ROW_NUMBER()` sobre una partición devuelve un orden inesperado.
**Solución:** Especifica siempre un `ORDER BY` explícito dentro de la definición de la ventana. Sin él, el orden de las filas no es determinista.


## 8. Consejos para el examen

- RANK asigna el mismo rango a los empates y omite números; DENSE_RANK asigna el mismo rango pero sin huecos; ROW_NUMBER siempre es único
- LAST_VALUE requiere `ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING` para ver todas las filas de la partición
- LAG accede a las filas anteriores; LEAD accede a las siguientes; ambos aceptan un parámetro de desplazamiento (1 por defecto)
- ROWS se basa en un recuento de filas (número exacto de filas); RANGE se basa en valores (rango lógico de valores)


## 9. Puntos clave

- **Función de ventana**: cálculo sobre filas relacionadas (sin agrupación)
- **PARTITION BY**: define los grupos del ámbito de la ventana
- **ORDER BY**: especifica el orden dentro de la partición
- **ROW_NUMBER**: número secuencial único
- **RANK/DENSE_RANK**: tratan los empates de forma distinta
- **LAG/LEAD**: acceden a filas desplazadas
- **Totales acumulados**: SUM con ROWS BETWEEN
- **Marco**: ROWS (según recuento) frente a RANGE (según valores)
- **FIRST_VALUE/LAST_VALUE**: valores límite de la ventana


## 10. Temas relacionados

- [SQL Essentials](../../../shared/fundamentals/sql-essentials.md) - Conceptos esenciales de SQL que sirven de base a las funciones de ventana
- [Window Functions Examples](../../../shared/code-examples/sql/window_functions.md) - Patrones de código reutilizables para funciones de ventana
- [SQL Functions Cheat Sheet](../../../shared/cheat-sheets/sql-functions.md) - Guía rápida de todas las categorías de funciones SQL


## 11. Documentación oficial

- [Funciones de ventana de Databricks](https://docs.databricks.com/sql/language-manual/sql-ref-functions-builtin.html#window-functions)
- [Funciones de ventana](https://docs.databricks.com/aws/en/sql/language-manual/sql-ref-window-functions)

---

**[← Anterior: Agregaciones, ordenamiento y filtrado](./04-aggregations-grouping.md) | [↑ Volver a la sección](./README.md) | [Siguiente: Creación de tablas y time travel de Delta →](./06-creating-tables-and-time-travel.md)**

# JOIN y operaciones de conjuntos

## 1. Tipos de JOIN en Databricks SQL
Los `JOIN` combinan **columnas** de dos o más tablas horizontalmente basándose en una condición lógica (clave de unión).

### Comparativa rápida de los JOIN estándar

| Tipo de JOIN | Qué filas devuelve | Comportamiento con no coincidencias |
| :--- | :--- | :--- |
| **`INNER JOIN`** (o `JOIN`) | Solo las filas donde existe coincidencia en **ambas** tablas. | Se descartan las filas sin pareja en cualquiera de los lados. |
| **`LEFT OUTER JOIN`** (o `LEFT JOIN`) | Todas las filas de la tabla izquierda (`A`) + coincidencias de la derecha (`B`). | Las columnas de `B` se rellenan con `NULL` si no hay coincidencia. |
| **`RIGHT OUTER JOIN`** (o `RIGHT JOIN`) | Todas las filas de la tabla derecha (`B`) + coincidencias de la izquierda (`A`). | Las columnas de `A` se rellenan con `NULL` si no hay coincidencia. |
| **`FULL OUTER JOIN`** (o `FULL JOIN`) | Todas las filas de **ambas** tablas (`A` y `B`), coincidan o no. | Rellena con `NULL` el lado que no tenga coincidencia. |
| **`CROSS JOIN`** | Producto cartesiano (`A × B`): combina cada fila de `A` con todas las de `B`. | No lleva cláusula `ON`. Genera `N × M` filas (¡cuidado con el rendimiento!). |


## 2. JOIN Avanzados / Especiales (¡Muy preguntados en el examen!)
Databricks SQL soporta dos tipos de `JOIN` filtrantes que **solo devuelven columnas de la tabla izquierda** y ayudan a escribir consultas más limpias y rápidas que usando `IN` o `NOT IN`:

### A. `LEFT SEMI JOIN` (o `SEMI JOIN`)
* **Qué hace:** Devuelve las filas de la tabla izquierda (`A`) que **tienen al menos una coincidencia** en la tabla derecha (`B`).
* **Diferencia clave frente a `INNER JOIN`:**
  1. **No duplica filas** de `A` aunque haya múltiples coincidencias en `B`.
  2. **No incluye ninguna columna** de la tabla `B` en el resultado (`SELECT *` solo muestra columnas de `A`).
* **Equivalente lógico:** `SELECT a.* FROM a WHERE EXISTS (SELECT 1 FROM b WHERE a.id = b.id)`

```sql
-- Ejemplo: Obtener clientes que han realizado al menos una compra (sin duplicar clientes)
SELECT c.customer_id, c.name
FROM customers c
LEFT SEMI JOIN orders o ON c.customer_id = o.customer_id;
```

### B. `LEFT ANTI JOIN` (o `ANTI JOIN`)
* **Qué hace:** Devuelve las filas de la tabla izquierda (`A`) que **NO tienen ninguna coincidencia** en la tabla derecha (`B`).
* **Ventaja clave frente a `NOT IN`:** Es **seguro ante valores `NULL`**. En SQL estándar, si la subconsulta de un `NOT IN` contiene un `NULL`, toda la consulta devuelve 0 filas; `ANTI JOIN` evita ese problema.
* **Equivalente lógico:** `SELECT a.* FROM a LEFT JOIN b ON a.id = b.id WHERE b.id IS NULL`

```sql
-- Ejemplo: Obtener clientes registrados que NUNCA han realizado una compra
SELECT c.customer_id, c.name
FROM customers c
LEFT ANTI JOIN orders o ON c.customer_id = o.customer_id;
```


### Tipos de Joins

```text
  INNER JOIN               LEFT JOIN                RIGHT JOIN
    A     B                  A     B                  A     B
  .---. .---.              .---. .---.              .---. .---.
 /    /X\    \            /XXXX/X\    \            /    /X\XXXX\

|    |XXX|    |          |XXXX|XXX|    |          |    |XXX|XXXX|
 \    \X/    /            \XXXX\X/    /            \    \X/XXXX/
  '---' '---'              '---' '---'              '---' '---'
 Solo intersección        Todo A + match B         Todo B + match A


 FULL OUTER JOIN         LEFT SEMI JOIN           LEFT ANTI JOIN
    A     B                  A     B                  A     B
  .---. .---.              .---. .---.              .---. .---.
 /XXXX/X\XXXX\            /    /X\    \            /XXXX/ \    \

|XXXX|XXX|XXXX|          |    |XXX|    |          |XXXX|   |    |
 \XXXX\X/XXXX/            \    \X/    /            \XXXX\ /    /
  '---' '---'              '---' '---'              '---' '---'
 Todo A y Todo B          Solo cols de A           Solo cols de A
                          (si hay match en B)      (si NO hay match en B)
```

### INNER JOIN

Devuelve solo las filas con valores coincidentes en ambas tablas:

```sql
-- Basic INNER JOIN
SELECT
    o.order_id,
    o.amount,
    c.customer_name,
    c.email
FROM orders o
INNER JOIN customers c ON o.customer_id = c.customer_id;

-- Implicit INNER JOIN (WHERE clause)
SELECT
    o.order_id,
    c.customer_name
FROM orders o, customers c
WHERE o.customer_id = c.customer_id;
```

**Visualización:**

```text
Pedidos:          Clientes:            Resultado:
1 → Alice         1 Alice              1 → Alice
2 → Bob           2 Bob                2 → Bob
3 → Charlie
4 → (null)
                  INNER JOIN conserva solo las filas coincidentes
```

**Consideraciones de rendimiento:**

- El tipo de join más rápido
- Filtra de inmediato las filas sin coincidencia
- Resulta útil para eliminar registros huérfanos

### LEFT JOIN (LEFT OUTER JOIN)

Devuelve todas las filas de la tabla izquierda y las filas coincidentes de la tabla derecha cuando existen:

```sql
-- LEFT JOIN keeps all customers, even if no orders
SELECT
    c.customer_id,
    c.customer_name,
    COUNT(o.order_id) as order_count,
    COALESCE(SUM(o.amount), 0) as total_spent
FROM customers c
LEFT JOIN orders o ON c.customer_id = o.customer_id
GROUP BY c.customer_id, c.customer_name;

-- Find customers with no orders
SELECT
    c.customer_name
FROM customers c
LEFT JOIN orders o ON c.customer_id = o.customer_id
WHERE o.order_id IS NULL;  -- NULL indicates no match
```

**Visualización:**

```text
Clientes:         Pedidos:             Resultado:
1 Alice    →      1 → $100             1 Alice → $100
2 Bob      →      2 → $200             2 Bob → $200
3 Charlie  →      (ninguno)            3 Charlie → NULL
4 Diana    →                           4 Diana → NULL
                  LEFT JOIN conserva todos los clientes
```

### RIGHT JOIN (RIGHT OUTER JOIN)

Devuelve todas las filas de la tabla derecha y las filas coincidentes de la tabla izquierda cuando existen:

```sql
-- RIGHT JOIN (less common than LEFT)
SELECT
    e.employee_name,
    d.department_name
FROM employees e
RIGHT JOIN departments d ON e.dept_id = d.dept_id;

-- Equivalent using LEFT (preferred):
SELECT
    e.employee_name,
    d.department_name
FROM departments d
LEFT JOIN employees e ON e.dept_id = d.dept_id;
```

**Mejores prácticas modernas de SQL:**

- RIGHT JOIN se usa rara vez
- Reescríbelo como LEFT JOIN invirtiendo el orden de las tablas
- Es más legible y coherente

### FULL OUTER JOIN

Devuelve todas las filas de ambas tablas, con NULL donde no hay coincidencias:

```sql
-- FULL OUTER JOIN - all customers and all orders
SELECT
    c.customer_id,
    c.customer_name,
    o.order_id,
    o.amount
FROM customers c
FULL OUTER JOIN orders o ON c.customer_id = o.customer_id
ORDER BY c.customer_id;

-- Find unmatched records (either side)
SELECT
    c.customer_id,
    o.order_id
FROM customers c
FULL OUTER JOIN orders o ON c.customer_id = o.customer_id
WHERE c.customer_id IS NULL OR o.order_id IS NULL;
```

**Visualización:**

```text
Clientes:         Pedidos:             Resultado:
1 Alice    →      1 → $100             1 Alice → $100
2 Bob      →      2 → $200             2 Bob → $200
3 Charlie  →      3 → $150             3 Charlie → $150
4 Diana           5 → $300             4 Diana → NULL
                                        NULL → 5 $300
```

### CROSS JOIN

Producto cartesiano: cada fila de la izquierda se combina con cada fila de la derecha:

```sql
-- CROSS JOIN (all combinations)
SELECT
    c.color,
    s.size
FROM colors c
CROSS JOIN sizes s;

-- Equivalent syntax
SELECT
    c.color,
    s.size
FROM colors c, sizes s;

-- Data:
-- colors: red, blue, green (3 rows)
-- sizes: S, M, L, XL (4 rows)
-- Result: 3 × 4 = 12 rows
--
-- red/S, red/M, red/L, red/XL,
-- blue/S, blue/M, blue/L, blue/XL,
-- green/S, green/M, green/L, green/XL
```

**Casos de uso:**

- Combinaciones de productos (p. ej., colores × tallas)
- Generación de rangos de fechas
- Generación de todas las posibilidades

```sql
-- Generate all dates in range
SELECT d.*, p.*
FROM (SELECT DATE('2024-01-01') + INTERVAL (n) DAY as date_value
      FROM RANGE(365)) d
CROSS JOIN products p;
```

### SEMI JOIN

Devuelve filas de la tabla izquierda para las que existe una coincidencia en la tabla derecha (pero excluye las columnas de la derecha):

```sql
-- Find customers with at least one order
SELECT DISTINCT c.customer_id, c.customer_name
FROM customers c
SEMI JOIN orders o ON c.customer_id = o.customer_id;

-- Equivalent using IN
SELECT DISTINCT customer_id, customer_name
FROM customers
WHERE customer_id IN (SELECT DISTINCT customer_id FROM orders);

-- Equivalent using EXISTS
SELECT c.customer_id, c.customer_name
FROM customers c
WHERE EXISTS (
    SELECT 1 FROM orders o
    WHERE o.customer_id = c.customer_id
);
```

**Rendimiento:** SEMI JOIN está optimizado para este caso de uso y suele ser más rápido que IN/EXISTS

### ANTI JOIN

Devuelve filas de la tabla izquierda para las que NO existe coincidencia en la tabla derecha:

```sql
-- Find customers with NO orders
SELECT DISTINCT c.customer_id, c.customer_name
FROM customers c
ANTI JOIN orders o ON c.customer_id = o.customer_id;

-- Equivalent using NOT IN
SELECT DISTINCT customer_id, customer_name
FROM customers
WHERE customer_id NOT IN (SELECT DISTINCT customer_id FROM orders);

-- Equivalent using NOT EXISTS
SELECT c.customer_id, c.customer_name
FROM customers c
WHERE NOT EXISTS (
    SELECT 1 FROM orders o
    WHERE o.customer_id = c.customer_id
);
```


### Optimización del rendimiento de los joins

#### Estrategia de índices

```sql
-- Create indexes on join columns (where supported)
CREATE INDEX idx_orders_customer ON orders(customer_id);
CREATE INDEX idx_customers_id ON customers(customer_id);

-- Verify join uses indexes
EXPLAIN SELECT * FROM orders o
JOIN customers c ON o.customer_id = c.customer_id;
```

#### Plan de ejecución

```text
-- Salida de EXPLAIN:
Join [customer_id#1 = customer_id#100]
├─ Scan Delta [orders]
│  └─ PushedFilters: None
└─ Scan Delta [customers]
   └─ PushedFilters: None

-- Consejos de optimización:
-- 1. Filtrar antes de unir: cláusulas WHERE antes de JOIN
-- 2. Proyectar columnas: SELECT solo las columnas necesarias
-- 3. Unir por claves primarias: permite más optimizaciones
```

#### El orden de los joins importa

```sql
-- ❌ Inefficient - large table first
SELECT *
FROM huge_transactions t
JOIN small_reference r ON t.id = r.id
WHERE t.status = 'completed';

-- ✅ Better - filter before join
SELECT *
FROM huge_transactions t
WHERE t.status = 'completed'  -- Reduce size first
JOIN small_reference r ON t.id = r.id;  -- Then join
```


## 3. Reglas Críticas y Trampas de Examen con JOIN

1. **Los `NULL` nunca hacen match en un `JOIN`:**
   * En SQL, `NULL = NULL` evalúa a `UNKNOWN` (falso en un `JOIN`). Si ambas tablas tienen `NULL` en la columna clave, **no se unirán** en un `INNER JOIN`.
   * *Solución si necesitas unir nulos:* Usar el operador **null-safe equality** `<=>` (`ON a.id <=> b.id`) o `IS NOT DISTINCT FROM`.
2. **Filtro en `ON` vs. Filtro en `WHERE` en un `LEFT JOIN`:**
   * Si pones una condición sobre la tabla derecha en el **`WHERE`** (ej. `WHERE b.status = 'PAID'`), estás eliminando los `NULL` generados por el `LEFT JOIN` y **convirtiéndolo en la práctica en un `INNER JOIN`**.
   * Si quieres mantener todas las filas de la izquierda, la condición sobre `b` debe ir dentro de la cláusula **`ON`** (`LEFT JOIN b ON a.id = b.id AND b.status = 'PAID'`).
3. **Data Skew (Sesgo de datos) en JOIN:**
   * Si una clave de unión tiene millones de registros iguales (por ejemplo, `customer_id = NULL` o `-1`), una sola tarea del SQL Warehouse recibirá casi todos los datos, provocando lentitud o *spilling to disk*.


## 4. Operaciones de Conjuntos (Set Operations)
Las operaciones de conjuntos combinan los resultados de dos o más consultas `SELECT` **verticalmente** (apilando filas).

### Requisitos obligatorios (Reglas de validez):
1. Todas las consultas `SELECT` deben tener **exactamente el mismo número de columnas**.
2. Las columnas correspondientes deben tener **tipos de datos compatibles** (o que puedan convertirse implícitamente).
3. Los nombres de las columnas del resultado final se toman de la **primera consulta `SELECT`**.

### Comparativa de Operadores de Conjuntos

| Operador | Qué devuelve | ¿Elimina duplicados? | Rendimiento |
| :--- | :--- | :---: | :--- |
| **`UNION ALL`** | Todas las filas de la consulta 1 y todas las de la consulta 2. | ❌ **No** (mantiene todo) | ⚡ **Muy rápido** (no requiere ordenar ni hacer *hash* para deduplicar). |
| **`UNION`** (o `UNION DISTINCT`) | Todas las filas de ambas consultas, eliminando filas idénticas. | ✅ **Sí** | 🐢 Más lento (requiere intercambio/deduplicación en red). |
| **`INTERSECT`** (o `INTERSECT DISTINCT`) | Solo las filas exactas que aparecen en **ambas** consultas. | ✅ **Sí** | Medio |
| **`INTERSECT ALL`** | Filas comunes manteniendo el número mínimo de repeticiones en ambos lados. | ❌ **No** | Medio |
| **`EXCEPT`** / **`MINUS`** | Filas que están en la **primera consulta** pero **NO** en la segunda. | ✅ **Sí** | Medio |
| **`EXCEPT ALL`** / **`MINUS ALL`** | Filas de la primera consulta restando las ocurrencias de la segunda. | ❌ **No** | Medio |

> **Nota de sintaxis en Databricks SQL:** `MINUS` es un sinónimo válido de `EXCEPT` en Databricks SQL.

### Comportamiento con `NULL` en Operaciones de Conjuntos:
* **¡Ojo con la diferencia frente a `JOIN`!** En operaciones de conjuntos (`UNION`, `INTERSECT`, `EXCEPT`), **dos valores `NULL` SÍ se consideran iguales** entre sí para efectos de deduplicación o intersección.


## 5. Ejemplos Prácticos de Conjuntos

```sql
-- 1. Consolidar ventas históricas y actuales sin perder registros ni gastar tiempo deduplicando
SELECT order_id, amount, order_date FROM sales_2025
UNION ALL
SELECT order_id, amount, order_date FROM sales_2026;

-- 2. Encontrar correos electrónicos presentes en la lista de newsletter pero NO en clientes activos
SELECT email FROM newsletter_subscribers
EXCEPT
SELECT email FROM active_customers;
```


## 6. Preguntas Tipo Examen (Autoevaluación)

**Pregunta 1:** *Un analista necesita obtener la lista de todos los productos del catálogo (`products`) que nunca han sido vendidos (`order_items`). Además, quiere asegurarse de que el resultado no se vea afectado si existen valores `NULL` en la tabla de ventas y solo necesita las columnas de la tabla `products`. ¿Qué operación es la más adecuada?*

* **Respuesta:** **`LEFT ANTI JOIN`** (`SELECT p.* FROM products p LEFT ANTI JOIN order_items o ON p.product_id = o.product_id`).

**Pregunta 2:** *Tienes dos tablas con la misma estructura (`ventas_europa` y `ventas_america`) y sabes por reglas de negocio que no comparten ningún `id_transaccion`. Necesitas unirlas en una sola vista. ¿Qué operador debes usar para maximizar el rendimiento del SQL Warehouse?*

* **Respuesta:** **`UNION ALL`**, porque evita el costo computacional de buscar y eliminar duplicados innecesarios que realizaría `UNION`.

**Pregunta 3:** *Un analista escribe la siguiente consulta esperando ver todos los empleados y, si tienen, sus ventas de 2026: `SELECT e.name, s.amount FROM employees e LEFT JOIN sales s ON e.id = s.emp_id WHERE s.year = 2026;`. Sin embargo, los empleados sin ventas desaparecieron del resultado. ¿Por qué ocurrió esto y cómo se corrige?*

* **Respuesta:** La condición `s.year = 2026` en el `WHERE` filtró las filas donde `s.year` era `NULL` (los empleados sin ventas), convirtiendo el `LEFT JOIN` en un `INNER JOIN`. Se corrige moviendo `AND s.year = 2026` a la cláusula **`ON`**.

**Pregunta 4:** *¿Cuál es la diferencia principal entre un `INNER JOIN` y un `LEFT SEMI JOIN` cuando un cliente tiene 5 pedidos en la tabla derecha?*

* **Respuesta:** El `INNER JOIN` devolverá **5 filas** para ese cliente e incluirá columnas de ambas tablas; el `LEFT SEMI JOIN` devolverá **1 sola fila** para ese cliente y **únicamente las columnas de la tabla izquierda**.


## 7. Problemas y errores comunes

### Errores OOM

**Escenario:** La asimetría de datos (data skew) hace que un ejecutor se quede sin memoria.
**Solución:** Usa Adaptive Query Execution (AQE) y revisa la lógica de unión (joins).

### Duplicación inesperada de filas tras un JOIN

**Escenario:** Un LEFT JOIN produce más filas que la tabla izquierda.
**Solución:** La tabla derecha tiene claves duplicadas. Usa `SELECT DISTINCT` sobre la clave de unión o agrega antes de unir para garantizar una cardinalidad uno a uno.


## 8. Consejos para el examen

- Para encontrar registros sin coincidencia (p. ej., clientes sin pedidos), usa LEFT JOIN con `WHERE key IS NULL`
- CROSS JOIN produce un producto cartesiano: número de filas = filas de la izquierda multiplicadas por las filas de la derecha
- SEMI JOIN suele estar más optimizado que IN o EXISTS para las comprobaciones de existencia
- Filtra siempre las filas antes de unir para mejorar el rendimiento (WHERE antes de JOIN)
- Cuando una clave tiene dos columnas, une por ambas: unir por una sola multiplica las filas
- `UNION` aplica `DISTINCT` por defecto; `UNION ALL` conserva los duplicados


## 9. Puntos clave

- **INNER JOIN**: solo las filas coincidentes de ambas tablas
- **LEFT JOIN**: todas las filas de la izquierda + las filas coincidentes de la derecha
- **FULL OUTER JOIN**: todas las filas de ambas tablas (izquierda + derecha)
- **CROSS JOIN**: producto cartesiano (todas las combinaciones)
- **SEMI/ANTI**: comprobaciones de existencia sin añadir columnas
- **Varias tablas**: se pueden encadenar varios joins
- **Condición de unión**: puede usar expresiones, rangos y varias columnas
- **El orden importa**: filtrar antes de unir para mejorar el rendimiento
- **Manejo de NULL**: IS NULL detecta las filas sin coincidencia
- **Operaciones de conjuntos**: `UNION` (distintos) frente a `UNION ALL` (conserva duplicados); `INTERSECT`; `EXCEPT`/`MINUS`


## 10. Temas relacionados

- [SQL Essentials](../../../shared/fundamentals/sql-essentials.md) - Conceptos esenciales de SQL, incluidos los fundamentos de los joins
- [SQL Functions Cheat Sheet](../../../shared/cheat-sheets/sql-functions.md) - Guía rápida de funciones usadas en condiciones de unión
- [Window Functions](../../../shared/code-examples/sql/window_functions.md) - Patrones avanzados de SQL que a menudo se combinan con joins


## 11. Documentación oficial

- [JOIN](https://docs.databricks.com/aws/en/sql/language-manual/sql-ref-syntax-qry-select-join)
- [Operadores de conjuntos](https://docs.databricks.com/aws/en/sql/language-manual/sql-ref-syntax-qry-select-setops)

---

**[← Anterior: Editor de consultas, asistente y ejecución](./02-query-editor.md) | [↑ Volver a la sección](./README.md) | [Siguiente: Agregaciones, ordenamiento y filtrado →](./04-aggregations-grouping.md)**

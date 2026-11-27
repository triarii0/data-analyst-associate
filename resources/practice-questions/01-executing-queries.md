# Preguntas de práctica de Ejecución de consultas con Databricks SQL y SQL warehouses

**Peso en el examen: 20 %** — Sección 4 de la guía de examen de octubre de 2025

Las preguntas marcadas como *(2025 blueprint)* abordan habilidades añadidas en la guía de examen de octubre de 2025. Todas las preguntas se verificaron con la documentación de Databricks el 26 de octubre de 2026.

[← Volver a Preguntas de práctica](./README.md) | [Siguiente: Creación de paneles y visualizaciones](./02-dashboards-visualizations.md)

---

## Pregunta 1: Tipo de SQL warehouse para concurrencia imprevisible *(Media)*

Un equipo cuenta con 50 analistas que ejecutan consultas en momentos imprevisibles, con picos repentinos de actividad. ¿Qué tipo de SQL warehouse gestiona mejor esta carga de trabajo?

A) Serverless warehouse, porque se inicia en segundos y utiliza Intelligent Workload Management para escalar  
B) Classic warehouse, porque es la opción de menor coste  
C) Pro warehouse, porque añade clústeres después de que las consultas lleven varios minutos en cola  
D) Un clúster de uso general para cada analista  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> Los SQL warehouses serverless se inician en unos 2–6 segundos y utilizan Intelligent Workload Management (IWM) para iniciar las consultas de inmediato cuando hay capacidad y para añadir clústeres rápidamente cuando crecen las colas.
>
> Los warehouses pro y classic también escalan automáticamente, pero añaden clústeres en función de la carga estimada y tardan unos 4 minutos en iniciarse, por lo que responden más lentamente a los picos. Los clústeres por analista no son SQL warehouses y desperdician capacidad de cómputo.

</details>


## Pregunta 2: Serverless frente a Pro: tiempo de inicio *(Fácil)*

Un equipo de producto necesita un SQL warehouse que esté listo en cuestión de segundos cuando llegue la primera consulta del panel de la mañana. ¿Qué tipo deben elegir?

A) Serverless  
B) Classic  
C) Pro  
D) Cualquier tipo: todos los tipos de warehouse se inician en segundos  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> Los warehouses serverless se inician normalmente en 2–6 segundos porque Databricks gestiona el cómputo.
>
> Los warehouses pro y classic suelen tardar unos 4 minutos en iniciarse (D).

</details>


## Pregunta 3: Valor predeterminado de auto stop en serverless *(Fácil)*

Un administrador del espacio de trabajo crea un SQL warehouse serverless en la interfaz de usuario y deja **Auto stop** en su valor predeterminado. ¿Después de cuántos minutos de inactividad se detiene el warehouse?

A) 5 minutos  
B) 10 minutos  
C) 45 minutos  
D) 120 minutos  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> En los warehouses serverless, el valor predeterminado de auto stop es de 10 minutos y el mínimo en la interfaz es de 5 minutos.
>
> Los warehouses pro y classic tienen un valor predeterminado de 45 minutos, con un mínimo de 10 minutos. 120 minutos no es un valor predeterminado actual. El contador de auto stop solo comienza cuando no hay consultas en ejecución.

</details>


## Pregunta 4: Colas del warehouse en horas punta *(Media)*

Un SQL warehouse pro está configurado con **Scaling** establecido en un mínimo y un máximo de 1 clúster. Durante las horas punta, las consultas esperan en la cola aunque cada una termina rápidamente una vez que comienza. ¿Qué cambio resuelve esto?

A) Aumentar el tamaño del warehouse de Small a Large  
B) Desactivar la caché de resultados de consultas  
C) Elevar el número máximo de clústeres para que el warehouse pueda añadir clústeres bajo carga  
D) Convertir el warehouse en classic  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> Scaling establece el número mínimo y máximo de clústeres. Más clústeres permiten que el warehouse ejecute más consultas al mismo tiempo. Databricks recomienda aproximadamente un clúster por cada 10 consultas concurrentes.
>
> Un tamaño mayor reduce la latencia de cada consulta, pero no añade concurrencia (A). Desactivar la caché solo añade trabajo (B). Classic no ofrece ninguna ventaja de concurrencia sobre pro (D).

</details>


## Pregunta 5: Capacidad exclusiva de serverless *(Media)*

¿Qué capacidad tiene un SQL warehouse serverless que NO tienen los warehouses pro y classic?

A) Photon  
B) Predictive I/O  
C) Auto stop  
D) Intelligent Workload Management  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> Intelligent Workload Management (IWM) está disponible solo en los warehouses serverless.
>
> Photon funciona en los tres tipos (A). Predictive I/O funciona en pro y serverless (B). Auto stop está disponible en todos los tipos (C).

</details>


## Pregunta 6: Tipo de warehouse predeterminado *(Fácil)* *(2025 blueprint)*

En un espacio de trabajo que admite cómputo serverless, un analista crea un SQL warehouse en la interfaz de usuario y no cambia el tipo. ¿Qué tipo se crea?

A) Classic  
B) Pro  
C) El tipo del warehouse utilizado más recientemente  
D) Serverless  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> En la interfaz de usuario, el tipo de SQL warehouse predeterminado es serverless en los espacios de trabajo elegibles.
>
> A través de la API con parámetros predeterminados, el tipo predeterminado es classic. Esa diferencia es un error común.

</details>


## Pregunta 7: Selección del tamaño del warehouse *(Media)*

Un analista ejecuta cada día consultas de agregación complejas sobre 500 GB de datos de Delta Lake. ¿Qué tamaño de warehouse es el más adecuado?

A) Large o X-Large, porque las consultas complejas sobre grandes volúmenes de datos se benefician de más trabajadores  
B) 2X-Small, porque es el que cuesta menos  
C) Small, porque está diseñado para usuarios individuales  
D) El tamaño no importa, porque todos los tamaños devuelven el mismo resultado con la misma rapidez  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> Cada tamaño superior duplica el número de trabajadores. Aumentar el tamaño reduce la latencia de las consultas pesadas.
>
> Los tamaños más pequeños son adecuados para tareas ligeras y exploratorias. Todos los tamaños devuelven el mismo resultado, pero no en el mismo tiempo (D).

</details>


## Pregunta 8: CREATE TABLE en Unity Catalog *(Media)*

¿Qué sentencia crea una tabla administrada denominada `transactions` en el esquema `gold` del catálogo `ml_catalog`?

A) `CREATE TABLE gold.transactions AS SELECT ...` sin ningún catálogo establecido  
B) `CREATE TABLE ml_catalog.gold.transactions AS SELECT ...`  
C) `USE CATALOG ml_catalog; CREATE TABLE gold.transactions AS SELECT ...`  
D) Tanto B como C  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> Un nombre totalmente calificado de tres niveles (B) y una sentencia `USE CATALOG` seguida de un nombre de dos niveles (C) crean la tabla en `ml_catalog.gold`.
>
> Sin un catálogo establecido (A), el nombre de dos niveles se resuelve contra el catálogo predeterminado actual, que puede no ser `ml_catalog`.

</details>


## Pregunta 9: INNER JOIN frente a LEFT JOIN *(Media)*

Una consulta debe devolver todos los empleados y sus nombres de departamento, incluidos los empleados que no tienen departamento. ¿Qué tipo de unión se debe utilizar?

A) LEFT JOIN de employees a departments  
B) INNER JOIN  
C) CROSS JOIN  
D) LEFT SEMI JOIN  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> Un LEFT JOIN conserva todas las filas de la tabla izquierda (employees). Los empleados sin departamento obtienen NULL en las columnas del departamento.
>
> Un INNER JOIN descarta los empleados sin coincidencia. Un CROSS JOIN devuelve todas las combinaciones. Un SEMI JOIN devuelve solo las filas izquierdas que coinciden y ninguna columna del lado derecho.

</details>


## Pregunta 10: Unión mediante una clave compuesta *(Difícil)* *(2025 blueprint)*

Las tablas `order_lines` y `shipments` están identificadas ambas por `order_id` y `line_number`. Un analista las une con `ON o.order_id = s.order_id` y ve muchas más filas de lo esperado. ¿Cuál es la solución?

A) Sustituir el INNER JOIN por un FULL OUTER JOIN  
B) Añadir `DISTINCT` al `SELECT` externo  
C) Unir por ambas claves: `ON o.order_id = s.order_id AND o.line_number = s.line_number`  
D) Añadir `ORDER BY order_id`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> Cuando la clave tiene dos columnas, unir solo una de ellas hace que cada línea de un pedido coincida con cada envío de ese pedido, lo que multiplica las filas.
>
> Una unión externa completa añade aún más filas (A). `DISTINCT` puede ocultar el síntoma, pero sigue emparejando las líneas incorrectas (B). Ordenar no cambia qué filas coinciden (D).

</details>


## Pregunta 11: Anti join *(Media)*

Una consulta debe devolver todos los clientes que NO han realizado ninguna compra. ¿Qué enfoques funcionan?

A) `customers LEFT JOIN purchases ... WHERE purchases.purchase_id IS NULL`  
B) `customers LEFT ANTI JOIN purchases ON ...`  
C) Tanto A como B  
D) `customers FULL OUTER JOIN purchases ... WHERE purchases.purchase_id IS NOT NULL`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> `LEFT ANTI JOIN` devuelve solo las filas izquierdas que no tienen coincidencia. Un `LEFT JOIN` filtrado a las filas donde la clave derecha es `IS NULL` produce el mismo resultado.
>
> La opción D conserva las filas que sí tienen compras, que es lo contrario de lo que se necesita.

</details>


## Pregunta 12: Semi join *(Media)*

Una consulta debe devolver cada producto que tenga al menos una reseña, una vez por producto. ¿Qué unión es la más adecuada?

A) INNER JOIN con reviews  
B) LEFT JOIN con reviews  
C) LEFT SEMI JOIN con reviews  
D) CROSS JOIN con reviews  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> Un `LEFT SEMI JOIN` devuelve las filas izquierdas en las que existe una coincidencia y nunca las duplica.
>
> Un INNER JOIN devuelve una fila por reseña coincidente, por lo que los productos con muchas reseñas aparecen muchas veces. Un LEFT JOIN también conserva los productos sin reseñas.

</details>


## Pregunta 13: UNION frente a UNION ALL *(Fácil)* *(2025 blueprint)*

La tabla `a` contiene los valores 1, 2, 2 y la tabla `b` contiene 2, 3. ¿Qué devuelve `SELECT x FROM a UNION SELECT x FROM b`?

A) 1, 2, 2, 2, 3  
B) 2  
C) 1, 2, 3  
D) 1  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> `UNION` aplica `DISTINCT` por defecto, por lo que se eliminan las filas duplicadas y el resultado es 1, 2, 3.
>
> `UNION ALL` conserva los duplicados y devolvería 1, 2, 2, 2, 3 (A). La opción B es lo que devuelve `INTERSECT`. La opción D es lo que devuelve `EXCEPT`.

</details>


## Pregunta 14: Clientes perdidos entre años *(Media)* *(2025 blueprint)*

Un analista necesita los identificadores de clientes que compraron en 2024 pero no en 2025. ¿Qué consulta los devuelve?

A) `SELECT customer_id FROM sales_2024 UNION SELECT customer_id FROM sales_2025`  
B) `SELECT customer_id FROM sales_2024 INTERSECT SELECT customer_id FROM sales_2025`  
C) `SELECT customer_id FROM sales_2024 EXCEPT SELECT customer_id FROM sales_2025`  
D) `SELECT customer_id FROM sales_2024 UNION ALL SELECT customer_id FROM sales_2025`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> `EXCEPT` devuelve las filas de la primera consulta que no aparecen en la segunda. Databricks SQL también acepta `MINUS` como sinónimo.
>
> `INTERSECT` devuelve los clientes que compraron en ambos años (B). `UNION` y `UNION ALL` combinan ambas listas (A, D). Todas las operaciones de conjuntos requieren el mismo número de columnas a ambos lados.

</details>


## Pregunta 15: Recuento aproximado de valores distintos *(Fácil)* *(2025 blueprint)*

Un mosaico de un panel muestra el número de visitantes distintos sobre miles de millones de visitas a páginas. Se acepta una estimación con unos pocos puntos porcentuales de error si es más rápida. ¿Qué función es adecuada?

A) `count(DISTINCT visitor_id)`  
B) `approx_count_distinct(visitor_id)`  
C) `count(visitor_id)`  
D) `sum(DISTINCT visitor_id)`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> `approx_count_distinct` devuelve un recuento estimado de valores distintos como `BIGINT`. Utiliza el algoritmo HyperLogLog++ y es preciso dentro del 5 % por defecto. Un argumento `relativeSD` opcional establece el error permitido.
>
> `count(DISTINCT ...)` es exacto, pero más costoso (A). `count(col)` cuenta valores no NULL, no valores distintos (C). La opción D suma identificadores distintos, lo que no tiene sentido (D).

</details>


## Pregunta 16: Estadísticas resumidas en un notebook *(Fácil)* *(2025 blueprint)*

Un analista quiere ver estadísticas resumidas de cada columna de un DataFrame desde código Python en un notebook. ¿Qué comando lo hace?

A) `dbutils.data.summarize(df)`  
B) `df.printSchema()`  
C) `spark.sql("DESCRIBE HISTORY my_table")`  
D) `df.count()`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> `dbutils.data.summarize` calcula y muestra estadísticas resumidas de un DataFrame de Spark o pandas. En la interfaz del notebook, el perfil de datos de una tabla de resultados ofrece una vista similar, con histogramas incluidos. Algunas estadísticas son aproximadas, salvo que se pase `precise=True`.
>
> `printSchema` muestra solo nombres y tipos de columnas (B). `DESCRIBE HISTORY` enumera las versiones de la tabla (C). `count()` devuelve solo el número de filas (D).

</details>


## Pregunta 17: Función de ventana: ROW_NUMBER *(Media)*

Una consulta debe devolver exactamente una fila por cliente: la compra con el importe más alto. ¿Qué función de ventana garantiza un número único por fila dentro de cada cliente?

A) `RANK() OVER (PARTITION BY customer_id ORDER BY amount DESC)`  
B) `DENSE_RANK() OVER (PARTITION BY customer_id ORDER BY amount DESC)`  
C) `ROW_NUMBER() OVER (PARTITION BY customer_id ORDER BY amount DESC)`  
D) `NTILE(1) OVER (PARTITION BY customer_id ORDER BY amount DESC)`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> `ROW_NUMBER()` numera las filas 1, 2, 3… sin empates, por lo que filtrar por `= 1` devuelve exactamente una fila por cliente.
>
> `RANK()` y `DENSE_RANK()` asignan el mismo número a las filas empatadas, por lo que `= 1` puede devolver varias filas. `NTILE(1)` coloca todas las filas en el bloque 1.

</details>


## Pregunta 18: RANK frente a DENSE_RANK *(Difícil)*

Las cifras de ventas son Alice = 100, Bob = 100 y Carol = 90, ordenadas de mayor a menor venta. ¿Qué valores devuelven `RANK()` y `DENSE_RANK()` para Carol?

A) `RANK()` = 3, `DENSE_RANK()` = 2  
B) `RANK()` = 2, `DENSE_RANK()` = 2  
C) `RANK()` = 2, `DENSE_RANK()` = 3  
D) `RANK()` = 3, `DENSE_RANK()` = 3  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> Alice y Bob empatan en el puesto 1 en ambas funciones. `RANK()` deja un hueco después de un empate, por lo que Carol ocupa el 3. `DENSE_RANK()` no deja huecos, por lo que Carol ocupa el 2.

</details>


## Pregunta 19: Función LAG *(Media)*

Una consulta calcula el cambio en las ventas mensuales respecto al mes anterior. ¿Qué expresión devuelve el valor del mes anterior?

A) `PREV(sales) OVER (ORDER BY month)`  
B) `LAG(sales, 1) OVER (ORDER BY month)`  
C) `LEAD(sales, 1) OVER (ORDER BY month)`  
D) `FIRST_VALUE(sales) OVER (ORDER BY month)`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> `LAG(column, offset)` devuelve el valor de `offset` filas antes de la fila actual.
>
> `LEAD` mira hacia delante, no hacia atrás (C). `FIRST_VALUE` devuelve el primer valor de la ventana, no el anterior (D). `PREV` no es una función de Databricks SQL (A).

</details>


## Pregunta 20: Total acumulado *(Media)*

Un analista necesita un total acumulado de ventas por fecha dentro de cada región. ¿Qué expresión lo calcula?

A) `SUM(sales) OVER (PARTITION BY region)`  
B) `SUM(sales)` con `GROUP BY region, sale_date`  
C) `CUMSUM(sales) OVER (PARTITION BY region ORDER BY sale_date)`  
D) `SUM(sales) OVER (PARTITION BY region ORDER BY sale_date)`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> Añadir `ORDER BY` dentro de `OVER` hace que la ventana crezca fila a fila, lo que produce una suma acumulada dentro de cada región.
>
> Sin `ORDER BY`, cada fila obtiene el total de la partición (A). `GROUP BY` devuelve un total por región y fecha, no un total acumulado (B). `CUMSUM` no es una función de Databricks SQL (C).

</details>


## Pregunta 21: CTE frente a subconsulta *(Fácil)*

¿Qué afirmación sobre las expresiones de tabla comunes (CTE) es VERDADERA?

A) Las CTE siempre son más rápidas que las subconsultas equivalentes  
B) Las CTE nombran un resultado intermedio, lo que mejora la legibilidad, y el nombre se puede referenciar más de una vez en la consulta  
C) Las CTE no se pueden usar en sentencias `SELECT`  
D) Las CTE se almacenan como tablas cuando termina la consulta  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> Una CTE (`WITH name AS (...)`) nombra un resultado intermedio durante una sola sentencia. Puede referenciarse varias veces sin repetir la lógica.
>
> Las CTE son una herramienta de legibilidad, no una garantía de mayor velocidad (A), y no se conserva nada cuando termina la sentencia (D).

</details>


## Pregunta 22: Una fila por elemento de un array *(Fácil)*

Una tabla tiene una columna `tags` de tipo `ARRAY<STRING>`. Una consulta necesita una fila de salida por etiqueta. ¿Qué función produce esto?

A) `FLATTEN(tags)`  
B) `ARRAY_JOIN(tags, ',')`  
C) `SPLIT(tags, ',')`  
D) `EXPLODE(tags)`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> `EXPLODE` es una función generadora que devuelve una fila por elemento del array.
>
> `FLATTEN` combina arrays anidados en uno solo sin añadir filas (A). `ARRAY_JOIN` concatena los elementos en una única cadena (B). `SPLIT` convierte una cadena en un array, que es la dirección opuesta (C).

</details>


## Pregunta 23: Agregar valores en un array *(Fácil)*

¿Qué función de agregación recoge los valores de muchas filas en un array por grupo?

A) `COLLECT_LIST` (o su sinónimo `ARRAY_AGG`)  
B) `CONCAT_WS`  
C) `EXPLODE`  
D) `COUNT`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> `COLLECT_LIST` devuelve un array con los valores del grupo. `ARRAY_AGG` es el nombre del estándar SQL para la misma agregación.
>
> `CONCAT_WS` une cadenas con un separador, pero no es una agregación (B). `EXPLODE` hace lo contrario: convierte un array en muchas filas (C).

</details>


## Pregunta 24: Convertir meses en columnas *(Media)*

Un analista quiere las ventas mensuales como columnas (Jan, Feb, Mar), una fila por producto. ¿Qué cláusula lo hace de forma más concisa?

A) `PIVOT (SUM(sales) FOR month IN ('Jan', 'Feb', 'Mar'))`  
B) `CASE WHEN month = 'Jan' THEN sales END` en la lista `SELECT`, sin agregación  
C) `GROUP BY month HAVING month IN ('Jan', 'Feb', 'Mar')`  
D) `TRANSPOSE(sales, month)`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> `PIVOT` convierte los valores distintos de una columna en columnas y agrega una medida para cada una: `FROM t PIVOT (agg(col) FOR pivot_col IN (values))`.
>
> La agregación condicional puede producir la misma forma, pero solo con `SUM(CASE ...)` y `GROUP BY product`. Sin agregación, la opción B devuelve una fila por fila de entrada. La opción C mantiene los meses como filas y `TRANSPOSE` no es una función de Databricks SQL.

</details>


## Pregunta 25: Días entre dos fechas *(Fácil)*

¿Qué expresión devuelve el número de días desde `order_date` hasta hoy como un número positivo para pedidos pasados?

A) `datediff(order_date, current_date())`  
B) `datediff(current_date(), order_date)`  
C) `months_between(current_date(), order_date)`  
D) `date_sub(current_date(), order_date)`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> `datediff(endDate, startDate)` devuelve el número de días desde `startDate` hasta `endDate` como `INTEGER`. La fecha final va primero.
>
> Invertir los argumentos devuelve un número negativo para pedidos pasados (A). `months_between` mide meses (C). `date_sub` resta un número de días a una fecha y devuelve una fecha (D).

</details>


## Pregunta 26: Ordenar con NULL *(Media)* *(2025 blueprint)*

Un analista ejecuta `SELECT * FROM products ORDER BY price ASC`. Algunos precios son NULL. ¿Dónde aparecen esas filas y cómo puede moverlas al final?

A) Al final por defecto; no hay que cambiar nada  
B) Se excluyen de los resultados ordenados; añadir `WHERE price IS NULL`  
C) Al principio por defecto; añadir `NULLS LAST`  
D) Al principio por defecto; añadir `DESC`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> En Databricks SQL, los NULL se ordenan primero con `ASC` y al final con `DESC`, salvo indicación contraria. `ORDER BY price ASC NULLS LAST` mantiene el orden ascendente y mueve los NULL al final.
>
> Ordenar nunca elimina filas (B). Cambiar a `DESC` mueve los NULL, pero invierte el orden de los precios (D).

</details>


## Pregunta 27: Diagnosticar un error con el asistente *(Fácil)* *(2025 blueprint)*

Una celda SQL de un notebook falla con un error. El analista quiere que Databricks Assistant proponga una versión corregida del código. ¿Qué comando debe usar?

A) `/explain`  
B) `/doc`  
C) `/findTables`  
D) `/fix`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> `/fix` propone correcciones de errores de código en una vista de diferencias. El botón **Diagnose error** que aparece bajo una celda fallida ejecuta `/fix` automáticamente.
>
> `/explain` describe qué hace el código (A). `/doc` añade comentarios (B). `/findTables` busca tablas relevantes en los metadatos de Unity Catalog (C). Databricks Assistant pasó a denominarse **Genie Code** el 11 de marzo de 2026, y estos comandos siguen funcionando con el nuevo nombre.

</details>


## Pregunta 28: Unión federada *(Media)* *(2025 blueprint)*

Un analista debe unir la tabla Delta de Unity Catalog `main.sales.orders` con una tabla `customers` que reside en una base de datos operativa PostgreSQL. No se permite copiar los datos de PostgreSQL. ¿Cuál es el enfoque de Databricks?

A) Usar Lakehouse Federation: crear una conexión y un catálogo externo para PostgreSQL y, a continuación, unir la tabla externa con la tabla Delta mediante nombres de tres niveles  
B) Cargar la tabla de PostgreSQL con `COPY INTO` y luego unirla  
C) Pedir al equipo de PostgreSQL que envíe los datos mediante Delta Sharing  
D) Exportar la tabla de PostgreSQL a CSV y subirla a través de la interfaz  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> Lakehouse Federation ofrece acceso gobernado y de solo lectura a bases de datos externas a través de catálogos externos de Unity Catalog. Las consultas se envían a la fuente por JDBC, por lo que los datos permanecen donde están. Una consulta puede unir una tabla externa con una tabla Delta como cualquier otras dos tablas.
>
> Las opciones B y D copian los datos. Delta Sharing (ahora denominado OpenSharing) comparte datos entre plataformas y no es una forma de consultar una base de datos PostgreSQL (C).

</details>


## Pregunta 29: Warehouse para consultas federadas *(Media)* *(2025 blueprint)*

Una consulta federada contra un catálogo externo falla en un SQL warehouse, pero funciona en otro. ¿Qué tipo de warehouse no puede ejecutar consultas de Lakehouse Federation?

A) Classic  
B) Serverless  
C) Pro  
D) Todos los tipos pueden ejecutarlas  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> Lakehouse Federation requiere un SQL warehouse pro o serverless (versión 2023.40 o posterior). Los clústeres necesitan Databricks Runtime 13.3 LTS o posterior en modo de acceso estándar o dedicado.
>
> Los catálogos externos son de solo lectura, por lo que las fuentes federadas se pueden consultar y unir, pero no se puede escribir en ellas.

</details>


## Pregunta 30: Tabla de streaming frente a vista materializada *(Media)* *(2025 blueprint)*

Nuevos archivos JSON llegan a un volumen de Unity Catalog cada pocos minutos. Un analista quiere una tabla que cargue solo los archivos nuevos en cada actualización. ¿Qué objeto es adecuado?

A) Una tabla de streaming: `CREATE OR REFRESH STREAMING TABLE ... AS SELECT * FROM STREAM read_files(...)`  
B) Una vista estándar sobre `read_files`  
C) Una vista materializada que vuelve a leer todos los archivos en cada actualización  
D) Una vista temporal  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> Una tabla de streaming procesa solo las filas que han llegado desde la última actualización y las añade. Con `STREAM read_files`, utiliza Auto Loader para recoger archivos nuevos de forma incremental.
>
> Las vistas materializadas son adecuadas para consultas complejas, como agregaciones y uniones, cuyos resultados se calculan previamente y se actualizan (C). Las vistas estándar y temporales no almacenan nada y vuelven a leer la fuente cada vez (B, D).

</details>


## Pregunta 31: Vista frente a vista materializada *(Media)* *(2025 blueprint)*

¿Cuál es la diferencia clave entre una vista estándar (o dinámica) y una vista materializada en Databricks SQL?

A) Una vista estándar ejecuta su consulta cada vez que se lee; una vista materializada almacena los resultados y los actualiza al actualizarse  
B) Una vista estándar almacena sus resultados; una vista materializada ejecuta su consulta cada vez que se lee  
C) Solo las vistas materializadas se pueden conceder a otros usuarios  
D) Solo las vistas estándar se pueden usar en paneles  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> Una vista materializada es una tabla administrada de Unity Catalog que almacena los resultados de una consulta y los actualiza al actualizarse, de forma incremental cuando es posible. Una vista estándar almacena solo el texto de la consulta. Una vista dinámica es una vista estándar que utiliza funciones como `is_account_group_member()` para filtrar filas o enmascarar columnas para cada usuario.
>
> Ambos tipos se pueden conceder (C) y usar en paneles (D).

</details>


## Pregunta 32: Actualizar cuando cambia la fuente *(Media)* *(2025 blueprint)*

Un analista quiere que una vista materializada se actualice automáticamente cada vez que se actualiza su tabla de origen, sin gestionar una programación. ¿Qué cláusula debe usar?

A) `SCHEDULE EVERY 1 HOUR`  
B) `REFRESH ASYNC`  
C) `WITH CONTINUOUS STREAMING`  
D) `TRIGGER ON UPDATE`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> `CREATE OR REPLACE MATERIALIZED VIEW mv TRIGGER ON UPDATE AS SELECT ...` actualiza la vista automáticamente cuando se actualiza la tabla de origen.
>
> `SCHEDULE EVERY` o `SCHEDULE CRON` actualizan según un horario (A). `REFRESH MATERIALIZED VIEW mv ASYNC` inicia una actualización manual sin esperar a que termine (B). La opción C no es sintaxis válida.

</details>


## Pregunta 33: Precalcular una consulta lenta de un panel *(Difícil)*

Cada vez que alguien abre un panel, se ejecuta la misma agregación pesada sobre una gran tabla de hechos. Los datos cambian una vez por noche. ¿Qué enfoque reduce más el tiempo de carga manteniendo las cifras actualizadas?

A) Dejar que cada consulta del panel ejecute la agregación cuando se abre el panel  
B) Mover el panel a un SQL warehouse más grande  
C) Confiar solo en la caché de resultados de consultas  
D) Crear una vista materializada con la agregación, actualizada con una programación nocturna, y apuntar el conjunto de datos del panel a ella  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> Una vista materializada ejecuta la agregación costosa una vez por actualización y almacena el resultado, por lo que cada lectura del panel es un análisis barato. Programar la actualización después de la carga nocturna la mantiene actualizada.
>
> Un warehouse más grande sigue repitiendo la consulta pesada en cada sesión (B). La caché de resultados se invalida cuando cambian las tablas y solo ayuda con consultas idénticas repetidas (C).

</details>


## Pregunta 34: Tabla unificada a partir de archivos y Delta *(Difícil)* *(2025 blueprint)*

Los datos de clientes llegan como archivos CSV en un volumen y los datos de productos como archivos Parquet. Los pedidos ya están en una tabla Delta. Un analista quiere una tabla administrada que una los tres. ¿Qué sentencia lo hace en SQL?

A) `CREATE VIEW unified AS SELECT * FROM csv.customers, parquet.products`  
B) `CREATE TABLE unified AS SELECT ... FROM read_files('/Volumes/main/raw/customers', format => 'csv') c JOIN read_files('/Volumes/main/raw/products', format => 'parquet') p ON ... JOIN main.sales.orders o ON ...`  
C) `COPY INTO unified FROM main.sales.orders`  
D) `CREATE EXTERNAL TABLE unified USING CSV, PARQUET, DELTA`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> `read_files` lee archivos CSV, JSON, Parquet, Avro, ORC, XML, de texto y binarios como una tabla. Utilizado dentro de `CREATE TABLE ... AS SELECT`, se puede unir con tablas Delta para crear una nueva tabla administrada.
>
> `COPY INTO` carga archivos, no tablas Delta (C). Una tabla tiene un solo formato, por lo que la opción D no es válida, y la opción A no es sintaxis válida para leer archivos.

</details>


## Pregunta 35: Viaje en el tiempo al día anterior *(Media)* *(2025 blueprint)*

Un analista necesita la tabla `orders` tal como estaba a medianoche del 25 de octubre de 2026. ¿Qué consulta funciona?

A) `SELECT * FROM orders AS OF DATE '2026-10-25'`  
B) `SELECT * FROM orders TIMESTAMP AS OF '2026-10-25'`  
C) `SELECT * FROM orders WHERE _version_date = '2026-10-25'`  
D) `SELECT * FROM orders HISTORY '2026-10-25'`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> El viaje en el tiempo de Delta Lake utiliza `TIMESTAMP AS OF <timestamp>` o `VERSION AS OF <version>`. También se puede escribir `orders@v123` para una versión.
>
> Utilice `DESCRIBE HISTORY orders` para encontrar los números de versión y las marcas de tiempo existentes. Las otras opciones no son sintaxis válida.

</details>


## Pregunta 36: El viaje en el tiempo falla para una versión antigua *(Difícil)* *(2025 blueprint)*

Una tabla Delta utiliza la configuración de retención predeterminada y `VACUUM` se ejecuta sobre ella cada semana. `DESCRIBE HISTORY` sigue mostrando una versión de hace 20 días, pero `SELECT ... VERSION AS OF` de esa versión falla. ¿Por qué?

A) El historial se conserva solo 7 días por defecto  
B) El viaje en el tiempo funciona solo en tablas externas  
C) La tabla se optimizó con liquid clustering  
D) `VACUUM` eliminó los archivos de datos que necesita la versión antigua; por defecto elimina archivos que llevan más de 7 días sin referenciarse  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> El historial de la tabla se conserva 30 días por defecto (`logRetentionDuration`), por lo que la versión sigue apareciendo. `VACUUM` elimina los archivos de datos sin referenciar más antiguos que `deletedFileRetentionDuration`, que por defecto es de 7 días. Una vez que esos archivos desaparecen, no se puede leer ni restaurar esa versión.
>
> El viaje en el tiempo funciona en tablas Delta administradas y externas (B). La agrupación no elimina el historial (C).

</details>


## Pregunta 37: Reemplazar una tabla de forma segura *(Media)* *(2025 blueprint)*

Un analista debe reconstruir `warehouse_db.product_inventory` con una nueva definición. Los usuarios ya tienen `SELECT` sobre la tabla y dependen de su historial. ¿Qué enfoque conserva ambos?

A) `DROP TABLE warehouse_db.product_inventory;` y después `CREATE TABLE ...`  
B) `CREATE TABLE IF NOT EXISTS warehouse_db.product_inventory (...)`  
C) `CREATE OR REPLACE TABLE IF NOT EXISTS warehouse_db.product_inventory (...)`  
D) `CREATE OR REPLACE TABLE warehouse_db.product_inventory (...)`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> En las tablas Delta, `REPLACE` conserva el historial de la tabla y los privilegios concedidos, los filtros de filas y las máscaras de columna. Databricks recomienda encarecidamente `REPLACE` antes que eliminar y volver a crear una tabla.
>
> Eliminar la tabla pierde el historial y los permisos (A). `IF NOT EXISTS` no hace nada cuando la tabla existe (B). `OR REPLACE` y `IF NOT EXISTS` no se pueden combinar (C).

</details>


**[↑ Volver a las preguntas de práctica de Data Analyst Associate](./README.md) | [Siguiente: preguntas de práctica de Creación de paneles y visualizaciones →](./02-dashboards-visualizations.md)**

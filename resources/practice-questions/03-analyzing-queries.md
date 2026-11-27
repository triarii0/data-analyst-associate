# Preguntas de práctica de Análisis de consultas

**Peso en el examen: 15 %** — Sección 5 de la guía de examen de octubre de 2025

Las preguntas marcadas como *(2025 blueprint)* abordan habilidades añadidas en la guía de examen de octubre de 2025. Todas las preguntas se verificaron con la documentación de Databricks el 26 de octubre de 2026.

[← Anterior: Creación de paneles y visualizaciones](./02-dashboards-visualizations.md) | [↑ Volver a Preguntas de práctica](./README.md) | [Siguiente: Genie](./04-genie-agents.md)

---

## Pregunta 1: Diagnosticar una consulta lenta *(Media)*

Una consulta en Databricks SQL se ejecuta más lentamente de lo esperado. ¿Qué función ayuda al analista a diagnosticarla?

A) Registros de experimentos de MLflow  
B) Registros de eventos del clúster  
C) El registro de transacciones de Delta  
D) El historial de consultas, que muestra la duración, las filas y la E/S de la consulta, con un enlace a su perfil de consulta  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> El historial de consultas enumera cada consulta con su estado, duración, filas devueltas y detalles de E/S. **See query profile** abre el plan de ejecución con métricas para cada operador.
>
> MLflow realiza el seguimiento de experimentos de ML (A) y el registro de Delta registra cambios en las tablas, no el rendimiento de las consultas (C).

</details>


## Pregunta 2: Filtrar por el resultado de una función de ventana *(Difícil)*

Un analista quiere solo el producto principal por ingresos en cada categoría. ¿Qué cláusula filtra el resultado de una función de ventana sin una subconsulta?

A) `HAVING rn = 1`  
B) `WHERE ROW_NUMBER() OVER (PARTITION BY category ORDER BY revenue DESC) = 1`  
C) `QUALIFY ROW_NUMBER() OVER (PARTITION BY category ORDER BY revenue DESC) = 1`  
D) `FILTER (WHERE rn = 1)`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> `QUALIFY` filtra los resultados de funciones de ventana, del mismo modo que `HAVING` filtra las agregaciones.
>
> `WHERE` se evalúa antes que las funciones de ventana, por lo que no puede hacer referencia a ellas (B). `HAVING` filtra grupos después de `GROUP BY` (A). `FILTER (WHERE ...)` se aplica a las filas de entrada de una agregación (D).

</details>


## Pregunta 3: Agregación en WHERE *(Media)*

Un analista escribe `SELECT region, COUNT(*) FROM orders WHERE COUNT(*) > 5 GROUP BY region` y obtiene un error. ¿Qué corrige la consulta?

A) Sustituir `WHERE` por `QUALIFY`  
B) Mover la condición a `HAVING COUNT(*) > 5` después de `GROUP BY`  
C) Sustituir `COUNT(*)` por `SUM(1)` en la cláusula `WHERE`  
D) Añadir `ORDER BY COUNT(*)`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> `WHERE` filtra filas antes de la agregación, por lo que no puede contener funciones de agregación. `HAVING` filtra después de `GROUP BY` y admite agregaciones.
>
> `QUALIFY` es para funciones de ventana (A) y `SUM(1)` sigue siendo una agregación (C).

</details>


## Pregunta 4: SUBSTR con desfase de uno *(Difícil)*

Los valores de `product_code` tienen el aspecto `XABCDE99`. Un analista necesita los caracteres del 2 al 5 (`ABCD`), pero escribe `SUBSTR(product_code, 2, 5)` y obtiene `ABCDE`. ¿Cuál es la expresión correcta?

A) `SUBSTR(product_code, 2, 4)`  
B) `LEFT(product_code, 5)`  
C) `SUBSTR(product_code, 1, 5)`  
D) `SUBSTR(product_code, 5, 2)`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> `SUBSTR(str, pos, len)` comienza en una posición basada en 1 y devuelve `len` caracteres. Empezar en 2 con longitud 4 devuelve los caracteres 2, 3, 4 y 5.
>
> El tercer argumento es una longitud, no una posición final, por lo que una longitud de 5 devolvió un carácter de más. `LEFT(..., 5)` devuelve `XABCD` (B).

</details>


## Pregunta 5: Ver el plan de una consulta *(Fácil)*

¿Qué sentencia muestra el plan de ejecución de una consulta sin ejecutarla?

A) `EXPLAIN SELECT ...`  
B) `DESCRIBE my_table`  
C) `PROFILE SELECT ...`  
D) `SHOW PLAN SELECT ...`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> `EXPLAIN` devuelve el plan lógico y físico de una sentencia. Después de ejecutar una consulta, su perfil de consulta muestra métricas reales de ejecución para cada operador.
>
> `DESCRIBE` describe los esquemas de las tablas (B). Las opciones C y D no son sentencias de Databricks SQL.

</details>


## Pregunta 6: Salarios por encima de la media *(Media)*

¿Qué consulta devuelve los empleados que ganan más que el salario medio?

A) `SELECT * FROM employees WHERE salary > (SELECT AVG(salary) FROM employees)`  
B) `SELECT * FROM employees WHERE salary > AVG(salary)`  
C) `SELECT * FROM employees HAVING salary > AVG(salary)`  
D) `SELECT * FROM employees WHERE salary > MEAN(salary)`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> Las agregaciones no pueden aparecer directamente en `WHERE`. Una subconsulta escalar calcula la media una vez y compara cada fila con ella.
>
> `MEAN` es un sinónimo de `AVG` en Databricks SQL, pero sigue siendo una agregación, por lo que la opción D falla por el mismo motivo que la opción B.

</details>


## Pregunta 7: Cuándo no se usa la caché de resultados *(Media)*

Las cachés de resultados de consultas de Databricks SQL pueden devolver resultados sin volver a ejecutar la consulta. ¿En qué caso NO se utiliza un resultado en caché?

A) La consulta se ejecutó hace una hora en el mismo warehouse  
B) El texto de la consulta es idéntico  
C) La tabla Delta subyacente se ha actualizado desde que se almacenó el resultado en caché  
D) El warehouse ya estaba en ejecución  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> Las cachés de resultados se invalidan cuando se actualizan las tablas subyacentes, por lo que la consulta se ejecuta de nuevo contra los datos actuales.
>
> La caché de resultados local reside en la memoria de un clúster. La caché de resultados remota (solo serverless) es compartida por todos los warehouses de un espacio de trabajo. Ambas conservan entradas durante un máximo de 24 horas.

</details>


## Pregunta 8: Qué es Photon *(Fácil)* *(2025 blueprint)*

¿Qué es Photon?

A) Una herramienta BI para crear paneles  
B) Un formato de archivo que sustituye a Parquet  
C) Un programador de consultas SQL  
D) El motor de consultas vectorizado nativo de Databricks, que es el motor predeterminado de todos los SQL warehouses  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> Photon es el motor de consultas vectorizado nativo de Databricks. Acelera las cargas de trabajo SQL y DataFrame, las uniones, las agregaciones, los análisis y las escrituras de Delta y Parquet sin cambios en el código, y es el motor predeterminado en todos los SQL warehouses.
>
> No es un formato de archivo (B) ni un programador (C).

</details>


## Pregunta 9: Photon y las UDF *(Media)* *(2025 blueprint)*

Una consulta en un SQL warehouse llama a una función definida por el usuario (UDF). ¿Qué ocurre con las partes de la consulta que Photon no admite?

A) La consulta falla  
B) Se recurren al entorno de ejecución de Spark y los resultados siguen siendo correctos  
C) Photon reescribe la UDF automáticamente  
D) El warehouse se reinicia sin Photon  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> Photon no admite UDF, API de RDD ni API de Dataset. Las operaciones no admitidas recurren al entorno de ejecución estándar de Spark y los resultados siguen siendo correctos. Solo esa parte de la consulta pierde la aceleración de Photon.
>
> Photon tampoco aporta gran beneficio a las consultas que ya terminan en menos de unos dos segundos.

</details>


## Pregunta 10: ¿Usó la consulta Photon? *(Media)* *(2025 blueprint)*

¿Cómo puede ver un analista cuánta parte del trabajo de una consulta se ejecutó en Photon?

A) Ejecutar `DESCRIBE HISTORY` sobre la tabla  
B) Comprobar el tamaño del warehouse  
C) Abrir el perfil de la consulta; sus detalles de ejecución muestran el porcentaje de tiempo de tarea empleado en Photon  
D) Buscar la palabra «Photon» en el conjunto de resultados  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> Los detalles de ejecución del perfil de la consulta muestran la proporción de tiempo de tarea empleada en Photon. En el plan, los operadores de Photon se muestran con un color distinto al de los operadores estándar de Spark.
>
> `DESCRIBE HISTORY` enumera las versiones de la tabla, no la ejecución de consultas (A).

</details>


## Pregunta 11: Detectar una unión que explota *(Media)* *(2025 blueprint)*

Una consulta que une dos tablas devuelve cientos de millones de filas y tarda mucho tiempo. ¿Qué herramienta muestra dónde se dispara el número de filas?

A) El gráfico de linaje de Catalog Explorer  
B) El historial de programaciones del panel  
C) `SHOW TABLES`  
D) El perfil de la consulta, que muestra filas, tiempo y memoria para cada operador  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> El perfil de la consulta muestra cada operador con métricas como el tiempo empleado, las filas y la memoria máxima. Databricks señala las uniones que explotan y los análisis completos de tabla como errores comunes que ayuda a detectar. Los iconos de filtro muestran el porcentaje de datos descartados durante los análisis.
>
> Cuando están disponibles, los insights de rendimiento en el panel de detalles de la consulta también sugieren correcciones, y **Optimize** abre Genie Code (antes Databricks Assistant) para reescribir la consulta.

</details>


## Pregunta 12: Encontrar consultas fallidas *(Fácil)* *(2025 blueprint)*

Un analista necesita todas las consultas que un compañero ejecutó ayer y que fallaron. ¿Cuál es la forma más rápida?

A) Buscar en el historial de revisiones del notebook  
B) Abrir el historial de consultas y filtrar por usuario, rango de fechas y estado  
C) Leer el registro de transacciones de Delta de cada tabla  
D) Pedir a un administrador del espacio de trabajo que exporte los registros de auditoría  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> El historial de consultas se puede filtrar por usuario, rango de fechas, cómputo, duración, estado de la consulta, tipo de sentencia, identificador de sentencia y etiquetas de consulta.
>
> Con el cómputo serverless habilitado, también incluye las consultas SQL y Python ejecutadas en cómputo serverless para notebooks y jobs.

</details>


## Pregunta 13: Análisis de consultas en toda la cuenta *(Media)* *(2025 blueprint)*

Un equipo de plataforma quiere escribir SQL que encuentre las consultas más lentas en todos los warehouses de la cuenta. ¿Qué fuente deben consultar?

A) `system.query.history`  
B) `information_schema.tables`  
C) `DESCRIBE HISTORY`  
D) La caché de resultados local del warehouse  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> La tabla del sistema `system.query.history` contiene registros de consultas de toda la cuenta. Por defecto, solo los administradores pueden acceder a las tablas del sistema, por lo que los demás usuarios necesitan que se les conceda acceso.
>
> `information_schema.tables` enumera los metadatos de las tablas (B) y `DESCRIBE HISTORY` cubre los cambios de una sola tabla (C).

</details>


## Pregunta 14: Resultado rápido tras un reinicio *(Difícil)* *(2025 blueprint)*

En un SQL warehouse serverless, un analista ejecuta una consulta, el warehouse se detiene y se inicia de nuevo, y la misma consulta devuelve el resultado al instante. Ninguna tabla ha cambiado. ¿Qué caché lo explica?

A) La caché de resultados local en la memoria del clúster  
B) La caché de disco en los SSD del clúster  
C) La caché de resultados remota, que persiste tras los reinicios del warehouse y es compartida por todos los warehouses del espacio de trabajo  
D) La caché del navegador  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> La caché de resultados remota solo está disponible en los warehouses serverless. Es una caché persistente compartida por todos los warehouses de un espacio de trabajo, sobrevive a las paradas y los reinicios, y conserva entradas durante 24 horas.
>
> La caché de resultados local y la caché de disco se borran cuando se detiene el clúster (A, B). Una consulta comprueba primero la caché local y después la remota, y solo se ejecuta cuando ninguna de las dos contiene el resultado.

</details>


## Pregunta 15: Comparar rendimiento sin caché *(Fácil)* *(2025 blueprint)*

Un analista quiere medir el tiempo de una consulta de forma fiable y debe evitar que Databricks SQL devuelva un resultado en caché. ¿Qué debe ejecutar primero?

A) `DROP CACHE`  
B) `OPTIMIZE my_table`  
C) `SET use_cached_result = false`  
D) `REFRESH TABLE my_table`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> `SET use_cached_result = false` desactiva la caché de resultados para la sesión. Databricks aconseja usarlo solo para pruebas o comparaciones de rendimiento.
>
> `OPTIMIZE` reescribe los archivos de datos y no afecta al almacenamiento de resultados en caché (B).

</details>


## Pregunta 16: ¿Quién eliminó las filas? *(Fácil)* *(2025 blueprint)*

Desaparecieron filas de una tabla Delta durante la noche. ¿Qué comando muestra qué operación se ejecutó, quién la ejecutó y cuándo?

A) `DESCRIBE DETAIL orders`  
B) `SHOW GRANTS ON TABLE orders`  
C) `DESCRIBE HISTORY orders`  
D) `SELECT * FROM orders VERSION AS OF 0`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> `DESCRIBE HISTORY` devuelve cada escritura en la tabla en orden inverso, con la operación, el usuario, la marca de tiempo, `operationParameters` y `operationMetrics` (como el número de filas eliminadas).
>
> `DESCRIBE DETAIL` muestra los metadatos actuales de la tabla, como la ubicación y el tamaño (A). La opción D lee la primera versión de los datos, pero no indica quién los cambió.

</details>


## Pregunta 17: Filas cambiadas entre versiones *(Media)* *(2025 blueprint)*

Un analista quiere las filas que están en la versión 12 de la tabla `orders` pero no estaban en la versión 10. ¿Qué consulta las devuelve?

A) `SELECT * FROM orders VERSION AS OF 12 EXCEPT SELECT * FROM orders VERSION AS OF 10`  
B) `DESCRIBE HISTORY orders LIMIT 2`  
C) `RESTORE TABLE orders TO VERSION AS OF 10`  
D) `SELECT * FROM orders WHERE version BETWEEN 10 AND 12`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> El viaje en el tiempo puede leer dos versiones de la misma tabla y `EXCEPT` devuelve las filas de la primera consulta que faltan en la segunda. Es una forma rápida de validar una carga o comparar resultados a lo largo del tiempo.
>
> `RESTORE` revierte la tabla y la modifica (C). Las tablas no tienen una columna `version` (D).

</details>


## Pregunta 18: Habilitar liquid clustering *(Fácil)* *(2025 blueprint)*

¿Qué sentencia crea una tabla que usa liquid clustering sobre `customer_id`?

A) `CREATE TABLE sales (...) PARTITIONED BY (customer_id)`  
B) `CREATE TABLE sales (...) ZORDER BY (customer_id)`  
C) `CREATE TABLE sales (...) BUCKET BY (customer_id)`  
D) `CREATE TABLE sales (...) CLUSTER BY (customer_id)`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> `CLUSTER BY` habilita el liquid clustering. Para una tabla existente, use `ALTER TABLE ... CLUSTER BY (...)`. Databricks recomienda el liquid clustering para todas las tablas nuevas, incluidas las tablas de streaming y las vistas materializadas.
>
> El liquid clustering sustituye a la partición y a `ZORDER` y no se puede combinar con ellos (A, B).

</details>


## Pregunta 19: Elegir las claves de agrupación *(Media)* *(2025 blueprint)*

Una tabla de 2 TB se consulta normalmente con `WHERE event_date = ...` y `WHERE country = ...`. ¿Qué columnas deben ser las claves de liquid clustering?

A) La columna de clave principal, sea cual sea el filtro de las consultas  
B) Todas las columnas de la tabla  
C) `event_date` y `country`, las columnas más utilizadas en los filtros  
D) Una columna de hash aleatoria  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> Elija las claves de agrupación entre las columnas más utilizadas en los filtros de las consultas. Se pueden especificar hasta cuatro claves.
>
> Agrupar por columnas que las consultas no filtran no ayuda a descartar datos (A, D) y no se permiten más de cuatro claves (B).

</details>


## Pregunta 20: Cambiar las claves de agrupación *(Difícil)* *(2025 blueprint)*

Un analista ejecuta `ALTER TABLE sales CLUSTER BY (country)` sobre una tabla que contiene años de datos. ¿Qué ocurre con los datos existentes?

A) Toda la tabla se reescribe inmediatamente durante el `ALTER`  
B) El comando falla porque las claves de agrupación no se pueden cambiar  
C) Las claves cambian sin reescribir los datos existentes; `OPTIMIZE` agrupa los datos de forma incremental y `OPTIMIZE ... FULL` vuelve a agruparlos todos  
D) La tabla se convierte en una tabla particionada  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> Las claves de liquid clustering se pueden redefinir sin reescribir los datos existentes. `OPTIMIZE` es incremental y reescribe solo lo necesario. `OPTIMIZE table_name FULL` vuelve a agrupar todos los registros. La optimización predictiva puede ejecutar `OPTIMIZE` automáticamente en las tablas habilitadas.
>
> Las claves se pueden cambiar en cualquier momento (B) y el liquid clustering no es partición (D).

</details>


## Pregunta 21: Liquid clustering automático *(Media)* *(2025 blueprint)*

¿Qué hace `CLUSTER BY AUTO` y qué requiere?

A) Databricks elige las claves de agrupación según cómo se consulta la tabla; requiere una tabla administrada de Unity Catalog y optimización predictiva  
B) Agrupa por todas las columnas; funciona en cualquier tabla  
C) Desactiva la agrupación  
D) Particiona la tabla por fecha automáticamente  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> El liquid clustering automático permite que Databricks elija y cambie las claves de agrupación para mejorar el rendimiento de las consultas. Funciona en tablas administradas de Unity Catalog y necesita optimización predictiva para la selección de claves y la agrupación, que se ejecutan de forma asíncrona.

</details>


## Pregunta 22: Columna ausente en GROUP BY *(Fácil)* *(2025 blueprint)*

`SELECT region, product, SUM(sales) FROM orders GROUP BY region` falla. ¿Por qué?

A) `SUM` no se puede usar con `GROUP BY`  
B) `product` se selecciona, pero no está agrupado ni agregado  
C) `GROUP BY` debe ir antes de `FROM`  
D) `region` debe agregarse  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> Todas las columnas no agregadas de la lista `SELECT` deben aparecer en `GROUP BY`. Añada `product` a `GROUP BY` (o use `GROUP BY ALL`) o elimínelo de la lista `SELECT`.

</details>


## Pregunta 23: Comparar con NULL *(Media)* *(2025 blueprint)*

`SELECT * FROM orders WHERE discount = NULL` no devuelve filas, aunque muchos pedidos no tienen descuento. ¿Cuál es la solución?

A) `WHERE discount IS NULL`  
B) `WHERE discount == NULL`  
C) `WHERE discount = 'NULL'`  
D) `WHERE NOT discount`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> Comparar cualquier cosa con NULL mediante `=` devuelve NULL, no TRUE, por lo que ninguna fila pasa el filtro. Use `IS NULL` o `IS NOT NULL`.
>
> La opción C compara con el texto `'NULL'`, que es un valor distinto.

</details>


## Pregunta 24: LEFT JOIN que se comporta como INNER JOIN *(Difícil)* *(2025 blueprint)*

Un analista quiere todos los clientes con sus pedidos de 2026, incluidos los clientes sin pedidos en 2026. La consulta es:

`SELECT c.id, o.amount FROM customers c LEFT JOIN orders o ON c.id = o.customer_id WHERE o.order_year = 2026`

Faltan los clientes sin pedidos en 2026. ¿Qué lo corrige?

A) Cambiar a `RIGHT JOIN`  
B) Añadir `DISTINCT`  
C) Cambiar a `CROSS JOIN`  
D) Mover la condición a la unión: `ON c.id = o.customer_id AND o.order_year = 2026`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> Para los clientes sin pedido coincidente, `o.order_year` es NULL, por lo que el filtro `WHERE` los elimina y la unión externa actúa como una unión interna. Filtrar la tabla derecha en la cláusula `ON` conserva todos los clientes.

</details>


**[← Anterior: preguntas de práctica de Creación de paneles y visualizaciones](./02-dashboards-visualizations.md) | [↑ Volver a las preguntas de práctica de Data Analyst Associate](./README.md) | [Siguiente: preguntas de práctica de Genie →](./04-genie-agents.md)**

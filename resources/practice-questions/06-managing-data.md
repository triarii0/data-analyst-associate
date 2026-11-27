# Preguntas de práctica sobre gestión de datos

**Peso en el examen: 8 %** — Sección 2 de la guía del examen de octubre de 2025

Las preguntas marcadas como *(2025 blueprint)* cubren habilidades añadidas en la guía del examen de octubre de 2025. Todas las preguntas se verificaron con la documentación de Databricks el 26 de octubre de 2026.

[← Anterior: Comprensión de Databricks Data + AI Platform](./05-databricks-platform.md) | [↑ Volver a las preguntas de práctica](./README.md) | [Siguiente: Seguridad de datos](./07-securing-data.md)

---

## Pregunta 1: Añadir una nueva columna *(Media)*

Un analista añade datos que tienen una columna adicional a una tabla Delta existente con `df.write.format("delta").mode("append").saveAsTable("sales")`. La nueva columna no está en el esquema de la tabla. ¿Qué sucede?

A) La columna se añade al esquema de la tabla automáticamente  
B) La escritura falla, porque Delta Lake aplica el esquema de la tabla a menos que la evolución del esquema esté habilitada  
C) La nueva columna se descarta silenciosamente  
D) La columna se almacena como una cadena JSON  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> Delta Lake aplica el esquema al escribir. Para añadir la nueva columna durante la escritura, habilite la evolución del esquema, por ejemplo con `.option("mergeSchema", "true")`.
>
> Delta nunca descarta ni reconfigura columnas silenciosamente (C, D).

</details>


## Pregunta 2: Reemplazar un esquema *(Fácil)*

¿Qué hace la opción `overwriteSchema` cuando se combina con `mode("overwrite")` en una escritura de Delta Lake?

A) Reemplaza el esquema de la tabla por el esquema de los datos que se escriben  
B) Añade nuevas columnas al esquema existente  
C) Comprueba que los esquemas coincidan y falla si difieren  
D) Convierte todas las columnas a string  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> `overwriteSchema` reemplaza el esquema además de los datos. Se necesita cuando las columnas se renombran, se eliminan o cambian de tipo. `mergeSchema` solo añade columnas.

</details>


## Pregunta 3: Establecer una propiedad de tabla *(Fácil)*

¿Qué sentencia establece una propiedad personalizada en una tabla Delta?

A) `SET TABLE finance.transactions PROPERTIES (owner_team = 'finance')`  
B) `UPDATE TABLE finance.transactions SET PROPERTY owner_team = 'finance'`  
C) `CONFIGURE TABLE finance.transactions WITH (owner_team = 'finance')`  
D) `ALTER TABLE finance.transactions SET TBLPROPERTIES ('owner_team' = 'finance')`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> `ALTER TABLE ... SET TBLPROPERTIES` establece propiedades de tabla de clave-valor. Las otras opciones no son sintaxis válida.

</details>


## Pregunta 4: ¿Quién lee esta tabla? *(Media)*

Un equipo de cumplimiento quiere ver qué notebooks, jobs y dashboards leen de `gold.revenue_metrics`. ¿Qué funcionalidad muestra esto?

A) Seguimiento de experimentos de MLflow  
B) El registro de transacciones de Delta  
C) Monitoreo de SQL warehouse  
D) El linaje de Unity Catalog en la pestaña Lineage de la tabla en Catalog Explorer  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> Unity Catalog captura el linaje a nivel de tabla y, cuando es posible, a nivel de columna, en consultas, notebooks, jobs, dashboards y pipelines. La pestaña Lineage los enumera y **See Lineage Graph** los muestra como un diagrama.
>
> El linaje mostrado en Catalog Explorer se conserva de forma indefinida, mientras que las tablas del sistema de linaje conservan un año móvil. El linaje no se conserva cuando se renombra un objeto.

</details>


## Pregunta 5: Valores NULL en SUM *(Media)*

Una columna `revenue` contiene algunos valores NULL. ¿Qué devuelve `SUM(revenue)`?

A) NULL, porque cualquier NULL hace que la agregación sea NULL  
B) Un error  
C) La suma con los NULL contados como 0, y 0 cuando todos los valores son NULL  
D) La suma de los valores no NULL  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> Las funciones de agregación como `SUM`, `AVG`, `MIN`, `MAX` y `COUNT(col)` ignoran los NULL. `COUNT(*)` cuenta todas las filas. `SUM` devuelve NULL solo cuando todos los valores son NULL, por lo que la opción C es incorrecta.
>
> Si se desea que NULL cuente como cero, escriba `SUM(COALESCE(revenue, 0))`.

</details>


## Pregunta 6: Certificar una tabla *(Difícil)* *(2025 blueprint)*

Un responsable de datos quiere que `main.sales.transactions` aparezca como certificada para que los analistas sepan que es la fuente de confianza. ¿Qué debe hacer el responsable?

A) Establecer la etiqueta gobernada `system.certification_status` en `certified` en la tabla, con `SET TAG ON TABLE` o **Assign certification** en la interfaz  
B) Establecer la propiedad de tabla `certified` en `true` con `ALTER TABLE ... SET TBLPROPERTIES`  
C) Otorgar el privilegio `CERTIFY` sobre la tabla al grupo de responsables de datos  
D) Añadir la palabra "certified" al comentario de la tabla  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> La certificación es la etiqueta gobernada por el sistema `system.certification_status` con los valores `certified` o `deprecated`. En SQL, use `SET TAG ON TABLE` con la clave de la etiqueta entre comillas invertidas. En la interfaz, elija **Assign certification** en el menú de tres puntos del objeto.
>
> Aplicarla requiere el permiso ASSIGN sobre esa etiqueta gobernada, además de APPLY TAG sobre el objeto (o su propiedad) y USE SCHEMA y USE CATALOG sobre sus objetos superiores. Una propiedad o un comentario de tabla no muestran la insignia (B, D), y `CERTIFY` no es un privilegio (C).

</details>


## Pregunta 7: Encontrar datos certificados *(Fácil)* *(2025 blueprint)*

Un analista quiere ver solo tablas certificadas al buscar en el workspace. ¿Cómo puede hacerlo?

A) Buscar `certificationStatus:certified` o usar el filtro **Certification status**  
B) Consultar `system.query.history`  
C) Ejecutar `SHOW TABLES LIKE 'certified*'`  
D) No se pueden buscar tablas certificadas  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> La búsqueda de workspace admite `certificationStatus:certified` y `certificationStatus:deprecated`, y cuenta con un filtro **Certification status**.
>
> `SHOW TABLES LIKE` compara nombres de tablas, no etiquetas (C).

</details>


## Pregunta 8: Etiquetar una columna PII *(Media)* *(2025 blueprint)*

Un analista debe etiquetar la columna `ssn` de `abac.customers.profiles` como PII. ¿Qué sentencia lo hace?

A) `ALTER TABLE abac.customers.profiles SET TBLPROPERTIES ('ssn' = 'pii')`  
B) `ALTER TABLE abac.customers.profiles ALTER COLUMN ssn SET TAGS ('pii' = 'ssn')`  
C) `UPDATE abac.customers.profiles SET TAG ssn = 'pii'`  
D) `TAG COLUMN ssn AS pii`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> `ALTER TABLE ... ALTER COLUMN ... SET TAGS` añade etiquetas de columna. En Catalog Explorer, se hace lo mismo desde la página Overview del objeto, en **Tags** > **Add tags**.
>
> Para etiquetar un objeto, debe ser su propietario o tener APPLY TAG sobre él, además de USE SCHEMA y USE CATALOG sobre sus objetos superiores. Las claves de etiqueta distinguen entre mayúsculas y minúsculas, y cada objeto puede tener hasta 50 etiquetas.

</details>


## Pregunta 9: Listar columnas etiquetadas *(Media)* *(2025 blueprint)*

Un analista quiere una lista en SQL de todas las columnas etiquetadas como `pii` en un catálogo. ¿Dónde debe buscar?

A) `DESCRIBE HISTORY`  
B) `information_schema.column_tags`  
C) `system.query.history`  
D) `SHOW GRANTS`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> El information schema tiene vistas para las etiquetas, entre ellas `CATALOG_TAGS`, `SCHEMA_TAGS`, `TABLE_TAGS`, `COLUMN_TAGS` y `VOLUME_TAGS`. Los resultados se limitan a los objetos sobre los que se tiene privilegios.

</details>


## Pregunta 10: Eliminar filas no válidas *(Fácil)* *(2025 blueprint)*

Una tabla Delta `silver.orders` contiene filas con valores negativos en `amount`, que no son válidos. ¿Qué sentencia las elimina?

A) `DROP ROWS FROM silver.orders WHERE amount < 0`  
B) `DELETE FROM silver.orders WHERE amount < 0`  
C) `TRUNCATE TABLE silver.orders WHERE amount < 0`  
D) `ALTER TABLE silver.orders DROP amount < 0`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> `DELETE FROM ... WHERE` elimina las filas coincidentes de una tabla Delta, y el cambio se registra en el historial de la tabla.
>
> `TRUNCATE TABLE` elimina todas las filas y no admite una cláusula `WHERE` (C). Las opciones A y D no son sintaxis válida.

</details>


## Pregunta 11: Completar valores faltantes *(Fácil)* *(2025 blueprint)*

En una vista depurada, un valor NULL en `region` debe aparecer como `'Unknown'`. ¿Qué expresión lo hace?

A) `NULLIF(region, 'Unknown')`  
B) `region IS 'Unknown'`  
C) `COALESCE(region, 'Unknown')`  
D) `CAST(region AS 'Unknown')`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> `COALESCE` devuelve su primer argumento no NULL, por lo que las regiones NULL pasan a ser `'Unknown'`. `IFNULL` y `NVL` hacen lo mismo con dos argumentos.
>
> `NULLIF(a, b)` hace lo contrario: devuelve NULL cuando `a` es igual a `b` (A).

</details>


## Pregunta 12: Eliminar duplicados *(Difícil)* *(2025 blueprint)*

`bronze.customers` contiene filas duplicadas exactas. Un analista quiere una tabla `silver.customers` depurada con cada fila una sola vez. ¿Qué sentencia lo hace?

A) `CREATE TABLE silver.customers AS SELECT * FROM bronze.customers GROUP BY ALL HAVING COUNT(*) > 1`  
B) `DELETE DUPLICATES FROM bronze.customers`  
C) `CREATE OR REPLACE TABLE silver.customers AS SELECT DISTINCT * FROM bronze.customers`  
D) `CREATE VIEW silver.customers AS SELECT * FROM bronze.customers LIMIT 1`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> `SELECT DISTINCT *` conserva una copia de cada fila idéntica, y `CREATE OR REPLACE TABLE ... AS` almacena el resultado.
>
> La opción A conserva solo las filas que estaban duplicadas. La opción B no es sintaxis válida. Cuando las filas son duplicadas por clave en lugar de idénticas, use `ROW_NUMBER()` con `QUALIFY ... = 1` en su lugar.

</details>


## Pregunta 13: Valores no válidos en una columna numérica *(Media)* *(2025 blueprint)*

Una columna de tipo string `qty_raw` contiene principalmente números, pero a veces contiene texto como `'n/a'`. `CAST(qty_raw AS INT)` hace que la consulta falle. ¿Qué permite que la consulta se ejecute correctamente y convierte los valores no válidos en NULL?

A) `CAST(qty_raw AS STRING)`  
B) `qty_raw::BIGINT`  
C) `INT(qty_raw)`  
D) `try_cast(qty_raw AS INT)`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> `try_cast` devuelve NULL cuando un valor no se puede convertir, en lugar de generar un error. Luego puede tratar los NULL, por ejemplo con `COALESCE` o un filtro `WHERE`.
>
> `::` es una forma abreviada de `CAST`, por lo que falla de la misma manera (B).

</details>


**[← Anterior: Preguntas de práctica sobre Comprensión de Databricks Data + AI Platform](./05-databricks-platform.md) | [↑ Volver a las preguntas de práctica de Data Analyst Associate](./README.md) | [Siguiente: Preguntas de práctica sobre Seguridad de datos →](./07-securing-data.md)**

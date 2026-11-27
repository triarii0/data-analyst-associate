# Preguntas de práctica sobre seguridad de datos

**Peso en el examen: 8 %** — Sección 9 de la guía del examen de octubre de 2025

Las preguntas marcadas como *(2025 blueprint)* cubren habilidades añadidas en la guía del examen de octubre de 2025. Todas las preguntas se verificaron con la documentación de Databricks el 26 de octubre de 2026.

[← Anterior: Gestión de datos](./06-managing-data.md) | [↑ Volver a las preguntas de práctica](./README.md) | [Siguiente: Importación de datos](./08-importing-data.md)

---

## Pregunta 1: Espacio de nombres de tres niveles *(Fácil)*

¿Qué nombre hace referencia correctamente a una tabla en Unity Catalog?

A) `schema.table`  
B) `catalog.schema.table`  
C) `workspace.catalog.table`  
D) `database.table`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> Unity Catalog usa un espacio de nombres de tres niveles: `catalog.schema.object`, donde el objeto puede ser una tabla, una vista, un volumen, una función o un modelo. La forma de dos niveles `schema.table` proviene del metastore de Hive heredado.

</details>


## Pregunta 2: Otorgar acceso de lectura *(Fácil)*

¿Qué sentencia otorga al grupo `finance_analysts` acceso de lectura a una tabla de Unity Catalog?

A) `GRANT READ ON TABLE main.finance.transactions TO finance_analysts`  
B) `GRANT ACCESS ON TABLE main.finance.transactions TO finance_analysts`  
C) `GRANT VIEW ON TABLE main.finance.transactions TO finance_analysts`  
D) `GRANT SELECT ON TABLE main.finance.transactions TO finance_analysts`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> `SELECT` es el privilegio de Unity Catalog para leer una tabla o vista. El grupo también necesita `USE CATALOG` sobre `main` y `USE SCHEMA` sobre `main.finance`.
>
> `READ`, `ACCESS` y `VIEW` no son privilegios de tabla. Los nombres de principal que contienen caracteres especiales, como `@` o `-`, deben ir entre comillas invertidas.

</details>


## Pregunta 3: Eliminar el acceso *(Fácil)*

Se debe eliminar el acceso de un analista a `finance.transactions`. ¿Qué sentencia es correcta?

A) `DELETE GRANT SELECT ON TABLE finance.transactions FROM contractors`  
B) `REVOKE SELECT ON TABLE finance.transactions FROM contractors`  
C) `DROP GRANT SELECT ON TABLE finance.transactions FROM contractors`  
D) `DENY SELECT ON TABLE finance.transactions TO contractors`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> `REVOKE` elimina un privilegio que se había otorgado. Si el privilegio se otorgó en el esquema o catálogo superior, revóquelo allí, porque se hereda.
>
> `DENY` solo se aplica al catálogo heredado `hive_metastore` y no es compatible con Unity Catalog (D).

</details>


## Pregunta 4: Catálogo y esquema predeterminados *(Media)*

Un analista ejecuta `USE CATALOG analytics; USE SCHEMA reporting;`. ¿Qué tabla lee `SELECT * FROM revenue`?

A) `main.default.revenue`  
B) `analytics.default.revenue`  
C) `default.reporting.revenue`  
D) `analytics.reporting.revenue`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> `USE CATALOG` establece el catálogo actual y `USE SCHEMA` establece el esquema actual, por lo que los nombres sin calificar se resuelven como `analytics.reporting.revenue`.

</details>


## Pregunta 5: Una tabla, filas distintas por usuario *(Difícil)*

Una tabla de ventas debe mostrar a cada representante de ventas solo las filas de su propia región, en todas las herramientas y dashboards, sin tablas ni vistas separadas. ¿Qué funcionalidad lo hace?

A) Un filtro de dashboard que configura cada usuario  
B) Una cláusula `WHERE` escrita para cada usuario en cada consulta  
C) Un filtro de filas de Unity Catalog sobre la tabla  
D) Un dashboard distinto por región  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> Un filtro de filas es una UDF de SQL asociada con `ALTER TABLE ... SET ROW FILTER`. Las filas para las que devuelve FALSE se excluyen en tiempo de consulta, en todas las consultas sobre la tabla.
>
> Los filtros de dashboard pueden ser modificados por los espectadores, por lo que no constituyen seguridad (A). Para aplicar la misma regla en muchas tablas, Databricks recomienda políticas ABAC basadas en etiquetas gobernadas.

</details>


## Pregunta 6: Privilegios necesarios para consultar *(Fácil)* *(2025 blueprint)*

Un grupo tiene `SELECT` sobre `main.sales.orders`, pero aún recibe un error de permisos. ¿Qué privilegios faltan probablemente?

A) `USE CATALOG` sobre `main` y `USE SCHEMA` sobre `main.sales`  
B) `MODIFY` sobre la tabla  
C) `CREATE TABLE` sobre el esquema  
D) `ALL PRIVILEGES` sobre el metastore  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> Leer una tabla requiere `SELECT` sobre la tabla, además de `USE CATALOG` y `USE SCHEMA` sobre sus objetos superiores.
>
> `MODIFY` sirve para insertar, actualizar y eliminar (B). `CREATE TABLE` sirve para crear tablas (C).

</details>


## Pregunta 7: Otorgar permisos a nivel de esquema *(Media)* *(2025 blueprint)*

Un responsable de datos ejecuta `GRANT SELECT ON SCHEMA main.gold TO bi_readers`. La semana siguiente se crea una tabla nueva en `main.gold`. ¿Puede `bi_readers` leerla?

A) No, cada tabla nueva necesita su propio otorgamiento  
B) Solo si la tabla nueva es externa  
C) Solo después de que el administrador del metastore lo apruebe  
D) Sí, porque los privilegios otorgados en un esquema se aplican a todas las tablas y vistas actuales y futuras de dicho esquema  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> Los privilegios de Unity Catalog se heredan. Un privilegio otorgado en un catálogo o esquema se aplica a los objetos hijos correspondientes, incluidos los objetos creados posteriormente.
>
> El grupo aún necesita `USE CATALOG` sobre `main` y `USE SCHEMA` sobre `main.gold`.

</details>


## Pregunta 8: Propiedad cuando alguien deja la empresa *(Media)* *(2025 blueprint)*

Un analista que es propietario de varias tablas de producción deja la empresa. ¿Qué se debe hacer con la propiedad?

A) Transferir la propiedad a un grupo, por ejemplo con `ALTER TABLE ... OWNER TO data_owners`  
B) Nada, porque los propietarios no tienen derechos especiales  
C) Eliminar y volver a crear las tablas  
D) Otorgar `SELECT` a todos  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> Todo objeto protegible tiene un propietario, y el propietario tiene todos los privilegios sobre él, incluido el de otorgar privilegios a otros. Transferir la propiedad a un grupo significa que ninguna persona controla el activo por sí sola. Puede transferir la propiedad con `ALTER <securable> ... OWNER TO <principal>` o con el campo **Owner** en Catalog Explorer.
>
> El propietario actual, un administrador del metastore, el propietario del contenedor superior o un usuario con `MANAGE` puede transferir la propiedad.

</details>


## Pregunta 9: El privilegio MANAGE *(Media)* *(2025 blueprint)*

Un líder de equipo debe gestionar quién puede acceder a una tabla sin convertirse en su propietario. ¿Qué privilegio lo permite?

A) `SELECT`  
B) `MODIFY`  
C) `MANAGE`  
D) `BROWSE`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> `MANAGE` permite a un usuario gestionar los privilegios sobre un objeto, transferir su propiedad y eliminarlo sin ser el propietario.
>
> `ALL PRIVILEGES` no incluye `MANAGE`. `BROWSE` permite a los usuarios descubrir objetos y ver sus metadatos sin leer datos (D).

</details>


## Pregunta 10: Enmascarar una columna PII *(Media)* *(2025 blueprint)*

Recursos Humanos quiere que todos, excepto el grupo `hr_admins`, vean `ssn` como `***-**-****` en cada consulta sobre la tabla `employees`. ¿Qué enfoque es adecuado?

A) Ocultar la columna en un dashboard  
B) Quitar `SELECT` a todos  
C) Almacenar la columna en una tabla externa separada  
D) Crear una UDF de SQL que devuelva el valor real solo para los miembros de `hr_admins` y asociarla con `ALTER TABLE employees ALTER COLUMN ssn SET MASK mask_ssn`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> Una máscara de columna es una UDF de SQL que devuelve el valor original o un valor enmascarado en tiempo de consulta. Cada columna puede tener una máscara.
>
> Ocultar una columna en una herramienta no la protege en otras herramientas (A). Quitar `SELECT` bloquea la tabla completa (B).

</details>


## Pregunta 11: Rutas de volúmenes *(Fácil)* *(2025 blueprint)*

Los archivos CSV sin procesar se almacenan en un volumen de Unity Catalog llamado `landing` en `main.raw`. ¿Qué ruta hace referencia a un archivo de dicho volumen?

A) `/mnt/main/raw/landing/orders.csv`  
B) `main.raw.landing.orders.csv`  
C) `s3://Volumes/main/raw/landing/orders.csv`  
D) `/Volumes/main/raw/landing/orders.csv`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> Los volúmenes se sitúan en el tercer nivel del espacio de nombres (`catalog.schema.volume`) y sus archivos usan la ruta `/Volumes/<catalog>/<schema>/<volume>/<path>`.
>
> Leer archivos requiere `READ VOLUME` y escribir requiere `WRITE VOLUME`, además de `USE CATALOG` y `USE SCHEMA`.

</details>


## Pregunta 12: Compartir una consulta guardada de forma segura *(Media)* *(2025 blueprint)*

Un analista comparte una consulta guardada en una carpeta compartida. Sus colegas deben poder ejecutarla pero no modificarla. ¿Qué permiso deben recibir?

A) CAN RUN  
B) CAN MANAGE  
C) CAN EDIT  
D) IS OWNER  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> Los objetos de workspace como consultas, dashboards, notebooks y carpetas se protegen con listas de control de acceso. Para las consultas, los niveles son CAN VIEW, CAN RUN, CAN EDIT y CAN MANAGE. Los objetos de una carpeta heredan los permisos de la carpeta.
>
> Los permisos de objetos de workspace no sustituyen a los privilegios de Unity Catalog. Ejecutar la consulta sigue requiriendo acceso a las tablas que lee.

</details>


## Pregunta 13: Rol de administrador opcional *(Media)* *(2025 blueprint)*

¿Qué rol de administrador de Unity Catalog es opcional?

A) Administrador de cuenta  
B) Administrador de workspace  
C) Administrador del metastore  
D) Ninguno, los tres son obligatorios  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> Los administradores de cuenta y los administradores de workspace son obligatorios en todas las implementaciones. El rol de administrador del metastore es opcional, pero se necesita para algunas tareas, como cambiar la propiedad de objetos que no le pertenecen.
>
> Databricks recomienda asignar el rol de administrador del metastore a un grupo en lugar de a un único usuario.

</details>


## Pregunta 14: Comprobaciones de grupo en una vista dinámica *(Difícil)* *(2025 blueprint)*

Una vista dinámica sobre datos de Unity Catalog debe mostrar los salarios solo a los miembros del grupo de nivel de cuenta `auditors`. ¿Qué función debe usar la vista?

A) `is_account_group_member('auditors')`  
B) `is_member('auditors')`  
C) `current_database() = 'auditors'`  
D) `has_role('auditors')`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> `is_account_group_member()` comprueba la pertenencia a grupos de nivel de cuenta y se recomienda para vistas dinámicas sobre datos de Unity Catalog.
>
> `is_member()` comprueba solo los grupos de nivel de workspace y existe por compatibilidad con Hive metastore, por lo que Databricks desaconseja su uso con Unity Catalog (B). Además, no otorgue a los usuarios acceso a las tablas que lee la vista, ya que podrían eludirla.

</details>


**[← Anterior: Preguntas de práctica sobre gestión de datos](./06-managing-data.md) | [↑ Volver a las preguntas de práctica de Data Analyst Associate](./README.md) | [Siguiente: Preguntas de práctica sobre importación de datos →](./08-importing-data.md)**

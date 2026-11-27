# Preguntas de práctica sobre modelado de datos con Databricks SQL

**Peso en el examen: 5 %** — Sección 8 de la guía del examen de octubre de 2025

Todas las preguntas de esta sección corresponden a la guía del examen de octubre de 2025 y están marcadas como *(2025 blueprint)*. Todas las preguntas se verificaron con la documentación de Databricks y las recomendaciones del blog de Databricks el 26 de octubre de 2026.

[← Anterior: Importación de datos](./08-importing-data.md) | [↑ Volver a las preguntas de práctica](./README.md)

---

## Pregunta 1: Forma de un esquema en estrella *(Fácil)* *(2025 blueprint)*

¿Qué descripción corresponde a un esquema en estrella?

A) Todas las tablas normalizadas a tercera forma normal  
B) Hubs, links y satellites  
C) Una tabla de hechos central con medidas, unida a tablas de dimensiones desnormalizadas  
D) Una tabla ancha con todos los atributos  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> Un esquema en estrella tiene una tabla de hechos central que contiene medidas, como importes y cantidades, unida a tablas de dimensiones como fecha, producto y cliente. Las dimensiones están desnormalizadas, con columnas redundantes, de modo que las consultas necesitan menos uniones.
>
> La opción B describe Data Vault. La opción A describe un modelo 3NF.

</details>


## Pregunta 2: Copo de nieve frente a estrella *(Media)* *(2025 blueprint)*

¿En qué se diferencia un esquema en copo de nieve de un esquema en estrella?

A) Un esquema en copo de nieve no tiene tabla de hechos  
B) Un esquema en copo de nieve almacena datos solo en Snowflake  
C) Un esquema en copo de nieve desnormaliza la tabla de hechos  
D) Un esquema en copo de nieve normaliza las dimensiones en varias tablas relacionadas, por lo que las consultas necesitan más uniones  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> En un esquema en copo de nieve, una dimensión como producto se divide en tablas de producto, categoría y departamento. Esto reduce la redundancia, pero añade uniones. Un esquema en estrella mantiene cada dimensión en una sola tabla desnormalizada, lo cual es más sencillo para los usuarios de BI.
>
> El nombre no tiene relación con el producto Snowflake (B).

</details>


## Pregunta 3: Identificar la tabla de hechos *(Fácil)* *(2025 blueprint)*

Un modelo tiene `dim_customer`, `dim_product`, `dim_date` y una tabla con `order_id`, `customer_key`, `product_key`, `date_key`, `quantity` y `net_amount`. ¿Qué es esa última tabla?

A) Una tabla de hechos  
B) Una tabla de dimensiones  
C) Una tabla puente  
D) Una tabla satellite  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> Contiene medidas numéricas (`quantity`, `net_amount`) y claves foráneas a cada dimensión, lo cual es característico de una tabla de hechos.

</details>


## Pregunta 4: Componentes de Data Vault *(Media)* *(2025 blueprint)*

En un modelo Data Vault, ¿qué tipo de tabla almacena los atributos descriptivos de una entidad de negocio y su historial?

A) Satellite  
B) Hub  
C) Link  
D) Fact  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> Los hubs contienen las entidades centrales del negocio, identificadas por claves de negocio. Los links contienen solo las claves de unión que relacionan los hubs. Los satellites contienen los atributos descriptivos de un hub o un link, junto con su historial.
>
> Los facts pertenecen a los modelos dimensionales, no a Data Vault (D).

</details>


## Pregunta 5: Claves en Data Vault *(Difícil)* *(2025 blueprint)*

¿Qué recomienda el modelado Data Vault como clave primaria de un hub y cómo se puede construir en Databricks SQL?

A) Una columna de identidad de incremento automático  
B) Solo la marca de tiempo de carga  
C) Un UUID aleatorio por carga  
D) Un hash de la clave de negocio, construido con funciones como `md5` o `sha2`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> Data Vault recomienda aplicar hash a las claves de negocio para formar las claves primarias. Databricks SQL proporciona las funciones `hash`, `md5` y SHA para ello.
>
> Un hash es determinista, por lo que la misma clave de negocio siempre produce la misma clave primaria, carga tras carga. Un UUID aleatorio cambia en cada carga (C).

</details>


## Pregunta 6: Modelos y capas medallion *(Media)* *(2025 blueprint)*

Según las recomendaciones de Databricks, ¿dónde suelen ubicarse Data Vault y los esquemas en estrella de Kimball en la arquitectura medallion?

A) Ambos en bronze  
B) Esquemas en estrella en silver; Data Vault en gold  
C) Ninguno encaja en un lakehouse  
D) Data Vault (y modelos similares a 3NF) en silver; esquemas en estrella de Kimball en gold  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> Las recomendaciones de Databricks sitúan los hubs, links y satellites de Data Vault, y otros modelos similares a 3NF, en la capa silver. Los esquemas en estrella estilo Kimball y los data marts encajan en la capa gold, que sirve para la generación de informes.
>
> Bronze contiene los datos sin procesar y sin validar tal como llegaron (A).

</details>


## Pregunta 7: Para qué sirve la capa gold *(Fácil)* *(2025 blueprint)*

¿Qué afirmación describe mejor la capa gold?

A) Datos altamente refinados, modelados de forma dimensional con medidas definidas, que impulsan dashboards, análisis, ML y aplicaciones  
B) Archivos sin procesar exactamente como llegaron  
C) Un área de preparación temporal que se elimina a diario  
D) La capa donde se desactiva la aplicación del esquema  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> Gold contiene vistas altamente refinadas de los datos. La documentación de medallion describe modelarlos con un modelo dimensional, estableciendo relaciones y definiendo medidas. Entre sus usuarios se encuentran analistas de negocio, desarrolladores de BI, científicos de datos y ejecutivos.
>
> Los datos sin procesar están en bronze (B). Silver contiene datos validados, depurados y enriquecidos.

</details>


## Pregunta 8: Por qué añadir un esquema en estrella sobre un Data Vault *(Media)* *(2025 blueprint)*

Un equipo carga datos de origen en un Data Vault en silver. ¿Por qué siguen construyendo esquemas en estrella en gold para los analistas?

A) Data Vault no puede almacenar historial  
B) Data Vault está optimizado para escritura y necesita muchas uniones para responder preguntas de negocio; los esquemas en estrella son más simples y rápidos de consultar para BI  
C) No se pueden construir esquemas en estrella a partir de un vault  
D) Gold debe contener exactamente una tabla  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> Data Vault es un estilo optimizado para escritura que se adapta bien a fuentes que cambian. Responder una pregunta de negocio puede requerir muchas uniones entre hubs, links y satellites. Un modelo dimensional en gold presenta los mismos datos en una forma que los usuarios y las herramientas de BI consultan fácilmente.
>
> Los satellites conservan el historial, por lo que la opción A es falsa.

</details>


## Pregunta 9: Dónde se realiza la depuración *(Fácil)* *(2025 blueprint)*

En la arquitectura medallion, ¿dónde se realizan normalmente la conversión de tipos, el tratamiento de valores NULL y la deduplicación?

A) Silver  
B) Bronze  
C) Gold  
D) En cada dashboard  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> Silver contiene versiones validadas, depuradas y enriquecidas de los datos de bronze. Medallion es un patrón recomendado, no un requisito.
>
> Gold construye modelos de nivel de negocio sobre datos silver depurados (C).

</details>


**[← Anterior: Preguntas de práctica sobre importación de datos](./08-importing-data.md) | [↑ Volver a las preguntas de práctica de Data Analyst Associate](./README.md)**

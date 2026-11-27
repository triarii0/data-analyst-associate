# Preguntas de práctica sobre importación de datos

**Peso en el examen: 5 %** — Sección 3 de la guía del examen de octubre de 2025

Todas las preguntas de esta sección corresponden a la guía del examen de octubre de 2025 y están marcadas como *(2025 blueprint)*. Todas las preguntas se verificaron con la documentación de Databricks el 26 de octubre de 2026. El examen menciona **Delta Sharing**; Databricks lo renombró a **OpenSharing** el 10 de junio de 2026.

[← Anterior: Seguridad de datos](./07-securing-data.md) | [↑ Volver a las preguntas de práctica](./README.md) | [Siguiente: Modelado de datos con Databricks SQL](./09-data-modeling.md)

---

## Pregunta 1: Cargar un archivo pequeño *(Fácil)* *(2025 blueprint)*

Un analista tiene un archivo CSV de 40 MB en su equipo local y quiere convertirlo en una tabla. ¿Qué camino es el más rápido?

A) Escribir un stream de Auto Loader  
B) **New** > **Add or upload data** > **Create or modify a table** y después cargar el archivo  
C) Pedir a un administrador que cree una ubicación externa  
D) Usar Lakehouse Federation  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> La interfaz de carga de archivos crea o sobrescribe una tabla Delta administrada a partir de archivos locales pequeños. Acepta archivos CSV, TSV, JSON, Avro, Parquet y de texto, hasta 10 archivos a la vez con un tamaño total inferior a 2 GB. Necesita un recurso de cómputo en ejecución y permiso para crear tablas en el esquema de destino.
>
> Auto Loader y las ubicaciones externas sirven para archivos que ya están en almacenamiento en la nube (A, C). Federation consulta bases de datos externas (D).

</details>


## Pregunta 2: Archivos que aún no son tablas *(Fácil)* *(2025 blueprint)*

Un analista quiere cargar un conjunto de archivos PDF y JSON para que un equipo los procese más tarde. ¿Dónde deben ir?

A) A una tabla administrada, usando la interfaz de carga de tablas  
B) Al editor de SQL  
C) A un conjunto de datos de dashboard  
D) A un volumen de Unity Catalog, usando **Upload files to a volume**  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> Los volúmenes gobiernan archivos de cualquier formato. El límite de carga de la interfaz es de 5 GB por archivo, y cargar requiere `WRITE VOLUME` sobre el volumen. Más adelante, puede seleccionar archivos en el volumen y hacer clic en **Create table** para crear una tabla administrada a partir de ellos.
>
> La interfaz de carga de tablas acepta solo formatos tabulares (A).

</details>


## Pregunta 3: Volver a ejecutar una carga *(Fácil)* *(2025 blueprint)*

Una sentencia `COPY INTO` programada carga archivos desde almacenamiento en la nube a una tabla Delta. Se ejecuta de nuevo antes de que lleguen archivos nuevos. ¿Qué sucede?

A) Todos los archivos se cargan una segunda vez  
B) La sentencia falla  
C) La tabla se trunca y se vuelve a cargar  
D) Los archivos que ya se cargaron se omiten, por lo que no se crean duplicados  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> `COPY INTO` es idempotente. Omite los archivos de la ubicación de origen que ya ha cargado, por lo que es seguro programarlo.
>
> `COPY_OPTIONS ('mergeSchema' = 'true')` permite que la carga evolucione el esquema de la tabla.

</details>


## Pregunta 4: ¿COPY INTO o Auto Loader? *(Media)* *(2025 blueprint)*

Un bucket de origen recibirá millones de archivos con el tiempo, y su esquema cambia con frecuencia. ¿Qué método de ingesta recomienda Databricks?

A) `COPY INTO`  
B) La interfaz de carga de archivos  
C) `INSERT INTO ... VALUES`  
D) Auto Loader  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> Databricks sugiere `COPY INTO` para archivos en el orden de los miles y Auto Loader para archivos en el orden de los millones o más. Auto Loader también gestiona mejor los esquemas que evolucionan con frecuencia. `COPY INTO` es más sencillo cuando se necesita volver a cargar un subconjunto de archivos vuelto a subir.

</details>


## Pregunta 5: Auto Loader desde SQL *(Media)* *(2025 blueprint)*

Un analista que solo usa SQL quiere cargar de forma incremental en una tabla los archivos JSON nuevos de un volumen, cada hora. ¿Qué sentencia usa Auto Loader?

A) `CREATE VIEW events AS SELECT * FROM json.'/Volumes/main/raw/events'`  
B) `CREATE TABLE events AS SELECT * FROM read_files('/Volumes/main/raw/events')`, volver a ejecutar cada hora  
C) `CREATE OR REFRESH STREAMING TABLE events SCHEDULE EVERY 1 hour AS SELECT * FROM STREAM read_files('/Volumes/main/raw/events', format => 'json')`  
D) `COPY events FROM '/Volumes/main/raw/events'`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> En una tabla de streaming, `STREAM read_files(...)` usa Auto Loader, por lo que cada actualización procesa solo los archivos nuevos. `SCHEDULE EVERY` establece la programación de la actualización.
>
> Volver a ejecutar un CTAS simple vuelve a leer y reescribir todo cada vez (B). La opción D no es sintaxis válida.

</details>


## Pregunta 6: Garantías de Auto Loader *(Media)* *(2025 blueprint)*

¿Por qué Auto Loader puede reanudarse después de un fallo sin cargar archivos dos veces?

A) Elimina los archivos de origen después de leerlos  
B) Registra los archivos descubiertos en su checkpoint, lo que garantiza que cada archivo se procese exactamente una vez  
C) Vuelve a cargar todo el directorio cada vez y elimina los duplicados  
D) Carga solo los archivos cuyos nombres contienen una fecha  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> Auto Loader (la fuente `cloudFiles`) almacena los metadatos de los archivos descubiertos en un almacén clave-valor en su ubicación de checkpoint. Eso garantiza el procesamiento exactamente una vez y, tras un fallo, reanuda desde el checkpoint.
>
> También puede detectar cambios de esquema y rescatar datos inesperados en lugar de perderlos. Nunca elimina los archivos de origen (A).

</details>


## Pregunta 7: Compartir con un socio que no usa Databricks *(Media)* *(2025 blueprint)*

Una empresa debe dar a un socio acceso en vivo y de solo lectura a tres tablas. El socio no usa Databricks. ¿Qué debe usar la empresa?

A) Lakehouse Federation  
B) Delta Sharing (ahora OpenSharing) con el protocolo abierto de compartición  
C) Una exportación CSV semanal por correo electrónico  
D) Partner Connect  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> Con OpenSharing (antes Delta Sharing), un proveedor comparte tablas en una compartición de solo lectura. El protocolo Databricks-to-Open atiende a receptores en cualquier plataforma, autenticados con tokens de portador o federación OIDC. Los datos no se replican, por lo que los receptores ven las actualizaciones casi en tiempo real.
>
> Lakehouse Federation sirve para leer bases de datos externas en Databricks, no para compartir hacia afuera (A).

</details>


## Pregunta 8: Recibir una compartición *(Difícil)* *(2025 blueprint)*

Otro cliente de Databricks comparte tablas con su cuenta mediante compartición Databricks-to-Databricks. ¿Cómo empiezan sus analistas a consultarlas?

A) Ejecutar `COPY INTO` desde el almacenamiento del proveedor  
B) Pedir al proveedor una URL JDBC  
C) Un usuario con `CREATE CATALOG` y `USE PROVIDER` (o un administrador del metastore) crea un catálogo a partir de la compartición y después otorga acceso a los analistas  
D) Cargar los archivos del proveedor mediante la interfaz  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> El receptor crea un catálogo a partir de la compartición en Catalog Explorer, con la CLI o con SQL. Los datos de un catálogo compartido son de solo lectura y no se pueden otorgar privilegios de escritura sobre ellos.
>
> Los datos compartidos se actualizan casi en tiempo real, por lo que no se necesita copiarlos ni cargarlos (A, D).

</details>


## Pregunta 9: Enviar eventos a través de una API *(Media)* *(2025 blueprint)*

Un equipo de aplicaciones quiere enviar registros de eventos desde su servicio directamente a una tabla Delta de Unity Catalog mediante una API, sin ejecutar un bus de mensajes. ¿Qué funcionalidad de Databricks está diseñada para esto?

A) La interfaz de carga de archivos  
B) Zerobus Ingest  
C) Lakehouse Federation  
D) Databricks Marketplace  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> Zerobus Ingest, parte de Lakeflow Connect, es una API de streaming basada en envío (push) que escribe registros directamente en tablas Delta administradas por Unity Catalog. Los clientes usan SDK sobre gRPC, una API REST u otros protocolos compatibles.
>
> La interfaz de carga sirve para archivos manuales (A) y Lakehouse Federation lee bases de datos externas (C).

</details>


## Pregunta 10: Obtener datos de terceros *(Fácil)* *(2025 blueprint)*

Un analista necesita un conjunto de datos público de clima para enriquecer los datos de ventas, sin crear un pipeline de ingesta. ¿Dónde debe buscar primero?

A) Partner Connect  
B) El historial de consultas  
C) Databricks Marketplace  
D) Auto Loader  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> Marketplace enumera productos de datos gratuitos y comerciales de proveedores. Los conjuntos de datos suelen llegar como catálogos de datos tabulares compartidos con OpenSharing (antes Delta Sharing), por lo que no hay nada que copiar ni programar.

</details>


## Pregunta 11: Leer archivos desde S3 con gobernanza *(Difícil)* *(2025 blueprint)*

Un analista quiere cargar archivos CSV desde `s3://acme-raw/orders/` con `COPY INTO`, gobernado por Unity Catalog. ¿Qué debe existir primero?

A) Nada, cualquier usuario puede leer cualquier ruta de S3  
B) Una conexión de Lakehouse Federation a S3  
C) Una ubicación externa de Unity Catalog (o un volumen externo) que cubra la ruta, con privilegios otorgados al analista  
D) Una publicación de Marketplace  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> Unity Catalog gobierna el acceso al almacenamiento en la nube mediante ubicaciones externas, que asocian una ruta con una credencial de almacenamiento, y mediante volúmenes. `COPY INTO` puede cargar desde S3, ADLS, GCS o volúmenes de Unity Catalog.
>
> Lakehouse Federation se conecta a bases de datos y catálogos externos, no a rutas de archivos sin procesar (B).

</details>


**[← Anterior: Preguntas de práctica sobre seguridad de datos](./07-securing-data.md) | [↑ Volver a las preguntas de práctica de Data Analyst Associate](./README.md) | [Siguiente: Preguntas de práctica sobre modelado de datos con Databricks SQL →](./09-data-modeling.md)**

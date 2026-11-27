# Preguntas de práctica de Comprensión de la plataforma Databricks Data + AI

**Peso en el examen: 11 %** — Sección 1 de la guía de examen de octubre de 2025

El PDF de la guía de examen denomina a este dominio «Understanding of Databricks Data Intelligence Platform». La página de certificación utiliza ahora «Databricks Data + AI Platform», que también es el nombre que aparece en la documentación actual. Las preguntas marcadas como *(2025 blueprint)* abordan habilidades añadidas en la guía de examen de octubre de 2025. Todas las preguntas se verificaron con la documentación de Databricks el 26 de octubre de 2026.

[← Anterior: Genie](./04-genie-agents.md) | [↑ Volver a Preguntas de práctica](./README.md) | [Siguiente: Administración de datos](./06-managing-data.md)

---

## Pregunta 1: Conectar una herramienta BI *(Fácil)*

Un equipo usa Tableau y quiere consultar tablas a través de un SQL warehouse de Databricks. ¿Qué método de conexión debe utilizar?

A) La biblioteca Python de Delta Lake  
B) Acceso directo al bucket S3  
C) El controlador JDBC u ODBC con los datos de conexión del warehouse  
D) Consultar una API REST cada 5 minutos  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> Las herramientas BI se conectan a los SQL warehouses mediante los controladores JDBC y ODBC de Databricks. La conexión utiliza el nombre de host del servidor y la ruta HTTP del warehouse, además de un método de autenticación como OAuth o un token de acceso personal.
>
> Leer directamente el almacenamiento en la nube evita la gobernanza de Unity Catalog (B).

</details>


## Pregunta 2: Partner Connect *(Fácil)*

Un equipo quiere conectar una herramienta de ingesta de socios como Fivetran al espacio de trabajo sin configurar manualmente el cómputo y las credenciales. ¿Qué función ayuda?

A) La API REST de Databricks  
B) Delta Sharing  
C) Ubicaciones externas de Unity Catalog  
D) Partner Connect  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> Partner Connect conecta el espacio de trabajo con soluciones de socios seleccionadas desde la interfaz de Databricks. Aprovisiona los recursos de Databricks que necesita el socio, como un SQL warehouse, un service principal y un token de acceso personal.
>
> Partner Connect requiere el plan Premium o superior.

</details>


## Pregunta 3: Eliminar tablas administradas y externas *(Media)*

Un analista elimina una tabla Delta administrada y después elimina una tabla Delta externa. ¿Qué ocurre en cada caso?

A) Ambas eliminaciones borran los archivos de datos y los metadatos  
B) Ambas eliminaciones borran solo los metadatos  
C) Administrada: se eliminan los datos y los metadatos. Externa: solo se eliminan los metadatos y los archivos permanecen en el almacenamiento  
D) Externa: se eliminan los datos y los metadatos. Administrada: solo se eliminan los metadatos  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> Unity Catalog administra el almacenamiento de una tabla administrada, por lo que eliminar la tabla también elimina sus datos. Una tabla externa solo apunta a archivos que usted administra, por lo que al eliminarla se elimina la definición de la tabla y se conservan los archivos.
>
> Por eso `SHOW TABLES` puede dejar de enumerar una tabla externa eliminada mientras sus archivos siguen en el almacenamiento en la nube.

</details>


## Pregunta 4: Metastore frente a catálogo *(Media)*

En Unity Catalog, ¿Cómo se relacionan un metastore y un catálogo?

A) El metastore es el contenedor de nivel superior de una región; los catálogos son objetos dentro de él  
B) Son dos nombres para el mismo objeto  
C) Un catálogo contiene varios metastores  
D) Cada espacio de trabajo tiene su propio metastore y los catálogos existen en el nivel de cuenta  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> Un metastore es el contenedor de nivel superior de los metadatos de Unity Catalog y se crea en el nivel de cuenta para cada región. Los espacios de trabajo de esa región se asocian a él, y contiene los catálogos, que contienen los esquemas, que a su vez contienen tablas, vistas, volúmenes y funciones.

</details>


## Pregunta 5: Se eliminan archivos de una tabla externa *(Difícil)*

Una tabla externa apunta a `s3://company-data/raw/sales/`. Una canalización defectuosa elimina todo lo que hay bajo esa ruta. ¿Qué ocurre cuando los analistas consultan la tabla?

A) La consulta falla, porque los archivos a los que apunta la tabla ya no existen  
B) La tabla devuelve los últimos resultados de la caché indefinidamente  
C) Delta Lake restaura los archivos automáticamente a partir del registro de transacciones  
D) Unity Catalog elimina la tabla automáticamente  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> Los datos de una tabla externa existen solo en su ruta de almacenamiento. Unity Catalog conserva la definición de la tabla, pero una vez que los archivos desaparecen no queda nada que leer, por lo que las consultas fallan.
>
> El registro de transacciones registra los cambios, pero no contiene copias de los datos, por lo que no puede recrear los archivos eliminados (C). Unity Catalog no elimina tablas por sí mismo (D).

</details>


## Pregunta 6: ¿Qué componente hace qué? *(Fácil)* *(2025 blueprint)*

¿Qué componente de la plataforma añade transacciones ACID y viaje en el tiempo a los datos almacenados en almacenamiento de objetos en la nube?

A) Delta Lake  
B) Unity Catalog  
C) Lakeflow Jobs  
D) Databricks SQL  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> Delta Lake es el formato de tabla abierto que añade transacciones ACID, historial de tablas y viaje en el tiempo sobre archivos Parquet en el almacenamiento en la nube.
>
> Unity Catalog gobierna el acceso, el linaje y el descubrimiento (B). Lakeflow Jobs orquesta tareas según una programación o un desencadenante (C). Databricks SQL ofrece SQL warehouses, el editor SQL, paneles y alertas (D).

</details>


## Pregunta 7: Delta Live Tables en la actualidad *(Media)* *(2025 blueprint)*

La guía de examen enumera «Delta Live Tables» como un componente de la plataforma. ¿Cómo se denomina en la documentación actual y qué hace?

A) Lakeflow Jobs; programa notebooks  
B) Delta Sharing; comparte tablas con socios  
C) Photon; acelera las consultas  
D) Lakeflow Spark Declarative Pipelines; se declaran tablas, vistas materializadas y tablas de streaming, y la canalización gestiona las dependencias, las actualizaciones y la calidad de los datos  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> Delta Live Tables (DLT) pasó a llamarse Lakeflow Declarative Pipelines y, el 12 de noviembre de 2025, se renombró Lakeflow Spark Declarative Pipelines. La documentación actual también usa la forma abreviada «Lakeflow pipelines». Usted declara qué debe contener cada conjunto de datos y la canalización determina el orden, las actualizaciones y las expectativas.
>
> En el examen, espere el nombre antiguo. Lakeflow Jobs es el orquestador (A).

</details>


## Pregunta 8: Orquestar tareas *(Fácil)* *(2025 blueprint)*

Un equipo quiere ejecutar cada noche una consulta SQL, después un notebook y después la actualización de una canalización, en ese orden, con reintentos y notificaciones. ¿Qué componente debe usar?

A) Unity Catalog  
B) Lakeflow Jobs  
C) Databricks Marketplace  
D) Delta Lake  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> Lakeflow Jobs orquesta tareas como notebooks, SQL, canalizaciones y alertas de Databricks SQL, con programaciones, dependencias, reintentos y notificaciones.
>
> Los otros componentes almacenan, gobiernan o comparten datos. No programan trabajo.

</details>


## Pregunta 9: El motor de inteligencia de datos *(Media)* *(2025 blueprint)*

La guía de examen menciona un «Data Intelligence Engine» como componente de la plataforma. ¿A qué se refiere?

A) Al motor de consultas que sustituye a Spark  
B) A una base de datos independiente para modelos de IA  
C) A la capa de IA, que Databricks denomina DatabricksIQ, que aprende la semántica de los datos de una organización y alimenta el resto de la plataforma  
D) Al motor de facturación de DBU  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> Cuando Databricks presentó la Data Intelligence Platform en noviembre de 2023, describió un motor de inteligencia de datos denominado DatabricksIQ. Utiliza modelos de IA para comprender la semántica de los datos empresariales. Por ejemplo, genera descripciones y etiquetas en Unity Catalog que hacen que toda la plataforma conozca la jerga, los acrónimos y las métricas de la organización.
>
> Photon es el motor de consultas (A), no el motor de inteligencia de datos.

</details>


## Pregunta 10: Qué significa «Certified» *(Fácil)* *(2025 blueprint)*

En Catalog Explorer, una tabla muestra una insignia de certificación (una marca de verificación). ¿Qué indica al analista?

A) La tabla ha superado una prueba automatizada de calidad de datos  
B) La tabla se ha marcado como un recurso de confianza y recomendado, con la etiqueta `system.certification_status` establecida en `certified`  
C) La tabla está cifrada  
D) La tabla es de solo lectura  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> La certificación es una etiqueta gobernada por el sistema, `system.certification_status`, con los valores `certified` y `deprecated`. Los recursos certificados muestran una marca de verificación y los obsoletos muestran un icono de restricción, de modo que los usuarios pueden distinguir los datos fiables de los que deben dejar de usar.
>
> La insignia es una etiqueta de gobernanza. No ejecuta pruebas, no cifra datos ni cambia permisos.

</details>


## Pregunta 11: Ver el linaje *(Difícil)* *(2025 blueprint)*

Un analista abre una tabla en Catalog Explorer y quiere un diagrama interactivo de sus fuentes upstream y sus consumidores downstream. ¿Qué debe hacer y qué privilegio necesita?

A) Abrir la pestaña History; MODIFY sobre la tabla  
B) Ejecutar `DESCRIBE HISTORY`; propiedad de la tabla  
C) Abrir la pestaña Lineage y hacer clic en **See Lineage Graph**; al menos BROWSE sobre el catálogo principal de la tabla  
D) Abrir la pestaña Permissions; MANAGE sobre la tabla  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> La pestaña Lineage enumera las tablas, los notebooks, los jobs y los paneles relacionados, y **See Lineage Graph** abre el gráfico interactivo. Ver el linaje requiere al menos el privilegio BROWSE sobre el catálogo principal. Ver notebooks, jobs o paneles en el gráfico también requiere permisos de espacio de trabajo sobre ellos.
>
> `DESCRIBE HISTORY` muestra las versiones de la tabla, no el linaje (B).

</details>


## Pregunta 12: Función de Databricks Marketplace *(Fácil)* *(2025 blueprint)*

¿Qué es Databricks Marketplace?

A) Una tienda para comprar más DBU  
B) Una biblioteca de conectores Spark  
C) Un foro de preguntas sobre certificación  
D) Un intercambio abierto donde los proveedores publican productos de datos, como conjuntos de datos, modelos de IA, notebooks y aplicaciones, a los que los consumidores pueden acceder desde su espacio de trabajo  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> Marketplace es un intercambio abierto de productos de datos e IA. Las ofertas pueden ser gratuitas o comerciales, y públicas o privadas. Algunas están disponibles en cuanto se solicitan y se aceptan los términos, mientras que otras requieren la aprobación del proveedor.
>
> Marketplace utiliza Delta Sharing (renombrado OpenSharing el 10 de junio de 2026) para el intercambio seguro, por lo que los datos no se copian en su espacio de trabajo.

</details>


## Pregunta 13: Obtener datos de Marketplace *(Media)* *(2025 blueprint)*

Un analista encuentra un conjunto de datos gratuito en Databricks Marketplace que está disponible al instante. ¿Cómo empieza a consultarlo?

A) Descargar archivos CSV y subirlos a través de la interfaz  
B) Pedir al proveedor que envíe por correo las credenciales de su base de datos  
C) Solicitar acceso desde Marketplace en su espacio de trabajo de Databricks; el conjunto de datos queda entonces disponible como un catálogo que se puede consultar  
D) Ejecutar `COPY INTO` desde el bucket del proveedor  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> Solicitar productos de datos requiere un espacio de trabajo de Databricks. Los conjuntos de datos se suelen entregar como catálogos de datos tabulares, compartidos en directo con OpenSharing (antes Delta Sharing), por lo que no hay que crear una canalización de ingesta.
>
> Explorar el Marketplace público no requiere un espacio de trabajo, pero obtener los datos sí.

</details>


**[← Anterior: preguntas de práctica de Genie](./04-genie-agents.md) | [↑ Volver a las preguntas de práctica de Data Analyst Associate](./README.md) | [Siguiente: preguntas de práctica de Administración de datos →](./06-managing-data.md)**

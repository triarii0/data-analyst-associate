# Examen simulado 2 de Data Analyst Associate — Preguntas

[← Volver al Examen simulado 2](./README.md) | [Preguntas de práctica](../practice-questions/README.md)

---


> [!note] Actualización de la plataforma (oct. de 2026)
> Las preguntas se agrupan según los nueve dominios de la guía del examen de octubre de 2025. Los Genie Spaces pasaron a denominarse **Genie Agents** el 8 de julio de 2026, y **Delta Sharing** ahora es **OpenSharing** (10 de junio de 2026). El examen sigue utilizando los nombres originales. **Delta Live Tables** ahora es **Lakeflow Spark Declarative Pipelines**.

---


## Ejecución de consultas con Databricks SQL y Warehouses (Preguntas 1–9)

## Pregunta 1 *(Difícil)*

Un SQL warehouse Pro está configurado con `max clusters = 3`. ¿Cuándo se inicia un tercer clúster?

A) Cuando los dos clústeres existentes están totalmente utilizados y hay nuevas consultas en espera en la cola  
B) Cuando se envía la primera consulta  
C) Cuando el warehouse ha estado en funcionamiento durante más de 1 hora  
D) Cuando cualquier consulta tarda más de 60 segundos  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> El escalado de varios clústeres en los warehouses Pro se basa en la demanda. Se inicia un nuevo
> clúster solo cuando los clústeres existentes están al límite de su capacidad y las consultas se
> acumulan en la cola. Con `max clusters = 3`, el tercer clúster se inicia cuando el primero y el
> segundo están totalmente cargados. Los clústeres se reducen cuando disminuye la demanda.

</details>



## Pregunta 2 *(Fácil)*

¿Qué permite la federación de consultas de Databricks SQL?

A) Ejecutar la misma consulta en varios SQL warehouses simultáneamente  
B) Compartir consultas entre distintos espacios de trabajo de Databricks  
C) Federar el control de acceso entre varios metastore de Unity Catalog  
D) Consultar fuentes de datos externas (p. ej., MySQL, PostgreSQL) directamente desde Databricks SQL sin copiar los datos  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> La federación de consultas permite que Databricks SQL consulte bases de datos externas (MySQL,
> PostgreSQL, Snowflake, etc.) directamente mediante un objeto de conexión definido en Unity
> Catalog. Los datos no se copian en Databricks: las consultas se envían a la fuente cuando es
> posible y los resultados se recuperan bajo demanda.

</details>



## Pregunta 3 *(Media)*

¿Quién puede crear un SQL warehouse?

A) Cualquier usuario autenticado  
B) Usuarios con el permiso CAN USE sobre un warehouse existente  
C) Administradores del espacio de trabajo o usuarios con permiso de creación de clústeres sin restricciones  
D) Solo los administradores de la cuenta  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> Para crear un SQL warehouse es necesario ser administrador del espacio de trabajo o tener el permiso
> de creación de clústeres sin restricciones. CAN USE solo permite a un usuario ejecutar consultas en un warehouse existente (B).

</details>



## Pregunta 4 *(Difícil)*

Un SQL warehouse está configurado con auto-stop en 30 minutos. Una consulta de larga duración lleva 25 minutos ejecutándose. ¿Qué ocurre al llegar a los 30 minutos?

A) El warehouse se detiene y la consulta se cancela  
B) La consulta se pausa y se reanuda cuando el warehouse se reinicia  
C) El temporizador de auto-stop no se aplica a las consultas activas: el warehouse se detiene solo después de que la consulta finaliza y comienza el período de inactividad  
D) El warehouse envía una advertencia pero sigue funcionando durante otros 30 minutos  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> La detención automática solo se activa cuando el warehouse está inactivo, es decir, cuando no hay
> consultas activas en ejecución. Si hay una consulta en curso, el temporizador de inactividad se
> reinicia. El warehouse no se detendrá mientras haya consultas en ejecución, independientemente de la duración de auto-stop configurada.

</details>



## Pregunta 5 *(Fácil)*

¿Qué sintaxis SQL permite consultar una tabla Delta tal como existía en una versión específica?

A) `SELECT * FROM table VERSION AS OF 5`  
B) `SELECT * FROM table AT VERSION = 5`  
C) `SELECT * FROM table WHERE _version = 5`  
D) `SELECT * FROM table SNAPSHOT VERSION 5`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> `VERSION AS OF <n>` es la sintaxis de viaje en el tiempo de Delta Lake para consultar una versión
> específica. La sintaxis equivalente basada en marca de tiempo es `TIMESTAMP AS OF '<timestamp>'`.
> Ambas son expresiones estándar de viaje en el tiempo de Delta Lake compatibles con Databricks SQL.

</details>



## Pregunta 6 *(Difícil)*

Una función de ventana utiliza la cláusula de marco `ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW`. ¿Qué calcula?

A) El agregado sobre toda la partición, sin tener en cuenta la posición de la fila actual  
B) El agregado entre la primera y la última fila de todo el conjunto de resultados  
C) El agregado sobre una ventana móvil de 30 días  
D) Un agregado acumulativo desde la primera fila de la partición hasta la fila actual  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> `ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW` define un marco que comienza en la
> primera fila de la partición y termina en la fila actual. Combinado con `SUM` o `COUNT`,
> este produce un total acumulativo. `UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING`
> abarcaría toda la partición.

</details>



## Pregunta 7 *(Media)*

`NTILE(4) OVER (ORDER BY revenue DESC)` — ¿qué hace esta función?

A) Devuelve las 4 primeras filas por ingresos  
B) Devuelve los valores de ingresos en los percentiles 25, 50, 75 y 100  
C) Divide el conjunto de resultados en 4 grupos de igual tamaño y asigna a cada fila un número de grupo (1–4)  
D) Cuenta el número de valores de ingresos distintos en cada cuartil  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> `NTILE(n)` divide el conjunto de resultados ordenado en `n` grupos de igual tamaño y asigna a
> cada fila un número de grupo entre 1 y n. Con `NTILE(4)` ordenado por `revenue DESC`, el grupo 1
> contiene los valores del cuartil superior y el grupo 4, los del cuartil inferior. Las filas se
> distribuyen de la forma más uniforme posible entre los grupos.

</details>



## Pregunta 8 *(Difícil)*

¿Qué operador comprueba la igualdad entre dos valores, incluido el caso en que ambos son NULL?

A) `=`  
B) `IS NOT DISTINCT FROM`  
C) `<=>`  
D) Tanto B como C son correctas  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> El operador `=` estándar devuelve NULL (no TRUE) cuando alguno de los operandos es NULL, por lo
> que `NULL = NULL` se evalúa como NULL (no TRUE). Tanto `<=>` (igualdad segura con NULL, Spark SQL)
> como `IS NOT DISTINCT FROM` (SQL estándar) devuelven TRUE cuando ambos lados son NULL. En
> Databricks SQL se admiten ambas sintaxis.

</details>



## Pregunta 9 *(Difícil)*

Un analista escribe: `SELECT region, revenue, DENSE_RANK() OVER (ORDER BY revenue DESC) AS dr FROM sales QUALIFY dr <= 3`. ¿Qué devuelve?

A) Solo las 3 filas con mayores ingresos  
B) Todas las filas en las que el rango denso de los ingresos es 1, 2 o 3: varias filas por rango si hay empates  
C) Un error: `QUALIFY` no se puede usar con `DENSE_RANK()`  
D) Los 3 valores de ingresos distintos más altos, con una fila cada uno  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> `DENSE_RANK()` asigna el mismo rango a las filas empatadas y nunca omite números de rango.
> `QUALIFY dr <= 3` conserva todas las filas cuyo rango denso es 1, 2 o 3. Si tres filas comparten
> el rango 1, se devuelven las tres. Este patrón resulta útil para devolver los N grupos principales incluidos todos los empates.

</details>



## Creación de paneles y visualizaciones (Preguntas 10–16)

## Pregunta 10 *(Fácil)*

Un panel de Databricks SQL contiene un widget de texto con contenido markdown. ¿Cuál es el caso de uso principal de un widget de texto?

A) Añadir documentación estática, instrucciones, encabezados de sección o contexto a un panel  
B) Mostrar un resultado de consulta en vivo en texto con formato  
C) Mostrar automáticamente la marca de tiempo de la última actualización  
D) Incorporar una URL externa en el panel  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> Los widgets de texto muestran contenido markdown estático: encabezados, texto explicativo,
> instrucciones o enlaces. Se usan para añadir contexto y documentación a los paneles. Los widgets
> de texto no ejecutan consultas ni muestran datos en vivo. Para mostrar una métrica única en vivo, usa un widget de contador.

</details>



## Pregunta 11 *(Media)*

La programación de un panel tiene un canal de Microsoft Teams como suscriptor. ¿Qué recibe el canal en cada ejecución programada?

A) Nada; Teams no puede suscribirse a paneles  
B) El texto SQL del panel  
C) Solo una exportación CSV de cada conjunto de datos  
D) Una instantánea PNG en el canal, un enlace al panel y un PDF  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> Los destinos de Slack y Microsoft Teams (configurados por un administrador del espacio de trabajo)
> reciben una instantánea PNG, un enlace y un PDF. Los suscriptores por correo electrónico reciben
> una instantánea PDF, opcionalmente con archivos adjuntos CSV, TSV o Excel.

</details>



## Pregunta 12 *(Difícil)*

Un analista crea un gráfico de barras y quiere que las barras de `revenue > 1,000,000` aparezcan en verde y las demás en gris. ¿Cómo se logra?

A) Este coloreado condicional no es compatible: todas las barras usan un único color  
B) Usar un `CASE WHEN` en SQL para añadir una columna de categoría y, a continuación, usar esa columna como dimensión 'Color' o 'Group By' en el gráfico  
C) Escribir un script de Python para procesar posteriormente los colores del gráfico  
D) Usar la opción 'Threshold' del gráfico para definir rangos de color  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> Para lograr un coloreado condicional, añade una columna de categoría calculada en SQL:
> `CASE WHEN revenue > 1000000 THEN 'High' ELSE 'Normal' END AS revenue_tier`.
> Configura 'Color' o 'Group By' del gráfico como `revenue_tier` y asigna los colores por
> categoría en la configuración del gráfico. Este es el patrón estándar para aplicar estilos
> condicionales a las visualizaciones en Databricks SQL.

</details>



## Pregunta 13 *(Fácil)*

Un filtro de panel debe permitir a los usuarios seleccionar un rango de fechas (fecha de inicio y fecha de fin). ¿Cómo utiliza un conjunto de datos de panel AI/BI un parámetro de rango de fechas?

A) No puede: un rango de fechas debe ser dos parámetros de texto independientes  
B) Solo los SQL warehouses serverless admiten rangos de fechas  
C) Un parámetro de rango de fechas expone `.min` y `.max`, por ejemplo `WHERE order_date BETWEEN :date_range.min AND :date_range.max`  
D) Un rango de fechas necesita una lista desplegable personalizada con valores predefinidos  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> Los parámetros de rango de fechas en los paneles AI/BI crean valores `.min` y `.max` que se usan
> con marcadores de parámetros con nombre. Añadir `OR :date_range IS NULL` permite gestionar la
> selección 'All'. Los paneles no admiten la sintaxis heredada de mustache (`{{ }}`).

</details>



## Pregunta 14 *(Media)*

Un conjunto de datos de panel usa el parámetro `:min_revenue`. El autor quiere que los espectadores vean los datos filtrados a `100000` hasta que cambien el filtro. ¿Cómo se configura?

A) No se pueden establecer valores predeterminados: los espectadores deben elegir un valor primero  
B) En el editor de conjuntos de datos, escribe `100000` en el campo situado bajo el nombre del parámetro y ejecuta la consulta; ese valor pasa a ser el predeterminado  
C) Codificar `100000` directamente en el SQL y mantener `:min_revenue` en un comentario  
D) Usar `COALESCE(:min_revenue, 100000)` y dejar el parámetro sin valor predeterminado  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> Para probar un parámetro de panel, escribes un valor bajo el nombre del parámetro y ejecutas la
> consulta. Al ejecutar la consulta también se guarda ese valor como predeterminado del parámetro,
> de modo que el panel se abre con él.

</details>



## Pregunta 15 *(Media)*

¿Qué estados puede devolver la evaluación de una alerta de Databricks SQL de la última versión?

A) OK, TRIGGERED, ERROR  
B) Solo OK, TRIGGERED, UNKNOWN  
C) GREEN, YELLOW, RED  
D) RUNNING y STOPPED  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> Cada evaluación se resuelve como OK, TRIGGERED o ERROR. Las alertas más recientes ya no utilizan
> el estado UNKNOWN de las alertas heredadas.

</details>



## Pregunta 16 *(Media)*

Un directivo quiere recibir a las 9:00 una instantánea diaria por correo electrónico de un panel de Databricks SQL sin necesidad de iniciar sesión. ¿Qué función ofrece esto?

A) Una alerta SQL con programación diaria  
B) Una integración de Partner Connect con una herramienta de correo electrónico  
C) Una consulta programada que envía los resultados por correo como archivo adjunto CSV  
D) Una suscripción al panel: envía un correo con una captura del panel según una programación  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> Los paneles de Databricks SQL admiten suscripciones: se envía a los destinatarios una imagen
> renderizada (captura de pantalla) del panel según una programación configurada. Los destinatarios
> no necesitan iniciar sesión en Databricks para recibir la instantánea. Esto es distinto de las
> alertas SQL, que solo se activan cuando se cumple una condición de umbral.

</details>



## Análisis de consultas (Preguntas 17–23)

## Pregunta 17 *(Fácil)*

¿Qué hace la aceleración de Photon en Databricks SQL?

A) Comprime los archivos de tablas Delta para reducir los costos de almacenamiento  
B) Sustituye el motor Spark SQL basado en JVM por un motor de ejecución vectorizada nativo en C++ para mejorar el rendimiento de las consultas  
C) Almacena en caché los resultados de las consultas en memoria para su reutilización inmediata  
D) Acelera solo las celdas de notebooks de Python, no las consultas SQL  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> Photon es el motor de consultas vectorizado nativo en C++ de Databricks que sustituye partes del
> motor de ejecución Spark basado en JVM. Está optimizado para cargas de trabajo analíticas de SQL
> y mejora significativamente el rendimiento de los escaneos, agregaciones, uniones y operaciones
> de ordenación sobre grandes conjuntos de datos. Photon está habilitado por defecto en los warehouses Serverless y Pro.

</details>



## Pregunta 18 *(Media)*

Una consulta que llama a un UDF de Python se ejecuta en un SQL warehouse. Su perfil de consulta muestra que solo una pequeña parte del tiempo de tarea se dedicó a Photon. ¿Qué afirmación es correcta?

A) Photon no admite UDF, por lo que esas operaciones recurren al entorno de ejecución Spark y los resultados siguen siendo correctos  
B) La consulta devolvió resultados incorrectos porque Photon no pudo procesar el UDF  
C) Un administrador desactivó Photon en el warehouse  
D) Reescribir la consulta como conjunto de datos de panel hace que Photon ejecute el UDF  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> Photon es el motor predeterminado en todos los SQL warehouses, pero no admite UDF, API de RDD, API de Dataset ni streaming con estado. Las operaciones no compatibles recurren al entorno de ejecución Spark y los resultados siguen siendo correctos.
>
> El perfil de consulta muestra el porcentaje de tiempo de tarea dedicado a Photon, y los operadores de Photon se muestran con un color distinto. Un porcentaje bajo significa que parte del plan se ejecutó fuera de Photon. No significa que los resultados sean incorrectos ni que Photon esté desactivado. Un panel ejecuta el mismo SQL en el mismo motor.

</details>



## Pregunta 19 *(Media)*

¿Cuál es la diferencia entre Query Profile y `EXPLAIN` en Databricks SQL?

A) `EXPLAIN` muestra el plan lógico/físico de la consulta antes de la ejecución; Query Profile muestra métricas reales de ejecución (filas procesadas, tiempo por etapa) después de la ejecución  
B) Son idénticos: ambos muestran el plan de ejecución de la consulta  
C) Query Profile solo funciona para consultas de streaming; `EXPLAIN` solo funciona para consultas por lotes  
D) `EXPLAIN` requiere privilegios de administrador; Query Profile está disponible para todos los usuarios  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> `EXPLAIN` genera el plan de consulta (lógico, analizado, optimizado o físico) antes de que se
> ejecute la consulta: es una herramienta de fase de planificación. Query Profile (disponible en
> Query History) muestra métricas reales de ejecución una vez finalizada: filas leídas por nodo,
> tiempo de ejecución por etapa, bytes intercambiados (shuffle) e información de sesgo. Ambos son útiles para la optimización, pero tienen finalidades distintas.

</details>



## Pregunta 20 *(Fácil)*

¿Cuánto tiempo se mantienen en caché los resultados de las consultas en Databricks SQL de forma predeterminada?

A) 5 minutos  
B) 1 hora  
C) 24 horas  
D) Hasta que se reinicia el SQL warehouse  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> Databricks SQL almacena en caché los resultados de las consultas durante un máximo de 24 horas
> de forma predeterminada. La caché se invalida antes si se escribe en la tabla Delta subyacente
> (se registra una nueva transacción). Esto garantiza que nunca se sirvan resultados en caché para tablas con datos nuevos, independientemente del TTL.

</details>



## Pregunta 21 *(Fácil)*

Un analista optimiza una consulta y la vuelve a ejecutar para medir la mejora. La consulta responde en unos pocos milisegundos cada vez, lo que parece demasiado rápido para ser real. ¿Qué debe ejecutar el analista antes de cronometrar la consulta?

A) Ejecutar la consulta varias veces y conservar el tiempo más rápido  
B) Cambiar a un warehouse de mayor tamaño  
C) `SET use_cached_result = false`  
D) `EXPLAIN` sobre la consulta  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> Una consulta sin cambios sobre tablas sin cambios se responde desde la caché de resultados, por lo que su tiempo de ejecución no dice nada sobre la optimización. `SET use_cached_result = false` desactiva el almacenamiento de resultados en caché para la sesión. Úsalo solo para pruebas y comparativas de rendimiento.
>
> Conservar el tiempo más rápido de varias ejecuciones mide la caché, no la consulta. Cambiar a un warehouse más grande modifica las condiciones de prueba en lugar de desactivar la caché. `EXPLAIN` muestra el plan sin ejecutar la consulta, por lo que no puede medir el tiempo de ejecución.

</details>



## Pregunta 22 *(Difícil)*

Una consulta usa `WHERE product_id NOT IN (SELECT product_id FROM returns)`. La tabla `returns` contiene un valor NULL en `product_id`. ¿Qué resultado devuelve la consulta principal?

A) Todas las filas en las que `product_id` no está entre los valores no NULL de `returns`  
B) El mismo resultado que al usar `NOT EXISTS`  
C) Un error: los valores NULL no se permiten en las subconsultas `NOT IN`  
D) Cero filas: `NOT IN` con una subconsulta que contiene NULL siempre devuelve un resultado vacío  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> Este es un caso clásico de trampa con NULL en SQL. `NOT IN` se evalúa como `value != x AND value != y AND value != NULL`.
> Dado que cualquier comparación con NULL devuelve UNKNOWN (no TRUE), toda la condición `NOT IN`
> se convierte en UNKNOWN para cada fila, y solo las filas TRUE pasan el filtro WHERE. El resultado
> es cero filas. Usa `NOT EXISTS` o filtra los NULL de la subconsulta para evitar este comportamiento.

</details>



## Pregunta 23 *(Media)*

Un analista ejecuta `ALTER TABLE main.sales.events CLUSTER BY (country)` sobre una tabla grande con liquid clustering. Al día siguiente, las consultas que filtran por `country` no son más rápidas. ¿Cómo se explica?

A) Las claves de liquid clustering solo se pueden definir al crear la tabla  
B) El liquid clustering solo funciona para tablas con menos de cuatro columnas  
C) El `ALTER` debe ir seguido de un `VACUUM` para que las claves surtan efecto  
D) Cambiar las claves de agrupación no reescribe los datos existentes. La agrupación se produce cuando se ejecuta `OPTIMIZE` (o `OPTIMIZE ... FULL`, o la optimización predictiva)  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> Las claves de liquid clustering se pueden cambiar en cualquier momento, pero el `ALTER` no reescribe los archivos existentes. La agrupación se produce al ejecutar `OPTIMIZE`, que funciona de forma incremental, o `OPTIMIZE ... FULL`, que reagrupa todo, o automáticamente mediante la optimización predictiva.
>
> El límite de cuatro se aplica al número de claves de agrupación, no a las columnas de la tabla. `VACUUM` elimina los archivos antiguos sin referencias y no agrupa los datos.

</details>



## Desarrollo, uso compartido y mantenimiento de AI/BI Genie Spaces (Preguntas 24–28)

## Pregunta 24 *(Media)*

El equipo de finanzas pregunta a un Genie space «¿Cuáles son los ingresos de EMEA?», «¿Cuáles son los ingresos de APAC?» y así sucesivamente cada semana. El director financiero (CFO) quiere que estas respuestas provengan de una lógica verificada por el equipo de datos, no de SQL generado de nuevo cada vez.

¿Qué debería añadir el autor del space?

A) Una instrucción de texto: «Los ingresos de una región son SUM(invoice_total) filtrado por region»  
B) Una consulta de ejemplo parametrizada titulada «¿Cuáles son los ingresos de una región?» que use `:region_name`, para que se convierta en un recurso de confianza  
C) Una pregunta de referencia (benchmark) para cada región  
D) Una expresión SQL de campo que devuelva el nombre de la región  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> Los recursos de confianza son consultas de ejemplo parametrizadas y funciones SQL de Unity Catalog cuya lógica exacta ha sido verificada por un autor; las respuestas basadas en ellas provienen de esa lógica verificada. Los parámetros usan la sintaxis `:name`. Una instrucción de texto (A) es el último recurso documentado y no produce respuestas verificadas. Los benchmarks (C) solo puntúan la exactitud: nunca alimentan el contexto de Genie. Una expresión SQL de campo (D) define un valor por fila, no una respuesta verificada.

</details>



## Pregunta 25 *(Media)*

Un equipo crea una Databricks App que utiliza un Genie space como recurso de la aplicación. Las preguntas realizadas a través de la aplicación no devuelven filas, mientras que el autor del space ve todos los resultados en la interfaz de Genie. La aplicación no usa autorización de usuario. ¿Cuál es la solución más probable?

A) Conceder `SELECT` al service principal de la aplicación sobre las tablas de Unity Catalog que usa el space  
B) Dar `CAN MANAGE` al service principal de la aplicación sobre el SQL warehouse  
C) Volver a crear el space dentro de la carpeta del espacio de trabajo de la aplicación  
D) Desactivar los filtros de filas de Unity Catalog para la aplicación  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> Todas las integraciones de Genie responden con SQL que Unity Catalog comprueba respecto a la identidad que realiza la llamada. En el caso de un recurso de Databricks App, esa identidad es el **service principal** de la aplicación, salvo que la aplicación use autorización de usuario. La aplicación necesita `Can run` sobre el space y el service principal necesita `SELECT` sobre los datos.
>
> Los permisos de warehouse no conceden acceso a los datos. Trasladar el space no cambia nada en cuanto a permisos. Desactivar los filtros de filas debilitaría la seguridad en lugar de conceder el acceso que falta a la aplicación.

</details>



## Pregunta 26 *(Media)*

Un analista compartió un Genie space con el grupo de operaciones con el permiso `CAN RUN`. Los usuarios de operaciones pueden abrir el space, pero todas las respuestas vuelven vacías. El autor del space ve todos los resultados para las mismas preguntas.

¿Cuál es la causa más probable?

A) Los usuarios de operaciones no tienen `CAN USE` sobre el SQL warehouse  
B) Los usuarios de operaciones no tienen `SELECT` sobre las tablas de Unity Catalog que usa el space  
C) `CAN RUN` no permite hacer preguntas; necesitan `CAN MANAGE`  
D) Genie almacena los resultados en caché por autor  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> Compartir el space no comparte los datos. Genie ejecuta las consultas con las credenciales de warehouse incorporadas del autor, por lo que los consumidores **no** necesitan permisos de warehouse (A es incorrecta), pero cada consulta se comprueba respecto a los permisos de Unity Catalog del usuario que pregunta: sin `SELECT` no obtienen filas. `CAN RUN` (o `CAN VIEW`) basta para usar un space (C) y los resultados no se almacenan en caché por autor (D).

</details>



## Pregunta 27 *(Fácil)*

Un curador debe editar las instrucciones de un Genie space, añadir recursos de confianza y ejecutar benchmarks, pero no debe gestionar con quién se comparte el space. ¿Qué nivel de permiso es el mínimo para esta labor?

A) `CAN VIEW`  
B) `CAN RUN`  
C) `CAN EDIT`  
D) `CAN MANAGE`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> `CAN EDIT` es el nivel mínimo para editar el space, añadir o eliminar recursos de confianza y ejecutar benchmarks.
>
> `CAN VIEW` y `CAN RUN` permiten a los consumidores usar el space, siempre que tengan `SELECT` sobre los datos. `CAN MANAGE` corresponde al propietario o al analista principal y se necesita para tareas como insertar el space y eliminar conversaciones desde la página de supervisión.

</details>



## Pregunta 28 *(Media)*

El sistema de origen cambió los valores de estado de los pedidos de `CXL` a `CANCELLED`. Desde el cambio, Genie responde «¿Cuántos pedidos cancelados hubo la semana pasada?» con cero, aunque los datos son correctos. La columna `status` tiene la coincidencia de entidades (entity matching) habilitada y un comentario de columna obsoleto.

¿Qué pasos de mantenimiento solucionan esto según el procedimiento documentado?

A) Aumentar el tamaño del SQL warehouse y volver a ejecutar la pregunta  
B) Actualizar el comentario de la columna `status` en Unity Catalog, ejecutar **Refresh prompt matching** para la columna y volver a ejecutar los benchmarks  
C) Añadir una instrucción de texto que diga «CXL significa cancelado»  
D) Eliminar el historial de conversaciones del space  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> La habilidad de optimización del examen incluye actualizar los metadatos de Unity Catalog y validar con benchmarks. La coincidencia de entidades almacena valores representativos de la columna, por lo que, tras cambiar los valores, se actualizan con **Refresh prompt matching**; actualizar el comentario de UC corrige la descripción para todos los consumidores; los benchmarks confirman la solución. El tamaño de cómputo (A) no afecta a la interpretación, una instrucción sobre el código antiguo (C) ahora es incorrecta y eliminar el historial (D) solo elimina las pruebas.

</details>



## Comprensión de la plataforma Databricks Data + AI (Preguntas 29–33)

## Pregunta 29 *(Media)*

Un analista usa Python para consultar Databricks SQL de forma programática y recuperar los resultados como un DataFrame. ¿Qué método de conexión es el más adecuado para este caso de uso?

A) Controlador ODBC: el más adecuado para herramientas BI interactivas de escritorio  
B) Controlador JDBC: el más adecuado para aplicaciones basadas en Java  
C) Databricks SQL Connector for Python (`databricks-sql-connector`) mediante el controlador de Python  
D) Delta Sharing: permite el acceso programático a los datos  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> El Databricks SQL Connector for Python (`databricks-sql-connector`) es el controlador recomendado
> para las aplicaciones de Python que consultan Databricks SQL. Utiliza el protocolo Thrift sobre
> HTTPS y devuelve los resultados como estructuras de datos de Python compatibles con pandas. ODBC
> y JDBC suelen ser utilizados por herramientas BI y aplicaciones basadas en JVM, no por scripts de Python.

</details>



## Pregunta 30 *(Media)*

¿Qué significa «adjuntar un espacio de trabajo a un metastore de Unity Catalog»?

A) Vincular el espacio de trabajo para que utilice el metastore especificado para todos los metadatos de catálogos, esquemas y tablas, lo que habilita la gobernanza de Unity Catalog  
B) Conectar la red del espacio de trabajo a la VNet del metastore  
C) Conceder al administrador del espacio de trabajo acceso a todos los catálogos del metastore  
D) Copiar las tablas existentes del Hive metastore del espacio de trabajo en Unity Catalog  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> Un metastore de Unity Catalog es un almacén de metadatos regional. Adjuntar un espacio de trabajo
> a un metastore significa que el espacio de trabajo utiliza ese metastore para todos los objetos
> de Unity Catalog. Varios espacios de trabajo de la misma región pueden compartir un único
> metastore, lo que permite la gobernanza y el uso compartido de datos entre espacios de trabajo mediante un catálogo unificado.

</details>



## Pregunta 31 *(Fácil)*

Antes de crear un panel, un analista encuentra varias tablas `orders` con nombres similares y debe elegir la de confianza. ¿Qué debería comprobar el analista en Catalog Explorer?

A) La tabla con la ejecución de `OPTIMIZE` más reciente en su historial  
B) La tabla con el mayor tamaño de almacenamiento  
C) La tabla cuyo nombre aparece primero en la lista del esquema  
D) La insignia **Certified** de la tabla y su pestaña **Lineage** para confirmar sus fuentes de origen  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> Catalog Explorer muestra una insignia **Certified** (la etiqueta gobernada por el sistema `system.certification_status` establecida en `certified`) o una insignia **deprecated**. La pestaña **Lineage** muestra las tablas, notebooks, jobs y paneles anteriores y posteriores, y para verla se necesita al menos `BROWSE` en el catálogo principal.
>
> El tamaño, el historial de mantenimiento y el orden de los nombres no dicen nada sobre si una tabla es de confianza.

</details>



## Pregunta 32 *(Media)*

¿Cuál es una diferencia clave entre Databricks SQL y ejecutar SQL en un notebook de Databricks?

A) Los notebooks admiten tablas Delta; Databricks SQL no  
B) Databricks SQL ofrece una interfaz diseñada a propósito con historial de consultas, paneles, alertas y consultas programadas; los notebooks requieren código personalizado para estas funciones  
C) Los notebooks usan Unity Catalog; Databricks SQL usa solo el Hive metastore  
D) Databricks SQL solo admite sentencias SELECT; los notebooks admiten todos los comandos SQL  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> Databricks SQL es una interfaz analítica diseñada a propósito con historial de consultas, caché de
> resultados, paneles, alertas y ejecución programada de consultas integrados. El SQL en notebooks
> requiere código personalizado o Jobs para replicar estas funciones. Ambas interfaces admiten las
> mismas tablas Delta subyacentes y Unity Catalog.

</details>



## Pregunta 33 *(Fácil)*

¿Qué componente de Databricks Intelligence Platform ofrece ETL declarativo, en el que se definen tablas, vistas materializadas y tablas de streaming y la plataforma gestiona las dependencias, las actualizaciones y las expectativas de calidad de datos? La guía del examen lo denomina Delta Live Tables.

A) Lakeflow Spark Declarative Pipelines  
B) Lakeflow Jobs  
C) Unity Catalog  
D) Databricks SQL  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> Delta Live Tables pasó a ser Lakeflow Declarative Pipelines y se renombró como **Lakeflow Spark Declarative Pipelines** el 12 de noviembre de 2025. Es el componente de ETL declarativo que gestiona dependencias, actualizaciones y expectativas.
>
> **Lakeflow Jobs** orquesta notebooks, SQL y pipelines mediante programaciones o desencadenantes. **Unity Catalog** goberna los datos y la IA. **Databricks SQL** ofrece SQL warehouses, el editor de SQL, paneles y alertas.

</details>



## Gestión de datos (Preguntas 34–37)

## Pregunta 34 *(Fácil)*

¿Qué comando SQL enumera todas las tablas visibles en el esquema actual dentro de Unity Catalog?

A) `LIST TABLES`  
B) `DESCRIBE SCHEMA`  
C) `SHOW TABLES`  
D) `SELECT * FROM information_schema.tables LIMIT 10`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> `SHOW TABLES` (opcionalmente `SHOW TABLES IN schema_name`) enumera todas las tablas y vistas del
> esquema actual o del especificado. `DESCRIBE SCHEMA` muestra los metadatos del esquema, no el
> contenido de sus tablas. `INFORMATION_SCHEMA.TABLES` también funciona, pero requiere una consulta SELECT completa.

</details>



## Pregunta 35 *(Media)*

Un analista que no es propietario de la tabla `main.crm.customers` intenta añadirle la etiqueta `domain = crm` en Catalog Explorer y la acción se rechaza. El analista ya tiene `SELECT` sobre la tabla. ¿Qué más se necesita?

A) `MODIFY` sobre la tabla, porque las etiquetas modifican los datos de la tabla  
B) Derechos de administrador del espacio de trabajo, porque solo los administradores pueden etiquetar objetos  
C) Nada; las etiquetas solo se pueden añadir con SQL, no en Catalog Explorer  
D) `APPLY TAG` sobre la tabla, además de `USE SCHEMA` y `USE CATALOG` en sus objetos superiores (o la propiedad de la tabla)  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> Etiquetar un objeto requiere ser su propietario o tener `APPLY TAG` sobre él junto con `USE SCHEMA` y `USE CATALOG` en los objetos superiores. Las etiquetas son pares clave-valor en catálogos, esquemas, tablas, columnas, volúmenes y otros objetos, con un máximo de 50 etiquetas por objeto.
>
> Las etiquetas son metadatos, por lo que no intervienen privilegios de datos como `MODIFY`, y etiquetar no está limitado a los administradores. En Catalog Explorer, abre la página **Overview** del objeto y haz clic en **Add tags** bajo **Tags**. También funciona SQL, por ejemplo `SET TAG ON TABLE main.crm.customers domain = crm`.

</details>



## Pregunta 36 *(Fácil)*

¿Qué información está disponible en `INFORMATION_SCHEMA.COLUMNS` para un esquema de Unity Catalog?

A) El número actual de filas de cada tabla  
B) Los nombres de columna, los tipos de datos, la nulabilidad y la posición ordinal de todas las tablas del esquema  
C) El historial de acceso de cada columna  
D) Las estadísticas por columna utilizadas por el optimizador de consultas  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> `INFORMATION_SCHEMA.COLUMNS` es una vista de esquema de información SQL estándar que enumera
> todas las columnas de las tablas del esquema, incluidos el nombre de la columna, el tipo de
> datos, la posición ordinal y si la columna admite valores NULL. Es la forma estándar de
> inspeccionar programáticamente los esquemas de tablas en Unity Catalog.

</details>



## Pregunta 37 *(Media)*

¿Cuál es la diferencia entre `COALESCE(a, b, c)` e `IFNULL(a, b)`?

A) Son idénticas: `IFNULL` es solo un alias de `COALESCE`  
B) `COALESCE` acepta dos o más argumentos y devuelve el primero que no sea NULL; `IFNULL` acepta exactamente dos argumentos y devuelve el segundo si el primero es NULL  
C) `IFNULL` es solo para tipos numéricos; `COALESCE` funciona con todos los tipos  
D) `COALESCE` cortocircuita la evaluación; `IFNULL` siempre evalúa todos los argumentos  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> `COALESCE(a, b, c, ...)` acepta dos o más argumentos y devuelve el primer valor no NULL de la
> lista. `IFNULL(a, b)` es una forma abreviada de dos argumentos que devuelve `b` cuando `a` es NULL.
> `IFNULL(a, b)` es funcionalmente equivalente a `COALESCE(a, b)`. `NVL` (derivado de Oracle) es
> otro sinónimo de la forma de dos argumentos, también compatible con Databricks SQL.

</details>



## Protección de datos (Preguntas 38–41)

## Pregunta 38 *(Media)*

En Unity Catalog, ¿qué permite al usuario la sentencia `GRANT USE SCHEMA ON SCHEMA catalog.schema TO user`?

A) Resolver y acceder a los objetos del esquema: se requiere antes de que surta efecto cualquier privilegio a nivel de tabla  
B) Crear nuevas tablas en el esquema  
C) Eliminar el esquema y su contenido  
D) Conceder a otros usuarios acceso al esquema  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> `USE SCHEMA` es un privilegio previo necesario en Unity Catalog. Sin él, un usuario no puede
> resolver ningún objeto del esquema aunque tenga SELECT sobre tablas individuales. También se
> requiere `USE CATALOG` en el catálogo principal. Estos privilegios forman una jerarquía: catálogo
> → esquema → tabla.

</details>



## Pregunta 39 *(Difícil)*

Un ingeniero de datos quiere que los analistas lean la columna `email` de una tabla pero nunca vean los valores reales de `ssn`. ¿Qué enfoque de Unity Catalog permite esto?

A) Un filtro de filas sobre la tabla  
B) Una máscara de columna sobre `ssn` (o una vista que excluya `ssn`, con `SELECT` concedido sobre la vista)  
C) Un clon de la tabla que copie todas las columnas  
D) Ocultar la columna `ssn` en un panel  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> La protección a nivel de columna en Unity Catalog utiliza máscaras de columna (una UDF de SQL
> asociada mediante `ALTER TABLE ... ALTER COLUMN ... SET MASK`) o vistas que exponen solo las
> columnas permitidas. Un filtro de filas limita las filas, no las columnas (A). Ocultar una columna en una herramienta no la protege (D).

</details>



## Pregunta 40 *(Media)*

El propietario de `main.finance.ledger` ha dejado la empresa y la tabla debe seguir siendo administrable. ¿Cuál es la solución recomendada?

A) Conceder `ALL PRIVILEGES` sobre la tabla al responsable del antiguo propietario  
B) Eliminar la tabla y volver a crearla con un nuevo propietario  
C) Un administrador del metastore, el propietario del esquema principal o un usuario con `MANAGE` ejecuta `ALTER TABLE main.finance.ledger OWNER TO` para asignar un grupo como `finance-owners`  
D) Esperar a que el administrador del espacio de trabajo se convierta automáticamente en el propietario  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> Cada objeto tiene un propietario, que dispone de todos los privilegios sobre él. La propiedad puede transferirla el propietario actual, un administrador del metastore, el propietario del contenedor principal o un usuario con `MANAGE`. Transferirla a un **grupo** evita que una futura baja deje la tabla sin propietario.
>
> `ALL PRIVILEGES` no incluye `MANAGE` ni transfiere la propiedad. Eliminar y volver a crear la tabla implica riesgo de pérdida de datos y descarta su historial y sus permisos concedidos. La propiedad nunca se transfiere automáticamente.

</details>



## Pregunta 41 *(Media)*

Un analista comparte una consulta guardada con un colega con acceso 'Can View'. ¿Qué puede hacer el colega con la consulta?

A) Editar el SQL y guardar los cambios  
B) Eliminar la consulta  
C) Compartir la consulta con otros usuarios  
D) Ejecutar la consulta y ver los resultados, pero no modificar el SQL ni guardar cambios  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> 'Can View' sobre una consulta guardada permite al destinatario abrir y leer el SQL, ejecutar la
> consulta y ver los resultados. No puede modificar el SQL, renombrar, eliminar ni compartir la
> consulta con otros. Se requiere el permiso 'Can Edit' para realizar y guardar cambios en la consulta.

</details>



## Importación de datos (Preguntas 42–43)

## Pregunta 42 *(Fácil)*

Una organización asociada en Databricks comparte tablas en vivo con tu equipo mediante Delta Sharing. ¿Cómo empieza tu equipo a consultarlas?

A) Descargar los archivos Parquet del socio y cargarlos con `COPY INTO`  
B) Pedir al socio una cadena de conexión JDBC a su SQL warehouse  
C) Configurar un conector administrado de Lakeflow Connect al espacio de trabajo del socio  
D) Un usuario con `CREATE CATALOG` y `USE PROVIDER` (o un administrador del metastore) crea un catálogo a partir del recurso compartido y, a continuación, concede acceso al equipo. Los datos compartidos son de solo lectura  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> Delta Sharing (ahora denominado OpenSharing) comparte una colección de tablas de solo lectura de un proveedor a un receptor sin replicación, por lo que los receptores ven las actualizaciones del proveedor casi en tiempo real. En el uso compartido de Databricks a Databricks, un usuario con `CREATE CATALOG` y `USE PROVIDER` crea un catálogo a partir del recurso compartido y concede acceso a sus usuarios.
>
> Copiar archivos o conectarse mediante JDBC renuncia al recurso compartido en vivo y gobernado y crea copias que deben mantenerse sincronizadas. Los conectores administrados ingieren datos de aplicaciones SaaS y bases de datos, no del recurso compartido de otra organización.

</details>



## Pregunta 43 *(Media)*

Una aplicación debe enviar registros individuales directamente a una tabla Delta administrada de Unity Catalog, sin bus de mensajes ni archivos de aterrizaje. ¿Qué ruta de ingesta se ajusta a esto?

A) Zerobus Ingest  
B) Databricks Marketplace  
C) Lakehouse Federation  
D) Un `COPY INTO` programado  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> **Zerobus Ingest**, parte de Lakeflow Connect, es una API de streaming basada en envío (push) que escribe registros en tablas Delta administradas de Unity Catalog, sin zona de aterrizaje de archivos ni bus de mensajes intermedio.
>
> Marketplace entrega productos de datos de terceros como catálogos compartidos. Lakehouse Federation consulta una base de datos externa in situ. `COPY INTO` carga archivos desde el almacenamiento en la nube, por lo que necesita los archivos de aterrizaje que este escenario descarta.

</details>



## Modelado de datos con Databricks SQL (Preguntas 44–45)

## Pregunta 44 *(Fácil)*

En un modelo Data Vault, ¿qué tipo de tabla contiene los atributos descriptivos de una entidad de negocio junto con su historial?

A) Hub  
B) Link  
C) Satellite  
D) Tabla de hechos  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> Las **Satellites** contienen los atributos descriptivos de un hub o un link, junto con su historial.
>
> Un **hub** contiene una entidad de negocio principal identificada por su clave de negocio. Un **link** contiene solo las claves que relacionan los hubs, como una tabla de hechos sin hechos. Las tablas de hechos pertenecen a modelos dimensionales (estrella o copo de nieve), no a Data Vault.

</details>



## Pregunta 45 *(Media)*

Según las recomendaciones de Databricks, ¿dónde suelen situarse los modelos Data Vault y los esquemas de estrella de estilo Kimball en la arquitectura medallón?

A) Ambos pertenecen a Bronze  
B) Esquemas de estrella en Silver y Data Vault en Gold  
C) Data Vault en Bronze y esquemas de estrella en Silver  
D) Data Vault en Silver y esquemas de estrella en Gold  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> Databricks sitúa las tablas hub, link y satellite de Data Vault en la capa **Silver**, junto a los modelos de tipo 3NF, y los esquemas de estrella de Kimball y los data marts en la capa **Gold**.
>
> Bronze contiene las tablas de aterrizaje y preparación con la forma de la fuente. Data Vault está optimizado para escrituras y necesita muchas uniones para responder preguntas de negocio, por lo que los equipos publican esquemas de estrella sobre él en Gold para BI.

</details>



## Clave de respuestas

| # | Respuesta | # | Respuesta | # | Respuesta |
|---|--------|---|--------|---|--------|
| 1 | A | 16 | D | 31 | D |
| 2 | D | 17 | B | 32 | B |
| 3 | C | 18 | A | 33 | A |
| 4 | C | 19 | A | 34 | C |
| 5 | A | 20 | C | 35 | D |
| 6 | D | 21 | C | 36 | B |
| 7 | C | 22 | D | 37 | B |
| 8 | D | 23 | D | 38 | A |
| 9 | B | 24 | B | 39 | B |
| 10 | A | 25 | A | 40 | C |
| 11 | D | 26 | B | 41 | D |
| 12 | B | 27 | C | 42 | D |
| 13 | C | 28 | B | 43 | A |
| 14 | B | 29 | C | 44 | C |
| 15 | A | 30 | A | 45 | D |

## Registro de rendimiento por sección

| Dominio (peso de oct. de 2025) | Preguntas | Tu puntuación | Porcentaje |
| ------ | --------- | ---------- | ---------- |
| Ejecución de consultas con Databricks SQL y Warehouses (20 %) | 1–9 (9 preguntas) | ___/9 | ___% |
| Creación de paneles y visualizaciones (16 %) | 10–16 (7 preguntas) | ___/7 | ___% |
| Análisis de consultas (15 %) | 17–23 (7 preguntas) | ___/7 | ___% |
| Desarrollo, uso compartido y mantenimiento de AI/BI Genie Spaces (12 %) | 24–28 (5 preguntas) | ___/5 | ___% |
| Comprensión de la plataforma Databricks Data + AI (11 %) | 29–33 (5 preguntas) | ___/5 | ___% |
| Gestión de datos (8 %) | 34–37 (4 preguntas) | ___/4 | ___% |
| Protección de datos (8 %) | 38–41 (4 preguntas) | ___/4 | ___% |
| Importación de datos (5 %) | 42–43 (2 preguntas) | ___/2 | ___% |
| Modelado de datos con Databricks SQL (5 %) | 44–45 (2 preguntas) | ___/2 | ___% |
| **Total** | **45 preguntas** | **___/45** | **___%** |

---


**[← Volver al examen simulado](./README.md)** | **[← Volver a Recursos](../README.md)** | **[← Volver a Data Analyst Associate](../../README.md)**

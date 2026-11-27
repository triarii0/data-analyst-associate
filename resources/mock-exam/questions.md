# Examen simulado 1 de Data Analyst Associate — Preguntas

[← Volver al examen simulado](./README.md) | [Preguntas de práctica](../practice-questions/README.md)

---

> [!note] Actualización de la plataforma (oct. de 2026)
> Las preguntas se agrupan según los nueve dominios de la guía del examen de octubre de 2025. Genie Spaces pasó a denominarse **Genie Agents** el 8 de julio de 2026, y **Delta Sharing** pasó a ser **OpenSharing** el 10 de junio de 2026. El examen sigue utilizando los nombres originales. **Delta Live Tables** pasó a ser **Lakeflow Spark Declarative Pipelines**.

---

## Ejecución de consultas con Databricks SQL y SQL warehouses (Preguntas 1–9)

## Pregunta 1 *(Fácil)*

¿Qué tipo de SQL warehouse ofrece un inicio casi instantáneo sin retraso de arranque en frío?

A) Classic  
B) Pro  
C) Serverless  
D) Todos los tipos de warehouse tienen un inicio instantáneo  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> Los SQL warehouses Serverless ofrecen un inicio casi instantáneo porque Databricks administra la
> infraestructura de cómputo de antemano. Los warehouses Classic y Pro requieren entre 2 y 5 minutos
> para aprovisionar los nodos del clúster antes de que puedan ejecutarse las consultas.

</details>


## Pregunta 2 *(Media)*

Un equipo necesita 8 sesiones concurrentes de analistas más picos de tráfico de paneles sin que se acumule una cola. ¿Qué configuración de warehouse admite esto mejor?

A) Warehouse Classic con Scaling establecido en un mínimo y un máximo de 1 clúster  
B) Un warehouse cuyo máximo de Scaling permite más de un clúster, de modo que agrega clústeres cuando hay carga  
C) Un warehouse X-Large con 1 clúster, porque un tamaño mayor elimina las colas  
D) Un clúster personal de uso general para cada analista  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> Scaling (el número mínimo y máximo de clústeres) controla la concurrencia. Los warehouses Pro y
> Classic agregan clústeres cuando aumenta la carga en cola, y los warehouses serverless escalan con
> Intelligent Workload Management. Databricks recomienda aproximadamente un clúster por cada 10
> consultas concurrentes. Un tamaño mayor reduce la latencia de cada consulta, pero no aumenta la
> concurrencia (C).

</details>


## Pregunta 3 *(Fácil)*

¿Qué tipos de SQL warehouse pueden ejecutar consultas de Lakehouse Federation contra un catálogo externo?

A) Classic y Pro  
B) Todos los tipos de warehouse por igual  
C) Solo Classic  
D) Pro y Serverless  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> Lakehouse Federation requiere un SQL warehouse Pro o Serverless (versión 2023.40 o superior).
> Los warehouses Classic no pueden ejecutar consultas federadas.

</details>


## Pregunta 4 *(Fácil)*

Cuando dos filas tienen el mismo valor, ¿qué función produce huecos en la secuencia de números de rango?

A) `RANK()` — las filas empatadas reciben el mismo rango; el siguiente rango se omite  
B) `ROW_NUMBER()` — los empates producen huecos  
C) `DENSE_RANK()` — siempre produce huecos entre todos los rangos  
D) Tanto `RANK()` como `ROW_NUMBER()` producen huecos en caso de empate  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> `RANK()` asigna el mismo número de rango a las filas empatadas y luego omite el siguiente valor
> de rango (p. ej., 1, 1, 3). `ROW_NUMBER()` asigna números secuenciales únicos independientemente
> de los empates: sin huecos ni duplicados. `DENSE_RANK()` asigna el mismo rango a las filas
> empatadas, pero nunca omite valores (p. ej., 1, 1, 2).

</details>


## Pregunta 5 *(Difícil)*

Una consulta usa `SUM(revenue) OVER (PARTITION BY region)` sin cláusula `ORDER BY`. ¿Qué recibe cada fila?

A) Un total acumulado que se incrementa dentro de la región  
B) El valor individual de `revenue` de cada fila, sin modificaciones  
C) NULL: `ORDER BY` es obligatorio para todas las funciones de ventana  
D) La suma total de toda la región: el mismo valor para cada fila de esa región  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> Sin `ORDER BY` en la especificación de ventana, `SUM() OVER (PARTITION BY ...)` calcula el
> agregado sobre toda la partición y devuelve ese único valor para cada fila de la partición.
> Agregar `ORDER BY` cambia el marco a una suma acumulada (progresiva) desde la primera fila hasta
> la fila actual.

</details>


## Pregunta 6 *(Fácil)*

¿Qué tipo de JOIN devuelve filas de la tabla izquierda solo cuando no existe ninguna coincidencia en la tabla derecha?

A) LEFT JOIN  
B) LEFT JOIN con `WHERE right_key IS NULL` — esta es la única forma  
C) LEFT ANTI JOIN  
D) EXCEPT  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> `LEFT ANTI JOIN` devuelve explícitamente las filas de la tabla izquierda para las que no existe
> ninguna fila coincidente en la tabla derecha. Es semánticamente equivalente a
> `LEFT JOIN ... WHERE right_key IS NULL`, pero más legible y a veces más eficiente. `EXCEPT` opera
> sobre diferencias de conjuntos de filas completas, no sobre condiciones de combinación.

</details>


## Pregunta 7 *(Fácil)*

¿Cuál es la diferencia entre `COLLECT_LIST(product)` y `COLLECT_SET(product)`?

A) `COLLECT_LIST` incluye valores duplicados; `COLLECT_SET` devuelve solo valores únicos  
B) `COLLECT_LIST` conserva el orden de inserción; `COLLECT_SET` ordena alfabéticamente  
C) Son idénticas: ambas devuelven todos los valores del grupo  
D) `COLLECT_SET` es más rápida; `COLLECT_LIST` se usa solo para conjuntos de datos grandes  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> `COLLECT_LIST` agrega todos los valores, incluidos los duplicados, en un arreglo.
> `COLLECT_SET` devuelve solo valores distintos. Ninguna de las dos garantiza un orden específico
> de los elementos en el arreglo resultante.

</details>


## Pregunta 8 *(Media)*

La tabla A tiene 100 filas y la tabla B tiene 50 filas. Se realiza un `CROSS JOIN`. ¿Cuántas filas hay en el resultado?

A) 100 filas  
B) 150 filas  
C) 5,000 filas  
D) 50 filas  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> Un `CROSS JOIN` produce el producto cartesiano de ambas tablas: cada fila de A se combina con
> cada fila de B. 100 × 50 = 5,000 filas. Las combinaciones cruzadas son intencionales cuando se
> necesitan todas las combinaciones (p. ej., generar combinaciones de fecha y producto).

</details>


## Pregunta 9 *(Media)*

Un analista necesita una tabla de resumen que se actualice cada noche sin ejecutar la consulta manualmente. ¿Cuál es el enfoque más adecuado?

A) Abrir Databricks SQL cada mañana y ejecutar la consulta manualmente  
B) Crear una vista materializada con una actualización nocturna mediante `SCHEDULE CRON`  
C) Escribir un script de Python que se ejecute en la computadora del analista a medianoche  
D) Usar Lakeflow Spark Declarative Pipelines: es la única opción compatible con actualizaciones automatizadas  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> Una vista materializada puede crearse desde un SQL warehouse con una cláusula `SCHEDULE`, y
> Databricks ejecuta la actualización automáticamente. Los Lakeflow Jobs también pueden programar
> SQL, por lo que los pipelines (las «Delta Live Tables» del examen, ahora Lakeflow Spark
> Declarative Pipelines) no son la única opción (D).

</details>


## Creación de paneles y visualizaciones (Preguntas 10–16)

## Pregunta 10 *(Fácil)*

¿Qué widget de panel está diseñado para mostrar de forma destacada un único valor de KPI, como "Total Revenue: $1.2M"?

A) Widget de tabla  
B) Widget de texto  
C) Widget de contador  
D) Widget de tabla dinámica  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> Los widgets de contador están diseñados específicamente para mostrar un único valor de métrica
> destacado. Se usan típicamente para KPI como los ingresos totales, el número de usuarios activos
> o el volumen diario de pedidos. Un widget de texto muestra markdown estático; un widget de tabla
> muestra datos tabulares.

</details>


## Pregunta 11 *(Media)*

¿Cuándo es un gráfico circular la visualización más adecuada?

A) Cuando se muestran partes de un todo que suman el 100 %, con menos de 6 segmentos  
B) Cuando se muestra la tendencia de una métrica a lo largo del tiempo  
C) Cuando se comparan valores absolutos entre muchas categorías  
D) Cuando uno o más valores son negativos  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> Los gráficos circulares funcionan mejor para relaciones parte-todo con pocas categorías
> (normalmente menos de 6). Con más categorías, los segmentos se vuelven demasiado pequeños para
> distinguirlos. Los gráficos circulares no son adecuados para series temporales, valores
> negativos ni comparaciones en las que importa la magnitud precisa.

</details>


## Pregunta 12 *(Fácil)*

¿Cómo se configura una programación de actualización en un panel de Databricks SQL?

A) En la configuración del panel, establecer el intervalo de actualización y asignar un SQL warehouse  
B) Crear un Databricks Job que llame a la API del panel con el intervalo deseado  
C) Habilitar la actualización automática en el notebook que proporciona los datos del panel  
D) Los paneles no pueden tener programaciones de actualización: solo se pueden programar consultas individuales  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> Los paneles de Databricks SQL tienen una configuración de programación integrada. Desde la
> configuración del panel, establezca el intervalo (p. ej., diariamente a las 8:00) y seleccione
> qué SQL warehouse ejecutará las consultas subyacentes. No se requiere ningún Job ni notebook
> externo.

</details>


## Pregunta 13 *(Fácil)*

El autor de una alerta quiere que una alerta de SQL publique en un canal de Slack, pero no aparece ninguna opción de Slack en la lista de notificaciones de la alerta. ¿Qué se necesita?

A) Pegar la contraseña del canal de Slack en la consulta de la alerta  
B) Convertir la alerta en un panel  
C) Slack nunca es compatible; usar SMS  
D) Un administrador del workspace debe crear primero un destino de notificaciones de Slack  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> Los administradores del workspace configuran destinos de notificaciones (como Slack, Microsoft
> Teams y webhooks). Luego, los autores de alertas seleccionan usuarios o destinos en la
> configuración de notificaciones de la alerta.

</details>


## Pregunta 14 *(Media)*

Se comparte un panel AI/BI con un usuario que tiene el permiso CAN VIEW / CAN RUN. ¿Qué puede hacer ese usuario?

A) Editar el SQL del conjunto de datos  
B) Ver el panel publicado e interactuar con sus filtros, pero no editar el borrador  
C) Agregar nuevos widgets de visualización  
D) Cambiar quién más puede acceder al panel  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> Los espectadores de un panel AI/BI publicado (antes un panel de Lakeview) pueden abrirlo y usar
> sus filtros. Editar el borrador requiere CAN EDIT, y cambiar permisos requiere CAN MANAGE.

</details>


## Pregunta 15 *(Fácil)*

Un filtro de panel pasa una fecha seleccionada a un conjunto de datos de un panel AI/BI. ¿Qué fragmento de SQL usa correctamente este parámetro?

A) `WHERE order_date = :selected_date`  
B) `WHERE order_date = {{selected_date}}`  
C) `WHERE order_date = @selected_date`  
D) `WHERE order_date = $selected_date`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> Los paneles AI/BI usan marcadores de parámetros con nombre (`:name`). Los paneles no admiten la
> sintaxis heredada de «mustache» (`{{ }}`), que solo funciona en el editor de SQL heredado y debe
> convertirse antes de usarla en un panel.

</details>


## Pregunta 16 *(Media)*

Un analista necesita notificar al equipo de operaciones cuando el retraso diario de pedidos sin procesar supera los 10,000. La notificación debe emitirse a los pocos minutos de cruzarse el umbral y enviarse a un canal de Slack.

¿Qué función se ajusta mejor?

A) Programar la actualización del panel cada hora y depender de que los miembros del equipo lo revisen  
B) Crear una alerta de SQL sobre una consulta que devuelve `unprocessed_order_count` y configurarla para que se evalúe cada 5 minutos con un umbral de `> 10000`; enrutarla a un destino de webhook de Slack  
C) Crear un panel con un mosaico de KPI que se pone rojo cuando el recuento supera 10,000  
D) Usar un Lakeflow Job que ejecute el SQL cada noche y envíe un resumen por correo electrónico  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> Las alertas de Databricks SQL son la vía documentada para las notificaciones basadas en umbrales:
> evalúan un resultado de consulta según una programación (hasta 1 minuto), aplican un comparador
> (`>`, `<`, `==`) y lo enrutan a correo electrónico, Slack o cualquier destino de webhook. A y C
> son pasivas: requieren que alguien las revise. D es demasiado lenta (nocturna) y no se basa en
> umbrales.

</details>


## Análisis de consultas (Preguntas 17–23)

## Pregunta 17 *(Media)*

¿Qué función de Databricks SQL ayuda a identificar las consultas más costosas por costo y duración?

A) Registro de transacciones de Delta  
B) Query History: muestra el tiempo de ejecución, los bytes examinados y el usuario  
C) Registros de eventos del clúster  
D) Seguimiento de experimentos de MLflow  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> Query History de Databricks SQL realiza un seguimiento de todas las consultas con su tiempo de
> ejecución, bytes examinados, tiempo en cola y el usuario que las ejecutó. Los administradores
> pueden filtrar por warehouse, usuario e intervalo de tiempo para identificar consultas costosas o
> lentas.

</details>


## Pregunta 18 *(Media)*

Después de que un analista agregue una combinación con la tabla `customers`, una consulta de panel se vuelve más lenta y sus totales de ingresos son mayores de lo esperado. En el perfil de consulta, el operador de combinación genera unas 50 veces más filas que la entrada `orders`. ¿Cuál es la causa más probable?

A) Photon está desactivado en el warehouse  
B) La caché de resultados de consulta devolvió resultados obsoletos  
C) El warehouse es demasiado pequeño para la combinación  
D) Una combinación explosiva: la clave de combinación no es única en el lado de `customers`, por lo que cada pedido coincide con muchas filas  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> Cuando las filas que salen de una combinación superan con mucho a sus entradas, la combinación es
> «explosiva». Las claves duplicadas en el lado derecho multiplican las filas, lo que infla tanto
> el tiempo de ejecución como los totales. Corrija las claves de combinación, por ejemplo,
> combinando por la clave completa o desduplicando antes la dimensión.
>
> Databricks señala las combinaciones explosivas y los escaneos completos de tabla como problemas
> comunes que el perfil de consulta ayuda a detectar. Desactivar Photon o usar un warehouse
> pequeño cambia la velocidad, pero ninguna de estas dos acciones crea filas adicionales. Un
> resultado en caché sería más rápido, no más lento, y se invalida cuando cambian las tablas.

</details>


## Pregunta 19 *(Media)*

Una consulta SQL en Databricks SQL lee de una tabla Delta. La misma consulta se ejecuta de nuevo 30 segundos después, sin cambios en los datos. ¿Qué sucede?

A) Los resultados se devuelven desde la caché de resultados de consulta, sin usar el cómputo del warehouse  
B) La consulta se vuelve a ejecutar por completo en el warehouse  
C) La segunda consulta falla por un conflicto de caché  
D) La caché de consultas solo se activa después de un mínimo de 5 minutos entre ejecuciones  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> Databricks SQL mantiene una caché de resultados de consulta. Cuando la misma consulta se ejecuta
> dentro del TTL de la caché y la versión de la tabla Delta subyacente no ha cambiado, los
> resultados se devuelven desde la caché al instante: no se usa cómputo. La caché se invalida
> automáticamente cuando se escribe en la tabla.

</details>


## Pregunta 20 *(Difícil)*

Un analista escribe `WHERE ROW_NUMBER() OVER (PARTITION BY id ORDER BY date DESC) = 1`. La consulta falla. ¿Cuál es el enfoque correcto?

A) Usar `HAVING` en lugar de `WHERE`  
B) Agregar `GROUP BY` antes de la cláusula `WHERE`  
C) Usar la palabra clave `FILTER` directamente después de la función de ventana  
D) Envolver la consulta en una CTE y filtrar el resultado de la ventana en la consulta externa, o usar `QUALIFY`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> Las funciones de ventana se evalúan después de aplicar `WHERE`, por lo que no puede filtrar los
> resultados de una función de ventana en `WHERE`. Dos enfoques correctos:
>
> - Envolver en una CTE: `WITH cte AS (SELECT *, ROW_NUMBER() OVER (...) AS rn FROM t) SELECT * FROM cte WHERE rn = 1`
> - Usar `QUALIFY`: `SELECT * FROM t QUALIFY ROW_NUMBER() OVER (...) = 1`

</details>


## Pregunta 21 *(Fácil)*

Desaparecieron filas de `main.sales.orders` durante la noche. El analista necesita ver qué usuario ejecutó cada operación y cuándo, junto con métricas como el número de filas eliminadas. ¿Qué comando muestra esto?

A) `DESCRIBE HISTORY main.sales.orders`  
B) `DESCRIBE TABLE EXTENDED main.sales.orders`  
C) `SHOW GRANTS ON TABLE main.sales.orders`  
D) `SHOW TBLPROPERTIES main.sales.orders`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> `DESCRIBE HISTORY` devuelve una fila por cada escritura: la versión, la marca de tiempo, el
> usuario, la operación (`WRITE`, `MERGE`, `DELETE`, ...), `operationParameters` como un predicado
> `DELETE`, y `operationMetrics` como el número de filas eliminadas. El historial se conserva 30
> días por defecto.
>
> `DESCRIBE TABLE EXTENDED` muestra el esquema y los metadatos de la tabla. `SHOW GRANTS` enumera
> los privilegios y `SHOW TBLPROPERTIES` enumera las propiedades de la tabla. Ninguno de ellos
> registra quién cambió los datos.

</details>


## Pregunta 22 *(Fácil)*

Un analista necesita filtrar las filas cuyo resultado de una función de ventana sea igual a 1. ¿Qué cláusula SQL está diseñada para este fin?

A) `HAVING ROW_NUMBER() OVER (...) = 1`  
B) `WHERE ROW_NUMBER() OVER (...) = 1`  
C) `QUALIFY ROW_NUMBER() OVER (...) = 1`  
D) `FILTER ROW_NUMBER() OVER (...) = 1`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> `QUALIFY` es la cláusula SQL diseñada específicamente para filtrar según los resultados de las
> funciones de ventana. Se evalúa después de calcular las funciones de ventana, lo que permite
> filtrar directamente su salida. `WHERE` y `HAVING` se evalúan antes de las funciones de ventana:
> no pueden hacer referencia a sus resultados. `FILTER` modifica funciones agregadas, no funciones
> de ventana.

</details>


## Pregunta 23 *(Media)*

La caché de resultados de consulta sirve datos obsoletos después de que una canalización ETL escribe nuevos datos en la tabla Delta subyacente. ¿Qué invalida la caché?

A) La expiración del TTL de 24 horas de la caché  
B) Reiniciar el SQL warehouse  
C) Ejecutar un comando manual de limpieza de caché en el warehouse  
D) Escribir en la tabla Delta subyacente: la invalidación de la caché es automática  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> La caché de resultados de Databricks SQL realiza un seguimiento de las versiones de las tablas
> Delta mediante el registro de transacciones. Cuando se escriben nuevos datos en una tabla Delta
> (se registra una nueva transacción), la caché de todas las consultas que acceden a esa tabla se
> invalida automáticamente. La siguiente consulta se vuelve a ejecutar con datos actualizados.

</details>


## Desarrollo, compartición y mantenimiento de AI/BI Genie Spaces (Preguntas 24–28)

## Pregunta 24 *(Media)*

Se ha pedido a un equipo de analítica que ofrezca a la organización de éxito del cliente una forma de autoservicio para formular preguntas en lenguaje natural sobre un conjunto curado de tablas de UC. Los usuarios de CS no escriben SQL. El equipo también debe asegurarse de que los usuarios de CS no puedan ver las columnas de PII, aunque esas columnas existan en las tablas subyacentes.

¿Qué enfoque es el documentado y más seguro?

A) Crear una aplicación de Streamlit que llame a un LLM ajustado entrenado con el esquema y permitir que los usuarios de CS se autentiquen con PAT  
B) Crear un AI/BI Genie Space limitado a las tablas de UC curadas; apoyarse en las máscaras de columna y los filtros de fila de UC para aplicar las restricciones de PII; otorgar al grupo de CS acceso al espacio  
C) Programar exportaciones diarias de las tablas curadas a un esquema de UC independiente sin las columnas de PII y luego otorgar acceso a ese esquema  
D) Usar Mosaic AI Vector Search para incrustar cada fila y permitir que los usuarios de CS consulten semánticamente  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> Genie Spaces es la superficie documentada de analítica en lenguaje natural para exactamente este
> caso de uso. Los permisos de UC, incluidas las máscaras de columna y los filtros de fila, se
> aplican a cada consulta SQL generada por Genie, por lo que los usuarios de CS no pueden ver
> físicamente las columnas de PII para las que no tienen permiso, independientemente de cómo
> Genie formule la pregunta. La opción A reinventa Genie de manera deficiente. La opción C duplica
> los datos y crea un problema de actualización y desviación. La opción D es un patrón de
> aumentación por recuperación sobre datos *no estructurados*: la búsqueda vectorial devuelve filas
> similares por distancia de incrustación, lo cual es la herramienta equivocada para consultas
> analíticas estructuradas (recuentos, agregados, combinaciones).

</details>


## Pregunta 25 *(Media)*

Un analista intenta crear un Genie space en el SQL warehouse clásico de su equipo, pero no puede seleccionarlo. ¿Qué requiere un Genie space?

A) Un clúster de uso general con Photon habilitado  
B) Cualquier SQL warehouse, siempre que el autor tenga `CAN MANAGE` sobre él  
C) Un punto de conexión de servicio de modelos dedicado para el espacio  
D) Un SQL warehouse Pro o Serverless (se recomienda Serverless por rendimiento)  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> Los Genie spaces (ahora denominados Genie Agents) ejecutan el SQL que generan en un SQL warehouse
> **Pro o Serverless**. Los warehouses Classic no son compatibles, y se recomienda Serverless por
> rendimiento.
>
> El autor necesita `CAN USE` sobre el warehouse, el derecho de Databricks SQL y `SELECT` sobre los
> datos, pero ningún nivel de permiso permite usar un warehouse Classic. El requisito documentado
> es un SQL warehouse Pro o Serverless, no un clúster de uso general ni un punto de conexión de
> servicio.

</details>


## Pregunta 26 *(Media)*

Un Genie Space interpreta mal con frecuencia el término "revenue" (ingresos): a veces suma `invoice_total` y a veces `paid_amount`. El equipo de datos quiere que el espacio *siempre* use `SUM(invoice_total)` para cualquier pregunta sobre los ingresos.

¿Qué mecanismo de curación es la *primera opción recomendada oficialmente* según las prácticas recomendadas de Databricks?

A) Agregar una instrucción de texto libre: "Los ingresos equivalen a invoice_total"  
B) Agregar una **expresión SQL** denominada `revenue` definida como `SUM(invoice_total)` para que Genie pueda invocarla directamente  
C) Entrenar un modelo de incrustación personalizado con el vocabulario del equipo  
D) Agregar un comentario de columna en `invoice_total` que diga "esto son los ingresos"  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> La guía oficial de prácticas recomendadas de Genie de Databricks indica **preferir las expresiones
> SQL antes que las instrucciones de texto** cuando ambas pueden expresar la regla. Una expresión
> SQL con nombre es exacta y determinista: Genie la compone en la consulta generada de forma
> literal. Las instrucciones de texto libre (opción A) son el *último recurso* documentado. La
> opción C es irrelevante: Genie genera SQL, no incrustaciones. La opción D (comentarios de columna
> o metadatos) es un mecanismo complementario, pero menos preciso que una expresión SQL para una
> regla de agregación.

</details>


## Pregunta 27 *(Media)*

Un equipo crea un Genie space sobre 40 tablas que cubren ventas, soporte y recursos humanos. Los usuarios informan de respuestas incorrectas frecuentes causadas por combinaciones erróneas. ¿Qué recomienda la guía de curación de Databricks?

A) Agregar una instrucción de texto que enumere todas las combinaciones válidas entre las 40 tablas  
B) Aumentar el tamaño del SQL warehouse para que Genie pueda considerar más tablas  
C) Limitar el alcance de cada espacio, apuntando a cinco tablas o menos, y precombinar las tablas relacionadas en vistas o vistas de métricas  
D) Agregar todas las tablas restantes del catálogo para que Genie tenga el contexto completo  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> La guía oficial recomienda «apuntar a cinco tablas o menos» e incluir solo las tablas necesarias
> para las preguntas que el espacio debe responder. Cuando un tema requiere más tablas,
> precombínelas en vistas o vistas de métricas, o divida un dominio amplio en varios espacios
> enfocados.
>
> Demasiadas tablas diluyen el contexto de Genie y aumentan la probabilidad de combinaciones
> erróneas. Más tablas agravan el problema, y el tamaño del warehouse afecta a la velocidad, no a
> cómo Genie interpreta una pregunta. Las instrucciones de texto son el último recurso documentado.

</details>


## Pregunta 28 *(Media)*

Un Genie Space lleva tres meses en funcionamiento. Los análisis de uso muestran que el 15 % de las preguntas de los usuarios reciben respuestas que el equipo de datos verifica manualmente como incorrectas. La analista principal quiere mejorar la precisión de forma sistemática.

¿Qué flujo de trabajo aborda mejor las respuestas incorrectas recurrentes?

A) Aumentar la temperatura del modelo para que Genie produzca respuestas más variadas  
B) Revisar las preguntas, las valoraciones y las solicitudes "Fix it" / revisión en la pestaña **Monitor** del espacio, agregar una expresión SQL, una consulta de ejemplo o un recurso de confianza para cada fallo recurrente, agregar las preguntas fallidas como puntos de referencia y volver a ejecutarlos  
C) Desactivar Genie y reemplazarlo por paneles DBSQL curados manualmente  
D) Entrenar un modelo de incrustación personalizado con el vocabulario del equipo  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> El ciclo de optimización documentado es: hacer seguimiento de las preguntas de los usuarios, la
> precisión y los comentarios (la pestaña **Monitor** muestra cada pregunta y respuesta, con la
> opinión del usuario y un resumen semanal) → curar (se prefieren las expresiones SQL y el SQL de
> ejemplo antes que las instrucciones de texto) → validar con **puntos de referencia**. Cada
> corrección debe convertirse en un punto de referencia para que un cambio posterior no pueda
> romperla silenciosamente. A empeora las respuestas, no las mejora (la temperatura sirve para la
> creatividad, no para la precisión). C abandona el valor de la analítica en lenguaje natural. D no
> se aplica: Genie genera SQL, no incrustaciones.

</details>


## Comprensión de la plataforma Databricks Data + AI (Preguntas 29–33)

## Pregunta 29 *(Fácil)*

Un analista necesita conectar Power BI Desktop a un SQL warehouse de Databricks. ¿Qué protocolo de conexión debe usar?

A) Delta Sharing  
B) Acceso directo a almacenamiento en la nube (S3/ADLS)  
C) API REST con salida JSON  
D) El controlador ODBC o JDBC proporcionado por Databricks  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> Databricks proporciona controladores ODBC y JDBC compatibles con herramientas de BI estándar,
> incluidas Power BI, Tableau y Excel. Delta Sharing sirve para compartir datos entre
> organizaciones, no para conectar herramientas de BI.

</details>


## Pregunta 30 *(Fácil)*

Un equipo solicita un conjunto de datos de referencia de terceros desde Databricks Marketplace. ¿Cómo llegan normalmente los datos a su workspace?

A) Mediante Delta Sharing (ahora OpenSharing), normalmente como un catálogo de tablas, por lo que no hay que crear ninguna canalización de copia  
B) Como archivos CSV que cada usuario descarga en su computadora y vuelve a cargar  
C) Mediante una canalización de Auto Loader que el equipo debe crear contra el bucket de almacenamiento del proveedor  
D) No es posible: Marketplace solo enumera modelos y aplicaciones de IA, no conjuntos de datos  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> Marketplace es un intercambio abierto donde los proveedores publican conjuntos de datos, modelos
> de IA, notebooks, aplicaciones y servidores MCP. Los conjuntos de datos se entregan mediante Delta
> Sharing (renombrado OpenSharing el 10 de junio de 2026) y normalmente llegan como catálogos de
> datos tabulares, de modo que los consumidores los consultan sin crear ninguna ingesta.
>
> Cualquiera puede explorar el Marketplace abierto, pero para solicitar un producto se necesita un
> workspace de Databricks. Algunas ofertas son inmediatas y otras requieren la aprobación del
> proveedor.

</details>


## Pregunta 31 *(Fácil)*

¿Cuál es el espacio de nombres de tres niveles de Unity Catalog?

A) workspace.database.table  
B) catalog.schema.table  
C) metastore.catalog.table  
D) environment.schema.table  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> Unity Catalog usa `catalog.schema.table`. Los esquemas de Unity Catalog equivalen a las bases de
> datos del metastore de Hive heredado de dos niveles. El metastore es el contenedor de nivel
> superior que aloja los catálogos, pero no forma parte del espacio de nombres de referencia de
> objetos.

</details>


## Pregunta 32 *(Fácil)*

En Unity Catalog, ¿qué hace `USE CATALOG my_catalog`?

A) Crea un catálogo nuevo denominado `my_catalog`  
B) Enumera todos los objetos de `my_catalog`  
C) Otorga al usuario actual acceso a `my_catalog`  
D) Establece `my_catalog` como el catálogo predeterminado para la sesión actual  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> `USE CATALOG` establece el catálogo predeterminado de la sesión. Las consultas posteriores que
> usen nombres de dos niveles (`schema.table`) o de un solo nivel (`table`) se resuelven contra este
> catálogo predeterminado. No crea el catálogo, no otorga acceso a él ni enumera sus objetos.

</details>


## Pregunta 33 *(Fácil)*

¿Qué afirmación describe correctamente las transacciones ACID de Delta Lake?

A) Las transacciones de Delta Lake son finalmente consistentes, no totalmente ACID  
B) Las tablas Delta admiten atomicidad, consistencia, aislamiento y durabilidad en todas las operaciones de lectura y escritura  
C) Las garantías ACID se aplican solo a las escrituras por lotes, no a las escrituras en streaming  
D) El cumplimiento de ACID requiere que haya un metastore de Unity Catalog adjunto  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> El registro de transacciones de Delta Lake (`_delta_log`) ofrece garantías ACID completas para
> todas las operaciones: por lotes, en streaming y lecturas/escrituras concurrentes. Este es un
> diferenciador clave frente a los lagos de datos Parquet/CSV simples, que no ofrecen garantías
> transaccionales.

</details> 


## Gestión de datos (Preguntas 34–37)

## Pregunta 34 *(Fácil)*

Se elimina una tabla Delta administrada con `DROP TABLE`. ¿Qué sucede con los archivos de datos subyacentes?

A) Los archivos permanecen en DBFS y deben eliminarse manualmente  
B) Los archivos se mueven a una papelera de reciclaje y se conservan durante 30 días  
C) Los archivos se eliminan permanentemente junto con los metadatos de la tabla  
D) Los archivos se archivan en el almacenamiento en la nube durante 90 días  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> Eliminar una tabla administrada elimina tanto la definición de la tabla del metastore como los
> archivos de datos subyacentes del almacenamiento en la nube. Esta operación es irreversible sin
> una copia de seguridad. Las tablas externas se comportan de forma diferente: solo se eliminan sus
> metadatos y los archivos permanecen intactos.

</details>


## Pregunta 35 *(Fácil)*

Un ingeniero de datos agrega datos con columnas nuevas a una tabla Delta existente. ¿Qué opción de escritura habilita la evolución del esquema?

A) `.option("autoSchema", "true")`  
B) `.option("allowNewColumns", "true")`  
C) `.option("mergeSchema", "true")`  
D) No se necesita ninguna opción: Delta siempre acepta columnas nuevas  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> `mergeSchema=true` (o `spark.databricks.delta.schema.autoMerge.enabled = true` de forma global)
> habilita la evolución del esquema durante las escrituras. Las columnas nuevas del DataFrame de
> entrada se agregan automáticamente al esquema de la tabla Delta. Sin esta opción, una escritura
> que contenga columnas adicionales genera un error de discrepancia de esquema.

</details>


## Pregunta 36 *(Fácil)*

Se elimina con `DROP TABLE` una tabla externa que apunta a `gs://bucket/data/`. ¿Qué sucede con los archivos en GCS?

A) Los archivos se eliminan de GCS inmediatamente  
B) Los archivos se archivan en Databricks durante 30 días  
C) Los archivos se mueven a una ubicación de DBFS administrada por Databricks  
D) Los archivos permanecen en GCS: solo se elimina la entrada de metadatos de la tabla  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> Las tablas externas no son propietarias de sus datos. `DROP TABLE` sobre una tabla externa
> elimina solo la entrada de metadatos del catálogo (la definición de la tabla). Los archivos de
> datos reales en la ubicación de almacenamiento externa no se ven afectados en absoluto.

</details>


## Pregunta 37 *(Media)*

Un ingeniero de datos ejecuta:

```sql
ALTER TABLE t SET TBLPROPERTIES (
  'delta.deletedFileRetentionDuration' = 'interval 30 days'
);
```

¿Qué configura esto?

A) VACUUM conservará los archivos de datos eliminados durante al menos 30 días antes de borrarlos  
B) La propia tabla caduca y se elimina después de 30 días  
C) Las filas de datos eliminadas se conservan durante 30 días y pueden restaurarse  
D) El registro de transacciones de Delta se compacta y se conserva durante 30 días  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> `delta.deletedFileRetentionDuration` establece el período mínimo de retención de archivos para
> VACUUM. VACUUM no eliminará archivos más recientes que este umbral, lo que permite consultas de
> viaje en el tiempo hasta 30 días atrás. El valor predeterminado es 7 días.

</details>


## Protección de datos (Preguntas 38–41)

## Pregunta 38 *(Fácil)*

¿Qué sentencia GRANT permite a un usuario leer datos de una tabla Delta en Unity Catalog?

A) `GRANT READ ON TABLE catalog.schema.table TO user`  
B) `GRANT VIEW ON TABLE catalog.schema.table TO user`  
C) `GRANT SELECT ON TABLE catalog.schema.table TO user`  
D) `GRANT ACCESS ON TABLE catalog.schema.table TO user`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> `SELECT` es el nombre correcto del privilegio de lectura en Unity Catalog. `READ`, `VIEW` y
> `ACCESS` no son nombres de privilegios válidos en Unity Catalog. Los usuarios también suelen
> necesitar `USE CATALOG` y `USE SCHEMA` en los objetos superiores para resolver la tabla.

</details>


## Pregunta 39 *(Fácil)*

¿Qué devuelve `SHOW GRANTS ON TABLE catalog.schema.table`?

A) Todos los privilegios otorgados sobre la tabla y las entidades a las que se otorgaron  
B) El esquema de la tabla: nombres de columnas y tipos de datos  
C) La ubicación de almacenamiento y el formato de archivo de la tabla  
D) El historial reciente de acceso a datos de la tabla  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> `SHOW GRANTS ON <object>` enumera todos los privilegios de Unity Catalog otorgados sobre ese
> objeto específico: el tipo de privilegio (p. ej., SELECT, MODIFY), la entidad (usuario, grupo o
> principal de servicio) y el otorgante. Es la herramienta principal para auditar permisos a nivel
> de objeto.

</details>


## Pregunta 40 *(Difícil)*

Un panel de ventas muestra datos diferentes para cada representante de ventas según quién haya iniciado sesión. Este comportamiento se implementa con:

A) Copias separadas del panel para cada representante de ventas  
B) Un parámetro del panel prepoblado con el nombre del usuario que ha iniciado sesión  
C) Una política de seguridad a nivel de fila de Unity Catalog sobre la tabla subyacente  
D) Una UDF de Python en la consulta del panel que filtra por el usuario de la sesión  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> Las políticas de seguridad a nivel de fila de Unity Catalog filtran automáticamente las filas
> según la identidad del usuario actual en el momento de la consulta. La consulta del panel
> devuelve los datos adecuados para el usuario que ha iniciado sesión, sin necesidad de
> configuración a nivel de panel. Esto es más seguro que el filtrado basado en parámetros, que un
> usuario podría eludir.

</details>


## Pregunta 41 *(Difícil)*

Un equipo de cumplimiento normativo necesita un registro consultable mediante SQL de quién accedió a los datos de la empresa y cuándo. ¿Qué función de Databricks lo proporciona mejor?

A) El registro de transacciones de Delta, que registra las escrituras en una tabla  
B) Abrir la página Query History en la interfaz de usuario cada día  
C) Los registros de auditoría en la tabla del sistema `system.access.audit`  
D) Seguimiento de experimentos de MLflow  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> Los registros de auditoría documentan los eventos de la cuenta y del workspace, incluidos los
> accesos a datos, con la identidad del usuario y la hora, y la tabla del sistema
> `system.access.audit` permite a los administradores analizarlos con SQL. El registro de Delta
> cubre solo los cambios en una tabla (A), y examinar el historial de consultas en la interfaz no
> constituye un registro de auditoría gobernado (B). Para el análisis a nivel de consulta, la tabla
> del sistema complementaria es `system.query.history`.

</details>


## Importación de datos (Preguntas 42–43)

## Pregunta 42 *(Fácil)*

Un analista intenta crear una tabla con **Crear o modificar una tabla** cargando 12 archivos CSV que suman 3 GB, y la carga se rechaza. ¿Qué debe hacer el analista?

A) Cargar los archivos en un volumen de Unity Catalog y luego crear la tabla desde el volumen (por ejemplo, con `COPY INTO`)  
B) Comprimir los archivos en un único archivo zip y cargar ese en su lugar  
C) Convertir primero los archivos a formato Excel  
D) Pedir a un administrador que aumente el límite de carga del workspace  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> La carga con **Crear o modificar una tabla** acepta hasta 10 archivos a la vez, con un total
> inferior a 2 GB, en formato CSV, TSV, JSON, Avro, Parquet o texto. No se admiten archivos zip ni
> tar.
>
> **Cargar archivos en un volumen** acepta cualquier formato, hasta 5 GB por archivo, con
> `WRITE VOLUME`. Desde el volumen, el analista puede hacer clic en **Crear tabla** o cargar los
> archivos con `COPY INTO`.

</details>


## Pregunta 43 *(Media)*

Los archivos de eventos llegan a un bucket de S3 cada pocos minutos, y el bucket contendrá millones de archivos en un año. El esquema cambia de vez en cuando. El analista trabaja solo con SQL. ¿Qué enfoque de ingesta se ajusta mejor?

A) Una sentencia `COPY INTO` programada cada pocos minutos  
B) Cargar los archivos mediante la interfaz **Crear o modificar una tabla** cada mañana  
C) Una conexión de Lakehouse Federation al bucket  
D) Una tabla de streaming definida con `STREAM read_files`, que usa Auto Loader  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> Auto Loader es la opción documentada para millones de archivos y esquemas que evolucionan
> frecuentemente. En SQL, una tabla de streaming con `STREAM read_files` usa Auto Loader
> internamente, por ejemplo `CREATE OR REFRESH STREAMING TABLE ... AS SELECT * FROM STREAM read_files(...)`.
>
> La guía de Databricks recomienda `COPY INTO` para archivos en el orden de los miles. La carga
> mediante la interfaz es para archivos locales puntuales. Lakehouse Federation consulta bases de
> datos externas in situ y no ingiere archivos desde S3.

</details>


## Modelado de datos con Databricks SQL (Preguntas 44–45)

## Pregunta 44 *(Media)*

Un analista crea un panel de ingresos diarios que ejecuta una consulta agregada sobre una tabla Delta `orders` de la capa Silver (~500 GB). La agregación tarda 90 segundos y el panel es visto por más de 50 usuarios al día. El analista quiere cargas del panel en menos de un segundo.

¿Qué enfoque es el mejor según la documentación?

A) Vista estándar: `CREATE VIEW gold.daily_revenue AS SELECT date, SUM(amount) ... GROUP BY date`  
B) Vista materializada: `CREATE MATERIALIZED VIEW gold.daily_revenue AS ...` con una programación de actualización cada hora  
C) Fijar el panel a un SQL Warehouse Pro con la caché de resultados habilitada  
D) Convertir la tabla orders para usar bucketing por fecha  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> Las vistas materializadas (MV) precalculan la agregación y la persisten como datos Delta; las
> lecturas posteriores del panel son lecturas del resultado materializado (menos de un segundo).
> Para una agregación de 90 segundos que se lee más de 50 veces al día, la MV es el patrón canónico:
> el costo de almacenamiento es trivial comparado con el cómputo ahorrado. A vuelve a ejecutar la
> agregación en cada lectura. C ayuda un poco (la caché de resultados devuelve resultados
> almacenados cuando coinciden los hashes de consulta y datos), pero la caché se invalida en cada
> escritura en `orders` y no ayuda al *primer* espectador de cada día. D es irrelevante (el
> bucketing sirve para las combinaciones).

</details>


## Pregunta 45 *(Media)*

Un equipo está diseñando la capa Gold que alimenta los paneles de BI. Los analistas quieren consultas simples con el menor número posible de combinaciones y tiempos de respuesta rápidos. ¿Qué modelo se ajusta mejor?

A) Un esquema de copo de nieve con tablas de dimensión totalmente normalizadas  
B) Un modelo Data Vault de hubs, links y satellites  
C) Consultar directamente las tablas Bronze para evitar el trabajo de modelado  
D) Un esquema estrella: una tabla de hechos central combinada con tablas de dimensión desnormalizadas  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> Un **esquema estrella** requiere una combinación por dimensión, por lo que las consultas son más
> simples y rápidas. Es el estándar para BI y el modelo típico de la capa Gold.
>
> Un esquema de **copo de nieve** normaliza las dimensiones en más tablas, lo que implica más
> combinaciones y consultas más lentas. Elija este modelo cuando la normalización sea crítica.
> **Data Vault** está optimizado para escritura y normalmente se ubica en la capa Silver, con
> esquemas estrella publicados encima para BI. La capa **Bronze** contiene datos sin procesar y no
> validados, que los analistas rara vez consultan directamente.

</details>


## Clave de respuestas

| # | Respuesta | # | Respuesta | # | Respuesta |
|---|--------|---|--------|---|--------|
| 1 | C | 16 | B | 31 | B |
| 2 | B | 17 | B | 32 | D |
| 3 | D | 18 | D | 33 | B |
| 4 | A | 19 | A | 34 | C |
| 5 | D | 20 | D | 35 | C |
| 6 | C | 21 | A | 36 | D |
| 7 | A | 22 | C | 37 | A |
| 8 | C | 23 | D | 38 | C |
| 9 | B | 24 | B | 39 | A |
| 10 | C | 25 | D | 40 | C |
| 11 | A | 26 | B | 41 | C |
| 12 | A | 27 | C | 42 | A |
| 13 | D | 28 | B | 43 | D |
| 14 | B | 29 | D | 44 | B |
| 15 | A | 30 | A | 45 | D |

## Registro de rendimiento por sección

| Dominio (peso de octubre de 2025) | Preguntas | Su puntuación | Porcentaje |
| ------ | --------- | ---------- | ---------- |
| Ejecución de consultas con Databricks SQL y SQL warehouses (20 %) | 1–9 (9 preguntas) | ___/9 | ___% |
| Creación de paneles y visualizaciones (16 %) | 10–16 (7 preguntas) | ___/7 | ___% |
| Análisis de consultas (15 %) | 17–23 (7 preguntas) | ___/7 | ___% |
| Desarrollo, compartición y mantenimiento de AI/BI Genie Spaces (12 %) | 24–28 (5 preguntas) | ___/5 | ___% |
| Comprensión de la plataforma Databricks Data + AI (11 %) | 29–33 (5 preguntas) | ___/5 | ___% |
| Gestión de datos (8 %) | 34–37 (4 preguntas) | ___/4 | ___% |
| Protección de datos (8 %) | 38–41 (4 preguntas) | ___/4 | ___% |
| Importación de datos (5 %) | 42–43 (2 preguntas) | ___/2 | ___% |
| Modelado de datos con Databricks SQL (5 %) | 44–45 (2 preguntas) | ___/2 | ___% |
| **Total** | **45 preguntas** | **___/45** | **___%** |

---

**[← Volver al examen simulado](./README.md)** | **[← Volver a Recursos](../README.md)** | **[← Volver a Data Analyst Associate](../../README.md)**

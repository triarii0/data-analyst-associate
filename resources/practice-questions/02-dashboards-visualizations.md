# Preguntas de práctica de Creación de paneles y visualizaciones

**Peso en el examen: 16 %** — Sección 6 de la guía de examen de octubre de 2025

Las preguntas marcadas como *(2025 blueprint)* abordan habilidades añadidas en la guía de examen de octubre de 2025. Todas las preguntas se verificaron con la documentación de Databricks el 26 de octubre de 2026. Los parámetros utilizan marcadores de parámetros con nombre (`:name`). La sintaxis heredada de mustache (`{{ }}`) aparece solo donde se indica como heredada.

[← Anterior: Ejecución de consultas](./01-executing-queries.md) | [↑ Volver a Preguntas de práctica](./README.md) | [Siguiente: Análisis de consultas](./03-analyzing-queries.md)

---


## Pregunta 1: Mostrar un único KPI *(Fácil)*

¿Qué visualización de un panel AI/BI muestra de forma destacada un único número agregado, como los ingresos totales?

A) Tabla  
B) Contador  
C) Línea  
D) Pivote  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> Un contador muestra un único valor de forma destacada y puede compararlo con un valor de referencia, lo cual es adecuado para KPI.
>
> Una tabla muestra filas y columnas, un gráfico de líneas muestra la evolución a lo largo del tiempo y un pivote resume valores en dos dimensiones.

</details>


## Pregunta 2: Paneles heredados *(Fácil)*

Un equipo todavía tiene paneles creados en el editor heredado de paneles de Databricks SQL. ¿Qué recomienda Databricks?

A) Usar paneles AI/BI; los paneles heredados ya no son compatibles y no se pueden crear nuevos  
B) Seguir creando paneles heredados nuevos porque ejecutan las consultas más rápido  
C) Exportar los paneles heredados a PDF y dejar de usar paneles  
D) Convertirlos en paneles de notebook  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> Los paneles AI/BI (antes denominados paneles Lakeview) son el producto actual de creación de paneles. Databricks ha retirado la documentación de los paneles heredados, ya no se pueden crear paneles heredados y Databricks recomienda usar paneles AI/BI en su lugar.
>
> La velocidad del panel depende de las consultas y del warehouse, no del editor (B).

</details>


## Pregunta 3: Actualización diaria de un panel *(Media)*

Una parte interesada necesita que un panel se actualice automáticamente cada mañana a las 8:00. ¿Cómo se debe configurar?

A) Hacer clic en **Schedule** en el panel y establecer la frecuencia, la hora y la zona horaria  
B) No hacer nada, porque los paneles solo se actualizan cuando se abren  
C) Escribir un notebook que capture el panel como imagen y programarlo como un job  
D) Los paneles no se pueden programar, así que hay que programar cada consulta por separado  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> El botón **Schedule** de un panel AI/BI establece la frecuencia, la hora de inicio y la zona horaria, con sintaxis cron opcional. Por defecto, la ejecución programada utiliza el SQL warehouse del panel, y se puede elegir otro en la configuración avanzada.
>
> Las programaciones también pueden enviar instantáneas a los suscriptores, por lo que no hace falta un notebook personalizado (C).

</details>


## Pregunta 4: Umbral de una alerta *(Media)*

Un analista crea una alerta SQL sobre una consulta que devuelve el recuento de transacciones fallidas. La alerta debe activarse cuando el recuento sea mayor que 100. ¿Cómo se debe establecer la condición?

A) El valor es NULL  
B) Valor `=` 100  
C) Valor `>` 100  
D) Valor `>=` 100  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> Una condición de alerta compara una columna con un umbral mediante un operador. La columna puede ser el valor de la primera fila o una agregación de todas las filas, como `SUM` o `AVG`, y el umbral es un valor estático por defecto. «Mayor que 100» es `> 100`.
>
> `>=` también se activaría exactamente en 100 (D). `= 100` se activa solo en 100 (B) e `IS NULL` comprueba valores ausentes (A).

</details>


## Pregunta 5: Enviar una alerta a Slack *(Media)*

Una alerta SQL debe publicar un mensaje en un canal de Slack cuando se active. ¿Cómo se configura?

A) Un administrador del espacio de trabajo crea un destino de notificación de Slack y el autor de la alerta lo selecciona en la configuración de notificaciones de la alerta  
B) El autor de la alerta pega la contraseña de Slack en la consulta de la alerta  
C) Un script de Python consulta el estado de la alerta cada minuto  
D) Las alertas solo pueden notificar por correo electrónico  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> Los administradores del espacio de trabajo configuran los destinos de notificación. Después, los autores de alertas eligen usuarios o destinos en la configuración de notificaciones de la alerta. **Notify on OK** también puede enviar un mensaje cuando la alerta vuelve a la normalidad.
>
> No hace falta ninguna consulta periódica personalizada (C) y las alertas no se limitan al correo electrónico (D).

</details>


## Pregunta 6: Compartir con usuarios de la cuenta *(Media)*

Un responsable quiere que un panel AI/BI publicado pueda ser visto por usuarios asociados que están registrados en la cuenta de Databricks, pero que no son miembros del espacio de trabajo. ¿Qué debe hacer?

A) Añadir a todos los asociados al espacio de trabajo con derechos de edición  
B) Enviar por correo una exportación CSV cada semana  
C) Compartir el panel publicado con esos usuarios de la cuenta o permitir que cualquier persona de la cuenta lo vea  
D) Usar Delta Sharing para compartir el panel  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> Los paneles publicados se pueden compartir con usuarios de la cuenta que no tienen acceso al espacio de trabajo. Esos usuarios están limitados al permiso CAN RUN y inician sesión con correo electrónico, una contraseña de un solo uso o SSO.
>
> Añadir a todos al espacio de trabajo concede muchos más accesos de los necesarios (A). Delta Sharing (ahora OpenSharing) comparte datos, no paneles (D).

</details>


## Pregunta 7: Tendencia a lo largo del tiempo *(Fácil)*

Los datos de ventas deben mostrar la tendencia de 12 meses. ¿Qué visualización es la más adecuada?

A) Gráfico de barras  
B) Gráfico de líneas  
C) Gráfico de dispersión  
D) Gráfico circular  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> Los gráficos de líneas presentan la evolución de una o más métricas a lo largo del tiempo.
>
> Los gráficos de barras son adecuados para comparar categorías. Los gráficos de dispersión muestran la relación entre dos variables numéricas. Los gráficos circulares muestran proporciones de un total.

</details>


## Pregunta 8: Lista detallada con clasificación *(Fácil)*

¿Qué escenario es el más adecuado para una visualización de tabla?

A) Los ingresos totales como un único número KPI  
B) La tendencia de ventas de 12 meses  
C) Los 20 productos principales con sus ingresos, unidades vendidas y puesto  
D) La cuota de ventas por región  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> Las tablas muestran muchas columnas por fila y permiten reordenar, ocultar y dar formato a las columnas, lo cual es adecuado para listas detalladas.
>
> Los KPI usan contadores (A), las tendencias usan gráficos de líneas (B) y las proporciones usan gráficos circulares o de barras (D).

</details>


## Pregunta 9: Programación de alertas para un SLA *(Media)*

Una alerta SQL vigila que el inventario baje por debajo de un umbral de reposición. El negocio debe ser notificado en un plazo de una hora. ¿Qué programación es adecuada?

A) Cada hora o con más frecuencia  
B) Cada 24 horas  
C) Evaluación de streaming en tiempo real  
D) Solo evaluación manual  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> Las alertas ejecutan su consulta según una programación y evalúan la condición en cada ejecución. Una programación horaria (o más corta) cumple el requisito de notificación de una hora.
>
> Las alertas no se evalúan sobre un flujo continuo (C). Una programación diaria no cumple el requisito (B) y la evaluación manual no está automatizada (D).

</details>


## Pregunta 10: Sintaxis de parámetros en un conjunto de datos de panel *(Fácil)*

Un conjunto de datos de panel debe aceptar una fecha seleccionada por los espectadores. ¿Qué sintaxis utiliza la consulta del conjunto de datos?

A) `@order_date`  
B) `{{order_date}}`  
C) `$order_date`  
D) `:order_date`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> Los paneles AI/BI, el editor SQL actual, los notebooks y Genie utilizan marcadores de parámetros con nombre: dos puntos seguidos del nombre del parámetro.
>
> La sintaxis de mustache con llaves dobles (B) es heredada y solo funciona en el editor SQL heredado. Los paneles no la admiten, por lo que las consultas que la usen deben convertirse a marcadores con nombre.

</details>


## Pregunta 11: Lista desplegable de regiones *(Fácil)*

Un panel necesita un control que permita a los espectadores elegir una región de una lista, y la región seleccionada filtra un conjunto de datos a través del parámetro `:region`. ¿Qué debe añadir el autor?

A) Un widget de texto  
B) Un selector de fechas  
C) Una visualización de contador  
D) Un widget de filtro con un selector de un solo valor enlazado al parámetro `:region`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> Los parámetros se pueden enlazar a widgets de filtro para que las elecciones de los espectadores se pasen a la consulta. Un selector de un solo valor limita la elección a un valor de una lista.
>
> Los widgets de texto muestran contenido y no pasan valores (A). Un contador es una visualización (C).

</details>


## Pregunta 12: Parámetro de selección múltiple *(Media)*

Un parámetro de panel `:categories` tiene activada la opción **Allow multiple selections**. ¿Qué cláusula `WHERE` gestiona uno o varios valores seleccionados?

A) `WHERE category = :categories`  
B) `WHERE array_contains(:categories, category)`  
C) `WHERE category IN (:categories)`  
D) `WHERE category LIKE :categories`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> Un parámetro de selección múltiple se pasa como un array, por lo que la consulta debe usar `ARRAY_CONTAINS`. Añadir `OR :categories IS NULL` gestiona la selección «All».
>
> Las otras opciones comparan un valor único con un array. `IN ({{ ... }})` era un patrón de la sintaxis heredada de mustache.

</details>


## Pregunta 13: Incrustación para usuarios externos *(Difícil)*

Una empresa quiere incrustar un panel AI/BI publicado en su portal de clientes. Los clientes no tienen cuentas de Databricks. ¿Qué enfoque admite Databricks?

A) Incrustación básica con iframe, que no requiere inicio de sesión  
B) Compartir el panel con Delta Sharing  
C) Exportar el panel a HTML y alojarlo  
D) Incrustación para usuarios externos, en la que la aplicación se autentica en Databricks con un service principal  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> La incrustación para usuarios externos permite que espectadores sin cuenta de Databricks vean un panel. La aplicación se autentica con un service principal y un secreto OAuth, y los tokens son válidos durante una hora.
>
> La incrustación básica exige que los espectadores inicien sesión con credenciales de Databricks (A). Solo se pueden incrustar paneles publicados, y los administradores del espacio de trabajo deben definir las superficies donde se permite la incrustación.

</details>


## Pregunta 14: Actualización de panel frente a evaluación de alerta *(Media)*

¿Cuál es la diferencia entre la programación de un panel y la programación de una alerta SQL?

A) Son el mismo objeto  
B) Los paneles se actualizan automáticamente, mientras que las alertas nunca se ejecutan según una programación  
C) La programación de un panel actualiza los datos del panel y puede enviar instantáneas; la programación de una alerta ejecuta la consulta de la alerta, evalúa la condición y notifica cuando se cumple  
D) Las alertas solo se pueden crear a partir de conjuntos de datos de paneles  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> Los paneles y las alertas son objetos separados con sus propias programaciones. Cada vez que se ejecuta, una alerta se evalúa como OK, TRIGGERED o ERROR.
>
> En las alertas más recientes, la consulta se escribe en la propia alerta, por lo que no se puede reutilizar una consulta guardada existente (D).

</details>


## Pregunta 15: Páginas y conjuntos de datos *(Fácil)* *(2025 blueprint)*

Un panel necesita una página de resumen ejecutivo y una página de operaciones detallada. Cada página muestra gráficos de tablas distintas. ¿Cómo se crea en los paneles AI/BI?

A) Crear dos paneles y enlazarlos con una URL  
B) Crear un único conjunto de datos que una todas las tablas, porque un panel solo permite un conjunto de datos  
C) Añadir una segunda página al lienzo y definir varios conjuntos de datos en la pestaña Data  
D) Usar un panel de notebook para la segunda página  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> Los paneles nuevos empiezan con una página y se añaden más con el icono de más junto al título de la página. La pestaña Data contiene los conjuntos de datos del panel, definidos a partir de tablas, vistas o SQL personalizado. Las visualizaciones de cualquier página pueden usar cualquier conjunto de datos.
>
> Un panel no está limitado a un solo conjunto de datos (B).

</details>


## Pregunta 16: Texto e imágenes *(Fácil)* *(2025 blueprint)*

Un autor quiere una breve explicación y el logotipo de la empresa en la parte superior de un panel. ¿Qué tipos de widget se usan?

A) Un contador y una tabla  
B) Un widget de texto para la explicación y un widget de imagen para el logotipo  
C) Un widget de filtro  
D) Los paneles no admiten imágenes  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> Los paneles AI/BI admiten widgets de visualización, filtro, texto e imagen.
>
> Los títulos y las descripciones de los widgets también admiten formato de texto enriquecido.

</details>


## Pregunta 17: Gráfico a partir de un resultado de consulta *(Fácil)* *(2025 blueprint)*

Un analista ejecuta una consulta en el editor SQL y quiere un gráfico de barras del resultado. ¿Cuál es la forma más rápida?

A) Hacer clic en **+** sobre el resultado, seleccionar **Visualization** y elegir el tipo  
B) Exportar el resultado a CSV y crear el gráfico en otro lugar  
C) Crear primero un panel, porque las visualizaciones solo existen en los paneles  
D) Ejecutar `CREATE VISUALIZATION`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> Tanto en el editor SQL como en los notebooks, se hace clic en **+** sobre un resultado y se selecciona **Visualization** para abrir el editor de visualizaciones.
>
> En un notebook, una visualización de una celda SQL también se puede añadir a un panel. Las visualizaciones de celdas que no son SQL y los perfiles de datos solo se pueden añadir a un panel de notebook.

</details>


## Pregunta 18: Probar un parámetro de un conjunto de datos *(Media)* *(2025 blueprint)*

Un autor añade `WHERE region = :region` a un conjunto de datos de panel y quiere comprobar la consulta antes de publicar. ¿Cómo se prueba el parámetro?

A) Publicar el panel y pedir a un espectador que lo pruebe  
B) Sustituir `:region` por un literal, ejecutarlo y volver a poner el marcador  
C) Escribir un valor en el campo situado bajo el nombre del parámetro en el editor de conjuntos de datos y ejecutar la consulta  
D) Los parámetros no se pueden probar en el editor de conjuntos de datos  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> Para probar, escriba un valor en el campo de texto situado bajo el nombre del parámetro y ejecute la consulta. Al ejecutarla, ese valor también se guarda como valor predeterminado del parámetro.
>
> No hace falta publicar ni editar la consulta (A, B).

</details>


## Pregunta 19: Parámetro de rango de fechas *(Media)* *(2025 blueprint)*

Un parámetro de panel `:ship_date` es un rango de fechas. ¿Qué cláusula filtra el conjunto de datos al rango seleccionado?

A) `WHERE l_shipdate BETWEEN :ship_date.start AND :ship_date.end`  
B) `WHERE l_shipdate BETWEEN '{{ ship_date.start }}' AND '{{ ship_date.end }}'`  
C) `WHERE l_shipdate = :ship_date`  
D) `WHERE l_shipdate BETWEEN :ship_date.min AND :ship_date.max`  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> Un parámetro de rango de fechas expone los valores `.min` y `.max`. Añadir `OR :ship_date IS NULL` gestiona la selección «All».
>
> `.start` y `.end` no son los nombres de las propiedades (A). La opción B es sintaxis heredada de mustache, que los paneles no admiten.

</details>


## Pregunta 20: Publicar con permisos de espectador *(Difícil)* *(2025 blueprint)*

Una tabla de ventas tiene un filtro de filas de Unity Catalog por el que cada representante ve solo su propia región. Después de publicar un panel, todos los representantes ven todas las regiones. ¿Cuál es la causa más probable y cuál es la solución?

A) Los filtros de filas no se aplican a los paneles, así que hay que añadir un filtro al panel  
B) El warehouse es demasiado pequeño, así que hay que aumentar su tamaño  
C) El filtro de filas debe adjuntarse al panel en lugar de a la tabla  
D) El panel se publicó con permisos de datos compartidos, por lo que las consultas se ejecutan con las credenciales del publicador; vuelva a publicarlo con permisos de datos individuales  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> Por defecto, los espectadores ejecutan las consultas con las credenciales del publicador (permisos de datos compartidos), por lo que el filtro de filas evalúa la identidad del publicador. Con permisos de datos individuales, los espectadores ejecutan las consultas con sus propias credenciales y el filtro de filas se aplica por espectador.
>
> Los filtros de filas se adjuntan a las tablas y se aplican a todas las consultas (A, C). El tamaño del warehouse no tiene nada que ver con el acceso (B).

</details>


## Pregunta 21: Instantánea a un canal de Teams *(Media)* *(2025 blueprint)*

Un equipo quiere que se publique una instantánea de un panel en un canal de Microsoft Teams todos los lunes a las 9:00. ¿Qué debe hacer el autor del panel?

A) Compartir el enlace del panel en Teams una vez  
B) Crear una alerta SQL con una condición siempre verdadera  
C) Incrustar el panel en Teams  
D) Crear una programación para el lunes a las 9:00 y añadir un destino de notificación de Teams como suscriptor  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: D**
>
> Las programaciones pueden tener suscriptores, incluidos destinos de notificación de Slack y Microsoft Teams que haya configurado un administrador del espacio de trabajo. Los suscriptores de Teams y Slack reciben una instantánea PNG, un enlace al panel y un PDF. Los suscriptores por correo reciben una instantánea PDF.
>
> Una lista de suscripciones puede contener hasta 100 suscriptores, y un destino de notificación cuenta como uno.

</details>


## Pregunta 22: Relación entre dos medidas *(Fácil)* *(2025 blueprint)*

Un analista quiere mostrar si el gasto en publicidad y los ingresos evolucionan conjuntamente en 200 campañas. ¿Qué visualización comunica mejor esto?

A) Gráfico circular  
B) Contador  
C) Gráfico de dispersión  
D) Embudo  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> Los gráficos de dispersión muestran la relación entre dos variables numéricas. Cada campaña es un punto.
>
> Los gráficos circulares muestran proporciones (A), los contadores muestran un único valor (B) y los embudos muestran la pérdida entre etapas (D).

</details>


## Pregunta 23: ¿Filtro o parámetro? *(Difícil)* *(2025 blueprint)*

Un panel sobre un conjunto de datos grande necesita un selector de región y, además, un selector de ciudad que muestre solo las ciudades de la región elegida. ¿Qué recomienda Databricks considerar en primer lugar?

A) Los filtros de campo, que admiten cascada y suelen rendir mejor en conjuntos de datos grandes  
B) Un parámetro en la consulta de cada conjunto de datos  
C) Un panel distinto por región  
D) Un widget de texto que enumere las regiones  

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> Los filtros de campo filtran los campos de un conjunto de datos, admiten comportamiento en cascada y suelen rendir mejor en conjuntos de datos grandes, por lo que Databricks recomienda considerarlos primero.
>
> Los parámetros modifican la propia consulta. Son útiles cuando el valor debe incluirse dentro del SQL, por ejemplo en un cálculo o como argumento de una función.

</details>


**[← Anterior: preguntas de práctica de Ejecución de consultas](./01-executing-queries.md) | [↑ Volver a las preguntas de práctica de Data Analyst Associate](./README.md) | [Siguiente: preguntas de práctica de Análisis de consultas →](./03-analyzing-queries.md)**

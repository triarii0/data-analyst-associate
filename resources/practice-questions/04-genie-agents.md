# Preguntas de práctica de Espacios AI/BI Genie (Agentes Genie)

**Peso en el examen: 12 %**

Las preguntas abarcan el dominio de Genie del temario de octubre de 2025 y se verificaron con la documentación de Databricks Genie Agents el 26 de octubre de 2026. Los espacios Genie pasaron a denominarse **Agentes Genie** el 8 de julio de 2026; el examen sigue utilizando el término «espacios Genie».

[← Volver a Preguntas de práctica](./README.md) | [Volver a Recursos](../README.md)

---

## Pregunta 1: De dónde procede la respuesta de Genie *(Fácil)* *(2025 blueprint)*

Un usuario de negocio pregunta a un espacio Genie: «¿Cuántos pedidos se enviaron el mes pasado?». ¿Cómo obtiene Genie el número?

A) El modelo lee las filas de la tabla y las cuenta por sí mismo
B) Genie genera una consulta SQL de solo lectura que se ejecuta en el SQL warehouse del espacio y después resume el resultado
C) Genie busca el número en una caché de resultados de paneles anteriores
D) Genie exporta la tabla al proveedor del modelo, que calcula la respuesta

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> Genie traduce la pregunta a SQL de solo lectura, la ejecuta en el SQL warehouse del espacio y devuelve la tabla de resultados, una visualización y un resumen en texto.
>
> El modelo no examina ni cuenta filas (A, D) y Genie no responde a partir de una caché de paneles (C). Por eso se aplican los permisos de Unity Catalog: el SQL generado se ejecuta como cualquier otra consulta.

</details>


## Pregunta 2: Requisito de warehouse *(Fácil)* *(2025 blueprint)*

Un analista está creando un espacio Genie. ¿Qué tipos de SQL warehouse puede utilizar el espacio?

A) Cualquier warehouse, incluido classic
B) Solo warehouses pro o serverless
C) Solo serverless
D) Ningún warehouse: Genie usa su propio cómputo

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> Genie requiere un SQL warehouse pro o serverless; Databricks recomienda serverless por rendimiento.
>
> Los warehouses classic no son compatibles (A). Serverless se recomienda, pero no es la única opción (C). El SQL generado se ejecuta en el warehouse del espacio, no en un cómputo independiente de Genie (D).

</details>


## Pregunta 3: PII y Genie *(Media)* *(2025 blueprint)*

Un espacio Genie cubre una tabla `customers` que tiene una máscara de columna en `email` y un filtro de filas que limita a cada representante a su propia región. Un representante pregunta a Genie: «Enumera los correos electrónicos de todos los clientes». ¿Qué ocurre?

A) Genie devuelve todos los correos porque el autor del espacio creó el espacio
B) Genie rechaza todas las preguntas sobre la tabla customers
C) La consulta se ejecuta con los permisos de Unity Catalog del representante, por lo que se aplican la máscara y el filtro de filas
D) Genie devuelve todas las filas, pero oculta la columna de correo solo en el gráfico

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> Genie se ejecuta en el warehouse del autor, pero el acceso a los datos se comprueba con los permisos de Unity Catalog del usuario que pregunta. Los filtros de filas y las máscaras de columna se aplican por usuario.
>
> La identidad del autor no amplía el acceso (A). Genie no bloquea la tabla (B) y el enmascaramiento se aplica mediante UC en el resultado de la consulta, no solo en un gráfico (D).

</details>


## Pregunta 4: Permisos de los consumidores *(Media)* *(2025 blueprint)*

El equipo de marketing debe poder hacer preguntas en un espacio Genie. ¿Qué combinación es suficiente para un usuario de marketing?

A) `CAN USE` sobre el SQL warehouse y `CAN VIEW` sobre el espacio
B) `CAN RUN` sobre el espacio y `SELECT` sobre todos los objetos de Unity Catalog que utiliza el espacio (además del acceso de consumidor o la licencia de Databricks SQL)
C) Solo `CAN MANAGE` sobre el espacio
D) Derechos de administrador del espacio de trabajo

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> Los consumidores necesitan al menos `CAN VIEW` o `CAN RUN` sobre el espacio, `SELECT` sobre los datos de UC subyacentes y la licencia de acceso de consumidor o de Databricks SQL.
>
> Los consumidores NO necesitan permisos sobre el warehouse: las consultas se ejecutan con las credenciales del warehouse que incorporó el autor, por lo que A añade un permiso innecesario y sigue sin conceder acceso a los datos. `CAN MANAGE` (C) y los derechos de administrador (D) son muy superiores a lo necesario y tampoco conceden acceso a los datos por sí solos.

</details>


## Pregunta 5: Recursos de confianza *(Media)* *(2025 blueprint)*

¿Qué elementos son **recursos de confianza** en un espacio Genie?

A) Instrucciones de texto y sinónimos de columnas
B) Consultas SQL de ejemplo parametrizadas y funciones SQL de Unity Catalog
C) Evaluaciones comparativas y su SQL de referencia
D) Expresiones SQL (filtros, medidas, campos)

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> Los recursos de confianza son consultas de ejemplo parametrizadas y funciones SQL cuya lógica exacta ha verificado un autor; las respuestas que los utilizan proceden de esa lógica verificada.
>
> Las instrucciones de texto y los sinónimos (A) son contexto, no lógica verificada. Las evaluaciones comparativas (C) solo sirven para puntuar la exactitud. Las expresiones SQL (D) son entradas del almacén de conocimiento.

</details>


## Pregunta 6: Sintaxis de parámetros *(Fácil)* *(2025 blueprint)*

Un autor quiere una consulta de ejemplo que responda a «¿Cuáles son los ingresos de la región X?» para cualquier región. ¿Cómo se escribe el parámetro en el SQL de ejemplo?

A) `WHERE region = ?`
B) `WHERE region = {{region}}`
C) `WHERE region = :region_name`
D) `WHERE region = $region`

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: C**
>
> Las consultas de ejemplo de Genie utilizan parámetros con nombre: escriba dos puntos seguidos de un nombre (`:parameter_name`) o haga clic en **Add parameter**.
>
> El icono de engranaje establece el tipo de datos del parámetro (String, Date, Date and Time, Decimal, Integer) y un comentario. Los marcadores `?`, la sintaxis de mustache `{{ }}` y las variables `$` no son la sintaxis de parámetros de Genie.

</details>


## Pregunta 7: Elegir una palanca de curación *(Media)* *(2025 blueprint)*

Genie a veces suma `paid_amount` y a veces `invoice_total` cuando los usuarios preguntan por ingresos. La regla de negocio es que los ingresos siempre son `SUM(invoice_total)`. ¿Cuál es la corrección recomendada?

A) Añadir una instrucción de texto: «Revenue means invoice_total»
B) Añadir una expresión SQL de Measure denominada `revenue` con el código `SUM(invoice_total)` y sinónimos como «sales»
C) Añadir una pregunta de evaluación comparativa sobre los ingresos
D) Ocultar la columna `paid_amount` a todos los usuarios con una máscara de columna de UC

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> La guía de curación indica priorizar las expresiones SQL y el SQL de ejemplo frente a las instrucciones de texto, y usar instrucciones de texto solo como último recurso. Una expresión SQL de Measure codifica el KPI de forma exacta.
>
> Una instrucción de texto (A) funciona con menos fiabilidad. Una evaluación comparativa (C) solo mide la exactitud: no modifica el contexto de Genie. Enmascarar una columna (D) cambia el acceso a los datos de todos y es la herramienta equivocada para un problema de interpretación.

</details>


## Pregunta 8: Los usuarios escriben valores distintos *(Media)* *(2025 blueprint)*

Los usuarios preguntan por «NYC», pero la columna `city` almacena `New York City`. Genie sigue devolviendo resultados vacíos. ¿Qué función resuelve esto?

A) La correspondencia de entidades en la columna `city` (en la configuración avanzada de la columna)
B) Una instrucción de texto que enumere todos los alias de la ciudad
C) Aumentar el tamaño del warehouse
D) Un filtro de filas sobre `city`

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: A**
>
> La correspondencia de entidades (el diccionario de valores) asocia lo que los usuarios escriben con los valores realmente almacenados. Funciona en columnas de cadena y requiere asistencia de formato; los límites son 120 columnas y 1.024 valores por columna.
>
> Una lista larga de alias en las instrucciones (B) es la palanca de último recurso y difícil de mantener. El tamaño del warehouse (C) no afecta a la interpretación. Un filtro de filas (D) restringe el acceso, pero no resuelve los términos.

</details>


## Pregunta 9: Almacén de conocimiento frente a Unity Catalog *(Media)* *(2025 blueprint)*

Un curador edita la descripción de una tabla en el almacén de conocimiento del espacio Genie. ¿Qué ocurre con el comentario de la tabla en Unity Catalog?

A) Se sobrescribe con la nueva descripción
B) Permanece sin cambios: el almacén de conocimiento está limitado al espacio; **Reset** restaura la descripción de UC en el espacio
C) Se elimina
D) Se copia en todos los demás espacios Genie que usan la tabla

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> Las descripciones y los sinónimos del almacén de conocimiento están limitados al espacio Genie y no sobrescriben los metadatos almacenados en Unity Catalog. **Reset** vuelve a traer la descripción de UC al espacio.
>
> Para mejorar todos los espacios y herramientas a la vez, actualice el propio comentario de UC (por ejemplo, con `COMMENT ON TABLE`).

</details>


## Pregunta 10: Puntuación de evaluaciones comparativas *(Difícil)* *(2025 blueprint)*

Una pregunta de evaluación comparativa tiene una SQL Answer que devuelve `region, revenue`. En una ejecución en modo Chat, Genie devuelve los ingresos correctos por región más una columna adicional `order_count`. ¿Cómo se etiqueta el resultado?

A) Good, porque los valores de ingresos coinciden
B) Bad, porque el resultado tiene columnas adicionales
C) Siempre se necesita revisión manual
D) Se omite porque las columnas difieren

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> El modo Chat compara los conjuntos de resultados con la SQL Answer. Los resultados con columnas adicionales se etiquetan como **Bad**; las coincidencias exactas, las coincidencias con distinto orden de filas o los valores numéricos que se redondean a las mismas 4 cifras significativas se etiquetan como **Good**.
>
> El curador puede abrir la ejecución, comparar **Model output** con **Ground truth** y volver a etiquetar el resultado o usar **Update ground truth**. El modo Agent, en cambio, utiliza un juez LLM guiado por la nota de evaluación.

</details>


## Pregunta 11: Para qué sirven las evaluaciones comparativas *(Fácil)* *(2025 blueprint)*

¿Cuál es el propósito de las preguntas de evaluación comparativa en un espacio Genie?

A) Se añaden al contexto de Genie para que responda mejor a preguntas similares
B) Puntúan la exactitud del espacio de forma sistemática; no alimentan el contexto de Genie
C) Sustituyen a las consultas SQL de ejemplo
D) Limitan qué preguntas pueden hacer los usuarios

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> Las evaluaciones comparativas son preguntas de prueba que solo se usan para evaluar la exactitud. Se pueden añadir hasta 500 y cada una se ejecuta como una conversación nueva, sin contexto de hilo.
>
> Para mejorar las respuestas, añada SQL de ejemplo, expresiones SQL, funciones o instrucciones; después, las evaluaciones comparativas confirman la mejora.

</details>


## Pregunta 12: Actuar ante los comentarios de los usuarios *(Media)* *(2025 blueprint)*

Tras un mes en producción, los usuarios han marcado varias respuestas de Genie con **Fix it** y **Request review**. ¿Cuál es el mejor flujo de mantenimiento?

A) Aumentar el tamaño del warehouse y pedir a los usuarios que lo intenten de nuevo
B) Revisar las preguntas en la pestaña **Monitor**, aplicar correcciones de curación (instrucciones, recursos de confianza, almacén de conocimiento o metadatos de UC), añadir las preguntas fallidas como evaluaciones comparativas y volver a ejecutar las evaluaciones
C) Eliminar las conversaciones para que desaparezcan los comentarios
D) Recrear el espacio desde cero

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> La habilidad de «optimización» del examen es un bucle: hacer seguimiento de las preguntas de los usuarios, la exactitud y los comentarios (pestaña Monitor, Weekly digest); actualizar las instrucciones y los recursos de confianza a partir de las aportaciones de las partes interesadas; actualizar los metadatos de UC; y validar con evaluaciones comparativas.
>
> Un cómputo mayor (A) no corrige errores de interpretación, eliminar conversaciones (C) descarta la evidencia y reconstruir (D) pierde la curación.

</details>


## Pregunta 13: Incrustar un espacio *(Media)* *(2025 blueprint)*

Un equipo quiere el espacio Genie dentro de su portal de intranet. ¿Qué se requiere?

A) Solo un enlace público: cualquier persona con la URL puede usarlo sin iniciar sesión
B) Un administrador del espacio de trabajo añade el dominio del portal a las superficies de incrustación permitidas, un autor con `CAN MANAGE` copia el código iframe desde **Share** y los usuarios finales siguen necesitando acceso al espacio y a los datos
C) Exportar el espacio como un archivo HTML estático
D) Conceder `SELECT` al servidor web del portal sobre todas las tablas

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> La incrustación con iframe requiere que el dominio de host esté en la lista de permitidos en la configuración de control de acceso de Genie, un autor con al menos `CAN MANAGE` y usuarios finales con acceso explícito al espacio y a sus datos. Se pide autenticación a los usuarios que no han iniciado sesión.
>
> No existe acceso público anónimo (A), ni exportación estática (C), ni permisos concedidos a un servidor web (D).

</details>


## Pregunta 14: Delimitar el alcance de un espacio *(Media)* *(2025 blueprint)*

Un analista quiere un espacio Genie para las 40 tablas de toda la empresa. ¿Qué recomienda la guía oficial de curación?

A) Añadir las 40 tablas: el límite es 50
B) Procurar cinco tablas o menos por espacio, preunir tablas en vistas o vistas de métricas cuando un tema requiera más y dividir los dominios amplios en espacios centrados
C) Añadir las tablas, pero ocultar todas las columnas excepto las claves
D) Usar instrucciones de texto para describir las 40 tablas

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> La guía de curación recomienda procurar cinco tablas o menos e incluir solo las necesarias para las preguntas que el espacio debe atender; el límite estricto es de 50 tablas, vistas o vistas de métricas.
>
> Muchas tablas diluyen el contexto de Genie y aumentan la probabilidad de uniones incorrectas. Ocultar columnas (C) o escribir instrucciones largas (D) no solucionan un alcance excesivamente amplio.

</details>


## Pregunta 15: Columnas ocultas *(Difícil)* *(2025 blueprint)*

Un curador oculta la columna `salary` en el almacén de conocimiento de un espacio Genie. ¿Qué afirmación es verdadera?

A) Ya nadie puede leer `salary`
B) Genie deja de usar `salary` en su contexto, pero los usuarios con acceso directo a la tabla pueden seguir consultándola: use permisos de UC o máscaras de columna para restringir el acceso
C) La columna se elimina de la tabla
D) Ocultar la columna también la oculta en Catalog Explorer

<details>
<summary>💡 Ver respuesta y explicación</summary>

> [!success]- Respuesta
> **Respuesta correcta: B**
>
> Ocultar una columna solo la elimina del contexto de Genie. Las columnas ocultas siguen siendo visibles para los usuarios con acceso directo a la tabla, por lo que ocultarlas no es un control de seguridad.
>
> Restrinja los datos sensibles con permisos de Unity Catalog, filtros de filas y máscaras de columna.

</details>


[← Volver a Preguntas de práctica](./README.md) | [Volver a Recursos](../README.md)

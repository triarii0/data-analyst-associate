# Control de acceso y seguridad

## 1. Modelo de Seguridad en Unity Catalog

Unity Catalog centraliza el control de acceso para todos los activos de datos e IA bajo el principio de **denegación por defecto (*Deny by default*)** y **mínimo privilegio (*Least Privilege*)**.

```mermaid
flowchart TD
    U["<b>Usuario / Grupo / Service Principal</b>"] --> C["<b>1. Catalog</b><br/>Requiere: USE CATALOG"]
    C --> S["<b>2. Schema</b><br/>Requiere: USE SCHEMA"]
    S --> T["<b>3. Table / View</b><br/>Requiere: SELECT (o MODIFY)"]
    T --> G{"<b>¿Seguridad Granular?</b>"}
    G -->|"Sin restricciones"| R1["Devuelve todas las filas y columnas"]
    G -->|"Row Filter / Column Mask<br/>o Dynamic View"| R2["Filtra filas y enmascara columnas<br/>según identidad del usuario"]

    style U fill:#2b5c8f,color:#fff
    style C fill:#2e7d32,color:#fff
    style S fill:#ef6c00,color:#fff
    style T fill:#6a1b9a,color:#fff
    style R2 fill:#c62828,color:#fff
```

### Reglas fundamentales de evaluación en el examen:
1. **No hay herencia automática hacia abajo por defecto (salvo privilegios `ALL PRIVILEGES` o concesiones explícitas a nivel de esquema/catálogo):**
   * Si otorgas `GRANT SELECT ON SCHEMA sales.gold TO grupo`, el grupo tendrá `SELECT` sobre todas las tablas actuales y futuras de ese esquema.
   * Si otorgas `GRANT SELECT ON TABLE sales.gold.orders TO grupo`, el grupo **aún necesita** `USE CATALOG` en `sales` y `USE SCHEMA` en `gold` para poder llegar a la tabla.
2. **Propiedad (`OWNER`):**
   * Quien crea un objeto (catálogo, esquema, tabla o vista) se convierte automáticamente en su **`OWNER`**.
   * El propietario tiene control total sobre el objeto y puede otorgar permisos (`GRANT`) a otros usuarios o grupos.
   * *Buena práctica de examen:* En producción, la propiedad de tablas y esquemas debe asignarse a un **Grupo** (`ALTER TABLE ... SET OWNER TO \`data_team\``) y no a un usuario individual.
3. **Permisos de Workspace vs. Permisos de Unity Catalog:**
   * Para ejecutar una consulta sobre una tabla, un analista necesita permisos en **dos planos**:
     * **Cómputo (Workspace):** `CAN USE` sobre el **SQL Warehouse**.
     * **Datos (Unity Catalog):** `USE CATALOG` + `USE SCHEMA` + `SELECT`.


## 2. Tabla de Privilegios Principales de Unity Catalog

| Privilegio | Objeto(s) aplicables | Qué permite hacer |
| :--- | :--- | :--- |
| **`USE CATALOG`** | Catalog | Entrar al catálogo y listar sus esquemas (no otorga lectura de tablas por sí solo). |
| **`USE SCHEMA`** | Schema | Entrar al esquema y listar sus tablas/vistas (no otorga lectura de tablas por sí solo). |
| **`SELECT`** | Table, View, Materialized View, Streaming Table | Leer datos mediante consultas `SELECT`. |
| **`MODIFY`** | Table, Streaming Table | Insertar, actualizar o eliminar datos (`INSERT`, `UPDATE`, `DELETE`, `MERGE`, `COPY INTO`). |
| **`CREATE TABLE` / `CREATE VIEW`** | Schema | Crear nuevas tablas o vistas dentro del esquema. |
| **`EXECUTE`** | Function (UDF) | Ejecutar una función SQL/Python registrada en Unity Catalog. |
| **`BROWSE`** | Catalog, Schema, Table, View | Ver metadatos, comentarios y linaje en **Catalog Explorer** sin poder leer los datos (`SELECT`). |
| **`APPLY TAG`** | Catalog, Schema, Table, View, Column | Añadir o editar etiquetas (`TAGS`) en el objeto. |
| **`ALL PRIVILEGES`** | Cualquier objeto securizable | Otorga todos los privilegios disponibles sobre ese objeto (excepto la propiedad `OWNER`). |

### Sintaxis SQL de Control de Acceso (`GRANT` / `REVOKE` / `SHOW`)
```sql
-- 1. Dar acceso de lectura a una tabla específica
GRANT USE CATALOG ON CATALOG prod_sales TO `marketing_analysts`;
GRANT USE SCHEMA ON SCHEMA prod_sales.gold TO `marketing_analysts`;
GRANT SELECT ON TABLE prod_sales.gold.campaign_metrics TO `marketing_analysts`;

-- 2. Dar acceso de lectura a TODAS las tablas actuales y futuras de un esquema
GRANT SELECT ON SCHEMA prod_sales.gold TO `bi_readers`;

-- 3. Revocar un permiso
REVOKE SELECT ON TABLE prod_sales.gold.campaign_metrics FROM `marketing_analysts`;

-- 4. Auditar quién tiene permisos sobre una tabla
SHOW GRANTS ON TABLE prod_sales.gold.campaign_metrics;
```


## 3. Seguridad Granular: Nivel de Fila y Nivel de Columna (¡Muy Preguntado!)

Cuando una misma tabla contiene datos sensibles (ej. salarios, correos PII o ventas de distintos países) y distintos usuarios deben ver subconjuntos diferentes sin duplicar la tabla, existen **dos enfoques principales**:

### Enfoque A: Row Filters y Column Masks Nativos (Recomendado sobre tablas)
Se definen funciones SQL en Unity Catalog y se vinculan directamente a la tabla base. Cualquier consulta contra esa tabla aplica la regla de forma transparente:

1. **Column Mask (Enmascaramiento de Columna):** Oculta o transforma el valor de una columna sensible según la identidad del usuario.
   ```sql
   -- 1. Crear función de enmascaramiento
   CREATE FUNCTION prod_hr.gold.mask_ssn(ssn STRING)
   RETURN CASE
       WHEN is_account_group_member('hr_payroll') THEN ssn
       ELSE 'XXX-XX-' || RIGHT(ssn, 4)
   END;

   -- 2. Aplicar la máscara directamente a la columna de la tabla
   ALTER TABLE prod_hr.gold.employees
   ALTER COLUMN ssn SET MASK prod_hr.gold.mask_ssn;
   ```

2. **Row Filter (Filtro de Fila):** Filtra qué filas puede ver cada usuario según su grupo o atributo.
   ```sql
   -- 1. Crear función booleana de filtrado de fila
   CREATE FUNCTION prod_sales.gold.region_filter(region_col STRING)
   RETURN IF(is_account_group_member('global_execs'), true, region_col = 'EMEA');

   -- 2. Aplicar el filtro a la tabla
   ALTER TABLE prod_sales.gold.orders
   SET ROW FILTER prod_sales.gold.region_filter ON (region);
   ```

### Enfoque B: Vistas Dinámicas (`Dynamic Views`)
* Útiles cuando quieres combinar filtrado de filas, enmascaramiento de columnas y transformaciones/joins en una vista virtual sin alterar la tabla base (o cuando trabajas sobre tablas compartidas de solo lectura).
* Utilizan las funciones de identidad **`current_user()`** y **`is_account_group_member('nombre_grupo')`**.

> **¡Trampa de examen con funciones de identidad!**  
> Utiliza siempre **`is_account_group_member('grupo')`** (a nivel de cuenta en Unity Catalog) en lugar de la antigua `is_member('grupo')` (que solo revisaba grupos locales del workspace heredado).


## 4. Credenciales de Ejecución en Objetos Compartidos (`Run as Viewer` vs. `Run as Owner`)

El examen evalúa constantemente cómo interactúan los permisos de Unity Catalog al compartir **Consultas**, **AI/BI Dashboards** y **Genie Spaces**:

| Objeto Compartido | Opciones de Credenciales | Comportamiento de Seguridad |
| :--- | :--- | :--- |
| **AI/BI Dashboard (Publicado)** | **`Run as Viewer`** *(por defecto)* o **`Run as Owner`** | *Viewer:* Aplica los permisos UC, *Row Filters* y *Column Masks* de cada espectador.<br>*Owner:* Ejecuta con los permisos del propietario (útil para compartir resúmenes agregados con usuarios sin acceso a las tablas base). |
| **Consulta SQL (*Saved Query*)** | **`Run as Viewer`** o **`Run as Owner`** | *Viewer:* Requiere que el compañero tenga `SELECT` en las tablas.<br>*Owner:* Permite ejecutar la consulta usando los permisos del autor. |
| **AI/BI Genie Space** | **Siempre `Run as Viewer`** | Genie **siempre** ejecuta bajo la identidad del usuario que hace la pregunta; nunca omite las políticas de Unity Catalog. |
| **Suscripciones Programadas (*Schedules*)** | **Siempre `Run as Owner`** | Los correos/Slack programados de un dashboard se generan usando los permisos del propietario. |


## 5. Auditoría y Cumplimiento (`System Tables`)

Unity Catalog registra automáticamente todos los eventos de acceso, cambios de permisos (`GRANT`/`REVOKE`), consultas ejecutadas y linaje en el catálogo **`system`** (accesible solo para administradores o analistas con permisos explícitos sobre esas tablas del sistema):

* **`system.access.audit`:** Registro detallado de quién accedió a qué tabla, quién ejecutó un `GRANT` o `DROP`, intentos de acceso denegados y exportaciones de datos.
* **`system.access.table_lineage` / `column_lineage`:** Trazabilidad completa de origen y destino de datos.
* **`system.query.history`:** Historial de todas las consultas SQL ejecutadas en los SQL Warehouses del workspace/cuenta.


## 6. Métodos de autenticación

### Personal Access Token (PAT)

El más común para el acceso mediante programación:

```bash
# Generate PAT (Account Settings → User Settings → Personal Access Tokens)

dapi1234567890abcdefghijklmnopqrstu

# Use in API calls

curl -X GET https://dbc-abc123.cloud.databricks.com/api/2.1/catalogs \
  -H "Authorization: Bearer dapi1234567890abcdefghijklmnopqrstu"

# Use in connections

connection = sql.connect(
    server_hostname="dbc-abc123.cloud.databricks.com",
    http_path="/sql/1.0/warehouses/abc123",
    auth_type="pat",
    token="dapi1234567890abcdefghijklmnopqrstu"
)
```

**Seguridad:**

- Se crea por usuario
- Puede revocarse individualmente
- Caduca a los 90 días (configurable)
- Debe rotarse periódicamente

### Single Sign-On (SSO)

Autenticación empresarial mediante un proveedor de identidad:

```yaml
SSO Configuration:
  Identity Provider: Azure AD, Okta, Google Workspace
  Protocol: SAML 2.0 or OAuth 2.0
  Automatic provisioning: Yes
  MFA support: Yes

Workflow:
  1. User clicks Databricks link
  2. Redirected to SSO provider
  3. User authenticates
  4. Authority redirected back to Databricks
  5. Session created with enterprise identity
```

### Service Principals (M2M)

Para autenticación de automatización y de servicio a servicio:

```python
# Service Principal Authentication

connection = sql.connect(
    server_hostname="dbc-abc123.cloud.databricks.com",
    http_path="/sql/1.0/warehouses/abc123",
    auth_type="service_principal",
    tenant_id="tenant-id",
    client_id="client-id",  # Service Principal ID
    client_secret="client-secret"  # Secret
)

# In automated pipeline (e.g., Airflow)

@task
def query_databricks():
    with sql.connect(...service_principal_auth...) as conn:
        cursor = conn.cursor()
        cursor.execute("SELECT * FROM prod.sales.orders")
        results = cursor.fetchall()
    return results
```


## 7. Preguntas Tipo Examen (Autoevaluación)

**Pregunta 1:** *Una tabla `finance.gold.transactions` contiene transacciones de todo el mundo. La política de seguridad exige que los analistas del grupo `eu_analysts` solo vean filas donde `region = 'EUROPE'`, mientras que los miembros de `global_auditors` puedan ver todas las filas, sin crear múltiples copias de la tabla. ¿Cuál es la solución más directa en Unity Catalog?*
* **Respuesta:** Crear una **función de filtro de fila (*Row Filter*)** utilizando `is_account_group_member('global_auditors')` y aplicarla a la tabla mediante `ALTER TABLE finance.gold.transactions SET ROW FILTER ... ON (region)`.

**Pregunta 2:** *Un analista senior crea una vista dinámica que filtra datos por departamento usando la función `is_member('finance_team')`. Sin embargo, los miembros del grupo `finance_team` configurado a nivel de cuenta en Unity Catalog ven cero filas. ¿Cuál es el error en la consulta?*
* **Respuesta:** Debe reemplazar `is_member()` (grupos locales heredados) por **`is_account_group_member('finance_team')`**, que evalúa la pertenencia a grupos a nivel de cuenta de Unity Catalog.

Pregunta 3: Un administrador otorga `GRANT SELECT ON TABLE prod.sales.revenue TO \analysts;`, pero cuando los usuarios de ese grupo ejecutan `SELECT * FROM prod.sales.revenue;`, reciben el error `PERMISSION_DENIED`. ¿Qué privilegios faltan?
* **Respuesta:** Faltan los privilegios de navegación en los niveles superiores de la jerarquía: `USE CATALOG ON CATALOG prod` y `USE SCHEMA ON SCHEMA prod.sales.`

Pregunta 4: El equipo de cumplimiento necesita saber qué usuarios ejecutaron consultas `SELECT` sobre la tabla confidencial `hr.gold.salaries` durante los últimos 30 días. ¿Dónde pueden consultar esta información mediante SQL?
* **Respuesta:** En la tabla de auditoría del sistema system.access.audit.


## 8. Problemas y errores comunes

### El permiso se otorgó pero los datos siguen inaccesibles

**Escenario:** El administrador otorga SELECT sobre una tabla, pero el usuario sigue recibiendo un error de permiso denegado.
**Solución:** Unity Catalog requiere `USE CATALOG` + `USE SCHEMA` sobre los objetos superiores, además del permiso a nivel de tabla.


## 9. Consejos para el examen

- Proteja los datos PII con máscaras de columna en la tabla (o vistas dinámicas); compruebe los grupos con `is_account_group_member()`
- Los filtros de filas aplican seguridad a nivel de fila directamente a una tabla con `ALTER TABLE ... SET ROW FILTER`
- Unity Catalog no admite `DENY`; retire el acceso con `REVOKE`
- La autenticación con Service Principal (M2M) es la mejor opción para canalizaciones CI/CD y automatización
- Use `SHOW GRANTS ON TABLE tablename` para verificar quién tiene acceso a una tabla

## 10. Conclusiones clave

- **Autenticación**: PAT (mediante programación), SSO (empresarial), Service Principals (automatización)
- **Permisos del workspace**: ACL sobre consultas, paneles, notebooks, carpetas y warehouses
- **RBAC de catálogo**: privilegios SELECT, MODIFY, CREATE TABLE, CREATE SCHEMA
- **Jerarquía**: los permisos se propagan (catálogo → esquema → tabla → fila/columna)
- **Nivel de fila**: filtros de filas en tablas o vistas dinámicas
- **Nivel de columna**: máscaras de columna en tablas o vistas dinámicas
- **Grupos**: simplifican la gestión de permisos entre muchos usuarios
- **Usuario actual**: use current_user(), current_catalog(), current_schema()


## 11. Temas relacionados

- [Conceptos básicos de Unity Catalog](../../../shared/fundamentals/unity-catalog-basics.md) - Base para comprender el control de acceso en Unity Catalog
- [Guía rápida de Unity Catalog](../../../shared/cheat-sheets/unity-catalog-quick-ref.md) - Referencia rápida de permisos y sintaxis de GRANT
- [Unity Catalog](../05-understanding-databricks-platform/01-unity-catalog.md) - La plataforma de gobernanza que sustenta el control de acceso


## 12. Documentación oficial

- [Listas de control de acceso](https://docs.databricks.com/aws/en/security/auth/access-control/)
- [Privilegios de Unity Catalog y objetos protegibles](https://docs.databricks.com/aws/en/data-governance/unity-catalog/manage-privileges/privileges)

---

**[↑ Volver a la sección](./README.md) | [Siguiente: Propiedad, acceso detallado y PII →](./02-ownership-fine-grained-access-and-pii.md)**

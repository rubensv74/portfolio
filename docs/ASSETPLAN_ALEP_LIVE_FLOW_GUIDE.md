# AssetPlan / ALEP — guía ejecutable del flow live de Assets

Estado: diseño listo para construir.  
Última decisión: Assets lee siempre desde Fabric en vivo. No usa cache, no llama al procedimiento SQL antiguo y no crea casos unresolved.

## 1. Alcance cerrado

El flow es **Power Apps (V2) → Power Automate → Fabric → Power Apps**.

Fuente única:

\`ops_ing_trd277.FAC_ALEP_EQ_EXPORT_OFFICIAL_CURRENT\`

Identidad del asset:

\`ProjectCode + EquipmentId\`

Mapeo de columnas:

| Respuesta del flow | Fuente Fabric |
|---|---|
| ProjectCode | ID_COD_PROJECT |
| EquipmentId | ID_EQUIPMENT |
| TagNumber | ID_TAG_NUMBER |
| EquipmentType | DS_EQUIPMENT_TYPE |
| CommodityCode | ID_COMMODITY_CODE |
| EquipmentDescription | DS_DESCRIPTION |
| Discipline | DS_DISCIPLINE |
| RevisionNumber | NU_REVISION |
| Quantity | NU_QUANTITY |

La clasificación de familia no bloquea Assets. Para esta fase, el resultado devuelve \`FamilyResolutionState = NOT_ENRICHED\`. La decisión de tipos unresolved queda para el modal de Technical Fields.

## 2. Contrato del flow

Nombre recomendado:

\`AssetPlan_GetAlepAssetsPaged\`

### Entradas del trigger Power Apps (V2)

Crear estos inputs:

| Input | Tipo | Obligatorio | Valor por defecto |
|---|---|---:|---:|
| ProjectCode | Number | Sí | — |
| PageNumber | Number | No | 1 |
| PageSize | Number | No | 50 |
| SearchText | Text | No | vacío |
| EquipmentType | Text | No | vacío |
| CommodityCode | Text | No | vacío |

Reglas:

- \`ProjectCode\` debe ser positivo.
- \`PageNumber\` se limita a mínimo 1.
- \`PageSize\` se limita entre 1 y 200.
- Los filtros de texto son opcionales.
- No se debe consultar \`AssetPlan.AlepAssetProjectCache\`.
- No se debe llamar a \`AssetPlan.usp_AlepAsset_SearchPaged\`.

## 3. Acciones y expresiones

Usa exactamente estos nombres de acción. Si Power Automate añade sufijos, actualiza las referencias de las expresiones para que coincidan.

### 3.1 Normalización de inputs

Agregar cuatro acciones **Compose**.

**Compose_ProjectCode**

\`\`\`
int(triggerBody()?['ProjectCode'])
\`\`\`

**Compose_PageNumber**

\`\`\`
max(1, int(coalesce(triggerBody()?['PageNumber'], 1)))
\`\`\`

**Compose_PageSize**

\`\`\`
min(200, max(1, int(coalesce(triggerBody()?['PageSize'], 50))))
\`\`\`

**Compose_SearchText**

\`\`\`
trim(coalesce(triggerBody()?['SearchText'], ''))
\`\`\`

Agregar también:

**Compose_EquipmentType**

\`\`\`
trim(coalesce(triggerBody()?['EquipmentType'], ''))
\`\`\`

**Compose_CommodityCode**

\`\`\`
trim(coalesce(triggerBody()?['CommodityCode'], ''))
\`\`\`

### 3.2 Paginación

Agregar dos acciones **Compose**.

**Compose_RowFrom**

\`\`\`
add(
  1,
  mul(
    sub(outputs('Compose_PageNumber'), 1),
    outputs('Compose_PageSize')
  )
)
\`\`\`

**Compose_RowTo**

\`\`\`
mul(
  outputs('Compose_PageNumber'),
  outputs('Compose_PageSize')
)
\`\`\`

### 3.3 Escapado SQL

Agregar tres acciones **Compose**. El valor resultante no lleva comillas externas; la plantilla SQL las añadirá.

**Compose_SearchTextSql**

\`\`\`
replace(outputs('Compose_SearchText'),'''','''''')
\`\`\`

**Compose_EquipmentTypeSql**

\`\`\`
replace(outputs('Compose_EquipmentType'),'''','''''')
\`\`\`

**Compose_CommodityCodeSql**

\`\`\`
replace(outputs('Compose_CommodityCode'),'''','''''')
\`\`\`

## 4. Consulta paginada

### 4.1 Plantilla SQL

Agregar un **Compose** llamado \`Compose_PageSqlTemplate\` con este texto literal:

\`\`\`sql
WITH base AS
(
    SELECT
        CAST(ID_COD_PROJECT AS INT) AS ProjectCode,
        CAST(ID_EQUIPMENT AS STRING) AS EquipmentId,
        CAST(ID_TAG_NUMBER AS STRING) AS TagNumber,
        CAST(DS_EQUIPMENT_TYPE AS STRING) AS EquipmentType,
        CAST(ID_COMMODITY_CODE AS STRING) AS CommodityCode,
        CAST(DS_DESCRIPTION AS STRING) AS EquipmentDescription,
        CAST(DS_DISCIPLINE AS STRING) AS Discipline,
        CAST(NU_REVISION AS INT) AS RevisionNumber,
        CAST(NU_QUANTITY AS DECIMAL(18,3)) AS Quantity
    FROM ops_ing_trd277.FAC_ALEP_EQ_EXPORT_OFFICIAL_CURRENT
    WHERE CAST(ID_COD_PROJECT AS STRING) = '__PROJECT_CODE__'
),
filtered AS
(
    SELECT *
    FROM base
    WHERE
        (
            '__SEARCH_TEXT__' = ''
            OR LOWER(COALESCE(EquipmentId, '')) LIKE CONCAT('%', LOWER('__SEARCH_TEXT__'), '%')
            OR LOWER(COALESCE(TagNumber, '')) LIKE CONCAT('%', LOWER('__SEARCH_TEXT__'), '%')
            OR LOWER(COALESCE(EquipmentType, '')) LIKE CONCAT('%', LOWER('__SEARCH_TEXT__'), '%')
            OR LOWER(COALESCE(CommodityCode, '')) LIKE CONCAT('%', LOWER('__SEARCH_TEXT__'), '%')
            OR LOWER(COALESCE(EquipmentDescription, '')) LIKE CONCAT('%', LOWER('__SEARCH_TEXT__'), '%')
        )
        AND
        (
            '__EQUIPMENT_TYPE__' = ''
            OR LOWER(COALESCE(EquipmentType, '')) = LOWER('__EQUIPMENT_TYPE__')
        )
        AND
        (
            '__COMMODITY_CODE__' = ''
            OR LOWER(COALESCE(CommodityCode, '')) = LOWER('__COMMODITY_CODE__')
        )
),
numbered AS
(
    SELECT
        *,
        ROW_NUMBER() OVER
        (
            ORDER BY
                COALESCE(EquipmentId, ''),
                COALESCE(TagNumber, ''),
                COALESCE(EquipmentType, ''),
                COALESCE(CommodityCode, '')
        ) AS RowNumber
    FROM filtered
)
SELECT
    ProjectCode,
    EquipmentId,
    TagNumber,
    EquipmentType,
    CommodityCode,
    EquipmentDescription,
    Discipline,
    RevisionNumber,
    Quantity,
    CAST('NOT_ENRICHED' AS STRING) AS FamilyResolutionState
FROM numbered
WHERE RowNumber BETWEEN __ROW_FROM__ AND __ROW_TO__
ORDER BY RowNumber
\`\`\`

### 4.2 Sustitución de tokens

Agregar un **Compose** llamado \`Compose_AssetsPageQuery\` con esta expresión completa:

\`\`\`
replace(
  replace(
    replace(
      replace(
        replace(
          replace(
            outputs('Compose_PageSqlTemplate'),
            '__PROJECT_CODE__',
            string(outputs('Compose_ProjectCode'))
          ),
          '__SEARCH_TEXT__',
          outputs('Compose_SearchTextSql')
        ),
        '__EQUIPMENT_TYPE__',
        outputs('Compose_EquipmentTypeSql')
      ),
      '__COMMODITY_CODE__',
      outputs('Compose_CommodityCodeSql')
    ),
    '__ROW_FROM__',
    string(outputs('Compose_RowFrom'))
  ),
  '__ROW_TO__',
  string(outputs('Compose_RowTo'))
)
\`\`\`

### 4.3 Ejecución Fabric

Agregar la acción de ejecución de consulta Fabric disponible en el entorno y nombrarla:

\`Fabric_Query_AssetsPage\`

En el campo de consulta, usar:

\`\`\`
outputs('Compose_AssetsPageQuery')
\`\`\`

La acción debe ejecutar la consulta contra el workspace/lakehouse que contiene \`ops_ing_trd277.FAC_ALEP_EQ_EXPORT_OFFICIAL_CURRENT\`.

No sustituyas esta acción por SQL Server ni por una tabla cacheada.

## 5. Consulta de total

La paginación necesita el total filtrado para que la pantalla pueda mostrar \`hasNextPage\`.

### 5.1 Plantilla

Agregar un **Compose** llamado \`Compose_CountSqlTemplate\`:

\`\`\`sql
SELECT
    COUNT(*) AS TotalRows
FROM ops_ing_trd277.FAC_ALEP_EQ_EXPORT_OFFICIAL_CURRENT
WHERE CAST(ID_COD_PROJECT AS STRING) = '__PROJECT_CODE__'
  AND
  (
      '__SEARCH_TEXT__' = ''
      OR LOWER(COALESCE(CAST(ID_EQUIPMENT AS STRING), '')) LIKE CONCAT('%', LOWER('__SEARCH_TEXT__'), '%')
      OR LOWER(COALESCE(CAST(ID_TAG_NUMBER AS STRING), '')) LIKE CONCAT('%', LOWER('__SEARCH_TEXT__'), '%')
      OR LOWER(COALESCE(CAST(DS_EQUIPMENT_TYPE AS STRING), '')) LIKE CONCAT('%', LOWER('__SEARCH_TEXT__'), '%')
      OR LOWER(COALESCE(CAST(ID_COMMODITY_CODE AS STRING), '')) LIKE CONCAT('%', LOWER('__SEARCH_TEXT__'), '%')
      OR LOWER(COALESCE(CAST(DS_DESCRIPTION AS STRING), '')) LIKE CONCAT('%', LOWER('__SEARCH_TEXT__'), '%')
  )
  AND
  (
      '__EQUIPMENT_TYPE__' = ''
      OR LOWER(COALESCE(CAST(DS_EQUIPMENT_TYPE AS STRING), '')) = LOWER('__EQUIPMENT_TYPE__')
  )
  AND
  (
      '__COMMODITY_CODE__' = ''
      OR LOWER(COALESCE(CAST(ID_COMMODITY_CODE AS STRING), '')) = LOWER('__COMMODITY_CODE__')
  )
\`\`\`

### 5.2 Sustitución

Agregar un **Compose** llamado \`Compose_AssetsCountQuery\`:

\`\`\`
replace(
  replace(
    replace(
      replace(
        outputs('Compose_CountSqlTemplate'),
        '__PROJECT_CODE__',
        string(outputs('Compose_ProjectCode'))
      ),
      '__SEARCH_TEXT__',
      outputs('Compose_SearchTextSql')
    ),
    '__EQUIPMENT_TYPE__',
    outputs('Compose_EquipmentTypeSql')
  ),
  '__COMMODITY_CODE__',
  outputs('Compose_CommodityCodeSql')
)
\`\`\`

Agregar una segunda acción Fabric llamada:

\`Fabric_Query_AssetsCount\`

Consulta:

\`\`\`
outputs('Compose_AssetsCountQuery')
\`\`\`

## 6. Normalización de la respuesta Fabric

La respuesta esperada de ambas acciones Fabric es un array de filas en la propiedad \`rows\`.

Agregar:

**Compose_PageRows**

\`\`\`
coalesce(
  body('Fabric_Query_AssetsPage')?['rows'],
  body('Fabric_Query_AssetsPage')?['resultSets']?['Table1']?['rows'],
  body('Fabric_Query_AssetsPage')
)
\`\`\`

Agregar:

**Compose_CountRows**

\`\`\`
coalesce(
  body('Fabric_Query_AssetsCount')?['rows'],
  body('Fabric_Query_AssetsCount')?['resultSets']?['Table1']?['rows'],
  body('Fabric_Query_AssetsCount')
)
\`\`\`

Agregar:

**Compose_TotalRows**

\`\`\`
int(first(outputs('Compose_CountRows'))?['TotalRows'])
\`\`\`

Si el conector Fabric configurado en el entorno devuelve otro envoltorio, conserva la consulta y cambia únicamente las rutas de estas dos acciones después de inspeccionar la primera ejecución. Ese es un gate de conexión, no una razón para volver al cache.

## 7. Proyección para Power Apps

Agregar una acción **Select** llamada \`Select_Assets\`.

**From**

\`\`\`
outputs('Compose_PageRows')
\`\`\`

Mapeo:

| Campo | Expresión |
|---|---|
| ProjectCode | \`item()?['ProjectCode']\` |
| EquipmentId | \`item()?['EquipmentId']\` |
| TagNumber | \`item()?['TagNumber']\` |
| EquipmentType | \`item()?['EquipmentType']\` |
| CommodityCode | \`item()?['CommodityCode']\` |
| EquipmentDescription | \`item()?['EquipmentDescription']\` |
| Discipline | \`item()?['Discipline']\` |
| RevisionNumber | \`item()?['RevisionNumber']\` |
| Quantity | \`item()?['Quantity']\` |
| FamilyResolutionState | \`item()?['FamilyResolutionState']\` |

Agregar:

**Compose_HasNextPage**

\`\`\`
less(
  mul(outputs('Compose_PageNumber'), outputs('Compose_PageSize')),
  outputs('Compose_TotalRows')
)
\`\`\`

## 8. Respuesta al canvas app

Agregar **Respond to a PowerApp or flow** con estos campos:

| Campo | Expresión |
|---|---|
| Items | \`body('Select_Assets')\` |
| TotalRows | \`outputs('Compose_TotalRows')\` |
| PageNumber | \`outputs('Compose_PageNumber')\` |
| PageSize | \`outputs('Compose_PageSize')\` |
| HasNextPage | \`outputs('Compose_HasNextPage')\` |

Si el diseñador exige tipos, configura:

- Items: Text/JSON
- TotalRows: Number
- PageNumber: Number
- PageSize: Number
- HasNextPage: Boolean

La llamada desde Power Apps debe enviar:

\`\`\`
AssetPlan_GetAlepAssetsPaged.Run(
    Value(varProjectCode),
    varAssetPage,
    varAssetPageSize,
    Coalesce(txtAssetSearch.Text, ""),
    Coalesce(ddEquipmentType.Selected.Value, ""),
    Coalesce(ddCommodity.Selected.Value, "")
)
\`\`\`

## 9. Encaje con el árbol de Assets

La pantalla usará un árbol como Technical Fields:

\`Project → Area → System → Equipment Family → Asset\`

El árbol es navegación de la UI. Este flow es la lectura live paginada de assets y acepta el contexto que actualmente está definido: proyecto y filtros de equipment type/commodity. No se debe inventar una jerarquía desde cache ni clasificar unresolved durante la carga.

Mientras el modelo de nodos no esté conectado a columnas live equivalentes, el árbol puede seleccionar el proyecto y aplicar los filtros disponibles; la familia se muestra como estado no enriquecido hasta que el usuario decida en Technical Fields.

## 10. Pruebas mínimas

Ejecutar el flow en este orden.

### Caso A — proyecto 10230

Inputs:

\`\`\`json
{
  "ProjectCode": 10230,
  "PageNumber": 1,
  "PageSize": 50,
  "SearchText": "",
  "EquipmentType": "",
  "CommodityCode": ""
}
\`\`\`

Verificar:

- La acción Fabric consulta \`ops_ing_trd277.FAC_ALEP_EQ_EXPORT_OFFICIAL_CURRENT\`.
- Hay filas si existen assets live para el proyecto.
- \`TotalRows\` coincide con el count.
- \`HasNextPage\` es verdadero solo si hay más de 50 filas.
- No se crea ni actualiza ningún \`EquipmentTypeResolutionCase\`.
- No se usa \`AlepAssetProjectCache\`.

### Caso B — proyecto 10610

Inputs:

\`\`\`json
{
  "ProjectCode": 10610,
  "PageNumber": 1,
  "PageSize": 50,
  "SearchText": "MOTOR",
  "EquipmentType": "",
  "CommodityCode": ""
}
\`\`\`

Verificar:

- El filtro se aplica en Fabric.
- Los resultados conservan el tipo live.
- \`FamilyResolutionState\` sigue siendo \`NOT_ENRICHED\`.
- No se bootstrappea ningún unresolved.

### Caso C — paginación

Usar un proyecto con más de una página:

\`\`\`json
{
  "ProjectCode": 10230,
  "PageNumber": 2,
  "PageSize": 10,
  "SearchText": "",
  "EquipmentType": "",
  "CommodityCode": ""
}
\`\`\`

Verificar que no haya duplicados entre páginas y que el orden sea estable.

## 11. Gates de entrega

| Gate | Estado | Evidencia |
|---|---|---|
| Fuente live decidida | PASS | Fuente y anti-cache fijados arriba |
| Identidad del asset | PASS | ProjectCode + EquipmentId |
| Contrato del flow | PASS | Inputs, filtros y respuesta definidos |
| SQL paginado/count | PASS | Plantillas y sustituciones completas |
| Documentación GitHub | PASS | Este documento |
| Flow creado y guardado | GATED | Requiere acceso al tenant Power Platform |
| Conexión Fabric ejecutada | GATED | Requiere una ejecución real |
| Pruebas 10230/10610 | GATED | Requiere run history del entorno |
| Integración final de pantalla | GATED | Depende de la respuesta real del conector |

## 12. Única intervención necesaria

Para cerrar los gates restantes, debes hacer exactamente esto:

1. Crear o abrir \`AssetPlan_GetAlepAssetsPaged\` en Power Automate.
2. Copiar las acciones y expresiones de este documento.
3. Seleccionar la conexión Fabric ya autorizada y apuntarla al objeto live indicado.
4. Guardar el flow.
5. Ejecutar los tres casos de prueba.
6. Compartir el resultado de estas acciones:
   - \`Fabric_Query_AssetsPage\`
   - \`Fabric_Query_AssetsCount\`
   - \`Compose_PageRows\`
   - respuesta final del flow

Con esas cuatro evidencias se puede cerrar el gate de ejecución y continuar con la pantalla sin volver a experimentar con cache, procedimientos antiguos ni resolución automática.

# AssetPlan — guía ejecutable del flow de Assets ALEP

## Decisión cerrada

Assets consulta en vivo esta fuente Fabric:

`ops_ing_trd277.FAC_ALEP_EQ_EXPORT_OFFICIAL_CURRENT`

No usa el cache `AssetPlan.AlepAssetProjectCache`, la antigua `AssetPlan.usp_AlepAsset_SearchPaged`, ni bootstrap de unresolved o propuestas de Technical Fields.

Los tipos unresolved son assets válidos y deben aparecer. Technical Fields decidirá su familia después desde el modal. Esta primera versión devuelve el dato live y `familyResolutionState = NOT_ENRICHED`; no etiqueta como `RESOLVED` algo que no se haya enriquecido.

## Contrato

Flow: `AssetPlan_GetAlepAssetsPaged`

Trigger: **Power Apps (V2)**

Inputs:

| Nombre | Tipo | Regla |
|---|---|---|
| `ProjectCode` | Number | obligatorio, positivo |
| `PageNumber` | Number | opcional, defecto 1 |
| `PageSize` | Number | opcional, defecto 50, máximo 200 |
| `SearchText` | Text | opcional |
| `EquipmentType` | Text | opcional |
| `CommodityCode` | Text | opcional |

Identidad del asset: `ProjectCode + EquipmentId`. `TagNumber` es display, no identidad.

Respuesta:

```json
{
  "ok": true,
  "status": "READY",
  "projectCode": 10230,
  "pageNumber": 1,
  "pageSize": 50,
  "totalRows": 0,
  "hasNextPage": false,
  "items": []
}
```

## Construcción en Power Automate

### 1. Normalizar inputs

Añade estas acciones Compose, con estos nombres exactos:

- `Compose_ProjectCode`
- `Compose_PageNumber`
- `Compose_PageSize`
- `Compose_SearchText`
- `Compose_EquipmentType`
- `Compose_CommodityCode`

Expresiones:

```text
int(triggerBody()?['ProjectCode'])
```

```text
if(
  or(
    equals(triggerBody()?['PageNumber'], null),
    lessOrEquals(int(triggerBody()?['PageNumber']), 0)
  ),
  1,
  int(triggerBody()?['PageNumber'])
)
```

```text
if(
  or(
    equals(triggerBody()?['PageSize'], null),
    lessOrEquals(int(triggerBody()?['PageSize']), 0)
  ),
  50,
  min(int(triggerBody()?['PageSize']), 200)
)
```

Para los tres textos:

```text
trim(coalesce(triggerBody()?['SearchText'], ''))
```

Cambia el nombre de la entrada para EquipmentType y CommodityCode.

Añade:

`Compose_RowFrom`

```text
add(
  1,
  mul(
    sub(outputs('Compose_PageNumber'), 1),
    outputs('Compose_PageSize')
  )
)
```

`Compose_RowTo`

```text
mul(
  outputs('Compose_PageNumber'),
  outputs('Compose_PageSize')
)
```

### 2. Escapar filtros

Añade tres Compose:

- `Compose_SearchTextSql`
- `Compose_EquipmentTypeSql`
- `Compose_CommodityCodeSql`

Usa:

```text
replace(outputs('Compose_SearchText'), '''', '''''')
```

Repite cambiando el nombre de la acción. Esto escapa comillas simples antes de insertar valores en la consulta.

### 3. Crear la consulta paginada

Añade un Compose llamado `Compose_AssetsPageQuery`.

Su expresión es una concatenación del SQL fijo y los valores normalizados. La consulta que debe generar es esta:

```sql
WITH SourceRows AS
(
    SELECT
        CAST(ID_COD_PROJECT AS INT) AS ProjectCode,
        CAST(ID_EQUIPMENT AS STRING) AS EquipmentId,
        ID_TAG_NUMBER AS TagNumber,
        DS_EQUIPMENT_TYPE AS EquipmentType,
        ID_COMMODITY_CODE AS CommodityCode,
        DS_DESCRIPTION AS EquipmentDescription,
        DS_DISCIPLINE AS Discipline,
        NU_REVISION AS RevisionNumber,
        NU_QUANTITY AS Quantity,
        ROW_NUMBER() OVER (ORDER BY ID_TAG_NUMBER, ID_EQUIPMENT) AS RowNumber
    FROM ops_ing_trd277.FAC_ALEP_EQ_EXPORT_OFFICIAL_CURRENT
    WHERE CAST(ID_COD_PROJECT AS STRING) = '<ProjectCode>'
      AND (
          '<SearchText>' = ''
          OR CAST(ID_TAG_NUMBER AS STRING) LIKE CONCAT('%', '<SearchText>', '%')
          OR DS_EQUIPMENT_TYPE LIKE CONCAT('%', '<SearchText>', '%')
          OR DS_DESCRIPTION LIKE CONCAT('%', '<SearchText>', '%')
          OR CAST(ID_COMMODITY_CODE AS STRING) LIKE CONCAT('%', '<SearchText>', '%')
      )
      AND ('<EquipmentType>' = '' OR DS_EQUIPMENT_TYPE = '<EquipmentType>')
      AND (
          '<CommodityCode>' = ''
          OR CAST(ID_COMMODITY_CODE AS STRING) = '<CommodityCode>'
      )
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
    'NOT_ENRICHED' AS FamilyResolutionState,
    RowNumber
FROM SourceRows
WHERE RowNumber BETWEEN <RowFrom> AND <RowTo>
ORDER BY RowNumber
```

En la expresión `concat()`, cada marcador debe sustituirse por:

| Marcador | Acción |
|---|---|
| `<ProjectCode>` | `outputs('Compose_ProjectCode')` |
| `<SearchText>` | `outputs('Compose_SearchTextSql')` |
| `<EquipmentType>` | `outputs('Compose_EquipmentTypeSql')` |
| `<CommodityCode>` | `outputs('Compose_CommodityCodeSql')` |
| `<RowFrom>` | `outputs('Compose_RowFrom')` |
| `<RowTo>` | `outputs('Compose_RowTo')` |

No pongas las marcas angulares en la consulta final.

### 4. Ejecutar Fabric

Añade la acción Fabric existente que ejecuta consulta SQL/Spark y llámala:

`Fabric_Query_AssetsPage`

En el campo Query usa:

```text
outputs('Compose_AssetsPageQuery')
```

Debe ser una acción del conector Fabric. No uses la acción de Azure SQL.

### 5. Crear y ejecutar el count

Añade `Compose_AssetsCountQuery` con los mismos filtros y esta consulta:

```sql
SELECT COUNT(*) AS TotalRows
FROM ops_ing_trd277.FAC_ALEP_EQ_EXPORT_OFFICIAL_CURRENT
WHERE CAST(ID_COD_PROJECT AS STRING) = '<ProjectCode>'
  AND (
      '<SearchText>' = ''
      OR CAST(ID_TAG_NUMBER AS STRING) LIKE CONCAT('%', '<SearchText>', '%')
      OR DS_EQUIPMENT_TYPE LIKE CONCAT('%', '<SearchText>', '%')
      OR DS_DESCRIPTION LIKE CONCAT('%', '<SearchText>', '%')
      OR CAST(ID_COMMODITY_CODE AS STRING) LIKE CONCAT('%', '<SearchText>', '%')
  )
  AND ('<EquipmentType>' = '' OR DS_EQUIPMENT_TYPE = '<EquipmentType>')
  AND (
      '<CommodityCode>' = ''
      OR CAST(ID_COMMODITY_CODE AS STRING) = '<CommodityCode>'
  )
```

Sustituye los mismos seis marcadores de la tabla anterior. Ejecuta el Compose con la misma acción Fabric y llama la acción:

`Fabric_Query_AssetsCount`

### 6. Preparar respuesta

Extrae el primer valor `TotalRows` de la respuesta count. Si el conector devuelve un wrapper `fields/rows`, usa el primer objeto de `rows`; no envíes el wrapper entero al cliente.

Calcula `hasNextPage` con:

```text
less(
  mul(
    outputs('Compose_PageNumber'),
    outputs('Compose_PageSize')
  ),
  int(outputs('Compose_TotalRows'))
)
```

La respuesta debe conservar únicamente:

- `ok`
- `status`
- `projectCode`
- `pageNumber`
- `pageSize`
- `totalRows`
- `hasNextPage`
- `items`

No uses `Apply to each` para paginar.

## Validación obligatoria

Ejecutar el flow manualmente con:

| Input | Valor |
|---|---|
| ProjectCode | 10230 |
| PageNumber | 1 |
| PageSize | 50 |
| SearchText | vacío |
| EquipmentType | vacío |
| CommodityCode | vacío |

Debe comprobarse:

1. `items` contiene assets de la fuente Fabric.
2. Los tipos `Booster`, `Internal`, `Oil Skimmer` y `Purifier` no desaparecen.
3. La segunda página no repite la primera.
4. `totalRows` coincide con el count usando los mismos filtros.
5. El mismo `EquipmentId` no se duplica dentro del resultado.
6. La respuesta no contiene datos de `AlepAssetProjectCache`.
7. El estado de familia es `NOT_ENRICHED`, no una resolución inventada.

Segundo caso:

| Input | Valor |
|---|---|
| ProjectCode | 10610 |
| PageNumber | 1 |
| PageSize | 50 |
| SearchText | MOTOR |
| EquipmentType | vacío |
| CommodityCode | vacío |

Debe aparecer `MOTOR SPACE HEATER`.

## Gates

- **Gate técnico cerrado:** la arquitectura live, la identidad y el contrato están decididos.
- **Gate de ejecución:** requiere una ejecución real del flow con el conector Fabric configurado. Esta guía no afirma que el runtime esté validado hasta ver esa ejecución.
- **Intervención del usuario:** solo es necesaria para crear/guardar el flow en el entorno Power Automate y ejecutar los dos casos de prueba; no hay que tocar Technical Fields ni cargar unresolved.

---
title: Tipos de datos de tabla en SQL de Synapse
description: Recomendaciones para definir los tipos de datos de tabla en SQL de Synapse.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 1e6cd43e61389596be9b134ab2ad62bbf324a5cd
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122693571"
---
# <a name="table-data-types-in-synapse-sql"></a>Tipos de datos de tabla en SQL de Synapse

En este artículo, encontrará recomendaciones para definir tipos de datos de tabla en un grupo dedicado de Synapse SQL. 

## <a name="data-types"></a>Tipos de datos

El grupo dedicado de Synapse SQL admite los tipos de datos usados más comúnmente. Para obtener una lista de los tipos de datos admitidos, consulte [tipos de datos](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes&preserve-view=true) en la instrucción CREATE TABLE. Para Synapse SQL sin servidor, consulte el artículo [Consulta de archivos de almacenamiento con un grupo de SQL sin servidor en Azure Synapse Analytics](./query-data-storage.md) y [Uso de OPENROWSET con un grupo de SQL sin servidor en Azure Synapse Analytics](./develop-openrowset.md).

## <a name="minimize-row-length"></a>Minimizar la longitud de fila

Minimizar el tamaño de los tipos de datos acorta la longitud de fila, lo que conduce a un mejor rendimiento de las consultas. Utilice el tipo de datos más pequeño que sirva para los datos.

- Evite definir las columnas de caracteres con una longitud predeterminada de gran tamaño. Por ejemplo, si el valor más largo es de 25 caracteres, defina la columna como VARCHAR(25).
- Evite el uso de [NVARCHAR][NVARCHAR] cuando solo necesite VARCHAR.
- Utilice NVARCHAR(4000) o VARCHAR(8000) cuando sea posible en lugar de NVARCHAR(MAX) o VARCHAR(MAX).
- Evite el uso de valores float y decimales con una escala de 0 (cero).  Deben ser TINYINT, SMALLINT, INT o BIGINT.

> [!NOTE]
> Si usa tablas externas de PolyBase para cargar las tablas de Synapse SQL, la longitud definida para la fila de la tabla no puede superar 1 MB. Cuando una fila con datos de longitud variable supera 1 MB, puede cargar la fila con BCP, pero no con PolyBase.

## <a name="identify-unsupported-data-types"></a>Identificar los tipos de datos no admitidos

Si va a migrar la base de datos desde otra base de datos SQL, puede encontrar tipos de datos no admitidos en SQL de Synapse. Utilice esta consulta para detectar tipos de datos no admitidos en el esquema de SQL existente.

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','xml')
 OR  y.[is_user_defined] = 1;
```

## <a name="workarounds-for-unsupported-data-types"></a><a name="unsupported-data-types"></a>Soluciones alternativas para los tipos de datos no admitidos

La lista siguiente muestra los tipos de datos que SQL de Synapse no admite y proporciona alternativas que puede usar en lugar de los tipos de datos no admitidos.

| Tipo de datos no admitido | Solución alternativa |
| --- | --- |
| [geometry](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?view=azure-sqldw-latest&preserve-view=true&preserve-view=true) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| [geography](/sql/t-sql/spatial-geography/spatial-types-geography) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql?view=azure-sqldw-latest&preserve-view=true)(4000) |
| [image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?view=azure-sqldw-latest&preserve-view=true) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| [text](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?view=azure-sqldw-latest&preserve-view=true) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| [ntext](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?view=azure-sqldw-latest&preserve-view=true) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| [sql_variant](/sql/t-sql/data-types/sql-variant-transact-sql?view=azure-sqldw-latest&preserve-view=true) |Divida la columna en varias columnas fuertemente tipadas. |
| [table](/sql/t-sql/data-types/table-transact-sql?view=azure-sqldw-latest&preserve-view=true) |Convierta en tablas temporales o considere la posibilidad de almacenar datos en Storage mediante [CETAS](../sql/develop-tables-cetas.md). |
| [timestamp](/sql/t-sql/data-types/date-and-time-types) |Cambie el código para usar [datetime2](/sql/t-sql/data-types/datetime2-transact-sql?view=azure-sqldw-latest&preserve-view=true) y la función [CURRENT_TIMESTAMP](/sql/t-sql/functions/current-timestamp-transact-sql?view=azure-sqldw-latest&preserve-view=true). Solo se admiten las constantes como valores predeterminados, por lo tanto, current_timestamp no se puede definir como una restricción predeterminada. Si tiene que migrar valores de la versión de fila de una columna de tipo timestamp, use [BINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?view=azure-sqldw-latest&preserve-view=true)(8) o [VARBINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?view=azure-sqldw-latest&preserve-view=true)(8) para valores de versión de fila NOT NULL o NULL. |
| [xml](/sql/t-sql/xml/xml-transact-sql?view=azure-sqldw-latest&preserve-view=true) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| [tipo definido por el usuario](/sql/relational-databases/native-client/features/using-user-defined-types) |Volver a convertir el tipo de datos nativo cuando sea posible. |
| valores predeterminados | Los valores predeterminados solo admiten literales y constantes. |

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre el desarrollo de tablas, vea [Información general sobre tablas](develop-overview.md).

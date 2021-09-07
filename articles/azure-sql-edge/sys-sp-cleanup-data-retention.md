---
title: 'sys.sp_cleanup_data_retention (Transact-SQL): Azure SQL Edge'
description: Obtenga información sobre sys.sp_cleanup_data_retention (Transact-SQL) en Azure SQL Edge
keywords: sys.sp_cleanup_data_retention (Transact-SQL), Azure SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 64d24c089ed109c8bd90d76b832c7a38d2185184
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751347"
---
# <a name="syssp_cleanup_data_retention-transact-sql"></a>sys.sp_cleanup_data_retention (Transact-SQL)

**Se aplica a:** Azure SQL Edge

Realiza la limpieza de registros obsoletos de las tablas que tienen habilitadas las directivas de retención de datos. Para obtener más información, vea [Retención de datos](data-retention-overview.md).

## <a name="syntax"></a>Sintaxis

```syntaxsql
sys.sp_cleanup_data_retention
    { [@schema_name = ] 'schema_name' },
    { [@table_name = ] 'table_name' },
    [ [@rowcount =] rowcount OUTPUT ]

```

## <a name="arguments"></a>Argumentos
`[ @schema_name = ] schema_name` Es el nombre del esquema propietario de la tabla en la que se debe realizar la limpieza. *schema_name* es un parámetro obligatorio de tipo **sysname**.

`[ @table_name = ] 'table_name'` Es el nombre de la tabla en la que se debe realizar la operación de limpieza. *table_name* es un parámetro obligatorio de tipo **sysname**.

## <a name="output-parameter"></a>Parámetro de salida

`[ @rowcount = ] rowcount OUTPUT` rowcount es un parámetro de salida opcional que representa el número de registros que se limpian de la tabla. *rowcount* es de tipo int.

## <a name="permissions"></a>Permisos
 Requiere permisos de db_owner.

## <a name="next-steps"></a>Pasos siguientes
- [Retención de datos y purga automática de datos](data-retention-overview.md)
- [Administración de datos históricos con directivas de retención](data-retention-cleanup.md)
- [Habilitación y deshabilitación de la retención de datos](data-retention-enable-disable.md)

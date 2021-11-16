---
title: Tipos de intercalación de almacenamiento de datos
description: Tipos de intercalación admitidos para un grupo de SQL dedicado (anteriormente SQL DW) en Azure Synapse Analytics.
services: synapse-analytics
author: antvgski
manager: igorstan
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: cc2f5f735f20e54baa22eea7ad4d9fd00a3bc426
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131850116"
---
# <a name="database-collation-support-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Compatibilidad de la intercalación de base de datos para un grupo de SQL dedicado (anteriormente SQL DW) en Azure Synapse Analytics 

La intercalación de bases de datos predeterminada se puede cambiar desde Azure Portal cuando se crea un grupo de SQL dedicado (anteriormente SQL DW). Esta capacidad facilita aún más la creación de una base de datos mediante una de las 3800 intercalaciones de bases de datos compatibles.

Las intercalaciones proporcionan la configuración regional, la página de códigos, el criterio de ordenación y las reglas de distinción de caracteres para los tipos de datos basados en caracteres. Una vez que se han elegido estas opciones, todas las columnas y expresiones que requieren información de intercalación heredan la intercalación elegida de la configuración de base de datos. La herencia predeterminada se puede reemplazar si se indica explícitamente una intercalación diferente para un tipo de datos basado en caracteres.

> [!NOTE]
> En Azure Synapse Analytics, el texto de la consulta (incluidas variables, constantes, etc.) siempre se controla mediante la intercalación a nivel de base de datos y no con la intercalación a nivel de servidor como en otras ofertas de SQL Server.

## <a name="changing-collation"></a>Cambio de la intercalación

Para cambiar la intercalación predeterminada, actualice el campo Intercalación en la experiencia de aprovisionamiento.

Por ejemplo, si quiere cambiar la intercalación predeterminada para que distinga mayúsculas de minúsculas, basta con que cambie el nombre de la intercalación de SQL_Latin1_General_CP1_CI_AS a SQL_Latin1_General_CP1_CS_AS.

## <a name="list-of-unsupported-collation-types"></a>Lista de los tipos de intercalación que no se admiten

* Japanese_Bushu_Kakusu_140_BIN
* Japanese_Bushu_Kakusu_140_BIN2
* Japanese_Bushu_Kakusu_140_CI_AI_VSS
* Japanese_Bushu_Kakusu_140_CI_AI_WS_VSS
* Japanese_Bushu_Kakusu_140_CI_AI_KS_VSS
* Japanese_Bushu_Kakusu_140_CI_AI_KS_WS_VSS
* Japanese_Bushu_Kakusu_140_CI_AS_VSS
* Japanese_Bushu_Kakusu_140_CI_AS_WS_VSS
* Japanese_Bushu_Kakusu_140_CI_AS_KS_VSS
* Japanese_Bushu_Kakusu_140_CI_AS_KS_WS_VSS
* Japanese_Bushu_Kakusu_140_CS_AI_VSS
* Japanese_Bushu_Kakusu_140_CS_AI_WS_VSS
* Japanese_Bushu_Kakusu_140_CS_AI_KS_VSS
* Japanese_Bushu_Kakusu_140_CS_AI_KS_WS_VSS
* Japanese_Bushu_Kakusu_140_CS_AS_VSS
* Japanese_Bushu_Kakusu_140_CS_AS_WS_VSS
* Japanese_Bushu_Kakusu_140_CS_AS_KS_VSS
* Japanese_Bushu_Kakusu_140_CS_AS_KS_WS_VSS
* Japanese_Bushu_Kakusu_140_CI_AI
* Japanese_Bushu_Kakusu_140_CI_AI_WS
* Japanese_Bushu_Kakusu_140_CI_AI_KS
* Japanese_Bushu_Kakusu_140_CI_AI_KS_WS
* Japanese_Bushu_Kakusu_140_CI_AS
* Japanese_Bushu_Kakusu_140_CI_AS_WS
* Japanese_Bushu_Kakusu_140_CI_AS_KS
* Japanese_Bushu_Kakusu_140_CI_AS_KS_WS
* Japanese_Bushu_Kakusu_140_CS_AI
* Japanese_Bushu_Kakusu_140_CS_AI_WS
* Japanese_Bushu_Kakusu_140_CS_AI_KS
* Japanese_Bushu_Kakusu_140_CS_AI_KS_WS
* Japanese_Bushu_Kakusu_140_CS_AS
* Japanese_Bushu_Kakusu_140_CS_AS_WS
* Japanese_Bushu_Kakusu_140_CS_AS_KS
* Japanese_Bushu_Kakusu_140_CS_AS_KS_WS
* Japanese_XJIS_140_BIN
* Japanese_XJIS_140_BIN2
* Japanese_XJIS_140_CI_AI_VSS
* Japanese_XJIS_140_CI_AI_WS_VSS
* Japanese_XJIS_140_CI_AI_KS_VSS
* Japanese_XJIS_140_CI_AI_KS_WS_VSS
* Japanese_XJIS_140_CI_AS_VSS
* Japanese_XJIS_140_CI_AS_WS_VSS
* Japanese_XJIS_140_CI_AS_KS_VSS
* Japanese_XJIS_140_CI_AS_KS_WS_VSS
* Japanese_XJIS_140_CS_AI_VSS
* Japanese_XJIS_140_CS_AI_WS_VSS
* Japanese_XJIS_140_CS_AI_KS_VSS
* Japanese_XJIS_140_CS_AI_KS_WS_VSS
* Japanese_XJIS_140_CS_AS_VSS
* Japanese_XJIS_140_CS_AS_WS_VSS
* Japanese_XJIS_140_CS_AS_KS_VSS
* Japanese_XJIS_140_CS_AS_KS_WS_VSS
* Japanese_XJIS_140_CI_AI
* Japanese_XJIS_140_CI_AI_WS
* Japanese_XJIS_140_CI_AI_KS
* Japanese_XJIS_140_CI_AI_KS_WS
* Japanese_XJIS_140_CI_AS
* Japanese_XJIS_140_CI_AS_WS
* Japanese_XJIS_140_CI_AS_KS
* Japanese_XJIS_140_CI_AS_KS_WS
* Japanese_XJIS_140_CS_AI
* Japanese_XJIS_140_CS_AI_WS
* Japanese_XJIS_140_CS_AI_KS
* Japanese_XJIS_140_CS_AI_KS_WS
* Japanese_XJIS_140_CS_AS
* Japanese_XJIS_140_CS_AS_WS
* Japanese_XJIS_140_CS_AS_KS
* Japanese_XJIS_140_CS_AS_KS_WS
* SQL_EBCDIC1141_CP1_CS_AS
* SQL_EBCDIC277_2_CP1_CS_AS

## <a name="checking-the-current-collation"></a>Comprobación de la intercalación actual

Para comprobar la intercalación actual de la base de datos, puede ejecutar el siguiente fragmento de código de T-SQL:

```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Collation') AS Collation;
```

Cuando se pasa "Collation" como parámetro de propiedad, la función DatabasePropertyEx devuelve la intercalación actual de la base de datos especificada. Para obtener más información, vea [DatabasePropertyEx](/sql/t-sql/functions/databasepropertyex-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

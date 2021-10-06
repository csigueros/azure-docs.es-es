---
title: ST_WITHIN en el lenguaje de consulta de Azure Cosmos DB
description: Obtenga información acerca de la función del sistema de SQL ST_WITHIN en Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/21/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 7f3aea14b4da97624abb8515c560281afbdefe18
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128625266"
---
# <a name="st_within-azure-cosmos-db"></a>ST_WITHIN (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 Devuelve una expresión condicional que indica si el objeto de GeoJSON (Point, Polygon, MultiPolygon o LineString) especificado en el primer argumento se encuentra en el objeto de GeoJSON (Point, Polygon, MultiPolygon o LineString) del segundo.  
  
## <a name="syntax"></a>Sintaxis
  
```sql
ST_WITHIN (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*spatial_expr*  
   Es cualquier expresión de objeto de tipo Point, Polygon o LineString de GeoJSON.  
  
## <a name="return-types"></a>Tipos de valores devueltos
  
  Devuelve un valor booleano.  
  
## <a name="examples"></a>Ejemplos
  
  En el ejemplo siguiente se muestra cómo buscar todos los documentos de la familia en Polygon con `ST_WITHIN`.  
  
```sql
SELECT f.id
FROM Families f
WHERE ST_WITHIN(f.location, {  
    'type':'Polygon',
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 El conjunto de resultados es el siguiente:  
  
```json
[{ "id": "WakefieldFamily" }]  
```  

## <a name="remarks"></a>Observaciones

Esta función del sistema se beneficiará de un [índice geoespacial](../index-policy.md#spatial-indexes), excepto en las consultas con agregados.

> [!NOTE]
> La especificación GeoJSON requiere que los puntos dentro de un polígono se especifiquen en orden en sentido contrario a las agujas del reloj. Un elemento Polygon cuyos puntos se hayan especificado en el sentido de las agujas del reloj representa el inverso de la región dentro de él.


## <a name="next-steps"></a>Pasos siguientes

- [Funciones espaciales (Azure Cosmos DB)](sql-query-spatial-functions.md)
- [Funciones del sistema (Azure Cosmos DB)](sql-query-system-functions.md)
- [Introducción a Azure Cosmos DB](../introduction.md)

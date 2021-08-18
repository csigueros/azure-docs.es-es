---
title: ACOS en el lenguaje de consulta de Azure Cosmos DB
description: Obtenga información sobre cómo la función del sistema SQL ACOS (arccosice) en Azure Cosmos DB devuelve el ángulo, en radianes, cuyo coseno es la expresión numérica especificada.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: af76b7cb36deb4a58d2666497a957ff14ea90b03
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206742"
---
# <a name="acos-azure-cosmos-db"></a>ACOS (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 Devuelve el ángulo, en radianes, cuyo coseno es la expresión numérica especificada; también se denomina arcocoseno.  
  
## <a name="syntax"></a>Sintaxis
  
```sql
ACOS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*numeric_expr*  
   Es una expresión numérica.  
  
## <a name="return-types"></a>Tipos de valores devueltos
  
  Devuelve una expresión numérica.  
  
## <a name="examples"></a>Ejemplos
  
  El siguiente ejemplo devuelve el `ACOS` de -1.  
  
```sql
SELECT ACOS(-1) AS acos 
```  
  
 El conjunto de resultados es el siguiente:  
  
```json
[{"acos": 3.1415926535897931}]  
```  

## <a name="remarks"></a>Observaciones

Esta función del sistema no usará el índice.

## <a name="next-steps"></a>Pasos siguientes

- [Funciones matemáticas (Azure Cosmos DB)](sql-query-mathematical-functions.md)
- [Funciones del sistema (Azure Cosmos DB)](sql-query-system-functions.md)
- [Introducción a Azure Cosmos DB](../introduction.md)

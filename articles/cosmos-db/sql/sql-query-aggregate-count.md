---
title: COUNT en el lenguaje de consulta de Azure Cosmos DB
description: Aprenda sobre la función del sistema de SQL Count (COUNT) en Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: edc3c824bc9ac08e325259c0b5f736867f877062
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206674"
---
# <a name="count-azure-cosmos-db"></a>COUNT (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Esta función del sistema devuelve el recuento de los valores de la expresión.
  
## <a name="syntax"></a>Sintaxis
  
```sql
COUNT(<scalar_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*scalar_expr*  
   Es cualquier expresión escalar.
  
## <a name="return-types"></a>Tipos de valores devueltos
  
Devuelve una expresión numérica.  
  
## <a name="examples"></a>Ejemplos
  
En el ejemplo siguiente se devuelve el recuento total de elementos de un contenedor:
  
```sql
SELECT COUNT(1)
FROM c
``` 
COUNT puede tener cualquier expresión escalar como entrada. La siguiente consulta generará resultados equivalentes:

```sql
SELECT COUNT(2)
FROM c
```

## <a name="remarks"></a>Comentarios

Esta función del sistema se beneficiará de un [índice de intervalo](../index-policy.md#includeexclude-strategy) para cualquier propiedad del filtro de la consulta.

## <a name="next-steps"></a>Pasos siguientes

- [Funciones matemáticas en Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funciones del sistema en Azure Cosmos DB](sql-query-system-functions.md)
- [Funciones de agregado en Azure Cosmos DB](sql-query-aggregate-functions.md)
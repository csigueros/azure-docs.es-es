---
title: StringToBoolean en el lenguaje de consulta de Azure Cosmos DB
description: Obtenga información sobre la función del sistema SQL StringToBoolean en Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d221fb9a738d1b508b08e605ffa3c5d1c927317a
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206695"
---
# <a name="stringtoboolean-azure-cosmos-db"></a>StringToBoolean (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 Devuelve la expresión traducida a un valor booleano. Si no se puede traducir la expresión, devuelve undefined.  
  
## <a name="syntax"></a>Sintaxis
  
```sql
StringToBoolean(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   Es una expresión de cadena que se va a analizar como una expresión booleana.  
  
## <a name="return-types"></a>Tipos de valores devueltos
  
  Devuelve una expresión booleana o undefined.  
  
## <a name="examples"></a>Ejemplos
  
  En el ejemplo siguiente se muestra cómo se comporta `StringToBoolean` en tipos diferentes. 
 
 Los siguientes son ejemplos con entradas válidas.

Se admiten espacios en blanco únicamente antes o después de "true" o "false".

```sql
SELECT 
    StringToBoolean("true") AS b1, 
    StringToBoolean("    false") AS b2,
    StringToBoolean("false    ") AS b3
```  
  
 El conjunto de resultados es el siguiente:  
  
```json
[{"b1": true, "b2": false, "b3": false}]
```  

Los siguientes son ejemplos con una entrada no válida.

 Los valores booleanos distinguen mayúsculas de minúsculas y deben escribirse con caracteres todos en minúsculas; es decir, "true" y "false".

```sql
SELECT 
    StringToBoolean("TRUE"),
    StringToBoolean("False")
```  

El conjunto de resultados es el siguiente:  
  
```json
[{}]
``` 

La expresión pasada se analizará como una expresión booleana; estas entradas no se evalúan para el tipo booleano y, por tanto, se devuelven undefined.

```sql
SELECT 
    StringToBoolean("null"),
    StringToBoolean(undefined),
    StringToBoolean(NaN), 
    StringToBoolean(false), 
    StringToBoolean(true)
```  

El conjunto de resultados es el siguiente:  
  
```json
[{}]
```  

## <a name="remarks"></a>Observaciones

Esta función del sistema no usará el índice.

## <a name="next-steps"></a>Pasos siguientes

- [Funciones de cadena Azure Cosmos DB](sql-query-string-functions.md)
- [Funciones del sistema (Azure Cosmos DB)](sql-query-system-functions.md)
- [Introducción a Azure Cosmos DB](../introduction.md)

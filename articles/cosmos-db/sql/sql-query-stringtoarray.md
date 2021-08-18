---
title: StringToArray en el lenguaje de consulta de Azure Cosmos DB
description: Obtenga información sobre la función del sistema SQL StringToArray en Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 19a65352cc7b14d4f4f9b0753c0c0270ff0bafb0
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206722"
---
# <a name="stringtoarray-azure-cosmos-db"></a>StringToArray (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 Devuelve la expresión traducida a una matriz. Si no se puede traducir la expresión, devuelve undefined.  
  
## <a name="syntax"></a>Sintaxis
  
```sql  
StringToArray(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   Es una expresión de cadena que se va a analizar como una expresión de matriz JSON. 
  
## <a name="return-types"></a>Tipos de valores devueltos
  
  Devuelve una expresión de matriz o undefined. 
  
## <a name="remarks"></a>Observaciones
  Los valores de cadena anidados deben escribirse entre comillas dobles para ser un JSON válido. Para más información sobre el formato JSON, consulte [json.org](https://json.org/). Esta función del sistema no usará el índice.
  
## <a name="examples"></a>Ejemplos
  
  En el ejemplo siguiente se muestra cómo se comporta `StringToArray` en tipos diferentes. 
  
 Los siguientes son ejemplos con entradas válidas.

```sql
SELECT 
    StringToArray('[]') AS a1, 
    StringToArray("[1,2,3]") AS a2,
    StringToArray("[\"str\",2,3]") AS a3,
    StringToArray('[["5","6","7"],["8"],["9"]]') AS a4,
    StringToArray('[1,2,3, "[4,5,6]",[7,8]]') AS a5
```

El conjunto de resultados es el siguiente:

```json
[{"a1": [], "a2": [1,2,3], "a3": ["str",2,3], "a4": [["5","6","7"],["8"],["9"]], "a5": [1,2,3,"[4,5,6]",[7,8]]}]
```

El siguiente es un ejemplo de entrada no válida. 
   
 Las comillas simples dentro de la matriz no son válidas en JSON.
Aunque son válidas dentro de una consulta, no se analizarán en matrices válidas. Las cadenas dentro de la cadena de matriz deben escaparse "[\\"\\"]" o la comilla que la rodea debe ser simple '[""]'.

```sql
SELECT
    StringToArray("['5','6','7']")
```

El conjunto de resultados es el siguiente:

```json
[{}]
```

Los siguientes son ejemplos de entrada no válida.
   
 La expresión pasada se analizará como una matriz JSON; a continuación no se evalúa para el tipo de matriz y, por tanto, se devuelven undefined.
   
```sql
SELECT
    StringToArray("["),
    StringToArray("1"),
    StringToArray(NaN),
    StringToArray(false),
    StringToArray(undefined)
```

El conjunto de resultados es el siguiente:

```json
[{}]
```

## <a name="next-steps"></a>Pasos siguientes

- [Funciones de cadena Azure Cosmos DB](sql-query-string-functions.md)
- [Funciones del sistema (Azure Cosmos DB)](sql-query-system-functions.md)
- [Introducción a Azure Cosmos DB](../introduction.md)

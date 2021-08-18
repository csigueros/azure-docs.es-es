---
title: IS_DEFINED en el lenguaje de consulta de Azure Cosmos DB
description: Obtenga información acerca de la función del sistema de SQL IS_DEFINED en Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 4b61458dc1cf6ad189e6a19a92703d932a2ecea2
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206752"
---
# <a name="is_defined-azure-cosmos-db"></a>IS_DEFINED (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 Devuelve un valor booleano que indica si se ha asignado un valor a la propiedad.  
  
## <a name="syntax"></a>Sintaxis
  
```sql
IS_DEFINED(<expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*expr*  
   Es cualquier expresión.  
  
## <a name="return-types"></a>Tipos de valores devueltos
  
  Devuelve una expresión condicional.  
  
## <a name="examples"></a>Ejemplos
  
  En el ejemplo siguiente se comprueba la presencia de una propiedad en el documento JSON especificado. En el primer caso se devuelve true, ya que "a" está presente, pero en el segundo, false, ya que "b" no lo está.  
  
```sql
SELECT IS_DEFINED({ "a" : 5 }.a) AS isDefined1, IS_DEFINED({ "a" : 5 }.b) AS isDefined2 
```  
  
 El conjunto de resultados es el siguiente:  
  
```json
[{"isDefined1":true,"isDefined2":false}]  
```  

## <a name="remarks"></a>Observaciones

Esta función del sistema se beneficiará de un [índice de intervalo](../index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Pasos siguientes

- [Funciones de comprobación de tipos de Azure Cosmos DB](sql-query-type-checking-functions.md)
- [Funciones del sistema (Azure Cosmos DB)](sql-query-system-functions.md)
- [Introducción a Azure Cosmos DB](../introduction.md)

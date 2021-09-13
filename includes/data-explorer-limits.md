---
author: ilayrn
ms.service: data-explorer
ms.topic: include
ms.date: 01/20/2020
ms.author: ilayr
ms.openlocfilehash: 86b2353109d4005594ee08e27283d6aa9c654120
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/26/2021
ms.locfileid: "123078509"
---
En la tabla siguiente se describen los límites máximos de los clústeres de Azure Data Explorer.

| Recurso | Límite |
| --- | --- |
| Clústeres por región por suscripción | 20 |
| Instancias por clúster | 1000 | 
| Número de bases de datos en un clúster | 10 000 |
| Número de clústeres seguidores (consumidores de recursos compartidos de datos) por clúster líder (productor de recursos compartidos de datos) | 100 |

En la tabla siguiente se describen los límites de las operaciones de administración realizadas en los clústeres de Azure Data Explorer.

| Ámbito | Operación | Límite |
| --- | --- | --- |
| Clúster | lectura (por ejemplo, obtener un clúster) | 500 por cada 5 minutos |
| Clúster | escritura (por ejemplo, crear una base de datos) | 1000 por hora |


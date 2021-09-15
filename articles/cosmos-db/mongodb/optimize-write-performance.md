---
title: Optimización del rendimiento de escritura en la API de Azure Cosmos DB para MongoDB
description: En este artículo se describe cómo optimizar el rendimiento de escritura en la API de Azure Cosmos DB para MongoDB para obtener el máximo rendimiento posible para el costo más bajo.
author: gahl-levy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 08/26/2021
ms.author: gahllevy
ms.openlocfilehash: 2e04953e766c76275079731751cb006fe46712e7
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123039558"
---
# <a name="optimize-write-performance-in-azure-cosmos-db-api-for-mongodb"></a>Optimización del rendimiento de escritura en la API de Azure Cosmos DB para MongoDB
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

La optimización del rendimiento de escritura le ayuda a sacar el máximo partido de la API de Azure Cosmos DB para la escala ilimitada de MongoDB. A diferencia de otros servicios administrados de MongoDB, la API para MongoDB particiona automáticamente y de forma transparente las colecciones (cuando se usan colecciones particionadas) para escalar de forma infinita. 

La manera de escribir datos debe ser consciente de esto mediante la paralelización y la propagación de datos entre particiones para obtener el máximo número de escrituras de las bases de datos y colecciones. En este artículo se explican los procedimientos recomendados para optimizar el rendimiento de escritura.

## <a name="spread-the-load-across-your-shards"></a>Propagación de la carga entre las particiones
Al escribir datos en una API particionada para la colección de MongoDB, los datos se dividen (particionan) en segmentos pequeños y se escriben en cada partición en función del valor del campo de la clave de partición. Puede pensar en cada segmento como una pequeña parte de una máquina virtual que solo almacena los documentos que contienen un valor de clave de partición único. 

Si la aplicación escribe una gran cantidad de datos en una sola partición, esto no será eficaz porque la aplicación estaría maximizando el rendimiento de una sola partición en lugar de distribuir la carga entre todas las particiones. La carga de escritura se repartirá uniformemente entre la colección escribiendo en paralelo en muchos documentos con valores de clave de partición únicos.

Un ejemplo de ello sería una aplicación de catálogo de productos particionada en el campo de categoría. En lugar de escribir en una categoría (partición) a la vez, es mejor escribir en todas las categorías simultáneamente para lograr el máximo rendimiento de escritura. 

## <a name="reduce-the-number-of-indexes"></a>Reducción del número de índices
La [indexación](../mongodb-indexing.md) es una excelente característica para reducir drásticamente el tiempo que se tarda en consultar los datos. Para obtener la experiencia de consulta más flexible, la API para MongoDB habilita un índice de caracteres comodín en los datos de forma predeterminada para realizar consultas en todos los campos de forma muy rápida. Sin embargo, todos los índices, que incluyen índices con caracteres comodín, introducen carga adicional al escribir datos porque las escrituras cambian la colección y los índices. 

Reducir el número de índices solo a los índices que necesita para admitir las consultas permitirá que las escrituras sean más rápidas y más baratas. Como norma general, se recomienda lo siguiente:

* Cualquier campo por el que filtre debe tener un índice de campo único correspondiente. Esta opción también habilita el filtrado de varios campos.
* Cualquier grupo de campos que ordene debe tener un índice compuesto para ese grupo. 

## <a name="set-ordered-to-false-in-the-mongodb-drivers"></a>Definición del orden en false en los controladores de MongoDB
De forma predeterminada, los controladores de MongoDB establecen la opción de ordenación en "true" al escribir datos, de tal forma que se escribe en cada documento en orden uno por uno. Esta opción reduce el rendimiento de escritura, ya que cada solicitud de escritura tiene que esperar a que se complete la anterior. Al escribir datos, establezca esta opción en false para mejorar el rendimiento. 

```JavaScript
db.collection.insertMany(
   [ <doc1> , <doc2>, ... ],
   {
      ordered: false
   }
)
```

## <a name="tune-for-the-optimal-batch-size-and-thread-count"></a>Ajuste del tamaño de lote óptimo y del número de subprocesos
La paralelización de las operaciones de escritura en muchos subprocesos o procesos es clave para escalar las escrituras. La API para MongoDB acepta escrituras en lotes de hasta 1000 documentos para cada proceso o subproceso. 

Si escribe más de 1000 documentos a la vez por proceso o subproceso, las funciones de cliente como `insertMany()` deben limitarse a aproximadamente 1000 documentos. De lo contrario, el cliente esperará a que cada lote se confirme antes de pasar al siguiente lote. En algunos casos, dividir los lotes con menos o ligeramente más de 1000 documentos será más rápido.



## <a name="next-steps"></a>Pasos siguientes

* Más información sobre la [indexación en la API para MongoDB](../mongodb-indexing.md)
* Más información sobre la [creación de particiones en Azure Cosmos DB](../partitioning-overview.md)
* Más información sobre la [solución de problemas comunes](error-codes-solutions.md)
* ¿Intenta planear la capacidad de una migración a Azure Cosmos DB? Puede usar la información sobre el clúster de bases de datos existente para el planeamiento de capacidad.
    * Si lo único que sabe es el número de núcleos virtuales y servidores del clúster de bases de datos existente, lea sobre el [cálculo de unidades de solicitud mediante núcleos o CPU virtuales](../convert-vcore-to-request-unit.md). 
    * Si conoce las tasas de solicitudes típicas de la carga de trabajo de la base de datos actual, obtenga información sobre el [cálculo de unidades de solicitud mediante la herramienta de planeamiento de capacidad de Azure Cosmos DB](estimate-ru-capacity-planner.md).

---
title: Asignación de niveles de coherencia para la API para MongoDB de Azure Cosmos DB
description: Asignación de niveles de coherencia para la API para MongoDB de Azure Cosmos DB.
author: gahl-levy
ms.author: gahllevy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: fa434fc45b46353d04a22000f9f2f252898cb715
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779934"
---
# <a name="consistency-levels-for-azure-cosmos-db-and-the-api-for-mongodb"></a>Niveles de coherencia para Azure Cosmos DB y la API para MongoDB
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

A diferencia de Azure Cosmos DB, el nivel de coherencia nativo de MongoDB no proporciona garantías de coherencia definidas con precisión. En su lugar, MongoDB nativo permite a los usuarios configurar las siguientes garantías de coherencia (una preocupación de escritura, una preocupación de lectura y la directiva isMaster) para dirigir las operaciones de lectura a las réplicas principales o secundarias y lograr el nivel de coherencia deseado.

Cuando se usa la API de Azure Cosmos DB para MongoDB, el controlador de MongoDB trata la región de escritura como la réplica principal y todas las demás regiones son réplicas de lectura. Puede elegir qué región asociada con su cuenta de Azure Cosmos como una réplica principal.

> [!NOTE]
> El modelo de coherencia predeterminado de Azure Cosmos DB es Session. Session es un modelo de coherencia centrado en el cliente que no es compatible de forma nativa ni con Cassandra ni con MongoDB. Para más información sobre el modelo de coherencia que se debe elegir, consulte [Niveles de coherencia en Azure Cosmos DB](../consistency-levels.md).

Al usar la API de Azure Cosmos DB para MongoDB:

* la preocupación de escritura se asigna al nivel de coherencia predeterminado configurado en la cuenta de Azure Cosmos.

* Azure Cosmos DB asignará dinámicamente la preocupación de lectura que el controlador de cliente de MongoDB especifique en uno de los niveles de coherencia de Azure Cosmos DB configurados dinámicamente en una solicitud de lectura.  

* Puede anotar una región concreta asociada a la cuenta de Azure Cosmos como "Principal" si la establece como la primera región grabable. 

## <a name="mapping-consistency-levels"></a>Asignación de niveles de coherencia

En la tabla siguiente se muestra cómo las preocupaciones de lectura o escritura nativas de MongoDB se asignan a los niveles de coherencia de Azure Cosmos cuando se usa la API de Azure Cosmos DB para MongoDB:

:::image type="content" source="../media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png" alt-text="Asignación de modelos de coherencia de MongoDB" lightbox= "../media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png":::

Si la cuenta de Azure Cosmos está configurada con un nivel de coherencia que no es el de coherencia alta, puede averiguar la probabilidad de que los clientes obtengan lecturas de coherencia alta para sus cargas de trabajo consultando la métrica de *obsolescencia limitada de probabilidad* (PBS). Esta métrica se expone en Azure Portal; para obtener más información, consulte [Supervisión de la métrica de obsolescencia limitada de manera probabilística (PBS)](../how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

La obsolescencia limitada de probabilidad muestra cómo de posible es la coherencia final. Esta métrica proporciona una visión general de la frecuencia con la que puede obtener una coherencia mayor que el nivel de coherencia que tiene configurado actualmente en su cuenta de Azure Cosmos. En otras palabras, puede ver la probabilidad (en milisegundos) de obtener lecturas con coherencia alta para una combinación de regiones de escritura y lectura.

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre la distribución global y los niveles de coherencia en Azure Cosmos DB:

* [Introducción a la distribución global](../distribute-data-globally.md)
* [Información general sobre el nivel de coherencia](../consistency-levels.md)
* [Alta disponibilidad](../high-availability.md)

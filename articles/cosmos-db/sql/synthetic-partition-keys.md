---
title: Crear una clave de partición sintética en Azure Cosmos DB
description: Aprenda a usar las claves de partición sintética en los contenedores de Azure Cosmos para distribuir los datos y la carga de trabajo de manera uniforme a través de las claves de partición.
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/26/2021
author: markjbrown
ms.author: mjbrown
ms.openlocfilehash: 169294ffa113ef02c42b0fbc7d1ff8e8381b9eff
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123114215"
---
# <a name="create-a-synthetic-partition-key"></a>Creación de una clave de partición sintética
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

El procedimiento recomendado es tener una clave de partición con muchos valores distintos, por ejemplo, centenares o miles. El objetivo es distribuir los datos y la carga de trabajo uniformemente entre los elementos asociados con estos valores de clave de partición. Si esta propiedad no existe en los datos, se puede crear una *clave de partición sintética*. En este documento se describen varias técnicas básicas para generar una clave de partición sintética para el contenedor de Cosmos.

## <a name="concatenate-multiple-properties-of-an-item"></a>Concatenación de varias propiedades de un elemento

Puede formar una clave de partición mediante la concatenación de varios valores de propiedades en una única propiedad artificial `partitionKey`. Estas claves se conocen como claves sintéticas. Por ejemplo, considere el siguiente documento de ejemplo:

```JavaScript
{
"deviceId": "abc-123",
"date": 2018
}
```

Para el documento anterior, una opción es establecer /deviceId o /date como clave de partición. Use esta opción si desea crear particiones del contenedor basadas en el id. de dispositivo o en la fecha. Otra opción es concatenar estos dos valores en una propiedad `partitionKey` sintética que se usa como clave de partición.

```JavaScript
{
"deviceId": "abc-123",
"date": 2018,
"partitionKey": "abc-123-2018"
}
```

En escenarios en tiempo real, puede tener miles de elementos en una base de datos. En lugar de agregar manualmente la clave sintética, defina la lógica en el lado cliente para concatenar los valores e inserte la clave sintética en los elementos de los contenedores de Cosmos.

## <a name="use-a-partition-key-with-a-random-suffix"></a>Uso de una clave de partición con un sufijo aleatorio

Otra posible estrategia para distribuir más uniformemente la carga de trabajo es anexar un número aleatorio al final del valor de la clave de partición. La distribución de los elementos de esta forma permite realizar operaciones de escritura en paralelo entre las particiones.

Por ejemplo, si una clave de partición representa una fecha, puede elegir un número aleatorio entre 1 y 400, y concatenarlo como sufijo a la fecha. Este método da como resultado valores de clave de partición como  `2018-08-09.1`, `2018-08-09.2`, etc., hasta  `2018-08-09.400`. Dado que la clave de partición se genera aleatoriamente, las operaciones de escritura del contenedor cada día se reparten uniformemente entre varias particiones. Este método mejora el paralelismo y el rendimiento general.

## <a name="use-a-partition-key-with-pre-calculated-suffixes"></a>Uso de una clave de partición con sufijos calculados previamente 

La estrategia de sufijos aleatorios puede mejorar considerablemente el rendimiento de escritura, pero resulta difícil leer un elemento específico. No conoce el valor de sufijo que se usó al escribir el elemento. Para que sea más fácil leer los elementos individuales, use la estrategia de sufijos calculados previamente. En lugar de usar un número aleatorio para distribuir los elementos entre las particiones, use un número que se calcula en función de algo que desea consultar.

Considere el ejemplo anterior, donde un contenedor utiliza una fecha como clave de partición. Ahora suponga que cada elemento tiene un atributo  `Vehicle-Identification-Number` (`VIN`) al que se quiere acceder. Supongamos además que a menudo se ejecutan consultas para buscar elementos por `VIN`, además de por fecha. Antes de que la aplicación escriba el elemento en el contenedor, puede calcular un sufijo hash según el número de bastidor y anexarlo a la fecha de la clave de partición. El cálculo puede generar un número entre 1 y 400 que se distribuye uniformemente. Este resultado es similar a los resultados producidos por el método de sufijos aleatorios. El valor de la clave de partición será entonces la fecha concatenada con el resultado calculado.

Con esta estrategia, las escrituras se distribuyen uniformemente entre los valores de clave de partición y entre todas las particiones. Puede leer fácilmente un elemento y una fecha concretos, porque puede calcular el valor de la clave de partición para un `Vehicle-Identification-Number` específico. La ventaja de este método es que puede evitar la creación de una única clave de partición frecuente, por ejemplo, una clave de partición que soporta toda la carga de trabajo. 

## <a name="next-steps"></a>Pasos siguientes

Puede obtener más información sobre el concepto de la creación de particiones en los siguientes artículos:

* Más información sobre las [particiones lógicas](../partitioning-overview.md).
* Obtenga más información sobre el [aprovisionamiento del rendimiento de contenedores y bases de datos de Azure Cosmos](../set-throughput.md).
* Obtenga más información sobre el [aprovisionamiento del rendimiento de un contenedor de Azure Cosmos](how-to-provision-container-throughput.md).
* Obtenga más información sobre el [aprovisionamiento del rendimiento de una base de datos de Azure Cosmos](how-to-provision-database-throughput.md).
* ¿Intenta planear la capacidad de una migración a Azure Cosmos DB? Puede usar información sobre el clúster de bases de datos existente para planear la capacidad.
    * Si lo único que sabe es el número de núcleos virtuales y servidores del clúster de bases de datos existente, lea sobre el [cálculo de unidades de solicitud mediante núcleos o CPU virtuales](../convert-vcore-to-request-unit.md). 
    * Si conoce las tasas de solicitudes típicas de la carga de trabajo de la base de datos actual, obtenga información sobre el [cálculo de unidades de solicitud mediante la herramienta de planeamiento de capacidad de Azure Cosmos DB](estimate-ru-with-capacity-planner.md).

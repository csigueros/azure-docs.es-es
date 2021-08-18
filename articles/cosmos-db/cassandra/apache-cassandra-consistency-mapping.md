---
title: Niveles de coherencia de Apache Cassandra y Azure Cosmos DB
description: Niveles de coherencia de Apache Cassandra y Azure Cosmos DB.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: 8159fa7574830a6754a409245f7fca4590a0c5c3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780418"
---
# <a name="apache-cassandra-and-azure-cosmos-db-cassandra-api-consistency-levels"></a>Niveles de coherencia de Apache Cassandra API y Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

A diferencia de Azure Cosmos DB, Apache Cassandra no proporciona de forma nativa garantías de coherencia definidas con precisión. En su lugar, Cassandra Apache proporciona un nivel de coherencia de escritura y de lectura para permitir los inconvenientes que representan la alta disponibilidad, coherencia, disponibilidad y latencia. Al usar Cassandra API de Azure Cosmos DB:

* El nivel de coherencia de escritura de Apache Cassandra se asigna al nivel de coherencia predeterminado configurado en la cuenta de Azure Cosmos. No se puede cambiar la coherencia de una operación de escritura (CL) en cada solicitud.

* Azure Cosmos DB asignará dinámicamente el nivel de coherencia de lectura que el controlador de cliente de Cassandra especifique en uno de los niveles de coherencia de Azure Cosmos DB configurados dinámicamente en una solicitud de lectura.

## <a name="multi-region-writes-vs-single-region-writes"></a>Escrituras en varias regiones frente a escrituras de región única

La base de datos de Apache Cassandra es un sistema que presenta de manera predeterminada una arquitectura multimaestro y que no proporciona una opción integrada para escrituras de región única con replicación en varias regiones para las lecturas. Sin embargo, Azure Cosmos DB proporciona la capacidad inmediata de disponer de configuraciones de escritura para una única región o para [varias regiones](../how-to-multi-master.md). Una de las ventajas de poder elegir una configuración de escritura de región única en varias regiones es la prevención de escenarios de conflictos entre regiones y la opción de mantener una coherencia fuerte entre varias regiones. 

Con las escrituras de región única, puede mantener una coherencia fuerte al tiempo que conserva un nivel de alta disponibilidad en todas las regiones gracias a la [conmutación por error automática](../high-availability.md#multi-region-accounts-with-a-single-write-region-write-region-outage). En esta configuración, todavía puede aprovechar la ubicación de los datos para reducir la latencia de lectura al degradar la coherencia final en función de cada solicitud. Además de estas funcionalidades, la plataforma de Azure Cosmos DB también proporciona la capacidad de habilitar [redundancia de zona](../high-availability.md#availability-zone-support) al seleccionar una región. Por lo tanto, a diferencia de Apache Cassandra nativo, Azure Cosmos DB le permite recorrer la [gama de ventajas e inconvenientes](../consistency-levels.md#rto) del teorema de CAP más detalladamente.

## <a name="mapping-consistency-levels"></a>Asignación de niveles de coherencia

La plataforma de Azure Cosmos DB proporciona un conjunto de cinco valores de coherencia bien definidos y orientados a casos de uso empresariales con respecto a las ventajas e inconvenientes definidos por el [teorema de CAP](https://en.wikipedia.org/wiki/CAP_theorem) y el [teorema de PACLC](https://en.wikipedia.org/wiki/PACELC_theorem). Dado que este enfoque difiere significativamente del de Apache Cassandra, se recomienda dedicar un tiempo a revisar y comprender la configuración de coherencia de Azure Cosmos DB en nuestra [documentación](../consistency-levels.md), o bien ver esta breve guía en [vídeo](https://www.youtube.com/watch?v=t1--kZjrG-o) para comprender la configuración de coherencia de la plataforma Azure Cosmos DB.

En la tabla siguiente se muestran las posibles asignaciones entre los niveles de coherencia de Apache Cassandra y de Azure Cosmos DB al usar Cassandra API. Esto muestra configuraciones para una única región, lecturas en varias regiones con escrituras de región única y escrituras en varias regiones.

> [!NOTE]
> Estas no son asignaciones exactas. En su lugar, se han proporcionado las análogas más cercanas a Apache Cassandra y se ha eliminado la ambigüedad con todas las diferencias cualitativas de la columna situada más a la derecha. Como se mencionó anteriormente, se recomienda revisar la [configuración de coherencia](../consistency-levels.md) de Azure Cosmos DB. 

:::image type="content" source="./media/apache-cassandra-consistency-mapping/account.png" alt-text="Asignación en el nivel de cuenta de la coherencia con Cassandra" lightbox="./media/apache-cassandra-consistency-mapping/account.png" :::

:::image type="content" source="./media/apache-cassandra-consistency-mapping/dynamic.png" alt-text="Asignación dinámica de la coherencia con Cassandra" lightbox="./media/apache-cassandra-consistency-mapping/dynamic.png" :::

Si la cuenta de Azure Cosmos está configurada con un nivel de coherencia que no es el de coherencia alta, puede averiguar la probabilidad de que los clientes obtengan lecturas de coherencia alta para sus cargas de trabajo consultando la métrica de *obsolescencia limitada de probabilidad* (PBS). Esta métrica se expone en Azure Portal; para obtener más información, consulte [Supervisión de la métrica de obsolescencia limitada de manera probabilística (PBS)](../how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

La obsolescencia limitada de probabilidad muestra cómo de posible es la coherencia final. Esta métrica proporciona una visión general de la frecuencia con la que puede obtener una coherencia mayor que el nivel de coherencia que tiene configurado actualmente en su cuenta de Azure Cosmos. En otras palabras, puede ver la probabilidad (en milisegundos) de obtener lecturas con coherencia alta para una combinación de regiones de escritura y lectura.

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre la distribución global y los niveles de coherencia en Azure Cosmos DB:

* [Introducción a la distribución global](../distribute-data-globally.md)
* [Información general sobre el nivel de coherencia](../consistency-levels.md)
* [Alta disponibilidad](../high-availability.md)

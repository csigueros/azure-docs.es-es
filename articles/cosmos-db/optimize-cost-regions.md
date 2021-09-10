---
title: Optimización de los costos para las implementaciones en varias regiones de Azure Cosmos DB
description: En este artículo se explica cómo administrar los costos de las implementaciones en varias regiones de Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: 0cd4174cf937fa8e2cda96237a09e8cb4f5270e4
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123029153"
---
# <a name="optimize-multi-region-cost-in-azure-cosmos-db"></a>Optimización del costo de varias regiones de Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Puede agregar regiones a la cuenta de Azure Cosmos o eliminarlas en cualquier momento. La capacidad de proceso que se configura para varias bases de datos y contenedores de Azure Cosmos se reserva en cada región asociada a la cuenta. Si el rendimiento aprovisionado por hora, que es la suma de RU/s configurada en todos los contenedores y las bases de datos para su cuenta de Azure Cosmos, es `T` y el número de regiones de Azure asociado a la cuenta de la base de datos es `N`, el rendimiento aprovisionado para la cuenta de Cosmos para una hora determinada es `T x N RU/s`.

La capacidad de proceso con una sola región de escritura cuesta 0,008 USD/hora por cada 100 RU/s y la capacidad de proceso aprovisionada con varias regiones de escritura cuesta 0,016 USD/hora por cada 100 RU/s. Para más información, consulte la [página de precios](https://azure.microsoft.com/pricing/details/cosmos-db/) de Azure Cosmos DB.

## <a name="costs-for-multiple-write-regions"></a>Costos para varias regiones de escritura

En un sistema de escrituras en varias regiones, las RU disponibles netas para las operaciones de escritura aumentan `N` veces, donde `N` es el número de regiones de escritura. A diferencia de la arquitectura con una sola región de escritura, ahora se puede escribir en todas las regiones y estas admiten la resolución de conflictos. Desde el punto de vista de la planificación de los costos, para realizar escrituras por valor de `M` RU/s por todo el mundo, es necesario aprovisionar M `RUs` en un nivel de contenedor o base de datos. A continuación, puede agregar tantas regiones como desee y usarlas para la escritura global por valor de `M` RU.

### <a name="example"></a>Ejemplo

Imagine que tiene un contenedor en la región Oeste de EE. UU., configurado para las escrituras en una sola región, con un rendimiento aprovisionado de 10 000 RU/s y que almacena 0,5 TB de datos este mes. Supongamos que agrega una región, Este de EE. UU., con el mismo almacenamiento y rendimiento, y que desea poder escribir en los contenedores de ambas regiones desde la aplicación. Su nueva factura total mensual (suponiendo que un mes tiene 730 horas) será la siguiente:

|**Elemento**|**Uso (mensual)**|**Tarifa**|**Costo mensual**|
|----|----|----|----|
|Factura del rendimiento para un contenedor en la región Oeste de EE. UU. (operaciones de escritura en una sola región) |10 000 RU/s * 730 horas |0,008 USD por cada 100 RU/s por hora |584 USD |
|Factura del rendimiento para un contenedor en 2 regiones: Oeste de EE. UU. y Este de EE. UU. (operaciones de escritura en varias regiones) |2 * 10 000 RU/s * 730 horas |0,016 USD por cada 100 RU/s por hora |2336 USD |
|Factura de almacenamiento por el contenedor en Oeste de EE. UU. |0,5 TB (o 512 GB) |0,25 USD/GB |128 USD |
|Factura de almacenamiento para un contenedor de 2 regiones: Oeste de EE. UU. y Este de EE. UU. |2 * 0,5 TB (o 1024 GB) |0,25 USD/GB |256 USD |

## <a name="improve-throughput-utilization-on-a-per-region-basis"></a>Mejora del uso de la capacidad de proceso por región

Si experimenta un uso ineficaz, por ejemplo, una o varias regiones de lectura infrautilizadas, puede tomar medidas para optimizar el uso de las RU en las regiones de lectura mediante la fuente de cambios de la región de lectura, o bien moverlas a otra secundaria si se están sobreutilizando. Tendrá que asegurarse de optimizar el rendimiento aprovisionado (RU) en la región de escritura en primer lugar. Las escrituras cuestan más que las lecturas, a excepción de las consultas muy grandes, por lo que mantener un uso uniforme puede suponer un reto. En general, supervise el rendimiento consumido en las regiones y agregue o quite regiones a petición para escalar el rendimiento de lectura y escritura. Asegúrese de comprender el impacto en la latencia de las aplicaciones que se implementan en la misma región.

## <a name="next-steps"></a>Pasos siguientes

A continuación, puede seguir obteniendo más información sobre la optimización de costos en Azure Cosmos DB con los siguientes artículos:

* Obtenga más información sobre la [optimización para desarrollo y pruebas](optimize-dev-test.md).
* Obtenga más información sobre [la factura de Azure Cosmos DB](understand-your-bill.md).
* Obtenga más información sobre la [optimización del costo de la capacidad del rendimiento](optimize-cost-throughput.md).
* Obtenga más información sobre la [optimización del costo del almacenamiento](optimize-cost-storage.md).
* Obtenga más información sobre la [optimización del costo de la lectura y la escritura](optimize-cost-reads-writes.md).
* Obtenga más información sobre la [optimización del costo de las consultas](./optimize-cost-reads-writes.md).
* ¿Intenta planear la capacidad de una migración a Azure Cosmos DB? Puede usar información sobre el clúster de bases de datos existente para planear la capacidad.
    * Si lo único que sabe es el número de núcleos virtuales y servidores del clúster de bases de datos existente, lea sobre el [cálculo de unidades de solicitud mediante núcleos o CPU virtuales](convert-vcore-to-request-unit.md). 
    * Si conoce las tasas de solicitudes típicas de la carga de trabajo de la base de datos actual, obtenga información sobre el [cálculo de unidades de solicitud mediante la herramienta de planeamiento de capacidad de Azure Cosmos DB](estimate-ru-with-capacity-planner.md).

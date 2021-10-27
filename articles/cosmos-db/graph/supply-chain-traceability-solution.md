---
title: Solución de rastreabilidad de la cadena de suministro de Infosys mediante Gremlin API de Azure Cosmos DB
description: La solución de grafos de rastreabilidad de la cadena de suministro implementada por Infosys usa la API de Gremlin de Azure Cosmos DB y otros servicios de Azure. Esta proporciona la funcionalidad de seguimiento de la cadena de suministro global de los productos terminados.
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 10/07/2021
author: manishmsfte
ms.author: mansha
ms.openlocfilehash: 3dacf188e0a3fbf901a97ff2125788f080d6532e
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "130003901"
---
# <a name="supply-chain-traceability-solution-using-azure-cosmos-db-gremlin-api"></a>Solución de rastreabilidad de la cadena de suministro mediante Gremlin API de Azure Cosmos DB

[!INCLUDE[appliesto-gremlin-api](../includes/appliesto-gremlin-api.md)]

En este artículo se proporciona información general sobre las [soluciones de grafos de rastreabilidad implementadas por Infosys](https://azuremarketplace.microsoft.com/marketplace/apps/infosysltd.infosys-traceability-knowledge-graph?tab=Overview). Estas soluciones usan la API de Gremlin de Azure Cosmos DB y otras funcionalidades de Azure para poder supervisar la cadena de suministro global de los productos terminados.

Después de leer este artículo, aprenderá lo siguiente:

* Qué es la rastreabilidad en el contexto de una cadena de suministro.
* Arquitectura de una solución de rastreabilidad global que se ofrece mediante funcionalidades de Azure.  
* Cómo ayuda la base de datos de grafos de Azure Cosmos DB a las complejas relaciones entre la materia prima y el producto acabado en una cadena de suministro global.
* Cómo ayudan los servicios de la plataforma de integración de Azure, como API Management o Event Hubs, a integrar diversos ecosistemas de aplicaciones de la cadena de suministro.
* Cómo puede obtener ayuda a Infosys para usar esta solución con fines de rastreabilidad.

## <a name="overview"></a>Información general

En la cadena de suministro de alimentos, la rastreabilidad del producto consiste en realizar un seguimiento de ellos a lo largo de toda la cadena de suministro durante todo el ciclo de vida del producto. La cadena de suministro incluye los procesos de suministro, fabricación y distribución. La rastreabilidad es fundamental para la seguridad de los alimentos, la marca y la exposición normativa. En el pasado, algunas organizaciones no podían realizar un seguimiento eficaz de los productos en su cadena de suministro, lo que daba lugar a costosas retiradas, multas y problemas de salud de los consumidores. Las soluciones de trazabilidad debían responder a las necesidades de armonización de datos, ingesta de datos a diferente velocidad y veracidad y, lo que es más importante, seguir el ciclo de inventario, objetivos que no eran posibles con las plataformas tradicionales.

La solución de rastreabilidad de Infosys, desarrollada con funcionalidades de Azure, como servicios de aplicaciones, servicios de integración y servicios de bases de datos, proporciona las siguientes funcionalidades vitales:

* Conexión con fábricas, almacenes o centros de distribución.
* Ingesta o procesamiento de eventos de movimiento de existencias paralelos.
* Un grafo de conocimiento, que muestra las conexiones entre la materia prima, el lote, los pallets de productos terminados, la relación de subordinación de pallets de varios niveles y el movimiento de los productos.
* Portal de usuarios con una variedad de funcionalidades de búsqueda que va desde la búsqueda con caracteres comodín hasta la búsqueda de palabras clave específicas.
* La posibilidad de identificar los impactos de un incidente de calidad, como el lote de materia prima afectado, los pallets con problemas o la ubicación de dichos pallets.
* Posibilidad de registrar el historial de eventos en varios mercados, incluida la información sobre la retirada de productos.

## <a name="solution-architecture"></a>Arquitectura de la solución

La rastreabilidad de la cadena de suministro suele compartir patrones en la ingesta de movimientos de pallets, la administración de incidencias de calidad y el seguimiento/análisis de los datos de los almacenes. En primer lugar, estos sistemas necesitan ingerir ráfagas de datos procedentes de los sistemas de administración de fábricas y almacenes que recorren la geografía. A continuación, estos sistemas procesan y analizan los datos de streaming para obtener relaciones complejas entre la materia prima, los lotes de producción, los palés de productos acabados y las complejas relaciones entre elementos primarios y secundarios (empaquetado conjunto/reempaquetado). Luego, el sistema debe almacenar información sobre las intrincadas relaciones entre la materia prima, los productos acabados y los palés, elementos todos ellos necesarios de cara a la rastreabilidad. Un portal de usuarios con funcionalidad de búsqueda permite a los usuarios realizar un seguimiento de los productos de la red de la cadena de suministro. Estos servicios habilitan la solución de trazabilidad completa que admite funcionalidades nativas de la nube, con prioridad a API y basadas en datos.

Microsoft Azure ofrece servicios enriquecidos que se pueden aplicar para casos de uso de rastreabilidad, como Azure Cosmos DB, Azure Event Hubs, Azure API Management, Azure App Service, Azure SignalR, Azure Synapse Analytics y Power BI.

La solución de rastreabilidad de Infosys proporciona una solución prediseñada que puede usarse para mejorar la funcionalidad de seguimiento. En la imagen siguiente se explica la arquitectura utilizada para esta solución de rastreabilidad:

:::image type="content" source="./media/supply-chain-traceability-solution/solution-architecture.png" alt-text="Arquitectura de la solución de rastreabilidad de la cadena de suministro" lightbox="./media/supply-chain-traceability-solution/solution-architecture.png" border="false":::

Los diferentes servicios de Azure usados en esta arquitectura ayudan con las siguientes tareas:

* Azure Cosmos DB permite escalar o reducir verticalmente y de forma elástica el rendimiento. La API de Gremlin permite crear y consultar relaciones complejas entre la materia prima, los productos terminados y los almacenes.
* Azure API Management proporciona diferentes API de eventos de movimiento de existencias a proveedores de logística de terceros (3PL) y a sistemas de administración de almacenamiento (WMS).  
* Azure Event Hubs permite recopilar grandes cantidades de eventos simultáneos de WMS y 3PL para procesarse posteriormente.
* Las aplicaciones de Azure Function procesan eventos e ingieren datos en Azure Cosmos DB mediante Gremlin API.
* El servicio Azure Search permite a los usuarios realizar búsquedas complejas y filtrar información de los pallets.
* Azure Databricks lee la fuente de cambios y crea modelos en Synapse Analytics con el fin de generar informes de autoservicio para los usuarios en Power BI.
* El servicio Web Apps y el plan de App Service de Azure permiten implementar el portal de usuarios.
* La cuenta de Azure Storage almacena los datos archivados para necesidades normativas a largo plazo.

## <a name="graph-db-and-its-data-design"></a>La base de datos de grafos y su diseño de datos

Para producir y distribuir productos hay que mantener un conjunto complejo y dinámico de relaciones.  Un modelo de datos adaptable de nuestro grafo de rastreabilidad permite almacenar dichas relaciones empezando por la recepción de la materia prima, la fabricación de los productos terminados en una fábrica, la transferencia a diferentes almacenes durante la cadena de suministro y, finalmente, el traslado al almacén del cliente. En términos generales, una visualización del proceso se parece a la siguiente imagen:

:::image type="content" source="./media/supply-chain-traceability-solution/data-design-visualization.png" alt-text="Visualización del diseño de datos de la cadena de suministro" lightbox="./media/supply-chain-traceability-solution/data-design-visualization.png" border="true":::

En el diagrama anterior se muestra una vista simplificada y resumida de un proceso complejo de la cadena de suministro. Sin embargo, gracias a que se puede obtener la información fundamental sobre el movimiento de las existencias desde las fábricas y los almacenes en tiempo real, es posible crear un detallado grafo que conecte todos estos datos dispares.

1. El proceso de rastreabilidad se inicia cuando el proveedor envía la materia prima a las fábricas, y se crean los nodos iniciales (vértices) del grafo y las relaciones (bordes).

1. Los productos terminados (elementos) se producen a partir de la materia prima y se empaquetan en pallets.

1. A continuación, los pallets se trasladan a los almacenes de las fábricas o del mercado según las demandas o los pedidos de los clientes.

1. El almacén podría ser propiedad de la empresa o 3PL. Posteriormente, los pallets se envían a otros almacenes según los pedidos de los clientes. En función de las demandas de los clientes, se crean pallets secundarios o pallets secundarios de los secundarios para dar cabida a la cantidad solicitada. A veces, se crea un elemento completamente nuevo combinando varios elementos. Por ejemplo, en un escenario de co-packing donde se produce un paquete de variedades, a veces el mismo artículo se reempaqueta en cantidades más pequeñas o más grandes en un pallet diferente como parte de un pedido del cliente.

   :::image type="content" source="./media/supply-chain-traceability-solution/pallet-relationship.png" alt-text="Relación de los pallets en la solución de rastreabilidad de la cadena de suministro" lightbox="./media/supply-chain-traceability-solution/pallet-relationship.png" border="true":::

1. A continuación, los pallets viajan a través de la red de la cadena de suministro y, finalmente, llegan al almacén del cliente. Durante ese proceso, los pallets pueden seguir dividiéndose o combinarse con otros para producir nuevos pallets que satisfagan los pedidos de los clientes.

1. Finalmente, el sistema crea un grafo complejo que contiene información vital de las relaciones para administrar los incidentes de calidad, que analizaremos en breve.

   :::image type="content" source="./media/supply-chain-traceability-solution/supply-chain-object-relationship.png" alt-text="Arquitectura completa de la relación de los objetos de la cadena de suministro" lightbox="./media/supply-chain-traceability-solution/supply-chain-object-relationship.png" border="true":::

1. Estas relaciones complejas son fundamentales en un incidente de calidad en el que el sistema puede realizar un seguimiento de los pallets a través de la cadena de suministro. El gráfico y sus recorridos proporcionan la información necesaria para ello. Por ejemplo, si hay un problema con una materia prima, el grafo puede mostrar los pallets afectados y la ubicación actual.

## <a name="next-steps"></a>Pasos siguientes

* [Solución de grafos de rastreabilidad de Infosys](https://azuremarketplace.microsoft.com/marketplace/apps/infosysltd.infosys-traceability-knowledge-graph?tab=Overview)
* [Infosys Integrate+ for Azure](https://azuremarketplace.microsoft.com/marketplace/apps/infosysltd.infosys-integrate-for-azure)
* Para visualizar datos de grafos, consulte las [soluciones de visualización de la API de Gremlin](graph-visualization-partners.md).
* Para modelar los datos de grafos, consulte las [soluciones de modelado de la API de Gremlin](graph-modeling-tools.md).

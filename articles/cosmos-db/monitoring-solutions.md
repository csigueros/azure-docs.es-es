---
title: Supervisión de Azure Cosmos DB mediante herramientas de supervisión de terceros
description: En este artículo se describen las herramientas de supervisión de terceros que ayudan a supervisar Cosmos DB.
author: manishmsfte
ms.author: mansha
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/28/2021
ms.openlocfilehash: b78d6a4c92ad96c15d55332aac320bad09c58c1e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779909"
---
# <a name="monitoring-azure-cosmos-db-using-third-party-solutions"></a>Supervisión de Azure Cosmos DB mediante soluciones de terceros
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Además de Azure Monitor, puede usar soluciones de supervisión de terceros para supervisar las instancias de Cosmos DB.

> [!IMPORTANT] 
> Las soluciones mencionadas en este artículo tienen solo carácter informativo; la propiedad es del propietario de cada solución individual. Se recomienda a los usuarios que realicen una evaluación exhaustiva y después seleccionen la opción más adecuada.

## <a name="datadog"></a>Datadog
{Admite: SQL API, API de Azure Cosmos DB para MongoDB, Gremlin API, Cassandra API y Table API}

[Datadog](https://www.datadoghq.com/) es una plataforma totalmente unificada que abarca, entre otras cosas, la supervisión de la infraestructura, la supervisión del rendimiento de las aplicaciones, la administración de registros y la supervisión de la experiencia del usuario. Al reunir datos de todas las herramientas y servicios de la pila de la empresa, Datadog proporciona una única fuente de confianza para solucionar problemas y optimizar el rendimiento y la colaboración entre equipos.
Todo el contenido de Datadog se organiza bajo el mismo conjunto de etiquetas, por lo que todos los datos relacionados con un problema determinado se correlacionan automáticamente. Al eliminar los puntos ciegos, Datadog reduce el riesgo de errores pasados por alto, mitiga la carga del mantenimiento continuo de los servicios y acelera las transformaciones digitales.

Datadog recopila más de 40 métricas de medición y recuento diferentes de CosmosDB, incluido el almacenamiento total disponible por región, el número de bases de datos SQL creadas y mucho más. Estas métricas se recopilan mediante la integración de Azure con Datadog y aparecen en la plataforma un 40 % más rápido que en el resto del sector. Datadog también proporciona un panel de serie para CosmosDB, que ofrece información inmediata sobre el rendimiento de las instancias de CosmosDB. Los usuarios pueden visualizar las métricas de nivel de plataforma, como el total de unidades de solicitud consumidas, así como las métricas de nivel de API, como el número de espacios de claves de Cassandra creados para comprender mejor su uso de CosmosDB.

Varios clientes de Azure Cosmos DB usan Datadog, entre los que se incluyen:
- Maersk
- PWC 
- PayScale 
- AllScripts 
- Hearst



:::image type="content" source="./media/monitor-solutions/datadog-demo.gif" alt-text="Demostración de Datadog" border="false":::
**Figura:** Datadog en acción

Vínculos útiles:
- [Detalles de precios](https://www.datadoghq.com/pricing/)
- [Comienzo de la versión de prueba de 14 días](https://www.datadoghq.com/free-datadog-trial/)


## <a name="dynatrace"></a>Dynatrace
{Admite: SQL API y API de Azure Cosmos DB para MongoDB}

[Dynatrace](https://www.dynatrace.com/platform/) ofrece inteligencia de software para la nube, de forma que se pueda superar la complejidad de esta plataforma y acelerar la transformación digital. Gracias a la observabilidad automática e inteligente a gran escala, la plataforma de inteligencia de software todo en uno de Dynatrace ofrece respuestas precisas sobre el rendimiento y la seguridad de las aplicaciones, la infraestructura subyacente y la experiencia de todos los usuarios, para que los equipos puedan automatizar las operaciones en la nube, publicar mejor software más rápido y ofrecer experiencias digitales sin igual.  
Con Mongo API, Dynatrace recopila y entrega métricas de CosmosDB, que incluyen el número de llamadas y los tiempos de respuesta, todo ello visualizado según las operaciones de agregación, comandos, lectura y escritura.  También indica las instrucciones de base de datos exactas ejecutadas en su entorno.  Por último, gracias al poder del [motor de IA de Davis](https://www.dynatrace.com/davis), se puede detectar exactamente qué instrucción de base de datos es la causa principal de la degradación y ver la base de datos identificada como tal.

:::image type="content" source="./media/monitor-solutions/dynatrace-demo.gif" alt-text="Varias pantallas de Dynatrace para proporcionar información de supervisión de Cosmos DB" border="false":::
**Figura:** Dynatrace en acción

### <a name="useful-links"></a>Vínculos útiles

- [Pruebe Dynatrace con una evaluación gratuita de 15 días](https://www.dynatrace.com/trial)
- [Iniciar desde Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/dynatrace.dynatrace-managed)
- [Documentación sobre la integración de Cosmos DB con Azure Monitor](https://www.dynatrace.com/support/help/technology-support/cloud-platforms/microsoft-azure-services/set-up-integration-with-azure-monitor/?_ga=2.184080354.559899881.1623174355-748416177.1603817475)
- [Cosmos DB: detalles de la integración de Dynatrace](https://www.dynatrace.com/news/blog/azure-services-explained-part-4-azure-cosmos-db/?_ga=2.185016301.559899881.1623174355-748416177.1603817475)
- [Supervisión de Dynatrace para bases de datos de Azure](https://www.dynatrace.com/technologies/azure-monitoring/azure-database-performance/)
- [Introducción a Dynatrace para soluciones de Azure](https://www.dynatrace.com/technologies/azure-monitoring/)
- [Asociados de soluciones](https://www.dynatrace.com/partners/solution-partners/)

## <a name="next-steps"></a>Pasos siguientes
- [Referencia de datos de supervisión de Azure Cosmos DB](./monitor-cosmos-db-reference.md)

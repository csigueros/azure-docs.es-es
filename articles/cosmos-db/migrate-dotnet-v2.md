---
title: Migración de la aplicación para usar el SDK de .NET 2.0 de Azure Cosmos DB (Microsoft.Azure.Cosmos)
description: Aprenda a actualizar la aplicación .NET existente del SDK v1 al SDK de .NET v2 para Core (SQL) API.
author: stefArroyo
ms.author: esarroyo
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/26/2021
ms.openlocfilehash: 0d6fcdc1fde00a24b4317b035e043e3b30ba5b6e
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123037132"
---
# <a name="migrate-your-application-to-use-the-azure-cosmos-db-net-sdk-v2"></a>Migración de la aplicación para usar el SDK de .NET v2 de Azure Cosmos DB

> [!IMPORTANT]
> Es importante tener en cuenta que la versión 3 del SDK de .NET está disponible actualmente y hay un plan de migración de v2 a v3 disponible [aquí](migrate-dotnet-v3.md). Para información sobre el SDK de .NET v2 de Azure Cosmos DB, consulte las [notas de la versión](sql-api-sdk-dotnet.md), el [repositorio de GitHub de .NET](https://github.com/Azure/azure-cosmos-dotnet-v2), las [sugerencias de rendimiento](performance-tips.md) del SDK de .NET v2 y la [guía de solución de problemas](troubleshoot-dot-net-sdk.md).
>

En este artículo se destacan algunas de las cuestiones relacionadas con la actualización de la aplicación .NET v1 existente al nuevo SDK de .NET v2 de Azure Cosmos DB para Core (SQL) API. El SDK de .NET v2 de Azure Cosmos DB corresponde al espacio de nombres `Microsoft.Azure.DocumentDB`. Puede usar la información de este documento si va a migrar la aplicación desde cualquiera de las siguientes plataformas .NET de Azure Cosmos DB para usar el SDK v2 `Microsoft.Azure.Cosmos`:

* SDK de .NET Framework v1 de Azure Cosmos DB para SQL API
* SDK de .NET Core v1 de Azure Cosmos DB para SQL API

## <a name="whats-available-in-the-net-v2-sdk"></a>Características disponibles en el SDK de .NET v2

El SDK v2 contiene muchas mejoras de uso y rendimiento, entre las que se incluyen:

* Compatibilidad con el modo TCP directo para clientes que no son de Windows
* Compatibilidad con escrituras de varias regiones
* Mejoras en el rendimiento de las consultas
* Compatibilidad con colecciones e indexación geométricas o geoespaciales
* Mayores mejoras de los diagnósticos de transporte TCP y directo
* Actualizaciones en la pila de transporte TCP directo para reducir el número de conexiones establecidas
* Mejoras en la reducción de la latencia en RequestTimeout

La mayoría de la lógica de reintento y los niveles inferiores del SDK siguen siendo prácticamente iguales.

## <a name="why-migrate-to-the-net-v2-sdk"></a>Razones para migrar al SDK de .NET v2

Además de las numerosas mejoras de rendimiento, las inversiones en las nuevas características realizadas en el último SDK no se trasladarán a las versiones anteriores.

Además, los SDK anteriores se reemplazarán por versiones más recientes y el SDK v1 pasará a [modo de mantenimiento](sql-api-sdk-dotnet.md). Para obtener la mejor experiencia de desarrollo, se recomienda migrar la aplicación a una versión posterior del SDK.

## <a name="major-changes-from-v1-sdk-to-v2-sdk"></a>Principales cambios al pasar del SDK v1 al SDK v2

### <a name="direct-mode--tcp"></a>Modo directo + TCP

El SDK de .NET v2 ahora es compatible con el modo directo y de puerta de enlace. El modo directo admite la conectividad a través del protocolo TCP y ofrece un mejor rendimiento, ya que se conecta directamente a las réplicas de back-end con menos saltos de red.

Para más información, lea la [guía de los modos de conectividad del SDK de SQL de Azure Cosmos DB](sql-sdk-connection-modes.md).

### <a name="session-token-formatting"></a>Formato de token de sesión

El SDK v2 ya no usa el formato de token de sesión *simple* que usa la versión 1, sino que emplea el formato *vectorial*. Dado que los formatos no son intercambiables, se debe convertir el formato al pasar a la aplicación cliente con versiones diferentes.

Para más información, consulte [Conversión de formatos de token de sesión en el SDK de .NET](how-to-convert-session-token.md).

### <a name="using-the-net-change-feed-processor-sdk"></a>Uso del SDK para los procesadores de fuente de cambios de .NET

La biblioteca de procesadores de fuente de cambios de .NET 2.1.x requiere `Microsoft.Azure.DocumentDB` 2.0 o posterior.

La biblioteca 2.1.x presenta los siguientes cambios principales:

* Mejoras en la estabilidad y el diagnóstico
* Control mejorado de errores y excepciones
* Compatibilidad agregada con colecciones de concesión con particiones
* Extensiones avanzadas para implementar la interfaz de `ChangeFeedDocument` y la clase para el seguimiento y el control de errores adicionales
* Se ha agregado compatibilidad con el uso de un almacén personalizado para conservar los tokens de continuación por partición.

Para más información, consulte las [notas de la versión](sql-api-sdk-dotnet-changefeed.md) de la biblioteca de procesadores de fuente de cambios.

### <a name="using-the-bulk-executor-library"></a>Uso de la biblioteca Bulk Executor

La biblioteca Bulk Executor v2 actualmente tiene una dependencia del SDK de .NET 2.5.1 o posterior de Azure Cosmos DB.

Para más información, consulte la [información general sobre la biblioteca Bulk Executor de Azure Cosmos DB](bulk-executor-overview.md) y las [notas de la versión](sql-api-sdk-bulk-executor-dot-net.md) de la biblioteca Bulk Executor de .NET.

## <a name="next-steps"></a>Pasos siguientes

* Lea las [sugerencias de rendimiento adicionales](sql-api-get-started.md) con el uso de SQL API v2 de Azure Cosmos DB para optimizar la aplicación a fin de alcanzar el máximo rendimiento
* Más información sobre [lo que puede hacer con el SDK v2](sql-api-dotnet-samples.md).
* ¿Intenta planear la capacidad de una migración a Azure Cosmos DB?
    * Si lo único que sabe es el número de núcleos virtuales y servidores del clúster de bases de datos existente, obtenga información sobre el [cálculo de unidades de solicitud mediante núcleos o CPU virtuales](convert-vcore-to-request-unit.md). 
    * Si conoce las tasas de solicitudes típicas de la carga de trabajo de la base de datos actual, obtenga información sobre el [cálculo de unidades de solicitud mediante la herramienta de planeamiento de capacidad de Azure Cosmos DB](estimate-ru-with-capacity-planner.md).
---
title: Modos de conectividad de SDK de SQL de Azure Cosmos DB
description: Obtenga información sobre los diferentes modos de conectividad disponibles en los SDK de SQL de Azure Cosmos DB.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 08/26/2021
ms.author: maquaran
ms.custom: devx-track-dotnet, contperf-fy21q2
ms.openlocfilehash: 645093cc63f351cc196e7d90812e6bc430259eed
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123030924"
---
# <a name="azure-cosmos-db-sql-sdk-connectivity-modes"></a>Modos de conectividad de SDK de SQL de Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

La forma en que un cliente se conecta a Azure Cosmos DB tiene importantes implicaciones de rendimiento, especialmente para la latencia observada en el cliente. Azure Cosmos DB ofrece un modelo de programación RESTful sencillo y abierto sobre HTTPS denominado modo de puerta de enlace. Además, ofrece un protocolo TCP eficaz, que también es RESTful en su modelo de comunicación y usa TLS para la autenticación inicial y el cifrado del tráfico, denominado modo directo.

## <a name="available-connectivity-modes"></a>Modos de conectividad disponibles

Los dos modos de conectividad disponibles son:

  * Modo de puerta de enlace
      
    El modo de puerta de enlace se admite en todas las plataformas de SDK. Si la aplicación se ejecuta dentro de una red corporativa con restricciones de firewall estrictas, el modo de puerta de enlace es la mejor opción, ya que utiliza el puerto HTTPS estándar y un único punto de conexión DNS. La desventaja para el rendimiento, sin embargo, es que el modo de puerta de enlace implica un salto de red adicional cada vez que se leen desde o se escriben datos a Azure Cosmos DB. También se recomienda el modo de conexión de puerta de enlace cuando se ejecutan aplicaciones en entornos que tienen un número limitado de conexiones de socket.

    Cuando use el SDK de Azure Functions, especialmente en el [plan de consumo](../azure-functions/consumption-plan.md), tenga en cuenta los [límites actuales en las conexiones](../azure-functions/manage-connections.md).

  * Modo directo

    El modo directo admite la conectividad a través del protocolo TCP y ofrece un mejor rendimiento, ya que hay menos saltos de red. La aplicación se conecta directamente a las réplicas de back-end. El modo directo solo se admite actualmente en plataformas SDK de .NET y Java.
     
:::image type="content" source="./media/performance-tips/connection-policy.png" alt-text="Modos de conectividad de Azure Cosmos DB" border="false":::

En esencia, estos modos de conectividad condicionan la ruta que las solicitudes de plano de datos (lecturas y escrituras de documentos) toman desde la máquina cliente hasta las particiones en el back-end de Azure Cosmos DB. El modo directo es la opción preferida para obtener el mejor rendimiento: permite al cliente abrir conexiones TCP directamente en las particiones en el back-end de Azure Cosmos DB y enviar solicitudes *directamente* sin intermediarios. Por el contrario, en el modo de puerta de enlace, las solicitudes realizadas por el cliente se enrutan a un servidor denominado "puerta de enlace" en el front-end de Azure Cosmos DB que, a su vez, deriva sus solicitudes a las particiones adecuadas en el back-end de Azure Cosmos DB.

## <a name="service-port-ranges"></a>Intervalos de puertos de servicio

Al usar el modo directo, además de los puertos de puerta de enlace, debe asegurarse de que el intervalo de puertos entre 10000 y 20000 está abierto porque Azure Cosmos DB utiliza puertos TCP dinámicos. Al usar el modo directo en [puntos de conexión privados](./how-to-configure-private-endpoints.md), el intervalo completo de puertos TCP (de 0 a 65535) debe estar abierto. Si estos puertos no están abiertos e intenta usar el protocolo TCP, puede recibir un error 503 de servicio no disponible.

En la tabla siguiente se muestra un resumen de los modos de conectividad disponibles para varias API y los puertos de servicio que se usan para cada API:

|Modo de conexión  |Protocolo admitido  |SDK admitidos  |API o puerto de servicio  |
|---------|---------|---------|---------|
|Puerta de enlace  |   HTTPS    |  Todos los SDK    |   SQL (443), MongoDB (10250, 10255, 10256), Tabla (443), Cassandra (10350), Graph (443) <br> El puerto 10250 se asigna a una instancia de API de Azure Cosmos DB para MongoDB predeterminada sin replicación geográfica. Mientras que los puertos 10255 y 10256 se asignan a la instancia que tiene replicación geográfica.   |
|Directo    |     TCP    |  SDK de .NET SDK de Java    | Al usar puntos de conexión de servicio o públicos: puertos en el intervalo de 10000 a 20000<br>Al usar puntos de conexión privados: puertos en el intervalo de 0 a 65535 |

## <a name="next-steps"></a>Pasos siguientes

Para las optimizaciones de rendimiento específico de la plataforma SDK:

* [Sugerencias de rendimiento del SDK de .NET V2](performance-tips.md)

* [Sugerencias de rendimiento del SDK de .NET V3](performance-tips-dotnet-sdk-v3-sql.md)
 
* [Sugerencias de rendimiento del SDK de Java V4](performance-tips-java-sdk-v4-sql.md)

* ¿Intenta planear la capacidad de una migración a Azure Cosmos DB? Puede usar información sobre el clúster de bases de datos existente para planear la capacidad.
    * Si lo único que sabe es el número de núcleos virtuales y servidores del clúster de bases de datos existente, lea sobre el [cálculo de unidades de solicitud mediante núcleos o CPU virtuales](convert-vcore-to-request-unit.md). 
    * Si conoce las tasas de solicitudes típicas de la carga de trabajo de la base de datos actual, obtenga información sobre el [cálculo de unidades de solicitud mediante la herramienta de planeamiento de capacidad de Azure Cosmos DB](estimate-ru-with-capacity-planner.md).
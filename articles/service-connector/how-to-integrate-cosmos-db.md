---
title: Integración de Azure Cosmos DB con Service Connector
description: Integre Azure Cosmos DB en la aplicación con Service Connector
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: how-to
ms.date: 11/11/2021
ms.openlocfilehash: 8bdd4a23ac0736f2ff4ddf3095f0d77b2af066ce
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2021
ms.locfileid: "132373452"
---
# <a name="integrate-azure-cosmos-db-with-service-connector"></a>Integración de Azure Cosmos DB con Service Connector

En esta página se muestran los tipos de autenticación admitidos y los tipos de cliente de Azure Cosmos DB con Service Connector. Es posible que todavía pueda conectarse a Azure Cosmos DB en otros lenguajes de programación sin usar Service Connector. En esta página también se muestran el nombre y el valor predeterminados de la variable de entorno (o configuración de Spring Boot) que se obtienen al crear la conexión de servicio. Puede obtener más información sobre la [convención de nomenclatura de variables de entorno de Service Connector](concept-service-connector-internals.md).

## <a name="supported-compute-service"></a>Servicio de proceso admitido

- Azure App Service
- Azure Spring Cloud

## <a name="supported-authentication-types-and-client-types"></a>Tipos de autenticación y tipos de cliente admitidos

| Tipo de cliente | Identidad administrada asignada por el sistema | Identidad administrada asignada por el usuario | Secret/ConnectionString | Entidad de servicio |
| --- | --- | --- | --- | --- |
| .NET | ![icono sí](./media/green-check.png) | ![icono sí](./media/green-check.png) | ![icono sí](./media/green-check.png) | ![icono sí](./media/green-check.png) |
| Java  | ![icono sí](./media/green-check.png) | ![icono sí](./media/green-check.png) | ![icono sí](./media/green-check.png) | ![icono sí](./media/green-check.png) |
| Java: Spring Boot | | | ![icono sí](./media/green-check.png) | |
| Node.js | ![icono sí](./media/green-check.png) | ![icono sí](./media/green-check.png) | ![icono sí](./media/green-check.png) | ![icono sí](./media/green-check.png) |
| Go | ![icono sí](./media/green-check.png) | ![icono sí](./media/green-check.png) | ![icono sí](./media/green-check.png) | ![icono sí](./media/green-check.png) |


## <a name="default-environment-variable-names-or-application-properties"></a>Nombres de variables de entorno o propiedades de aplicación predeterminados

### <a name="dotnet-java-nodejs-and-go"></a>Dotnet, Java, Nodejs y Go

**Secret/ConnectionString**

| Nombre de la variable de entorno predeterminado | Descripción | Valor de ejemplo |
| --- | --- | --- |
| AZURE_COSMOS_CONNECTIONSTRING | Cadena de conexión de Mango DB en Cosmos DB | `mongodb://{mango-db-admin-user}:{********}@{mango-db-server}.mongo.cosmos.azure.com:10255/?ssl=true&replicaSet=globaldb&retrywrites=false&maxIdleTimeMS=120000&appName=@{mango-db-server}@` |

**Identidad administrada asignada por el sistema**

| Nombre de la variable de entorno predeterminado | Descripción | Valor de ejemplo |
| --- | --- | --- |
| AZURE_COSMOS_LISTCONNECTIONSTRINGURL | Dirección URL para obtener la cadena de conexión | `https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group-name}/providers/Microsoft.DocumentDB/databaseAccounts/{your-database-server}/listConnectionStrings?api-version=2021-04-15` |
| AZURE_COSMOS_SCOPE | Ámbito de la identidad administrada | `https://management.azure.com/.default` |
| AZURE_COSMOS_RESOURCEENDPOINT | Punto de conexión del recurso| `https://{your-database-server}.documents.azure.com:443/` |

**Identidad administrada asignada por el usuario**

| Nombre de la variable de entorno predeterminado | Descripción | Valor de ejemplo |
| --- | --- | --- |
| AZURE_COSMOS_LISTCONNECTIONSTRINGURL | Dirección URL para obtener la cadena de conexión | `https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group-name}/providers/Microsoft.DocumentDB/databaseAccounts/{your-database-server}/listConnectionStrings?api-version=2021-04-15` |
| AZURE_COSMOS_SCOPE | Ámbito de la identidad administrada | `https://management.azure.com/.default` |
| AZURE_COSMOS_CLIENTID | Identificador del secreto de cliente | `{client-id}` |
| AZURE_COSMOS_SUBSCRIPTIONID | Su Id. de suscripción | `{your-subscription-id}` |
| AZURE_COSMOS_RESOURCEENDPOINT | Punto de conexión del recurso| `https://{your-database-server}.documents.azure.com:443/` |

**Entidad de seguridad de servicio**

| Nombre de la variable de entorno predeterminado | Descripción | Valor de ejemplo |
| --- | --- | --- |
| AZURE_COSMOS_LISTCONNECTIONSTRINGURL | Dirección URL para obtener la cadena de conexión | `https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group-name}/providers/Microsoft.DocumentDB/databaseAccounts/{your-database-server}/listConnectionStrings?api-version=2021-04-15` |
| AZURE_COSMOS_SCOPE | Ámbito de la identidad administrada | `https://management.azure.com/.default` |
| AZURE_COSMOS_CLIENTID | Identificador del secreto de cliente | `{client-id}` |
| AZURE_COSMOS_CLIENTSECRET | Secreto del secreto de cliente | `{client-secret}` |
| AZURE_COSMOS_TENANTID | El identificador de inquilino | `{tenant-id}` |
| AZURE_COSMOS_SUBSCRIPTIONID | Su Id. de suscripción | `{your-subscription-id}` |
| AZURE_COSMOS_RESOURCEENDPOINT | Punto de conexión del recurso| `https://{your-database-server}.documents.azure.com:443/` |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre Service Connector, siga los tutoriales que se enumeran a continuación.

> [!div class="nextstepaction"]
> [Más información sobre los conceptos de Service Connector](./concept-service-connector-internals.md)

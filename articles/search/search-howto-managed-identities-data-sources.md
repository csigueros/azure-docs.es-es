---
title: Configuración de una conexión a un origen de datos mediante una identidad administrada
titleSuffix: Azure Cognitive Search
description: Obtenga información sobre cómo configurar una conexión de indexador a un origen de datos mediante una identidad administrada.
author: markheff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/02/2021
ms.openlocfilehash: bd4d10c32f1c850adf6dc886672b16937b553222
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122179398"
---
# <a name="set-up-an-indexer-connection-to-a-data-source-using-a-managed-identity"></a>Configuración de una conexión de indexador a un origen de datos mediante una identidad administrada

> [!IMPORTANT] 
> No se admite la configuración de una conexión a un origen de datos mediante una identidad administrada con el nivel Gratis de Azure Cognitive Search.

Un [indexador](search-indexer-overview.md) de Azure Cognitive Search es un rastreador que proporciona una manera de extraer datos del origen de datos en Azure Cognitive Search. Un indexador obtiene una conexión de origen de datos de un objeto de origen de datos creado por usted. El objeto de origen de datos normalmente incluye las credenciales del origen de datos de destino. Por ejemplo, el objeto de origen de datos podría incluir una clave de cuenta de Azure Storage si desea indexar datos de un contenedor de Blob Storage.

En muchos casos, proporcionar credenciales directamente en el objeto de origen de datos no es un problema, pero pueden presentarse algunos desafíos:
* ¿Cómo puedo proteger las credenciales en mi código que crea el objeto de origen de datos?
* Si la contraseña o la clave de la cuenta se ve comprometida y necesito cambiarla, ahora necesito actualizar mis objetos de origen de datos con la nueva clave o contraseña de la cuenta para que mi indexador pueda conectarse de nuevo al origen de datos.

Estos problemas pueden resolverse mediante la configuración de la conexión con una identidad administrada.

## <a name="using-managed-identities"></a>Uso de identidades administradas

Las [identidades administradas](../active-directory/managed-identities-azure-resources/overview.md) son una característica que proporciona a las aplicaciones una identidad administrada automáticamente en Azure Active Directory (Azure AD). Puede usar esta característica en Azure Cognitive Search para crear un objeto de origen de datos con una cadena de conexión que no incluya ninguna credencial. En su lugar, se concederá al servicio de búsqueda acceso al origen de datos mediante el control de acceso basado en rol de Azure (RBAC de Azure).

Al configurar un origen de datos mediante una identidad administrada, puede cambiar las credenciales del origen de datos y los indexadores seguirán pudiendo conectarse al origen de datos. También puede crear objetos de origen de datos en el código sin tener que incluir una clave de cuenta ni usar Key Vault para recuperar una clave de cuenta.

Hay dos tipos de identidades administradas: Azure Cognitive Search admite identidades administradas asignadas por el sistema e identidades administradas asignadas por el usuario.

### <a name="system-assigned-managed-identity"></a>Identidad administrada asignada por el sistema

Una [identidad administrada asignada por el sistema](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) se puede asignar a un único servicio de Azure. Puede asignar una identidad administrada asignada por el sistema a un único servicio de Azure Cognitive Search, que estará vinculada al ciclo de vida de ese servicio de búsqueda.

### <a name="user-assigned-managed-identity-preview"></a>Identidad administrada asignada por el usuario (versión preliminar)

> [!IMPORTANT]
>Esta característica se encuentra en versión preliminar pública en los [términos de uso complementarios](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). La versión 2021-04-30-Preview de la de API REST y [Management REST API 2021-04-01-Preview](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update) proporcionan esta característica.

Una [identidad asignada administrada por el usuario](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) es un recurso de Azure independiente que puede asignarse a uno o varios servicios de Azure. Un único servicio de Azure Cognitive Search puede tener asignadas una o varias identidades administradas asignadas por el usuario. Una única identidad administrada asignada por el usuario se puede asignar a varios servicios de búsqueda.

## <a name="limitations"></a>Limitaciones

Los orígenes de datos siguientes admiten la configuración de una conexión de indexador mediante identidades administradas. 

* [Azure Blob Storage, Azure Data Lake Storage Gen2 (versión preliminar), Azure Table Storage](search-howto-managed-identities-storage.md)
* [Azure Cosmos DB](search-howto-managed-identities-cosmos-db.md)
* [Azure SQL Database](search-howto-managed-identities-sql.md)

Las siguientes características no admiten actualmente el uso de identidades administradas para configurar la conexión:
* Knowledge Store
* Aptitudes personalizadas
 
## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre cómo configurar una conexión de indexador mediante identidades administradas:

* [Azure Blob Storage, Azure Data Lake Storage Gen2 (versión preliminar), Azure Table Storage](search-howto-managed-identities-storage.md)
* [Azure Cosmos DB](search-howto-managed-identities-cosmos-db.md)
* [Azure SQL Database](search-howto-managed-identities-sql.md)
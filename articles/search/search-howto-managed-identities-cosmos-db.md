---
title: Configuración de una conexión a una cuenta de Cosmos DB mediante una identidad administrada
titleSuffix: Azure Cognitive Search
description: Aprenda a configurar una conexión de indexador a una cuenta de Cosmos DB mediante una identidad administrada.
author: markheff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/02/2021
ms.openlocfilehash: 38709d7799d09e38fdebc8eebd7484504ce4ebd2
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122178950"
---
# <a name="set-up-an-indexer-connection-to-a-cosmos-db-database-using-a-managed-identity"></a>Configuración de una conexión de indexador a una base de datos de Cosmos DB mediante una identidad administrada

En esta página se describe cómo configurar una conexión de indexador a una base de datos de Azure Cosmos DB mediante una identidad administrada en lugar de proporcionar credenciales en la cadena de conexión del objeto de origen de datos.

Puede usar una identidad administrada asignada por el sistema o asignada por el usuario (versión preliminar).

Antes de obtener más información acerca de esta característica, se recomienda comprender qué es un indexador y cómo configurar un indexador para el origen de datos. Se puede encontrar más información en los vínculos siguientes:

* [Información general del indexador](search-indexer-overview.md)
* [Indexador de Azure Cosmos DB](search-howto-index-cosmosdb.md)

## <a name="1---set-up-a-managed-identity"></a>1 - Configuración de una identidad administrada

Configure la [identidad administrada](../active-directory/managed-identities-azure-resources/overview.md) usando una de las siguientes opciones.

### <a name="option-1---turn-on-system-assigned-managed-identity"></a>Opción 1: Activación de la identidad administrada asignada por el sistema

Cuando se habilita una identidad administrada asignada por el sistema, Azure crea una identidad para el servicio de búsqueda que se puede usar para autenticarse en otros servicios de Azure en el mismo inquilino y la misma suscripción. Después, puede usar esta identidad en las asignaciones de control de acceso basado en rol de Azure (RBAC de Azure) que permiten el acceso a los datos durante la indexación.

![Activar la identidad administrada asignada por el sistema](./media/search-managed-identities/turn-on-system-assigned-identity.png "Activar la identidad administrada asignada por el sistema")

Después de seleccionar **Guardar**, verá un identificador de objeto que se ha asignado al servicio de búsqueda.

![Id. de objeto](./media/search-managed-identities/system-assigned-identity-object-id.png "Id. de objeto")
 
### <a name="option-2---assign-a-user-assigned-managed-identity-to-the-search-service-preview"></a>Opción 2: Asignación de una identidad administrada asignada por el usuario al servicio de búsqueda (versión preliminar)

Si todavía no tiene creada una identidad administrada asignada por el usuario, tendrá que crear una. Una identidad administrada asignada por el usuario es un recurso en Azure.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
1. Seleccione **+ Crear un recurso**.
1. En la barra de búsqueda "Servicios de búsqueda y Marketplace", busque "Identidad administrada asignada por el usuario" y, después, seleccione **Crear**.
1. Asigne un nombre descriptivo a la identidad.

A continuación, asigne la identidad administrada asignada por el usuario al servicio de búsqueda. Esta operación se puede realizar mediante la [API de administración 2021-04-01-preview](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update).

La propiedad de identidad toma un tipo y una o más identidades asignadas por el usuario completas:

* **type** es el tipo de identidad. Los valores válidos son "SystemAssigned", "UserAssigned" o "SystemAssigned, UserAssigned" si desea usar ambos. Un valor de "None" borrará del servicio de búsqueda las identidades asignadas previamente.
* **userAssignedIdentities** incluye los detalles de la identidad administrada asignada por el usuario.
    * Formato de la identidad administrada asignada por el usuario: 
        * /subscriptions/**subscription ID**/resourcegroups/**nombre del grupo de recursos**/providers/Microsoft.ManagedIdentity/userAssignedIdentities/**nombre de la identidad administrada**

Ejemplo de cómo asignar una identidad administrada asignada por el usuario a un servicio de búsqueda:

```http
PUT https://management.azure.com/subscriptions/[subscription ID]/resourceGroups/[resource group name]/providers/Microsoft.Search/searchServices/[search service name]?api-version=2021-04-01-preview
Content-Type: application/json

{
  "location": "[region]",
  "sku": {
    "name": "[sku]"
  },
  "properties": {
    "replicaCount": [replica count],
    "partitionCount": [partition count],
    "hostingMode": "default"
  },
  "identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/[subscription ID]/resourcegroups/[resource group name]/providers/Microsoft.ManagedIdentity/userAssignedIdentities/[name of managed identity]": {}
    }
  }
} 
```
 
## <a name="2---add-a-role-assignment"></a>2 - Agregar una asignación de roles

En este paso, concederá permiso a su servicio Azure Cognitive Search o a la identidad administrada asignada por el usuario para leer datos de su base de datos de Cosmos DB.

1. En Azure Portal, navegue hasta la cuenta de Cosmos DB que contiene los datos que quiere indexar.
2. Seleccione **Access Control (IAM)**
3. Seleccione **Agregar** y, a continuación, **Agregar asignación de roles**.

    ![Agregar asignación de roles](./media/search-managed-identities/add-role-assignment-cosmos-db.png "Agregar asignación de roles")

4. Seleccione el **Rol de lector de cuentas de Cosmos DB**.
5. Deje **Asignar acceso a** como **Usuario, grupo o entidad de servicio de Azure AD**.
6. Si usa una identidad administrada asignada por el sistema, busque el servicio de búsqueda y selecciónelo. Si usa una identidad administrada asignada por el usuario, busque el nombre de la identidad y selecciónelo. Seleccione **Guardar**.

    Ejemplo de Cosmos DB en el que se usa una identidad administrada asignada por el sistema:

    ![Agregue la asignación del rol Lector y acceso a datos](./media/search-managed-identities/add-role-assignment-cosmos-db-account-reader-role.png "Agregar la asignación del rol Lector y acceso a datos").

## <a name="3---create-the-data-source"></a>3 - Crear el origen de datos

Cree el origen de datos y proporcione una identidad administrada asignada por el sistema o asignada por el usuario (versión preliminar). Tenga presente que ya no va a usar la API de REST de administración en los pasos siguientes.

### <a name="option-1---create-the-data-source-with-a-system-assigned-managed-identity"></a>Opción 1: Creación del origen de datos con una identidad administrada asignada por el sistema

La [API de REST](/rest/api/searchservice/create-data-source), Azure Portal y el [SDK de .NET](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype) permiten usar una identidad administrada asignada por el sistema. A continuación se muestra un ejemplo de cómo crear un origen de datos para indexar los datos de Cosmos DB mediante la [API REST](/rest/api/searchservice/create-data-source) y una cadena de conexión de identidad administrada. El formato de la cadena de conexión de identidad administrada es el mismo para la API REST, el SDK de .NET y Azure Portal.

Si se utilizan identidades administradas para autenticarse, las **credenciales** no incluirán una clave de cuenta.

```http
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [Search service admin key]

{
    "name": "cosmos-db-datasource",
    "type": "cosmosdb",
    "credentials": {
        "connectionString": "Database=sql-test-db;ResourceId=/subscriptions/[subscription ID]/resourceGroups/[resource group name]/providers/Microsoft.DocumentDB/databaseAccounts/[Cosmos DB account name]/;"
    },
    "container": { "name": "myCollection", "query": null },
    "dataChangeDetectionPolicy": {
        "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName": "_ts"
    }
}
```    

El cuerpo de la solicitud contiene la definición del origen de datos, que debe incluir los siguientes campos:

| Campo   | Descripción |
|---------|-------------|
| **name** | Necesario. Elija un nombre para representar el objeto de origen de datos. |
|**type**| Necesario. Debe ser `cosmosdb`. |
|**credentials** | Necesario. <br/><br/>Al conectarse mediante una identidad administrada, el formato de las **credenciales** debe ser: *Database=[nombre-base-de-datos];ResourceId=[cadena-id-recurso];(ApiKind=[clase-api];)*<br/> <br/>Formato de ResourceId: *ResourceId=/subscriptions/**id. de la suscripción**/resourceGroups/**nombre del grupo de recursos**/providers/Microsoft.DocumentDB/databaseAccounts/**nombre de la cuenta de Cosmos DB**/;*<br/><br/>En las colecciones de SQL, la cadena de conexión no requiere un elemento ApiKind.<br/><br/>Para las colecciones de MongoDB, agregue **ApiKind=MongoDb** a la cadena de conexión. <br/><br/>En el caso de los gráficos de Gremlin, regístrese para la [versión preliminar del indexador validada](https://aka.ms/azure-cognitive-search/indexer-preview) para obtener acceso a la versión preliminar e información sobre cómo dar formato a las credenciales.<br/>|
| **container** | Contiene los siguientes elementos: <br/>**name**: Necesario. Especifique el identificador de la colección de la base de datos que se va a indexar.<br/>**query**: Opcional. Puede especificar una consulta para acoplar un documento JSON arbitrario en un esquema plano que Azure Cognitive Search pueda indizar.<br/>En MongoDB API y Gremlin API, no se admiten consultas. |
| **dataChangeDetectionPolicy** | Recomendado |
|**dataDeletionDetectionPolicy** | Opcional |

### <a name="option-2---create-the-data-source-with-a-user-assigned-managed-identity"></a>Opción 2: Creación del origen de datos con una identidad administrada asignada por el usuario

La API de REST 2021-04-30-preview admite la identidad administrada asignada por el usuario. A continuación se muestra un ejemplo de cómo crear un origen de datos para indexar datos de una cuenta de almacenamiento usando la [API de REST](/rest/api/searchservice/create-data-source), una cadena de conexión de identidad administrada y la identidad administrada asignada por el usuario.

```http
POST https://[service name].search.windows.net/datasources?api-version=2021-04-30-preview
Content-Type: application/json
api-key: [Search service admin key]

{
    "name": "cosmos-db-datasource",
    "type": "cosmosdb",
    "credentials": {
        "connectionString": "Database=sql-test-db;ResourceId=/subscriptions/[subscription ID]/resourceGroups/[resource group name]/providers/Microsoft.DocumentDB/databaseAccounts/[Cosmos DB account name]/;"
    },
    "container": { 
        "name": "myCollection", "query": null 
    },
    "identity" : { 
        "@odata.type": "#Microsoft.Azure.Search.DataUserAssignedIdentity",
        "userAssignedIdentity" : "/subscriptions/[subscription ID]/resourcegroups/[resource group name]/providers/Microsoft.ManagedIdentity/userAssignedIdentities/[managed identity name]" 
    },
    "dataChangeDetectionPolicy": {
        "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName": "_ts"
    }
}
```    

El cuerpo de la solicitud contiene la definición del origen de datos, que debe incluir los siguientes campos:

| Campo   | Descripción |
|---------|-------------|
| **name** | Necesario. Elija un nombre para representar el objeto de origen de datos. |
|**type**| Necesario. Debe ser `cosmosdb`. |
|**credentials** | Necesario. <br/><br/>Al conectarse mediante una identidad administrada, el formato de las **credenciales** debe ser: *Database=[nombre-base-de-datos];ResourceId=[cadena-id-recurso];(ApiKind=[clase-api];)*<br/> <br/>Formato de ResourceId: *ResourceId=/subscriptions/**id. de la suscripción**/resourceGroups/**nombre del grupo de recursos**/providers/Microsoft.DocumentDB/databaseAccounts/**nombre de la cuenta de Cosmos DB**/;*<br/><br/>En las colecciones de SQL, la cadena de conexión no requiere un elemento ApiKind.<br/><br/>Para las colecciones de MongoDB, agregue **ApiKind=MongoDb** a la cadena de conexión. <br/><br/>En el caso de los gráficos de Gremlin, regístrese para la [versión preliminar del indexador validada](https://aka.ms/azure-cognitive-search/indexer-preview) para obtener acceso a la versión preliminar e información sobre cómo dar formato a las credenciales.<br/>|
| **container** | Contiene los siguientes elementos: <br/>**name**: Necesario. Especifique el identificador de la colección de la base de datos que se va a indexar.<br/>**query**: Opcional. Puede especificar una consulta para acoplar un documento JSON arbitrario en un esquema plano que Azure Cognitive Search pueda indizar.<br/>En MongoDB API y Gremlin API, no se admiten consultas. |
| **identity** | Contiene la colección de identidades administradas asignadas por el usuario. Solo se debe proporcionar una identidad administrada asignada por el usuario al crear el origen de datos. Contiene los siguientes elementos: <br/>**userAssignedIdentities** incluye los detalles de la identidad administrada asignada por el usuario.<br/><br/>Formato de identidad administrada asignada por el usuario: /subscriptions/**subscription ID**/resourcegroups/**nombre del grupo de recursos**/providers/Microsoft.ManagedIdentity/userAssignedIdentities/**nombre de la identidad administrada**|
| **dataChangeDetectionPolicy** | Recomendado |
|**dataDeletionDetectionPolicy** | Opcional |

## <a name="4---create-the-index"></a>4 - Crear el índice

El índice especifica los campos de un documento, los atributos y otras construcciones que conforman la experiencia de búsqueda.

Aquí se muestra cómo crear un índice con un campo `booktitle` que admite búsquedas:

```
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-target-index",
    "fields": [
    { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
    { "name": "booktitle", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
    ]
}
```

Para obtener más información sobre la creación de índices, vea el artículo de [creación de índices](/rest/api/searchservice/create-index).

## <a name="5---create-the-indexer"></a>5 - Crear el indexador

Un indexador conecta un origen de datos con un índice de búsqueda de destino y proporciona una programación para automatizar la actualización de datos.

Una vez creados el índice y el origen de datos, ya podrá crear el indexador.

Ejemplo de definición del indexador:

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "cosmos-db-indexer",
      "dataSourceName" : "cosmos-db-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }
```

Este indizador se ejecutará cada dos horas (el intervalo de programación se establece en PT2H). Para ejecutar un indizador cada 30 minutos, establézcalo en PT30M. El intervalo más breve que se admite es de 5 minutos. La programación es opcional: si se omite, el indizador solo se ejecuta una vez cuando se crea. Sin embargo, puede ejecutarlo a petición en cualquier momento.   

Para más información sobre la API Create Indexer, consulte [Crear indexador](/rest/api/searchservice/create-indexer).

Para más información sobre cómo definir las programaciones del indexador, consulte [Programación de indexadores para Azure Cognitive Search](search-howto-schedule-indexers.md).

## <a name="troubleshooting"></a>Solución de problemas

Si no puede indexar los datos de Cosmos DB, tenga en cuenta lo siguiente:

1. Si ha rotado recientemente las claves de la cuenta de Cosmos DB, tendrá que esperar hasta 15 minutos para que la cadena de conexión de la identidad administrada funcione.

1. Compruebe si la cuenta de Cosmos DB tiene acceso restringido a las redes seleccionadas. Si es así, vea [Acceso del indexador a orígenes de datos mediante las características de seguridad de red de Azure](search-indexer-securing-resources.md).

## <a name="next-steps"></a>Pasos siguientes

* [Indexador de Azure Cosmos DB](search-howto-index-cosmosdb.md)
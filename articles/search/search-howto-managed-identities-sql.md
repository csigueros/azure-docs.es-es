---
title: Configuración de una conexión a Azure SQL Database mediante una identidad administrada
titleSuffix: Azure Cognitive Search
description: Aprenda a configurar una conexión de indexador a Azure SQL Database mediante una identidad administrada.
author: markheff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/02/2021
ms.openlocfilehash: c0bff947d0f5b58a956d05959dd49bbea1f942ab
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122183988"
---
# <a name="set-up-an-indexer-connection-to-azure-sql-database-using-a-managed-identity"></a>Configuración de una conexión de indexador a Azure SQL Database mediante una identidad administrada

Esta página describe cómo configurar una conexión de indexador a Azure SQL Database mediante una identidad administrada en lugar de proporcionar credenciales en la cadena de conexión del objeto de origen de datos.

Puede usar una identidad administrada asignada por el sistema o asignada por el usuario (versión preliminar).

Antes de obtener más información acerca de esta característica, se recomienda comprender qué es un indexador y cómo configurar un indexador para el origen de datos. Se puede encontrar más información en los vínculos siguientes:

* [Información general del indexador](search-indexer-overview.md)
* [Indexador de Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

## <a name="1---set-up-a-managed-identity"></a>1 - Configuración de una identidad administrada

Configure la [identidad administrada](../active-directory/managed-identities-azure-resources/overview.md) usando una de las siguientes opciones.

### <a name="option-1---turn-on-system-assigned-managed-identity"></a>Opción 1: Activación de la identidad administrada asignada por el sistema

Cuando se habilita una identidad administrada asignada por el sistema, Azure crea una identidad para el servicio de búsqueda que se puede usar para autenticarse en otros servicios de Azure en el mismo inquilino y la misma suscripción. Después, puede usar esta identidad en las asignaciones de control de acceso basado en rol de Azure (RBAC de Azure) que permiten el acceso a los datos durante la indexación.

![Activar la identidad administrada asignada por el sistema](./media/search-managed-identities/turn-on-system-assigned-identity.png "Activación de la identidad administrada asignada por el sistema")

Después de seleccionar **Guardar**, verá un identificador de objeto que se ha asignado al servicio de búsqueda.

![Id. de objeto](./media/search-managed-identities/system-assigned-identity-object-id.png "Id. de objeto")
 
### <a name="option-2---assign-a-user-assigned-managed-identity-to-the-search-service-preview"></a>Opción 2: Asignación de una identidad administrada asignada por el usuario al servicio de búsqueda (versión preliminar)

Si todavía no tiene creada una identidad administrada asignada por el usuario, tendrá que crear una. Una identidad administrada asignada por el usuario es un recurso en Azure.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
1. Seleccione **+ Crear un recurso**.
1. En la barra de búsqueda "Search services and marketplace» (Servicios de búsqueda y Marketplace), busque "Identidad administrada asignada por el usuario" y, después, seleccione **Crear**.
1. Asigne un nombre descriptivo a la identidad.

A continuación, asigne la identidad administrada asignada por el usuario al servicio de búsqueda. Esta operación se puede realizar mediante la [API de administración 2021-04-01-preview](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update).

La propiedad de identidad toma un tipo y una o más identidades asignadas por el usuario completamente calificadas:

* **type** es el tipo de identidad. Los valores válidos son "SystemAssigned", "UserAssigned" o "SystemAssigned, UserAssigned" si desea usar ambos. Un valor de "None" borrará del servicio de búsqueda las identidades asignadas previamente.
* **userAssignedIdentities** incluye los detalles de la identidad administrada asignada por el usuario.
    * Formato de la identidad administrada asignada por el usuario: 
        * /subscriptions/**ID de suscripción**/resourcegroups/**nombre del grupo de recursos**/providers/Microsoft.ManagedIdentity/userAssignedIdentities/**nombre de la identidad administrada**

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

## <a name="2---provision-azure-active-directory-admin-for-sql-server"></a>2\. Aprovisionamiento del administrador de Azure Active Directory para SQL Server

Cuando se conecte a la base de datos en el paso siguiente, tendrá que conectarse a una cuenta Azure Active Directory (Azure AD) que tenga acceso de administrador a la base de datos para que el servicio de búsqueda tenga permiso de acceso a la base de datos.

Consulte [Configuración y administración de la autenticación de Azure AD con Azure SQL](../azure-sql/database/authentication-aad-configure.md?tabs=azure-powershell) para proporcionar acceso de administrador de Azure AD a la base de datos.

## <a name="3---assign-permissions-to-read-the-database"></a>3 - Asignación de permisos para leer la base de datos

Siga los pasos que se indican a continuación para asignar al servicio de búsqueda o a la identidad gestionada asignada por el usuario el permiso para leer la base de datos.

1. Conexión a Visual Studio

    ![Conexión a Visual Studio](./media/search-managed-identities/connect-with-visual-studio.png "Conexión a Visual Studio")

2. Autenticación con la cuenta de Azure AD

    ![Autenticar](./media/search-managed-identities/visual-studio-authenticate.png "Authenticate")

3. Ejecute los siguientes comandos:

    Ponga entre corchetes el nombre del servicio de búsqueda o el nombre de la identidad administrada asignada por el usuario.
    
    ```
    CREATE USER [insert your search service name here or user-assigned managed identity name] FROM EXTERNAL PROVIDER;
    EXEC sp_addrolemember 'db_datareader', [insert your search service name here or user-assigned managed identity name];
    ```

    ![Nueva consulta](./media/search-managed-identities/visual-studio-new-query.png "Nueva consulta")

    ![Ejecutar consulta](./media/search-managed-identities/visual-studio-execute-query.png "Ejecutar consulta")

>[!NOTE]
> Si se modifica la identidad del servicio de búsqueda o la identidad asignada por el usuario del paso 1 después de completar este paso, tendrá que eliminar la pertenencia a rol y el usuario de la base de datos SQL. A continuación, vuelva a agregar los permisos completando el paso 3.
> Para eliminar la pertenencia al rol y el usuario, ejecute los siguientes comandos:
> ```
> sp_droprolemember 'db_datareader', [insert your search service name or user-assigned managed identity name];
> DROP USER IF EXISTS [insert your search service name or user-assigned managed identity name];
> ```

## <a name="4---add-a-role-assignment"></a>4\. Adición de una asignación de roles

En este paso, concederá permiso a su servicio de Azure Cognitive Search para leer los datos de SQL Server.

1. En Azure Portal, vaya a la página de Azure SQL Server.
2. Seleccione **Access Control (IAM)**
3. Seleccione **Agregar** y, a continuación, **Agregar asignación de roles**.

    ![Agregar asignación de roles](./media/search-managed-identities/add-role-assignment-sql-server.png "Agregar asignación de roles")

4. Seleccione el rol de **lector** adecuado.
5. Deje **Asignar acceso a** como **Usuario, grupo o entidad de servicio de Azure AD**.
6. Si usa una identidad administrada asignada por el sistema, busque el servicio de búsqueda y selecciónelo. Si usa una identidad administrada asignada por el usuario, busque el nombre de la identidad y selecciónelo. Seleccione **Guardar**.

    Ejemplo de Azure SQL en el que se usa una identidad administrada asignada por el sistema:

    ![Adición de una asignación de roles](./media/search-managed-identities/add-role-assignment-sql-server-reader-role.png "Adición de una asignación de roles")

## <a name="5---create-the-data-source"></a>5\. Creación del origen de datos

Cree el origen de datos y proporcione una identidad administrada asignada por el sistema o asignada por el usuario (versión preliminar). Tenga presente que ya no va a usar la API de REST de administración en los pasos siguientes.

### <a name="option-1---create-the-data-source-with-a-system-assigned-managed-identity"></a>Opción 1: Creación del origen de datos con una identidad administrada asignada por el sistema

La [API de REST](/rest/api/searchservice/create-data-source), Azure Portal y el [SDK de .NET](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) admiten una identidad administrada asignada por el sistema. A continuación se muestra un ejemplo de cómo crear un origen de datos para indexar los datos de la base de datos de Azure SQL mediante la [API REST](/rest/api/searchservice/create-data-source) y una cadena de conexión de identidad administrada. El formato de la cadena de conexión de identidad administrada es el mismo para la API REST, el SDK de .NET y Azure Portal.

Al crear un origen de datos mediante la [API de REST](/rest/api/searchservice/create-data-source), el origen de datos debe tener las siguientes propiedades obligatorias:

* **name** es el nombre único del origen de datos dentro del servicio de búsqueda.
* **type** es `azuresql`.
* **credentials**
    * Cuando se usa una identidad administrada con fines de autenticación, el formato de **credentials** es diferente a cuando no se usa la identidad administrada. Aquí proporcionará un nombre de catálogo o base de datos original y un ResourceId que no tenga ninguna clave o contraseña de cuenta. ResourceId debe incluir el identificador de suscripción de Azure SQL Database, así como el grupo de recursos de SQL Database y el nombre de la base de datos SQL. 
    * Formato de la cadena de conexión de identidad administrada:
        * *Initial Catalog|Database=**nombre de la base de datos**;ResourceId=/subscriptions/**Id. de la suscripción**/resourceGroups/**nombre del grupo de recursos**/providers/Microsoft.Sql/servers/**nombre de SQL Server**/Connection Timeout=**duración del tiempo de espera de conexión**;*
* **container** indica el nombre de la tabla o la vista que desea indexar.

Ejemplo de cómo crear un objeto de origen de datos de Azure SQL mediante la [API de REST](/rest/api/searchservice/create-data-source):

```
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sql-datasource",
    "type" : "azuresql",
    "credentials" : { 
        "connectionString" : "Database=[SQL database name];ResourceId=/subscriptions/[subscription ID]/resourceGroups/[resource group name]/providers/Microsoft.Sql/servers/[SQL Server name];Connection Timeout=30;"
    },
    "container" : { 
        "name" : "my-table" 
    }
} 
```

### <a name="option-2---create-the-data-source-with-a-user-assigned-managed-identity"></a>Opción 2: Creación del origen de datos con una identidad administrada asignada por el usuario

La API de REST 2021-04-30-preview admite la identidad administrada asignada por el usuario. A continuación se muestra un ejemplo de cómo crear un origen de datos para indexar datos de una cuenta de almacenamiento usando la [API de REST](/rest/api/searchservice/create-data-source), una cadena de conexión de identidad administrada y la identidad administrada asignada por el usuario.

El origen de datos debe tener las siguientes propiedades obligatorias:

* **name** es el nombre único del origen de datos dentro del servicio de búsqueda.
* **type** es `azuresql`.
* **credentials**
    * Cuando se usa una identidad administrada con fines de autenticación, el formato de **credentials** es diferente a cuando no se usa la identidad administrada. Aquí proporcionará un nombre de catálogo o base de datos original y un ResourceId que no tenga ninguna clave o contraseña de cuenta. ResourceId debe incluir el identificador de suscripción de Azure SQL Database, así como el grupo de recursos de SQL Database y el nombre de la base de datos SQL. 
    * Formato de la cadena de conexión de identidad administrada:
        * *Initial Catalog|Database=**nombre de la base de datos**;ResourceId=/subscriptions/**Id. de la suscripción**/resourceGroups/**nombre del grupo de recursos**/providers/Microsoft.Sql/servers/**nombre de SQL Server**/Connection Timeout=**duración del tiempo de espera de conexión**;*
* **container** indica el nombre de la tabla o la vista que desea indexar.
* **identity** contiene la colección de identidades administradas asignadas por el usuario. Solo se debe proporcionar una identidad administrada asignada por el usuario al crear el origen de datos.
    * **userAssignedIdentities** incluye los detalles de la identidad administrada asignada por el usuario.
        * Formato de la identidad administrada asignada por el usuario: 
            * /subscriptions/**ID de suscripción**/resourcegroups/**nombre del grupo de recursos**/providers/Microsoft.ManagedIdentity/userAssignedIdentities/**nombre de la identidad administrada**

Ejemplo de cómo crear un objeto de origen de datos de blob mediante la [API de REST](/rest/api/searchservice/create-data-source):

```http
POST https://[service name].search.windows.net/datasources?api-version=2021-04-30-preview
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sql-datasource",
    "type" : "azuresql",
    "credentials" : { 
        "connectionString" : "Database=[SQL database name];ResourceId=/subscriptions/[subscription ID]/resourceGroups/[resource group name]/providers/Microsoft.Sql/servers/[SQL Server name];Connection Timeout=30;"
    },
    "container" : { 
        "name" : "my-table" 
    },
    "identity" : { 
        "@odata.type": "#Microsoft.Azure.Search.DataUserAssignedIdentity",
        "userAssignedIdentity" : "/subscriptions/[subscription ID]/resourcegroups/[resource group name]/providers/Microsoft.ManagedIdentity/userAssignedIdentities/[managed identity name]"
    }
}   
```

## <a name="6---create-the-index"></a>6\. Creación del índice

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

## <a name="7---create-the-indexer"></a>7\. Creación del indexador

Un indizador conecta un origen de datos con un índice de búsqueda de destino y proporciona una programación para automatizar la actualización de datos.

Una vez creados el origen de datos y los índices, podrá crear el indexador:

Ejemplo de definición del indexador para un indexador de Azure SQL:

```
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sql-indexer",
    "dataSourceName" : "sql-datasource",
    "targetIndexName" : "my-target-index",
    "schedule" : { "interval" : "PT2H" }
}
```    

Este indizador se ejecutará cada dos horas (el intervalo de programación se establece en PT2H). Para ejecutar un indizador cada 30 minutos, establézcalo en PT30M. El intervalo más breve que se admite es de 5 minutos. La programación es opcional: si se omite, el indizador solo se ejecuta una vez cuando se crea. Sin embargo, puede ejecutarlo a petición en cualquier momento.   

Para más información sobre la API Create Indexer, consulte [Crear indexador](/rest/api/searchservice/create-indexer).

Para más información sobre cómo definir las programaciones del indexador, consulte [Programación de indexadores para Azure Cognitive Search](search-howto-schedule-indexers.md).

## <a name="troubleshooting"></a>Solución de problemas

Si recibe un error cuando el indexador intenta conectarse al origen de datos que indica que el cliente no tiene permiso para acceder al servidor, consulte los [errores comunes del indexador](./search-indexer-troubleshooting.md).

## <a name="see-also"></a>Consulte también

Más información acerca del indexar de Azure SQL:
* [Indexador de Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
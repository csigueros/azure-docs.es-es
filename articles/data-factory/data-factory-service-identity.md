---
title: Identidad administrada
titleSuffix: Azure Data Factory & Azure Synapse
description: Obtenga información sobre el uso de identidades administradas en Azure Data Factory y Azure Synapse.
author: nabhishek
ms.service: data-factory
ms.subservice: security
ms.topic: conceptual
ms.date: 07/19/2021
ms.author: abnarain
ms.custom: devx-track-azurepowershell, synapse
ms.openlocfilehash: ed7cd2836f601e412aca7f73c52f2a0e13173a8a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131011491"
---
# <a name="managed-identity-for-azure-data-factory-and-azure-synapse"></a>Identidad administrada para Azure Data Factory y Azure Synapse 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artículo ayuda a entender qué es la identidad administrada (anteriormente conocida como Managed Service Identity/MSI) y cómo funciona en Azure Data Factory y Azure Synapse.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Información general

Las identidades administradas eliminan la necesidad de administrar las credenciales. Proporcionan una identidad que usa la instancia del servicio al conectarse a recursos que admiten la autenticación de Azure Active Directory (Azure AD). Por ejemplo, el servicio puede usar una identidad administrada para acceder a recursos como [Azure Key Vault](../key-vault/general/overview.md), donde los administradores de datos pueden almacenar credenciales o acceder a cuentas de almacenamiento de manera segura. El servicio usa la identidad administrada para obtener tokens de Azure AD.

Hay dos tipos de identidades administradas compatibles: 

- **Asignadas por el sistema**: puede habilitar una identidad administrada directamente en una instancia de servicio. Cuando se permite una identidad administrada asignada por el sistema durante la creación del servicio, se crea una identidad en Azure AD asociada al ciclo de vida de esa instancia de servicio. Por diseño, solo ese recurso de Azure puede usar esta identidad para solicitar tokens de Azure AD. Por tanto, cuando se elimina el recurso, Azure elimina automáticamente la identidad. Azure Synapse Analytics requiere que se cree una identidad administrada asignada por el sistema junto con el área de trabajo de Synapse.
- **Asignada por el usuario:** también es posible crear una identidad administrada como recurso independiente de Azure. Puede [crear una identidad administrada asignada por el usuario](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) y asignarla a una o varias instancias de una factoría de datos o un área de trabajo de Synapse. En las identidades administradas asignadas por el usuario, la identidad se administra independientemente de los recursos que la utilicen.

La identidad administrada proporciona las siguientes ventajas:

- [Almacenamiento de credenciales en Azure Key Vault](store-credentials-in-key-vault.md), en cuyo caso la identidad administrada se usa para la autenticación de Azure Key Vault.
- Obtenga acceso a almacenes de datos o procesos mediante la autenticación de identidad administrada, como Azure Blob Storage, Azure Data Explorer, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database, Azure SQL Managed Instance, Azure Synapse Analytics, REST, actividad de Databricks, actividad web y mucho más. Consulte los artículos sobre el conector y las actividades para obtener más información.
- La identidad administrada asignada por el usuario también se usa para cifrar o descifrar los metadatos mediante la clave administrada por el cliente almacenada en Azure Key Vault, lo que brinda cifrado doble. 

## <a name="system-assigned-managed-identity"></a>Identidad administrada asignada por el sistema 

>[!NOTE]
> La identidad administrada asignada por el sistema también se conoce como "identidad administrada" en otra parte de la documentación y en la interfaz de usuario de Data Factory Studio y Synapse Studio por motivos de compatibilidad con versiones anteriores. Al hacer referencia a una "identidad administrada asignada por el usuario", lo mencionaremos de manera explícita. 

### <a name="generate-system-assigned-managed-identity"></a><a name="generate-managed-identity"></a> Generación de una identidad administrada asignada por el sistema

La identidad administrada por el sistema se genera de la manera siguiente:

- Cuando se crea una factoría de datos o un área de trabajo de Synapse mediante **Azure Portal o PowerShell**, la identidad administrada siempre se crea automáticamente.
- Cuando se crea una factoría de datos o un área de trabajo de Synapse mediante **SDK**, la identidad administrada se crea solo si se especifica "Identity = new FactoryIdentity()" en el objeto de factoría o "Identity = new ManagedIdentity" para la creación.  Vea el ejemplo que aparece en el [Inicio rápido de .NET: Crear una factoría de datos](quickstart-create-data-factory-dot-net.md#create-a-data-factory).
- Cuando se crea una factoría de datos o un área de trabajo de Synapse mediante la **API REST**, la identidad administrada solo se crea si se especifica la sección "identity" en el cuerpo de la solicitud. Vea el ejemplo que aparece en el [Inicio rápido de REST: Crear una factoría de datos](quickstart-create-data-factory-rest-api.md#create-a-data-factory).

Si observa que la instancia de servicio no tiene una identidad administrada asociada tras la instrucción [Recuperar identidad administrada](#retrieve-managed-identity), puede generar una de forma explícita si la actualiza con el iniciador de identidades mediante programación:

- [Generar identidad administrada con PowerShell](#generate-system-assigned-managed-identity-using-powershell)
- [Generar identidad administrada con API de REST](#generate-system-assigned-managed-identity-using-rest-api)
- [Generar identidad administrada con una plantilla de Azure Resource Manager](#generate-system-assigned-managed-identity-using-an-azure-resource-manager-template)
- [Generar identidad administrada con SDK](#generate-system-assigned-managed-identity-using-sdk)

>[!NOTE]
>
>- La identidad administrada no se puede modificar. La actualización de una instancia de servicio que ya tiene una identidad administrada no tiene ningún impacto, y la identidad administrada se mantiene sin cambios.
>- Si actualiza una instancia de servicio que ya tiene una identidad administrada sin especificar el parámetro "identity" en el objeto de factoría o área de trabajo o sin especificar la sección "identity" en el cuerpo de la solicitud de REST, se obtiene un error.
>- Cuando se elimina una instancia de servicio, se elimina también la identidad administrada asociada.

#### <a name="generate-system-assigned-managed-identity-using-powershell"></a>Generación de una identidad administrada asignada por el sistema mediante PowerShell

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

Llame al comando **Set-AzDataFactoryV2** y verá cómo se van agregando campos "Identity":

```powershell
PS C:\> Set-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName> -Location <region>

DataFactoryName   : ADFV2DemoFactory
DataFactoryId     : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory
ResourceGroupName : <resourceGroupName>
Location          : East US
Tags              : {}
Identity          : Microsoft.Azure.Management.DataFactory.Models.FactoryIdentity
ProvisioningState : Succeeded
```
# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

Llame al comando **New-AzSynapseWorkspace** y verá cómo los campos "Identity" se van generando:

```powershell
PS C:\> $creds = New-Object System.Management.Automation.PSCredential ("ContosoUser", $password)
PS C:\> New-AzSynapseWorkspace -ResourceGroupName <resourceGroupName> -Name <workspaceName> -Location <region> -DefaultDataLakeStorageAccountName <storageAccountName> -DefaultDataLakeStorageFileSystem <fileSystemName> -SqlAdministratorLoginCredential $creds

DefaultDataLakeStorage           : Microsoft.Azure.Commands.Synapse.Models.PSDataLakeStorageAccountDetails
ProvisioningState                : Succeeded
SqlAdministratorLogin            : ContosoUser
VirtualNetworkProfile            :
Identity                         : Microsoft.Azure.Commands.Synapse.Models.PSManagedIdentity
ManagedVirtualNetwork            :
PrivateEndpointConnections       : {}
WorkspaceUID                     : <workspaceUid>
ExtraProperties                  : {[WorkspaceType, Normal], [IsScopeEnabled, False]}
ManagedVirtualNetworkSettings    :
Encryption                       : Microsoft.Azure.Commands.Synapse.Models.PSEncryptionDetails
WorkspaceRepositoryConfiguration :
Tags                             :
TagsTable                        :
Location                         : <region>
Id                               : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/
                                   Microsoft.Synapse/workspaces/<workspaceName>
Name                             : <workspaceName>
Type                             : Microsoft.Synapse/workspaces
```
---

#### <a name="generate-system-assigned-managed-identity-using-rest-api"></a>Generación de una identidad administrada asignada por el sistema mediante la API REST

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

> [!NOTE]
> Si intenta actualizar una instancia de servicio que ya tiene una identidad administrada sin especificar el parámetro **identity** en el objeto de factoría o sin proporcionar una sección **identity** en el cuerpo de la solicitud de REST, se obtiene un error.

Llame a la API siguiente con la sección "identity" en el cuerpo de la solicitud:

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2018-06-01
```

**Cuerpo de la solicitud**: agregue "identity": { "type": "SystemAssigned" }.

```json
{
    "name": "<dataFactoryName>",
    "location": "<region>",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
```

**Respuesta**: la identidad administrada se crea automáticamente y la sección "identity" se rellena en consecuencia.

```json
{
    "name": "<dataFactoryName>",
    "tags": {},
    "properties": {
        "provisioningState": "Succeeded",
        "loggingStorageAccountKey": "**********",
        "createTime": "2017-09-26T04:10:01.1135678Z",
        "version": "2018-06-01"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "765ad4ab-XXXX-XXXX-XXXX-51ed985819dc",
        "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>",
    "type": "Microsoft.DataFactory/factories",
    "location": "<region>"
}
```
# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

> [!NOTE]
> Si intenta actualizar una instancia de servicio que ya tiene una identidad administrada sin especificar el parámetro **identity** en el objeto de área de trabajo o sin proporcionar una sección **identity** en el cuerpo de la solicitud de REST, se obtiene un error.

Llame a la API siguiente con la sección "identity" en el cuerpo de la solicitud:

```
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Synapse/workspaces/{workspaceName}?api-version=2018-06-01
```

**Cuerpo de la solicitud**: agregue "identity": { "type": "SystemAssigned" }.

```json
{
    "name": "<workspaceName>",
    "location": "<region>",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
```

**Respuesta**: la identidad administrada se crea automáticamente y la sección "identity" se rellena en consecuencia.

```json
{
    "name": "<workspaceName>",
    "tags": {},
    "properties": {
        "provisioningState": "Succeeded",
        "loggingStorageAccountKey": "**********",
        "createTime": "2021-09-26T04:10:01.1135678Z",
        "version": "2018-06-01"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "765ad4ab-XXXX-XXXX-XXXX-51ed985819dc",
        "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Synapse/workspaces/<workspaceName>",
    "type": "Microsoft.Synapse/workspaces",
    "location": "<region>"
}
```
---

#### <a name="generate-system-assigned-managed-identity-using-an-azure-resource-manager-template"></a>Generación de una identidad administrada asignada por el sistema mediante una plantilla de Azure Resource Manager

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)
**Plantilla**: agregue "identity": { "type": "SystemAssigned" }.

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "resources": [{
        "name": "<dataFactoryName>",
        "apiVersion": "2018-06-01",
        "type": "Microsoft.DataFactory/factories",
        "location": "<region>",
        "identity": {
            "type": "SystemAssigned"
        }
    }]
}
```
# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)
**Plantilla**: agregue "identity": { "type": "SystemAssigned" }.

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "resources": [{
        "name": "<workspaceName>",
        "apiVersion": "2018-06-01",
        "type": "Microsoft.Synapse/workspaces",
        "location": "<region>",
        "identity": {
            "type": "SystemAssigned"
        }
    }]
}
```
---

#### <a name="generate-system-assigned-managed-identity-using-sdk"></a>Generación de una identidad administrada asignada por el sistema mediante el SDK

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)
Llame a la función create_or_update con Identity=new FactoryIdentity(). Código de ejemplo mediante .NET:

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```
# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)
```csharp
Workspace workspace = new Workspace
{
    Identity = new ManagedIdentity
    {
        Type = ResourceIdentityType.SystemAssigned
    },
    DefaultDataLakeStorage = new DataLakeStorageAccountDetails
    {
        AccountUrl = <defaultDataLakeStorageAccountUrl>,
        Filesystem = <DefaultDataLakeStorageFilesystem>
    },
    SqlAdministratorLogin = <SqlAdministratorLoginCredentialUserName>
    SqlAdministratorLoginPassword = <SqlAdministratorLoginCredentialPassword>,
    Location = <region>
};
client.Workspaces.CreateOrUpdate(resourceGroupName, workspaceName, workspace);
```
---

### <a name="retrieve-system-assigned-managed-identity"></a><a name="retrieve-managed-identity"></a> Recuperación de una identidad administrada asignada por el sistema

Puede recuperar la identidad administrada desde Azure Portal o mediante programación. Las secciones siguientes le muestran algunos ejemplos.

>[!TIP]
> Si no ve la identidad administrada, [genérela](#generate-managed-identity) mediante la actualización de la instancia de servicio.

#### <a name="retrieve-system-assigned-managed-identity-using-azure-portal"></a>Recuperación de una identidad administrada asignada por el sistema mediante Azure Portal

Puede encontrar la información de la identidad administrada en Azure Portal -> su factoría de datos -o área de trabajo de Synapse -> Propiedades.

- Id. del objeto de identidad administrada
- Inquilino de identidad administrada (solo aplicable para Azure Data Factory)

La información de identidad administrada también se mostrará cuando se crea un servicio vinculado que admita la autenticación de identidad administrada, como Azure Blob, Azure Data Lake Storage, Azure Key Vault, etc.

Al conceder el permiso, en la pestaña Access Control de recursos de Azure (IAM) -> Agregar asignación de roles -> Asignar acceso a -> seleccione Data Factory en identidad administrada asignada por el sistema -> seleccione en función del nombre de factoría; o, en general, puede usar el id. de objeto o el nombre de la factoría de datos (como nombre de identidad administrada) para encontrar esta identidad. Si necesita obtener el id. de la aplicación de la identidad administrada, puede usar PowerShell.

#### <a name="retrieve-system-assigned-managed-identity-using-powershell"></a>Recuperación de una identidad administrada asignada por el sistema mediante PowerShell

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)
Al obtener una instancia de servicio específica, se devuelven el identificador de identidad de seguridad y el de inquilino de la identidad administrada del modo siguiente. Use el valor de **PrincipalId** para conceder acceso:

```powershell
PS C:\> (Get-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

Para obtener el identificador de aplicación, copie el identificador de entidad de seguridad y ejecute el comando de Azure Active Directory siguiente con el identificador de entidad de seguridad como parámetro.

```powershell
PS C:\> Get-AzADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```
# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)
Al obtener una instancia de servicio específica, se devuelven el identificador de identidad de seguridad y el de inquilino de la identidad administrada del modo siguiente. Use el valor de **PrincipalId** para conceder acceso:

```powershell
PS C:\> (Get-AzSynapseWorkspace -ResourceGroupName <resourceGroupName> -Name <workspaceName>).Identity

IdentityType   PrincipalId                          TenantId                            
------------   -----------                          --------                            
SystemAssigned cadadb30-XXXX-XXXX-XXXX-ef3500e2ff05 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

Para obtener el identificador de aplicación, copie el identificador de entidad de seguridad y ejecute el comando de Azure Active Directory siguiente con el identificador de entidad de seguridad como parámetro.

```powershell
PS C:\> Get-AzADServicePrincipal -ObjectId cadadb30-XXXX-XXXX-XXXX-ef3500e2ff05

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : <workspaceName>
Id                    : cadadb30-XXXX-XXXX-XXXX-ef3500e2ff05
Type                  : ServicePrincipal
```
---

#### <a name="retrieve-managed-identity-using-rest-api"></a>Recuperación de identidad administrada mediante la API de REST

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)
Al obtener una instancia de servicio específica, se devuelven el identificador de identidad de seguridad y el de inquilino de la identidad administrada del modo siguiente.

Llame a la siguiente API en la solicitud:

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}?api-version=2018-06-01
```

**Respuesta**: Obtendrá una respuesta como la que se muestra en el siguiente ejemplo. La sección "identity" se rellena en consecuencia.

```json
{
    "name":"<dataFactoryName>",
    "identity":{
        "type":"SystemAssigned",
        "principalId":"554cff9e-XXXX-XXXX-XXXX-90c7d9ff2ead",
        "tenantId":"72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>",
    "type":"Microsoft.DataFactory/factories",
    "properties":{
        "provisioningState":"Succeeded",
        "createTime":"2020-02-12T02:22:50.2384387Z",
        "version":"2018-06-01",
        "factoryStatistics":{
            "totalResourceCount":0,
            "maxAllowedResourceCount":0,
            "factorySizeInGbUnits":0,
            "maxAllowedFactorySizeInGbUnits":0
        }
    },
    "eTag":"\"03006b40-XXXX-XXXX-XXXX-5e43617a0000\"",
    "location":"<region>",
    "tags":{

    }
}
```

> [!TIP] 
> Para recuperar la identidad administrada de una plantilla de ARM, agregue una sección de **salidas** en el código JSON de ARM:

```json
{
    "outputs":{
        "managedIdentityObjectId":{
            "type":"string",
            "value":"[reference(resourceId('Microsoft.DataFactory/factories', parameters('<dataFactoryName>')), '2018-06-01', 'Full').identity.principalId]"
        }
    }
}
```
# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)
Al obtener una instancia de servicio específica, se devuelven el identificador de identidad de seguridad y el de inquilino de la identidad administrada del modo siguiente.

Llame a la siguiente API en la solicitud:

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Synapse/workspaces/{workspaceName}?api-version=2018-06-01
```

**Respuesta**: Obtendrá una respuesta como la que se muestra en el siguiente ejemplo. La sección "identity" se rellena en consecuencia.

```json
{
  "properties": {
    "defaultDataLakeStorage": {
      "accountUrl": "https://exampledatalakeaccount.dfs.core.windows.net",
      "filesystem": "examplefilesystem"
    },
    "encryption": {
      "doubleEncryptionEnabled": false
    },
    "provisioningState": "Succeeded",
    "connectivityEndpoints": {
      "web": "https://web.azuresynapse.net?workspace=%2fsubscriptions%2{subscriptionId}%2fresourceGroups%2f{resourceGroupName}%2fproviders%2fMicrosoft.Synapse%2fworkspaces%2f{workspaceName}",
      "dev": "https://{workspaceName}.dev.azuresynapse.net",
      "sqlOnDemand": "{workspaceName}-ondemand.sql.azuresynapse.net",
      "sql": "{workspaceName}.sql.azuresynapse.net"
    },
    "managedResourceGroupName": "synapseworkspace-managedrg-f77f7cf2-XXXX-XXXX-XXXX-c4cb7ac3cf4f",
    "sqlAdministratorLogin": "sqladminuser",
    "privateEndpointConnections": [],
    "workspaceUID": "e56f5773-XXXX-XXXX-XXXX-a0dc107af9ea",
    "extraProperties": {
      "WorkspaceType": "Normal",
      "IsScopeEnabled": false
    },
    "publicNetworkAccess": "Enabled",
    "cspWorkspaceAdminProperties": {
      "initialWorkspaceAdminObjectId": "3746a407-XXXX-XXXX-XXXX-842b6cf1fbcc"
    },
    "trustedServiceBypassEnabled": false
  },
  "type": "Microsoft.Synapse/workspaces",
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Synapse/workspaces/{workspaceName}",
  "location": "eastus",
  "name": "{workspaceName}",
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47",
    "principalId": "cadadb30-XXXX-XXXX-XXXX-ef3500e2ff05"
  },
  "tags": {}
}
```

> [!TIP] 
> Para recuperar la identidad administrada de una plantilla de ARM, agregue una sección de **salidas** en el código JSON de ARM:

```json
{
    "outputs":{
        "managedIdentityObjectId":{
            "type":"string",
            "value":"[reference(resourceId('Microsoft.Synapse/workspaces', parameters('<workspaceName>')), '2018-06-01', 'Full').identity.principalId]"
        }
    }
}
```
---

## <a name="user-assigned-managed-identity"></a>Identidad administrada asignada por el usuario

Puede crear, eliminar y administrar identidades administradas asignadas por el usuario en Azure Active Directory. Para más información, consulte [Creación, enumeración, eliminación o asignación de un rol a una identidad administrada asignada por el usuario mediante Azure Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). 

Para usar una identidad administrada asignada por el usuario, primero debe [crear credenciales](credentials.md) en la instancia de servicio para UAMI.

## <a name="next-steps"></a>Pasos siguientes

- [Creación de credenciales](credentials.md).

Consulte los siguientes temas que presentan cuándo y cómo usar la identidad administrada:

- [Almacenamiento de credenciales en Azure Key Vault](store-credentials-in-key-vault.md).
- [Copia de datos con Azure Data Lake Storage Gen1 como origen o destino mediante Azure Data Factory](connector-azure-data-lake-store.md)

Vea [¿Qué son las identidades administradas de recursos de Azure?](../active-directory/managed-identities-azure-resources/overview.md) para obtener más información sobre las identidades administradas para recursos de Azure, en las que se basa la identidad administrada de Data Factory. y Azure Synapse.

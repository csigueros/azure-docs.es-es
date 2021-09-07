---
title: Identidad administrada de Data Factory
description: Obtenga información sobre la identidad administrada de Azure Data Factory.
author: nabhishek
ms.service: data-factory
ms.subservice: security
ms.topic: conceptual
ms.date: 07/19/2021
ms.author: abnarain
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a350553659ea6028e3fb2079f790d14ae1653a86
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121746567"
---
# <a name="managed-identity-for-data-factory"></a>Identidad administrada de Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

En este artículo podrá entender qué es la identidad administrada de Data Factory (anteriormente conocida como Managed Service Identity/MSI) y cómo funciona.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Información general

Las identidades administradas en factorías de datos eliminan la necesidad de que los ingenieros de datos administren las credenciales. Proporcionan una identidad que usa la instancia de Data Factory al conectarse a recursos que admiten la autenticación de Azure Active Directory (Azure AD). Por ejemplo, Data Factory puede usar una identidad administrada para acceder a recursos como [Azure Key Vault](../key-vault/general/overview.md), donde los administradores de datos pueden almacenar credenciales o acceder a cuentas de almacenamiento de manera segura. Data Factory usa la identidad administrada para obtener tokens de Azure AD.

Hay dos tipos de identidades administradas compatibles con Data Factory: 

- **Asignada por el sistema:** Data Factory le permite habilitar una identidad administrada directamente en una instancia de servicio. Cuando se permite una identidad administrada asignada por el sistema durante la creación de la factoría de datos, se crea una identidad en Azure AD asociada al ciclo de vida de esa instancia de servicio. Por diseño, solo ese recurso de Azure puede usar esta identidad para solicitar tokens de Azure AD. Por tanto, cuando se elimina el recurso, Azure elimina automáticamente la identidad.
- **Asignada por el usuario:** también es posible crear una identidad administrada como recurso independiente de Azure. Puede [crear una identidad administrada asignada por el usuario](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) y asignarla a una o varias instancias de una factoría de datos. En las identidades administradas asignadas por el usuario, la identidad se administra independientemente de los recursos que la utilicen.



La identidad administrada para Data Factory proporciona las ventajas siguientes:

- [Almacenamiento de credenciales en Azure Key Vault](store-credentials-in-key-vault.md), en cuyo caso la identidad administrada de Data Factory se usa para la autenticación de Azure Key Vault.
- Obtenga acceso a almacenes de datos o procesos mediante la autenticación de identidad administrada, como Azure Blob Storage, Azure Data Explorer, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database, Azure SQL Managed Instance, Azure Synapse Analytics, REST, actividad de Databricks, actividad web y mucho más. Consulte los artículos sobre el conector y las actividades para obtener más información.
- La identidad administrada asignada por el usuario también se usa para cifrar o descifrar los metadatos de la factoría de datos mediante la clave administrada por el cliente almacenada en Azure Key Vault, lo que brinda cifrado doble. 

## <a name="system-assigned-managed-identity"></a>Identidad administrada asignada por el sistema 

>[!NOTE]
> La identidad administrada asignada por el sistema también se conoce como "identidad administrada" en la documentación de Data Factory y en la interfaz de usuario de Data Factory con fines de compatibilidad con versiones anteriores. Al hacer referencia a una "identidad administrada asignada por el usuario", lo mencionaremos de manera explícita. 

#### <a name="generate-system-assigned-managed-identity"></a><a name="generate-managed-identity"></a> Generación de una identidad administrada asignada por el sistema

La identidad administrada por el sistema para Data Factory se genera de la manera siguiente:

- Cuando se crea una factoría de datos mediante **Azure Portal o PowerShell**, la identidad administrada siempre se crea automáticamente.
- Cuando se crea una factoría de datos mediante **SDK**, la identidad administrada se crea solo si se especifica "Identity = new FactoryIdentity()" en el objeto de factoría para la creación. Vea el ejemplo que aparece en el [Inicio rápido de .NET: Crear una factoría de datos](quickstart-create-data-factory-dot-net.md#create-a-data-factory).
- Cuando se crea una factoría de datos mediante **API de REST**, la identidad administrada solo se crea si se especifica la sección "identity" en el cuerpo de la solicitud. Vea el ejemplo que aparece en el [Inicio rápido de REST: Crear una factoría de datos](quickstart-create-data-factory-rest-api.md#create-a-data-factory).

Si observa que la factoría de datos no tiene una identidad administrada asociada tras la instrucción [Recuperar identidad administrada](#retrieve-managed-identity), puede generar una de forma explícita si actualiza la factoría de datos con el iniciador de identidades mediante programación:

- [Generar identidad administrada con PowerShell](#generate-system-assigned-managed-identity-using-powershell)
- [Generar identidad administrada con API de REST](#generate-system-assigned-managed-identity-using-rest-api)
- [Generar identidad administrada con una plantilla de Azure Resource Manager](#generate-system-assigned-managed-identity-using-an-azure-resource-manager-template)
- [Generar identidad administrada con SDK](#generate-system-assigned-managed-identity-using-sdk)

>[!NOTE]
>
>- La identidad administrada no se puede modificar. La actualización de una factoría de datos que ya tiene una identidad administrada no tiene ningún impacto, la identidad administrada se mantiene sin cambios.
>- Si actualiza una factoría de datos que ya tiene una identidad administrada sin especificar el parámetro "identity" en el objeto de factoría o sin especificar la sección "identity" en el cuerpo de la solicitud de REST, se obtiene un error.
>- Cuando se elimina una factoría de datos, se elimina también la identidad administrada asociada.

##### <a name="generate-system-assigned-managed-identity-using-powershell"></a>Generación de una identidad administrada asignada por el sistema mediante PowerShell

Llame al comando **Set-AzDataFactoryV2** y verá cómo se van agregando campos "Identity":

```powershell
PS C:\WINDOWS\system32> Set-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName> -Location <region>

DataFactoryName   : ADFV2DemoFactory
DataFactoryId     : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory
ResourceGroupName : <resourceGroupName>
Location          : East US
Tags              : {}
Identity          : Microsoft.Azure.Management.DataFactory.Models.FactoryIdentity
ProvisioningState : Succeeded
```

##### <a name="generate-system-assigned-managed-identity-using-rest-api"></a>Generación de una identidad administrada asignada por el sistema mediante la API REST

Llame a la siguiente API con la sección "identity" en el cuerpo de la solicitud:

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
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory",
    "type": "Microsoft.DataFactory/factories",
    "location": "<region>"
}
```

##### <a name="generate-system-assigned-managed-identity-using-an-azure-resource-manager-template"></a>Generación de una identidad administrada asignada por el sistema mediante una plantilla de Azure Resource Manager

**Plantilla**: agregue "identity": { "type": "SystemAssigned" }.

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

##### <a name="generate-system-assigned-managed-identity-using-sdk"></a>Generación de una identidad administrada asignada por el sistema mediante el SDK

Llame a la función create_or_update de la factoría de datos con Identity=new FactoryIdentity(). Código de ejemplo mediante .NET:

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

#### <a name="retrieve-system-assigned-managed-identity"></a><a name="retrieve-managed-identity"></a> Recuperación de una identidad administrada asignada por el sistema

Puede recuperar la identidad administrada desde Azure Portal o mediante programación. Las secciones siguientes le muestran algunos ejemplos.

>[!TIP]
> Si no ve la identidad administrada, [genérela](#generate-managed-identity) mediante la actualización de la factoría.

#### <a name="retrieve-system-assigned-managed-identity-using-azure-portal"></a>Recuperación de una identidad administrada asignada por el sistema mediante Azure Portal

Puede encontrar la información de la identidad administrada en Azure Portal -> su factoría de datos -> Propiedades.

- Id. del objeto de identidad administrada
- Inquilino de identidad administrada

La información de identidad administrada también se mostrará cuando se crea un servicio vinculado que admita la autenticación de identidad administrada, como Azure Blob, Azure Data Lake Storage, Azure Key Vault, etc.

Al conceder el permiso, en la pestaña Access Control de recursos de Azure (IAM) -> Agregar asignación de roles -> Asignar acceso a -> seleccione Data Factory en identidad administrada asignada por el sistema -> seleccione en función del nombre de factoría; o, en general, puede usar el id. de objeto o el nombre de la factoría de datos (como nombre de identidad administrada) para encontrar esta identidad. Si necesita obtener el id. de la aplicación de la identidad administrada, puede usar PowerShell.

#### <a name="retrieve-system-assigned-managed-identity-using-powershell"></a>Recuperación de una identidad administrada asignada por el sistema mediante PowerShell

Al obtener una factoría de datos específicas, se devuelven el identificador de identidad de seguridad y el de inquilino de la identidad administrada del modo siguiente. Use el valor de **PrincipalId** para conceder acceso:

```powershell
PS C:\WINDOWS\system32> (Get-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

Para obtener el identificador de aplicación, copie el identificador de entidad de seguridad y ejecute el comando de Azure Active Directory siguiente con el identificador de entidad de seguridad como parámetro.

```powershell
PS C:\WINDOWS\system32> Get-AzADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

#### <a name="retrieve-managed-identity-using-rest-api"></a>Recuperación de identidad administrada mediante la API de REST

Al obtener una factoría de datos específicas, se devuelven el identificador de identidad de seguridad y el de inquilino de la identidad administrada del modo siguiente.

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

## <a name="user-assigned-managed-identity"></a>Identidad administrada asignada por el usuario

Puede crear, eliminar y administrar identidades administradas asignadas por el usuario en Azure Active Directory. Para más información, consulte la documentación sobre [Creación, enumeración, eliminación o asignación de un rol a una identidad administrada asignada por el usuario mediante Azure Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). 

### <a name="credentials"></a>Credenciales

Presentamos Credenciales, que pueden contener identidades administradas asignadas por el usuario, entidades de servicio y también enumerar la identidad administrada asignada por el sistema que puede usar en los servicios vinculados que admiten la autenticación de Azure Active Directory (AAD). Le ayudan a consolidar y administrar todas las credenciales basadas en AAD.  

A continuación se muestran los pasos genéricos para usar una **identidad administrada asignada por el usuario** en los servicios vinculados para la autenticación. 

1. Asocie una identidad administrada asignada por el usuario a la instancia de Data Factory mediante Azure Portal, el SDK, PowerShell, la API REST. 
   En la captura de pantalla siguiente se usa Azure Portal (hoja Data Factory) para asociar la identidad administrada asignada por el usuario. 

   :::image type="content" source="media/managed-identities/uami-azure-portal.jpg" alt-text="Captura de pantalla que muestra cómo usar Azure Portal para asociar una identidad administrada asignada por el usuario." lightbox="media/managed-identities/uami-azure-portal.jpg":::

2. Cree una "Credencial" en la interfaz de usuario de Data Factory de forma interactiva. Puede seleccionar la identidad administrada asignada por el usuario asociada a Data Factory en el paso 1. 

   :::image type="content" source="media/managed-identities/credential-adf-ui-create-new-1.png" alt-text="Captura de pantalla que muestra el primer paso para crear nuevas credenciales." lightbox="media/managed-identities/credential-adf-ui-create-new-1.png":::

   :::image type="content" source="media/managed-identities/credential-adf-ui-create-new-2a.png" alt-text="Captura de pantalla que muestra el segundo paso para crear nuevas credenciales." lightbox="media/managed-identities/credential-adf-ui-create-new-2a.png":::

3. Cree un nuevo servicio vinculado y seleccione "Identidad administrada asignada por el usuario" en autenticación.

   :::image type="content" source="media/managed-identities/credential-adf-ui-create-new-linked-service.png" alt-text="Captura de pantalla que muestra el nuevo servicio vinculado con la autenticación de identidad administrada asignada por el usuario." lightbox="media/managed-identities/credential-adf-ui-create-new-linked-service.png":::

> [!NOTE] 
> Puede usar SDK, PowerShell o la API REST para las acciones anteriores.

## <a name="next-steps"></a>Pasos siguientes

Consulte los siguientes temas que presentan cuándo y cómo usar la identidad administrada de la factoría de datos:

- [Almacenamiento de credenciales en Azure Key Vault](store-credentials-in-key-vault.md)
- [Copia de datos con Azure Data Lake Storage Gen1 como origen o destino mediante Azure Data Factory](connector-azure-data-lake-store.md)

Vea [¿Qué es Managed Identities for Azure Resources?](../active-directory/managed-identities-azure-resources/overview.md) para obtener más información sobre las identidades administradas para recursos de Azure en las que se basa la identidad administrada de Data Factory.

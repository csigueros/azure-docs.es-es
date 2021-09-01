---
title: Recuperación de recursos de Cognitive Services eliminados
titleSuffix: Azure Cognitive Services
description: En este artículo se proporcionan instrucciones sobre cómo recuperar un recurso de Cognitive Services eliminado.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/02/2021
ms.author: nitinme
ms.openlocfilehash: 90071be491fa16d483d1348feabb7a1180c333e8
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114297417"
---
# <a name="recover-deleted-cognitive-services-resources"></a>Recuperación de recursos de Cognitive Services eliminados

En este artículo se proporcionan instrucciones sobre cómo recuperar un recurso de Cognitive Services que se ha eliminado. En el artículo también se proporcionan las instrucciones necesarias para purgar un recurso eliminado.

> [!NOTE]
> Las instrucciones de este artículo se pueden aplicar tanto a recursos de varios servicios como recursos de un solo servicio. Los recursos de varios servicios permiten el acceso a varios servicios cognitivos mediante una única clave y un punto de conexión. Por otro lado, los recursos de un solo servicio permiten el acceso solo a ese servicio cognitivo concreto para el que se creó el recurso.

## <a name="prerequisites"></a>Requisitos previos

* El recurso que se va a recuperar debe haberse eliminado en las últimas 48 horas.
* El recurso que se va a recuperar no se debe haber purgado aún. Los recursos purgados no se pueden recuperar.
* Antes de intentar recuperar un recurso eliminado, asegúrese de que existe el grupo de recursos de esa cuenta. Si se ha eliminado, debe volver a crearlo. No es posible recuperar un grupo de recursos. Para más información, consulte  [Administración de grupos de recursos](../azure-resource-manager/management/manage-resource-groups-portal.md).
* Si el recurso eliminado ha usado claves administradas por el cliente con Azure Key Vault y también se ha eliminado el almacén de claves, debe restaurarlo antes de restaurar la cuenta de almacenamiento. Para más información, consulte [Administración de la recuperación en Azure Key Vault](../key-vault/general/key-vault-recovery.md).
* Si el recurso eliminado ha usado un almacenamiento administrado por el cliente y la cuenta de almacenamiento también se ha eliminado, debe restaurarla antes de restaurar el recurso de Cognitive Services. Para obtener instrucciones, consulte [Recuperación de una cuenta de almacenamiento eliminada](../storage/common/storage-account-recover.md).

La suscripción debe tener permisos de `Microsoft.CognitiveServices/locations/resourceGroups/deletedAccounts/delete` para purgar recursos, como [Colaborador de Cognitive Services](../role-based-access-control/built-in-roles.md#cognitive-services-contributor) o [Colaborador](../role-based-access-control/built-in-roles.md#contributor). 

## <a name="recover-a-deleted-resource"></a>Recuperación de un secreto eliminado 

Para recuperar un recurso de Cognitive Services eliminado, use los siguientes comandos. Si procede, reemplace:

* `{subscriptionID}` por el identificador de su suscripción de Azure.
* `{resourceGroup}` por su grupo de recursos.
* `{resourceName}` por su nombre de recurso.
* `{location}` por la ubicación del recurso.

### <a name="using-the-rest-api"></a>Mediante la API de REST

Use el siguiente comando `PUT`:

```rest-api
https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroup}/providers/Microsoft.CognitiveServices/accounts/{resourceName}?Api-Version=2021-04-30
```

En el cuerpo de la solicitud, use el siguiente formato JSON:

```json
{ 
  "location": "{location}", 
   "properties": { 
        "restore": true 
    } 
} 
```

### <a name="using-powershell"></a>Usar PowerShell

Use el siguiente comando para restaura el recurso: 

```powershell
New-AzResource -Location {location}-Properties @{restore=$true} -ResourceId /subscriptions/{subscriptionID}/resourceGroups/{resourceGroup}/providers/Microsoft.CognitiveServices/accounts/{resourceName}   -ApiVersion 2021-04-30 
```

Si necesita encontrar el nombre de los recursos eliminados, puede obtener una lista de nombres de recursos eliminados con el siguiente comando: 

```powershell
Get-AzResource -ResourceId /subscriptions/{subscriptionId}/providers/Microsoft.CognitiveServices/deletedAccounts -ApiVersion 2021-04-30 
```

### <a name="using-the-azure-cli"></a>Uso de la CLI de Azure

```azurecli-interactive
az resource create --subscription {subscriptionID} -g {resourceGroup} -n {resourceName} --location {location} --namespace Microsoft.CognitiveServices --resource-type accounts --properties "{\"restore\": true}"
```

## <a name="purge-a-deleted-resource"></a>Purga de un recurso eliminado 

Una vez que elimine un recurso, no podrá crear otro con el mismo nombre en 48 horas. Para crear un recurso con el mismo nombre, deberá purgar el recurso eliminado.

Para purgar un recurso de Cognitive Services eliminado, use los siguientes comandos. Si procede, reemplace:

* `{subscriptionID}` por el identificador de su suscripción de Azure.
* `{resourceGroup}` por su grupo de recursos.
* `{resourceName}` por su nombre de recurso.
* `{location}` por la ubicación del recurso.

> [!NOTE]
> Una vez que se purga un recurso, se elimina permanentemente y no se puede restaurar. Perderá todos los datos y claves asociados al recurso.

### <a name="using-the-rest-api"></a>Mediante la API de REST

Use el siguiente comando `DELETE`:

```rest-api
https://management.azure.com/subscriptions/{subscriptionID}/providers/Microsoft.CognitiveServices/locations/{location}/resourceGroups/{resourceGroup}/deletedAccounts/{resourceName}?Api-Version=2021-04-30`
```

### <a name="using-powershell"></a>Usar PowerShell

```powershell
Remove-AzResource -ResourceId /subscriptions/{subscriptionID}/providers/Microsoft.CognitiveServices/locations/{location}/resourceGroups/{resourceGroup}/deletedAccounts/{resourceName}  -ApiVersion 2021-04-30`
```

### <a name="using-the-azure-cli"></a>Uso de la CLI de Azure

```azurecli-interactive
az resource delete --ids /subscriptions/{subscriptionId}/providers/Microsoft.CognitiveServices/locations/{location}/resourceGroups/{resourceGroup}/deletedAccounts/{resourceName}
```

## <a name="see-also"></a>Vea también
* [Creación de un recurso mediante Azure Portal](cognitive-services-apis-create-account.md)
* [Creación de un recurso mediante la CLI de Azure](cognitive-services-apis-create-account-cli.md)
* [Creación de un recurso mediante la biblioteca cliente](cognitive-services-apis-create-account-client-library.md)
* [Creación de un recurso mediante una plantilla de ARM](create-account-resource-manager-template.md)
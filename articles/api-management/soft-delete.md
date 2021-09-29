---
title: Eliminación temporal de Azure API Management (versión preliminar) | Microsoft Docs
description: La eliminación temporal permite recuperar instancias de API Management eliminadas.
ms.service: api-management
ms.topic: conceptual
author: dlepow
ms.author: danlep
ms.date: 11/27/2020
ms.openlocfilehash: 0cf99dfe2fb697b0a28db1fbacecaa2e2021a9d4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128622025"
---
# <a name="api-management-soft-delete-preview"></a>Eliminación temporal de API Management (versión preliminar)

Con la eliminación temporal de API Management (versión preliminar), puede recuperar y restaurar las instancias de API Management (APIM) eliminadas recientemente.

> [!IMPORTANT]
> Solo las instancias de API Management eliminadas con `2020-06-01-preview` y versiones posteriores de la API se eliminarán temporalmente y son recuperables mediante los pasos descritos en este artículo. Las instancias de APIM eliminadas con versiones anteriores de la API se seguirán eliminando de forma permanente. Azure PowerShell y la CLI de Azure no usan actualmente la versión `2020-06-01-preview` y también darán lugar a un comportamiento de eliminación permanente.

## <a name="supporting-interfaces"></a>Interfaces admitidas

La característica de eliminación temporal está disponible a través de una [API REST](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/restore).

> [!TIP]
> Consulte [Referencia de la API REST de Azure](/rest/api/azure/) para recibir sugerencias y obtener herramientas para llamar a las API REST de Azure.

| Operación | Descripción | Espacio de nombres de API Management | Versión mínima de la API |
|--|--|--|--|
| [Create or Update](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/createorupdate) (Crear o actualizar) | Crea o actualiza un servicio API Management.  | Servicio API Management | Any |
| [Create or Update](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/createorupdate) (Crear o actualizar) con la propiedad `restore` establecida en **true** | Recupera el servicio API Management si antes se eliminó temporalmente. Si se especifica `restore` y se establece en `true` se omitirán todas las demás propiedades.  | Servicio API Management |  2020-06-01-preview |
| [Eliminar](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/delete) | Elimina un servicio API Management existente. | Servicio API Management | 2020-06-01-preview|
| [Get By Name](/rest/api/apimanagement/2020-06-01-preview/deletedservices/getbyname) (Obtener por nombre) | Obtiene el servicio API Management eliminado temporalmente por el nombre. | Servicios eliminados | 2020-06-01-preview |
| [List By Subscription](/rest/api/apimanagement/2020-06-01-preview/deletedservices/listbysubscription) (Enumerar por suscripción) | Enumera todos los servicios eliminados temporalmente que están disponibles para recuperarse en la suscripción especificada. | Servicios eliminados | 2020-06-01-preview
| [Purgar](/rest/api/apimanagement/2020-06-01-preview/deletedservices/purge) | Purga el servicio API Management (lo elimina sin ninguna opción para recuperarlo). | Servicios eliminados | 2020-06-01-preview

## <a name="soft-delete-behavior"></a>Comportamiento de eliminación temporal

Puede usar cualquier versión de API para crear la instancia de API Management, pero tendrá que utilizar `2020-06-01-preview` o versiones posteriores para eliminar temporalmente la instancia de APIM (y tener la opción de recuperarla).

Tras eliminar una instancia de API Management, el servicio existirá en estado eliminado, lo que hará que sea inaccesible para cualquier operación de APIM. En este estado, la instancia de APIM solo se puede enumerar, recuperar o purgar (eliminar de forma permanente).

Al mismo tiempo, Azure programará la eliminación de los datos subyacentes correspondientes a la instancia de APIM para que se lleve a cabo después del intervalo de retención predeterminado (48 horas). El registro DNS correspondiente a la instancia también se conserva mientras dura el intervalo de retención. No se puede volver a usar el nombre de una instancia de API Management que se haya eliminado temporalmente hasta que haya transcurrido el período de retención.

Si la instancia de APIM no se recupera en 48 horas, se eliminará de forma permanente (irrecuperable). También puede optar por [purgar](#purge-a-soft-deleted-apim-instance) (eliminar de forma permanente) la instancia de APIM y renunciar al período de retención de eliminación temporal.

## <a name="list-deleted-apim-instances"></a>Enumeración de las instancias de APIM eliminadas

Puede comprobar que una instancia de APIM eliminada temporalmente está disponible para restaurarse (recuperarse) mediante las operaciones [Get By Name](/rest/api/apimanagement/2020-06-01-preview/deletedservices/getbyname) (Obtener por nombre) o [List By Subscription](/rest/api/apimanagement/2020-06-01-preview/deletedservices/listbysubscription) (Enumerar por suscripción) de los servicios eliminados.

### <a name="get-a-soft-deleted-instance-by-name"></a>Obtención de una instancia eliminada temporalmente por nombre

Use la operación [Get By Name](/rest/api/apimanagement/2020-06-01-preview/deletedservices/getbyname) (Obtener por nombre) de API Management, pero sustituya `{subscriptionId}`, `{location}` y `{serviceName}` por la suscripción de Azure, la ubicación del recurso y el nombre de la instancia de API Management:

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.ApiManagement/locations/{location}/deletedservices/{serviceName}?api-version=2020-06-01-preview
```

Si está disponible para la recuperación, Azure devolverá un registro de la instancia de APIM que muestra su valor de `deletionDate` y `scheduledPurgeDate`, por ejemplo:

```json
{
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ApiManagement/locations/southcentralus/deletedservices/apimtest",
    "name": "apimtest",
    "type": "Microsoft.ApiManagement/deletedservices",
    "location": "South Central US",
    "properties": {
        "serviceId": "/subscriptions/########-####-####-####-############/resourceGroups/apimtestgroup/providers/Microsoft.ApiManagement/service/apimtest",
        "scheduledPurgeDate": "2020-11-26T19:40:26.3596893Z",
        "deletionDate": "2020-11-24T19:40:50.1013572Z"
    }
}
```

### <a name="list-all-soft-deleted-instances-for-a-given-subscription"></a>Enumeración de todas las instancias eliminadas temporalmente de una suscripción determinada

Use la operación [List By Subscription](/rest/api/apimanagement/2020-06-01-preview/deletedservices/listbysubscription) (Enumerar por suscripción) de API Management, pero sustituya `{subscriptionId}` por su identificador de suscripción:

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.ApiManagement/deletedservices?api-version=2020-06-01-preview
```

Se devolverá una lista de todos los servicios eliminados temporalmente que están disponibles para recuperarse en la suscripción dada, donde se muestran los valores de `deletionDate` y `scheduledPurgeDate` para cada uno.

## <a name="recover-a-deleted-apim-instance"></a>Recuperación de una instancia de APIM eliminada

Use la operación [Create Or Update](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/createorupdate) (Crear o actualizar) de API Management, pero sustituya `{subscriptionId}`, `{resourceGroup}` y `{apimServiceName}` por la suscripción de Azure, el nombre del grupo de recursos y el nombre de API Management:

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.ApiManagement/service/{apimServiceName}?api-version=2020-06-01-preview
```

. . . y establezca la propiedad `restore` en `true` en el cuerpo de la solicitud. (Cuando se especifica esta marca y se establece en *true*, se omiten todas las demás propiedades). Por ejemplo:

```json
{
  "properties": {
    "publisherEmail": "help@contoso.com",
    "publisherName": "Contoso",
    "restore": true
  },
  "sku": {
    "name": "Developer",
    "capacity": 1
  },
  "location": "South Central US"
}
```

## <a name="purge-a-soft-deleted-apim-instance"></a>Purga de una instancia de APIM eliminada temporalmente

Use la operación [Purge](/rest/api/apimanagement/2020-06-01-preview/deletedservices/purge) (Purgar) de API Management, pero sustituya `{subscriptionId}`, `{location}` y `{serviceName}` por la suscripción de Azure, la ubicación del recurso y el nombre de API Management:

```rest
DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.ApiManagement/locations/{location}/deletedservices/{serviceName}?api-version=2020-06-01-preview
```

La instancia de API Management se eliminará permanentemente de Azure.

## <a name="next-steps"></a>Pasos siguientes

Más información sobre las opciones de copia de seguridad y recuperación de API Management a largo plazo:

- [Procedimiento para implementar la recuperación ante desastres mediante copias de seguridad y restauración del servicio en Azure API Management](api-management-howto-disaster-recovery-backup-restore.md)
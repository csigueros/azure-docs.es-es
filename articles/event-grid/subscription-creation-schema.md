---
title: Esquema de suscripción de Azure Event Grid
description: En este artículo se describen las propiedades de suscripción a un evento con Azure Event Grid. Esquema de suscripción de Event Grid.
ms.topic: reference
ms.date: 09/28/2021
ms.openlocfilehash: 5a0671bea2ef1cb6b7169ca4e2c94128d88f9f94
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129236507"
---
# <a name="event-grid-subscription-schema"></a>Esquema de suscripción de Event Grid

Para crear una suscripción de Event Grid, se envía una solicitud a la operación de suscripción Crear evento. Utilice el siguiente formato:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/{resource-provider}/{resource-type}/{resource-name}/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Por ejemplo, con el fin de crear una suscripción de eventos para una cuenta de almacenamiento denominada "`examplestorage`" en un grupo de recursos llamado `examplegroup`, utilice el formato siguiente:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageaccounts/examplestorage/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

El nombre de la suscripción de eventos debe tener entre 3 y 64 caracteres de longitud y solo puede contener los siguientes caracteres: a-z, A-z, 0-9, y "-". En el artículo se describen las propiedades y el esquema del cuerpo de la solicitud.
 
## <a name="event-subscription-properties"></a>Propiedades de la suscripción de eventos

| Propiedad | Tipo | Descripción |
| -------- | ---- | ----------- |
| destination | object | El objeto que define el punto de conexión. |
| filter | object | Un campo opcional para filtrar los tipos de eventos. |

### <a name="destination-object"></a>Objeto de destino

| Propiedad | Tipo | Descripción |
| -------- | ---- | ----------- |
| endpointType | string | El tipo de punto de conexión de la suscripción (webhook/HTTP, centro de eventos o cola). | 
| endpointUrl | string | Dirección URL de destino de los eventos en esta suscripción a eventos. | 

### <a name="filter-object"></a>Objeto de filtro

| Propiedad | Tipo | Descripción |
| -------- | ---- | ----------- |
| includedEventTypes | array | Realiza la correspondencia cuando el tipo de evento del mensaje de evento es una coincidencia exacta con uno de estos nombres de tipo de evento. Genera un error cuando el nombre del evento no coincide con los nombres de tipo de evento registrados para el origen del evento. El valor predeterminado coincide con todos los tipos de evento. |
| subjectBeginsWith | string | Un filtro de coincidencia de prefijo en el campo de asunto del mensaje del evento. El valor predeterminado o una cadena vacía coincide con todos los tipos de evento. | 
| subjectEndsWith | string | Un filtro de coincidencia de sufijo en el campo de asunto del mensaje del evento. El valor predeterminado o una cadena vacía coincide con todos los tipos de evento. |
| isSubjectCaseSensitive | string | Controla la coincidencia que distingue mayúsculas de minúsculas en los filtros. |
| enableAdvancedFilteringOnArrays | boolean | Habilita el uso de matrices para claves en el filtrado avanzado. Para obtener más información, consulte [Filtrado avanzado](event-filtering.md#advanced-filtering). |


## <a name="example-subscription-schema"></a>Esquema de suscripción de ejemplo

```json
{
  "properties": {
    "destination": {
      "endpointType": "webhook",
      "properties": {
          "endpointUrl": "https://example.azurewebsites.net/api/HttpTriggerCSharp1?code=VXbGWce53l48Mt8wuotr0GPmyJ/nDT4hgdFj9DpBiRt38qqnnm5OFg=="
      }
    },
    "filter": {
      "includedEventTypes": [ "Microsoft.Storage.BlobCreated", "Microsoft.Storage.BlobDeleted" ],
      "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
      "subjectEndsWith": ".jpg",
      "isSubjectCaseSensitive ": "true"
    }
  }
}
```

## <a name="next-steps"></a>Pasos siguientes

* Para ver una introducción a Event Grid, consulte el artículo acerca de [qué es Event Grid](overview.md).

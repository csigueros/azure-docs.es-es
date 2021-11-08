---
title: Empleo de la API de REST para administrar la exportación de datos en Azure IoT Central
description: Uso de la API REST de IoT Central para administrar la exportación de datos en una aplicación
author: v-krishnag
ms.author: v-krishnag
ms.date: 10/18/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: cb538b6c8777a9aeb1d02fe705a87831f7aaf5b9
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131433788"
---
# <a name="how-to-use-the-iot-central-rest-api-to-manage-data-exports"></a>Uso de la API REST de IoT Central para administrar las exportaciones de datos

La API de REST de IoT Central permite desarrollar aplicaciones cliente que se integran con aplicaciones de IoT Central. Puede usar la API REST para crear y administrar [las exportaciones de datos](howto-export-data.md) en la aplicación de IoT Central.

Cada llamada API de REST de IoT Central requiere un encabezado de autorización. Para obtener más información, vea los [procedimientos de autenticación y autorización de llamadas a la API REST de IoT Central](howto-authorize-rest-api.md).

Para ver la documentación de referencia sobre la API REST de IoT Central, consulte [Referencia de la API REST de Azure IoT Central](/rest/api/iotcentral/).

## <a name="data-export"></a>Exportación de datos

Puede usar la característica de exportación de datos de IoT Central para transmitir datos de telemetría, cambios de propiedad, eventos de conectividad de dispositivos, eventos de ciclo de vida de dispositivos y eventos de ciclo de vida de plantilla de dispositivo a destinos como Azure Event Hubs, Azure Service Bus, Azure Blob Storage y puntos de conexión de webhook.

Cada definición de exportación de datos puede enviar datos a uno o varios destinos. Cree las definiciones de destino antes de crear la definición de exportación.

### <a name="create-or-update-a-destination"></a>Creación o actualización de un destino

Use la siguiente solicitud para crear o actualizar una definición de destino:

```http
PUT https://{subdomain}.{baseDomain}/api/dataExport/destinations/{destinationId}?api-version=1.1-preview
```

* destinationId: identificador único para el destino.

En el ejemplo siguiente se muestra un cuerpo de solicitud que crea un destino de almacenamiento de blob:

```json
{
  "displayName": "Blob Storage Destination",
  "type": "blobstorage@v1",
  "connectionString": "DefaultEndpointsProtocol=https;AccountName=yourAccountName;AccountKey=********;EndpointSuffix=core.windows.net",
  "containerName": "central-data"
}
```

El cuerpo de la solicitud tiene algunos campos obligatorios:

* `displayName`: nombre para mostrar del destino.
* `type`: tipo de objeto de destino que puede ser uno de: `blobstorage@v1`, `dataexplorer@v1`, `eventhubs@v1`, `servicebusqueue@v1`, `servicebustopic@v1`, `webhook@v1`.
* `connectionString`: la cadena de conexión para acceder al recurso de destino.
* `containerName`: para un destino de almacenamiento de blobs, el nombre del contenedor donde se deben escribir los datos.

La respuesta a esta solicitud es similar al ejemplo siguiente: 

```json
{
    "id": "8dbcdb53-c6a7-498a-a976-a824b694c150",
    "displayName": "Blob Storage Destination",
    "type": "blobstorage@v1",
    "connectionString": "DefaultEndpointsProtocol=https;AccountName=yourAccountName;AccountKey=********;EndpointSuffix=core.windows.net",
    "containerName": "central-data",
    "status": "waiting"
}
```

### <a name="get-a-destination-by-id"></a>Obtener un destino por identificador

Use la siguiente solicitud para recuperar los detalles de un destino desde la aplicación:

```http
GET https://{subdomain}.{baseDomain}/api/dataExport/destinations/{destinationId}?api-version=1.1-preview
```

La respuesta a esta solicitud es similar al ejemplo siguiente:

```json
{
    "id": "8dbcdb53-c6a7-498a-a976-a824b694c150",
    "displayName": "Blob Storage Destination",
    "type": "blobstorage@v1",
    "connectionString": "DefaultEndpointsProtocol=https;AccountName=yourAccountName;AccountKey=********;EndpointSuffix=core.windows.net",
    "containerName": "central-data",
    "status": "waiting"
}
```

### <a name="list-destinations"></a>Destinos de la lista

Use la siguiente solicitud para recuperar una lista de destinos de la aplicación:

```http
GET https://{subdomain}.{baseDomain}/api/dataExport/destinations?api-version=1.1-preview
```

La respuesta a esta solicitud es similar al ejemplo siguiente: 

```json
{
    "value": [
        {
            "id": "8dbcdb53-c6a7-498a-a976-a824b694c150",
            "displayName": "Blob Storage Destination",
            "type": "blobstorage@v1",
            "authorization": {
                "type": "connectionString",
                "connectionString": DefaultEndpointsProtocol=https;AccountName=yourAccountName;AccountKey=********;EndpointSuffix=core.windows.net",
                "containerName": "central-data"
            },
            "status": "waiting"
        },
        {
            "id": "9742a8d9-c3ca-4d8d-8bc7-357bdc7f39d9",
            "displayName": "Webhook destination",
            "type": "webhook@v1",
            "url": "http://requestbin.net/r/f7x2i1ug",
            "headerCustomizations": {},
            "status": "error",
        }
        }
    ]
}
```

### <a name="patch-a-destination"></a>Revisión de un destino

```http
PATCH https://{subdomain}.{baseDomain}/api/dataExport/destinations/{destinationId}?api-version=1.1-preview
```

Puede usarlo para realizar una actualización incremental de una exportación. El cuerpo de la solicitud de ejemplo es similar al ejemplo siguiente, que actualiza `displayName` a un destino:

```json
{
  "displayName": "Blob Storage",
  "type": "blobstorage@v1",
  "connectionString": "DefaultEndpointsProtocol=https;AccountName=yourAccountName;AccountKey=********;EndpointSuffix=core.windows.net",
  "containerName": "central-data"
}
```

La respuesta a esta solicitud es similar al ejemplo siguiente:

```json
{
    "id": "8dbcdb53-c6a7-498a-a976-a824b694c150",
    "displayName": "Blob Storage",
    "type": "blobstorage@v1",
    "connectionString": "DefaultEndpointsProtocol=https;AccountName=yourAccountName;AccountKey=********;EndpointSuffix=core.windows.net",
    "containerName": "central-data",
    "status": "waiting"
}   
```

### <a name="delete-a-destination"></a>Eliminar un destino

Use la siguiente solicitud para eliminar un destino:

```http
DELETE https://{subdomain}.{baseDomain}/api/dataExport/destinations/{destinationId}?api-version=1.1-preview
```

### <a name="create-or-update-an-export-definition"></a>Creación o actualización de una definición de exportación

Use la siguiente solicitud para crear o actualizar una definición de exportación de datos:

```http
PUT https://{subdomain}.{baseDomain}/api/dataExport/exports/{exportId}?api-version=1.1-preview
```

En el ejemplo siguiente se muestra un cuerpo de solicitud que crea una definición de exportación para la telemetría del dispositivo:

```json
{
    "displayName": "Enriched Export",
    "enabled": true,
    "source": "telemetry",
    "enrichments": {
        "Custom data": {
            "value": "My value"
        }
    },
    "destinations": [
        {
            "id": "8dbcdb53-c6a7-498a-a976-a824b694c150"
        }
    ]
}
```

El cuerpo de la solicitud tiene algunos campos obligatorios:

* `displayName`: nombre para mostrar de la exportación.
* `enabled`: alterna para iniciar o detener la exportación de un envío de datos.
* `source`: tipo de datos para exportar.
* `destinations`: lista de destinos a los que la exportación debe enviar datos. Los identificadores de destino ya deben existir en la aplicación.

Hay algunos campos opcionales que puede usar para agregar más detalles a la exportación.

* `enrichments`: fragmentos de información adicionales que se incluirán con cada mensaje enviado. Los datos se representan como un conjunto de pares clave/valor, donde la clave es el nombre del enriquecimiento que aparecerá en el mensaje de salida y el valor identifica los datos que se van a enviar.
* `filter`: consulta que define qué eventos del origen se deben exportar.

La respuesta a esta solicitud es similar al ejemplo siguiente: 

```json
{
    "id": "6e93df53-1ce5-45e4-ad61-3eb0d684b3a5",
    "displayName": "Enriched Export",
    "enabled": true,
    "source": "telemetry",
    "enrichments": {
        "Custom data": {
            "value": "My value"
        }
    },
    "destinations": [
        {
            "id": "9742a8d9-c3ca-4d8d-8bc7-357bdc7f39d9"
        }
    ],
    "status": "starting"
}
```

### <a name="get-an-export-by-id"></a>Obtener una exportación por identificador

Use la siguiente solicitud para recuperar los detalles de una definición de exportación desde la aplicación:

```http
GET https://{subdomain}.{baseDomain}/api/dataExport/exports/{exportId}?api-version=1.1-preview
```

La respuesta a esta solicitud es similar al ejemplo siguiente:

```json
{
    "id": "8dbcdb53-c6a7-498a-a976-a824b694c150",
    "displayName": "Blob Storage Destination",
    "type": "blobstorage@v1",
    "connectionString": "DefaultEndpointsProtocol=https;AccountName=yourAccountName;AccountKey=********;EndpointSuffix=core.windows.net",
    "containerName": "central-data",
    "status": "waiting"
}
```

### <a name="list-export-definitions"></a>Lista de definiciones de exportación

Use la siguiente solicitud para recuperar una lista de definiciones de exportación de la aplicación:

```http
GET https://{subdomain}.{baseDomain}/api/dataExport/exports?api-version=1.1-preview
```

La respuesta a esta solicitud es similar al ejemplo siguiente: 

```json
{
  "value": [
    {
      "id": "6e93df53-1ce5-45e4-ad61-3eb0d684b3a5",
      "displayName": "Enriched Export",
      "enabled": true,
      "source": "telemetry",
      "enrichments": {
        "Custom data": {
          "value": "My value"
        }
      },
      "destinations": [
        {
          "id": "9742a8d9-c3ca-4d8d-8bc7-357bdc7f39d9"
        }
      ],
      "status": "starting"
    },
    {
      "id": "802894c4-33bc-4f1e-ad64-e886f315cece",
      "displayName": "Enriched Export",
      "enabled": true,
      "source": "telemetry",
      "enrichments": {
        "Custom data": {
          "value": "My value"
        }
      },
      "destinations": [
        {
          "id": "9742a8d9-c3ca-4d8d-8bc7-357bdc7f39d9"
        }
      ],
      "status": "healthy"
    }
  ]
}
```

### <a name="patch-an-export-definition"></a>Revisión de una definición de exportación

```http
PATCH https://{subdomain}.{baseDomain}/dataExport/exports/{exportId}?api-version=1.1-preview
```

Puede usarlo para realizar una actualización incremental de una exportación. El cuerpo de la solicitud de ejemplo es similar al ejemplo siguiente, que actualiza `enrichments` a una exportación:

```json
{
    "displayName": "Enriched Export",
    "enabled": true,
    "source": "telemetry",
    "enrichments": {
        "Custom data": {
            "value": "My value 2"
        }
    },
    "destinations": [
        {
            "id": "9742a8d9-c3ca-4d8d-8bc7-357bdc7f39d9"
        }
    ]
}
```

La respuesta a esta solicitud es similar al ejemplo siguiente:

```json
{
    "id": "6e93df53-1ce5-45e4-ad61-3eb0d684b3a5",
    "displayName": "Enriched Export",
    "enabled": true,
    "source": "telemetry",
    "enrichments": {
        "Custom data": {
            "value": "My"
        }
    },
    "destinations": [
        {
            "id": "9742a8d9-c3ca-4d8d-8bc7-357bdc7f39d9"
        }
    ],
    "status": "healthy"
}
```

### <a name="delete-an-export-definition"></a>Eliminar una definición de exportación

Use la siguiente solicitud para eliminar una definición de exportación:

```http
DELETE https://{subdomain}.{baseDomain}/api/dataExport/destinations/{destinationId}?api-version=1.1-preview
```

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a administrar la exportación de datos con la API de REST, el siguiente paso que se sugiere es [Empleo de la API de REST de IoT Central para administrar plantillas de dispositivos](howto-manage-device-templates-with-rest-api.md).

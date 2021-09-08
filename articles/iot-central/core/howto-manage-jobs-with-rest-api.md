---
title: Uso de la API REST para administrar trabajos en Azure IoT Central
description: Uso de la API REST de IoT Central para crear y administrar trabajos en una aplicación
author: dominicbetts
ms.author: dobett
ms.date: 08/30/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 89dd9c60e28db55ad1cab65fd5554d77244ff777
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2021
ms.locfileid: "123480757"
---
# <a name="how-to-use-the-iot-central-rest-api-to-create-and-manage-jobs"></a>Uso de la API REST de IoT Central para crear y administrar trabajos

La API de REST de IoT Central permite desarrollar aplicaciones cliente que se integran con aplicaciones de IoT Central. Puede usar la API REST para crear y administrar trabajos en la aplicación de IoT Central. La API de REST permite:

- Mostrar los trabajos y ver los detalles en la aplicación.
- Crear trabajos en la aplicación.
- Detener, reanudar y volver a ejecutar trabajos en la aplicación.

> [!IMPORTANT]
> La API de trabajos se encuentra actualmente en versión preliminar. Todas las llamadas a la API REST descritas en este artículo deben incluir `?api-version=preview`.

En este artículo se explica cómo usar la API `/jobs/{job_id}` para controlar dispositivos en masa. También puede controlar los dispositivos de forma individual.

Cada llamada API de REST de IoT Central requiere un encabezado de autorización. Para obtener más información, vea los [procedimientos de autenticación y autorización de llamadas a la API REST de IoT Central](howto-authorize-rest-api.md).

Para obtener documentación de referencia sobre la API de REST de IoT Central, vea [Referencia de la API de REST de Azure IoT Central](/rest/api/iotcentral/).

## <a name="job-payloads"></a>Cargas de trabajo

Muchas de las API que se describen en este artículo incluyen una definición similar al siguiente fragmento de código JSON:

```json
{
  "id": "job-014",
  "displayName": "Set target temperature",
  "description": "Set target temperature for all thermostat devices",
  "group": "833d7a7d-8f99-4e04-9e56-745806bdba6e",
  "batch": {
    "type": "percentage",
    "value": 25
  },
  "cancellationThreshold": {
    "type": "percentage",
    "value": 10,
    "batch": false
  },
  "data": [
    {
      "type": "PropertyJobData",
      "target": "dtmi:modelDefinition:zomtmdxh:eqod32zbyl",
      "path": "targetTemperature",
      "value": "56"
    }
  ],
  "status": "complete"
}
```

En la siguiente tabla se describen los campos del fragmento de código JSON anterior:

| Campo | Descripción |
| ----- | ----------- |
| `id` | Identificador único del trabajo en la aplicación. |
| `displayName` | Nombre para mostrar del trabajo en la aplicación. |
| `description` | Descripción del trabajo. |
| `group` | Identificador del grupo de dispositivos al que se aplica el trabajo. Use la API REST en versión preliminar `deviceGroups` para obtener una lista de los grupos de dispositivos de la aplicación. |
| `status` | El [estado](howto-manage-devices-in-bulk.md#view-job-status) del trabajo. Uno de estos valores: `complete`, `cancelled`, `failed`, `pending`, `running`, `stopped`. |
| `batch` | Si existe, en esta sección se define cómo [procesar por lotes](howto-manage-devices-in-bulk.md#create-and-run-a-job) los dispositivos en el trabajo. |
| `batch/type` | El tamaño de cada lote es un valor de `percentage` de los dispositivos totales del grupo o un valor de `number` de dispositivos. |
| `batch/value` | El porcentaje de dispositivos o el número de dispositivos de cada lote. |
| `cancellationThreshold` | Si existe, en esta sección se define el [umbral de cancelación](howto-manage-devices-in-bulk.md#create-and-run-a-job) del trabajo. |
| `cancellationThreshold/batch` | `true` o `false`. Si es true, se establece el umbral de cancelación de cada lote. Si es `false`, el umbral de cancelación se aplica a todo el trabajo. |
| `cancellationThreshold/type` | El umbral de cancelación del trabajo es un valor de `percentage` o un valor de `number` de dispositivos. |
| `cancellationThreshold/value` | El porcentaje de dispositivos o el número de dispositivos que definen el umbral de cancelación. |
| `data` | Matriz de operaciones que realiza el trabajo. |
| `data/type` | Uno de estos valores: `PropertyJobData`, `CommandJobData` o `CloudPropertyJobData` |
| `data/target` | El identificador del modelo de los dispositivos de destino |
| `data/path` | Nombre de la propiedad, comando o propiedad de nube. |
| `data/value` | Valor de propiedad que se establece o el parámetro de comando que se envía. |

## <a name="get-job-information"></a>Obtención de información del trabajo

Use la siguiente solicitud para recuperar la lista de trabajos de la aplicación:

```http
GET https://{your app subdomain}.azureiotcentral.com/api/jobs?api-version=preview
```

La respuesta a esta solicitud es similar al ejemplo siguiente:

```json
{
  "value": [
    {
      "id": "job-006",
      "displayName": "Set customer name",
      "description": "Set the customer name cloud property",
      "group": "4fcbec3b-5ee8-4324-855d-0f03b56bcf7f",
      "data": [
        {
          "type": "CloudPropertyJobData",
          "target": "dtmi:modelDefinition:bojo9tfju:yfvu5gv2vl",
          "path": "CustomerName",
          "value": "Contoso"
        }
      ],
      "status": "complete"
    },
    {
      "id": "job-005",
      "displayName": "Set target temperature",
      "description": "Set target temperature device property",
      "group": "833d7a7d-8f99-4e04-9e56-745806bdba6e",
      "data": [
        {
          "type": "PropertyJobData",
          "target": "dtmi:modelDefinition:zomtmdxh:eqod32zbyl",
          "path": "targetTemperature",
          "value": 56
        }
      ],
      "status": "complete"
    },
    {
      "id": "job-004",
      "displayName": "Run device report",
      "description": "Call command to run the device reports",
      "group": "833d7a7d-8f99-4e04-9e56-745806bdba6e",
      "batch": {
        "type": "percentage",
        "value": 25
      },
      "cancellationThreshold": {
        "type": "percentage",
        "value": 10,
        "batch": false
      },
      "data": [
        {
          "type": "CommandJobData",
          "target": "dtmi:modelDefinition:zomtmdxh:eqod32zbyl",
          "path": "getMaxMinReport",
          "value": "2021-06-15T05:00:00.000Z"
        }
      ],
      "status": "complete"
    }
  ]
}
```

Use la siguiente solicitud para recuperar un trabajo individual por el identificador:

```http
GET https://{your app subdomain}.azureiotcentral.com/api/jobs/job-004?api-version=preview
```

La respuesta a esta solicitud es similar al ejemplo siguiente:

```json
{
  "id": "job-004",
  "displayName": "Run device report",
  "description": "Call command to run the device reports",
  "group": "833d7a7d-8f99-4e04-9e56-745806bdba6e",
  "batch": {
    "type": "percentage",
    "value": 25
  },
  "cancellationThreshold": {
    "type": "percentage",
    "value": 10,
    "batch": false
  },
  "data": [
    {
      "type": "CommandJobData",
      "target": "dtmi:modelDefinition:zomtmdxh:eqod32zbyl",
      "path": "getMaxMinReport",
      "value": "2021-06-15T05:00:00.000Z"
    }
  ],
  "status": "complete"
}
```

Use la siguiente solicitud para recuperar los detalles de los dispositivos de un trabajo:

```http
GET https://{your app subdomain}.azureiotcentral.com/api/jobs/job-004/devices?api-version=preview
```

La respuesta a esta solicitud es similar al ejemplo siguiente:

```json
{
  "value": [
    {
      "id": "therm-01",
      "status": "completed"
    },
    {
      "id": "therm-02",
      "status": "completed"
    },
    {
      "id": "therm-03",
      "status": "completed"
    },
    {
      "id": "therm-04",
      "status": "completed"
    }
  ]
}
```

## <a name="create-a-job"></a>Creación de un trabajo

Use la siguiente solicitud para recuperar los detalles de los dispositivos de un trabajo:

```http
PUT https://{your app subdomain}.azureiotcentral.com/api/jobs/job-006?api-version=preview
{
  "displayName": "Set target temperature",
  "description": "Set target temperature device property",
  "group": "833d7a7d-8f99-4e04-9e56-745806bdba6e",
  "batch": {
    "type": "percentage",
    "value": 25
  },
  "cancellationThreshold": {
    "type": "percentage",
    "value": 10,
    "batch": false
  },
  "data": [
    {
      "type": "PropertyJobData",
      "target": "dtmi:modelDefinition:zomtmdxh:eqod32zbyl",
      "path": "targetTemperature",
      "value": "55"
    }
  ]
}
```

La respuesta a esta solicitud es similar al ejemplo siguiente. El estado inicial del trabajo es `pending`:

```json
{
  "id": "job-006",
  "displayName": "Set target temperature",
  "description": "Set target temperature device property",
  "group": "833d7a7d-8f99-4e04-9e56-745806bdba6e",
  "batch": {
    "type": "percentage",
    "value": 25
  },
  "cancellationThreshold": {
    "type": "percentage",
    "value": 10,
    "batch": false
  },
  "data": [
    {
      "type": "PropertyJobData",
      "target": "dtmi:modelDefinition:zomtmdxh:eqod32zbyl",
      "path": "targetTemperature",
      "value": "55"
    }
  ],
  "status": "pending"
}
```

## <a name="stop-resume-and-rerun-jobs"></a>Detener, reanudar y volver a ejecutar trabajos

Use la solicitud siguiente para detener un trabajo en ejecución:

```http
POST https://{your app subdomain}.azureiotcentral.com/api/jobs/job-006/stop?api-version=preview
```

Si la solicitud se realiza correctamente, devuelve una respuesta `204 - No Content`.

Use la solicitud siguiente para reanudar un trabajo detenido:

```http
POST https://{your app subdomain}.azureiotcentral.com/api/jobs/job-006/resume?api-version=preview
```

Si la solicitud se realiza correctamente, devuelve una respuesta `204 - No Content`.

Use el siguiente comando para volver a ejecutar un trabajo existente en los dispositivos con errores:

```http
PUT https://{your app subdomain}.azureiotcentral.com/api/jobs/job-006/rerun/rerun-001?api-version=preview
```

## <a name="next-steps"></a>Pasos siguientes

Ahora que sabe administrar trabajos con la API REST, el siguiente paso recomendado consiste en aprender a [administrar aplicaciones de IoT Central mediante la API REST](/learn/modules/manage-iot-central-apps-with-rest-api/).

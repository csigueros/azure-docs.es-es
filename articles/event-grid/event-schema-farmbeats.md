---
title: Azure FarmBeats como origen de datos de Event Grid (versión preliminar)
description: Describe las propiedades y los esquemas que se proporcionan para los eventos de Azure FarmBeats con Azure Event Grid
ms.topic: conceptual
ms.date: 06/06/2021
ms.openlocfilehash: 552a676fcfc457a662276aa7b5948670f700d208
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121726733"
---
# <a name="azure-farmbeats-as-event-grid-source-preview"></a>Azure FarmBeats como origen de datos de Event Grid (versión preliminar)
En este artículo se proporcionan las propiedades y los esquemas de los eventos de Azure FarmBeats.  Para una introducción a los esquemas de eventos, consulte [Esquema de eventos de Azure Event Grid](event-schema.md). 

## <a name="available-event-types"></a>Tipos de eventos disponibles

|Nombre del evento | Descripción|
|-----|----|
|Microsoft.AgFoodPlatform.FarmerChanged|Se publica cuando se crea, actualiza o elimina un granjero. 
|Microsoft.AgFoodPlatform.FarmChanged| Se publica cuando se crea, actualiza o elimina una granja.
|Microsoft.AgFoodPlatform.BoundaryChanged|Se publica cuando se crea, actualiza o elimina un límite.
|Microsoft.AgFoodPlatform.FieldChanged|Se publica cuando se crea, actualiza o elimina un campo.
|Microsoft.AgFoodPlatform.SeasonalFieldChanged|Se publica cuando se crea, actualiza o elimina un campo estacional.
|Microsoft.AgFoodPlatform.SeasonChanged|Se publica cuando se crea, actualiza o elimina una estación.
|Microsoft.AgFoodPlatform.CropChanged|Se publica cuando se crea, actualiza o elimina una cosecha.
|Microsoft.AgFoodPlatform.CropVarietyChanged|Se publica cuando se crea, actualiza o elimina una variedad de cosecha.
|Microsoft.AgFoodPlatform.SatelliteDataIngestionJobStatusChanged| Se publica cuando cambia el estado de un trabajo de ingesta de datos del satélite, por ejemplo, cuando se crea un trabajo, o este ha progresado o se ha completado.
|Microsoft.AgFoodPlatform.WeatherDataIngestionJobStatusChanged|Se publica cuando cambia el estado de un trabajo de ingesta de datos de tiempo atmosférico, por ejemplo, cuando se crea un trabajo, o este ha progresado o se ha completado.
|Microsoft.AgFoodPlatform.FarmOperationDataIngestionJobStatusChanged| Se publica cuando cambia el estado de un trabajo de ingesta de datos de operaciones de la granja, por ejemplo, cuando se crea un trabajo, o este ha progresado o se ha completado.
|Microsoft.AgFoodPlatform.ApplicationDataChanged|Se publica cuando se crean, actualizan o eliminan datos de la aplicación. Este evento se asocia a los datos de operaciones de la granja.
|Microsoft.AgFoodPlatform.HarvestingDataChanged|Se publica cuando se crean, actualizan o eliminan datos de la recolección. Este evento está asociado a los datos de operaciones de la granja.
|Microsoft.AgFoodPlatform.TillageDataChanged|Se publica cuando se crean, actualizan o eliminan datos de cultivo. Este evento se asocia a los datos de operaciones de la granja.
|Microsoft.AgFoodPlatform.PlantingDataChanged|Se publica cuando se crean, actualizan o eliminan datos de plantación. Este evento está asociado a los datos de operaciones de la granja.

## <a name="event-properties"></a>Propiedades de evento
Cada evento de FarmBeats tiene dos partes, una que es común a todos los eventos y otra (un objeto de datos) que contiene propiedades específicas de cada evento. 

La parte común a todos los eventos se elabora en el esquema siguiente.

### <a name="event-grid-event-schema"></a>Esquema de eventos de Event Grid
Un evento tiene los siguientes datos de nivel superior:

| Propiedad | Tipo | Descripción |
| -------- | ---- | ----------- |
| `topic` | string | Ruta de acceso completa a los recursos del origen del evento. En este campo no se puede escribir. Event Grid proporciona este valor. |
| `subject` | string | Ruta al asunto del evento definida por el anunciante. |
| `eventType` | string | Uno de los tipos de eventos registrados para este origen de eventos. |
| `eventTime` | string | La hora de generación del evento en función de la hora UTC del proveedor. |
| `id` | string | Identificador único para el evento |
| `data` | object | Datos del evento de App Configuration. |
| `dataVersion` | string | Versión del esquema del objeto de datos. El publicador define la versión del esquema. |
| `metadataVersion` | string | Versión del esquema de los metadatos del evento. Event Grid define el esquema de las propiedades de nivel superior. Event Grid proporciona este valor. |


Las tablas siguientes se elaboran según las propiedades del objeto de datos para cada evento.

*En el caso de los eventos FarmerChanged, FarmChanged, SeasonChanged, CropChanged, CropVarietyChanged de FarmBeats, el objeto de datos contiene las siguientes propiedades:*

|Propiedad | Tipo| Descripción|
|----| ----| ----|
id| string| Identificador del recurso definido por el usuario, como el identificador de granja, granjero, etc.
actionType| string| Indica el cambio desencadenado durante la publicación del evento. Los valores aplicables son Creado, Actualizado, Eliminado.
status| string| Contiene el estado definido por el usuario del recurso.
properties| object| Contiene los pares clave/valor definidos por el usuario.
modifiedDateTime| date-time|Fecha y hora en que se modificó por última vez el recurso, formato de ejemplo: aaaa-MM-ddTHH:mm:ssZ.
createdDateTime|date-time|Fecha y hora en que se creó el recurso, formato de ejemplo: aaaa-MM-ddTHH:mm:ssZ.
eTag|   string| Implementa una simultaneidad optimista.
description| string|    Descripción textual del recurso.


*Los eventos BoundaryChanged de FarmBeats tienen el siguiente objeto de datos:*

|Propiedad | Tipo| Descripción|
|----| ----| ----|
id| string| Identificador de límite definido por el usuario
actionType| string| Indica el cambio que se desencadena durante la publicación del evento. Los valores aplicables son Creado, Actualizado, Eliminado.
parentId|   string| Identificador del límite primario al que pertenece.
parentType| string| Tipo del límite primario al que pertenece.
isPrimary|  boolean|    Indica si el límite es principal.
farmerId|   string| Contiene el identificador del granjero asociado con el límite.
properties| object| Contiene los pares clave/valor definidos por el usuario.
modifiedDateTime| date-time|Fecha y hora en que se modificó por última vez el recurso, formato de ejemplo: aaaa-MM-ddTHH:mm:ssZ.
createdDateTime|date-time|Fecha y hora en que se creó el recurso, formato de ejemplo: aaaa-MM-ddTHH:mm:ssZ.
status| string| Contiene el estado definido por el usuario del recurso.
eTag|   string| Implementa una simultaneidad optimista.
description| string|    Descripción textual del recurso.

*Los eventos FieldChanged de FarmBeats tienen el siguiente objeto de datos:*

Propiedad|   Tipo|   Descripción
|----| ----| ----|
id| string| Identificador del campo definido por el usuario
farmId| string| Identificador de la granja a la que está asociado ese campo definido por el usuario
farmerId|   string| Identificador del granjero al que está asociado ese campo definido por el usuario
name|   string| Nombre definido por el usuario del campo
actionType| string| Indica el cambio que desencadenó la publicación del evento. Los valores aplicables son Creado, Actualizado, Eliminado.
properties| object| Contiene los pares clave/valor definidos por el usuario.
modifiedDateTime| date-time|Fecha y hora en que se modificó por última vez el recurso, formato de ejemplo: aaaa-MM-ddTHH:mm:ssZ.
createdDateTime|date-time|Fecha y hora en que se creó el recurso, formato de ejemplo: aaaa-MM-ddTHH:mm:ssZ.
status| string| Contiene el estado definido por el usuario del recurso.
eTag|   string| Implementa una simultaneidad optimista.
description|string| Descripción textual del recurso.

*Los eventos SeasonalFieldChanged de FarmBeats tienen el siguiente objeto de datos:*

Propiedad|   Tipo|   Descripción
|----| ----| ----|
id| string| Identificador del campo estacional definido por el usuario
farmId| string| Identificador de la granja a la que está asociado ese campo estacional y definido por el usuario
farmerId|   string| Identificador del granjero al que está asociado ese campo estacional y definido por el usuario
seasonId|   string| Identificador de la estación a la que está asociada ese campo estacional y definido por el usuario
fieldId|    string| Identificador del campo al que está asociado ese campo estacional y definido por el usuario
name|   string| Nombre del campo estacional definido por el usuario
actionType| string| Indica el cambio que desencadenó la publicación del evento. Los valores aplicables son Creado, Actualizado, Eliminado.
properties| object| Contiene los pares clave/valor definidos por el usuario.
modifiedDateTime| date-time|Fecha y hora en que se modificó por última vez el recurso, formato de ejemplo: aaaa-MM-ddTHH:mm:ssZ.
createdDateTime|date-time|Fecha y hora en que se creó el recurso, formato de ejemplo: aaaa-MM-ddTHH:mm:ssZ.
status| string| Contiene el estado definido por el usuario del recurso.
eTag|   string| Implementa una simultaneidad optimista.
description| string|    Descripción textual del recurso.

*Los eventos SatelliteDataIngestionJobChanged, WeatherDataIngestionJobChanged y FarmOperationsDataIngestionJobChanged de FarmBeats tienen el siguiente objeto de datos:*

Propiedad|   Tipo|   Descripción
|----|----|----|
id|String| Id. único del trabajo.
name| string| Nombre del trabajo definido por el usuario
status|string|Diversos estados en los que puede estar un trabajo.
isCancellationRequested| boolean|Marca que se establece cuando se solicita la cancelación del trabajo.
description|string| Descripción textual del trabajo.
farmerId|string| Identificador del granjero para el que se creó el trabajo.
message|string| Mensaje de estado para capturar más detalles del trabajo.
lastActionDateTime|date-time|Fecha y hora en que se realizó la última acción en el trabajo, formato de ejemplo: aaaa-MM-ddTHH:mm:ssZ.
createdDateTime|date-time|Fecha y hora en que se creó el recurso, formato de ejemplo: aaaa-MM-ddTHH:mm:ssZ.


*Los eventos de cambio de datos de operaciones de granja de FarmBeats, como ApplicationDataChanged, HarvestingDataChanged, PlantingDataChanged y TillageDataChanged, tienen el siguiente objeto de datos:*

Propiedad|   Tipo|   Descripción
|----|----|----|
id| string| Identificador del recurso definido por el usuario, como el identificador de granja, granjero, etc.
status| string| Contiene el estado del trabajo. 
actionType|string|
source| string| Mensaje de FarmBeats que ofrece detalles sobre el trabajo.    
modifiedDateTime| date-time|Fecha y hora en que se modificó por última vez el recurso, formato de ejemplo: aaaa-MM-ddTHH:mm:ssZ.
createdDateTime|date-time|Fecha y hora en que se creó el recurso, formato de ejemplo: aaaa-MM-ddTHH:mm:ssZ.
eTag|   string| Implementa una simultaneidad optimista.
description|string| Descripción textual del recurso.


## <a name="sample-events"></a>Eventos de muestra 
Estos ejemplos de eventos representan una notificación de eventos.

**Tipo de evento: Microsoft.AgFoodPlatform.FarmerChanged**

```json
{
    "data": {
      "actionType": "Created",
      "status": "Sample status",
      "modifiedDateTime": "2021-03-05T10:53:28Z",
      "eTag": "860197cc-0000-0700-0000-60420da80000",
      "id": "UNIQUE-FARMER-ID",
      "name": "sample farmer",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T10:53:28Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "81fbe1de-4ae4-4284-964f-59da80a6bfe7",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID",
    "eventType": "Microsoft.AgFoodPlatform.FarmerChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T10:53:28.2783745Z"
  }
```

**Tipo de evento: Microsoft.AgFoodPlatform.FarmChanged**

```json
  {
    "data": {
      "farmerId": "UNIQUE-FARMER-ID",
      "actionType": "Created",
      "status": "Sample status",
      "modifiedDateTime": "2021-03-05T10:55:57Z",
      "eTag": "8601e3d5-0000-0700-0000-60420e3d0000",
      "id": "UNIQUE-FARM-ID",
      "name": "Display name",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T10:55:57Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "31a31be7-51fb-48f3-adfd-6fb4400be002",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID/farms/UNIQUE-FARM-ID",
    "eventType": "Microsoft.AgFoodPlatform.FarmChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T10:55:57.6026173Z"
  }
```
**Tipo de evento: Microsoft.AgFoodPlatform.BoundaryChanged**

```json
  {
    "data": {
      "farmerId": "UNIQUE-FARMER-ID",
      "parentId": "OPTIONAL-UNIQUE-FIELD-ID",
      "isPrimary": true,
      "actionType": "Created",
      "modifiedDateTime": "2021-03-05T11:15:29Z",
      "eTag": "860109f7-0000-0700-0000-604212d10000",
      "id": "UNIQUE-BOUNDARY-ID",
      "name": "Display name",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T11:15:29Z"
    },
    "id": "3d3453b2-5a94-45a7-98eb-fc2979a00317",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID/boundaries/UNIQUE-BOUNDARY-ID",
    "eventType": "Microsoft.AgFoodPlatform.BoundaryChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T11:15:29.4797354Z"
  }
  ```

**Tipo de evento: Microsoft.AgFoodPlatform.FieldChanged**

```json
  {
    "data": {
      "farmerId": "UNIQUE-FARMER-ID",
      "farmId": "UNIQUE-FARM-ID",
      "actionType": "Created",
      "status": "Sample status",
      "modifiedDateTime": "2021-03-05T10:58:43Z",
      "eTag": "860124dc-0000-0700-0000-60420ee30000",
      "id": "UNIQUE-FIELD-ID",
      "name": "Display name",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T10:58:43Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "1ad04ed0-ac05-4c4e-aa3d-87facb3cc97c",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID/fields/UNIQUE-FIELD-ID",
    "eventType": "Microsoft.AgFoodPlatform.FieldChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T10:58:43.3222921Z"
  }
  ```
**Tipo de evento: Microsoft.AgFoodPlatform.SeasonalFieldChanged**
```json
  {
    "data": {
      "farmerId": "UNIQUE-FARMER-ID",
      "seasonId": "UNIQUE-SEASON-ID",
      "fieldId": "UNIQUE-FIELD-ID",
      "farmId": "UNIQUE-FARM-ID",
      "actionType": "Created",
      "status": "Sample status",
      "modifiedDateTime": "2021-03-05T11:24:56Z",
      "eTag": "8701300b-0000-0700-0000-604215080000",
      "id": "UNIQUE-SEASONAL-FIELD-ID",
      "name": "Display name",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T11:24:56Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "ff59a0a3-6226-42c0-9e70-01da55efa797",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID/seasonalFields/UNIQUE-SEASONAL-FIELD-ID",
    "eventType": "Microsoft.AgFoodPlatform.SeasonalFieldChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T11:24:56.4210287Z"
  }
```
**Tipo de evento: Microsoft.AgFoodPlatform.SeasonChanged**
```json
  {
    "data": {
      "actionType": "Created",
      "status": "Sample status",
      "modifiedDateTime": "2021-03-05T11:18:38Z",
      "eTag": "86019afd-0000-0700-0000-6042138e0000",
      "id": "UNIQUE-SEASON-ID",
      "name": "Display name",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T11:18:38Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "63989475-397b-4b92-8160-8743bf8e5804",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/seasons/UNIQUE-SEASON-ID",
    "eventType": "Microsoft.AgFoodPlatform.SeasonChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T11:18:38.5804699Z"
  }
  ```

  **Tipo de evento: Microsoft.AgFoodPlatform.CropChanged**

```json
  {
    "data": {
      "actionType": "Created",
      "status": "Sample status",
      "modifiedDateTime": "2021-03-05T11:03:48Z",
      "eTag": "8601c4e5-0000-0700-0000-604210150000",
      "id": "UNIQUE-CROP-ID",
      "name": "Display name",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T11:03:48Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "4c59a797-b76d-48ec-8915-ceff58628f35",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/crops/UNIQUE-CROP-ID",
    "eventType": "Microsoft.AgFoodPlatform.CropChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T11:03:49.0590658Z"
  }
  ```

**Tipo de evento: Microsoft.AgFoodPlatform.CropVarietyChanged**

```json
  {
    "data": {
      "cropId": "UNIQUE-CROP-ID",
      "actionType": "Created",
      "status": "string",
      "modifiedDateTime": "2021-03-05T11:10:21Z",
      "eTag": "860130ef-0000-0700-0000-6042119d0000",
      "id": "UNIQUE-CROP-VARIETY-ID",
      "name": "Sample status",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T11:10:21Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "29aefdb9-d648-442c-81f8-694f3f47583c",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/cropVarieties/UNIQUE-CROP-VARIETY-ID",
    "eventType": "Microsoft.AgFoodPlatform.CropVarietyChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T11:10:21.4572495Z"
  }
```
**Tipo de evento: Microsoft.AgFoodPlatform.SatelliteDataIngestionJobStatusChanged**
```json
[
  {
    "data": {
      "farmerId": "UNIQUE - FARMER - ID",
      "message": "Created job 'job1' to fetch satellite data for boundary 'boundary1' from startDate '06/01/2021' to endDate '06/01/2021' (both inclusive).",
      "status": "Waiting",
      "lastActionDateTime": "2021-06-01T11:25:37.8634096Z",
      "isCancellationRequested": false,
      "id": "UNIQUE - JOB - ID",
      "name": "samplejob",
      "description": "Sample for testing events",
      "createdDateTime": "2021-06-01T11:25:32.3421173Z",
      "properties": {
        "key1": "testvalue1",
        "key2": 123.45
      }
    },
    "id": "925c6be2-6561-4572-b7dd-0f3084a54567",
    "topic": "/subscriptions/{Subscription -ID}/resourceGroups/{RESOURCE - GROUP - NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/{UNIQUE-FARMER-ID}/satelliteDataIngestionJobs/{UNIQUE-JOB-ID}",
    "eventType": "Microsoft.AgFoodPlatform.SatelliteDataIngestionJobStatusChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-06-01T11:25:37.8634764Z"
  }
]
```
**Tipo de evento: Microsoft.AgFoodPlatform.WeatherDataIngestionJobStatusChanged**
```json
[
  {
    "data": {
      "farmerId": "UNIQUE-FARMER-ID",
      "message": "Created job to fetch weather data for job name 'job2', farmer id 'farmer2' and boundary id 'boundary2'.",
      "status": "Running",
      "lastActionDateTime": "2021-06-01T11:22:27.9031003Z",
      "isCancellationRequested": false,
      "id": "UNIQUE-JOB-ID",
      "createdDateTime": "2021-06-01T07:13:54.8843617Z"
    },
    "id": "ec30313a-ff2f-4b50-882b-31188113c15b",
    "topic": "/subscriptions/{Subscription -ID}/resourceGroups/{RESOURCE - GROUP - NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID/weatherDataIngestionJobs/UNIQUE-JOB-ID",
    "eventType": "Microsoft.AgFoodPlatform.WeatherDataIngestionJobStatusChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-06-01T11:22:27.9031302Z"
  }
]

```
**Tipo de evento: Microsoft.AgFoodPlatform.FarmOperationDataIngestionJobStatusChanged**
```json
[
  {
    "data": {
      "farmerId": "UNIQUE-FARMER-ID",
      "message": "Job completed successfully. Data statistics:{ Processed operations count = 6, Organizations count = 1, Processed organizations count = 1, Processed fields count = 2, Operations count = 6, ShapefileAttachmentsCount = 0, Fields count = 2 }",
      "status": "Succeeded",
      "lastActionDateTime": "2021-06-01T11:30:54.733625Z",
      "isCancellationRequested": false,
      "id": "UNIQUE-JOB-ID",
      "name": "sample-job",
      "description": "sample description",
      "createdDateTime": "2021-06-01T11:30:39.0905288Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "ebdbb7a1-ad28-4af7-b3a2-a4a3a2dd1b4f",
    "topic": "/subscriptions/{Subscription -ID}/resourceGroups/{RESOURCE - GROUP - NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID/farmOperationDataIngestionJobs/UNIQUE-JOB-ID",
    "eventType": "Microsoft.AgFoodPlatform.FarmOperationDataIngestionJobStatusChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-06-01T11:30:54.733671Z"
  }
]

```
**Tipo de evento: Microsoft.AgFoodPlatform.ApplicationDataChanged**

```json
  {
    "data": {
      "actionType": "Updated",
      "farmerId": "UNIQUE-FARMER-ID",
      "source": "Sample source",
      "modifiedDateTime": "2021-03-05T11:27:24Z",
      "eTag": "87011311-0000-0700-0000-6042159c0000",
      "id": "UNIQUE-APPLICATION-DATA-ID",
      "status": "Sample status",
      "name": "sample name",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T11:27:24Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "e499f6c4-63ba-4217-8261-0c6cb0e398d2",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID/applicationData/UNIQUE-APPLICATION-DATA-ID",
    "eventType": "Microsoft.AgFoodPlatform.ApplicationDataChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T11:27:24.164612Z"
  }
```

**Tipo de evento: Microsoft.AgFoodPlatform.HarvestDataChanged**
```json
  {
    "data": {
      "actionType": "Created",
      "farmerId": "UNIQUE-FARMER-ID",
      "source": "Sample source",
      "modifiedDateTime": "2021-03-05T11:33:41Z",
      "eTag": "8701141b-0000-0700-0000-604217150000",
      "id": "UNIQUE-HARVEST-DATA-ID",
      "status": "Sample status",
      "name": "sample name",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T11:33:41Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "dc3837c0-1eed-4bfa-88b6-d018cf6af4db",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID/harvestData/UNIQUE-HARVEST-DATA-ID",
    "eventType": "Microsoft.AgFoodPlatform.HarvestDataChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T11:33:41.3434992Z"
  }
```
**Tipo de evento: Microsoft.AgFoodPlatform.TillageDataChanged**
```json
  {
    "data": {
      "actionType": "Updated",
      "farmerId": "UNIQUE-FARMER-ID",
      "source": "sample source",
      "modifiedDateTime": "2021-06-15T10:31:07Z",
      "eTag": "6405f027-0000-0100-0000-60c8816b0000",
      "id": "c9858c3f-fb94-474a-a6de-103b453df976",
      "createdDateTime": "2021-06-15T10:31:07Z",
      "name": "sample name",
      "description":"sample description"
      "properties": {
        "_orgId": "498221",
        "_fieldId": "e61b83f4-3a12-431e-8010-596f2466dc27",
        "_cropSeason": "2010"
      }
    },
    "id": "f06f6686-1fa8-41fd-be99-46f40f495cce",
    "topic": "/subscriptions/da9091ec-d18f-456c-9c21-5783ee7f4645/resourceGroups/internal-farmbeats-resources/providers/Microsoft.AgFoodPlatform/farmBeats/internal-eus",
    "subject": "/farmers/10e3d7bf-c559-48be-af31-4e00df83bfcd/tillageData/c9858c3f-fb94-474a-a6de-103b453df976",
    "eventType": "Microsoft.AgFoodPlatform.TillageDataChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-06-15T10:31:07.6778047Z"
  }
```

**Tipo de evento: Microsoft.AgFoodPlatform.PlantingDataChanged**
```json
  {
    "data": {
      "actionType": "Created",
      "farmerId": "UNIQUE-FARMER-ID",
      "source": "Sample source",
      "modifiedDateTime": "2021-03-05T11:41:18Z",
      "eTag": "8701242a-0000-0700-0000-604218de0000",
      "id": "UNIQUE-PLANTING-DATA-ID",
      "status": "Sample status",
      "name": "sample name",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T11:41:18Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "42589c7f-4e16-4a4d-9314-d611c822f7ac",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID/plantingData/UNIQUE-PLANTING-DATA-ID",
    "eventType": "Microsoft.AgFoodPlatform.PlantingDataChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T11:41:18.1744322Z"
  }
```



## <a name="next-steps"></a>Pasos siguientes
* Para una introducción a Azure Event Grid, consulte [Introducción a Azure Event Grid](overview.md).

---
title: Suscripción a eventos del enrutador de trabajo
titleSuffix: An Azure Communication Services how-to guide
description: Use los SDK de Azure Communication Services para suscribirse a eventos del enrutador de trabajo desde Event Grid.
author: jasonshave
ms.author: jassha
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 10/14/2021
ms.custom: template-how-to
ms.openlocfilehash: 8e2cff056a6fb96f5cbcdbaea6be4c4257c3752b
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/18/2021
ms.locfileid: "130129042"
---
# <a name="subscribe-to-job-router-events"></a>Suscripción a eventos del enrutador de trabajo

En esta guía se describen los pasos para suscribirse a eventos del enrutador de trabajo desde la suscripción a Event Grid de Azure Communication Services. La recepción de eventos es una funcionalidad crítica que las aplicaciones personalizadas necesitarán realizar. Las acciones que realiza el enrutador de trabajos en los trabajos que envía se llevan a cabo de forma asincrónica y, aunque el SDK proporciona puntos de conexión para consultar el estado y el estado de los objetos en el sistema, la compilación de una aplicación personalizada reactiva controlada por eventos tiene importantes ventajas.

[!INCLUDE [Private Preview Disclaimer](../../includes/private-preview-include-section.md)]

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Un recurso de Communication Services implementado. [Cree un recurso de Communication Services](../../quickstarts/create-communication-resource.md).
- Opcional: finalización del inicio rápido para [empezar a trabajar con el enrutador de trabajos](../../quickstarts/router/get-started-router.md)
- Instalación del [cliente de Azure Resource Manager (ARM)](https://github.com/projectkudu/ARMClient)
- Revisión del [proyecto de ejemplo de GitHub mediante el visor de Event Grid](https://github.com/Azure/communication-preview/tree/master/samples/Job-Router/Event-Grid-Viewer)

## <a name="create-an-event-grid-subscription"></a>Creación de una suscripción de Event Grid

> [!NOTE]
> Los siguientes scripts se ejecutan mediante PowerShell

**Inicio de sesión en la cuenta de Azure**

```powershell
armclient azlogin
```

**Definición de una suscripción y un nombre de grupo de recursos**
```powershell
$env:SUB = 'subscriptions/<insert_subscription_id>'
$env:RG = 'resourcegroups/<insert_resource_group_name>'
```

**(Opcional) Enumeración de todos los recursos de ACS de la suscripción**
```powershell
armclient get "/$env:SUB/$env:RG/providers/Microsoft.Communication/communicationservices?api-version=2020-08-20"
```
**Salida**

:::image type="content" source="media/create-subscription-output.png" alt-text="Salida del comando de PowerShell":::

Como podemos ver, actualmente solo hay un recurso de Azure Communication Services en la suscripción y el grupo de recursos especificados.

**Definición del nombre del recurso de ACS**

Establezca el nombre del recurso de Azure Communication Services. Por ejemplo, si el punto de conexión al recurso es `https://contoso.communication.azure.net`, establezca `ACS_RESOURCE_NAME` en el prefijo del nombre DNS; `contoso`.

```powershell
$env:ACS_RESOURCE_NAME = "<insert_acs_resource_name>"
```

**(Opcional) Enumeración de las suscripciones de eventos existentes**

Comprobar si hay suscripciones de eventos existentes

```powershell
$env:ACS_RESOURCE_ARM_ID = "/$env:SUB/$env:RG/providers/Microsoft.Communication/CommunicationServices/$env:ACS_RESOURCE_NAME"
$env:API_VERSION = "?api-version=2020-06-01"
$env:EVENT_SUBSCRIPTIONS_PATH = "providers/Microsoft.EventGrid/eventSubscriptions"

armclient get "$env:ACS_RESOURCE_ARM_ID/$env:EVENT_SUBSCRIPTIONS_PATH/$env:API_VERSION"
```
:::image type="content" source="media/check-subscriptions.png" alt-text="Comprobación de suscripciones existentes":::

Como podemos ver, no se ha creado ninguna suscripción de eventos para este recurso.

**Creación de una nueva suscripción de eventos para eventos del enrutador**

Copie y pegue la siguiente carga de JSON en un archivo de texto denominado `test.json`.

*Carga de ejemplo*
```json
{
  "properties": {
    "destination": {
      "endpointType": "WebHook",
      "properties": {
        "endpointUrl": "<insert_webhook_path_here>"
      }
    },
    "filter": {
      "includedEventTypes": [
        "Microsoft.Communication.RouterJobReceived",
        "Microsoft.Communication.RouterJobClassified",
        "Microsoft.Communication.RouterJobLabelsUpdated",
        "Microsoft.Communication.RouterJobClassificationFailed",
        "Microsoft.Communication.RouterJobCompleted",
        "Microsoft.Communication.RouterJobClosed",
        "Microsoft.Communication.RouterJobCancelled",
        "Microsoft.Communication.RouterJobExceptionTriggered",
        "Microsoft.Communication.RouterJobExceptionCleared",
        "Microsoft.Communication.RouterWorkerOfferIssued",
        "Microsoft.Communication.RouterWorkerOfferAccepted",
        "Microsoft.Communication.RouterWorkerOfferDeclined",
        "Microsoft.Communication.RouterWorkerOfferRevoked",
        "Microsoft.Communication.RouterWorkerOfferExpired",
        "Microsoft.Communication.RouterWorkerRegistered",
        "Microsoft.Communication.RouterWorkerDeregistered"
      ],
      "subjectBeginsWith": "",
      "subjectEndsWith": ""
    }
  }
}
```

**Definición del nombre de la suscripción de eventos**
```powershell
$env:EVENT_SUBSCRIPTION_NAME = "RouterEventsSubScription_All"
```

**Creación de la suscripción de eventos**
```powershell
armclient put "$env:ACS_RESOURCE_ARM_ID/$env:EVENT_SUBSCRIPTIONS_PATH/$env:EVENT_SUBSCRIPTION_NAME/$env:API_VERSION" .\test.json
```
**Salida**

:::image type="content" source="media/create-subscription.png" alt-text="Creación de la suscripción de eventos":::

Como podemos ver, la suscripción de eventos se está creando y actualmente se encuentra en un estado de `Creating`. Normalmente tarda unos segundos en crearse.

**Comprobación de que la suscripción de eventos se haya creado correctamente**
```powershell
armclient get "$env:ACS_RESOURCE_ARM_ID/$env:EVENT_SUBSCRIPTIONS_PATH/$env:API_VERSION"
```

**Salida**

:::image type="content" source="media/verify-subscription-created.png" alt-text="Comprobación de que se haya creado la suscripción":::

Como podemos ver, la suscripción de eventos se ha creado correctamente ahora para todos los eventos del enrutador.

## <a name="creating-a-subscription-with-filters"></a>Creación de una suscripción con filtros

Al configurar las suscripciones de eventos, también puede usar filtros avanzados que controlen los eventos exactos que deben enviarse a una suscripción determinada. Por ejemplo, en el caso del ejemplo siguiente, solo los eventos `RouterJobCancelled` se suscriben y se envían al webhook en las condiciones siguientes:

- La **prioridad** del trabajo es mayor que `5`
- El trabajo se asignó a una cola de escalación
- El trabajo se canceló por inactividad
- El código de disposición de los trabajos cancelados finaliza con `_JobCancelledDueToInactivity`
- El id. de cola termina con el nombre `EscalationQueue`

```json
{
  "properties": {
    "destination": {
      "endpointType": "WebHook",
      "properties": {
        "endpointUrl": "<insert_webhook_path_here>",
        "maxEventsPerBatch": 1,
        "preferredBatchSizeInKilobytes": 64
      }
    },
    "filter": {
      "subjectEndsWith": "_JobCancelledDueToInactivity",
      "isSubjectCaseSensitive": true,
      "includedEventTypes": [
        "Microsoft.Communication.RouterJobCancelled"
      ],
      "advancedFilters": [
        {
          "operatorType": "NumberGreaterThan",
          "key": "data.priority",
          "value": 5
        },
        {
          "operatorType": "StringEndsWith",
          "key": "data.queueId",
          "values": [
            "EscalationQueue"
          ]
        }
      ],
      "enableAdvancedFilteringOnArrays": true
    }
  }
}
```

Copie y pegue la carga de JSON anterior en un texto, denomínela `test-with-advanced-filters.json` y, a continuación, ejecute el siguiente código de PowerShell:

```powershell
$env:API_VERSION = "?api-version=2020-10-15-preview"
$env:EVENT_SUBSCRIPTION_NAME = "RouterEventsSubScription_WithFilters"
armclient put "$env:ACS_RESOURCE_ARM_ID/$env:EVENT_SUBSCRIPTIONS_PATH/$env:EVENT_SUBSCRIPTION_NAME/$env:API_VERSION" .\test-with-advanced-filters.json
```

**Salida**

:::image type="content" source="media/advanced-filters.png" alt-text="Salida de filtros avanzados":::

> [!NOTE]
> Para obtener una lista completa de los operadores que se pueden usar al crear suscripciones, consulte [Event Grid | Filtrado de eventos: operadores](../../../event-grid/event-filtering.md).

## <a name="events-catalog"></a>Catálogo de eventos

| Eventos | Subdominio | Descripción |
|------|:------------:| ---------- |
| [`RouterJobReceived`](#microsoftcommunicationrouterjobreceived) | `Job` | Se creó un nuevo trabajo para el enrutamiento |
| [`RouterJobClassified`](#microsoftcommunicationrouterjobclassified)| `Job` | La directiva de clasificación se aplicó a un trabajo |
| [`RouterJobLabelsUpdated`](#microsoftcommunicationrouterjoblabelsupdated) | `Job` | Se cambiaron las etiquetas del trabajo |
| [`RouterJobClassificationFailed`](#microsoftcommunicationrouterjobclassificationfailed) | `Job` | El enrutador no pudo clasificar el trabajo mediante la directiva de clasificación |
| [`RouterJobCompleted`](#microsoftcommunicationrouterjobcompleted) | `Job` | Se completó un trabajo y entra en conclusión |
| [`RouterJobClosed`](#microsoftcommunicationrouterjobclosed) | `Job` | Se cerró un trabajo y se finalizó la conclusión |
| [`RouterJobCancelled`](#microsoftcommunicationrouterjobcancelled) | `Job` | Se canceló un trabajo |
| [`RouterJobExceptionTriggered`](#microsoftcommunicationrouterjobexceptiontriggered) | `Job` | Se desencadenó una excepción de trabajo |
| [`RouterJobExceptionCleared`](#microsoftcommunicationrouterjobexceptioncleared) | `Job` | Se borró una excepción de trabajo |
| [`RouterWorkerOfferIssued`](#microsoftcommunicationrouterworkerofferissued) | `Worker` | Se ofreció un trabajo a un trabajador |
| [`RouterWorkerOfferAccepted`](#microsoftcommunicationrouterworkerofferaccepted) | `Worker` | Se aceptó una oferta a un trabajador |
| [`RouterWorkerOfferDeclined`](#microsoftcommunicationrouterworkerofferdeclined) | `Worker` | Se rechazó una oferta a un trabajador |
| [`RouterWorkerOfferRevoked`](#microsoftcommunicationrouterworkerofferrevoked)  | `Worker` | Se revocó una oferta a un trabajador |
| [`RouterWorkerOfferExpired`](#microsoftcommunicationrouterworkerofferexpired)  | `Worker` | Caducó una oferta a un trabajador |
| [`RouterWorkerRegistered`](#microsoftcommunicationrouterworkerregistered)  | `Worker` | Se registró un trabajador |
| [`RouterWorkerDeregistered`](#microsoftcommunicationrouterworkerderegistered)  | `Worker` | Se anuló el registro de un trabajador |

## <a name="router-events"></a>Eventos del enrutador

### <a name="microsoftcommunicationrouterjobreceived"></a>Microsoft.Communication.RouterJobReceived

[Volver al catálogo de eventos](#events-catalog)

```json
{
  "id": "acdf8fa5-8ab4-4a65-874a-c1d2a4a97f2e",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "job/{job-id}/channel/{channel-id}",
  "data": {
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "jobStatus": "PendingClassification",
    "channelId": "FooVoiceChannelId",
    "classificationPolicyId": "test-policy",
    "queueId": "queue-id",
    "priority": 0,
    "labels": {
      "Locale": "en-us",
      "Segment": "Enterprise",
      "Token": "FooToken"
    }
  },
  "eventType": "Microsoft.Communication.RouterJobReceived",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:30Z"
}
```

**Lista de atributos**

| Atributo | Tipo | Nullable |Descripción | Notas |
|:--------- |:-----:|:-------:|-------------|-------|
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
| jobStatus| `enum` | ❌ | Valores posibles <ul> <li>PendingClassification</li> </ul> | Cuando se envía este evento, el proceso de clasificación aún no se ha ejecutado. |
|channelId | `string` | ❌ |
| classificationPolicyId | `string` | ✔️ | | `null` cuando se especifica `queueId` para un trabajo |
| queueId | `string` | ✔️ | | `null` cuando se especifica `classificationPolicyId` para un trabajo |
| priority | `int` | ✔️ | | NULL cuando se especifica `classificationPolicyId`. Valor distinto de NULL si hay una asignación de cola directa. |
| labels | `Dictionary<string, object>` | ✔️ | | En función de la entrada del usuario |

### <a name="microsoftcommunicationrouterjobclassified"></a>Microsoft.Communication.RouterJobClassified

[Volver al catálogo de eventos](#events-catalog)

```json
{
  "id": "b6d8687a-5a1a-42ae-b8b5-ff7ec338c872",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "job/{job-id}/channel/{channel-id}/queue/{queue-id}",
  "data": {
    "queueInfo": {
      "id": "625fec06-ab81-4e60-b780-f364ed96ade1",
      "name": "Queue 1",
      "labels": {
        "Language": "en",
        "Product": "Office",
        "Geo": "NA"
      }
    },
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "classificationPolicyId": "test-policy",
    "queueId": "625fec06-ab81-4e60-b780-f364ed96ade1",
    "priority": 5,
    "labels": {
      "Locale": "en-us",
      "Segment": "Enterprise",
      "Token": "FooToken"
    }
  },
  "eventType": "Microsoft.Communication.RouterJobClassified",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:30Z"
}
```

**Lista de atributos**

| Atributo | Tipo | Nullable |Descripción | Notas |
|:--------- |:-----:|:-------:|-------------|-------|
| queueInfo | `QueueInfo` | ❌ |
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
|channelId | `string` | ❌ |
| classificationPolicyId | `string` | ✔️ | | `null` cuando se especifica `queueId` para un trabajo (asignación de cola directa) |
| queueId | `string` | ✔️ | | `null` cuando se especifica `classificationPolicyId` para un trabajo |
| priority | `int` | ❌ |
| labels | `Dictionary<string, object>` | ✔️ | | En función de la entrada del usuario |

### <a name="microsoftcommunicationrouterjoblabelsupdated"></a>Microsoft.Communication.RouterJobLabelsUpdated

[Volver al catálogo de eventos](#events-catalog)

```json
{
  "id": "b6d8687a-5a1a-42ae-b8b5-ff7ec338c872",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "job/{job-id}/channel/{channel-id}",
  "data": {
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "jobStatus": "Queued",
    "channelId": "FooVoiceChannelId",
    "classificationPolicyId": "test-policy",
    "queueId": "625fec06-ab81-4e60-b780-f364ed96ade1",
    "priority": 5,
    "labelsAddedOrChanged": {
      "English": "5",
      "Office": "7"
    },
    "labels": {
      "Locale": "en-us",
      "Segment": "Enterprise",
      "Token": "FooToken",
      "English": "5",
      "Office": "7"
    }
  },
  "eventType": "Microsoft.Communication.RouterJobLabelsUpdated",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:30Z"
}
```

**Lista de atributos**

| Atributo | Tipo | Nullable |Descripción | Notas |
|:--------- |:-----:|:-------:|-------------|-------|
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
| jobStatus| `enum` | ❌ | Valores posibles <ul> <li>PendingClassification</li> <li>En cola</li> <li>Asignada</li> <li>Completado</li> <li>Closed</li> <li>Canceled</li> <li>ClassificationFailed</li> </ul> |
|channelId | `string` | ❌ |
| classificationPolicyId | `string` | ✔️ | | `null` cuando se especifica `queueId` para un trabajo |
| queueId | `string` | ✔️ | | `null` cuando se especifica `classificationPolicyId` para un trabajo |
| priority | `int` | ❌ |
| labelsAddedOrChanged | `Dictionary<string, object>` | ✔️ | | Etiquetas agregadas o cambiadas en función de la entrada del usuario. |
| labels | `Dictionary<string, object>` | ✔️ | | Conjunto completo de etiquetas asociadas al trabajo. |

### <a name="microsoftcommunicationrouterjobclassificationfailed"></a>Microsoft.Communication.RouterJobClassificationFailed

[Volver al catálogo de eventos](#events-catalog)

```json
{
  "id": "b6d8687a-5a1a-42ae-b8b5-ff7ec338c872",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "job/{job-id}/channel/{channel-id}/classificationpolicy/{classificationpolicy-id}",
  "data": {
    "errors": [
      {
        "code": null,
        "message": "Classification failed due to <reason>",
        "target": null,
        "innerError": null,
        "details": null
      }
    ],
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "classificationPolicyId": "test-policy",
    "labels": {
      "Locale": "en-us",
      "Segment": "Enterprise",
      "Token": "FooToken"
    }
  },
  "eventType": "Microsoft.Communication.RouterJobClassificationFailed",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:30Z"
}
```

**Lista de atributos**

| Atributo | Tipo | Nullable |Descripción | Notas |
|:--------- |:-----:|:-------:|-------------|-------|
| errors| `List<CommunicationError>` | ❌ |
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
|channelId | `string` | ❌ |
| classificationPolicyId | `string` | ❌ | |
| labels | `Dictionary<string, object>` | ✔️ | | En función de la entrada del usuario |

### <a name="microsoftcommunicationrouterjobcompleted"></a>Microsoft.Communication.RouterJobCompleted

[Volver al catálogo de eventos](#events-catalog)

```json
{
  "id": "b6d8687a-5a1a-42ae-b8b5-ff7ec338c872",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "job/{job-id}/channel/{channel-id}/assignment/{assignment-id}",
  "data": {
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "queueId": "queue-id",
    "assignmentId": "6f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "labels": {
      "Locale": "en-us",
      "Segment": "Enterprise",
      "Token": "FooToken"
    }
    "workerId": ""
  },
  "eventType": "Microsoft.Communication.RouterJobCompleted",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:30Z"
}
```

**Lista de atributos**

| Atributo | Tipo | Nullable |Descripción | Notas |
|:--------- |:-----:|:-------:|-------------|-------|
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
|channelId | `string` | ❌ |
| queueId | `string` | ❌ | |
| assignmentId| `string` | ❌ | |
| labels | `Dictionary<string, object>` | ✔️ | | En función de la entrada del usuario |
| workerId | `string` | ❌ | |

### <a name="microsoftcommunicationrouterjobclosed"></a>Microsoft.Communication.RouterJobClosed

[Volver al catálogo de eventos](#events-catalog)

```json
{
  "id": "b6d8687a-5a1a-42ae-b8b5-ff7ec338c872",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "job/{job-id}/channel/{channel-id}/assignment/{assignment-id}",
  "data": {
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "queueId": "",
    "dispositionCode": "",
    "workerId": "",
    "assignmentId": "",
    "labels": {
      "Locale": "en-us",
      "Segment": "Enterprise",
      "Token": "FooToken"
    }
  },
  "eventType": "Microsoft.Communication.RouterJobClosed",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:30Z"
}
```

**Lista de atributos**

| Atributo | Tipo | Nullable |Descripción | Notas |
|:--------- |:-----:|:-------:|-------------|-------|
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
|channelId | `string` | ❌ |
| queueId | `string` | ❌ | |
| dispositionCode| `string` | ✔️ | | En función de la entrada del usuario |
| workerId | `string` | ❌ | |
| assignmentId | `string` | ❌ | |
| labels | `Dictionary<string, object>` | ✔️ | | En función de la entrada del usuario |

### <a name="microsoftcommunicationrouterjobcancelled"></a>Microsoft.Communication.RouterJobCancelled

[Volver al catálogo de eventos](#events-catalog)

```json
{
  "id": "b6d8687a-5a1a-42ae-b8b5-ff7ec338c872",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "job/{job-id}/channel/{channel-id}/disposition/{disposition-code}",
  "data": {
    "note": "Cancelled due to <reason>",
    "dispositionCode": "100",
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "labels": {
      "Locale": "en-us",
      "Segment": "Enterprise",
      "Token": "FooToken"
    },
    "queueId": ""
  },
  "eventType": "Microsoft.Communication.RouterJobCancelled",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:30Z"
}
```

**Lista de atributos**

| Atributo | Tipo | Nullable |Descripción | Notas |
|:--------- |:-----:|:-------:|-------------|-------|
| nota| `string` | ✔️ | | En función de la entrada del usuario |
| dispositionCode| `string` | ❌ |
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
|channelId | `string` | ❌ |
| queueId | `string` | ✔️ | | Valor distinto de NULL cuando el trabajo se cancela después de que la clasificación se haya realizado correctamente. |
| labels | `Dictionary<string, object>` | ✔️ | | En función de la entrada del usuario |

### <a name="microsoftcommunicationrouterjobexceptiontriggered"></a>Microsoft.Communication.RouterJobExceptionTriggered

[Volver al catálogo de eventos](#events-catalog)

```json
{
  "id": "1027db4a-17fe-4a7f-ae67-276c3120a29f",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "job/{job-id}/channel/{channel-id}/exceptionrule/{rulekey}",
  "data": {
    "ruleKey": "r100",
    "exceptionRuleId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "labels": {
      "Locale": "en-us",
      "Segment": "Enterprise",
      "Token": "FooToken"
    }
  },
  "eventType": "Microsoft.Communication.RouterJobExceptionTriggered",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:31Z"
}
```

**Lista de atributos**

| Atributo | Tipo | Nullable |Descripción | Notas |
|:--------- |:-----:|:-------:|-------------|-------|
| ruleKey | `string` | ❌ | |
| exceptionRuleId| `string` | ❌ |
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
| channelId | `string` | ❌ |
| labels | `Dictionary<string, object>` | ✔️ | | En función de la entrada del usuario |

### <a name="microsoftcommunicationrouterjobexceptioncleared"></a>Microsoft.Communication.RouterJobExceptionCleared

[Volver al catálogo de eventos](#events-catalog)

```json
{
  "id": "1027db4a-17fe-4a7f-ae67-276c3120a29f",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "job/{job-id}/channel/{channel-id}/exceptionrule/{rulekey}",
  "data": {
    "ruleKey": "r100",
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "labels": {
      "Locale": "en-us",
      "Segment": "Enterprise",
      "Token": "FooToken"
    }
  },
  "eventType": "Microsoft.Communication.RouterJobExceptionCleared",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:31Z"
}
```

**Lista de atributos**

| Atributo | Tipo | Nullable |Descripción | Notas |
|:--------- |:-----:|:-------:|-------------|-------|
| ruleKey | `string` | ❌ | |
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
| channelId | `string` | ❌ |
| labels | `Dictionary<string, object>` | ✔️ | | En función de la entrada del usuario |

## <a name="worker-events"></a>Eventos del trabajador

### <a name="microsoftcommunicationrouterworkerofferissued"></a>Microsoft.Communication.RouterWorkerOfferIssued

[Volver al catálogo de eventos](#events-catalog)

```json
{
  "id": "1027db4a-17fe-4a7f-ae67-276c3120a29f",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "worker/{worker-id}/job/{job-id}",
  "data": {
    "workerId": "w100",
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "queueId": "625fec06-ab81-4e60-b780-f364ed96ade1",
    "offerId": "525fec06-ab81-4e60-b780-f364ed96ade1",
    "offerTimeUtc": "2021-06-23T02:43:30.3847144Z",
    "expiryTimeUtc": "2021-06-23T02:44:30.3847674Z",
    "jobPriority": 5,
    "jobLabels": {
      "Locale": "en-us",
      "Segment": "Enterprise",
      "Token": "FooToken"
    }
  },
  "eventType": "Microsoft.Communication.RouterWorkerOfferIssued",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:31Z"
}
```

**Lista de atributos**

| Atributo | Tipo | Nullable |Descripción | Notas |
|:--------- |:-----:|:-------:|-------------|-------|
| workerId | `string` | ❌ |
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
|channelId | `string` | ❌ |
| queueId | `string` | ❌ |
| offerId| `string` | ❌ |
| offerTimeUtc | `DateTimeOffset` | ❌ |
| expiryTimeUtc| `DateTimeOffset` | ❌ |
| jobPriority| `int` | ❌ |
| jobLabels | `Dictionary<string, object>` | ✔️ | | En función de la entrada del usuario |

### <a name="microsoftcommunicationrouterworkerofferaccepted"></a>Microsoft.Communication.RouterWorkerOfferAccepted

[Volver al catálogo de eventos](#events-catalog)

```json
{
  "id": "1027db4a-17fe-4a7f-ae67-276c3120a29f",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "worker/{worker-id}/job/{job-id}",
  "data": {
    "workerId": "w100",
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "jobPriority": 5,
    "jobLabels": {
      "Locale": "en-us",
      "Segment": "Enterprise",
      "Token": "FooToken"
    },
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "queueId": "625fec06-ab81-4e60-b780-f364ed96ade1",
    "offerId": "565fec06-ab81-4e60-b780-f364ed96ade1",
    "assignmentId": "765fec06-ab81-4e60-b780-f364ed96ade1"
  },
  "eventType": "Microsoft.Communication.RouterWorkerOfferAccepted",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:31Z"
}
```

**Lista de atributos**

| Atributo | Tipo | Nullable |Descripción | Notas |
|:--------- |:-----:|:-------:|-------------|-------|
| workerId | `string` | ❌ |
| jobId| `string` | ❌ |
| jobPriority| `int` | ❌ |
| jobLabels | `Dictionary<string, object>` | ✔️ | | En función de la entrada del usuario |
| channelReference | `string` | ❌ |
|channelId | `string` | ❌ |
| queueId | `string` | ❌ |
| offerId | `string` | ❌ |
| assignmentId | `string` | ❌ |

### <a name="microsoftcommunicationrouterworkerofferdeclined"></a>Microsoft.Communication.RouterWorkerOfferDeclined

[Volver al catálogo de eventos](#events-catalog)

```json
{
  "id": "1027db4a-17fe-4a7f-ae67-276c3120a29f",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "worker/{worker-id}/job/{job-id}",
  "data": {
    "workerId": "w100",
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "queueId": "625fec06-ab81-4e60-b780-f364ed96ade1",
    "offerId": "565fec06-ab81-4e60-b780-f364ed96ade1",
  },
  "eventType": "Microsoft.Communication.RouterWorkerOfferDeclined",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:31Z"
}
```

**Lista de atributos**

| Atributo | Tipo | Nullable |Descripción | Notas |
|:--------- |:-----:|:-------:|-------------|-------|
| workerId | `string` | ❌ |
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
|channelId | `string` | ❌ |
| queueId | `string` | ❌ |
| offerId | `string` | ❌ |

### <a name="microsoftcommunicationrouterworkerofferrevoked"></a>Microsoft.Communication.RouterWorkerOfferRevoked

[Volver al catálogo de eventos](#events-catalog)

```json
{
  "id": "1027db4a-17fe-4a7f-ae67-276c3120a29f",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "worker/{worker-id}/job/{job-id}",
  "data": {
    "offerId": "565fec06-ab81-4e60-b780-f364ed96ade1",
    "workerId": "w100",
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "queueId": "625fec06-ab81-4e60-b780-f364ed96ade1"
  },
  "eventType": "Microsoft.Communication.RouterWorkerOfferRevoked",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:31Z"
}
```

**Lista de atributos**

| Atributo | Tipo | Nullable |Descripción | Notas |
|:--------- |:-----:|:-------:|-------------|-------|
| offerId | `string` | ❌ |
| workerId | `string` | ❌ |
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
|channelId | `string` | ❌ |
| queueId | `string` | ❌ |

### <a name="microsoftcommunicationrouterworkerofferexpired"></a>Microsoft.Communication.RouterWorkerOfferExpired

[Volver al catálogo de eventos](#events-catalog)

```json
{
  "id": "1027db4a-17fe-4a7f-ae67-276c3120a29f",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "worker/{worker-id}/job/{job-id}",
  "data": {
    "offerId": "565fec06-ab81-4e60-b780-f364ed96ade1",
    "workerId": "w100",
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "queueId": "625fec06-ab81-4e60-b780-f364ed96ade1"
  },
  "eventType": "Microsoft.Communication.RouterWorkerOfferExpired",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:31Z"
}
```

**Lista de atributos**

| Atributo | Tipo | Nullable |Descripción | Notas |
|:--------- |:-----:|:-------:|-------------|-------|
| offerId | `string` | ❌ |
| workerId | `string` | ❌ |
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
|channelId | `string` | ❌ |
| queueId | `string` | ❌ |

### <a name="microsoftcommunicationrouterworkerregistered"></a>Microsoft.Communication.RouterWorkerRegistered

[Volver al catálogo de eventos](#events-catalog)

```json
{
  "id": "1027db4a-17fe-4a7f-ae67-276c3120a29f",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "worker/{worker-id}",
  "data": {
    "workerId": "worker3",
    "totalCapacity": 100,
    "queueAssignments": [
      {
        "id": "MyQueueId2",
        "name": "Queue 3",
        "labels": {
          "Language": "en",
          "Product": "Office",
          "Geo": "NA"
        }
      }
    ],
    "labels": {
      "x": "111",
      "y": "111"
    },
    "channelConfigurations": [
      {
        "channelId": "FooVoiceChannelId",
        "capacityCostPerJob": 10
      }
    ]
  },
  "eventType": "Microsoft.Communication.RouterWorkerRegistered",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:31Z"
}
```

**Lista de atributos**

| Atributo | Tipo | Nullable |Descripción | Notas |
|:--------- |:-----:|:-------:|-------------|-------|
| workerId | `string` | ❌ |
| totalCapacity | `int` | ❌ |
| queueAssignments| `List<QueueInfo>` | ❌ |
| labels| `Dictionary<string, object>` | ✔️ | | En función de la entrada del usuario |
| channelConfigurations| `List<ChannelConfiguration>` | ❌ |

### <a name="microsoftcommunicationrouterworkerderegistered"></a>Microsoft.Communication.RouterWorkerDeregistered

[Volver al catálogo de eventos](#events-catalog)

```json
{
  "id": "1027db4a-17fe-4a7f-ae67-276c3120a29f",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "worker/{worker-id}",
  "data": {
    "workerId": "worker3",
  },
  "eventType": "Microsoft.Communication.RouterWorkerDeregistered",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:31Z"
}
```
**Lista de atributos**

| Atributo | Tipo | Nullable |Descripción | Notas |
|:--------- |:-----:|:-------:|-------------|-------|
| workerId | `string` | ❌ |

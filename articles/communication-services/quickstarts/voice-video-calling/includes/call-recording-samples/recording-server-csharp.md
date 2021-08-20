---
title: Archivo de inclusión
description: Archivo de inclusión
services: azure-communication-services
author: ravithanneeru
manager: joseys
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: joseys
ms.openlocfilehash: 17363ba1c41c7c5046b769c7a03b3e05b97a08e6
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114339737"
---
## <a name="sample-code"></a>Código de ejemplo
Busque el código finalizado de este inicio rápido en [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/ServerRecording).

## <a name="prerequisites"></a>Requisitos previos

- Cree de una cuenta de Azure con una suscripción activa. Para más información, consulte [Creación de una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js (12.18.4 y posterior)](https://nodejs.org/en/download/).
- [Visual Studio (2019 y posterior)](https://visualstudio.microsoft.com/vs/).
- [.NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1) (asegúrese de instalar la versión que se corresponda con su instancia de Visual Studio, 32 o 64 bits).
- Cree un recurso de Azure Communication Services. Para más información, consulte [Creación de un recurso de Azure Communication Services](https://docs.microsoft.com/azure/communication-services/quickstarts/create-communication-resource). Deberá registrar la **cadena de conexión** del recurso para esta guía de inicio rápido.
- Un contenedor y una cuenta de Azure Storage. Para obtener más información, consulte [Crear una cuenta de almacenamiento](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal). Deberá registrar la **cadena de conexión** de almacenamiento y el **nombre del contenedor** para este inicio rápido.
- Un webhook [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview).

## <a name="object-model"></a>Modelo de objetos

Las siguientes clases controlan algunas de las características principales de las aplicaciones de servidor de grabación de C#.

| Nombre                                  | Descripción                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallingServerClient | Esta clase es necesaria para la funcionalidad de grabación. Cree una instancia de CallingServerClient mediante la cadena de conexión de los recursos de ACS y úsela para iniciar/detener y pausar/reanudar una grabación de llamadas. |

## <a name="getting-servercallid-as-a-requirement-for-call-recording-server-apis"></a>Obtención de serverCallId como requisito para las API de servidor de grabación de llamadas

> [!NOTE]
> Esta API se ofrece a los desarrolladores como versión preliminar y puede cambiar en función de los comentarios que recibamos. No utilice esta API en un entorno de producción. Para usar esta API, utilice la versión "beta" del SDK web de llamada de ACS. Hay disponible un ejemplo de cliente con flujos de grabación en [GitHub](https://github.com/Azure-Samples/communication-services-web-calling-hero/tree/public-preview).

La grabación de llamadas es una característica extendida de la llamada a la API principal. Primero debe obtener el objeto de API de la característica de grabación:

```JavaScript
const callRecordingApi = call.api(Features.Recording);
```

Suscríbase a los cambios de grabación:

```JavaScript
const isRecordingActiveChangedHandler = () => {
  console.log(callRecordingApi.isRecordingActive);
};

callRecordingApi.on('isRecordingActiveChanged', isRecordingActiveChangedHandler);
```

Obtenga el identificador de llamada del servidor que se puede usar para iniciar, detener, pausar y reanudar sesiones de grabación:

Una vez conectada la llamada, use el método `getServerCallId` para obtener el identificador de llamada del servidor.

```JavaScript
callAgent.on('callsUpdated', (e: { added: Call[]; removed: Call[] }): void => {
    e.added.forEach((addedCall) => {
        addedCall.on('stateChanged', (): void => {
            if (addedCall.state === 'Connected') {
                addedCall.info.getServerCallId().then(result => {
                    dispatch(setServerCallId(result));
                }).catch(err => {
                    console.log(err);
                });
            }
        });
    });
});
```

## <a name="create-a-calling-server-client"></a>Creación de un cliente del servidor de llamadas

Para crear un cliente del servidor de llamadas, usará su cadena de conexión Azure Communication Services y la pasará al objeto de cliente del servidor de llamadas.

```csharp
CallingServerClient callingServerClient = new CallingServerClient("<Resource_Connection_String>");
```

## <a name="start-recording-session-using-startrecordingasync-server-api"></a>Inicio de la sesión de grabación mediante la API de servidor "StartRecordingAsync"

Use el identificador de llamada del servidor recibido durante el inicio de una llamada.

```csharp
var startRecordingResponse = await callingServerClient.InitializeServerCall("<servercallid>").StartRecordingAsync("<callbackuri>").ConfigureAwait(false);
```
La respuesta de la API `StartRecordingAsync` contiene el identificador de grabación de la sesión de grabación.

## <a name="stop-recording-session-using-stoprecordingasync-server-api"></a>Detención de la sesión de grabación mediante la API de servidor "StopRecordingAsync"

Use el identificador de grabación recibido en respuesta a `StartRecordingAsync`.

```csharp
 var stopRecording = await callingServerClient.InitializeServerCall("<servercallid>").StopRecordingAsync("<recordingid>").ConfigureAwait(false);
```

## <a name="pause-recording-session-using-pauserecordingasync-server-api"></a>Pausa de la sesión de grabación mediante la API de servidor "PauseRecordingAsync"

Use el identificador de grabación recibido en respuesta a `StartRecordingAsync`.

```csharp
var pauseRecording = await callingServerClient.InitializeServerCall("<servercallid>").PauseRecordingAsync("<recordingid>");
```

## <a name="resume-recording-session-using-resumerecordingasync-server-api"></a>Reanudación de la sesión de grabación mediante la API de servidor "ResumeRecordingAsync"

Use el identificador de grabación recibido en respuesta a `StartRecordingAsync`.

```csharp
var resumeRecording = await callingServerClient.InitializeServerCall("<servercallid>").ResumeRecordingAsync("<recordingid>");
```

## <a name="download-recording-file-using-downloadstreamingasync-server-api"></a>Descarga del archivo de grabación mediante la API de servidor "DownloadStreamingAsync"

Use un webhook [Azure Event Grid](../../../../../event-grid/overview.md) u otra acción desencadenada debería utilizarse para notificar sus servicios cuando los elementos multimedia grabados ya se puedan descargar.

A continuación encontrará un ejemplo del esquema de eventos.

```
{
    "id": string, // Unique guid for event
    "topic": string, // Azure Communication Services resource id
    "subject": string, // /recording/call/{call-id}
    "data": {
        "recordingStorageInfo": {
            "recordingChunks": [
                {
                    "documentId": string, // Document id for retrieving from AMS storage
                    "contentLocation": string, //ACS URL where the content is located
                    "metadataLocation": string, // ACS URL where the metadata for this chunk is located
                    "index": int, // Index providing ordering for this chunk in the entire recording
                    "endReason": string, // Reason for chunk ending: "SessionEnded", "ChunkMaximumSizeExceeded”, etc.
                }
            ]
        },
        "recordingStartTime": string, // ISO 8601 date time for the start of the recording
        "recordingDurationMs": int, // Duration of recording in milliseconds
        "sessionEndReason": string // Reason for call ending: "CallEnded", "InitiatorLeft”, etc.
    },
    "eventType": string, // "Microsoft.Communication.RecordingFileStatusUpdated"
    "dataVersion": string, // "1.0"
    "metadataVersion": string, // "1"
    "eventTime": string // ISO 8601 date time for when the event was created
}
```

Use la API `DownloadStreamingAsync` para descargar los elementos multimedia grabados.

```csharp
var recordingDownloadUri = new Uri(downloadLocation);
var response = callingServerClient.DownloadStreamingAsync(recordingDownloadUri);
```
El elemento downloadLocation de la grabación se puede recuperar del atributo `contentLocation` de `recordingChunk`. El método `DownloadStreamingAsync` devuelve una respuesta de tipo `Response<Stream>`, que contiene el contenido descargado.
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
ms.openlocfilehash: e29973b55d3f74942f6d85fa28209b73773ad717
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121802807"
---
## <a name="sample-code"></a>Código de ejemplo
Busque el código finalizado de este inicio rápido en [GitHub](https://github.com/Azure-Samples/communication-services-java-quickstarts/tree/main/ServerRecording).

## <a name="prerequisites"></a>Requisitos previos

- Cree una cuenta de Azure con una suscripción activa. Para más información, consulte [Creación de una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Node.js (12.18.4 y posterior)](https://nodejs.org/en/download/)
- [Kit de desarrollo de Java (JDK)](/azure/developer/java/fundamentals/java-jdk-install), versión 11 o posterior.
- [Apache Maven](https://maven.apache.org/download.cgi).
- [Marco Spring Boot, versión 2.5.0](https://spring.io/projects/spring-boot)
- Cree un recurso de Azure Communication Services. Para más información, consulte [Creación de un recurso de Azure Communication Services](../../../create-communication-resource.md). Deberá registrar la **cadena de conexión** del recurso para esta guía de inicio rápido.
- Un contenedor y una cuenta de Azure Storage. Para obtener más información, consulte [Crear una cuenta de almacenamiento](../../../../../storage/common/storage-account-create.md?tabs=azure-portal). Deberá registrar la **cadena de conexión** y el **nombre del contenedor** para este inicio rápido.
- Un webhook [Azure Event Grid](../../../../../event-grid/overview.md).

## <a name="object-model"></a>Modelo de objetos

Las siguientes clases controlan algunas de las características principales de las aplicaciones de servidor de grabación de Java.

| Nombre                                  | Descripción                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallingServerClientBuilder | Esta clase se usa para crear una instancia de CallingServerClient.|
| CallingServerClient | Esta clase es necesaria para la funcionalidad de llamadas. Se obtiene una instancia por medio de CallingServerClientBuilder y se usa para iniciar o finalizar una llamada, reproducir o cancelar audio y agregar o quitar participantes. |

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

Para crear un cliente del servidor de llamadas, usará la cadena de conexión Communication Services y la pasará al objeto de cliente del servidor de llamadas.

```java
NettyAsyncHttpClientBuilder httpClientBuilder = new NettyAsyncHttpClientBuilder();
CallingServerClientBuilder builder = new CallingServerClientBuilder().httpClient(httpClientBuilder.build())
 .connectionString(connectionString);
 callingServerClient = builder.buildClient();
```

## <a name="start-recording-session-using-startrecordingwithresponse-server-api"></a>Inicio de la sesión de grabación mediante la API de servidor "startRecordingWithResponse"

Use el identificador de llamada de servidor recibido durante el inicio de la llamada.

```java
URI recordingStateCallbackUri = new URI("<CallbackUri>");

Response<StartCallRecordingResult> response = this.callingServerClient.initializeServerCall("<serverCallId>")
.startRecordingWithResponse(String.valueOf(recordingStateCallbackUri),null);
```
La respuesta de la API `startRecordingWithResponse` contiene el identificador de grabación de la sesión de grabación.

## <a name="stop-recording-session-using-stoprecordingwithresponse-server-api"></a>Detención de la sesión de grabación mediante la API de servidor "stopRecordingWithResponse"

Use el identificador de grabación recibido en respuesta a `startRecordingWithResponse`.

```java
Response<Void> response = this.callingServerClient.initializeServerCall(serverCallId)
.stopRecordingWithResponse(recordingId, null);
```

## <a name="pause-recording-session-using-pauserecordingwithresponse-server-api"></a>Pausa de la sesión de grabación mediante la API de servidor "pauseRecordingWithResponse"

Use el identificador de grabación recibido en respuesta a `startRecordingWithResponse`.

```java
Response<Void> response = this.callingServerClient.initializeServerCall(serverCallId)
.pauseRecordingWithResponse(recordingId, null);
```

## <a name="resume-recording-session-using-resumerecordingwithresponse-server-api"></a>Reanudación de la sesión de grabación mediante la API de servidor "resumeRecordingWithResponse"

Use el identificador de grabación recibido en respuesta a `startRecordingWithResponse`.

```java
Response<Void> response = this.callingServerClient.initializeServerCall(serverCallId)
.resumeRecordingWithResponse(serverCallId, null);
```

## <a name="download-recording-file-using-downloadtowithresponse-server-api"></a>Descarga del archivo de grabación mediante la API de servidor "downloadToWithResponse"

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
Use el método `downloadToWithResponse` de la clase `CallingServerClient` para descargar los elementos multimedia grabados. El método `downloadToWithResponse` admite los siguientes parámetros:

- `contentLocation`: dirección URL de ACS donde se encuentra el contenido.
- `destinationPath`: ubicación del archivo.
- `parallelDownloadOptionss`: un objeto ParallelDownloadOptions opcional para modificar cómo funcionará la descarga en paralelo.
- `overwrite`: es true para sobrescribir el archivo si existe.
- `context`: contexto que representa el contexto de solicitud.

```Java
Boolean overwrite = true;
ParallelDownloadOptions parallelDownloadOptions = null;
Context context = null;

String filePath = String.format(".\\%s.%s", documentId, fileType);
Path destinationPath = Paths.get(filePath);

Response<Void> downloadResponse = callingServerClient.downloadToWithResponse(contentLocation, destinationPath, parallelDownloadOptions, overwrite, context);
```
Los identificadores de documento y ubicación del contenido para los archivos de grabación se pueden recuperar de los campos `contentLocation` y `documentId` respectivamente, para cada `recordingChunk`.
---
title: Archivo de inclusión
description: C#
services: azure-communication-services
author: ravithanneeru
manager: joseys
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: joseys
ms.openlocfilehash: 6294759038112d5ca9b87818816ab4bb24d0f613
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114473205"
---
## <a name="prerequisites"></a>Requisitos previos

Antes de comenzar, compruebe lo siguiente:

- Cree de una cuenta de Azure con una suscripción activa. Para más información, consulte [Creación de una cuenta gratuita](https://azure.microsoft.com/free/).
- [Visual Studio (2019 y posterior)](https://visualstudio.microsoft.com/vs/).
- [.NET Framework 4.7.2](https://dotnet.microsoft.com/download/dotnet-framework/net472) (asegúrese de instalar la versión que se corresponda con su instancia de Visual Studio, de 32 o 64 bits).
- Cree un recurso de Azure Communication Services. Para más información, consulte [Creación de un recurso de Azure Communication Services](https://docs.microsoft.com/azure/communication-services/quickstarts/create-communication-resource). Debe registrar la **cadena de conexión** del recurso para este ejemplo.
- Obtenga un número de teléfono para el nuevo recurso de Azure Communication Services. Para obtener detalles, vea [Obtención de un número de teléfono](https://docs.microsoft.com/azure/communication-services/quickstarts/telephony-sms/get-phone-number?pivots=platform-azp).
- Descargue e instale [ngrok](https://www.ngrok.com/download). Cuando el ejemplo se ejecuta en local, ngrok permite la recepción de todos los eventos.
- (Opcional) Cree un recurso de Voz de Azure para generar mensajes personalizados que la aplicación reproduzca. Siga [estas instrucciones](../../../../../cognitive-services/speech-service/overview.md#try-the-speech-service-for-free) para crear el recurso.

> [!NOTE]
> Puede buscar el código finalizado de este inicio rápido en [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/OutboundCallReminder). En este ejemplo se usa el SDK de Voz de Azure Cognitive Services. La descarga del SDK de Voz de Azure Cognitive Services supone la aceptación de su [licencia](https://aka.ms/csspeech/license201809).

## <a name="object-model"></a>Modelo de objetos

Las siguientes clases controlan algunas de las características principales del SDK del servidor de llamadas de Azure Communication para C#.

| Nombre                                  | Descripción                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallingServerClient | Esta clase es necesaria para la funcionalidad de llamadas. Puede crear una instancia de CallingServerClient mediante la cadena de conexión de los recursos de Communication Services y usarla para iniciar o finalizar una llamada, reproducir o cancelar audio y agregar o quitar participantes. |
| CommunicationIdentityClient | Esta clase es necesaria para crear o eliminar una identidad de usuario de comunicación. |

## <a name="create-a-call-client"></a>Creación de un cliente de llamada

Para crear un cliente de llamada, usará la cadena de conexión de Communication Services y la pasará al objeto de cliente de llamada.

```csharp
this.CallClient = new CallingServerClient("<Connection_String>");
```

## <a name="create-user-identity"></a>Creación de una identidad de usuario

Use la cadena de conexión del recurso de Communication Services para crear un objeto `CommunicationIdentityClient` y, luego, use el método `CreateUserAsync` para crear una identidad de usuario de origen.

```csharp
var client = new CommunicationIdentityClient("<Connection_String>");
var user = await client.CreateUserAsync().ConfigureAwait(false);
```

## <a name="delete-user-identity"></a>Eliminación de una identidad de usuario

Use el método `DeleteUserAsync` del objeto `CommunicationIdentityClient` para eliminar una identidad de usuario:

```csharp
var client = new CommunicationIdentityClient("<Connection_String>");
await client.DeleteUserAsync(new CommunicationUserIdentifier(source)).ConfigureAwait(false);
```

## <a name="create-a-call"></a>Creación de una llamada

Use el método `CreateCallConnectionAsync` del objeto `CallingServerClient` para iniciar una llamada. Estos son los parámetros del método `CreateCallConnectionAsync`:
- `source`, es la identidad de usuario de origen del autor de la llamada de tipo `CommunicationIdentifier`.
- `targets`, es la lista de identidades de destino de tipo `IEnumerable<CommunicationIdentifier>`.
- `options`, son las opciones de la llamada de tipo `CreateCallOptions`. Estos son los parámetros del método `CreateCallOptions`:

    - `callbackUri`, es el URI de devolución de llamada de la aplicación.
    - `requestedModalities`, es la modalidad de llamada de la solicitud. El tipo es `IEnumerable<MediaType>`. Los valores pueden ser Audio o Vídeo.
    - `requestedCallEvents`, son los eventos de devolución de llamada solicitados. Este es el tipo de `IEnumerable<EventSubscriptionType>`. Use este parámetro para suscribirse a eventos `EventSubscriptionType.ParticipantsUpdated` y `EventSubscriptionType.DtmfReceived`.

- `cancellationToken`, es el token de cancelación. El tipo es `CancellationToken`.

```csharp
var source = new CommunicationUserIdentifier(callConfiguration.SourceIdentity);
var target = new PhoneNumberIdentifier(targetPhoneNumber);
var createCallOption = new CreateCallOptions(
    new Uri(callConfiguration.AppCallbackUrl),
    new List<MediaType> { MediaType.Audio },
    new List<EventSubscriptionType> { EventSubscriptionType.ParticipantsUpdated, EventSubscriptionType.DtmfReceived }
    );
createCallOption.AlternateCallerId = new PhoneNumberIdentifier(callConfiguration.SourcePhoneNumber);

Logger.LogMessage(Logger.MessageType.INFORMATION, "Performing CreateCall operation");
var call = await callClient.CreateCallConnectionAsync(source,
    new List<CommunicationIdentifier>() { target },
    createCallOption, reportCancellationToken)
    .ConfigureAwait(false);
```

El método `CreateCallConnectionAsync` devuelve un objeto `CallConnection`, que se puede usar para otras operaciones de llamada, como la reproducción de audio, la cancelación de audio y la finalización de llamada.

## <a name="play-audio"></a>Reproducir audio

Una vez creada la conexión de una llamada, puede usar el método `PlayAudioAsync` del objeto `CallConnection` para reproducir un mensaje de audio para el destinatario. El método `PlayAudioAsync` admite los siguientes parámetros:

- `options`, obligatorio, son las opciones de reproducción de audio. El tipo es `PlayAudioOptions`. El objeto `PlayAudioOptions` admite los siguientes parámetros:
    - `AudioFileUri`, obligatorio, es el URI del archivo de audio que tiene el mensaje que se va a reproducir.
    - `OperationContext`, obligatorio, es el identificador único del contexto de la solicitud.
    - `Loop`, opcional, se establece en true para repetir el mensaje de audio.
- `cancellationToken`, es el token de cancelación. El tipo es `CancellationToken`.

```csharp
// Preparing data for request
var playAudioRequest = new PlayAudioOptions()
{
    AudioFileUri = new Uri(callConfiguration.AudioFileUrl),
    OperationContext = Guid.NewGuid().ToString(),
    Loop = true,
};

Logger.LogMessage(Logger.MessageType.INFORMATION, "Performing PlayAudio operation");
var response = await callConnection.PlayAudioAsync(playAudioRequest, reportCancellationToken).ConfigureAwait(false);
```

El método `PlayAudioAsync` devuelve `PlayAudioResponse`, que se puede usar para obtener `OperationStatus`. `OperationStatus` podría tener los siguientes valores: `NotStarted`, `Running`, `Completed` o `Failed`.

```csharp
Logger.LogMessage(Logger.MessageType.INFORMATION, $"Play Audio state: {response.Value.Status}");
```

## <a name="add-a-participant-to-the-call"></a>Incorporación de un participante a la llamada

Use el método `AddParticipantAsync` del objeto `CallConnection` para agregar un participante a la llamada. El método `AddParticipantAsync` admite los siguientes parámetros:

- `participant`, es el identificador del participante, de tipo `CommunicationUserIdentifier` o `PhoneNumberIdentifier`.
- `alternateCallerId`, es el identificador del autor de la llamada de origen.
- `operationContext`, es el identificador único del contexto de la solicitud.
- `cancellationToken`, opcional, es el token de cancelación.


```csharp
// Preparing data for request
var operationContext = Guid.NewGuid().ToString();

if (identifierKind == CommunicationIdentifierKind.UserIdentity)
{
    var response = await callConnection.AddParticipantAsync(new CommunicationUserIdentifier(addedParticipant), null, operationContext).ConfigureAwait(false);
    Logger.LogMessage(Logger.MessageType.INFORMATION, $"PlayAudioAsync response --> {response}");
}
else if (identifierKind == CommunicationIdentifierKind.PhoneIdentity)
{
    var response = await callConnection.AddParticipantAsync(new PhoneNumberIdentifier(addedParticipant), callConfiguration.SourcePhoneNumber, operationContext).ConfigureAwait(false);
    Logger.LogMessage(Logger.MessageType.INFORMATION, $"PlayAudioAsync response --> {response}");
}
```

## <a name="cancel-media-processing"></a>Cancelar procesamiento multimedia

Use el método `CancelAllMediaOperationsAsync` del objeto `CallConnection` para cancelar la operación de reproducción de audio. El método `CancelAllMediaOperationsAsync` admite los siguientes parámetros:

- `operationContext`, es el identificador único del contexto de la solicitud.
- `reportCancellationToken`, es el token de cancelación. El tipo es `CancellationToken`.

```csharp
var operationContext = Guid.NewGuid().ToString();
var response = await callConnection.CancelAllMediaOperationsAsync(operationContext, reportCancellationToken).ConfigureAwait(false);
```

## <a name="hang-up-the-call"></a>Finalización de la llamada

Use el método `HangupAsync` del objeto `CallConnection` para finalizar la llamada. El método `HangupAsync` admite los siguientes parámetros:

- `reportCancellationToken`, es el token de cancelación. El tipo es `CancellationToken`.

```csharp
var hangupResponse = await callConnection.HangupAsync(reportCancellationToken).ConfigureAwait(false);
```
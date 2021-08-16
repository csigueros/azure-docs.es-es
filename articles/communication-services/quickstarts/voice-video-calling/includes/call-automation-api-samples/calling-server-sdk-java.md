---
title: Archivo de inclusión
description: Automatización de llamadas de Java
services: azure-communication-services
author: ravithanneeru
manager: joseys
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: joseys
ms.openlocfilehash: dc674a7e013ed39bd04161cb7e96dc969eaf47eb
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114473222"
---
## <a name="prerequisites"></a>Requisitos previos
Antes de comenzar, compruebe lo siguiente:
- Cree de una cuenta de Azure con una suscripción activa. Para más información, consulte [Creación de una cuenta gratuita](https://azure.microsoft.com/free/).
- [Java Development Kit (JDK)](/azure/developer/java/fundamentals/java-jdk-install), versión 11 o posterior.
- [Apache Maven](https://maven.apache.org/download.cgi).
- Cree un recurso de Azure Communication Services. Para más información, consulte [Creación de un recurso de Azure Communication Services](https://docs.microsoft.com/azure/communication-services/quickstarts/create-communication-resource). Debe registrar la **cadena de conexión** del recurso para este ejemplo.
- Obtenga un número de teléfono para el nuevo recurso de Azure Communication Services. Para obtener detalles, vea [Obtención de un número de teléfono](https://docs.microsoft.com/azure/communication-services/quickstarts/telephony-sms/get-phone-number?pivots=platform-azp).
- Descargue e instale [ngrok](https://www.ngrok.com/download). Cuando el ejemplo se ejecuta en local, ngrok permite la recepción de todos los eventos.
- (Opcional) Cree un recurso de Voz de Azure para generar mensajes personalizados que la aplicación reproduzca. Siga [estas instrucciones](../../../../../cognitive-services/speech-service/overview.md#try-the-speech-service-for-free) para crear el recurso.

> [!NOTE]
> Puede buscar el código finalizado de este inicio rápido en [GitHub](https://github.com/Azure-Samples/communication-services-java-quickstarts/tree/main/OutboundCallReminder). En este ejemplo se usa el SDK de Voz de Azure Cognitive Services. La descarga del SDK de Voz de Azure Cognitive Services supone la aceptación de su [licencia](https://aka.ms/csspeech/license201809).

## <a name="add-the-package-references-for-the-calling-server-sdk"></a>Adición de las referencias de paquete del SDK del servidor de llamadas

En el archivo POM, haga referencia al paquete `azure-communication-callingserver` con las API de llamadas:

```java
<dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-communication-callingserver</artifactId>
      <version>1.0.0-beta.2</version>
</dependency>
```

Para crear la identidad de usuario, la aplicación debe hacer referencia al paquete `azure-communication-identity`:

```java
  <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-communication-identity</artifactId>
      <version>1.1.1</version>
      <exclusions>
        <exclusion>
          <groupId>com.azure</groupId>
          <artifactId>azure-communication-common</artifactId>
        </exclusion>
      </exclusions>
    </dependency>
```

## <a name="object-model"></a>Modelo de objetos

Las siguientes clases controlan algunas de las características principales del SDK del servidor de llamadas de Azure Communication para Java.

| Nombre                                  | Descripción                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallingServerClientBuilder | Esta clase se usa para crear una instancia de CallingServerClient.|
| CallingServerClient | Esta clase es necesaria para la funcionalidad de llamadas. Se obtiene una instancia por medio de CallingServerClientBuilder y se usa para iniciar o finalizar una llamada, reproducir o cancelar audio y agregar o quitar participantes. |
| CommunicationIdentityClient | Esta clase es necesaria para crear o eliminar una identidad de usuario de comunicación. |

## <a name="create-a-call-client"></a>Creación de un cliente de llamada

Se deben pasar una cadena de conexión de Communication Services y un objeto httpClient a `CallingServerClientBuilder` por medio de las funciones `connectionString()` y `httpClient()` respectivamente. Para crear un objeto de cliente de llamada, use la función `buildClient()` del objeto `CallingServerClientBuilder`.

```java
NettyAsyncHttpClientBuilder httpClientBuilder = new NettyAsyncHttpClientBuilder();
CallingServerClientBuilder callClientBuilder = new CallingServerClientBuilder().httpClient(httpClientBuilder.build())
        .connectionString(this.callConfiguration.ConnectionString);

this.callingServerClient = callClientBuilder.buildClient();
```

## <a name="create-user-identity"></a>Creación de una identidad de usuario

Use el objeto `CommunicationIdentityClientBuilder` y establezca la cadena de conexión del recurso de Communication Services por medio de la función `connectionString()` para crear un objeto `CommunicationIdentityClient` y, luego, use la función `createUser` para crear una identidad de usuario de origen.

```java
CommunicationIdentityClient client = new CommunicationIdentityClientBuilder()
        .connectionString("<Connection_String>")
        .buildClient();
CommunicationUserIdentifier user = client.createUser();
```

## <a name="delete-user-identity"></a>Eliminación de una identidad de usuario

Use la función `deleteUser` del objeto `CommunicationIdentityClient` para eliminar una identidad de usuario:

```java
CommunicationIdentityClient client = new CommunicationIdentityClientBuilder()
        .connectionString("<Connection_String>")
        .buildClient();
client.deleteUser(new CommunicationUserIdentifier(source));    
```

## <a name="create-a-call"></a>Creación de una llamada

Use el método `createCallConnectionWithResponse` del objeto `CallingServerClient` para iniciar una llamada. El método `createCallConnectionWithResponse` admite los siguientes parámetros:
- `source`, es la identidad de usuario de origen del autor de la llamada de tipo `CommunicationUserIdentifier`.
- `targets`, es la lista de identidades de destino de tipo `Iterable<CommunicationIdentifier>`.
- `options`, son las opciones de la llamada de tipo `CreateCallOptions`. Estos son los parámetros del método `CreateCallOptions`:

    - `callbackUri`, es el URI de devolución de llamada de la aplicación.
    - `requestedModalities`, es la modalidad de llamada de la solicitud. El tipo es `Iterable<MediaType>`. Los valores pueden ser Audio o Vídeo.
    - `requestedCallEvents`, son los eventos de devolución de llamada solicitados de tipo `Iterable<EventSubscriptionType>`. Use este parámetro para suscribirse a eventos `EventSubscriptionType.PARTICIPANTS_UPDATED` y `EventSubscriptionType.DTMF_RECEIVED`.

- Establezca el identificador del autor de la llamada de origen como identificador de autor de llamada alternativo mediante la función `setAlternateCallerId()` del objeto `CreateCallOptions`.

```java
CommunicationUserIdentifier source = new CommunicationUserIdentifier(this.callConfiguration.SourceIdentity);
PhoneNumberIdentifier target = new PhoneNumberIdentifier(targetPhoneNumber);
List<MediaType> callModality = new ArrayList<>() { {add(MediaType.AUDIO);} };
List<EventSubscriptionType> eventSubscriptionType = new ArrayList<>() {
        {add(EventSubscriptionType.PARTICIPANTS_UPDATED); add(EventSubscriptionType.DTMF_RECEIVED);}};

CreateCallOptions createCallOption = new CreateCallOptions(this.callConfiguration.appCallbackUrl,
        callModality, eventSubscriptionType);
createCallOption.setAlternateCallerId(new PhoneNumberIdentifier(this.callConfiguration.sourcePhoneNumber));

Logger.logMessage(Logger.MessageType.INFORMATION,"Performing CreateCall operation");

List<CommunicationIdentifier> targets = new ArrayList<>() { {add(target);} };

Response<CallConnection> response = this.callingServerClient.createCallConnectionWithResponse(source, targets, createCallOption, null);
callConnection = response.getValue();
```

El método `createCallConnectionWithResponse` devuelve el objeto `Response<CallConnection>`, que se usa para otras operaciones de llamada, como la reproducción de audio, la cancelación de audio y la finalización de llamada.

## <a name="play-audio"></a>Reproducir audio

Una vez creada una llamada, puede usar el método `playAudioWithResponse` del objeto `CallConnection` para reproducir un mensaje de audio para el destinatario. El método `playAudioWithResponse` admite los siguientes parámetros:

- `audioFileUri`, es el URI del archivo de audio que tiene el mensaje que se va a reproducir.
- `playAudioOptions`, son las opciones de reproducción de audio. El tipo es `PlayAudioOptions`. El objeto `PlayAudioOptions` admite los siguientes parámetros:
    - `loop`, se establece en true para repetir el mensaje de audio.
    - `audioFileId`, identificador del medio en AudioFileUri; con él se almacena el medio en caché.
    - `operationContext`, es el identificador único del contexto de la solicitud.

```java
// Preparing data for request
String audioFileUri = callConfiguration.audioFileUrl;
Boolean loop = true;
String operationContext = UUID.randomUUID().toString();
String audioFileId = UUID.randomUUID().toString();
PlayAudioOptions playAudioOptions = new PlayAudioOptions();
playAudioOptions.setLoop(loop);
playAudioOptions.setAudioFileId(audioFileId);
playAudioOptions.setOperationContext(operationContext);

Logger.logMessage(Logger.MessageType.INFORMATION, "Performing PlayAudio operation");
Response<PlayAudioResult> playAudioResponse = this.callConnection.playAudioWithResponse(audioFileUri, playAudioOptions, null);

PlayAudioResult response = playAudioResponse.getValue();
```

El método `playAudio` devuelve `Response<PlayAudioResult>`, que se puede usar para obtener `OperationStatus` con el método `getStatus()`. `OperationStatus` podría tener los siguientes valores: `NotStarted`, `Running`, `Completed` o `Failed`.

```java
Logger.logMessage(Logger.MessageType.INFORMATION, "playAudioWithResponse -- > " + GetResponse(playAudioResponse) +
", Id: " + response.getOperationId() + ", OperationContext: " + response.getOperationContext() + ", OperationStatus: " +
response.getStatus().toString());
```

## <a name="add-a-participant-to-the-call"></a>Incorporación de un participante a la llamada

Use el método `addParticipantWithResponse` del objeto `CallConnection` para agregar un participante a la llamada. El método `addParticipantWithResponse` admite los siguientes parámetros:

- `participant`, es el identificador del participante, de tipo `CommunicationUserIdentifier` o `PhoneNumberIdentifier`.
- `alternateCallerId`, es el identificador del autor de la llamada de origen.
- `operationContext`, es el identificador único del contexto de la solicitud.

```java
// Preparing data for request
CommunicationIdentifier participant = null;
String operationContext = UUID.randomUUID().toString();

if (identifierKind == CommunicationIdentifierKind.UserIdentity) {
    participant = new CommunicationUserIdentifier(addedParticipant);

} else if (identifierKind == CommunicationIdentifierKind.PhoneIdentity) {
    participant = new PhoneNumberIdentifier(addedParticipant);
}

Response<AddParticipantResult> response = callConnection.addParticipantWithResponse(participant, this.callConfiguration.sourcePhoneNumber, operationContext, null);
```

## <a name="cancel-media-processing"></a>Cancelar procesamiento multimedia

Use el método `cancelAllMediaOperationsWithResponse` del objeto `CallConnection` para cancelar la operación de reproducción de audio. El método `cancelAllMediaOperationsWithResponse` admite los siguientes parámetros:

- `operationContext`, es el identificador único del contexto de la solicitud.

```java
String operationContext = UUID.randomUUID().toString();
Response<CancelAllMediaOperationsResult> cancelmediaresponse = this.callConnection.cancelAllMediaOperationsWithResponse(operationContext, null);
```

## <a name="hang-up-the-call"></a>Finalización de la llamada

Use el método `hangupWithResponse` del objeto `CallConnection` para finalizar la llamada.

```java
Response<Void> response = this.callConnection.hangupWithResponse(null);
```
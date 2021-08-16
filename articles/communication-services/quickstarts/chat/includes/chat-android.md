---
title: Archivo de inclusión
description: Archivo de inclusión
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 41a25c5b92bfc91379ecf9c869a6c1dbbb7c9680
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "114201134"
---
## <a name="sample-code"></a>Código de ejemplo
Busque el código finalizado de este inicio rápido en [GitHub](https://github.com/Azure-Samples/communication-services-android-quickstarts/tree/main/Add-chat).

## <a name="prerequisites"></a>Requisitos previos

Antes de comenzar, compruebe lo siguiente:

- Cree de una cuenta de Azure con una suscripción activa. Para más información, consulte [Creación de una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Instale [Android Studio](https://developer.android.com/studio). Lo usaremos para crear una aplicación de Android a fin de que el inicio rápido instale las dependencias.
- Cree un recurso de Azure Communication Services. Para más información, consulte [Creación de un recurso de Azure Communication Services](../../create-communication-resource.md). Para este inicio rápido, tendrá que **registrar el punto de conexión del recurso**.
- Cree **dos** usuarios de Communication Services y emítales un [token de acceso de usuario](../../access-tokens.md). Asegúrese de establecer el ámbito en el **chat** y **anote la cadena del token, así como la cadena userId**. En este inicio rápido, se creará una conversación con un participante inicial y, después, se agregará un segundo participante a esta.

## <a name="setting-up"></a>Instalación

### <a name="create-a-new-android-application"></a>Creación de una nueva aplicación de Android

1. Abra Android Studio y seleccione `Create a new project`. 
2. En la ventana siguiente, seleccione `Empty Activity` como plantilla del proyecto.
3. Al elegir las opciones, escriba `ChatQuickstart` como nombre de proyecto.
4. Haga clic en Siguiente y elija el directorio en el que desea que se cree el proyecto.

### <a name="install-the-libraries"></a>Instalación de las bibliotecas

Usaremos Gradle para instalar las dependencias de Communication Services necesarias. Desde la línea de comandos, vaya al directorio raíz del proyecto `ChatQuickstart`. Abra el archivo build.gradle de la aplicación y agregue las siguientes dependencias en el destino `ChatQuickstart`:

```
implementation 'com.azure.android:azure-communication-common:1.0.1'
implementation 'com.azure.android:azure-communication-chat:1.0.0'
implementation 'org.slf4j:slf4j-log4j12:1.7.29'
```

#### <a name="exclude-meta-files-in-packaging-options-in-root-buildgradle"></a>Exclusión de metarchivos en las opciones de empaquetado del archivo build.gradle raíz
```
android {
   ...
    packagingOptions {
        exclude 'META-INF/DEPENDENCIES'
        exclude 'META-INF/LICENSE'
        exclude 'META-INF/license'
        exclude 'META-INF/NOTICE'
        exclude 'META-INF/notice'
        exclude 'META-INF/ASL2.0'
        exclude("META-INF/*.md")
        exclude("META-INF/*.txt")
        exclude("META-INF/*.kotlin_module")
    }
}
```

#### <a name="alternative-to-install-libraries-through-maven"></a>(Opción alternativa) Para instalar bibliotecas a través de Maven
Para importar la biblioteca en su proyecto mediante el sistema de compilación [Maven](https://maven.apache.org/), agréguela a la sección `dependencies` del archivo `pom.xml` de la aplicación. Para ello, especifique su identificador de artefacto y la versión que quiere usar:

```xml
<dependency>
  <groupId>com.azure.android</groupId>
  <artifactId>azure-communication-chat</artifactId>
  <version>1.0.0</version>
</dependency>
```


### <a name="setup-the-placeholders"></a>Configuración de los marcadores de posición

Abra y edite el archivo `MainActivity.java`. En este inicio rápido, agregaremos el código a `MainActivity` y veremos la salida en la consola. En este inicio rápido no se aborda la creación de una interfaz de usuario. En la parte superior del archivo, importe `Communication common` y `Communication chat`, y las demás bibliotecas del sistema:

```
import com.azure.android.communication.chat.*;
import com.azure.android.communication.chat.models.*;
import com.azure.android.communication.common.*;

import android.os.Bundle;
import android.util.Log;
import android.widget.Toast;

import com.jakewharton.threetenabp.AndroidThreeTen;

import java.util.ArrayList;
import java.util.List;
```

Copie el código siguiente en la clase `MainActivity` del archivo `MainActivity.java`:

```java
    private String endpoint = "https://<resource>.communication.azure.com";
    private String firstUserId = "<first_user_id>";
    private String secondUserId = "<second_user_id>";
    private String firstUserAccessToken = "<first_user_access_token>";
    private String threadId = "<thread_id>";
    private String chatMessageId = "<chat_message_id>";
    private final String sdkVersion = "1.0.0";
    private static final String APPLICATION_ID = "Chat Quickstart App";
    private static final String SDK_NAME = "azure-communication-com.azure.android.communication.chat";
    private static final String TAG = "Chat Quickstart App";

    private void log(String msg) {
        Log.i(TAG, msg);
        Toast.makeText(this, msg, Toast.LENGTH_LONG).show();
    }
    
   @Override
    protected void onStart() {
        super.onStart();
        try {
            AndroidThreeTen.init(this);

            // <CREATE A CHAT CLIENT>

            // <CREATE A CHAT THREAD>

            // <CREATE A CHAT THREAD CLIENT>

            // <SEND A MESSAGE>
            
            // <RECEIVE CHAT MESSAGES>

            // <ADD A USER>

            // <LIST USERS>

            // <REMOVE A USER>
            
            // <<SEND A TYPING NOTIFICATION>>
            
            // <<SEND A READ RECEIPT>>
               
            // <<LIST READ RECEIPTS>>
        } catch (Exception e){
            System.out.println("Quickstart failed: " + e.getMessage());
        }
    }
```

1. Reemplace `<resource>` por el recurso de Communication Services
2. Reemplace `<first_user_id>` y `<second_user_id>` por los identificadores de usuario válidos de Communication Services que se generaron como parte de los requisitos previos.
3. Reemplace `<first_user_access_token>` por el token de acceso de Communication Services de `<first_user_id>` que se generó como parte de los requisitos previos.

En los pasos siguientes, se reemplazaran los marcadores de posición por código de ejemplo mediante la biblioteca de chat de Communication Services.


### <a name="create-a-chat-client"></a>Creación de un cliente de chat

Reemplace el comentario `<CREATE A CHAT CLIENT>` con el código siguiente (coloque las instrucciones de importación en la parte superior del archivo):

```java
import com.azure.android.core.http.policy.UserAgentPolicy;

ChatAsyncClient chatAsyncClient = new ChatClientBuilder()
    .endpoint(endpoint)
    .credential(new CommunicationTokenCredential(firstUserAccessToken))
    .addPolicy(new UserAgentPolicy(APPLICATION_ID, SDK_NAME, sdkVersion))
    .buildAsyncClient();

```

## <a name="object-model"></a>Modelo de objetos
Las siguientes clases e interfaces controlan algunas de las características principales de Chat SDK de Azure Communication Services para JavaScript.

| Nombre                                   | Descripción                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient/ChatAsyncClient | Esta clase es necesaria para la funcionalidad de chat. Cree una instancia de ella con la información de la suscripción y úsela para crear, obtener y eliminar conversaciones, y para suscribirse a eventos de chat. |
| ChatThreadClient/ChatThreadAsyncClient | Esta clase es necesaria para la funcionalidad de subproceso de chat. Obtiene una instancia de a través de la clase ChatClient y la usa para enviar, recibir, actualizar o eliminar mensajes, agregar, quitar u obtener usuarios, enviar notificaciones de escritura y confirmaciones de lectura. |

## <a name="start-a-chat-thread"></a>Inicio de un subproceso de chat

Ahora usaremos `ChatAsyncClient` para crear una conversación con un usuario inicial.

Reemplace el comentario `<CREATE A CHAT THREAD>` por el código siguiente:

```java
// A list of ChatParticipant to start the thread with.
List<ChatParticipant> participants = new ArrayList<>();
// The display name for the thread participant.
String displayName = "initial participant";
participants.add(new ChatParticipant()
    .setCommunicationIdentifier(new CommunicationUserIdentifier(firstUserId))
    .setDisplayName(displayName));

// The topic for the thread.
final String topic = "General";
// Optional, set a repeat request ID.
final String repeatabilityRequestID = "";
// Options to pass to the create method.
CreateChatThreadOptions createChatThreadOptions = new CreateChatThreadOptions()
    .setTopic(topic)
    .setParticipants(participants)
    .setIdempotencyToken(repeatabilityRequestID);

CreateChatThreadResult createChatThreadResult =
    chatAsyncClient.createChatThread(createChatThreadOptions).get();
ChatThreadProperties chatThreadProperties = createChatThreadResult.getChatThreadProperties();
threadId = chatThreadProperties.getId();

```

## <a name="get-a-chat-thread-client"></a>Obtención de un cliente de subproceso de chat

Ahora que hemos creado una conversación de chat, obtendremos un elemento `ChatThreadAsyncClient` para realizar operaciones en la conversación. Reemplace el comentario `<CREATE A CHAT THREAD CLIENT>` por el código siguiente:

```
ChatThreadAsyncClient chatThreadAsyncClient = new ChatThreadClientBuilder()
    .endpoint(endpoint)
    .credential(new CommunicationTokenCredential(firstUserAccessToken))
    .addPolicy(new UserAgentPolicy(APPLICATION_ID, SDK_NAME, sdkVersion))
    .chatThreadId(threadId)
    .buildAsyncClient();

```

## <a name="send-a-message-to-a-chat-thread"></a>Envío de un mensaje a un subproceso de chat

Ahora se enviará un mensaje a ese subproceso.

Reemplace el comentario `<SEND A MESSAGE>` por el código siguiente:

```java
// The chat message content, required.
final String content = "Test message 1";
// The display name of the sender, if null (i.e. not specified), an empty name will be set.
final String senderDisplayName = "An important person";
SendChatMessageOptions chatMessageOptions = new SendChatMessageOptions()
    .setType(ChatMessageType.TEXT)
    .setContent(content)
    .setSenderDisplayName(senderDisplayName);

// A string is the response returned from sending a message, it is an id, which is the unique ID of the message.
chatMessageId = chatThreadAsyncClient.sendMessage(chatMessageOptions).get().getId();

```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Recepción de mensajes de chat de un subproceso de chat
Con la señalización en tiempo real, puede suscribirse a nuevos mensajes entrantes y actualizar los mensajes actuales en la memoria en consecuencia. Azure Communication Services admite una [lista de eventos a los que se puede suscribir](../../../concepts/chat/concepts.md#real-time-notifications).

Reemplace el comentario `<RECEIVE CHAT MESSAGES>` con el código siguiente (coloque las instrucciones de importación en la parte superior del archivo):

```java

// Start real time notification
chatAsyncClient.startRealtimeNotifications(firstUserAccessToken, getApplicationContext());

// Register a listener for chatMessageReceived event
chatAsyncClient.addEventHandler(ChatEventType.CHAT_MESSAGE_RECEIVED, (ChatEvent payload) -> {
    ChatMessageReceivedEvent chatMessageReceivedEvent = (ChatMessageReceivedEvent) payload;
    // You code to handle chatMessageReceived event
    
});

```

> [!IMPORTANT]
> Problema conocido: al usar Android Chat y Calling SDK juntos en la misma aplicación, la característica de notificaciones en tiempo real de Chat SDK no funciona. Es posible que se produzca un problema de resolución de dependencias.
> Mientras se trabaja en una solución, para desactivar la característica de notificaciones en tiempo real, agregue la siguiente información de dependencia en el archivo build.gradle de la aplicación y sondee la API GetMessages para que muestre los mensajes entrantes a los usuarios. 
> 
> ```
> implementation ("com.azure.android:azure-communication-chat:1.0.0") {
>     exclude group: 'com.microsoft', module: 'trouter-client-android'
> }
> implementation 'com.azure.android:azure-communication-calling:1.0.0'
> ```
> 
> Tenga en cuenta que con la actualización anterior, si la aplicación intenta tocar cualquiera de las API de notificación como `chatAsyncClient.startRealtimeNotifications()` o `chatAsyncClient.addEventHandler()`, se producirá un error en tiempo de ejecución.


## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Adición de un usuario como miembro a la conversación del chat

Reemplace el comentario `<ADD A USER>` por el código siguiente:

```java
// The display name for the thread participant.
String secondUserDisplayName = "a new participant";
ChatParticipant participant = new ChatParticipant()
    .setCommunicationIdentifier(new CommunicationUserIdentifier(secondUserId))
    .setDisplayName(secondUserDisplayName);
        
chatThreadAsyncClient.addParticipant(participant);

```


## <a name="list-users-in-a-thread"></a>Enumeración de usuarios en una conversación

Reemplace el comentario `<LIST USERS>` con el código siguiente (coloque las instrucciones import en la parte superior del archivo):

```java
import com.azure.android.core.rest.util.paging.PagedAsyncStream;
import com.azure.android.core.util.RequestContext;

// The maximum number of participants to be returned per page, optional.
int maxPageSize = 10;

// Skips participants up to a specified position in response.
int skip = 0;

// Options to pass to the list method.
ListParticipantsOptions listParticipantsOptions = new ListParticipantsOptions()
    .setMaxPageSize(maxPageSize)
    .setSkip(skip);

PagedAsyncStream<ChatParticipant> participantsPagedAsyncStream =
      chatThreadAsyncClient.listParticipants(listParticipantsOptions, RequestContext.NONE);

participantsPagedAsyncStream.forEach(chatParticipant -> {
    // You code to handle participant
});

```


## <a name="remove-user-from-a-chat-thread"></a>Eliminación de un usuario de un subproceso de chat

Ahora se eliminará el segundo usuario del subproceso.

Reemplace el comentario `<REMOVE A USER>` por el código siguiente:

```java
// Using the unique ID of the participant.
chatThreadAsyncClient.removeParticipant(new CommunicationUserIdentifier(secondUserId)).get();

```

## <a name="send-a-typing-notification"></a>Envío de una notificación de escritura

Reemplace el comentario `<SEND A TYPING NOTIFICATION>` por el código siguiente:

```java
chatThreadAsyncClient.sendTypingNotification().get();
```

## <a name="send-a-read-receipt"></a>Envío de una confirmación de lectura

Se enviará la confirmación de lectura del mensaje enviado anteriormente.

Reemplace el comentario `<SEND A READ RECEIPT>` por el código siguiente:

```java
chatThreadAsyncClient.sendReadReceipt(chatMessageId).get();
```

## <a name="list-read-receipts"></a>Enumeración de las confirmaciones de lectura

Reemplace el comentario `<READ RECEIPTS>` por el código siguiente:

```java
// The maximum number of participants to be returned per page, optional.
maxPageSize = 10;
// Skips participants up to a specified position in response.
skip = 0;
// Options to pass to the list method.
ListReadReceiptOptions listReadReceiptOptions = new ListReadReceiptOptions()
    .setMaxPageSize(maxPageSize)
    .setSkip(skip);

PagedAsyncStream<ChatMessageReadReceipt> readReceiptsPagedAsyncStream =
      chatThreadAsyncClient.listReadReceipts(listReadReceiptOptions, RequestContext.NONE);

readReceiptsPagedAsyncStream.forEach(readReceipt -> {
    // You code to handle readReceipt
});

```

## <a name="run-the-code"></a>Ejecución del código

En Android Studio, presione el botón Ejecutar para compilar y ejecutar el proyecto. En la consola, puede ver la salida del código y la salida del registrador de ChatClient.

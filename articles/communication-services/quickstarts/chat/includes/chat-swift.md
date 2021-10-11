---
title: Archivo de inclusión
description: Archivo de inclusión
services: azure-communication-services
author: probableprime
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: rifox
ms.openlocfilehash: d61120d3f4e1256b9da50b8128b23fd73b36a5ef
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/03/2021
ms.locfileid: "129406839"
---
## <a name="sample-code"></a>Código de ejemplo
Busque el código finalizado de este inicio rápido en [GitHub](https://github.com/Azure-Samples/communication-services-ios-quickstarts/tree/main/add-chat).

## <a name="prerequisites"></a>Requisitos previos
Antes de comenzar, compruebe lo siguiente:

- Cree de una cuenta de Azure con una suscripción activa. Para más información, consulte [Creación de una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Instale [Xcode](https://developer.apple.com/xcode/) y [CocoaPods](https://cocoapods.org/). Va a usar Xcode para crear una aplicación iOS para el inicio rápido y Cocoapods para instalar las dependencias.
- Cree un recurso de Azure Communication Services. Para más información, consulte [Inicio rápido: Creación y administración de recursos de Communication Services](../../create-communication-resource.md). Para esta guía de inicio rápido, tendrá que registrar el punto de conexión del recurso.
- Cree dos usuarios de Azure Communication Services y emítales un [token de acceso de usuario](../../access-tokens.md). Asegúrese de establecer el ámbito en `chat` y anote las cadenas `token` e `userId`. En esta guía de inicio rápido, se creará una conversación con un participante inicial y, después, se agregará un segundo participante a esta.

## <a name="setting-up"></a>Instalación

### <a name="create-a-new-ios-application"></a>Creación de una aplicación iOS

Abra Xcode y seleccione **Create a new Xcode project** (Crear un nuevo proyecto en Xcode). A continuación, seleccione **iOS** para la plataforma y **App** (Aplicación) para la plantilla.

Para el nombre del proyecto, escriba **ChatQuickstart**. A continuación, seleccione **Storyboard** como interfaz, **UIKit App Delegate** como ciclo de vida y **Swift** como lenguaje.

Seleccione **Next** (Siguiente) y elija el directorio en el que desea que se cree el proyecto.

### <a name="install-the-libraries"></a>Instalación de las bibliotecas

Utilice Cocoapods para instalar las dependencias de Communication Services necesarias.

Desde la línea de comandos, vaya al directorio raíz del proyecto de iOS `ChatQuickstart`. Cree un archivo Podfile con el siguiente comando: `pod init`.

Abra el archivo Podfile y agregue las siguientes dependencias al destino `ChatQuickstart`:

```
pod 'AzureCommunicationCommon', '~> 1.0.2'
pod 'AzureCommunicationChat', '~> 1.1.0-beta.2'
```

Instale las dependencias con el comando siguiente: `pod install`. Tenga en cuenta que esto también crea un área de trabajo de Xcode.

Después de ejecutar `pod install`, vuelva a abrir el proyecto en Xcode; para ello, seleccione el área `.xcworkspace` recién creada.

### <a name="set-up-the-placeholders"></a>Configuración de los marcadores de posición

Abra el área de trabajo `ChatQuickstart.xcworkspace` en Xcode y, a continuación, abra `ViewController.swift`.

En este inicio rápido, va a agregar el código a `viewController` y verá la salida en la consola de Xcode. En este inicio rápido no se aborda la creación de una interfaz de usuario en iOS. 

Al principio de `viewController.swift`, importe las bibliotecas `AzureCommunication` y `AzureCommunicatonChat`:

```
import AzureCommunicationCommon
import AzureCommunicationChat
```

Copie el código siguiente en el método `viewDidLoad()` de `ViewController`:

```
override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view.

        let semaphore = DispatchSemaphore(value: 0)
        DispatchQueue.global(qos: .background).async {
            do {
                // <CREATE A CHAT CLIENT>
                
                // <CREATE A CHAT THREAD>

                // <LIST ALL CHAT THREADS>

                // <GET A CHAT THREAD CLIENT>

                // <SEND A MESSAGE>

                // <SEND A READ RECEIPT >

                // <RECEIVE MESSAGES>

                // <ADD A USER>
                
                // <LIST USERS>
            } catch {
                print("Quickstart failed: \(error.localizedDescription)")
            }
        }
    }
```

Con fines de demostración, usaremos un semáforo para sincronizar el código. En los pasos siguientes, va a reemplazar los marcadores de posición por código de ejemplo mediante la biblioteca de chat de Azure Communication Services.


### <a name="create-a-chat-client"></a>Creación de un cliente de chat

Para crear un cliente de chat, usará el punto de conexión de Communication Services y el token de acceso que se generó como parte de los pasos de requisitos previos.

Obtenga más información sobre los [tokens de acceso de usuario](../../access-tokens.md).

Este inicio rápido no cubre la creación de un nivel de servicio para administrar tokens para la aplicación de chat, aunque se recomienda. [Más información sobre la arquitectura del chat](../../../concepts/chat/concepts.md).

Reemplace el comentario `<CREATE A CHAT CLIENT>` por el fragmento de código siguiente:

```
let endpoint = "<ACS_RESOURCE_ENDPOINT>"
let credential =
try CommunicationTokenCredential(
    token: "<ACCESS_TOKEN>"
)
let options = AzureCommunicationChatClientOptions()

let chatClient = try ChatClient(
    endpoint: endpoint,
    credential: credential,
    withOptions: options
)
```

Reemplace `<ACS_RESOURCE_ENDPOINT>` por el punto de conexión del recurso de Azure Communication Services. Reemplace `<ACCESS_TOKEN>` por un token de acceso de Communication Services válido.

## <a name="object-model"></a>Modelo de objetos 

Las siguientes clases e interfaces controlan algunas de las características principales de Chat SDK de Azure Communication Services para iOS.

| Nombre                                   | Descripción                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `ChatClient` | Esta clase es necesaria para la funcionalidad de chat. Cree una instancia de ella con la información de la suscripción y úsela para crear, obtener y eliminar conversaciones, y para suscribirse a eventos de chat. |
| `ChatThreadClient` | Esta clase es necesaria para la funcionalidad de conversación de chat. Obtenga una instancia mediante `ChatClient` y úsela para enviar, recibir, actualizar y eliminar mensajes. También puede usarla para agregar, quitar y obtener usuarios, y enviar notificaciones de escritura y confirmaciones de lectura. |

## <a name="start-a-chat-thread"></a>Inicio de un subproceso de chat

`CreateChatThreadResult` es la respuesta devuelta por la creación de una conversación de chat.
Contiene una propiedad `chatThread` que es el objeto `ChatThreadProperties`. Este objeto contiene el valor threadId que se puede usar para obtener un `ChatThreadClient` para realizar operaciones en el subproceso creado: agregar participantes, enviar mensajes, etc.

Reemplace el comentario `<CREATE A CHAT THREAD>` por el fragmento de código siguiente:

```
let request = CreateChatThreadRequest(
    topic: "Quickstart",
    participants: [
        ChatParticipant(
            id: CommunicationUserIdentifier("<USER_ID>"),
            displayName: "Jack"
        )
    ]
)

var threadId: String?
chatClient.create(thread: request) { result, _ in
    switch result {
    case let .success(result):
        threadId = result.chatThread?.id

    case .failure:
        fatalError("Failed to create thread.")
    }
    semaphore.signal()
}
semaphore.wait()
```

Reemplace `<USER_ID>` por un identificador de usuario de Communication Services válido.

Aquí va a usar un semáforo para esperar al controlador de finalización antes de continuar. En pasos posteriores, usará el elemento `threadId` de la respuesta que se devuelve al controlador de finalización.

## <a name="list-all-chat-threads"></a>Lista de todas las conversaciones de chat

Después de crear una conversación de chat, podemos enumerar todas las conversaciones de chat mediante una llamada al método `listChatThreads` en `ChatClient`. Reemplace el comentario `<LIST ALL CHAT THREADS>` por el código siguiente:

```
chatClient.listThreads { result, _ in
    switch result {
    case let .success(threads):
        guard let chatThreadItems = threads.pageItems else {
            print("No threads returned.")
            return
        }

        for chatThreadItem in chatThreadItems {
            print("Thread id: \(chatThreadItem.id)")
        }
    case .failure:
        print("Failed to list threads")
    }
    semaphore.signal()
}
semaphore.wait()
```

## <a name="get-a-chat-thread-client"></a>Obtención de un cliente de subproceso de chat

El método `createClient` devuelve un `ChatThreadClient` para un subproceso que ya existe. Se puede usar para realizar operaciones en el subproceso creado: agregar participantes, enviar un mensaje, etc. threadId es el identificador único de la conversación del chat existente.

Reemplace el comentario `<GET A CHAT THREAD CLIENT>` por el código siguiente:

```
let chatThreadClient = try chatClient.createClient(forThread: threadId!)
```

## <a name="send-a-message-to-a-chat-thread"></a>Envío de un mensaje a un subproceso de chat

Utilice el método `send` para enviar un mensaje a un subproceso identificado mediante threadId.

`SendChatMessageRequest` se utiliza para describir la solicitud del mensaje:

- Utilice `content` para proporcionar el contenido del mensaje de chat.
- Utilice `senderDisplayName` para especificar el nombre para mostrar del remitente.
- Use `type` para especificar el tipo de mensaje, como "texto" o "html".
- Opcionalmente, use `metadata` para incluir los datos adicionales que quiera enviar con el mensaje. Este campo proporciona un mecanismo para que los desarrolladores amplíen la funcionalidad de los mensajes de chat y agreguen información personalizada para su caso de uso. Por ejemplo, al compartir un vínculo de archivo en el mensaje, es posible que quiera agregar "hasAttachment:true" en los metadatos para que la aplicación del destinatario pueda analizarlo y mostrarlo en consecuencia.

`SendChatMessageResult` es la respuesta que se devuelve al enviar un mensaje. Contiene un identificador que es el identificador único del mensaje.

Reemplace el comentario `<SEND A MESSAGE>` por el fragmento de código siguiente:

```
let message = SendChatMessageRequest(
                        content: "Hello!",
                        senderDisplayName: "Jack",
                        type: .text,
                        metadata: [
                            "hasAttachment": "true",
                            "attachmentUrl": "https://contoso.com/files/attachment.docx"
                        ]
                    )

var messageId: String?

chatThreadClient.send(message: message) { result, _ in
    switch result {
    case let .success(result):
        print("Message sent, message id: \(result.id)")
        messageId = result.id
    case .failure:
        print("Failed to send message")
    }
    semaphore.signal()
}
semaphore.wait()
```

## <a name="send-a-read-receipt"></a>Envío de una confirmación de lectura

Puede usar el método `sendReadReceipt` para publicar un evento de confirmación de lectura en un subproceso de chat en nombre de un usuario.
`messageId` es el identificador único del mensaje de chat que se leyó.

Reemplace el comentario `<SEND A READ RECEIPT>` por el código siguiente:

```
if let id = messageId {
    chatThreadClient.sendReadReceipt(forMessage: id) { result, _ in
        switch result {
        case .success:
            print("Read receipt sent")
        case .failure:
            print("Failed to send read receipt")
        }
        semaphore.signal()
    }
    semaphore.wait()
} else {
    print("Cannot send read receipt without a message id")
}
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Recepción de mensajes de chat de un subproceso de chat

Con la señalización en tiempo real, puede suscribirse para escuchar nuevos mensajes entrantes y actualizar los mensajes actuales en la memoria en consecuencia. Azure Communication Services admite una [lista de eventos a los que se puede suscribir](../../../concepts/chat/concepts.md#real-time-notifications).

Reemplace el comentario `<RECEIVE MESSAGES>` por el código siguiente. Después de habilitar las notificaciones, pruebe a enviar nuevos mensajes para ver ChatMessageReceivedEvents.

```
chatClient.startRealTimeNotifications { result in
    switch result {
    case .success:
        print("Real-time notifications started.")
    case .failure:
        print("Failed to start real-time notifications.")
    }
    semaphore.signal()
}
semaphore.wait()

chatClient.register(event: .chatMessageReceived, handler: { response in
    switch response {
    case let .chatMessageReceivedEvent(event):
        print("Received a message: \(event.message)")
    default:
        return
    }
})
```

También puede recuperar mensajes de chat mediante el sondeo del método `listMessages` a intervalos especificados. Ejecute el siguiente fragmento de código de `listMessages`

```
chatThreadClient.listMessages { result, _ in
    switch result {
    case let .success(messagesResult):
        guard let messages = messagesResult.pageItems else {
            print("No messages returned.")
            return
        }

        for message in messages {
            print("Received message with id: \(message.id)")
        }

    case .failure:
        print("Failed to receive messages")
    }
    semaphore.signal()
}
semaphore.wait()
```

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Adición de un usuario como miembro a la conversación del chat

Una vez que se crea un subproceso, puede agregar y quitar usuarios de este. Al agregar usuarios, les concede acceso para poder enviar mensajes a la conversación, y agregar o quitar otros participantes. Antes de llamar a `add`, asegúrese de que ha adquirido un token de acceso y una identidad nuevos para ese usuario. El usuario necesitará ese token de acceso para poder inicializar su cliente de chat.

Use el método `add` de `ChatThreadClient` para agregar uno o varios participantes a la conversación de chat. A continuación se indican los atributos admitidos para cada participante de la conversación:
- `id` es obligatorio y es la identidad del participante de la conversación.
- `displayName` es opcional y es el nombre para mostrar del participante de la conversación.
- `shareHistoryTime` es opcional y es la hora a partir de la cual el historial de chat se compartió con el participante.

Reemplace el comentario `<ADD A USER>` por el código siguiente:

```
let user = ChatParticipant(
    id: CommunicationUserIdentifier("<USER_ID>"),
    displayName: "Jane"
)

chatThreadClient.add(participants: [user]) { result, _ in
    switch result {
    case let .success(result):
        (result.invalidParticipants != nil) ? print("Added participant") : print("Error adding participant")
    case .failure:
        print("Failed to add the participant")
    }
    semaphore.signal()
}
semaphore.wait()
```

Reemplace `<USER_ID>` por el identificador de usuario de Communication Services del usuario que se va a agregar.

## <a name="list-users-in-a-thread"></a>Enumeración de usuarios en una conversación

Use el método `listParticipants` para obtener todos los participantes de una conversación de chat determinada.

Reemplace el comentario `<LIST USERS>` por el código siguiente:

```
chatThreadClient.listParticipants { result, _ in
    switch result {
    case let .success(participantsResult):
        guard let participants = participantsResult.pageItems else {
            print("No participants returned.")
            return
        }

        for participant in participants {
            let user = participant.id as! CommunicationUserIdentifier
            print("User with id: \(user.identifier)")
        }
    case .failure:
        print("Failed to list participants")
    }
    semaphore.signal()
}
semaphore.wait()
```

## <a name="run-the-code"></a>Ejecución del código

En Xcode, presione el botón Run (Ejecutar) para compilar y ejecutar el proyecto. En la consola, puede ver la salida del código y la salida del registrador de ChatClient.

**Nota:** Establezca `Build Settings > Build Options > Enable Bitcode` en `No`. Actualmente, el SDK de AzureCommunicationChat para iOS no admite la habilitación de bitcode, lo cual está en seguimiento en el [problema de GitHub](https://github.com/Azure/azure-sdk-for-ios/issues/787) siguiente.

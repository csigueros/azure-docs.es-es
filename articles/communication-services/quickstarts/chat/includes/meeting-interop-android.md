---
title: 'Inicio rápido: unirse a una reunión de Teams'
author: agiurg
ms.author: agiurg
ms.date: 07/20/2021
ms.topic: include
ms.service: azure-communication-services
ms.openlocfilehash: 07a8af0c389b0b5c2216f45e0a3fb0ba053e5690
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114442919"
---
En este artículo de inicio rápido aprenderá a chatear en una reunión de Teams mediante Chat SDK de Azure Communication Services para Android.

## <a name="prerequisites"></a>Requisitos previos 

- Una  [implementación de Teams](/deployoffice/teams-install). 
- Una [aplicación de llamada](../../voice-video-calling/get-started-teams-interop.md) en funcionamiento. 

## <a name="enable-teams-interoperability"></a>Habilitación de la interoperabilidad de Teams 

Cualquier usuario de Communication Services que se una a una reunión de Teams como invitado solo puede acceder al chat cuando se haya unido a la llamada de la reunión de Teams. Consulte la documentación de la [interoperabilidad de Teams](../../voice-video-calling/get-started-teams-interop.md) para aprender a agregar un usuario de Communication Services a una llamada de reunión de Teams.

Para usar esta característica debe ser miembro de la organización propietaria de ambas entidades.

## <a name="joining-the-meeting-chat"></a>Unión al chat de la reunión 

Una vez que la interoperabilidad de Teams se habilita, cualquier usuario de Communication Services puede unirse a la llamada de Teams como usuario externo mediante el SDK de llamadas. Al unirse a la llamada se le agregará también como participante en el chat de la reunión, donde podrán enviar mensajes a otros usuarios durante la llamada, y recibirlos de ellos. El usuario no tendrá acceso a los mensajes del chat enviados antes de que se haya unido a la llamada. Para unirse a la reunión e iniciar el chat, puede seguir los pasos siguientes.

## <a name="add-chat-to-the-teams-calling-app"></a>Incorporación de chat a la aplicación de llamada de Teams

En build.gradle a nivel del módulo, agregue la dependencia en Chat SDK.

> [!IMPORTANT]
> Problema conocido: Al usar Chat y Calling SDK de Android juntos en la misma aplicación, la característica de notificaciones en tiempo real de Chat SDK no funcionará. Recibirá un problema de resolución de dependencias. Mientras trabajamos en una solución, puede desactivar la característica de notificaciones en tiempo real agregando las siguientes exclusiones a la dependencia de Chat SDK en el archivo `build.gradle` de la aplicación:
> 
> ```groovy
> implementation ("com.azure.android:azure-communication-chat:1.0.0") {
>     exclude group: 'com.microsoft', module: 'trouter-client-android'
> }
> ```


## <a name="add-the-teams-ui-layout"></a>Incorporación del diseño de la interfaz de usuario de Teams

Reemplace el código de activity_main.xml por el siguiente fragmento de código. Agrega entradas para el identificador de subproceso y para enviar mensajes, un botón para enviar el mensaje con tipo y un diseño de chat básico.

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <EditText
        android:id="@+id/teams_meeting_thread_id"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginHorizontal="20dp"
        android:layout_marginTop="128dp"
        android:ems="10"
        android:hint="Meeting Thread Id"
        android:inputType="textUri"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <EditText
        android:id="@+id/teams_meeting_link"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginHorizontal="20dp"
        android:layout_marginTop="64dp"
        android:ems="10"
        android:hint="Teams meeting link"
        android:inputType="textUri"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <LinearLayout
        android:id="@+id/button_layout"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="30dp"
        android:gravity="center"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintHorizontal_bias="0.0"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/teams_meeting_thread_id">

        <Button
            android:id="@+id/join_meeting_button"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Join Meeting" />

        <Button
            android:id="@+id/hangup_button"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Hangup" />

    </LinearLayout>

    <TextView
        android:id="@+id/call_status_bar"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="40dp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent" />

    <TextView
        android:id="@+id/recording_status_bar"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="20dp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent" />

    <ScrollView
        android:id="@+id/chat_box"
        android:layout_width="374dp"
        android:layout_height="294dp"
        android:layout_marginTop="40dp"
        android:layout_marginBottom="20dp"
        app:layout_constraintBottom_toTopOf="@+id/send_message_button"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/button_layout"
        android:orientation="vertical"
        android:gravity="bottom"
        android:layout_gravity="bottom"
        android:fillViewport="true">

        <LinearLayout
            android:id="@+id/chat_box_layout"
            android:orientation="vertical"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent"
            android:gravity="bottom"
            android:layout_gravity="top"
            android:layout_alignParentBottom="true"/>
    </ScrollView>

    <EditText
        android:id="@+id/message_body"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginHorizontal="20dp"
        android:layout_marginTop="588dp"
        android:ems="10"
        android:inputType="textUri"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        tools:text="Type your message here..."
        tools:visibility="invisible" />

    <Button
        android:id="@+id/send_message_button"
        android:layout_width="138dp"
        android:layout_height="45dp"
        android:layout_marginStart="133dp"
        android:layout_marginTop="48dp"
        android:layout_marginEnd="133dp"
        android:text="Send Message"
        android:visibility="invisible"
        app:layout_constraintBottom_toTopOf="@+id/recording_status_bar"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintHorizontal_bias="0.428"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/chat_box" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

## <a name="enable-the-teams-ui-controls"></a>Habilitación de los controles de la interfaz de usuario de Teams

### <a name="import-packages-and-define-state-variables"></a>Importación de paquetes y definición de variables de estado

Agregue las siguientes importaciones adicionales al contenido de `MainActivity.java`:

```
import android.graphics.Typeface;
import android.graphics.Color;
import android.text.Html;
import android.os.Handler;
import android.view.Gravity;
import android.view.View;
import android.widget.LinearLayout;
import java.util.Collections;
import java.util.concurrent.CountDownLatch;
import java.util.concurrent.TimeUnit;
import com.azure.android.communication.chat.ChatThreadAsyncClient;
import com.azure.android.communication.chat.ChatThreadClientBuilder;
import com.azure.android.communication.chat.models.ChatMessage;
import com.azure.android.communication.chat.models.ChatMessageType;
import com.azure.android.communication.chat.models.ChatParticipant;
import com.azure.android.communication.chat.models.ListChatMessagesOptions;
import com.azure.android.communication.chat.models.SendChatMessageOptions;
import com.azure.android.communication.common.CommunicationIdentifier;
import com.azure.android.communication.common.CommunicationUserIdentifier;
import com.azure.android.core.rest.util.paging.PagedAsyncStream;
import com.azure.android.core.util.AsyncStreamHandler;
```

Agregue las siguientes variables a la clase `MainActivity`:

```
    // InitiatorId is used to differentiate incoming messages from outgoing messages
    private static final String InitiatorId = "<USER_ID>";
    private static final String ResourceUrl = "<COMMUNICATION_SERVICES_RESOURCE_ENDPOINT>";
    private String threadId;
    private ChatThreadAsyncClient chatThreadAsyncClient;
    
    // The list of ids corresponsding to messages which have already been processed
    ArrayList<String> chatMessages = new ArrayList<>();
```

Reemplace `<USER_ID>` por el identificador del usuario que inicia el chat.
Reemplace `<COMMUNICATION_SERVICES_RESOURCE_ENDPOINT>` por el punto de conexión del recurso de Communication Services. 

### <a name="initialize-the-chatthreadclient"></a>Inicialización de ChatThreadClient

Después de unirse a la reunión, cree una instancia de `ChatThreadClient` y haga visibles los componentes de chat.

Actualice el final del método `MainActivity.joinTeamsMeeting()` con el código siguiente:

```
    private void joinTeamsMeeting() {
        ...
        EditText threadIdView = findViewById(R.id.teams_meeting_thread_id);
        threadId = threadIdView.getText().toString();
        // Initialize Chat Thread Client
        chatThreadAsyncClient = new ChatThreadClientBuilder()
                .endpoint(ResourceUrl)
                .credential(new CommunicationTokenCredential(UserToken))
                .chatThreadId(threadId)
                .buildAsyncClient();
        Button sendMessageButton = findViewById(R.id.send_message_button);
        EditText messageBody = findViewById(R.id.message_body);
        // Register the method for sending messages and toggle the visibility of chat components
        sendMessageButton.setOnClickListener(l -> sendMessage());
        sendMessageButton.setVisibility(View.VISIBLE);
        messageBody.setVisibility(View.VISIBLE);
        
        // Start the polling for chat messages immediately
        handler.post(runnable);
    }
```

### <a name="enable-sending-messages"></a>Habilitación del envío de mensajes

Agregue el método `sendMessage()` a `MainActivity`. Usará `ChatThreadClient` para enviar mensajes en nombre del usuario.

```
    private void sendMessage() {
        // Retrieve the typed message content
        EditText messageBody = findViewById(R.id.message_body);
        // Set request options and send message
        SendChatMessageOptions options = new SendChatMessageOptions();
        options.setContent(messageBody.getText().toString());
        options.setSenderDisplayName("ACS User");
        chatThreadAsyncClient.sendMessage(options);
        // Clear the text box
        messageBody.setText("");
    }
```

### <a name="enable-polling-for-messages-and-rendering-them-in-the-application"></a>Habilitación del sondeo para mensajes y su representación en la aplicación

> [!IMPORTANT]
> Problema conocido: Dado que la característica de notificaciones en tiempo real de Chat SDK no funciona junto con Calling SDK, tendremos que sondear la API `GetMessages` a intervalos predefinidos. En este ejemplo, usaremos intervalos de 3 segundos.

Podemos obtener los siguientes datos de la lista de mensajes devueltos por la API `GetMessages`: 
 - Los mensajes `text` y `html` en el subproceso desde la unión
 - Cambios en la lista de subprocesos
 - Actualizaciones en el tema de subprocesos


En la clase `MainActivity`, agregue un controlador y una tarea ejecutable que se ejecutará a intervalos de 3 segundos:

```
    private Handler handler = new Handler();
    private Runnable runnable = new Runnable() {
        @Override
        public void run() {
            try {
                retrieveMessages();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            // Repeat every 3 seconds
            handler.postDelayed(runnable, 3000);
        }
    };
```

Tenga en cuenta que la tarea ya se ha iniciado al final del método `MainActivity.joinTeamsMeeting()` actualizado en el paso de inicialización.

Por último, agregaremos el método para consultar todos los mensajes accesibles en el subproceso, analizarlos por tipo de mensaje y mostrar los `html` y `text`:

```
    private void retrieveMessages() throws InterruptedException {
        // Initialize the list of messages not yet processed
        ArrayList<ChatMessage> newChatMessages = new ArrayList<>();
        
        // Retrieve all messages accessible to the user
        PagedAsyncStream<ChatMessage> messagePagedAsyncStream
                = this.chatThreadAsyncClient.listMessages(new ListChatMessagesOptions(), null);
        // Set up a lock to wait until all returned messages have been inspected
        CountDownLatch latch = new CountDownLatch(1);
        // Traverse the returned messages
        messagePagedAsyncStream.forEach(new AsyncStreamHandler<ChatMessage>() {
            @Override
            public void onNext(ChatMessage message) {
                // Messages that should be displayed in the chat
                if ((message.getType().equals(ChatMessageType.TEXT)
                    || message.getType().equals(ChatMessageType.HTML))
                    && !chatMessages.contains(message.getId())) {
                    newChatMessages.add(message);
                    chatMessages.add(message.getId());
                }
                if (message.getType().equals(ChatMessageType.PARTICIPANT_ADDED)) {
                    // Handle participants added to chat operation
                    List<ChatParticipant> participantsAdded = message.getContent().getParticipants();
                    CommunicationIdentifier participantsAddedBy = message.getContent().getInitiatorCommunicationIdentifier();
                }
                if (message.getType().equals(ChatMessageType.PARTICIPANT_REMOVED)) {
                    // Handle participants removed from chat operation
                    List<ChatParticipant> participantsRemoved = message.getContent().getParticipants();
                    CommunicationIdentifier participantsRemovedBy = message.getContent().getInitiatorCommunicationIdentifier();
                }
                if (message.getType().equals(ChatMessageType.TOPIC_UPDATED)) {
                    // Handle topic updated
                    String newTopic = message.getContent().getTopic();
                    CommunicationIdentifier topicUpdatedBy = message.getContent().getInitiatorCommunicationIdentifier();
                }
            }
            @Override
            public void onError(Throwable throwable) {
                latch.countDown();
            }
            @Override
            public void onComplete() {
                latch.countDown();
            }
        });
        // Wait until the operation completes
        latch.await(1, TimeUnit.MINUTES);
        // Returned messages should be ordered by the createdOn field to be guaranteed a proper chronological order
        // For the purpose of this demo we will just reverse the list of returned messages
        Collections.reverse(newChatMessages);
        for (ChatMessage chatMessage : newChatMessages)
        {
            LinearLayout chatBoxLayout = findViewById(R.id.chat_box_layout);
            // For the purpose of this demo UI, we don't need to use HTML formatting for displaying messages
            // The Teams client always sends html messages in meeting chats 
            String message = Html.fromHtml(chatMessage.getContent().getMessage(), Html.FROM_HTML_MODE_LEGACY).toString().trim();
            TextView messageView = new TextView(this);
            messageView.setText(message);
            // Compare with sender identifier and align LEFT/RIGHT accordingly
            // ACS users are of type CommunicationUserIdentifier
            CommunicationIdentifier senderId = chatMessage.getSenderCommunicationIdentifier();
            if (senderId instanceof CommunicationUserIdentifier
                && InitiatorId.equals(((CommunicationUserIdentifier) senderId).getId())) {
                messageView.setTextColor(Color.GREEN);
                messageView.setGravity(Gravity.RIGHT);
            } else {
                messageView.setTextColor(Color.BLUE);
                messageView.setGravity(Gravity.LEFT);
            }
            // Note: messages with the deletedOn property set to a timestamp, should be marked as deleted
            // Note: messages with the editedOn property set to a timestamp, should be marked as edited
            messageView.setTypeface(Typeface.SANS_SERIF, Typeface.BOLD);
            chatBoxLayout.addView(messageView);
        }
    }
```

El cliente de Teams no establece los nombres para mostrar de los participantes del subproceso chat. Los nombres se devolverán como NULL en la API para los participantes de la enumeración, en el evento `participantsAdded` y en el evento `participantsRemoved`. Los nombres para mostrar de los participantes del chat se pueden recuperar del campo `remoteParticipants` del objeto `call`.

## <a name="get-a-teams-meeting-chat-thread-for-a-communication-services-user"></a>Obtención de un subproceso del chat de la reunión para un usuario de Communication Services

El vínculo y el chat de la reunión de Teams se pueden recuperar mediante las instancias de Graph API, que se detallan en la [documentación de Graph](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true). El SDK de llamada de Communication Services acepta un vínculo a toda la reunión de Teams. Este vínculo se devuelve como parte del recurso `onlineMeeting`, accesible desde la [propiedad `joinWebUrl`](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true). Con las instancias de [Graph API](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true), también puede obtener `threadId`. La respuesta tendrá un objeto `chatInfo` que contiene el elemento `threadID`. 

También puede obtener la información de la reunión necesaria y el identificador de la conversación en la dirección URL **Unirse a la reunión** de la propia invitación a la reunión de Teams.
Así es el vínculo de una reunión en Teams: `https://teams.microsoft.com/l/meetup-join/meeting_chat_thread_id/1606337455313?context=some_context_here`. El valor de `threadId` estará donde se encuentra el elemento `meeting_chat_thread_id` en el vínculo. Asegúrese de que el elemento `meeting_chat_thread_id` se haya escapado antes de utilizarlo. Debería tener el siguiente formato: `19:meeting_ZWRhZDY4ZGUtYmRlNS00OWZaLTlkZTgtZWRiYjIxOWI2NTQ4@thread.v2`.


## <a name="run-the-code"></a>Ejecución del código

Ahora se puede iniciar la aplicación con el botón "Ejecutar aplicación" de la barra de herramientas (Mayús + F10).

Para unirse a la reunión y el chat de Teams, escriba el vínculo de la reunión de Teams y el identificador del subproceso en la interfaz de usuario.

Después de unirse a la reunión de Teams, deberá admitir al usuario a la reunión en el cliente de Teams. Una vez que el usuario se haya admitido y se haya unido al chat, podrá enviar y recibir mensajes.

:::image type="content" source="../join-teams-meeting-chat-quickstart-android.png" alt-text="Captura de pantalla de la aplicación Android completada.":::

> [!NOTE] 
> Actualmente, solo se admite el envío, la recepción y la edición de mensajes para los escenarios de interoperabilidad con Teams. Otras características, como los indicadores de escritura y que los usuarios de Communication Services agreguen o quiten otros usuarios de la reunión de Teams aún no se admiten.

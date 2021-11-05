---
title: 'Inicio rápido: unirse a una reunión de Teams'
author: juramir
ms.author: juramir
ms.date: 10/15/2021
ms.topic: include
ms.service: azure-communication-services
ms.openlocfilehash: fd559b8f393f7dca88bfb64fa24e9ba17e6b3832
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131253618"
---
En este inicio rápido, aprenderá a chatear en una reunión de Teams mediante el SDK de chat de Azure Communication Services para C#.

## <a name="sample-code"></a>Código de ejemplo
Busque el código de este inicio rápido en [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/ChatTeamsInteropQuickStart).

## <a name="prerequisites"></a>Requisitos previos 

* Una  [implementación de Teams](/deployoffice/teams-install). 
* Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).  
* Instale [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) con la carga de trabajo de desarrollo de la Plataforma universal de Windows.  
* Un recurso de Communication Services implementado. [Cree un recurso de Communication Services](../../create-communication-resource.md). 
* Un vínculo a la reunión de Teams.

## <a name="joining-the-meeting-chat"></a>Unión al chat de la reunión 

Un usuario de Communication Services puede unirse a una reunión de Teams como un usuario anónimo mediante el SDK de llamada. Al unirse a la reunión, se le agregará también como participante en el chat de la reunión, donde podrá enviar mensajes a otros usuarios durante la reunión, y recibirlos de ellos. El usuario no tendrá acceso a los mensajes de chat que se enviaron antes de unirse a la reunión y no podrá enviar ni recibir mensajes una vez que finalice la esta. Para unirse a la reunión e iniciar el chat, puede seguir los pasos siguientes.

## <a name="run-the-code"></a>Ejecución del código
Puede compilar y ejecutar el código en Visual Studio. Tenga en cuenta que, para las plataformas de la solución, se admiten `x64`, `x86` y `ARM64`. 

1. Abra una instancia de PowerShell, Terminal Windows, símbolo del sistema o equivalente y navegue hasta el directorio donde le gustaría clonar el ejemplo.
2. `git clone https://github.com/Azure-Samples/Communication-Services-dotnet-quickstarts.git`
3. Abra el proyecto ChatTeamsInteropQuickStart/ChatTeamsInteropQuickStart.csproj en Visual Studio.
4. Instale las siguientes versiones de paquetes NuGet (o versiones posteriores):
``` csharp
Install-Package Azure.Communication.Calling -Version 1.0.0-beta.29
Install-Package Azure.Communication.Chat -Version 1.1.0
Install-Package Azure.Communication.Common -Version 1.0.1
Install-Package Azure.Communication.Identity -Version 1.0.1

```

5. Con el recurso de Communication Services proporcionado según los requisitos previos, agregue la cadena de conexión al archivo **ChatTeamsInteropQuickStart/MainPage.xaml.cs**. 

``` csharp
//ACS resource connection string i.e = "endpoint=https://your-resource.communication.azure.net/;accesskey=your-access-key";
private const string connectionString_ = "";
```

> [!IMPORTANT]
> * Seleccione la plataforma adecuada en la lista desplegable "Plataformas de solución" de Visual Studio <b>antes de</b> ejecutar el código. es decir, `x64`
> * Asegúrese de que tiene el "Modo de desarrollador" de Windows 10 habilitado [(Configuración del desarrollador)](/windows/apps/get-started/enable-your-device-for-development).
>  
>  *Los pasos siguientes no funcionarán si esto no está configurado correctamente.*


6. Pulse F5 para iniciar el proyecto en modo de depuración.
7. Pegue un vínculo de reunión de Teams válido en el cuadro "Teams Meeting Link" (Vínculo a la reunión de Teams) (consulte la sección siguiente).
8. Pulse en "Unirse a la reunión de Teams" para empezar a chatear.

> [!IMPORTANT]
> Una vez que el SDK que realiza la llamada establece la conexión con la reunión de Teams, consulte en [Aplicación de Windows que llama a Communication Services](../../voice-video-calling/getting-started-with-calling.md) las funciones principales para controlar las operaciones de chat, que son: StartPollingForChatMessages y SendMessageButton_Click. Ambos fragmentos de código están en ChatTeamsInteropQuickStart\MainPage.xaml.cs 

```csharp
        /// <summary>
        /// Backgroung task that keeps polling for chat messages while the call connection is stablished
        /// </summary>
        private async Task StartPollingForChatMessages()
        {
            CommunicationTokenCredential communicationTokenCredential = new(user_token_);
            chatClient_ = new ChatClient(EndPointFromConnectionString(), communicationTokenCredential);
            await Task.Run(async () =>
            {
                keepPolling_ = true;

                ChatThreadClient chatThreadClient = chatClient_.GetChatThreadClient(thread_Id_);
                int previousTextMessages = 0;
                while (keepPolling_)
                {
                    try
                    {
                        CommunicationUserIdentifier currentUser = new(user_Id_);
                        AsyncPageable<ChatMessage> allMessages = chatThreadClient.GetMessagesAsync();
                        SortedDictionary<long, string> messageList = new();
                        int textMessages = 0;
                        string userPrefix;
                        await foreach (ChatMessage message in allMessages)
                        {
                            if (message.Type == ChatMessageType.Html || message.Type == ChatMessageType.Text)
                            {
                                textMessages++;
                                userPrefix = message.Sender.Equals(currentUser) ? "[you]:" : "";
                                messageList.Add(long.Parse(message.SequenceId), $"{userPrefix}{StripHtml(message.Content.Message)}");
                            }
                        }

                        //Update UI just when there are new messages
                        if (textMessages > previousTextMessages)
                        {
                            previousTextMessages = textMessages;
                            await Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
                            {
                                TxtChat.Text = string.Join(Environment.NewLine, messageList.Values.ToList());
                            });

                        }
                        if (!keepPolling_)
                        {
                            return;
                        }

                        await SetInCallState(true);
                        Thread.Sleep(3000);
                    }
                    catch (Exception e)
                    {
                        await Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
                        {
                            _ = new MessageDialog($"An error ocurred while fetching messages in PollingChatMessagesAsync(). The application will shutdown. Details : {e.Message}").ShowAsync();
                            throw e;
                        });
                        await SetInCallState(false);
                    }
                }
            });
        }
        private async void SendMessageButton_Click(object sender, RoutedEventArgs e)
        {
            SendMessageButton.IsEnabled = false;
            ChatThreadClient chatThreadClient = chatClient_.GetChatThreadClient(thread_Id_);
            _ = await chatThreadClient.SendMessageAsync(TxtMessage.Text);
            
            TxtMessage.Text = "";
            SendMessageButton.IsEnabled = true;
        }
```



## <a name="get-a-teams-meeting-link"></a>Obtención de un vínculo a la reunión de Teams

El vínculo a la reunión de Teams se puede recuperar mediante Graph API, como se detalla en la [documentación de Graph](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true). Este vínculo se devuelve como parte del recurso `onlineMeeting`, al que se puede acceder bajo la [propiedad `joinWebUrl`](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true). 

También puede obtener el vínculo de la reunión necesario en la dirección URL **Unirse a la reunión** de la propia invitación a la reunión de Teams.
Así es el vínculo de una reunión en Teams: `https://teams.microsoft.com/l/meetup-join/meeting_chat_thread_id/1606337455313?context=some_context_here`. 

:::image type="content" source="../join-teams-meeting-chat-quickstart-windows.png" alt-text="Captura de pantalla de la aplicación csharp completada.":::

> [!NOTE] 
> Actualmente, en los escenarios de interoperabilidad con Teams solo se admiten el envío, la recepción, la edición de mensajes y el envío de notificaciones de escritura. Otras características, como las confirmaciones de lectura y que los usuarios de Communication Services agreguen o quiten otros usuarios de la reunión de Teams, aún no se admiten.

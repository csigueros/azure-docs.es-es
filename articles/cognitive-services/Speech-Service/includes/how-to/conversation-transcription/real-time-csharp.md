---
author: laujan
ms.service: cognitive-services
ms.topic: include
ms.date: 10/20/2020
ms.author: lajanuar
ms.openlocfilehash: bc4709eeb59f0333a086336b4f61474671dac3af
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "122068972"
---
## <a name="install-the-speech-sdk"></a>Instalación de Speech SDK

En primer lugar, deberá instalar Speech SDK. Utilice las siguientes instrucciones en función de la plataforma:

* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-csharp&tabs=dotnet" target="_blank">.NET Framework </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-csharp&tabs=dotnetcore" target="_blank">.NET Core </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-csharp&tabs=unity" target="_blank">Unity </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-csharp&tabs=uwps" target="_blank">UWP </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-csharp&tabs=xaml" target="_blank">Xamarin </a>

## <a name="create-voice-signatures"></a>Creación de firmas de voz

(Puede omitir este paso si no quiere usar perfiles de usuario inscritos previamente para identificar participantes concretos).

Si quiere inscribir perfiles de usuario, el primer paso consiste en crear firmas de voz para los participantes en la conversación a fin de que puedan identificarse como hablantes únicos. El archivo de audio `.wav` de entrada para la creación de firmas de voz debe ser de 16 bits, con una frecuencia de muestreo de 16 kHz y un formato de canal único (mono). La longitud recomendada para cada muestra de audio está entre 30 segundos y dos minutos. Una muestra de audio demasiado corta dará como resultado una precisión reducida al reconocer el hablante. El archivo `.wav` debe ser una muestra de voz de **una persona** para que se cree un perfil de voz único.

El ejemplo siguiente muestra cómo crear una firma de voz [mediante la API REST](https://aka.ms/cts/signaturegenservice) de C#. Tenga en cuenta que debe sustituir la información real de `subscriptionKey`, `region` y la ruta de acceso a un archivo `.wav` de ejemplo.

```csharp
using System;
using System.IO;
using System.Net.Http;
using System.Runtime.Serialization;
using System.Threading.Tasks;
using Newtonsoft.Json;

[DataContract]
internal class VoiceSignature
{
    [DataMember]
    public string Status { get; private set; }

    [DataMember]
    public VoiceSignatureData Signature { get; private set; }

    [DataMember]
    public string Transcription { get; private set; }
}

[DataContract]
internal class VoiceSignatureData
{
    internal VoiceSignatureData()
    { }

    internal VoiceSignatureData(int version, string tag, string data)
    {
        this.Version = version;
        this.Tag = tag;
        this.Data = data;
    }

    [DataMember]
    public int Version { get; private set; }

    [DataMember]
    public string Tag { get; private set; }

    [DataMember]
    public string Data { get; private set; }
}

private static async Task<string> GetVoiceSignatureString()
{
    var subscriptionKey = "your-subscription-key";
    var region = "your-region";

    byte[] fileBytes = File.ReadAllBytes("path-to-voice-sample.wav");
    var content = new ByteArrayContent(fileBytes);
    var client = new HttpClient();
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
    var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromByteArray", content);
    
    var jsonData = await response.Content.ReadAsStringAsync();
    var result = JsonConvert.DeserializeObject<VoiceSignature>(jsonData);
    return JsonConvert.SerializeObject(result.Signature);
}
```

La ejecución de la función `GetVoiceSignatureString()` devuelve una cadena de firma de voz en el formato correcto. Ejecute la función dos veces, de modo que tenga dos cadenas para usarlas como entrada para las variables `voiceSignatureStringUser1` y `voiceSignatureStringUser2` a continuación.

> [!NOTE]
> Las firmas de voz **solo** pueden crearse mediante la API REST.

## <a name="transcribe-conversations"></a>Transcripción de conversaciones

El siguiente código de ejemplo muestra cómo transcribir conversaciones en tiempo real para dos hablantes. Se da por supuesto que ya ha creado cadenas de firma de voz para cada hablante como se muestra arriba. Sustituya la información real de `subscriptionKey`, `region` y la ruta de acceso `filepath` por la del audio que desea transcribir.

Si no usa perfiles de usuario inscritos previamente, se tardarán unos segundos más en completar el primer reconocimiento de usuarios desconocidos como speaker1, speaker2, etc.

> [!NOTE]
> Asegúrese de usar el mismo valor `subscriptionKey` en la aplicación para la creación de firmas; de lo contrario, se producirán errores. 

Este código de ejemplo realiza las tareas siguientes:

* Crea un `AudioConfig` a partir del archivo `.wav` de ejemplo para transcribir.
* Crea una `Conversation` mediante `CreateConversationAsync()`.
* Crea un objeto `ConversationTranscriber` usando el constructor y se suscribe a los eventos necesarios.
* Agrega participantes a la conversación. Las cadenas `voiceSignatureStringUser1` y `voiceSignatureStringUser2` deben aparecer como salidas de los pasos anteriores desde la función `GetVoiceSignatureString()`.
* Se une a la conversación y comienza la transcripción.

Llame a la función `TranscribeConversationsAsync()` para iniciar la transcripción de conversaciones.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Transcription;

class transcribe_conversation
{
// all your other code

public static async Task TranscribeConversationsAsync(string voiceSignatureStringUser1, string voiceSignatureStringUser2)
{
    var subscriptionKey = "your-subscription-key";
    var region = "your-region";
    var filepath = "audio-file-to-transcribe.wav";

    var config = SpeechConfig.FromSubscription(subscriptionKey, region);
    config.SetProperty("ConversationTranscriptionInRoomAndOnline", "true");
    // config.SpeechRecognitionLanguage = "zh-cn"; // en-us by default. This code specifies Chinese.
    var stopRecognition = new TaskCompletionSource<int>();

    using (var audioInput = AudioConfig.FromWavFileInput(filepath))
    {
        var meetingID = Guid.NewGuid().ToString();
        using (var conversation = await Conversation.CreateConversationAsync(config, meetingID))
        {
            // create a conversation transcriber using audio stream input
            using (var conversationTranscriber = new ConversationTranscriber(audioInput))
            {
                conversationTranscriber.Transcribing += (s, e) =>
                {
                    Console.WriteLine($"TRANSCRIBING: Text={e.Result.Text} SpeakerId={e.Result.UserId}");
                };

                conversationTranscriber.Transcribed += (s, e) =>
                {
                    if (e.Result.Reason == ResultReason.RecognizedSpeech)
                    {
                        Console.WriteLine($"TRANSCRIBED: Text={e.Result.Text} SpeakerId={e.Result.UserId}");
                    }
                    else if (e.Result.Reason == ResultReason.NoMatch)
                    {
                        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                    }
                };

                conversationTranscriber.Canceled += (s, e) =>
                {
                    Console.WriteLine($"CANCELED: Reason={e.Reason}");

                    if (e.Reason == CancellationReason.Error)
                    {
                        Console.WriteLine($"CANCELED: ErrorCode={e.ErrorCode}");
                        Console.WriteLine($"CANCELED: ErrorDetails={e.ErrorDetails}");
                        Console.WriteLine($"CANCELED: Did you update the subscription info?");
                        stopRecognition.TrySetResult(0);
                    }
                };

                conversationTranscriber.SessionStarted += (s, e) =>
                {
                    Console.WriteLine($"\nSession started event. SessionId={e.SessionId}");
                };

                conversationTranscriber.SessionStopped += (s, e) =>
                {
                    Console.WriteLine($"\nSession stopped event. SessionId={e.SessionId}");
                    Console.WriteLine("\nStop recognition.");
                    stopRecognition.TrySetResult(0);
                };

                // Add participants to the conversation.
                var speaker1 = Participant.From("User1", "en-US", voiceSignatureStringUser1);
                var speaker2 = Participant.From("User2", "en-US", voiceSignatureStringUser2);
                await conversation.AddParticipantAsync(speaker1);
                await conversation.AddParticipantAsync(speaker2);

                // Join to the conversation and start transcribing
                await conversationTranscriber.JoinConversationAsync(conversation);
                await conversationTranscriber.StartTranscribingAsync().ConfigureAwait(false);

                // waits for completion, then stop transcription
                Task.WaitAny(new[] { stopRecognition.Task });
                await conversationTranscriber.StopTranscribingAsync().ConfigureAwait(false);
            }
        }
    }
}
}
```
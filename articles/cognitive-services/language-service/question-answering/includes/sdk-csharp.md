---
title: 'Guía de inicio rápido: Biblioteca cliente de respuesta a preguntas para .NET'
description: En esta guía de inicio rápido se muestra cómo empezar a trabajar con la biblioteca cliente para .NET. Siga estos pasos para instalar el paquete y probar el código de ejemplo para realizar tareas básicas. La respuesta a preguntas le permite alimentar un servicio de preguntas y respuestas con su contenido semiestructurado, como documentos de preguntas frecuentes, direcciones URL y manuales de productos.
author: mrbullwinkle
ms.author: mbullwin
ms.topic: include
ms.date: 11/11/2021
ms.openlocfilehash: e374ef91c414b9d352b3c0cddd8af59da3e699a0
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132354042"
---
Utilice esta guía de inicio rápido para la biblioteca cliente de respuesta a preguntas de .NET para:

* Obtener una respuesta de una instancia de knowledge base.
* Obtener una respuesta de un cuerpo de texto que envíe junto con la pregunta.
* Obtener la puntuación de confianza para la respuesta a la pregunta.

 [Documentación de referencia de la API][questionanswering_refdocs]|[Código fuente][questionanswering_client_src] | [Paquete (NuGet)][questionanswering_nuget_package]  | [Ejemplos][questionanswering_samples] |

[questionanswering_nuget_package]: https://nuget.org/packages/Azure.AI.Language.
[questionanswering_refdocs]: https://docs.microsoft.com/dotnet/api/Azure.AI.Language.QuestionAnswering/
[questionanswering_client_src]: https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/cognitivelanguage/Azure.AI.Language.QuestionAnswering/src/
[questionanswering_samples]: https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/cognitivelanguage/Azure.AI.Language.QuestionAnswering/samples/README.md

## <a name="prerequisites"></a>Requisitos previos

* Una suscripción a Azure: [cree una cuenta gratuita](https://azure.microsoft.com/free/cognitive-services)
* El [IDE de Visual Studio](https://visualstudio.microsoft.com/vs/) o la versión actual de [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Las preguntas y respuestas requieren un [recurso de idioma](https://ms.portal.azure.com/?quickstart=true#create/Microsoft.CognitiveServicesTextAnalytics) con la característica de respuesta a preguntas personalizada habilitada para generar un punto de conexión y una clave de API. <!--TODO: Change link-->
    * Una vez implementado el recurso de idioma, seleccione **Ir al recurso**. Necesitará la clave y el punto de conexión del recurso que cree para conectarse a la API. Más adelante en este inicio rápido, debe pegar la clave y el punto de conexión en el código que se incluye a continuación.
* Instancia de knowledge base existente en la consulta. Si no ha configurado una instancia de knowledge base, puede seguir las instrucciones de la [**guía de inicio rápido de Language Studio**](../quickstart/sdk.md). O bien, agregue una instancia de knowledge base que use esta [dirección URL de la guía del usuario de Surface](https://download.microsoft.com/download/7/B/1/7B10C82E-F520-4080-8516-5CF0D803EEE0/surface-book-user-guide-EN.pdf) como origen de datos.

## <a name="setting-up"></a>Instalación

### <a name="cli"></a>CLI

En una ventana de consola (por ejemplo, cmd, PowerShell o Bash), use el comando `dotnet new` para crear una nueva aplicación de consola con el nombre `question-answering-quickstart`. Este comando crea un sencillo proyecto de C#, "Hola mundo", con un solo archivo de origen: *program.cs*.

```console
dotnet new console -n question-answering-quickstart
```

Cambie el directorio a la carpeta de aplicaciones recién creada. Para compilar la aplicación:

```console
dotnet build
```

La salida de la compilación no debe contener advertencias ni errores.

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

Dentro del directorio de aplicaciones, instale la biblioteca cliente de respuesta a preguntas personalizada para .NET con el siguiente comando:

```console
dotnet add package Azure.AI.Language.QuestionAnswering
```

## <a name="query-a-knowledge-base"></a>Consulta de una base de conocimiento

#### <a name="generate-an-answer-from-a-knowledge-base"></a>Generación de una respuesta de la instancia de knowledge base

En el ejemplo siguiente podrá consultar una instancia de knowledge base mediante `GetAnswers` para obtener una respuesta a su pregunta.

Recuerde que deberá actualizar el código y proporcionar sus propios valores para las variables siguientes.

|Nombre de la variable | Valor |
|--------------------------|-------------|
| `endpoint`               | Este valor se puede encontrar en la sección **Claves y punto de conexión** al examinar el recurso en Azure Portal. Como alternativa, puede encontrar el valor en **Language Studio** > **respuesta a la pregunta** > **Implementar la instancia de knowledge base** > **Obtener la dirección URL de predicción**. Un punto de conexión de ejemplo es: `https://southcentralus.api.cognitive.microsoft.com/`|
| `credential` | Este valor se puede encontrar en la sección **Claves y punto de conexión** al examinar el recurso en Azure Portal. Puede usar Key1 o Key2. Tenga siempre dos claves válidas para poder realizar la rotación segura de claves sin tiempo de inactividad. Como alternativa, puede encontrar el valor en **Language Studio** > **respuesta a la pregunta** > **Implementar la instancia de knowledge base** > **Obtener la dirección URL de predicción**. El valor de clave forma parte de la solicitud de ejemplo.|
| `projectName` | Nombre del proyecto de respuesta a preguntas.|
| `deploymentName`             | Hay dos posibles valores: `test` y `production`. `production` depende de que haya implementado su instancia de knowledge base de **Language Studio** > **respuesta a la pregunta** > **Implementar la knowledge bases**.|

En el directorio del proyecto, abra el archivo *program.cs* y reemplácelo con el código siguiente:

```csharp
using Azure;
using Azure.AI.Language.QuestionAnswering;
using System;

namespace question_answering
{
    class Program
    {
        static void Main(string[] args)
        {

            Uri endpoint = new Uri("https://{YOUR-ENDPOINT}.api.cognitive.microsoft.com/");
            AzureKeyCredential credential = new AzureKeyCredential("{YOUR-LANGUAGE-RESOURCE-KEY}");
            string projectName = "{YOUR-PROJECT-NAME}";
            string deploymentName = "production";

            string question = "How long should my Surface battery last?";

            QuestionAnsweringClient client = new QuestionAnsweringClient(endpoint, credential);
            QuestionAnsweringProject project = new QuestionAnsweringProject(projectName, deploymentName);

            Response<AnswersResult> response = client.GetAnswers(question, project);

            foreach (KnowledgeBaseAnswer answer in response.Value.Answers)
            {
                Console.WriteLine($"Q:{question}");
                Console.WriteLine($"A:{answer.Answer}");
            }
        }
    }
}
```

Aunque estamos codificando de forma rígida las variables para nuestro ejemplo. En el caso de producción, considere la posibilidad de usar alguna forma segura de almacenar las credenciales, y acceder a ellas. Por ejemplo, [Azure Key Vault](../../../../key-vault/general/overview.md) proporciona almacenamiento de claves seguro.

Una vez actualizado `Program.cs` con el código anterior, sustitúyalo con los valores de variable correctos. Ejecute la aplicación con el comando `dotnet run` desde el directorio de la aplicación.

```console
dotnet run
```

La respuesta tendrá el siguiente aspecto:

```console
Q: How much battery life do I have left?
A: If you want to see how much battery you have left, go to **Start  **> **Settings  **> **Devices  **> **Bluetooth & other devices  **, then find your pen. The current battery level will appear under the battery icon.
```

Para obtener información sobre la fiabilidad de la respuesta a la pregunta de si esta es la respuesta correcta, agregue una instrucción de impresión adicional debajo de las instrucciones impresas existentes:

```csharp
Console.WriteLine($"Q:{question}");
Console.WriteLine($"A:{answer.Answer}");
Console.WriteLine($"({answer.Confidence})"); // add this line
```

Si vuelve a ejecutar `dotnet run`, recibirá un resultado con una puntuación de confianza:

```console
Q:How much battery life do I have left?
A:If you want to see how much battery you have left, go to **Start  **> **Settings  **> **Devices  **> **Bluetooth & other devices  **, then find your pen. The current battery level will appear under the battery icon.
(0.9185)
```

La puntuación de confianza devuelve un valor entre 0 y 1. Puede pensar en este valor como un porcentaje y multiplicarlo por 100, por lo que una puntuación de confianza de 0,9185 significa que la respuesta a preguntas es un 91,85 % segura, ya que esta es la respuesta correcta a la pregunta en función de la instancia de knowledge base.

Si quiere excluir las respuestas en las que la puntuación de confianza está por debajo de un umbral determinado, puede usar `AnswerOptions` para agregar la propiedad `ConfidenceScoreThreshold`.

```csharp
QuestionAnsweringClient client = new QuestionAnsweringClient(endpoint, credential);
QuestionAnsweringProject project = new QuestionAnsweringProject(projectName, deploymentName);
AnswersOptions options = new AnswersOptions(); //Add this line
options.ConfidenceThreshold = 0.95; //Add this line

Response<AnswersResult> response = client.GetAnswers(question, project, options); //Add the additional options parameter
```

Puesto que sabemos por nuestra ejecución anterior del código que nuestra puntuación de confianza es `.9185`, establecer el umbral en `.95` dará como resultado la [ respuesta predeterminada ](../how-to/change-default-answer.md) que se devuelve.

```console
Q:How much battery life do I have left?
A:No good match found in KB
(0)
```

## <a name="query-text-without-a-knowledge-base"></a>Consulta de texto sin instancia de knowledge base

También puede usar la respuesta a preguntas sin instancia de knowledge base con `GetAnswersFromText`. En este caso, se proporciona la respuesta a la pregunta mediante una pregunta y los registros de texto asociados en los que le gustaría buscar una respuesta en el momento en que se envía la solicitud.

En este ejemplo, solo necesita modificar las variables para `endpoint` y `credential`.

```csharp
using Azure;
using Azure.AI.Language.QuestionAnswering;
using System;
using System.Collections.Generic;


namespace questionansweringcsharp
{
    class Program
    {
        static void Main(string[] args)
        {

            Uri endpoint = new Uri("https://{YOUR-ENDPOINT}.api.cognitive.microsoft.com/");
            AzureKeyCredential credential = new AzureKeyCredential("YOUR-LANGUAGE-RESOURCE-KEY");
            QuestionAnsweringClient client = new QuestionAnsweringClient(endpoint, credential);

            IEnumerable<TextDocument> records = new[]
            {
                new TextDocument("doc1", "Power and charging.It takes two to four hours to charge the Surface Pro 4 battery fully from an empty state. " +
                         "It can take longer if you're using your Surface for power-intensive activities like gaming or video streaming while you're charging it"),
                new TextDocument("doc2", "You can use the USB port on your Surface Pro 4 power supply to charge other devices, like a phone, while your Surface charges. " +
                         "The USB port on the power supply is only for charging, not for data transfer. If you want to use a USB device, plug it into the USB port on your Surface."),
            };

            AnswersFromTextOptions options = new AnswersFromTextOptions("How long does it takes to charge a surface?", records);
            Response<AnswersFromTextResult> response = client.GetAnswersFromText(options);

           foreach (TextAnswer answer in response.Value.Answers)
            {
                if (answer.Confidence > .9)
                {
                    string BestAnswer = response.Value.Answers[0].Answer;

                    Console.WriteLine($"Q:{options.Question}");
                    Console.WriteLine($"A:{BestAnswer}");
                    Console.WriteLine($"Confidence Score: ({response.Value.Answers[0].Confidence:P2})"); //:P2 converts the result to a percentage with 2 decimals of accuracy. 
                    break;
                }
                else
                {
                    Console.WriteLine($"Q:{options.Question}");
                    Console.WriteLine("No answers met the requested confidence score.");
                    break;
                }
            }

        }
    }
}
```

Para ejecutar el código anterior, reemplace `Program.cs` por el contenido del bloque de script anterior y modifique las variables `endpoint` y `credential` para que se correspondan con el recurso de lenguaje que creó como parte de los requisitos previos.

En este caso, se recorren en iteración todas las respuestas y solo se devuelve la respuesta con la puntuación de confianza más alta que sea mayor que 0,9. Para obtener más información sobre las opciones disponibles con `GetAnswersFromText`.

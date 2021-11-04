---
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 63d16ba3e98b4815d39ccbf7d9df79c23ad0b24a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093074"
---
[Documentación de referencia](/dotnet/api/azure.ai.textanalytics?preserve-view=true&view=azure-dotnet) | [Código fuente de la biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics) | [Paquete (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics/5.1.0) | [Muestras adicionales](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples)


## <a name="prerequisites"></a>Prerrequisitos

* Una suscripción a Azure: [cree una cuenta gratuita](https://azure.microsoft.com/free/cognitive-services)
* [IDE de Visual Studio](https://visualstudio.microsoft.com/vs/)
* Una vez que tenga la suscripción de Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Creación de un recurso de idioma"  target="_blank">cree un recurso de idioma</a> en Azure Portal para obtener la clave y el punto de conexión.  Una vez que se implemente, haga clic en **Ir al recurso**.
    * Necesitará la clave y el punto de conexión del recurso que cree para conectar la aplicación a la API. En una sección posterior de este mismo inicio rápido pegará la clave y el punto de conexión en el código siguiente.
    * Puede usar el plan de tarifa gratis (`F0`) para probar el servicio y actualizarlo más adelante a un plan de pago para producción.
* Para usar la característica Analizar, necesitará un recurso de idioma con el plan de tarifa estándar (S).

## <a name="setting-up"></a>Instalación

### <a name="create-a-new-net-core-application"></a>Creación de una aplicación de .NET Core

Utilice el IDE de Visual Studio para crear una aplicación de consola de .NET Core. Así se creará un proyecto "Hola mundo" con un solo archivo de origen de C#: *program.cs*.

Instale la biblioteca cliente, para lo que debe hacer clic con el botón derecho en la solución en el **Explorador de soluciones** y seleccionar **Administrar paquetes NuGet**. En el administrador de paquetes que se abre, seleccione **Examinar** y busque `Azure.AI.TextAnalytics`. Seleccione la versión `5.1.0` e **Instalar**. También puede usar la [Consola del Administrador de paquetes](/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).


## <a name="code-example"></a>Ejemplo de código

Copie el código siguiente en el archivo *program.cs*. No olvide reemplazar la variable `key` por la clave del recurso y la variable `endpoint` por el punto de conexión del recurso. 

[!INCLUDE [find the key and endpoint for a resource](../../../includes/find-azure-resource-info.md)]

```csharp
using Azure;
using System;
using Azure.AI.TextAnalytics;

namespace Example
{
    class Program
    {
        private static readonly AzureKeyCredential credentials = new AzureKeyCredential("replace-with-your-key-here");
        private static readonly Uri endpoint = new Uri("replace-with-your-endpoint-here");
        
        // Example method for extracting named entities from text 
        static void EntityRecognitionExample(TextAnalyticsClient client)
        {
            var response = client.RecognizeEntities("I had a wonderful trip to Seattle last week.");
            Console.WriteLine("Named Entities:");
            foreach (var entity in response.Value)
            {
                Console.WriteLine($"\tText: {entity.Text},\tCategory: {entity.Category},\tSub-Category: {entity.SubCategory}");
                Console.WriteLine($"\t\tScore: {entity.ConfidenceScore:F2},\tLength: {entity.Length},\tOffset: {entity.Offset}\n");
            }
        }

        static void Main(string[] args)
        {
            var client = new TextAnalyticsClient(endpoint, credentials);
            EntityRecognitionExample(client);

            Console.Write("Press any key to exit.");
            Console.ReadKey();
        }

    }
}
```

## <a name="output"></a>Output

```console
Named Entities:
        Text: trip,     Category: Event,        Sub-Category:
                Score: 0.61,    Length: 4,      Offset: 18

        Text: Seattle,  Category: Location,     Sub-Category: GPE
                Score: 0.82,    Length: 7,      Offset: 26

        Text: last week,        Category: DateTime,     Sub-Category: DateRange
                Score: 0.80,    Length: 9,      Offset: 34
```

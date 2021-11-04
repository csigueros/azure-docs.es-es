---
title: 'Inicio rápido: Biblioteca cliente de vinculación de entidad para C#'
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: f6f7a53a0d3b0d5f99aadadfee59e77102a68f42
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131030664"
---
[Documentación de referencia](/dotnet/api/azure.ai.textanalytics?preserve-view=true&view=azure-dotnet) | [Código fuente de la biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics) | [Paquete (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics/5.1.0) | [Ejemplos adicionales](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples)


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
using System.Globalization;
using Azure.AI.TextAnalytics;

namespace EntityLinkingExample
{
    class Program
    {
        private static readonly AzureKeyCredential credentials = new AzureKeyCredential("replace-with-your-key-here");
        private static readonly Uri endpoint = new Uri("replace-with-your-endpoint-here");
        
        // Example method for recognizing entities and providing a link to an online data source
        static void EntityLinkingExample(TextAnalyticsClient client)
        {
            var response = client.RecognizeLinkedEntities(
                "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
                "to develop and sell BASIC interpreters for the Altair 8800. " +
                "During his career at Microsoft, Gates held the positions of chairman, " +
                "chief executive officer, president and chief software architect, " +
                "while also being the largest individual shareholder until May 2014.");
            Console.WriteLine("Linked Entities:");
            foreach (var entity in response.Value)
            {
                Console.WriteLine($"\tName: {entity.Name},\tID: {entity.DataSourceEntityId},\tURL: {entity.Url}\tData Source: {entity.DataSource}");
                Console.WriteLine("\tMatches:");
                foreach (var match in entity.Matches)
                {
                    Console.WriteLine($"\t\tText: {match.Text}");
                    Console.WriteLine($"\t\tScore: {match.ConfidenceScore:F2}\n");
                }
            }
        }

        static void Main(string[] args)
        {
            var client = new TextAnalyticsClient(endpoint, credentials);
            EntityLinkingExample(client);

            Console.Write("Press any key to exit.");
            Console.ReadKey();
        }

    }
}

```

### <a name="output"></a>Output

```console
Linked Entities:
    Name: Microsoft,        ID: Microsoft,  URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
    Matches:
            Text: Microsoft
            Score: 0.55

            Text: Microsoft
            Score: 0.55

    Name: Bill Gates,       ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
    Matches:
            Text: Bill Gates
            Score: 0.63

            Text: Gates
            Score: 0.63

    Name: Paul Allen,       ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
    Matches:
            Text: Paul Allen
            Score: 0.60

    Name: April 4,  ID: April 4,    URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
    Matches:
            Text: April 4
            Score: 0.32

    Name: BASIC,    ID: BASIC,      URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
    Matches:
            Text: BASIC
            Score: 0.33

    Name: Altair 8800,      ID: Altair 8800,        URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
    Matches:
            Text: Altair 8800
            Score: 0.88
```

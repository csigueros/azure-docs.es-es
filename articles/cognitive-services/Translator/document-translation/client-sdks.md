---
title: Biblioteca cliente de C#, .NET o Python de Traducción de documentos
titleSuffix: Azure Cognitive Services
description: Uso de la biblioteca cliente (SDK) para C#, .NET o Python de Translator para el proceso y el servicio de traducción de documentos por lotes basado en la nube
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 07/06/2021
ms.author: lajanuar
ms.openlocfilehash: b7bcf5339f6bff6a0f055e2016bcd3e12bfd5f45
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121724506"
---
# <a name="document-translation-client-library-sdks"></a>SDK de la biblioteca cliente de Traducción de documentos
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD001 -->
[Traducción de documentos](overview.md) es una característica basada en la nube del servicio [Azure Translator](../translator-overview.md). Puede traducir documentos completos o procesar traducciones de documentos por lotes en varios formatos de archivo, a la vez que se conserva el formato y la estructura de los documentos originales. En este artículo, aprenderá a usar las bibliotecas cliente de C#, .NET y Python del servicio de traducción de documentos. Para la API REST, vea nuestro [Inicio rápido](get-started-with-document-translation.md).

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesitará lo siguiente:

* Una [**cuenta de Azure**](https://azure.microsoft.com/free/cognitive-services/) activa.  En caso de no tener ninguna, puede crear una [**cuenta gratuita**](https://azure.microsoft.com/free/).

* Un [**recurso Translator de servicio único**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) (**no** un recurso multiservicio de Cognitive Services).

* Una [**cuenta de Azure Blob Storage**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Tendrá que [**crear contenedores**](../../../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) en la cuenta de Azure Blob Storage para los archivos de origen y destino:

  * **Contenedor de origen**. En este contenedor se cargan los archivos para su traducción (obligatorio).
  * **Contenedor de destino**. En este contenedor se almacenarán los archivos traducidos (obligatorio).

* También tendrá que crear tokens de firma de acceso compartido (SAS) para los contenedores de origen y destino. Los elementos `sourceUrl` y `targetUrl` deben incluir un token de firma de acceso compartido (SAS), que se anexa como una cadena de consulta. El token se puede asignar al contenedor o a blobs específicos. *Consulte* [**Creación de tokens de SAS para el proceso de traducción de documentos**](create-sas-tokens.md).

  * El contenedor o el blob de **origen** deben tener designado acceso de **lectura** y de **lista**.
  * El contenedor o el blob de **destino** deben tener designado acceso de **escritura** y de **lista**.

Para obtener más información, *vea* [Creación de tokens de SAS](create-sas-tokens.md).

## <a name="client-libraries"></a>Bibliotecas de cliente

### <a name="cnet"></a>[C#/.NET](#tab/csharp)

| [Paquete (NuGet)][documenttranslation_nuget_package] | [Biblioteca cliente][documenttranslation_client_library_docs] |  [API REST][documenttranslation_rest_api] | [Documentación del producto][documenttranslation_docs] | [Ejemplos][documenttranslation_samples] |

> [!IMPORTANT]
> Se trata de una versión preliminar del SDK de traducción de documentos. Está disponible como introducción para que los clientes puedan obtener acceso anticipado y proporcionar comentarios. Las versiones preliminares todavía se encuentran en fase de desarrollo, están sujetas a cambios y es posible que algunas características no se admitan o tengan funcionalidades restringidas; no las use en aplicaciones de producción.

### <a name="set-up-your-project"></a>Configuración del proyecto

En una ventana de consola (por ejemplo, cmd, PowerShell o Bash), use el comando `dotnet new` para crear una nueva aplicación de consola con el nombre `batch-document-translation`. Este comando crea un sencillo proyecto de C#, "Hola mundo", con un solo archivo de origen: *program.cs*.

```console
dotnet new console -n batch-document-translation
```

Cambie el directorio a la carpeta de aplicaciones recién creada. Compile la aplicación con el comando siguiente:

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

### <a name="install-the-client-library"></a>Instalación de la biblioteca cliente

En el directorio de la aplicación, instale la biblioteca cliente de Traducción de documentos para .NET mediante uno de los métodos siguientes:

#### <a name="net-cli"></a>**CLI de .NET**

```console
dotnet add package Azure.AI.Translation.Document --version 1.0.0-beta.2
```

#### <a name="nuget-package-manager"></a>**Administrador de paquetes de NuGet**

```console
Install-Package Azure.AI.Translation.Document -Version 1.0.0-beta.2
```

#### <a name="nuget-packagereference"></a>**Referencia de paquetes NuGet**

```xml
<ItemGroup>
    <!-- ... -->
<PackageReference Include="Azure.AI.Translation.Document" Version="1.0.0-beta.2" />
    <!-- ... -->
</ItemGroup>
```

En el directorio del proyecto, abra el archivo Program.cs en el editor o IDE que prefiera. Agregue lo siguiente mediante directivas:

```csharp
using Azure;
using Azure.AI.Translation.Document;

using System;
using System.Threading;
```

En la clase **Program** de la aplicación, cree variables para la clave de suscripción y el punto de conexión personalizado. Para obtener información, *vea* [Nombre de dominio personalizado y clave de suscripción](get-started-with-document-translation.md#custom-domain-name-and-subscription-key).

```csharp
private static readonly string endpoint = "<your custom endpoint>";
private static readonly string subscriptionKey = "<your subscription key>";
```

### <a name="translate-a-document-or-batch-files"></a>Traducción de un documento o archivos por lotes

* Para iniciar una operación de traducción para uno o varios documentos en un único contenedor de blobs, llamará al método `StartTranslationAsync`.

* Para llamar a `StartTranslationAsync` debe inicializar un objeto `DocumentTranslationInput` que contenga los parámetros siguientes:

* **sourceUri**. URI de SAS para el contenedor de origen que contiene los documentos que se van a traducir.
* **targetUri** URI de SAS para el contenedor de destino en el que se escribirán los documentos traducidos.
* **targetLanguageCode**. Código de idioma de los documentos traducidos. Puede encontrar códigos de idioma en nuestra página de [Soporte técnico de idioma](../language-support.md).

```csharp

public void StartTranslation() {
  Uri sourceUri = new Uri("<sourceUrl>");
  Uri targetUri = new Uri("<targetUrl>");

  DocumentTranslationClient client = new DocumentTranslationClient(new Uri(endpoint), new AzureKeyCredential(subscriptionKey));

  DocumentTranslationInput input = new DocumentTranslationInput(sourceUri, targetUri, "es")

  DocumentTranslationOperation operation = await client.StartTranslationAsync(input);

  await operation.WaitForCompletionAsync();

  Console.WriteLine($ "  Status: {operation.Status}");
  Console.WriteLine($ "  Created on: {operation.CreatedOn}");
  Console.WriteLine($ "  Last modified: {operation.LastModified}");
  Console.WriteLine($ "  Total documents: {operation.DocumentsTotal}");
  Console.WriteLine($ "    Succeeded: {operation.DocumentsSucceeded}");
  Console.WriteLine($ "    Failed: {operation.DocumentsFailed}");
  Console.WriteLine($ "    In Progress: {operation.DocumentsInProgress}");
  Console.WriteLine($ "    Not started: {operation.DocumentsNotStarted}");

  await foreach(DocumentStatusResult document in operation.Value) {
    Console.WriteLine($ "Document with Id: {document.DocumentId}");
    Console.WriteLine($ "  Status:{document.Status}");
    if (document.Status == TranslationStatus.Succeeded) {
      Console.WriteLine($ "  Translated Document Uri: {document.TranslatedDocumentUri}");
      Console.WriteLine($ "  Translated to language: {document.TranslatedTo}.");
      Console.WriteLine($ "  Document source Uri: {document.SourceDocumentUri}");
    }
    else {
      Console.WriteLine($ "  Error Code: {document.Error.ErrorCode}");
      Console.WriteLine($ "  Message: {document.Error.Message}");
    }
  }
}
```

Eso es todo. Ha creado un programa para traducir documentos en un contenedor de blobs mediante la biblioteca cliente de .NET.

### <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
 > [**Explore más ejemplos de código de C#** ](https://github.com/Azure/azure-sdk-for-net/tree/Azure.AI.Translation.Document_1.0.0-beta.2/sdk/translation/Azure.AI.Translation.Document/samples)

<!-- LINKS -->

[documenttranslation_nuget_package]: https://www.nuget.org/packages/Azure.AI.Translation.Document/1.0.0-beta.2
[documenttranslation_client_library_docs]: /dotnet/api/azure.ai.translation.document
[documenttranslation_docs]: overview.md
[documenttranslation_rest_api]: reference/rest-api-guide.md
[documenttranslation_samples]: https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/translation/Azure.AI.Translation.Document/samples

### <a name="python"></a>[Python](#tab/python)

| [Paquete (PyPI)][python-dt-pypi] |  [Biblioteca cliente][python-dt-client-library] |  [API REST][python-rest-api] | [Documentación del producto][python-dt-product-docs] | [Ejemplos][python-dt-samples] |

> [!IMPORTANT]
> Se trata de una versión preliminar del SDK de traducción de documentos. Está disponible como introducción para que los clientes puedan obtener acceso anticipado y proporcionar comentarios. Las versiones preliminares todavía se encuentran en fase de desarrollo, están sujetas a cambios y es posible que algunas características no se admitan o tengan funcionalidades restringidas; no las use en aplicaciones de producción.

### <a name="set-up-your-project"></a>Configuración del proyecto

### <a name="install-the-client-library"></a>Instalación de la biblioteca cliente

Si no lo ha hecho, instale [Python](https://www.python.org/downloads/) y después la versión más reciente de la biblioteca cliente de Translator:

```console
pip install azure-ai-translation-document
```

### <a name="create-your-application"></a>Creación de una aplicación

Cree una aplicación de Python en el IDE o editor que prefiera. A continuación, importe las bibliotecas siguientes.

```python
    import os
    from azure.core.credentials import AzureKeyCredential
    from azure.ai.translation.document import DocumentTranslationClient
```

Cree variables para la clave de suscripción de recursos, el punto de conexión personalizado, sourceUrl y targetUrl. Para obtener más información, *vea* [Nombre de dominio personalizado y clave de suscripción](get-started-with-document-translation.md#custom-domain-name-and-subscription-key).

```python
 subscriptionKey = "<your-subscription-key>"
 endpoint = "<your-custom-endpoint>"
 sourceUrl = "<your-container-sourceUrl>"
 targetUrl = "<your-container-targetUrl>"
```

### <a name="translate-a-document-or-batch-files"></a>Traducción de un documento o archivos por lotes

```python
client = DocumentTranslationClient(endpoint, AzureKeyCredential(subscriptionKey))

    poller = client.begin_translation(sourceUrl, targetUrl, "fr")
    result = poller.result()

    print("Status: {}".format(poller.status()))
    print("Created on: {}".format(poller.details.created_on))
    print("Last updated on: {}".format(poller.details.last_updated_on))
    print("Total number of translations on documents: {}".format(poller.details.documents_total_count))

    print("\nOf total documents...")
    print("{} failed".format(poller.details.documents_failed_count))
    print("{} succeeded".format(poller.details.documents_succeeded_count))

    for document in result:
        print("Document ID: {}".format(document.id))
        print("Document status: {}".format(document.status))
        if document.status == "Succeeded":
            print("Source document location: {}".format(document.source_document_url))
            print("Translated document location: {}".format(document.translated_document_url))
            print("Translated to language: {}\n".format(document.translated_to))
        else:
            print("Error Code: {}, Message: {}\n".format(document.error.code, document.error.message))
```

Eso es todo. Ha creado un programa para traducir documentos en un contenedor de blobs mediante la biblioteca cliente de Python.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Explore más ejemplos de código de Python](https://github.com/Azure/azure-sdk-for-python/tree/azure-ai-translation-document_1.0.0b1/sdk/translation/azure-ai-translation-document/samples)

<!-- LINKS -->
[python-dt-pypi]: https://aka.ms/azsdk/python/texttranslation/pypi
[python-dt-client-library]: https://aka.ms/azsdk/python/documenttranslation/docs
[python-rest-api]: reference/rest-api-guide.md
[python-dt-product-docs]: overview.md
[python-dt-samples]: https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/translation/azure-ai-translation-document/samples

---
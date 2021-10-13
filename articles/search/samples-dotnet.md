---
title: Ejemplos de .NET
titleSuffix: Azure Cognitive Search
description: Busque ejemplos de código de C# de demostración de Azure Cognitive Search que usan las bibliotecas cliente de .NET.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: 0f4ce35a43702f958656b1ad077e6c0f84bc88bb
ms.sourcegitcommit: 079426f4980fadae9f320977533b5be5c23ee426
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/04/2021
ms.locfileid: "129418715"
---
# <a name="net-c-code-samples-for-azure-cognitive-search"></a>Ejemplos de código de .NET (C#) de Azure Cognitive Search

Obtenga información sobre los ejemplos de código de C# que muestran la funcionalidad y el flujo de trabajo de una solución de Azure Cognitive Search. En estos ejemplos se usa la [**biblioteca cliente de Azure Cognitive Search**](/dotnet/api/overview/azure/search) para el [**SDK de Azure para .NET**](/dotnet/azure/), que puede explorar por medio de los vínculos siguientes.

| Destino | Vínculo |
|--------|------|
| Descarga del paquete | [www.nuget.org/packages/Azure.Search.Documents/](https://www.nuget.org/packages/Azure.Search.Documents/) |
| Referencia de API | [azure.search.documents](/dotnet/api/azure.search.documents)  |
| Casos de prueba de la API | [github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/tests](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/tests) |
| Código fuente | [github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/src](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/src)  |

## <a name="sdk-samples"></a>Ejemplos del SDK

Los ejemplos de código del equipo de desarrollo del SDK de Azure muestran el uso de la API. Puede encontrar estos ejemplos en [**Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/samples**](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/) en GitHub.

| Ejemplos | Descripción |
|---------|-------------|
| ["Hello world", sincrónicamente](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01a_HelloWorld.md) | Muestra cómo crear un cliente, autenticarse y controlar errores mediante métodos sincrónicos.|
| ["Hello world", asincrónicamente](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01b_HelloWorldAsync.md) | Muestra cómo crear un cliente, autenticarse y controlar errores mediante métodos asincrónicos.  |
| [Operaciones de nivel de servicio](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample02_Service.md) | Muestra cómo crear índices, indizadores, orígenes de datos, conjuntos de aptitudes y asignaciones de sinónimos. En este ejemplo también se muestra cómo obtener las estadísticas de servicio y cómo consultar un índice.  |
| [Operaciones de índice](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample03_Index.md) | Muestra cómo realizar una acción en un índice existente; en este caso, obtener un recuento de los documentos almacenados en el índice.  |
| [FieldBuilderIgnore](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample04_FieldBuilderIgnore.md) | Muestra una técnica para trabajar con tipos de datos no admitidos.  |
| [Indexación de documentos (modelo de inserción)](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample05_IndexingDocuments.md) | Indexación de modelos de tipo "inserción", en el que se envía una carga JSON a un índice en un servicio.   |
| [Ejemplo de clave de cifrado](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample06_EncryptedIndex.md) | Muestra el uso de una clave de cifrado administrada por el cliente para agregar una capa adicional de protección para el contenido confidencial.  |

## <a name="doc-samples"></a>Ejemplos de documentación

Los ejemplos de código del equipo de Cognitive Search muestran características y flujos de trabajo. En tutoriales, inicios rápidos y artículos de procedimientos se hace referencia a muchos de estos ejemplos. Puede encontrar estos ejemplos en [**Azure-Samples/azure-search-dotnet-samples**](https://github.com/Azure-Samples/azure-search-dotnet-samples) y en [**Azure-Samples/search-dotnet-getting-started**](https://github.com/Azure-Samples/search-dotnet-getting-started/) en GitHub.

| Ejemplos | Artículo  |
|---------|-------------|
| [quickstart](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart) | Código fuente de [Inicio rápido: Creación de un índice de búsqueda](search-get-started-dotnet.md). En este artículo se habla del flujo de trabajo básico para crear, cargar y consultar un índice de búsqueda con datos de ejemplo. |
| [search-website](https://github.com/azure-samples/azure-search-dotnet-samples/tree/master/search-website) | Código fuente de [Tutorial: Incorporación de la funcionalidad de búsqueda a las aplicaciones web](tutorial-csharp-overview.md). Muestra una aplicación de búsqueda de un extremo a otro que incluye un cliente enriquecido más componentes para hospedar la aplicación y controlar las solicitudes de búsqueda.|
| [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)  | Código fuente de [Uso de la biblioteca cliente de .NET](search-howto-dotnet-sdk.md). En este artículo se recorre el flujo de trabajo básico, pero con más detalle y se analiza el uso de la API.  |
| [DotNetHowToSynonyms](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms)  | Código fuente de [Ejemplo: Incorporación de sinónimos en C#](search-synonyms-tutorial-sdk.md). Las listas de sinónimos se usan para la expansión de consultas, lo que proporciona términos coincidentes que son ajenos a un índice. |
| [DotNetToIndexers](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) | Código fuente de [Tutorial: Indexación de datos Azure SQL mediante el SDK de .NET](search-indexer-tutorial.md). En este artículo se muestra cómo configurar un indexador de Azure SQL que tiene una programación, asignaciones de campos y parámetros.  |
| [DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)  | Código fuente de [Configuración de claves administradas por el cliente para el cifrado de datos](search-security-manage-encryption-keys.md). |
| [Creación de la primera aplicación en C#](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11) |  Código fuente de [Tutorial: Creación de la primera aplicación de búsqueda](tutorial-csharp-create-first-app.md). Aunque la mayoría de los ejemplos son aplicaciones de consola, en este ejemplo de MVC se usa una página web para el índice de ejemplo "Hotels", en el que se muestra la búsqueda básica, la paginación, la función autocompletar y las consultas, las facetas y los filtros sugeridos. |
| [multiple-data-sources](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources)  | Código fuente de [Tutorial: Indexación de varios orígenes de datos](tutorial-multiple-data-sources.md). |
|  [optimize-data-indexing](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing) | Código fuente de [Tutorial: Optimización de la indexación mediante la API de inserción](tutorial-optimize-indexing-push-api.md).  |
| [tutorial-ai-enrichment](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/tutorial-ai-enrichment)  | Código fuente de [Tutorial: Contenido de blobs de Azure con capacidad de búsqueda y generado mediante inteligencia artificial con el SDK de .NET](cognitive-search-tutorial-blob-dotnet.md).  |

> [!Tip]
> Pruebe el [Explorador de ejemplos](/samples/browse/?languages=csharp&products=azure-cognitive-search) para buscar ejemplos de código de Microsoft en GitHub, filtrados por producto, servicio y lenguaje.

## <a name="other-samples"></a>Otros ejemplos

Los siguientes ejemplos también se publican en el equipo de Cognitive Search, pero no se hace referencia a ellos en la documentación. Los archivos Léame asociados proporcionan instrucciones de uso.

| Ejemplos | Descripción |
|---------|-------------|
| [Indexación de Data Lake Gen2 mediante Azure AD](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/data-lake-gen2-acl-indexing/README.md) | Código fuente que muestra las conexiones del indexador y la indexación de archivos y carpetas de Azure Data Lake Gen2 protegidos mediante controles de acceso basado en rol y Azure AD. |
| [azure-search-power-skills](https://github.com/Azure-Samples/azure-search-power-skills)  | Código fuente de aptitudes personalizadas que se pueden consumir y que puede incorporar en sus soluciones ganadas.  |
| [Acelerador de soluciones de minería de conocimiento](/samples/azure-samples/azure-search-knowledge-mining/azure-search-knowledge-mining/) | Incluye plantillas, archivos complementarios e informes analíticos para ayudarle a crear el prototipo de una solución de minería de conocimiento integral.  |
| [Repositorio de aplicaciones de búsqueda de COVID-19](https://github.com/liamca/covid19search) | Repositorio de código fuente para la [aplicación de búsqueda de COVID-19](https://covid19search.azurewebsites.net/) basada en Cognitive Search. |
| [JFK](https://github.com/Microsoft/AzureSearch_JFK_Files) | Más información sobre la [solución JFK](https://www.microsoft.com/ai/ai-lab-jfk-files) |
| [Buscar + acelerador QnA Maker](https://github.com/Azure-Samples/search-qna-maker-accelerator) | Una [solución](https://techcommunity.microsoft.com/t5/azure-ai/qna-with-azure-cognitive-search/ba-p/2081381) que combina la eficacia de la búsqueda y QnA Maker. Vea el [sitio de demostración](https://aka.ms/qnaWithAzureSearchDemo) activo. |

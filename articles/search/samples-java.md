---
title: Ejemplos de Java
titleSuffix: Azure Cognitive Search
description: Busque ejemplos de código de Java de demostración de Azure Cognitive Search que usan el SDK de .NET de Azure para Java.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: 39547b3405e25faabef64b6a9c80d0405fe1e66a
ms.sourcegitcommit: e832f58baf0b3a69c2e2781bd8e32d4f1ae932c6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/27/2021
ms.locfileid: "110586396"
---
# <a name="java-code-samples-for-azure-cognitive-search"></a>Ejemplos de código de Java de Azure Cognitive Search

Obtenga información sobre los ejemplos de código de Java que muestran la funcionalidad y el flujo de trabajo de una solución de Azure Cognitive Search. En estos ejemplos se usa la [**biblioteca cliente de Azure Cognitive Search**](/java/api/overview/azure/search-documents-readme) para el [**SDK de Azure para Java**](/azure/developer/java/sdk), que puede explorar por medio de los vínculos siguientes.

| Destino | Vínculo |
|--------|------|
| Descarga del paquete | [search.maven.org/artifact/com.azure/azure-search-documents](https://search.maven.org/artifact/com.azure/azure-search-documents) |
| Referencia de API | [com.azure.search.documents](/java/api/com.azure.search.documents)  |
| Casos de prueba de la API | [github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/test](https://github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/test) |
| Código fuente | [github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src](https://github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src)  |

## <a name="sdk-samples"></a>Ejemplos del SDK

Los ejemplos de código del equipo de desarrollo del SDK de Azure muestran el uso de la API. Puede encontrar estos ejemplos en [**Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/samples**](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/samples) en GitHub.

| Ejemplos | Descripción |
|---------|-------------|
| [Creación de índices de búsqueda](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexExample.java) | Muestra cómo crear [índices de búsqueda](search-what-is-an-index.md). |
| [Creación de sinónimos](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SynonymMapsCreateExample.java) | Muestra cómo crear [asignaciones de sinónimos](search-synonyms.md).  |
| [Creación de indizadores de búsqueda](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexerExample.java) | Muestra cómo crear [indizadores](search-indexer-overview.md). |
| [Creación del origen de datos del indizador de búsqueda](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/DataSourceExample.java) | Muestra cómo crear orígenes de datos de indizadores, necesarios para la indización basada en indizadores de [orígenes de datos de Azure compatibles](search-indexer-overview.md#supported-data-sources). |
| [Creación de conjuntos de aptitudes](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateSkillsetExample.java) |  Muestra cómo crear [conjuntos de aptitudes](cognitive-search-working-with-skillsets.md) que son indizadores adjuntos, y que realizan el enriquecimiento basado en IA durante la indexación. |
| [Carga de documentos](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/IndexContentManagementExample.java) | Muestra cómo cargar o combinar documentos en un índice en una operación de [importación de datos](search-what-is-data-import.md). |
| [Sintaxis de consulta](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SearchAsyncWithFullyTypedDocumentsExample.java) | Muestra cómo configurar una [consulta básica](search-query-overview.md). |

## <a name="doc-samples"></a>Ejemplos de documentación

Los ejemplos de código del equipo de Cognitive Search muestran características y flujos de trabajo. En tutoriales, inicios rápidos y artículos de procedimientos se hace referencia a muchos de estos ejemplos. Puede encontrar estos ejemplos en [**Azure-Samples/azure-search-java-samples**](https://github.com/Azure-Samples/azure-search-java-samples) en GitHub.

| Ejemplos | Artículo | 
|---------|-------------|
| [quickstart](https://github.com/Azure-Samples/azure-search-java-samples/tree/java-rest-api/quickstart) | Código fuente de [Inicio rápido: Creación de un índice de búsqueda en Java y REST](search-get-started-java.md). Este ejemplo no se ha actualizado para el SDK de Java. Llama a las API REST. |

> [!Tip]
> Pruebe el [Explorador de ejemplos](/samples/browse/?languages=java&products=azure-cognitive-search) para buscar ejemplos de código de Microsoft en Github, filtrados por producto, servicio y lenguaje.

## <a name="other-samples"></a>Otros ejemplos

Los siguientes ejemplos también se publican en el equipo de Cognitive Search, pero no se hace referencia a ellos en la documentación. Los archivos Léame asociados proporcionan instrucciones de uso.

| Ejemplos | Descripción |
|---------|-------------|
| [search-java-getting-started](https://github.com/Azure-Samples/azure-search-java-samples/tree/master/search-java-getting-started) | Usa la biblioteca de cliente del SDK de Java para crear, cargar y consultar un índice de búsqueda. Actualmente, este es un ejemplo independiente. |
| [search-java-indexer-demo](https://github.com/Azure-Samples/azure-search-java-samples/tree/java-rest-api/search-java-indexer-demo) | Muestra un indizador de Azure Cosmos DB en Java. Este ejemplo no se ha actualizado para el SDK de Java. Llama a las API REST.|

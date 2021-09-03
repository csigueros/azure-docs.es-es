---
title: Ejemplos de REST
titleSuffix: Azure Cognitive Search
description: Busque ejemplos de código de REST de demostración de Azure Cognitive Search que usan las API REST de búsqueda o administración.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: 8e25c921b390211f159e7f845dca9c7cbc9168c7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121739062"
---
# <a name="rest-code-samples-for-azure-cognitive-search"></a>Ejemplos de código de REST para Azure Cognitive Search

Obtenga información sobre los ejemplos de API REST que muestran la funcionalidad y el flujo de trabajo de una solución de Azure Cognitive Search. En estos ejemplos se usan las [**API REST de búsqueda**](/rest/api/searchservice).

REST es la interfaz de programación definitiva para Azure Cognitive Search y todas las operaciones que se pueden invocar mediante programación están disponibles en primer lugar en REST y luego en los SDK. Por esta razón, la mayoría de los ejemplos de la documentación aprovechan las API REST para mostrar o explicar conceptos importantes.

Los ejemplos de REST se suelen desarrollar y probar en Postman, pero puede usar cualquier cliente que admita llamadas HTTP:

+ Empiece con [Inicio rápido: Creación de un índice de Azure Cognitive Search mediante las API REST](search-get-started-rest.md) para obtener ayuda para la formulación de llamadas HTTP.
+ Pruebe [Extensión de Visual Studio Code para Azure Cognitive Search](search-get-started-vs-code.md), actualmente en versión preliminar, si trabaja en Visual Studio Code.

## <a name="doc-samples"></a>Ejemplos de documentación

Los ejemplos de código del equipo de Cognitive Search muestran características y flujos de trabajo. En tutoriales, inicios rápidos y artículos de procedimientos se hace referencia a muchos de estos ejemplos. Puede encontrar estos ejemplos en [**Azure-Samples/azure-search-postman-samples**](https://github.com/Azure-Samples/azure-search-postman-samples) en GitHub.

| Ejemplos | Artículo |
|---------|---------|
| [Guía de inicio rápido](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Quickstart) | Código fuente de [Inicio rápido: Creación de un índice de búsqueda mediante las API REST](search-get-started-rest.md). En este artículo se habla del flujo de trabajo básico para crear, cargar y consultar un índice de búsqueda con datos de ejemplo. |
| [Tutorial](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Tutorial) | Código fuente de [Tutorial: Uso de REST e IA para generar contenido en el que se pueden realizar búsquedas desde blobs de Azure](cognitive-search-tutorial-blob.md). En este artículo se muestra cómo crear un conjunto de aptitudes que itere sobre blobs de Azure para extraer información e inferir la estructura.|
| [Debug-sessions](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions) | Código fuente de [Tutorial: Diagnóstico, reparación y confirmación de cambios en el conjunto de aptitudes](cognitive-search-tutorial-debug-sessions.md). En este artículo se muestra cómo usar una sesión de depuración de conjunto de aptitudes en Azure Portal. REST se usa para crear los objetos que se usan durante la depuración.|
| [custom-analyzers](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/custom-analyzers) | Código fuente de [Tutorial: Creación de un analizador personalizado para números de teléfono](tutorial-create-custom-analyzer.md). En este artículo se explica cómo usar analizadores para conservar patrones y caracteres especiales en el contenido que permite búsquedas.|
| [knowledge-store](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/knowledge-store) | Código fuente para [Creación de un almacén de conocimiento mediante REST y Postman](knowledge-store-create-rest.md). En este artículo se explican los pasos necesarios para rellenar un almacén de conocimiento usado para flujos de trabajo de minería de conocimiento. |
| [projections](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/projections) | Código fuente para [Definición de proyecciones en un almacén de conocimiento](knowledge-store-projections-examples.md). En este artículo se explica cómo especificar las estructuras de datos físicas en un almacén de conocimiento.|
| [index-encrypted-blobs](https://github.com/Azure-Samples/azure-search-postman-samples/commit/f5ebb141f1ff98f571ab84ac59dcd6fd06a46718) | Código fuente para [Indexación de blobs cifrados mediante indexadores de blobs y conjuntos de aptitudes en Azure Cognitive Search](search-howto-index-encrypted-blobs.md). En este artículo se muestra cómo indexar documentos en Azure Blob Storage que se han cifrado previamente mediante Azure Key Vault. |

> [!Tip]
> Pruebe el [Explorador de ejemplos](/samples/browse/?expanded=azure&languages=http&products=azure-cognitive-search) para buscar ejemplos de código de Microsoft en GitHub, filtrados por producto, servicio y lenguaje.

## <a name="other-samples"></a>Otros ejemplos

Los siguientes ejemplos también se publican en el equipo de Cognitive Search, pero no se hace referencia a ellos en la documentación. Los archivos Léame asociados proporcionan instrucciones de uso.

| Ejemplos | Descripción |
|---------|-------------|
| [Query-examples](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Query-examples) | Colecciones de Postman que muestran las diversas técnicas de consulta, como búsqueda aproximada, RegEx y búsqueda con caracteres comodín, autocompletar, etc. |

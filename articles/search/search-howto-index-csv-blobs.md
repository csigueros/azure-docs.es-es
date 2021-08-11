---
title: Búsqueda en BLOb CSV
titleSuffix: Azure Cognitive Search
description: Extraiga e importe archivos .csv de Azure Blob Storage mediante el modo de análisis delimitedText.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: cae696e8a7bd5fbe477a1f4b5af5d4838d7d599b
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/07/2021
ms.locfileid: "111557133"
---
# <a name="how-to-index-csv-blobs-using-delimitedtext-parsing-mode-and-blob-indexers-in-azure-cognitive-search"></a>Indexación de blobs CSV mediante el modo de análisis delimitedText e indexadores de blobs en Azure Cognitive Search

El [indexador de blobs](search-howto-indexing-azure-blob-storage.md) de Azure Cognitive Search proporciona un modo de análisis `delimitedText` para archivos CSV que trata cada línea del CSV como un documento de búsqueda independiente. Por ejemplo, dado el siguiente texto delimitado por comas, `delimitedText` daría como resultado dos documentos en el índice de búsqueda: 

```text
id, datePublished, tags
1, 2016-01-12, "azure-search,azure,cloud"
2, 2016-07-07, "cloud,mobile"
```

Sin el modo de análisis `delimitedText`, todo el contenido del archivo CSV se trataría como un documento de búsqueda.

Siempre que cree varios documentos de búsqueda desde un solo blob, asegúrese de consultar [Indexación de blobs para producir varios documentos de búsqueda](search-howto-index-one-to-many-blobs.md) para comprender cómo funcionan las asignaciones de claves de documentos. El indexador de blobs es capaz de buscar o generar valores que definen de forma única cada documento nuevo. En concreto, puede crear una `AzureSearch_DocumentKey` transitoria que se genera cuando se analiza un blob en partes más pequeñas, donde el valor se usa como clave del documento de búsqueda en el índice.

## <a name="setting-up-csv-indexing"></a>Configuración de la indexación de CSV

Para indexar blobs de CSV, cree o actualice una definición de indizador con el modo de análisis `delimitedText` en una solicitud [Crear indizador](/rest/api/searchservice/create-indexer):

```http
{
  "name" : "my-csv-indexer",
  ... other indexer properties
  "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
}
```

`firstLineContainsHeaders` indica que la primera línea (no en blanco) de cada blob contiene encabezados.
Si el blob no contienen una línea de encabezado inicial, los encabezados deben especificarse en la configuración del indexador: 

```http
"parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 
```

Puede personalizar el carácter delimitador mediante la configuración de `delimitedTextDelimiter`. Por ejemplo:

```http
"parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }
```

> [!NOTE]
> Actualmente, solo se admite la codificación UTF-8. Si necesita compatibilidad con otras codificaciones, vote a favor de ella en [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

> [!IMPORTANT]
> Cuando se utiliza el modo de análisis de texto delimitado, Azure Cognitive Search supone que todos los blobs del origen de datos serán CSV. Si necesita compatibilidad con una combinación de blobs CSV y no CSV en el mismo origen de datos, vote por ella en [UserVoice](https://feedback.azure.com/forums/263029-azure-search).
>

## <a name="request-examples"></a>Ejemplos de solicitud

Resumiendo, estos son los ejemplos de cargas útiles completas. 

Origen de datos: 

```http
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-blob-datasource",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
    "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
}   
```

Indexador:

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  "name" : "my-csv-indexer",
  "dataSourceName" : "my-blob-datasource",
  "targetIndexName" : "my-target-index",
  "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
}
```



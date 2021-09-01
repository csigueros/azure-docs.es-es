---
title: Búsqueda a través de blobs JSON
titleSuffix: Azure Cognitive Search
description: Rastree el contenido de texto de los blobs JSON de Azure mediante el indexador de blobs de Azure Cognitive Search. Los indexadores automatizan la ingesta de datos para orígenes de datos seleccionados, como Azure Blob Storage.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: ba6df6b29c9d7b9b388b34dab163a132272c6200
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123223914"
---
# <a name="how-to-index-json-blobs-using-a-blob-indexer-in-azure-cognitive-search"></a>Indexación de blobs JSON con el indexador de blobs de Azure Cognitive Search

En este artículo se muestra cómo [configurar un indexador de blobs](search-howto-indexing-azure-blob-storage.md) para blobs que constan de documentos JSON. Los blobs JSON de Azure Blob Storage normalmente asumen cualquiera de estas formas:

+ Un documento JSON individual.
+ Un documento JSON que contiene una matriz de elementos JSON bien formados.
+ Un documento JSON que contiene varias entidades, separadas por una línea nueva.

El indexador de blobs proporciona un parámetro **`parsingMode`** para optimizar la salida del documento de búsqueda en función de los modos de análisis de la estructura, que constan de las siguientes opciones:

| parsingMode | Documento JSON | Descripción |
|--------------|-------------|--------------|
| **`json`** | Uno por blob | (Valor predeterminado) Analiza los blobs JSON como un único fragmento de texto. Cada blob JSON se convierte en un único documento de búsqueda individual. |
| **`jsonArray`** | Varios por blob | Analiza una matriz JSON en el blob, donde cada elemento de la matriz se convierte en un documento de búsqueda independiente.  |
| **`jsonLines`** | Varios por blob | Analiza un blob que contiene varias entidades JSON (también una matriz), con elementos individuales separados por una nueva línea. El indexador inicia un nuevo documento de búsqueda después de cada nueva línea. |

En el caso de **`jsonArray`** y **`jsonLines`** , debe examinar [Indexación de blobs para producir varios documentos de búsqueda](search-howto-index-one-to-many-blobs.md) para conocer la forma en que el indexador de blobs controla la desambiguación de la clave del documento para varios documentos de búsqueda generados a partir del mismo blob.

En la definición del indizador, puede establecer [asignaciones de campo](search-indexer-field-mappings.md) para elegir las propiedades del documento JSON de origen que se utilizan para completar el índice de búsqueda de destino. Por ejemplo, cuando se usa el modo de análisis de **`jsonArray`** , si la matriz existe una como propiedad de nivel bajo, puede establecer una propiedad **`document root`** que indica dónde se coloca la matriz en el blob.

En las siguientes secciones se describe cada modo con más detalle. Si no está familiarizado con los conceptos y los clientes de indexador, consulte [Creación de un indexador de búsqueda](search-howto-create-indexers.md). También debe conocer los detalles de la [configuración básica del indexador de blobs](search-howto-indexing-azure-blob-storage.md), que no se repite aquí.

<a name="parsing-single-blobs"></a>

## <a name="index-single-json-documents-one-per-blob"></a>Indexación de documentos JSON únicos (uno por blob)

De forma predeterminada, los indexadores de blobs analizan blobs JSON como un único fragmento de texto, un documento de búsqueda para cada blob de un contenedor. Si el JSON tiene estructura, el documento de búsqueda puede reflejarla, con elementos individuales representados como campos individuales. Por ejemplo, suponga que tiene el siguiente documento JSON en Azure Blob Storage:

```http
{
    "article" : {
        "text" : "A hopefully useful article explaining how to parse JSON blobs",
        "datePublished" : "2020-04-13",
        "tags" : [ "search", "storage", "howto" ]    
    }
}
```

El indexador de blobs analiza el documento JSON en un documento de búsqueda único y carga un índice al hacer que coincidan los campos "text", "datePublished" y "tags" del origen con los campos de destino con nombre y tipo idénticos. Dado un índice con los campos "text", "datePublished" y "tags", el indexador de blobs puede inferir la asignación correcta sin que haya presente una asignación de campos en la solicitud.

Aunque el comportamiento predeterminado es un documento de búsqueda por blob JSON, el establecimiento del modo de análisis de "JSON" cambia las asignaciones del contenido de los campos internas para el contenido, promocionando los campos dentro de `content` a campos reales en el índice de búsqueda. Una definición de indexador de ejemplo para el modo de análisis de **`json`** podría ser similar a esta:

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-json-indexer",
    "dataSourceName" : "my-blob-datasource",
    "targetIndexName" : "my-target-index",
    "parameters" : { "configuration" : { "parsingMode" : "json" } }
}
```

> [!NOTE]
> Igual que sucede con todos los indexadores, si los campos no coinciden claramente, debería especificar explícitamente [asignaciones de campos](search-indexer-field-mappings.md) individuales, a menos que use las asignaciones de campos implícitos disponibles para el contenido y los metadatos del blob, como se describe en la [configuración básica del indexador de blobs](search-howto-indexing-azure-blob-storage.md).

### <a name="json-example-single-hotel-json-files"></a>Ejemplo de JSON (archivos JSON de hotel individuales)

El [conjunto de datos del documento JSON del hotel](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/hotels/hotel-json-documents) en GitHub es útil para probar el análisis de JSON, donde cada blob representa un archivo JSON con estructura. Los archivos de datos se pueden cargar en Blob Storage y se puede usar el **Asistente para importar datos** para evaluar rápidamente cómo se analiza este contenido en documentos de búsqueda individuales. 

El conjunto de datos consta de cinco blobs, cada uno de los cuales contiene un documento del hotel con una colección de direcciones y una colección de habitaciones. El indexador de blobs detecta ambas colecciones y refleja la estructura de los documentos de entrada en el esquema de índice.

<a name="parsing-arrays"></a>

## <a name="parse-json-arrays"></a>Analizar matrices JSON

Si lo prefiere, puede usar la opción de matriz JSON. Esta opción es útil cuando los blobs contienen una matriz de objetos JSON bien formados y se desea que cada elemento se convierta en un documento de búsqueda independiente. Si se usa **`jsonArrays`** , el siguiente blob JSON genera tres documentos independientes, cada uno de ellos con `"id"` y `"text"` campos.  

```text
[
    { "id" : "1", "text" : "example 1" },
    { "id" : "2", "text" : "example 2" },
    { "id" : "3", "text" : "example 3" }
]
```

La propiedad **`parameters`** del indexador contiene valores del modo de análisis. Para una matriz JSON, la definición del indizador debe ser similar a la del ejemplo siguiente.

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-json-indexer",
    "dataSourceName" : "my-blob-datasource",
    "targetIndexName" : "my-target-index",
    "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
}
```

### <a name="jsonarrays-example-clinical-trials-sample-data"></a>Ejemplo de jsonArrays (datos de ejemplo de pruebas clínicas)

El [conjunto de datos JSON de pruebas clínicas](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials/clinical-trials-json) en GitHub es útil para probar el análisis de matrices JSON. Los archivos de datos se pueden cargar en Blob Storage y se puede usar el **Asistente para importar datos** para evaluar rápidamente cómo se analiza este contenido en documentos de búsqueda individuales. 

El conjunto de datos consta de ocho blobs, y cada uno de ellos contiene una matriz JSON de entidades, hasta un total de cien entidades. Las entidades varían en lo que se refiere a los campos que se rellenan, pero el resultado final es un documento de búsqueda por entidad, desde todas las matrices y en todos los blobs.

<a name="nested-json-arrays"></a>

### <a name="parsing-nested-json-arrays"></a>Análisis de matrices JSON anidadas

En el caso de las matrices JSON que tienen elementos anidados, puede especificar un elemento **`documentRoot`** para indicar una estructura de varios niveles. Por ejemplo, si los blobs tienen el siguiente aspecto:

```http
{
    "level1" : {
        "level2" : [
            { "id" : "1", "text" : "Use the documentRoot property" },
            { "id" : "2", "text" : "to pluck the array you want to index" },
            { "id" : "3", "text" : "even if it's nested inside the document" }  
        ]
    }
}
```

Utilice esta configuración para indexar la matriz de la propiedad `level2`:

```http
{
    "name" : "my-json-array-indexer",
    ... other indexer properties
    "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
}
```

## <a name="parse-json-entities-separated-by-newlines"></a>Análisis de entidades JSON separadas por nuevas líneas

Si el blob contiene varias entidades JSON separadas por una nueva línea y quiere que cada elemento se convierta en un documento de búsqueda independiente, use **`jsonLines`** .

```text
{ "id" : "1", "text" : "example 1" }
{ "id" : "2", "text" : "example 2" }
{ "id" : "3", "text" : "example 3" }
```

Para las líneas JSON, la definición del indizador debe ser similar a la del ejemplo siguiente.

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-json-indexer",
    "dataSourceName" : "my-blob-datasource",
    "targetIndexName" : "my-target-index",
    "parameters" : { "configuration" : { "parsingMode" : "jsonLines" } }
}
```

## <a name="map-json-fields-to-search-fields"></a>Asignación de campos JSON a campos de búsqueda

Las asignaciones de campos se utilizan para asociar un campo de origen a un campo de destino en aquellas situaciones en las que los nombres de campo y los tipos no son idénticos. Pero las asignaciones de campo también se pueden usar para hacer coincidir partes de un documento JSON y "elevarlas" a campos de nivel superior del documento de búsqueda.

Este escenario se ilustra en el ejemplo siguiente. Para obtener información sobre las asignaciones de campos, en general, consulte el artículo sobre [asignaciones de campos](search-indexer-field-mappings.md).

```http
{
    "article" : {
        "text" : "A hopefully useful article explaining how to parse JSON blobs",
        "datePublished" : "2016-04-13"
        "tags" : [ "search", "storage", "howto" ]    
    }
}
```

Supongamos que tiene un índice de búsqueda con los siguientes campos: `text` del tipo `Edm.String`, `date` del tipo `Edm.DateTimeOffset` y `tags` del tipo `Collection(Edm.String)`. Tenga en cuenta la diferencia entre "datePublished" en el origen y el campo `date` en el índice. Para asignar JSON en la forma deseada, utilice las siguientes asignaciones de campo:

```http
"fieldMappings" : [
    { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
    { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
    { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
    ]
```

Los campos de origen se especifican con la notación [puntero JSON](https://tools.ietf.org/html/rfc6901). Comience con una barra diagonal para hacer referencia a la raíz del documento JSON y, después, seleccione en la propiedad que desee (a un nivel arbitrario de anidamiento) mediante una ruta de acceso separada por una barra diagonal.

También puede hacer referencia a elementos individuales de la matriz mediante un índice de base cero. Por ejemplo, para elegir el primer elemento de la matriz "etiquetas" del ejemplo anterior, use una asignación de campo como esta:

```http
{ "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }
```

> [!NOTE]
> Si **`sourceFieldName`** hace referencia a una propiedad que no existe en el Blob JSON, la asignación se omite y no aparece ningún error. Este comportamiento permite que la indización continúe para los blobs JSON que tienen un esquema diferente (que es un caso de uso común). Dado que no hay ninguna comprobación de validación, busque detenidamente las asignaciones para comprobar si hay errores de escritura, con el fin de que no pierda documentos por la razón equivocada.
>

## <a name="next-steps"></a>Pasos siguientes

+ [Configuración de indexadores de blobs](search-howto-indexing-azure-blob-storage.md)
+ [Definición de asignaciones de campos](search-indexer-field-mappings.md)
+ [Introducción a los indexadores](search-indexer-overview.md)
+ [Procedimientos para indexar blobs de CSV con indexador de blobs](search-howto-index-csv-blobs.md)
+ [Tutorial: Búsqueda de datos semiestructurados en Azure Blob Storage](search-semi-structured-data.md)
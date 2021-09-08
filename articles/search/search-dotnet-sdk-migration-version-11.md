---
title: Actualización a la versión 11 del SDK de .NET
titleSuffix: Azure Cognitive Search
description: Migración del código a la versión 11 del SDK de .NET para Azure Cognitive Search a partir de versiones anteriores.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/09/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: bb596e993981275bbf61efd2542dbf1b566e1a3a
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/29/2021
ms.locfileid: "113003897"
---
# <a name="upgrade-to-azure-cognitive-search-net-sdk-version-11"></a>Actualización a la versión 11 del SDK de .NET para Azure Cognitive Search

Si la solución de búsqueda se basa en el [**SDK de Azure para .NET**](/dotnet/azure/), este artículo le ayudará a migrar el código de versiones anteriores de [**Microsoft.Azure.Search**](/dotnet/api/overview/azure/search/client10) a la versión 11, la nueva biblioteca cliente [**Azure.Search.Documents**](/dotnet/api/overview/azure/search.documents-readme). La versión 11 es una biblioteca cliente totalmente rediseñada que ha publicado el equipo de desarrollo de Azure SDK (las versiones anteriores fueron creadas por el equipo de desarrollo de Azure Cognitive Search). 

Con [una excepción](#WhatsNew), todas las características de la versión 10 se implementan en la versión 11. Entre las principales diferencias se incluyen:

+ Un paquete (**Azure.Search.Documents**) en lugar de cuatro
+ Tres clientes en lugar de dos: SearchClient, SearchIndexClient y SearchIndexerClient
+ Diferencias de nomenclatura en una serie de API y pequeñas diferencias estructurales que simplifican algunas tareas

El [registro de cambios](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md) de la biblioteca cliente tiene una lista detallada de actualizaciones.

Todos los fragmentos y ejemplos de código de C# en la documentación de producto de Cognitive Search se han revisado para indicar la nueva biblioteca cliente **Azure.Search.Documents**.

## <a name="why-upgrade"></a>¿Por qué actualizar?

A continuación se resumen las ventajas de la actualización:

+ Se agregarán nuevas características solo a **Azure.Search.Documents**. La versión anterior, Microsoft.Azure.Search, es ahora un cliente heredado. Las actualizaciones de las bibliotecas heredadas se limitan únicamente a correcciones de errores de alta prioridad.

+ Coherencia con otras bibliotecas cliente de Azure. **Azure.Search.Documents** tiene una dependencia de [Azure.Core](/dotnet/api/azure.core) y [System.Text.Json](/dotnet/api/system.text.json), y sigue enfoques convencionales para tareas comunes como conexiones de cliente y autorización.

## <a name="package-comparison"></a>Comparación de paquetes

La versión 11 consolida y simplifica la administración de paquetes para que haya menos que administrar.

| Versión 10 y anteriores | Versión 11 |
|------------------------|------------|
| [Microsoft.Azure.Search](https://www.nuget.org/packages/Microsoft.Azure.Search/) </br>[Microsoft.Azure.Search.Service](https://www.nuget.org/packages/Microsoft.Azure.Search.Service/) </br>[Microsoft.Azure.Search.Data](https://www.nuget.org/packages/Microsoft.Azure.Search.Data/) </br>[Microsoft.Azure.Search.Common](https://www.nuget.org/packages/Microsoft.Azure.Search.Common/)  | [Paquete Azure.Search.Documents](https://www.nuget.org/packages/Azure.Search.Documents/) |

## <a name="client-comparison"></a>Comparación de clientes

En la siguiente tabla se relacionan las bibliotecas cliente de las dos versiones.

|  Operaciones de cliente | Microsoft.Azure.Search&nbsp;(v. 10) | Azure.Search.Documents&nbsp;(v. 11) |
|---------------------|------------------------------|------------------------------|
| Tiene como destino la colección de documentos de un índice (consultas e importación de datos) | [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) | [SearchClient](/dotnet/api/azure.search.documents.searchclient) |
| Tiene como destino objetos relacionados con índices (índices, analizadores, mapas de sinónimos) | [SearchServiceClient](/dotnet/api/microsoft.azure.search.searchserviceclient) | [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) |
| Tiene como destino objetos relacionados con el indexador (indexadores, orígenes de datos, conjuntos de aptitudes) | [SearchServiceClient](/dotnet/api/microsoft.azure.search.searchserviceclient) | [SearchIndexerClient (**nueva**)](/dotnet/api/azure.search.documents.indexes.searchindexerclient) |

> [!Caution]
> Observe que SearchIndexClient existe en ambas versiones, pero tiene como destino diferentes operaciones. En la versión 10, SearchIndexClient crea índices y otros objetos. En la versión 11, SearchIndexClient funciona con índices existentes y tiene como destino la colección de documentos con las API de ingesta de datos y consultas. Para evitar confusiones al actualizar el código, tenga en cuenta el orden en que se actualizan las referencias de cliente. Si sigue la secuencia de [pasos de actualización](#UpgradeSteps), debería poder mitigar cualquier problema de reemplazo de cadenas.

<a name="naming-differences"></a>

## <a name="naming-and-other-api-differences"></a>Nomenclatura y otras diferencias de API

Además de las diferencias de cliente (indicadas anteriormente y que, por tanto, se omitieron aquí), se ha cambiado el nombre de varias API y, en algunos casos, se han rediseñado. A continuación se resumen las diferencias en los nombres de clase. Esta lista no es exhaustiva, pero agrupa los cambios de API por tarea, lo que puede resultar útil para las revisiones en bloques de código específicos. Para ver una lista de actualizaciones de API, consulte el [registro de cambios](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md) de `Azure.Search.Documents` en GitHub.

### <a name="authentication-and-encryption"></a>Autenticación y cifrado

| Versión 10 | Equivalente en la versión 11 |
|------------|-----------------------|
| [SearchCredentials](/dotnet/api/microsoft.azure.search.searchcredentials) | [AzureKeyCredential](/dotnet/api/azure.azurekeycredential) |
| EncryptionKey (sin documentar en la referencia de API. La compatibilidad con esta API ha pasado a estar disponible con carácter general en v10, pero solo estaba disponible en el [SDK de versión preliminar](https://www.nuget.org/packages/Microsoft.Azure.Search/8.0.0-preview)). | [SearchResourceEncryptionKey](/dotnet/api/azure.search.documents.indexes.models.searchresourceencryptionkey) |

### <a name="indexes-analyzers-synonym-maps"></a>Índices, analizadores y mapas de sinónimos

| Versión 10 | Equivalente en la versión 11 |
|------------|-----------------------|
| [Index](/dotnet/api/microsoft.azure.documents.index) | [SearchIndex](/dotnet/api/azure.search.documents.indexes.models.searchindex) |
| [Campo](/dotnet/api/microsoft.azure.search.models.field) | [SearchField](/dotnet/api/azure.search.documents.indexes.models.searchfield) |
| [DataType](/dotnet/api/microsoft.azure.search.models.datatype) | [SearchFieldDataType](/dotnet/api/azure.search.documents.indexes.models.searchfielddatatype) |
| [ItemError](/dotnet/api/microsoft.azure.search.models.itemerror) | [SearchIndexerError](/dotnet/api/azure.search.documents.indexes.models.searchindexererror) |
| [Analyzer](/dotnet/api/microsoft.azure.search.models.analyzer) | [LexicalAnalyzer](/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzer) (también, de `AnalyzerName` a `LexicalAnalyzerName`) |
| [AnalyzeRequest](/dotnet/api/microsoft.azure.search.models.analyzerequest) | [AnalyzeTextOptions](/dotnet/api/azure.search.documents.indexes.models.analyzetextoptions) |
| [StandardAnalyzer](/dotnet/api/microsoft.azure.search.models.standardanalyzer) | [LuceneStandardAnalyzer](/dotnet/api/azure.search.documents.indexes.models.lucenestandardanalyzer) |
| [StandardTokenizer](/dotnet/api/microsoft.azure.search.models.standardtokenizer) | [LuceneStandardTokenizer](/dotnet/api/azure.search.documents.indexes.models.lucenestandardtokenizer) (también, de `StandardTokenizerV2` a `LuceneStandardTokenizerV2`) |
| [TokenInfo](/dotnet/api/microsoft.azure.search.models.tokeninfo) | [AnalyzedTokenInfo](/dotnet/api/azure.search.documents.indexes.models.analyzedtokeninfo) |
| [Tokenizador](/dotnet/api/microsoft.azure.search.models.tokenizer) | [LexicalTokenizer](/dotnet/api/azure.search.documents.indexes.models.lexicaltokenizer) (también, de `TokenizerName` a `LexicalTokenizerName`) |
| [SynonymMap.Format](/dotnet/api/microsoft.azure.search.models.synonymmap.format) | Ninguno. Se eliminan las referencias a `Format`. |

Se han optimizado las definiciones de campo: [SearchableField](/dotnet/api/azure.search.documents.indexes.models.searchablefield), [SimpleField](/dotnet/api/azure.search.documents.indexes.models.simplefield) y [ComplexField](/dotnet/api/azure.search.documents.indexes.models.complexfield) son nuevas API para crear definiciones de campo.

### <a name="indexers-datasources-skillsets"></a>Indizadores, orígenes de datos y conjuntos de aptitudes

| Versión 10 | Equivalente en la versión 11 |
|------------|-----------------------|
| [Indizador](/dotnet/api/microsoft.azure.search.models.indexer) | [SearchIndexer](/dotnet/api/azure.search.documents.indexes.models.searchindexer) |
| [DataSource](/dotnet/api/microsoft.azure.search.models.datasource) | [SearchIndexerDataSourceConnection](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) |
| [Habilidad](/dotnet/api/microsoft.azure.search.models.skill) | [SearchIndexerSkill](/dotnet/api/azure.search.documents.indexes.models.searchindexerskill) |
| [Conjunto de aptitudes](/dotnet/api/microsoft.azure.search.models.skillset) | [SearchIndexerSkillset](/dotnet/api/azure.search.documents.indexes.models.searchindexerskill) |
| [DataSourceType](/dotnet/api/microsoft.azure.search.models.datasourcetype) | [SearchIndexerDataSourceType](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype) |

### <a name="data-import"></a>Importación de datos

| Versión 10 | Equivalente en la versión 11 |
|------------|-----------------------|
| [IndexAction](/dotnet/api/microsoft.azure.search.models.indexaction) | [IndexDocumentsAction](/dotnet/api/azure.search.documents.models.indexdocumentsaction) |
| [IndexBatch](/dotnet/api/microsoft.azure.search.models.indexbatch) | [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch) |

### <a name="query-requests-and-responses"></a>Solicitudes y respuestas de consultas

| Versión 10 | Equivalente en la versión 11 |
|------------|-----------------------|
| [DocumentsOperationsExtensions.SearchAsync](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.searchasync) | [SearchClient.SearchAsync](/dotnet/api/azure.search.documents.searchclient.searchasync) |
| [DocumentSearchResult](/dotnet/api/microsoft.azure.search.models.documentsearchresult-1) | [SearchResult](/dotnet/api/azure.search.documents.models.searchresult-1) o [SearchResults](/dotnet/api/azure.search.documents.models.searchresults-1), en función de si el resultado está en un único documento o en varios. |
| [DocumentSuggestResult](/dotnet/api/microsoft.azure.search.models.documentsuggestresult-1) | [SuggestResults](/dotnet/api/azure.search.documents.models.suggestresults-1) |
| [SearchParameters](/dotnet/api/microsoft.azure.search.models.searchparameters) |  [SearchOptions](/dotnet/api/azure.search.documents.searchoptions)  |
| [SuggestParameters](/dotnet/api/microsoft.azure.search.models.suggestparameters) |  [SuggestOptions](/dotnet/api/azure.search.documents.suggestoptions) |
| [SearchParameters.Filter](/dotnet/api/microsoft.azure.search.models.searchparameters.filter) |  [SearchFilter](/dotnet/api/azure.search.documents.searchfilter) (una nueva clase para construir expresiones de filtro de OData) |

### <a name="json-serialization"></a>Serialización de JSON

De forma predeterminada, el SDK de Azure usa [System.Text.Json](/dotnet/api/system.text.json) para la serialización de JSON. Para ello, se basa en las capacidades de esas API para controlar las transformaciones de texto que se implementaron previamente a través de una clase nativa [SerializePropertyNamesAsCamelCaseAttribute](/dotnet/api/microsoft.azure.search.models.serializepropertynamesascamelcaseattribute), que no tiene homólogo en la nueva biblioteca.

Para serializar los nombres de propiedad en camelCase, puede usar [JsonPropertyNameAttribute](/dotnet/api/system.text.json.serialization.jsonpropertynameattribute) (similar a [este ejemplo](https://github.com/Azure/azure-sdk-for-net/tree/d263f23aa3a28ff4fc4366b8dee144d4c0c3ab10/sdk/search/Azure.Search.Documents#use-c-types-for-search-results)).

Como alternativa, puede establecer una propiedad [JsonNamingPolicy](/dotnet/api/system.text.json.jsonnamingpolicy) proporcionada en [JsonSerializerOptions](/dotnet/api/system.text.json.jsonserializeroptions). El siguiente ejemplo de código System.Text.Json, tomado del [archivo Léame Microsoft.Azure.Core.Spatial](https://github.com/Azure/azure-sdk-for-net/blob/259df3985d9710507e2454e1591811f8b3a7ad5d/sdk/core/Microsoft.Azure.Core.Spatial/README.md#deserializing-documents) muestra el uso de camelCase sin tener que atribuir cada propiedad:

```csharp
// Get the Azure Cognitive Search endpoint and read-only API key.
Uri endpoint = new Uri(Environment.GetEnvironmentVariable("SEARCH_ENDPOINT"));
AzureKeyCredential credential = new AzureKeyCredential(Environment.GetEnvironmentVariable("SEARCH_API_KEY"));

// Create serializer options with our converter to deserialize geographic points.
JsonSerializerOptions serializerOptions = new JsonSerializerOptions
{
    Converters =
    {
        new MicrosoftSpatialGeoJsonConverter()
    },
    PropertyNamingPolicy = JsonNamingPolicy.CamelCase
};

SearchClientOptions clientOptions = new SearchClientOptions
{
    Serializer = new JsonObjectSerializer(serializerOptions)
};

SearchClient client = new SearchClient(endpoint, "mountains", credential, clientOptions);
Response<SearchResults<Mountain>> results = client.Search<Mountain>("Rainier");
```

Si usa Newtonsoft.Json para la serialización de JSON, puede pasar directivas de nomenclatura globales mediante atributos similares o mediante las propiedades de [JsonSerializerSettings](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonSerializerSettings.htm). Para obtener un ejemplo equivalente al anterior, vea el [ejemplo de deserialización de documentos](https://github.com/Azure/azure-sdk-for-net/blob/259df3985d9710507e2454e1591811f8b3a7ad5d/sdk/core/Microsoft.Azure.Core.Spatial.NewtonsoftJson/README.md) en el archivo Léame de Newtonsoft.Json.

<a name="WhatsNew"></a>

## <a name="inside-v11"></a>Dentro de v11

Cada versión de una biblioteca cliente de Azure Cognitive Search tiene como destino su versión correspondiente de la API REST. La API REST se considera fundamental para el servicio, con SDK individuales que encapsulan una versión de la API REST. Como desarrollador de .NET, puede ser útil revisar la [documentación de la API REST](/rest/api/searchservice/) más detallada, para profundizar más en objetos u operaciones específicos. La versión 11 tiene como destino el [servicio de búsqueda 2020-06-30](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/search/data-plane/Azure.Search/preview/2020-06-30/searchservice.json). 

La versión 11.0 es totalmente compatible con los siguientes objetos y operaciones:

+ Creación y administración de índices
+ Creación y administración de mapas de sinónimos
+ Creación y administración de indexadores
+ Creación y administración de orígenes de datos de indexadores
+ Creación y administración de conjuntos de aptitudes
+ Todos los tipos de consulta y sintaxis

Adiciones de la versión 11.1 (detalles en el [registro de cambios](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md#1110-2020-08-11)):

+ [FieldBuilder](/dotnet/api/azure.search.documents.indexes.fieldbuilder) (se agregó en la versión 11.1)
+ [Propiedad Serializer](/dotnet/api/azure.search.documents.searchclientoptions.serializer) (se agregó en la versión 11.1) para admitir la serialización personalizada

Adiciones de la versión 11.2 (detalles en el [registro de cambios](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md#1120-2021-02-10)):

+ Se ha agregado la propiedad [EncryptionKey](/dotnet/api/azure.search.documents.indexes.models.searchindexer.encryptionkey) a indexadores, orígenes de datos y conjuntos de aptitudes
+ Compatibilidad con la propiedad [IndexingParameters.IndexingParametersConfiguration](/dotnet/api/azure.search.documents.indexes.models.indexingparametersconfiguration)
+ Los [tipos geoespaciales](/dotnet/api/azure.search.documents.indexes.models.searchfielddatatype.geographypoint) se admiten de forma nativa en [FieldBuilder](/dotnet/api/azure.search.documents.indexes.fieldbuilder.build). [SearchFilter](/dotnet/api/azure.search.documents.searchfilter) puede codificar los tipos geométricos de Microsoft.Spatial sin una dependencia de ensamblado explícita.

  También puede continuar declarando explícitamente una dependencia de [Microsoft.Spatial](https://www.nuget.org/packages/Microsoft.Spatial/). Hay disponibles ejemplos de esta técnica para [System.Text.Json](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Microsoft.Azure.Core.Spatial/README.md) y [Newtonsoft.Json](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Microsoft.Azure.Core.Spatial.NewtonsoftJson/README.md).

Actualmente no se admite en ninguna versión de Azure.Search.Documents:

+ [Almacén de conocimiento](knowledge-store-concept-intro.md)

## <a name="before-upgrading"></a>Antes de actualizar

+ Se han actualizado los [inicios rápidos](search-get-started-dotnet.md), los tutoriales y los [ejemplos de C#](samples-dotnet.md) para que usen el paquete Azure.Search.Documents. Se recomienda revisar los ejemplos y tutoriales existentes para obtener información sobre las nuevas API antes de comenzar con una migración.

+ En [Uso de Azure.Search.Documents en una aplicación de .NET con C#](search-howto-dotnet-sdk.md) se indican las API más usadas. Incluso los usuarios con conocimientos de Cognitive Search pueden querer revisar esta introducción a la nueva biblioteca antes de llevar a cabo una migración.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Pasos para actualizar

Los pasos siguientes le ayudarán a comenzar a realizar una migración de código recorriendo el primer conjunto de tareas necesarias, especialmente en lo que respecta a las referencias de cliente.

1. Instale el paquete [Azure.Search.Documents](https://www.nuget.org/packages/Azure.Search.Documents/) haciendo clic con el botón derecho en las referencias del proyecto y seleccione "Administrar paquetes NuGet..." en Visual Studio.

1. Reemplace las directivas "using" de Microsoft.Azure.Search por lo siguiente:

   ```csharp
   using Azure;
   using Azure.Search.Documents;
   using Azure.Search.Documents.Indexes;
   using Azure.Search.Documents.Indexes.Models;
   using Azure.Search.Documents.Models;
   ```

1. Si hay clases que requieren la serialización de JSON, reemplace `using Newtonsoft.Json` por `using System.Text.Json.Serialization`.

1. Revise el código de autenticación del cliente. En versiones anteriores, se usarían propiedades en el objeto de cliente para establecer la clave de API (por ejemplo, la propiedad [SearchServiceClient. Credentials](/dotnet/api/microsoft.azure.search.searchserviceclient.credentials)). En la versión actual, use la clase [AzureKeyCredential](/dotnet/api/azure.azurekeycredential) para pasar la clave como una credencial, de modo que, si es necesario, pueda actualizar la clave de API sin crear objetos de cliente.

   Las propiedades del cliente se han simplificado a solo `Endpoint`, `ServiceName` y `IndexName` (cuando es necesario). En el ejemplo siguiente se usa la clase [Uri](/dotnet/api/system.uri) del sistema para proporcionar el punto de conexión y la clase [Environment](/dotnet/api/system.environment) para leer el valor de la clave:

   ```csharp
   Uri endpoint = new Uri(Environment.GetEnvironmentVariable("SEARCH_ENDPOINT"));
   AzureKeyCredential credential = new AzureKeyCredential(
      Environment.GetEnvironmentVariable("SEARCH_API_KEY"));
   SearchIndexClient indexClient = new SearchIndexClient(endpoint, credential);
   ```

1. Agregue nuevas referencias de cliente para los objetos relacionados con indexadores. Si usa indexadores, orígenes de usuario o conjuntos de habilidades, cambie las referencias de cliente a [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient). Este cliente es nuevo en la versión 11 y no tiene antecedente.

1. Revise las colecciones y las listas. En el nuevo SDK, todas las listas son de solo lectura para evitar problemas descendentes si la lista contiene valores NULL. El cambio de código consiste en agregar elementos a una lista. Por ejemplo, en lugar de asignar cadenas a una propiedad Select, debe agregarlas como se indica a continuación:

   ```csharp
   var options = new SearchOptions
    {
       SearchMode = SearchMode.All,
       IncludeTotalCount = true
    };

    // Select fields to return in results.
    options.Select.Add("HotelName");
    options.Select.Add("Description");
    options.Select.Add("Tags");
    options.Select.Add("Rooms");
    options.Select.Add("Rating");
    options.Select.Add("LastRenovationDate");
   ```

   Select, Facets, SearchFields, SourceFields, ScoringParameters y OrderBy son listas que ahora se deben reconstruir.

1. Actualice las referencias de cliente para las consultas y la importación de datos. Las instancias de [SearchIndexClient](/dotnet/api/microsoft.azure.search.searchindexclient) deben cambiarse a [SearchClient](/dotnet/api/azure.search.documents.searchclient). Para evitar confusión con los nombres, asegúrese de detectar todas las instancias antes de continuar con el siguiente paso.

1. Actualice las referencias de cliente en los objetos index, synonym map y analyzer. Las instancias de [SearchServiceClient](/dotnet/api/microsoft.azure.search.searchserviceclient) deben cambiarse a [SearchIndexClient](/dotnet/api/microsoft.azure.search.searchindexclient). 

1. Para el resto del código, actualice las clases, los métodos y las propiedades para usar las API de la nueva biblioteca. La sección [Nomenclatura y otras diferencias de API](#naming-differences) es un buen sitio para empezar, pero también puede revisar el [registro de cambios](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md).

   Si tiene problemas para encontrar API equivalentes, se recomienda registrar un problema en [https://github.com/MicrosoftDocs/azure-docs/issues](https://github.com/MicrosoftDocs/azure-docs/issues) para que podamos mejorar la documentación o investigar el problema.

1. Recompilar la solución. Después de corregir las advertencias o los errores de compilación, puede realizar cambios en la aplicación para aprovechar las ventajas de la [nueva funcionalidad](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes"></a>Últimos cambios

Dado los cambios radicales producidos en las bibliotecas y las API, una actualización a la versión 11 no es algo trivial y constituye un cambio importante en el sentido de que el código ya no será compatible con la versión 10 y anteriores. Para ver todas las diferencias, vea el [registro de cambios](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md) de `Azure.Search.Documents`.

En lo que respecta a las actualizaciones de la versión del servicio, donde los cambios de código de la versión 11 se relacionan con la funcionalidad existente (y no solo con la refactorización de las API), encontrará los siguientes cambios de comportamiento:

+ El [algoritmo de clasificación BM25](index-ranking-similarity.md) reemplaza el algoritmo de clasificación anterior por una tecnología más reciente. Los servicios nuevos usarán este algoritmo de manera automática. En el caso de los servicios existentes, debe establecer los parámetros para usar el algoritmo nuevo.

+ Los [resultados ordenados](search-query-odata-orderby.md) de los valores NULL han cambiado en esta versión y aparecen en primer lugar si la ordenación es `asc` y en el último si la ordenación es `desc`. Si ha escrito código para controlar cómo se ordenan los valores NULL, debe revisar y, posiblemente, quitar ese código si ya no es necesario.

## <a name="next-steps"></a>Pasos siguientes

+ [Uso de Azure.Search.Documents en una aplicación de .NET con C#](search-howto-dotnet-sdk.md)
+ [Tutorial: Incorporación de la funcionalidad de búsqueda a las aplicaciones web](tutorial-csharp-overview.md)
+ [Paquete Azure.Search.Documents](https://www.nuget.org/packages/Azure.Search.Documents/)
+ [Ejemplos en GitHub](https://github.com/azure/azure-sdk-for-net/tree/Azure.Search.Documents_11.0.0/sdk/search/Azure.Search.Documents/samples)
+ [Referencia de la API de Azure.Search.Document](/dotnet/api/overview/azure/search.documents-readme)

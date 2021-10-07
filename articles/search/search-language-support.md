---
title: Indexación multilingüe para consultas de búsqueda que no están en inglés
titleSuffix: Azure Cognitive Search
description: Cree un índice que admita contenido en varios idiomas y, a continuación, cree consultas con el ámbito de ese contenido.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/08/2021
ms.openlocfilehash: 6511764697adcd6c8743efe41d276ccef3ff1bb0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124823912"
---
# <a name="create-an-index-for-multiple-languages-in-azure-cognitive-search"></a>Creación de un índice para varios idiomas en Azure Cognitive Search

Un requisito fundamental de una aplicación de búsqueda multilingüe es la posibilidad de buscar y recuperar los resultados en el idioma del usuario. En Azure Cognitive Search, una manera de satisfacer los requisitos de idioma de una aplicación multilingüe es crear una serie de campos dedicados para almacenar cadenas en un idioma determinado y luego restringir la búsqueda de texto completo a solo esos campos en el momento de la consulta.

+ En las definiciones de campo, [especifique un analizador de idioma](index-add-language-analyzers.md) que invoque las reglas lingüísticas del idioma de destino. 

+ En la solicitud de consulta, establezca el parámetro `searchFields` para que la búsqueda de texto completo abarque campos específicos y después use `select` para devolver solo los campos que tienen contenido compatible.

El éxito de esta técnica depende de la integridad del contenido del campo. Azure Cognitive Search no traduce las cadenas ni realiza la detección del idioma como parte de la ejecución de la consulta. Es responsabilidad suya asegurarse de que los campos contienen las cadenas que espera.

## <a name="define-fields-for-content-in-different-languages"></a>Definición de campos para el contenido en distintos idiomas

En Azure Cognitive Search, las consultas tienen como destino un índice único. Los desarrolladores que quieran proporcionar cadenas específicas del idioma en una única experiencia de búsqueda suele definen campos dedicados para almacenar los valores: un campo para las cadenas en inglés, otro para las de francés, etc.

La propiedad "analizador" en una definición de campo se usa para configurar el [analizador de idioma](index-add-language-analyzers.md). Se usará para la indexación y la ejecución de la consulta.

```JSON
{
  "name": "hotels-sample-index",
  "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": "en.microsoft"
    },
    {
      "name": "Description_fr",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": "fr.microsoft"
    },
```

## <a name="build-and-load-an-index"></a>Creación y carga de un índice

Un paso intermedio (y, quizás, obvio) es que tiene que [crear y completar el índice](search-get-started-dotnet.md) antes de formular una consulta. Hemos mencionado este paso aquí para mayor integridad. Una forma de determinar la disponibilidad del índice es comprobar la lista de índices en el [portal](https://portal.azure.com).

> [!TIP]
> La detección del idioma y la traducción de texto son compatibles durante la ingesta de datos a través del [enriquecimiento de IA](cognitive-search-concept-intro.md) y los [conjuntos de aptitudes](cognitive-search-working-with-skillsets.md). Si tiene un origen de datos de Azure con contenido en varios idiomas, puede probar las características de traducción y detección de idiomas mediante el [Asistente para la importación de datos](cognitive-search-quickstart-blob.md).

## <a name="constrain-the-query-and-trim-results"></a>Restricción de la consulta y recorte de los resultados

Los parámetros de la consulta se usan para limitar la búsqueda a campos específicos y luego recortar los resultados de los campos que no resulten útiles para su escenario. 

| Parámetros | Propósito |
|-----------|--------------|
| **searchFields** | Limita la búsqueda de texto completo a la lista de campos con nombre. |
| **$select** | Recorta la respuesta para incluir solo los campos que especifique. De forma predeterminada, se devuelven todos los campos recuperables. El parámetro **$select** le permite elegir cuáles devolver. |

Dado un objetivo de restricción de la búsqueda a los campos que contienen cadenas en francés, usaría **searchFields** como destino de la consulta en los campos que contienen cadenas en ese idioma.

No es necesario especificar el analizador en una solicitud de consulta. Siempre se usará un analizador de idioma en la definición de campo durante el procesamiento de la consulta. En el caso de las consultas que especifican varios campos que invoquen distintos analizadores de idioma, los analizadores asignados para cada campo procesarán los términos o frases de forma independiente.

De forma predeterminada, una búsqueda devuelve todos los campos que están marcados como recuperables. Por lo tanto, puede que quiera excluir los campos que no se ajustan a la experiencia de búsqueda específica del idioma que desea proporcionar. En concreto, si limita la búsqueda a un campo con cadenas en francés, probablemente desee excluir los campos con cadenas en inglés de los resultados. Mediante el parámetro de consulta **$select** puede controlar qué campos se devuelven a la aplicación que realiza la llamada.

#### <a name="example-in-rest"></a>Ejemplo en REST

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "animaux acceptés",
    "searchFields": "Tags, Description_fr",
    "select": "HotelName, Description_fr, Address/City, Address/StateProvince, Tags",
    "count": "true"
}
```

#### <a name="example-in-c"></a>Ejemplo en C#

```csharp
private static void RunQueries(SearchClient srchclient)
{
    SearchOptions options;
    SearchResults<Hotel> response;

    options = new SearchOptions()
    {
        IncludeTotalCount = true,
        Filter = "",
        OrderBy = { "" }
    };

    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Description_fr");
    options.SearchFields.Add("Tags");
    options.SearchFields.Add("Description_fr");

    response = srchclient.Search<Hotel>("*", options);
    WriteDocuments(response);
}
```

## <a name="boost-language-specific-fields"></a>Priorización de campos específicos del idioma

A veces se desconoce el idioma del agente que emite una consulta, en cuyo caso la consulta puede emitir en todos los campos al mismo tiempo. Se puede definir una preferencia de resultados de IA en un determinado idioma mediante los [perfiles de puntuación](index-add-scoring-profiles.md). En el ejemplo siguiente, las coincidencias encontradas en la descripción en inglés tendrán una puntuación relativa mayor que las coincidencias en otros idiomas:

```JSON
  "scoringProfiles": [
    {
      "name": "englishFirst",
      "text": {
        "weights": { "description": 2 }
      }
    }
  ]
```

A continuación, se incluye el perfil de puntuación en la solicitud de búsqueda:

```http
POST /indexes/hotels/docs/search?api-version=2020-06-30
{
  "search": "pets allowed",
  "searchFields": "Tags, Description",
  "select": "HotelName, Tags, Description",
  "scoringProfile": "englishFirst",
  "count": "true"
}
```

## <a name="next-steps"></a>Pasos siguientes

+ [Adición de un analizador de idioma](index-add-language-analyzers.md)
+ [Funcionamiento de la búsqueda de texto completo en Azure Cognitive Search](search-lucene-query-architecture.md)
+ [API de REST de documentos de búsqueda](/rest/api/searchservice/search-documents)
+ [Información general sobre el enriquecimiento de IA](cognitive-search-concept-intro.md)
+ [Información general del conjunto de aptitudes](cognitive-search-working-with-skillsets.md)
---
title: Adición de la corrección ortográfica
titleSuffix: Azure Cognitive Search
description: Asocie la corrección ortográfica a la canalización de consulta para corregir los errores tipográficos en los términos de consulta antes de ejecutar la consulta.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/29/2021
ms.custom: references_regions
ms.openlocfilehash: 98aec7fc2a3857ac50125e7e49c5f9ab105a49d7
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129535923"
---
# <a name="add-spell-check-to-queries-in-cognitive-search"></a>Adición de la corrección ortográfica a las consultas en Cognitive Search

> [!IMPORTANT]
> La corrección ortográfica está en versión preliminar pública en los [términos de uso complementarios](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Solo está disponible a través de Azure Portal y la API REST en versión preliminar.

Se pueden mejorar las coincidencias mediante la corrección ortográfica de los términos de consulta de búsqueda individuales antes de que lleguen al motor de búsqueda. El parámetro **speller** es compatible con todos los tipos de consulta: [simple](query-simple-syntax.md), [completa](query-lucene-syntax.md) y [semántica](semantic-how-to-query-request.md), la nueva opción, actualmente en versión preliminar pública.

Speller se publicó junto con la [versión preliminar de búsqueda semántica](semantic-search-overview.md) y comparte el parámetro queryLanguage, pero hay que tener en cuenta que es una característica independiente con sus propios requisitos previos. No se aplican registros ni cargos adicionales por usar esta función.

## <a name="prerequisites"></a>Requisitos previos

Para usar la revisión ortográfica, necesitará lo siguiente:

+ Un servicio de búsqueda de nivel básico o superior, en cualquier región.

+ Índice de búsqueda existente, con contenido en un [idioma admitido](#supported-languages).

+ [Una solicitud de consulta](/rest/api/searchservice/preview-api/search-documents) que tenga "api-version=2020-06-30-Preview", "speller=lexicon" y "queryLanguage" establecidos en un [idioma admitido](#supported-languages). El corrector ortográfico funciona con las cadenas que se pasan en el parámetro de "búsqueda". No es compatible con los filtros.

Use un cliente de búsqueda que admita las API de versión preliminar en la solicitud de consulta. Para REST, Puede usar [Postman](search-get-started-rest.md), [Visual Studio Code](search-get-started-vs-code.md) o código que haya modificado para realizar llamadas de REST a las API en versión preliminar. También puede usar las versiones beta de los SDK de Azure.

| Biblioteca de cliente | Versiones |
|----------|----------|
| API DE REST | [2021-04-30 (versión preliminar)](/rest/api/searchservice/index-preview) o 2020-06-30 (versión preliminar) |
| SDK de Azure para .NET | [versión 11.3.0-beta.2](https://www.nuget.org/packages/Azure.Search.Documents/11.3.0-beta.2) | 
| SDK de Azure para Java |  [versión 11.4.0-beta.2](https://search.maven.org/artifact/com.azure/azure-search-documents/11.4.0-beta.2/jar) |
| SDK de Azure para JavaScript | [versión 11.2.0-beta.2](https://www.npmjs.com/package/@azure/search-documents/v/11.2.0-beta.2) |
| SDK de Azure para Python | [versión 11.2.0b3](https://pypi.org/project/azure-search-documents/11.2.0b3/) |

## <a name="spell-correction-with-simple-search"></a>Corrección ortográfica con búsqueda simple

En el ejemplo siguiente se usa el ejemplo hotels-sample-index integrado para mostrar la corrección ortográfica en una consulta de texto de formato libre. Sin la corrección ortográfica, la consulta devuelve cero resultados. Con la corrección, la consulta devuelve un resultado para el resort orientado a familias de Johnson.

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
{
    "search": "famly acitvites",
    "speller": "lexicon",
    "queryLanguage": "en-us",
    "queryType": "simple",
    "select": "HotelId,HotelName,Description,Category,Tags",
    "count": true
}
```

## <a name="spell-correction-with-full-lucene"></a>Corrección ortográfica con sintaxis de Lucene completa

La corrección ortográfica se produce en términos de consulta individuales que se someten a análisis de texto, por lo que puede usar el parámetro "speller" con algunas consultas de Lucene, pero no con otras.

+ Los formularios de consulta incompatibles que omiten el análisis de texto incluyen: caracteres comodín, expresiones regulares, coincidencias parciales.
+ Los formularios de consulta compatibles incluyen: búsqueda con campo, proximidad, priorización por término.

En este ejemplo se usa la búsqueda con campo en el campo Category, con la sintaxis de Lucene completa y un término de consulta mal escrito. Al incluir el corrector ortográfico, el error ortográfico en "Suiite" se corrige y la consulta se realiza correctamente.

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
{
    "search": "Category:(Resort and Spa) OR Category:Suiite",
    "queryType": "full",
    "speller": "lexicon",
    "queryLanguage": "en-us",
    "select": "Category",
    "count": true
}
```

## <a name="spell-correction-with-semantic-search"></a>Corrección ortográfica con búsqueda semántica

Esta consulta, con errores tipográficos en todos los términos excepto uno, se somete a correcciones ortográficas para devolver los resultados importantes. Para más información, consulte [Creación de consultas semánticas](semantic-how-to-query-request.md).

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview     
{
    "search": "hisotoric hotell wiht great restrant nad wiifi",
    "queryType": "semantic",
    "speller": "lexicon",
    "queryLanguage": "en-us",
    "searchFields": "HotelName,Tags,Description",
    "select": "HotelId,HotelName,Description,Category,Tags",
    "count": true
}
```

## <a name="supported-languages"></a>Idiomas compatibles

Los valores que se pueden usar en queryLanguage se pueden encontrar en la tabla siguiente, que se ha copiado de la lista de [idiomas admitidos (referencia de la API de REST)](/rest/api/searchservice/preview-api/search-documents#queryLanguage).

| Lenguaje | queryLanguage |
|----------|---------------|
| Inglés [EN] | EN, EN-US (valor predeterminado) |
| Español [ES] | ES, ES-ES (valor predeterminado)|
| Francés [FR] | FR, FR-FR (valor predeterminado) |
| Alemán [DE] | DE, DE-DE (valor predeterminado) |
| Neerlandés (Países Bajos) | NL, NL-BE, NL-NL (valor predeterminado) |

### <a name="querylanguage-considerations"></a>Consideraciones sobre queryLanguage

Tal como se indicó anteriormente, una solicitud de consulta solo puede tener un parámetro queryLanguage, pero ese parámetro lo comparten varias características, cada una de las cuales admite una cohorte diferente de idiomas. Si solo usa el corrector ortográfico, la lista de idiomas admitidos en la tabla anterior es la lista completa. 

### <a name="language-analyzer-considerations"></a>Consideraciones sobre el analizador de idiomas

Los índices con contenido que no está en inglés suelen usar [analizadores de idioma](index-add-language-analyzers.md) en campos que no están en inglés para aplicar las reglas lingüísticas del idioma nativo.

Si va a agregar un corrector ortográfico al contenido que también se somete a análisis de idioma, obtendrá mejores resultados si usa el mismo idioma en cada paso de la indexación y el procesamiento de consultas. Por ejemplo, si el contenido de un campo se indexó mediante el analizador de idioma "fr.microsoft", las consultas, la corrección ortográfica y las leyendas y respuestas semánticas deben usar una biblioteca o un lexicón de francés de algún tipo.

Para recapitular cómo se usan las bibliotecas de idioma en Cognitive Search:

+ Los analizadores de idioma se pueden invocar durante la indexación y la ejecución de consultas, y pueden ser de Apache Lucene (por ejemplo, "de.lucene") o de Microsoft ("de.microsoft").

+ Los léxicos de idioma invocados durante la corrección ortográfica se especifican mediante uno de los códigos de idioma de la tabla anterior.

En una solicitud de consulta, el valor asignado a queryLanguage se aplica por igual al corrector ortográfico, las [respuestas](semantic-answers.md) y las leyendas. 

> [!NOTE]
> La coherencia de idioma entre varios valores de propiedad solo es un problema si usa analizadores de idioma. Si usa analizadores independientes del idioma (como keyword, simple, standard, stop, whitespace o `standardasciifolding.lucene`), el valor de queryLanguage puede ser cualquiera que desee.

Aunque el contenido de un índice de búsqueda puede estar redactado en varios idiomas, es más probable que la entrada de la consulta lo esté en uno. El motor de búsqueda no comprueba la compatibilidad de queryLanguage, el analizador del lenguaje ni el idioma en que está redactado el contenido, por lo que debe asegurarse de establecer el ámbito de las consultas en consecuencia para evitar que se generen resultados incorrectos.

## <a name="next-steps"></a>Pasos siguientes

+ [Invocación de leyendas y clasificación semánticas](semantic-how-to-query-request.md)
+ [Creación de una consulta básica](search-query-create.md)
+ [Uso de la sintaxis de consulta completa de Lucene](query-Lucene-syntax.md)
+ [Uso de sintaxis de consulta simple](query-simple-syntax.md)
+ [Introducción a la búsqueda semántica](semantic-search-overview.md)
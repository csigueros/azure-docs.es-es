---
title: Adición de la corrección ortográfica
titleSuffix: Azure Cognitive Search
description: Asocie la corrección ortográfica a la canalización de consulta para corregir los errores tipográficos en los términos de consulta antes de ejecutar la consulta.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/27/2021
ms.custom: references_regions
ms.openlocfilehash: 3da9051a1f089d487be7021bf9341a95bae62b08
ms.sourcegitcommit: 6323442dbe8effb3cbfc76ffdd6db417eab0cef7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2021
ms.locfileid: "110614377"
---
# <a name="add-spell-check-to-queries-in-cognitive-search"></a>Adición de la corrección ortográfica a las consultas en Cognitive Search

> [!IMPORTANT]
> La corrección ortográfica está en versión preliminar pública en los [términos de uso complementarios](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Solo está disponible a través de Azure Portal y la API REST en versión preliminar.

Se pueden mejorar las coincidencias mediante la corrección ortográfica de los términos de consulta de búsqueda individuales antes de que lleguen al motor de búsqueda. El parámetro **speller** es compatible con todos los tipos de consulta: [simple](query-simple-syntax.md), [completa](query-lucene-syntax.md) y [semántica](semantic-how-to-query-request.md), la nueva opción, actualmente en versión preliminar pública.

## <a name="prerequisites"></a>Requisitos previos

El corrector ortográfico se lanzó conjuntamente con la [versión preliminar de la búsqueda semántica](semantic-search-overview.md). De por sí, es obligatorio [registrarse](https://aka.ms/SemanticSearchPreviewSignup), pero ello no supone ningún costo ni conlleva restricciones de nivel. El corrector ortográfico está disponible en las [mismas regiones](semantic-search-overview.md#availability-and-pricing) que la búsqueda semántica.

Una vez procesado el registro, necesitará lo siguiente:

+ Índice de búsqueda existente, con contenido en un [idioma admitido](#supported-languages). Actualmente, la corrección ortográfica no funciona con [sinónimos](search-synonyms.md). Evite su uso en índices que especifiquen una asignación de sinónimos en cualquier definición de campo.

+ Un cliente de búsqueda para el envío de consultas

  El cliente de búsqueda debe admitir las API REST en versión preliminar en la solicitud de consulta. Puede usar [Postman](search-get-started-rest.md), [Visual Studio Code](search-get-started-vs-code.md) o código que haya modificado para realizar llamadas de REST a las API en versión preliminar.

+ [Una solicitud de consulta](/rest/api/searchservice/preview-api/search-documents) que invoque la corrección ortográfica debe tener "api-version=2020-06-30-Preview", "speller=lexicon" y "queryLanguage" establecidos en un [idioma admitido](#supported-languages).

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

La corrección ortográfica se produce en términos de consulta individuales que se someten a análisis, por lo que puede usar el parámetro speller con algunas consultas de Lucene, pero no con otras.

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

En la tabla siguiente se pueden encontrar los valores válidos de la corrección ortográfica para queryLanguage. Esta lista es un subconjunto de los [idiomas admitidos (referencia de API REST)](/rest/api/searchservice/preview-api/search-documents#queryLanguage). Si usa leyendas y respuestas semánticas sin corrección ortográfica, puede elegir entre la lista más grande de idiomas y variantes.

| Lenguaje | queryLanguage |
|----------|---------------|
| Inglés [EN] | EN, EN-US (valor predeterminado) |
| Español [ES] | ES, ES-ES (valor predeterminado)|
| Francés [FR] | FR, FR-FR (valor predeterminado) |
| Alemán [DE] | DE, DE-DE (valor predeterminado) |

## <a name="language-considerations"></a>Consideraciones de idioma

El parámetro queryLanguage necesario para la corrección ortográfica debe ser coherente con los [analizadores de idioma](index-add-language-analyzers.md) asignados a las definiciones de campo en el esquema de índice. Por ejemplo, si el contenido de un campo se indexó mediante el analizador de idioma "fr.microsoft", las consultas, la corrección ortográfica y las leyendas y respuestas semánticas deben usar una biblioteca de idioma francés de algún tipo.

Para recapitular cómo se usan las bibliotecas de idioma en Cognitive Search:

+ Los analizadores de idioma se pueden invocar durante la indexación y la ejecución de consultas, y pueden ser de Lucene completo (por ejemplo, "de.lucene") o de Microsoft ("de.microsoft").

+ Los léxicos de idioma invocados durante la corrección ortográfica se especifican mediante uno de los códigos de idioma de la tabla anterior.

En una solicitud de consulta, el valor de queryLanguage se aplica por igual al corrector ortográfico, las [respuestas](semantic-answers.md) y las leyendas. No hay ninguna invalidación de las partes individuales de una respuesta semántica. 

> [!NOTE]
> La coherencia de idioma entre varios valores de propiedad solo es un problema si usa analizadores de idioma. Si usa analizadores independientes del idioma (como keyword, simple, standard, stop, whitespace o `standardasciifolding.lucene`), el valor de queryLanguage puede ser cualquiera que desee.

Aunque el contenido de un índice de búsqueda puede estar redactado en varios idiomas, es más probable que la entrada de la consulta lo esté en uno. El motor de búsqueda no comprueba la compatibilidad de queryLanguage, el analizador del lenguaje ni el idioma en que está redactado el contenido, por lo que debe asegurarse de establecer el ámbito de las consultas en consecuencia para evitar que se generen resultados incorrectos.

## <a name="next-steps"></a>Pasos siguientes

+ [Invocación de leyendas y clasificación semánticas](semantic-how-to-query-request.md)
+ [Creación de una consulta básica](search-query-create.md)
+ [Uso de la sintaxis de consulta completa de Lucene](query-Lucene-syntax.md)
+ [Uso de sintaxis de consulta simple](query-simple-syntax.md)
+ [Introducción a la búsqueda semántica](semantic-search-overview.md)
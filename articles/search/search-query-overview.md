---
title: Tipos de consulta
titleSuffix: Azure Cognitive Search
description: Obtenga información sobre los tipos de consultas que admite Cognitive Search, como texto libre, filtro, autocompletar y sugerencias, búsqueda geográfica, consultas del sistema y búsqueda de documentos.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: b9ca2a4ba836adacb81a82ad59a11c21ffd6c3fb
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129216885"
---
# <a name="querying-in-azure-cognitive-search"></a>Consultas de Azure Cognitive Search

Azure Cognitive Search ofrece un lenguaje de consulta enriquecido que admite una amplia gama de escenarios, desde la búsqueda de texto libre a patrones de consulta muy específicos. En este artículo se describen las solicitudes de consulta y los tipos de consultas que puede crear.

En Cognitive Search, una consulta es una especificación completa de una operación de ida y vuelta **`search`** , con parámetros que informan sobre la ejecución de la consulta y que dan forma a la respuesta devuelta. Los parámetros y los analizadores determinan el tipo de solicitud de consulta. El siguiente ejemplo es una consulta de texto libre con un operador booleano, que usa [Search Documents (API REST)](/rest/api/searchservice/search-documents) y que tiene como destino la colección de documentos [hotels-sample-index](search-get-started-portal.md).

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "queryType": "simple",
    "searchMode": "all",
    "search": "restaurant +view",
    "searchFields": "HotelName, Description, Address/City, Address/StateProvince, Tags",
    "select": "HotelName, Description, Address/City, Address/StateProvince, Tags",
    "top": "10",
    "count": "true",
    "orderby": "Rating desc"
}
```

Entre los parámetros que se han utilizado durante la ejecución de la consulta se incluyen:

+ **`queryType`** establece el analizador, que es el [analizador de consultas sencillas predeterminado](search-query-simple-examples.md) (óptimo para la búsqueda de texto completo) o el [analizador de consultas completas de Lucene](search-query-lucene-examples.md) usado en construcciones de consultas avanzadas como las expresiones regulares, la búsqueda de proximidad, la búsqueda aproximada y por caracteres comodín, por nombrar algunas.

+ **`searchMode`** especifica si las coincidencias se basan en todos los criterios ("all) o en cualquier criterio ("any") de la expresión. El valor predeterminado es any.

+ **`search`** proporciona los criterios de coincidencia, normalmente términos completos o frases, con o sin operadores. Cualquier campo que tenga el atributo *que permite búsquedas* en el esquema de índice es un candidato para este parámetro.

+ **`searchFields`** restringe la ejecución de consultas a campos utilizables en búsquedas. Durante el desarrollo, resulta útil usar la misma lista de campos para la selección y la búsqueda. En caso contrario, una coincidencia podría basarse en valores de campo que no se pueden ver en los resultados, lo que crea incertidumbre sobre el motivo por el que se devolvió el documento.

Parámetros que se usan para dar forma a la respuesta:

+ **`select`** especifica los campos que se van a devolver en la respuesta. Solo los campos marcados como *recuperable* en el índice se pueden usar en una instrucción select.

+ **`top`** devuelve el número especificado de documentos con la mejor coincidencia. En este ejemplo, solo se devuelven 10 resultados. Puede usar top y skip (no se muestra) para paginar los resultados.

+ **`count`** indica cuántos documentos en el índice completo coinciden globalmente, lo que puede ser mayor de lo que se devuelve. 

+ **`orderby`** se utiliza si desea ordenar los resultados por un valor, como una clasificación o una ubicación. De lo contrario, el valor predeterminado es usar la puntuación de relevancia para clasificar los resultados. Se le debe asignar el atributo *ordenable* a un campo para que pueda ser candidato para este parámetro.

La lista anterior es representativa pero no exhaustiva. Para obtener la lista completa de parámetros de una solicitud de consulta, consulte [Search Documents (API REST)](/rest/api/searchservice/search-documents).

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>Tipos de consultas

Con algunas excepciones importantes, una solicitud de consulta recorre en iteración los índices invertidos estructurados para conseguir unos exámenes rápidos, en los que se puede encontrar una coincidencia en cualquier campo, dentro de cualquier número de documentos de búsqueda. En Cognitive Search, la metodología principal para encontrar coincidencias es la búsqueda de texto completo o los filtros, pero también puede implementar otras experiencias de búsqueda conocidas como Autocomplete o la búsqueda de ubicación geográfica. En el resto de este artículo se resumen las consultas en Cognitive Search y se proporcionan vínculos que conducen a más información y ejemplos.

## <a name="full-text-search"></a>Búsqueda de texto completo

Si la aplicación de búsqueda incluye un cuadro de búsqueda que recopila entradas de términos, la búsqueda de texto completo es probablemente la operación de consulta que respalda esa experiencia. La búsqueda de texto completo acepta términos o frases que se pasan en un parámetro **`search`** en todos los campos *utilizables en búsquedas* del índice. Los operadores booleanos opcionales de la cadena de consulta pueden especificar criterios de inclusión o exclusión. Tanto el analizador simple como el analizador completo admiten la búsqueda de texto completo.

En Cognitive Search, la búsqueda de texto completo se basa en el motor de consultas de Apache Lucene. Las cadenas de consulta de la búsqueda de texto completo se someten a análisis léxico para mejorar la eficacia de los exámenes. El análisis incluye el uso de minúsculas en todos los términos, la eliminación de palabras irrelevantes como "el" y la reducción de los términos a sus formas raíz primitivas. El analizador predeterminado es Standard Lucene.

Cuando se encuentran términos coincidentes, el motor de consultas reconstituye un documento de búsqueda que contiene la coincidencia mediante la clave o el identificador del documento para ensamblar valores de campo, clasifica los documentos en orden de relevancia y devuelve los 50 resultados principales (de forma predeterminada) en la respuesta, o un número diferente si especificó **`top`** .

Si va a implementar una búsqueda de texto completo, comprender cómo se tokeniza el contenido le ayudará a depurar las anomalías de consulta. Las consultas a través de cadenas con guiones o caracteres especiales podrían requerir el uso de un analizador que no sea el estándar predeterminado de Lucene para asegurarse de que el índice contiene los tokens correctos. Puede invalidar el valor predeterminado con [analizadores de idiomas](index-add-language-analyzers.md#language-analyzer-list) o [especializados](index-add-custom-analyzers.md#built-in-analyzers) que modifiquen el análisis léxico. Un ejemplo es el analizador por [palabra clave](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html), que trata el contenido de un campo como token único. Esto es útil para los datos como códigos postales, identificadores y algunos nombres de producto. Para más información, consulte [Búsqueda de términos parciales y patrones con caracteres especiales](search-query-partial-matching.md).

Si prevé un uso intensivo de los operadores booleanos, lo cual es más probable en los índices que contienen bloques de texto de gran tamaño (un campo de contenido o descripciones largas), asegúrese de probar las consultas con el parámetro **`searchMode=Any|All`** para evaluar el impacto de esa configuración en la búsqueda booleana.

## <a name="autocomplete-and-suggested-queries"></a>Autocomplete y consultas sugeridas

[Autocomplete o los resultados sugeridos](search-add-autocomplete-suggestions.md) son alternativas a **`search`** que activan solicitudes de consulta sucesivas basadas en entradas de cadenas parciales (después de cada carácter) en una experiencia de búsqueda a medida que escribe. Puede usar los parámetros **`autocomplete`** y **`suggestions`** juntos o por separado, como se describe en [este tutorial](tutorial-csharp-type-ahead-and-suggestions.md), pero no puede usarlos con **`search`** . Los términos completados y las consultas sugeridas se derivan del contenido del índice. El motor nunca devolverá una cadena o sugerencia que no exista en el índice. Para más información, consulte [Autocomplete (API REST)](/rest/api/searchservice/autocomplete) y [Suggestions (API REST)](/rest/api/searchservice/suggestions).

## <a name="filter-search"></a>Filtrado de las búsquedas

Los filtros se usan ampliamente en aplicaciones que incluyen Cognitive Search. En las páginas de las aplicaciones, los filtros se suelen visualizar como facetas en las estructuras de navegación por vínculos para el filtrado dirigido por el usuario. Los filtros también se usan internamente para exponer segmentos de contenido indexado. Por ejemplo, puede inicializar una página de búsqueda mediante un filtro en una categoría de producto, o un idioma si el índice contiene campos en inglés y francés.

También podría necesitar filtros para invocar un formulario de consulta especializado, como se indica en la tabla siguiente. Puede utilizar un filtro con una búsqueda no especificada ( **`search=*`** ) o con una cadena de consulta que incluya términos, frases, operadores y patrones.

| Escenario de filtrado | Descripción |
|-----------------|-------------|
| Filtros de rango | En Azure Cognitive Search, las consultas de intervalo se compilan con el parámetro de filtro. Para más información y ejemplos, consulte [Ejemplo de filtro de intervalo](search-query-simple-examples.md#example-5-range-filters). |
| Búsqueda de ubicación geográfica | Si un campo utilizable en búsqueda es del [tipo Edm.GeographyPoint](/rest/api/searchservice/supported-data-types) puede crear una expresión de filtro para "buscar cerca de mí" o utilizar controles de búsqueda basados en mapas. Los campos que proporcionan búsquedas geográficas contienen coordenadas. Para más información y para poder ver un ejemplo, consulte [Ejemplo de búsqueda georreferenciada](search-query-simple-examples.md#example-6-geo-search). |
| Navegación por facetas | En el árbol de [navegación por facetas](search-faceted-navigation.md), los usuarios pueden seleccionar facetas y restringir los resultados en cada clic. Cada faceta tiene el respaldo de un filtro que excluye los documentos que ya no coinciden con los criterios. |

> [!NOTE]
> El texto que se usa en una expresión de filtro no se analiza durante el procesamiento de la consulta. Se supone que la entrada de texto es un patrón de caracteres textual que distingue entre mayúsculas y minúsculas, y que coincide o no. Las expresiones de filtro se construyen mediante la [sintaxis de OData](query-odata-filter-orderby-syntax.md) y se pasan en un parámetro **`filter`** en todos los campos *filtrables* del índice. Para más información, consulte [Filtros de Azure Cognitive Search](search-filters.md).

## <a name="document-look-up"></a>Búsqueda de documentos

A diferencia de los formularios de consulta descritos anteriormente, este recupera un solo [documento de búsqueda por identificador](/rest/api/searchservice/lookup-document), sin la búsqueda o examen del índice correspondiente. Solo se solicita y se devuelve un documento. Cuando un usuario selecciona un elemento de los resultados de la búsqueda, recuperar el documento y rellenar una página de detalles con campos es una respuesta típica, y una búsqueda de documento es la operación que lo hace posible.

## <a name="advanced-search-fuzzy-wildcard-proximity-regex"></a>Búsqueda avanzada: aproximada, con caracteres comodín, por proximidad, con expresiones regulares

Un formulario de consulta avanzado depende de los operadores y el analizador completos de Lucene que desencadenan un comportamiento de consulta específico.

| Tipo de consulta | Uso | Más información y ejemplos |
|------------|--------|------------------------------|
| [Búsqueda clasificada por campos](query-lucene-syntax.md#bkmk_fields) | parámetro **`search`** , **`queryType=full`**  | Compila una expresión de consulta compuesta con un único campo como destino. <br/>[Ejemplo de búsqueda clasificada por campos](search-query-lucene-examples.md#example-1-fielded-search) |
| [Búsqueda aproximada](query-lucene-syntax.md#bkmk_fuzzy) | parámetro **`search`** , **`queryType=full`** | Coincidencias en términos con construcción u ortografía similares. <br/>[Ejemplo de búsqueda aproximada](search-query-lucene-examples.md#example-2-fuzzy-search) |
| [Búsqueda por proximidad](query-lucene-syntax.md#bkmk_proximity) | parámetro **`search`** , **`queryType=full`** | Busca términos que están cerca uno del otro en un documento. <br/>[Ejemplo de búsqueda por proximidad](search-query-lucene-examples.md#example-3-proximity-search) |
| [Priorización de términos](query-lucene-syntax.md#bkmk_termboost) | parámetro **`search`** , **`queryType=full`** | Clasifica un documento superior si contiene el término prioritario con respecto a otros que no lo tienen. <br/>[Ejemplo de priorización de términos](search-query-lucene-examples.md#example-4-term-boosting) |
| [Búsqueda de expresiones regulares](query-lucene-syntax.md#bkmk_regex) | parámetro **`search`** , **`queryType=full`** | Coincidencias basadas en el contenido de una expresión regular. <br/>[Ejemplo de expresión regular](search-query-lucene-examples.md#example-5-regex) |
|  [búsqueda de prefijo o de carácter comodín](query-lucene-syntax.md#bkmk_wildcard) | parámetro **`search`** con **_`~`_* o **`?`** , **`queryType=full`**| Coincidencias según un prefijo y una virgulilla (`~`) o carácter individual (`?`). <br/>[Ejemplo de búsqueda con caracteres comodín](search-query-lucene-examples.md#example-6-wildcard-search) |

## <a name="next-steps"></a>Pasos siguientes

Para obtener una visión más detallada de la implementación de consultas, revise los ejemplos de cada sintaxis. Si no está familiarizado con la búsqueda de texto completo, echar un vistazo más detallado a lo que el motor de consultas hace puede ser una opción igualmente buena.

+ [Ejemplos de consulta simple](search-query-simple-examples.md)
+ [Ejemplos de consulta con sintaxis de Lucene para la creación de consultas avanzadas](search-query-lucene-examples.md)
+ [Funcionamiento de la búsqueda de texto completo en Azure Cognitive Search](search-lucene-query-architecture.md)
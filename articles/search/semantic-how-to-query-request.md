---
title: Creación de consultas semánticas
titleSuffix: Azure Cognitive Search
description: Establezca un tipo de consulta semántica para asociar los modelos de aprendizaje profundo al procesamiento de consultas, y así inferir la intención y el contexto dentro de la clasificación y la relevancia de la búsqueda.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/21/2021
ms.openlocfilehash: e3ae63b202d826e48789bd8d15a197048d5566b7
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122178333"
---
# <a name="create-a-query-that-invokes-semantic-ranking-and-returns-semantic-captions"></a>Crear una consulta que invoque la clasificación semántica y devuelva títulos semánticos

> [!IMPORTANT]
> La búsqueda semántica está en versión preliminar pública en los [términos de uso complementarios](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Está disponible a través de Azure Portal, la API REST en versión preliminar y los SDK en versión beta. Estas características son facturables. Para más información, consulte [Disponibilidad y precios](semantic-search-overview.md#availability-and-pricing).

La búsqueda semántica es una característica principal de Azure Cognitive Search que invoca un algoritmo de clasificación semántica sobre un conjunto de resultados y devuelve títulos semánticos (y, opcionalmente, [respuestas semánticas](semantic-answers.md)), con resaltados sobre los términos y frases más importantes. Los títulos y las respuestas se devuelven en las solicitudes de consultas formuladas con el tipo de consulta "semantic".

Las leyendas y las respuestas se extraen literalmente del texto del documento de búsqueda. El subsistema semántico determina qué parte del contenido tiene las características de una leyenda o respuesta, pero no crea oraciones ni frases. Por esta razón, el contenido que incluye explicaciones o definiciones funciona mejor para la búsqueda semántica.

## <a name="prerequisites"></a>Prerrequisitos

+ Un servicio Cognitive Search en un nivel Estándar (S1, S2, S3), ubicado en una de estas regiones: Centro-norte de EE. UU., Oeste de EE. UU., Oeste de EE. UU. 2, Este de EE. UU. 2, Europa del Norte y Oeste de Europa. Si tiene un servicio S1 o superior en una de estas regiones, puede suscribirse a la versión preliminar sin tener que crear un servicio.

+ [Suscríbase a la versión preliminar](https://aka.ms/SemanticSearchPreviewSignup). El plazo de respuesta esperado es de aproximadamente dos días laborables.

+ Índice de búsqueda existente, con contenido en un [idioma admitido](/rest/api/searchservice/preview-api/search-documents#queryLanguage). La búsqueda semántica funciona mejor en contenido informativo o descriptivo.

+ Un cliente de búsqueda para el envío de consultas.

  El cliente de búsqueda debe admitir las API REST en versión preliminar en la solicitud de consulta. Puede usar [Postman](search-get-started-rest.md), [Visual Studio Code](search-get-started-vs-code.md) o código que realice llamadas REST a las API en versión preliminar. También puede usar el [Explorador de búsqueda](search-explorer.md) de Azure Portal para enviar consultas semánticas. También puede usar [Azure.Search.Documents 11.3.0-beta.2](https://www.nuget.org/packages/Azure.Search.Documents/11.3.0-beta.2).

+ Una [solicitud de consulta](/rest/api/searchservice/preview-api/search-documents) debe incluir `queryType=semantic` y otros parámetros descritos en este artículo.

## <a name="whats-a-semantic-query-type"></a>¿Qué es una consulta de tipo semántico?

En Cognitive Search, una consulta es una solicitud parametrizada que determina el procesamiento de las consultas y la forma de las respuestas. Una *consulta semántica* [tiene parámetros](#query-using-rest) que invocan el modelo de reclasificación semántica. Este algoritmo puede evaluar el contexto y el significado de los resultados coincidentes, promover los mejores a los primeros puestos, y devolver respuestas semánticas y subtítulos.

La solicitud siguiente es representativa de una consulta semántica mínima (sin respuestas).

```http
POST https://[service name].search.windows.net/indexes/[index name]/docs/search?api-version=2020-06-30-Preview      
{    
    "search": " Where was Alan Turing born?",    
    "queryType": "semantic",  
    "searchFields": "title,url,body",  
    "queryLanguage": "en-us"  
}
```

Como sucede con todas las consultas de Cognitive Search, la solicitud va dirigida a la colección de documentos de un índice único. Además, las consultas semánticas pasan por la misma secuencia de análisis, examen y puntuación que una consulta no semántica. 

La diferencia radica en la relevancia y la puntuación. Tal como se define en esta versión preliminar, una consulta semántica es aquella cuyos *resultados* se vuelven a clasificar mediante un modelo de lenguaje semántico, lo que proporciona una manera de mostrar las coincidencias que el clasificador semántico considera de más importancia, en lugar de las puntuaciones que asigna el algoritmo predeterminado de clasificación de similitudes.

Solo las 50 primeras coincidencias de los resultados iniciales se pueden clasificar semánticamente y todas incluyen títulos en la respuesta. Opcionalmente, puede especificar un parámetro **`answer`** en la solicitud para extraer una posible respuesta. Para obtener más información, vea [Respuestas semánticas](semantic-answers.md).

## <a name="query-in-azure-portal"></a>Consulta en Azure Portal

El [Explorador de búsqueda](search-explorer.md) se ha actualizado para incluir las opciones de las consultas semánticas. Estas opciones se vuelven visibles en el portal después de completar los pasos siguientes:

1. Abra el portal con esta sintaxis: `https://portal.azure.com/?feature.semanticSearch=true`, en un servicio de búsqueda para el que está habilitada la versión preliminar.

1. Haga clic en **Explorador de búsqueda** en la parte superior de la página de información general.

1. Elija un índice que tenga contenido en un [idioma admitido](/rest/api/searchservice/preview-api/search-documents#queryLanguage).

1. En el Explorador de búsqueda, establezca las opciones de consulta que habilitan las consultas semánticas, searchFields y la corrección ortográfica. También puede pegar los parámetros de consulta necesarios en la cadena de consulta.

:::image type="content" source="./media/semantic-search-overview/search-explorer-semantic-query-options.png" alt-text="Opciones de consulta en el Explorador de búsqueda" border="true":::

## <a name="query-using-rest"></a>Consulta mediante REST

Use [Buscar en documentos (versión preliminar REST)](/rest/api/searchservice/preview-api/search-documents) para formular la solicitud mediante programación. Una respuesta incluye subtítulos y resaltado de forma automática. Si desea corrección ortográfica o respuestas, agregue **`speller`** o **`answers`** .

En el ejemplo siguiente se usa [hotels-sample-index](search-get-started-portal.md) para crear una solicitud de consulta semántica con corrección ortográfica, respuestas semánticas y títulos:

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview      
{
    "search": "newer hotel near the water with a great restaurant",
    "queryType": "semantic",
    "queryLanguage": "en-us",
    "searchFields": "HotelName,Category,Description",
    "speller": "lexicon",
    "answers": "extractive|count-3",
    "highlightPreTag": "<strong>",
    "highlightPostTag": "</strong>",
    "select": "HotelId,HotelName,Description,Category",
    "count": true
}
```

En la tabla siguiente se resumen los parámetros que se utilizan en una consulta semántica. Para obtener una lista de los parámetros de una solicitud, consulte [Buscar en documentos (versión preliminar REST)](/rest/api/searchservice/preview-api/search-documents).

| Parámetro | Tipo | Descripción |
|-----------|-------|-------------|
| queryType | String | Los valores válidos son simple, full y semantic. Para las consultas semánticas, se requiere un valor de "semantic". |
| queryLanguage | String | Necesario para las consultas semánticas. El léxico que especifique se aplica igualmente a la clasificación semántica, los títulos, las respuestas y la revisión ortográfica. Para más información, consulte [Idiomas admitidos (referencia de API REST)](/rest/api/searchservice/preview-api/search-documents#queryLanguage). |
| searchFields | String | Una lista delimitada por comas de campos que permiten búsqueda. Especifica los campos en los que se produce la clasificación semántica, de los que se extraen las leyendas y las respuestas. </br></br>A diferencia de los tipos de consulta simple y completa, el orden en que se muestran los campos determina la precedencia. Para ver más instrucciones de uso, vea [Paso 2: Establecer searchFields](#searchfields). |
| Corrector ortográfico | String | Parámetro opcional, no específico de las consultas semánticas, que corrige términos mal escritos antes de que lleguen al motor de búsqueda. Para obtener más información, vea [Agregar corrección ortográfica a las consultas](speller-how-to-add.md). |
| answers |String | Parámetros opcionales que especifican si el resultado incluye las respuestas semánticas. Actualmente, solo está implementado "extractive". Las respuestas se pueden configurar para que se devuelvan diez como máximo. El valor predeterminado es uno. En este ejemplo se muestra un recuento de tres respuestas: `extractive\|count-3`. Para obtener más información, consulte [Devolución de respuestas semánticas](semantic-answers.md).|

### <a name="formulate-the-request"></a>Formulación de la solicitud

En esta sección se indican los pasos para la formulación de consultas.

#### <a name="step-1-set-querytype-and-querylanguage"></a>Paso 1: Establecer queryType y queryLanguage

Agregue los siguientes parámetros al resto. Ambos parámetros son obligatorios.

```json
"queryType": "semantic",
"queryLanguage": "en-us",
```

El parámetro queryLanguage debe estar en un [idioma admitido](/rest/api/searchservice/preview-api/search-documents#queryLanguage) y ser coherente con los [analizadores del idioma](index-add-language-analyzers.md) asignados a las definiciones de campo en el esquema de índice. Por ejemplo, ha indexado cadenas en francés mediante un analizador de idioma francés (como "fr.microsoft" o "fr.lucene") y, por tanto, queryLanguage también debe ser una variante del idioma francés.

En una solicitud de consulta, si usa también la [corrección ortográfica](speller-how-to-add.md), el valor de queryLanguage que establezca se aplica por igual al corrector ortográfico, a las respuestas y a los títulos. No hay invalidaciones para partes individuales. La corrección ortográfica admite [menos idiomas](speller-how-to-add.md#supported-languages), por lo que si va a usar esa característica, deberá establecer queryLanguage en uno de los idiomas de la lista.

Aunque el contenido de un índice de búsqueda puede estar redactado en varios idiomas, es más probable que la entrada de la consulta lo esté en uno. El motor de búsqueda no comprueba la compatibilidad de queryLanguage, el analizador del lenguaje ni el idioma en que está redactado el contenido, por lo que debe asegurarse de establecer el ámbito de las consultas en consecuencia para evitar que se generen resultados incorrectos.

<a name="searchfields"></a>

#### <a name="step-2-set-searchfields"></a>Paso 2: Establecer searchFields

Agregue searchFields a la solicitud. Esto es opcional, pero muy recomendable.

```json
"searchFields": "HotelName,Category,Description",
```

El parámetro searchFields se usa para identificar los pasajes que se van a evaluar para determinar su "similitud semántica" con la consulta. En el caso de la versión preliminar, no se recomienda dejar searchFields en blanco, ya que el modelo requiere una sugerencia sobre qué campos son los más importantes para procesar.

A diferencia de otros parámetros, searchFields no es nuevo. Es posible que ya esté usando searchFields en el código existente para consultas de Lucene simples o completas. Si es así, vuelva a consultar cómo se usa el parámetro para que pueda comprobar el orden de los campos al cambiar a un tipo de consulta semántica.

##### <a name="allowed-data-types"></a>Tipos de datos admitidos

Al establecer searchFields, elija solo los campos de los siguientes [tipos de datos admitidos](/rest/api/searchservice/supported-data-types). Si se incluye un campo no válido, no se produce ningún error, pero esos campos no se usarán en la clasificación semántica.

| Tipo de datos | Ejemplo de hotels-sample-index |
|-----------|----------------------------------|
| Edm.String | HotelName, Category, Description |
| Edm.ComplexType | Address.StreetNumber, Address.City, Address.StateProvince, Address.PostalCode |
| Collection(Edm.String) | Etiquetas (una lista de cadenas delimitada por comas) |

##### <a name="order-of-fields-in-searchfields"></a>Orden de los campos en searchFields

El orden de los campos es fundamental porque el clasificador semántico limita la cantidad de contenido que puede procesar para seguir proporcionando un tiempo de respuesta razonable. Es más probable que se incluya el contenido de los campos al principio de la lista. El contenido del final podría truncarse si se alcanza el límite máximo. Para más información, consulte [Procesamiento previo durante la clasificación semántica](semantic-ranking.md#pre-processing).

+ Si solo va a especificar un campo, seleccione un campo descriptivo en el que se pueda encontrar la respuesta a las consultas semánticas, como el contenido principal de un documento. 

+ En el caso de dos o más campos de searchFields:

  + El primer campo siempre debe ser conciso (por ejemplo, un título o un nombre). Lo ideal es una cadena que tenga menos de 25 palabras.

  + Si el índice tiene un campo de dirección URL que es legible por el usuario, como `www.domain.com/name-of-the-document-and-other-details` (en lugar de centrado en la máquina, como `www.domain.com/?id=23463&param=eis`), colóquelo en segundo lugar en la lista (o en primer lugar si no hay ningún campo de título conciso).

  + Agregue otros campos descriptivos a los campos anteriores en los que se pueda encontrar la respuesta a las consultas semánticas, como el contenido principal de un documento.

#### <a name="step-3-remove-or-bracket-query-features-that-bypass-relevance-scoring"></a>Paso 3: Quitar o poner entre corchetes las funciones de consulta que omiten la puntuación por relevancia

Varias funcionalidades de consulta de Cognitive Search no se someten a la puntuación por relevancia y otras omiten por completo el motor de búsqueda de texto completo. Si la lógica de consulta incluye las siguientes funciones, los resultados no incluirán puntuaciones de relevancia ni clasificación semántica:

+ Los filtros, las consultas de búsqueda aproximada y las expresiones regulares iteran sobre el texto sin tokenizar y buscan coincidencias textuales en el contenido. Las puntuaciones de búsqueda de todos los formularios de consulta anteriores tienen un valor uniforme de 1.0 y no proporcionan información significativa para la clasificación semántica.

+ La ordenación (cláusulas orderBy) en campos específicos también invalidará las puntuaciones de búsqueda y la puntuación semántica. Dado que la puntuación semántica se usa para ordenar los resultados, incluir una lógica de ordenación explícita provocará un error HTTP 400.

#### <a name="step-4-add-answers"></a>Paso 4: Agregar respuestas

De manera opcional, agregue "respuestas" si quiere incluir un procesamiento adicional que proporcione una respuesta. Para más información sobre este parámetro, consulte [Especificación de respuestas semánticas](semantic-answers.md).

```json
"answers": "extractive|count-3",
```

Las respuestas (y los subtítulos) se extraen a partir de los pasajes que se encuentran en los campos enumerados en searchFields. Este es el motivo por el que desea incluir campos enriquecidos con contenido en searchFields, para que pueda obtener los mejores resultados en una respuesta. No se garantizan respuestas en cada solicitud. La consulta debe parecer una pregunta y el contenido debe incluir texto que parezca una respuesta.

#### <a name="step-5-add-other-parameters"></a>Paso 5: Agregar otros parámetros

Especifique cualquier otro parámetro que quiera incluir en la solicitud. Parámetros, como [speller](speller-how-to-add.md), [select](search-query-odata-select.md) y count mejoran la calidad de la solicitud y la legibilidad de la respuesta.

```json
"speller": "lexicon",
"select": "HotelId,HotelName,Description,Category",
"count": true,
"highlightPreTag": "<mark>",
"highlightPostTag": "</mark>",
```

El estilo de resaltado se aplica a los títulos de la respuesta. Puede usar el estilo predeterminado u, opcionalmente, personalizar el estilo de resaltado aplicado a los títulos. Los subtítulos aplican el formato de resaltado sobre los pasajes principales del documento que resumen la respuesta. El valor predeterminado es `<em>`. Si quiere especificar el tipo de formato (por ejemplo, fondo amarillo), puede establecer los parámetros highlightPreTag y highlightPostTag.

## <a name="query-using-azure-sdks"></a>Consulta mediante los SDK de Azure

Las versiones beta de los SDK de Azure incluyen compatibilidad con la búsqueda semántica. Dado que los SDK son versiones beta, no hay documentación ni ejemplos, pero puede consultar la sección anterior de API de REST para obtener información sobre cómo deben funcionar las API.

| SDK de Azure | Paquete |
|-----------|---------|
| .NET | [Azure.Search.Documents package 11.3.0-beta.2](https://www.nuget.org/packages/Azure.Search.Documents/11.3.0-beta.2)  |
| Java | [com.azure:azure-search-documents 11.4.0-beta.2](https://search.maven.org/artifact/com.azure/azure-search-documents/11.4.0-beta.2/jar)  |
| JavaScript | [azure/search-documents 11.2.0-beta.2](https://www.npmjs.com/package/@azure/search-documents/v/11.2.0-beta.2)|
| Python | [azure-search-documents 11.2.0b3](https://pypi.org/project/azure-search-documents/11.2.0b3/) |

## <a name="evaluate-the-response"></a>Evaluación de la respuesta

Al igual que con todas las consultas, una respuesta consta de todos los campos marcados como recuperables, o solo de los campos enumerados en el parámetro SELECT. Incluye la puntuación de relevancia original y también puede incluir un recuento o resultados por lotes, en función de cómo haya formulado la solicitud.

En una consulta semántica, la respuesta tiene elementos adicionales: una nueva puntuación de relevancia clasificada semánticamente, subtítulos en texto sin formato y con resaltados y, opcionalmente, una respuesta.

En una aplicación cliente puede estructurar la página de búsqueda para incluir un subtítulo como la descripción de la coincidencia, en lugar de todo el contenido de un campo específico. Esto resulta útil cuando los campos individuales son demasiado densos para la página resultados de la búsqueda.

La respuesta de la consulta de ejemplo anterior devuelve la siguiente coincidencia como elección principal. Los subtítulos se devuelven automáticamente, con texto sin formato y versiones resaltadas. Las respuestas se omiten en el ejemplo porque no se pudo determinar ninguna para esta consulta y corpus en particular.

```json
"@odata.count": 35,
"@search.answers": [],
"value": [
    {
        "@search.score": 1.8810667,
        "@search.rerankerScore": 1.1446577133610845,
        "@search.captions": [
            {
                "text": "Oceanside Resort. Luxury. New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
                "highlights": "<strong>Oceanside Resort.</strong> Luxury. New Luxury Hotel. Be the first to stay.<strong> Bay</strong> views from every room, location near the pier, rooftop pool, waterfront dining & more."
            }
        ],
        "HotelName": "Oceanside Resort",
        "Description": "New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
        "Category": "Luxury"
    },
```

## <a name="next-steps"></a>Pasos siguientes

Recuerde que la clasificación y las respuestas semánticas se crean sobre un conjunto de resultados inicial. Cualquier lógica que mejore la calidad de los resultados iniciales hará avanzar la búsqueda semántica. El paso siguiente sería revisar las características que contribuyen a los resultados iniciales, como los analizadores que afectan al modo en que se acortan las cadenas, los perfiles de puntuación que pueden optimizar los resultados y el algoritmo de relevancia predeterminado.

+ [Analizadores para el procesamiento de texto](search-analyzers.md)
+ [Algoritmo de clasificación de similitud](index-similarity-and-scoring.md)
+ [Perfiles de puntuación](index-add-scoring-profiles.md)
+ [Introducción a la búsqueda semántica](semantic-search-overview.md)
+ [Algoritmo de clasificación semántica](semantic-ranking.md)

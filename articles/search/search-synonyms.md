---
title: Sinónimos para la expansión de consultas a través de un índice de búsqueda
titleSuffix: Azure Cognitive Search
description: Creación de un mapa de sinónimos para expandir el ámbito de una consulta de búsqueda en un índice de Azure Cognitive Search. El ámbito se ha ampliado para que incluya los términos equivalentes que proporcione en una lista.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/11/2021
ms.openlocfilehash: ea92a5e196c809535801278631cbfdfdc5013199
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112288224"
---
# <a name="synonyms-in-azure-cognitive-search"></a>Sinónimos de Azure Cognitive Search

Dentro de un servicio de búsqueda, los mapas de sinónimos son un recurso global que asocia términos equivalentes, lo que amplía el ámbito de una consulta sin que el usuario tenga que proporcionar realmente el término. Por ejemplo, suponiendo que "perro", "can" y "cachorro" son sinónimos de un mismo mapa, una consulta sobre "can" coincidirá con un documento que contenga "perro".

## <a name="create-synonyms"></a>Creación de sinónimos

Un mapa de sinónimos es un recurso que se puede crear una vez y usar en muchos índices. El [nivel de servicio](search-limits-quotas-capacity.md#synonym-limits) determina el número de mapas de sinónimos que se pueden crear, que oscilan entre tres para los niveles Gratuito y Básico, y hasta 20 para los niveles Estándar. 

Puede crear varios mapas de sinónimos para distintos idiomas, como versiones en inglés y francés, o bien léxicos si el contenido incluye terminología técnica o compleja. Aunque se pueden crear varios mapas de sinónimos en el servicio de búsqueda, un campo solo puede usar uno.

Un mapa de sinónimos está formado por el nombre, el formato y las reglas que funcionan como entradas del mapa de sinónimos. El único formato que se admite es `solr` y el formato `solr` determina la construcción de reglas.

```http
POST /synonymmaps?api-version=2020-06-30
{
    "name": "geo-synonyms",
    "format": "solr",
    "synonyms": "
        USA, United States, United States of America\n
        Washington, Wash., WA => WA\n"
}
```

Para crear un mapa de sinónimos, hágalo mediante programación (el portal no admite definiciones de mapas de sinónimos):

+ [Creación de un mapa de sinónimos (API de REST)](/rest/api/searchservice/create-synonym-map) Esta referencia es la más descriptiva.
+ [Clase SynonymMap (.NET)](/dotnet/api/azure.search.documents.indexes.models.synonymmap) y [Agregar sinónimos mediante C#](search-synonyms-tutorial-sdk.md)
+ [Clase SynonymMap (Python)](/python/api/azure-search-documents/azure.search.documents.indexes.models.synonymmap)
+ [Interfaz SynonymMap (JavaScript)](/javascript/api/@azure/search-documents/synonymmap)
+ [Clase SynonymMap (Java)](/java/api/com.azure.search.documents.indexes.models.synonymmap)

## <a name="define-rules"></a>Definición de reglas

Las reglas de asignación se adhieren a la especificación del filtro de sinónimos de código abierto de Apache Solr descrita en este documento: [SynonymFilter](https://cwiki.apache.org/confluence/display/solr/Filter+Descriptions#FilterDescriptions-SynonymFilter). El formato `solr` admite dos tipos de reglas:

+ equivalencia (donde los términos son sustitutos iguales en la consulta)

+ asignaciones explícitas (donde los términos se asignan a un término explícito antes de la consulta)

Cada regla debe estar delimitada por el carácter de nueva línea (`\n`). Puede definir hasta 5000 reglas por mapa de sinónimos en un servicio gratuito y 20 000 reglas por mapa en otros niveles. Cada regla puede tener hasta 20 expansiones (o elementos). Para obtener más información, vea [Límites de sinónimos](search-limits-quotas-capacity.md#synonym-limits).

Los analizadores de consultas convertirán en minúsculas todos los términos en mayúsculas o con mayúsculas y minúsculas mezcladas, pero si quiere conservar los caracteres especiales en la cadena, como una coma o un guion, agregue los caracteres de escape adecuados al crear el mapa de sinónimos.

### <a name="equivalency-rules"></a>Reglas de equivalencia

Las reglas para términos equivalentes se delimitan mediante comas dentro de la misma regla. En el primer ejemplo, una consulta sobre `USA` se expandirá a `USA` o `"United States"`, o bien a `"United States of America"`. Tenga en cuenta que si quiere buscar coincidencias con una frase, la propia consulta debe ser una consulta de frases entre comillas.

En el caso de la equivalencia, una consulta para `dog` expandirá la consulta para incluir también `puppy` y `canine`.

```json
{
"format": "solr",
"synonyms": "
    USA, United States, United States of America\n
    dog, puppy, canine\n
    coffee, latte, cup of joe, java\n"
}
```

### <a name="explicit-mapping"></a>Asignación explícita

Las reglas para una asignación explícita se indican mediante una flecha `=>`. Cuando se especifica, una secuencia de términos de una consulta de búsqueda que coincide con el lado izquierdo de `=>` se sustituirá por las alternativas del lado derecho en el momento de la consulta.

En el caso explícito, una consulta para `Washington`, `Wash.` o `WA` se volverá a escribir como `WA`, y el motor de consultas solo buscará coincidencias en el término `WA`. La asignación explícita solo se aplica en la dirección especificada y, en este caso, no reescribe la consulta `WA` como `Washington`.

```json
{
"format": "solr",
"synonyms": "
    Washington, Wash., WA => WA\n
    California, Calif., CA => CA\n"
}
```

### <a name="escaping-special-characters"></a>Escape de caracteres especiales

En la búsqueda de texto completo, los sinónimos se analizan durante el procesamiento de consultas como cualquier otro término de consulta, lo que significa que las reglas en torno a los caracteres reservados y especiales se aplican a los términos del mapa de sinónimos. La lista de caracteres que requiere escape varía entre la sintaxis simple y la sintaxis completa:

+ [sintaxis simple](query-simple-syntax.md)  `+ | " ( ) ' \`
+ [sintaxis completa](query-lucene-syntax.md)  `+ - & | ! ( ) { } [ ] ^ " ~ * ? : \ /`

Recuerde que si necesita conservar los caracteres que de otro modo el analizador predeterminado descartaría durante la indexación, debe sustituir un analizador que los conserve. Algunas opciones incluyen [analizadores de lenguaje](index-add-language-analyzers.md) natural de Microsoft, que conserva palabras con guiones o un analizador personalizado para patrones más complejos. Para más información, vea [Términos parciales, patrones y caracteres especiales](search-query-partial-matching.md).

En el siguiente ejemplo se muestra cómo agregar a un carácter una barra diagonal inversa como carácter de escape:

```json
{
"format": "solr",
"synonyms": "WA\, USA, WA, Washington\n"
}
```

Como la barra diagonal inversa es en sí misma un carácter especial en otros lenguajes como JSON y C#, es probable que tenga que hacer doble escape. Por ejemplo, el JSON enviado la API de REST para la asignación de sinónimos anterior tendría el siguiente aspecto:

```json
{
"format":"solr",
"synonyms": "WA\\, USA, WA, Washington"
}
```

## <a name="upload-and-manage-synonym-maps"></a>Carga y administración de mapas de sinónimos

Como se ha mencionado antes, puede crear o actualizar un mapa de sinónimos sin interrumpir las cargas de trabajo de consulta e indexación. Un mapa de sinónimos es un objeto independiente (como los índices o los orígenes de datos) y mientras no se use en ningún campo, las actualizaciones no provocarán errores de indexación ni de consultas. Pero una vez que se agrega un mapa de sinónimos a una definición de campo, si después se elimina el mapa, se producirá un error 404 en cualquier consulta que incluya los campos en cuestión.

La creación, actualización y eliminación de un mapa de sinónimos es siempre una operación para todo el documento, lo que significa que no puede actualizar ni eliminar partes del mapa de sinónimos de forma incremental. Incluso para actualizar una sola regla es necesario volver a cargar.

## <a name="assign-synonyms-to-fields"></a>Asignación de sinónimos a campos

Después de cargar un mapa de sinónimos, puede habilitar los sinónimos en los campos del tipo `Edm.String` o `Collection(Edm.String)`, en campos que tengan `"searchable":true`. Como se ha indicado, una definición de campo solo puede usar un mapa de sinónimos.

```http
POST /indexes?api-version=2020-06-30
{
    "name":"hotels-sample-index",
    "fields":[
        {
            "name":"description",
            "type":"Edm.String",
            "searchable":true,
            "synonymMaps":[
            "en-synonyms"
            ]
        },
        {
            "name":"description_fr",
            "type":"Edm.String",
            "searchable":true,
            "analyzer":"fr.microsoft",
            "synonymMaps":[
            "fr-synonyms"
            ]
        }
    ]
}
```

## <a name="query-on-equivalent-or-mapped-fields"></a>Consulta en campos equivalentes o asignados

La adición de sinónimos no impone nuevos requisitos en la construcción de consultas. Puede emitir consultas de términos y frases como ha hecho antes de agregar sinónimos. La única diferencia es que si existe un término de consulta en el mapa de sinónimos, el motor de consultas expandirá o volverá a escribir el término o la frase, en función de la regla.

## <a name="how-synonyms-are-used-during-query-execution"></a>Cómo se usan los sinónimos durante la ejecución de la consulta

Los sinónimos son una técnica de expansión de consultas que complementa el contenido de un índice con términos equivalentes, aunque solo para los campos que tienen una asignación de sinónimo. Si una consulta de ámbito de campo *excluye* un campo habilitado para sinónimos, no se mostrarán coincidencias de la asignación de sinónimos.

En el caso de los campos habilitados para sinónimos, estos sinónimos están sujetos al mismo análisis de texto que el campo asociado. Por ejemplo, si un campo se analiza con el analizador de Lucene estándar, los términos de sinónimo también estarán sujetos al analizador de Lucene estándar en el momento de la consulta. Si quiere conservar los signos de puntuación, como los puntos o los guiones en el término sinónimo, aplique en el campo un analizador que conserve el contenido.

De manera interna, la característica Sinónimos reescribe la consulta original con sinónimos mediante el operador OR. Por este motivo, el resaltado de referencias y los perfiles de puntuación tratan el término original y los sinónimos como equivalentes.

Los sinónimos solo se aplican a las consultas de texto libre y no se admiten para los filtros, las facetas, la función autocompletar o las sugerencias. Autocompletar y las sugerencias se basan solo en el término original; las coincidencias de sinónimos no aparecen en la respuesta.

Las expansiones de sinónimos no se aplican a los términos de búsqueda de carácter comodín; los prefijos, las coincidencias parciales y las regex no se expanden.

Si tiene que realizar una consulta única que aplique la expansión de sinónimos y búsquedas aproximadas, de expresiones regulares y con comodines, puede combinar las consultas utilizando la sintaxis OR. Por ejemplo, para combinar sinónimos con caracteres comodín en la sintaxis de consulta única, el término sería `<query> | <query>*`.

Si dispone de un índice existente en un entorno de desarrollo (no producción), experimente con un diccionario pequeño para ver cómo la adición de sinónimos cambia la experiencia de búsqueda, incluida la repercusión en los perfiles de puntuación, el resaltado de referencias y las sugerencias.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Creación de un mapa de sinónimos (API REST)](/rest/api/searchservice/create-synonym-map)
---
title: Normalización de texto para filtros, facetas y organización
titleSuffix: Azure Cognitive Search
description: Especifique normalizadores para campos de texto en un índice a fin de personalizar el comportamiento de las coincidencia estrictas de palabras clave en el filtrado, aplicación de facetas y ordenación.
author: IshanSrivastava
manager: jlembicz
ms.author: ishansri
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/23/2021
ms.openlocfilehash: c050023c9cb859f3100f7e2165ba3a069ff4e372
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124784554"
---
# <a name="text-normalization-for-case-insensitive-filtering-faceting-and-sorting"></a>Normalización de texto para filtrado, aplicación de facetas y ordenación sin distinción de mayúsculas y minúsculas

> [!IMPORTANT] 
> Esta característica se encuentra en versión preliminar pública en los [Términos de uso complementarios](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). La [API REST de versión preliminar](/rest/api/searchservice/index-preview) admite esta característica.

Para buscar y recuperar documentos de un índice de Azure Cognitive Search, es necesario que la consulta coincida con el contenido del documento. El contenido se puede analizar a fin de generar tokens para buscar coincidencias, como cuando se usa el parámetro `search`, o se puede usar tal cual para buscar coincidencias estrictas de palabras clave, como con `$filter`, `facets` y `$orderby`. Este enfoque "todo o nada" abarca la mayoría de los escenarios, pero es insuficiente donde se requiere un procesamiento previo sencillo, como con el uso de mayúsculas y minúsculas, la eliminación de acentos, el plegado de ASCII, entre otros, sin tener que pasar por toda la cadena de análisis.

Considere los siguientes ejemplos:

+ `$filter=City eq 'Las Vegas'` solo devolverá documentos que contengan el texto exacto "Las Vegas" y excluirá los documentos con "LAS VEGAS" y "las vegas". Esto es deficiente cuando el caso de uso requiere todos los documentos, independientemente del uso de mayúsculas y minúsculas.

+ `search=*&facet=City,count:5` devolverá "Las Vegas", "LAS VEGAS" y "las vegas" como valores separados, a pesar de ser la misma ciudad.

+ `search=usa&$orderby=City` devolverá las ciudades en orden lexicográfico: "Las Vegas", "Seattle", "las vegas", incluso si la intención era ordenar las mismas ciudades juntas, independientemente del uso de mayúsculas y minúsculas. 

## <a name="normalizers"></a>Normalizadores

Un *normalizador* es un componente del motor de búsqueda que se encarga del procesamiento previo del texto para buscar coincidencias de palabras clave. Los normalizadores son similares a los analizadores, excepto en que no dividen en tokens la consulta. Las siguientes son algunas de las transformaciones que se pueden lograr mediante los normalizadores:

+ Convertir en mayúsculas o minúsculas.
+ Normalizar acentos y signos diacríticos como "ö" o "ê" a los caracteres ASCII equivalentes "o" y "e", respectivamente.
+ Asignar caracteres como `-` y el espacio en blanco a un carácter especificado por el usuario.

Los normalizadores se pueden especificar para los campos de texto en el índice y se aplican durante la indexación y ejecución de la consulta.

## <a name="predefined-and-custom-normalizers"></a>Normalizadores predefinidos y personalizados 

Azure Cognitive Search admite los normalizadores predefinidos para casos de uso comunes, junto con la capacidad de personalizarlos según sea necesario.

| Category | Descripción |
|----------|-------------|
| [Normalizadores predefinidos](#predefined-normalizers) | Están integrados y se pueden usar sin realizar ninguna configuración. |
|[Normalizadores personalizados](#add-custom-normalizers) | Para los escenarios avanzados. Requieren que el usuario defina una configuración para una combinación de elementos existentes, que consta de filtros de carácter y de token.<sup>1</sup>|

<sup>(1)</sup> Los normalizadores personalizados no especifican divisores en tokens, ya que los normalizadores siempre generan un token único.

## <a name="how-to-specify-normalizers"></a>Cómo especificar normalizadores

Los normalizadores se pueden especificar por campo en los campos de texto (`Edm.String` y `Collection(Edm.String)`) que tienen al menos una de las propiedades `filterable`, `sortable` o `facetable` establecidas en true. La configuración de un normalizador es opcional y su valor es `null` de manera predeterminada. Se recomienda evaluar los normalizadores predefinidos antes de configurar uno personalizado para facilitar su uso. Pruebe con otro normalizador si los resultados no son los esperados.

Los normalizadores solo se pueden especificar al agregar un nuevo campo al índice. Se recomienda evaluar las necesidades de normalización por adelantado y asignar los normalizadores en las fases iniciales de desarrollo, cuando eliminar y crear índices es algo habitual. Los normalizadores no se pueden especificar un campo que ya se ha creado.

1. Cuando cree una definición de campo en el [índice](/rest/api/searchservice/create-index), establezca la propiedad **normalizer** en una de las siguientes opciones: un [normalizador predefinido](#predefined-normalizers) (por ejemplo, `lowercase`), o un normalizador personalizado (que se defina en el mismo esquema del índice).  
 
   ```json
   "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "filterable": true,
      "analyzer": "en.microsoft",
      "normalizer": "lowercase"
      ...
    },
   ```

2. Los normalizadores personalizados deben definirse en la sección **[normalizers]** del índice en primer lugar y, a continuación, deben asignarse a la definición de campo tal como se muestra en el paso anterior. Para más información, consulte [Creación de un índice](/rest/api/searchservice/create-index) y también [Incorporación de normalizadores personalizados](#add-custom-normalizers).


   ```json
   "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": null,
      "normalizer": "my_custom_normalizer"
    },
   ```

 
> [!NOTE]
> Para cambiar el normalizador de un campo existente, tendrá que volver a generar el índice por completo (no puede volver a generar campos individuales).

Una buena solución alternativa para los índices de producción, en los que volver a generar índices es costoso, consiste en crear un nuevo campo idéntico al anterior pero con el nuevo normalizador, y usarlo en lugar del antiguo. Use [Update Index](/rest/api/searchservice/update-index) para incorporar el nuevo campo y [mergeOrUpload](/rest/api/searchservice/addupdate-or-delete-documents) para rellenarlo. Más adelante, como parte de un mantenimiento planeado del índice, puede limpiarlo para quitar campos obsoletos.

## <a name="add-custom-normalizers"></a>Incorporación de normalizadores personalizados

Los normalizadores personalizados se definen en el esquema de índice y se pueden especificar mediante la propiedad field. La definición del normalizador personalizado incluye un nombre, un tipo, uno o varios filtros de carácter y filtros de token. Los filtros de carácter y los filtros de token son los bloques de creación de un normalizador personalizado y se encargan del procesamiento del texto. Estos filtros se aplican de izquierda a derecha.

 `token_filter_name_1` es el nombre de un filtro de token, `char_filter_name_1` y `char_filter_name_2` son los nombres de los filtros de caracteres (consulte las tablas [Filtros de token compatibles](#supported-token-filters) y Filtros de caracteres a continuación para ver los valores válidos).

La definición del normalizador es una parte del índice mayor. Consulte [Create Index API](/rest/api/searchservice/create-index) (Creación de API de índice) para información sobre el resto del índice.

```
"normalizers":(optional)[
   {
      "name":"name of normalizer",
      "@odata.type":"#Microsoft.Azure.Search.CustomNormalizer",
      "charFilters":[
         "char_filter_name_1",
         "char_filter_name_2"
      ],
      "tokenFilters":[
         "token_filter_name_1
      ]
   }
],
"charFilters":(optional)[
   {
      "name":"char_filter_name_1",
      "@odata.type":"#char_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"tokenFilters":(optional)[
   {
      "name":"token_filter_name_1",
      "@odata.type":"#token_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
]
```

Los normalizadores personalizados se pueden agregar durante la creación del índice o posteriormente al actualizar uno existente. Para agregar un normalizador personalizado a un índice existente se debe especificar la marca **allowIndexDowntime** en [Actualizar índice](/rest/api/searchservice/update-index) y el índice no estará disponible durante unos segundos.

## <a name="normalizers-reference"></a>Referencia de normalizadores

### <a name="predefined-normalizers"></a>Normalizadores predefinidos
|**Nombre**|**Descripción y opciones**|  
|-|-|  
|estándar| Escribe en minúsculas el texto y pliega el código ASCII.|  
|minúsculas| Transforma los caracteres a minúsculas.|
|mayúsculas| Transforma los caracteres a mayúsculas.|
|asciifolding| Transforma los caracteres que no están en el bloque Unicode de caracteres latinos básicos en su equivalente ASCII, si existe alguno. Por ejemplo, cambia "à" por "a".|
|elision| Quita la elisión del principio de los tokens.|

### <a name="supported-char-filters"></a>Filtros de caracteres compatibles
Para obtener más información sobre los filtros de caracteres, consulte la [Referencia de filtros de caracteres](index-add-custom-analyzers.md#CharFilter).
+ [mapping](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/charfilter/MappingCharFilter.html)  
+ [pattern_replace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceCharFilter.html)

### <a name="supported-token-filters"></a>Filtros de tokens compatibles
La lista siguiente muestra los filtros de tokens admitidos para los normalizadores y es un subconjunto de los filtros de tokens generales implicados en un análisis léxico. Para obtener más información sobre los filtros, consulte la [Referencia de filtros de tokens](index-add-custom-analyzers.md#TokenFilters).

+ [arabic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ar/ArabicNormalizationFilter.html)
+ [asciifolding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html)
+ [cjk_width](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/cjk/CJKWidthFilter.html)  
+ [elision](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/util/ElisionFilter.html)  
+ [german_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/de/GermanNormalizationFilter.html)
+ [hindi_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/hi/HindiNormalizationFilter.html)  
+ [indic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/in/IndicNormalizationFilter.html)
+ [persian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/fa/PersianNormalizationFilter.html)
+ [scandinavian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianNormalizationFilter.html)  
+ [scandinavian_folding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianFoldingFilter.html)
+ [sorani_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ckb/SoraniNormalizationFilter.html)  
+ [lowercase](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)
+ [uppercase](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/UpperCaseFilter.html)


## <a name="custom-normalizer-example"></a>Ejemplo de normalizador personalizado

En el ejemplo siguiente se muestra una definición de normalizador personalizado con los filtros de caracteres y de token correspondientes. Las opciones personalizadas de los filtros de caracteres y de tokens se especifican por separado como construcciones con nombre y, después, se hace referencia a ellas en la definición del normalizados como se muestra a continuación.

* Se define un normalizador personalizado "my_custom_normalizer" en la sección `normalizers` de la definición del índice.
* El normalizador se compone de dos filtros de carácter y tres filtros de token: elisión, minúsculas y filtro de plegado de ASCII personalizado "my_asciifolding".
* El primer filtro de caracteres "map_dash" reemplaza todos los guiones por caracteres de subrayado, mientras que el segundo, "remove_whitespace", quita todos los espacios.

```json
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false,
        },
        {
           "name":"city",
           "type":"Edm.String",
           "filterable": true,
           "facetable": true,
           "normalizer": "my_custom_normalizer"
        }
     ],
     "normalizers":[
        {
           "name":"my_custom_normalizer",
           "@odata.type":"#Microsoft.Azure.Search.CustomNormalizer",
           "charFilters":[
              "map_dash",
              "remove_whitespace"
           ],,
           "tokenFilters":[              
              "my_asciifolding",
              "elision",
              "lowercase",
           ]
        }
     ],
     "charFilters":[
        {
           "name":"map_dash",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["-=>_"]
        },
        {
           "name":"remove_whitespace",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["\\u0020=>"]
        }
     ],
     "tokenFilters":[
        {
           "name":"my_asciifolding",
           "@odata.type":"#Microsoft.Azure.Search.AsciiFoldingTokenFilter",
           "preserveOriginal":true
        }
     ]
  }
```

## <a name="see-also"></a>Vea también
+ [Analizadores para procesamientos lingüísticos y textuales](search-analyzers.md)

+ [API de REST de documentos de búsqueda](/rest/api/searchservice/search-documents) 

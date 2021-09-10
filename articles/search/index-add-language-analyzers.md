---
title: Incorporación de analizadores de idioma a los campos de cadena
titleSuffix: Azure Cognitive Search
description: Análisis léxico en varios idiomas para consultas e índices en idiomas distintos del inglés en Azure Cognitive Search.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: 3a8a235e204826c26f20cc146003e9290331fe07
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "113091544"
---
# <a name="add-language-analyzers-to-string-fields-in-an-azure-cognitive-search-index"></a>Incorporación de analizadores de idioma a campos de cadena en un índice de Azure Cognitive Search

Un *analizador de idiomas* es un componente específico de un [analizador de texto](search-analyzers.md) que realiza un análisis léxico mediante las reglas lingüísticas del idioma de destino. Cada campo de búsqueda tiene una propiedad **analyzer**. Si el contenido consiste en cadenas traducidas, como campos independientes para texto en inglés y en chino, puede especificar los analizadores de idiomas en cada campo para acceder a las funcionalidades lingüísticas enriquecidas de esos analizadores.

## <a name="when-to-use-a-language-analyzer"></a>Cuándo usar un analizador de idioma

Debe considerar un analizador de idioma cuando el reconocimiento de la estructura de palabras o oraciones agrega valor al análisis de texto. Un ejemplo común es la asociación de formas de verbo irregulares ("traer" y "trajo") o nombres en masculino y femenino ("emperador" y "emperatriz"). Sin reconocimiento lingüístico, estas cadenas se analizan solo en características físicas, lo que no puede detectar la conexión. Dado que es más probable que los fragmentos de texto grandes tengan este contenido, los campos que se componen de descripciones, revisiones o resúmenes son buenos candidatos para un analizador de idioma.

También hay que tener en cuenta los analizadores de idioma cuando el contenido se compone de cadenas de idioma no occidentales. Aunque el [analizador predeterminado](search-analyzers.md#default-analyzer) es independiente del idioma, el concepto de usar espacios y caracteres especiales (guiones y barras diagonales) para separar cadenas suele ser más aplicable a los idiomas occidentales que a los no occidentales. 

Por ejemplo, en chino, japonés, coreano (CJK) y otros idiomas asiáticos, un espacio no es necesariamente un delimitador de palabras. Pensemos en la siguiente cadena japonesa. Dado que no tiene espacios, es probable que un analizador independiente del idioma analice toda la cadena como un token, cuando de hecho la cadena es en realidad una frase.

```
これは私たちの銀河系の中ではもっとも重く明るいクラスの球状星団です。
(This is the heaviest and brightest group of spherical stars in our galaxy.)
```

En el ejemplo anterior, una consulta correcta tendría que incluir el token completo o un token parcial con un carácter comodín de sufijo, lo que provocaría una experiencia de búsqueda no natural y de limitación.

Una mejor experiencia sería buscar palabras individuales: 明るい (brillante), 私たちの (nuestro), 銀河系 (galaxia). El uso de uno de los analizadores japoneses disponibles en Cognitive Search es más probable que desbloquee este comportamiento porque esos analizadores están mejor equipados para dividir el trozo de texto en palabras significativas en el idioma de destino.

## <a name="comparing-lucene-and-microsoft-analyzers"></a>Comparación de Lucene y analizadores de Microsoft

Azure Cognitive Search admite 35 analizadores respaldados por la tecnología Lucene y 50 analizadores respaldados por la tecnología de procesamiento de lenguaje natural de Microsoft que se usa en Office y Bing.

Es posible que algunos desarrolladores prefieran la solución más familiar, simple y de código abierto de Lucene. Los analizadores de idiomas de Lucene son más rápidos, pero los analizadores de Microsoft disponen de capacidades avanzadas, como la lematización, la descomposición de palabras (en idiomas como el alemán, danés, neerlandés, sueco, noruego, estonio, finés, húngaro, eslovaco) y el reconocimiento de entidades (direcciones URL, correos electrónicos, fechas y números). Si es posible, debe ejecutar las comparaciones de los analizadores de Microsoft y Lucene para decidir cuál es la que se ajusta mejor. Puede usar la [API de análisis](/rest/api/searchservice/test-analyzer) para ver los tokens generados a partir de un texto determinado mediante un analizador específico.

La indexación con analizadores de Microsoft es entre dos y tres veces más lenta de media que sus equivalentes de Lucene en función del idioma. El rendimiento de la búsqueda no debería verse afectado significativamente en las consultas de tamaño medio. 

### <a name="english-analyzers"></a>Analizadores de inglés

El analizador predeterminado es Standard Lucene, que funciona bien con el inglés, pero quizás no tan bien como el analizador de inglés de Lucene o el analizador de inglés de Microsoft.

+ El analizador de inglés de Lucene amplía el analizador estándar. Elimina los posesivos (los "'s" finales) de las palabras, aplica la lematización conforme al Algoritmo de lematización Porter y elimina las palabras no significativas del inglés.  

+ El analizador de inglés de Microsoft utiliza lemas en lugar de lexemas. Esto significa que puede controlar mucho mejor formas de palabras derivadas e irregulares, lo que da lugar a unos resultados de búsqueda más pertinentes. 

## <a name="how-to-specify-a-language-analyzer"></a>Cómo especificar un analizador de idioma

Puede definir un analizador de idioma en los campos "que se pueden buscar" de tipo Edm.String durante la definición del campo.

Aunque las definiciones de los campos tienen varias propiedades relacionadas con el analizador, solo se puede usar la propiedad "analyzer" para los analizadores de idioma. El valor de "analyzer" debe ser uno de los analizadores de idioma de la lista de analizadores de soporte.

```json
{
  "name": "hotels-sample-index",
  "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": "en.microsoft",
      "indexAnalyzer": null,
      "searchAnalyzer": null
    },
    {
      "name": "Description_fr",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": "fr.microsoft",
      "indexAnalyzer": null,
      "searchAnalyzer": null
    },
```

Para obtener más información sobre cómo crear un índice y establecer las propiedades de los campos, consulte [Creación de índices (REST)](/rest/api/searchservice/create-index). Para obtener más información sobre el análisis de texto, consulte [Analizadores de Azure Cognitive Search](search-analyzers.md).

<a name="language-analyzer-list"></a>

## <a name="supported-language-analyzers"></a>Analizadores de idioma compatibles

 A continuación se muestra la lista de idiomas admitidos y los nombres de analizadores de Lucene y de Microsoft.  

| Idioma | Nombre del analizador de Microsoft | Nombre del analizador de Lucene |
|----------|-------------------------|----------------------|
| Árabe   | ar.microsoft | ar.lucene |
| Armenio |           | hy.lucene |
| Bengalí   | bn.microsoft |  |
| Vasco   |  | eu.Lucene |
| Búlgaro | bg.microsoft | bg.lucene |
| Catalán  | ca.microsoft | ca.lucene |
| Chino simplificado | zh-Hans.microsoft | zh-Hans.lucene |
| Chino tradicional | zh-Hant.microsoft | zh-Hant.lucene |
| Croata | hr.microsoft |  |
| Checo | cs.microsoft | cs.lucene |
| Danés | da.microsoft | da.lucene |
| Neerlandés | nl.microsoft | nl.lucene |
| Inglés | en.microsoft | en.lucene |
| Estonio | et.microsoft |  |
| Finés | fi.microsoft | fi.lucene |
| Francés | fr.microsoft | fr.lucene |
| Gallego |  | gl.lucene |
| Alemán | de.microsoft | de.lucene |
| Griego | el.microsoft | el.lucene |
| Gujarati | gu.microsoft |  |
| Hebreo | he.microsoft |  |
| Hindi | hi.microsoft | hi.lucene |
| Húngaro | hu.microsoft | hu.lucene |
| Islandés | is.microsoft |  |
| Indonesio (Bahasa) | id.microsoft | id.lucene |
| Irlandés |  | ga.lucene |
| Italiano | it.microsoft | it.lucene |
| Japonés | ja.microsoft | ja.lucene |
| Canarés | kn.microsoft |  |
| Coreano | ko.Microsoft | ko.lucene |
| Letón | lv.microsoft | lv.lucene |
| Lituano | lt.microsoft |  |
| Malayalam | ml.microsoft |  |
| Malayo (latino) | ms.microsoft |  |
| Maratí | mr.microsoft |  |
| Noruego | nb.microsoft | no.lucene |
| Persa |  | fa.lucene |
| Polaco | pl.microsoft | pl.lucene |
| Portugués (Brasil) | pt-Br.microsoft | pt-Br.lucene |
| Portugués (Portugal) | pt-Pt.microsoft | pt-Pt.lucene |
| Punjabi | pa.microsoft |  |
| Rumano | ro.microsoft | ro.lucene |
| Ruso | ru.microsoft | ru.lucene |
| Serbio (cirílico) | sr-cyrillic.microsoft |  |
| Serbio (latino) | sr-latin.microsoft |  |
| Eslovaco | sk.microsoft |  |
| Esloveno | sl.microsoft |  |
| Español | es.Microsoft | es.lucene |
| Sueco | sv.microsoft | sv.lucene |
| Tamil | ta.microsoft |  |
| Telugu | te.microsoft |  |
| Tailandés | th.microsoft | th.lucene |
| Turco | tr.microsoft | tr.lucene |
| Ucraniano | uk.microsoft |  |
| Urdu | ur.microsoft |  |
| Vietnamita | vi.microsoft |  |

 Todos los analizadores con nombres anotados con **lucene** disponen de la tecnología de [analizadores de idioma de Apache Lucene](https://lucene.apache.org/core/6_6_1/core/overview-summary.html ).

## <a name="see-also"></a>Consulte también  

+ [Creación de un índice](search-what-is-an-index.md)
+ [Creación de un índice para varios idiomas](search-language-support.md)
+ [Creación de índices (API de REST)](/rest/api/searchservice/create-index)  
+ [Clase LexicalAnalyzerName (Azure SDK para .NET)](/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzername)

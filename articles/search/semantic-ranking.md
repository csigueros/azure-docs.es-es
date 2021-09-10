---
title: Clasificación semántica
titleSuffix: Azure Cognitive Search
description: Obtenga información sobre cómo funciona el algoritmo de clasificación semántica en Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2021
ms.openlocfilehash: 61fb6de73567b7a83e2c4db2010c717a160aebe0
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114473994"
---
# <a name="semantic-ranking-in-azure-cognitive-search"></a>Clasificación semántica en Azure Cognitive Search

> [!IMPORTANT]
> La búsqueda semántica está en versión preliminar pública en los [términos de uso complementarios](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Está disponible a través de Azure Portal, la API REST en versión preliminar y los SDK en versión beta. Estas características son facturables. Para más información, consulte [Disponibilidad y precios](semantic-search-overview.md#availability-and-pricing).

La clasificación semántica es una extensión de la canalización de ejecución de consultas que mejora la precisión mediante la reclasificación de las coincidencias principales de un conjunto de resultados inicial. La clasificación semántica está respaldada por grandes redes basadas en transformadores, y que se entrenan para capturar el significado semántico de los términos de la consulta, en lugar de las coincidencias lingüísticas en palabras clave. A diferencia del [algoritmo de clasificación de similitudes predeterminado](index-ranking-similarity.md), el clasificador semántico usa el contexto y el significado de las palabras para determinar la relevancia.

La clasificación semántica es una operación que consume tiempo y recursos. Para completar el procesamiento según la latencia esperada de una operación de consulta, las entradas en el clasificador semántico se consolidan y reducen para que los pasos de resumen y reclasificación subyacentes se puedan completar lo más rápido posible.

## <a name="pre-processing"></a>Procesamiento previo

Antes de realizar la puntuación por relevancia, el contenido se debe reducir a un número de entradas que se pueda administrar de forma eficaz mediante el categorizador semántico.

1. En primer lugar, la reducción del contenido comienza con el conjunto de resultados inicial que devuelve el [algoritmo de clasificación de similitud](index-ranking-similarity.md) predeterminado que se usa para la búsqueda de palabras clave. En el caso de una consulta determinada, los resultados pueden ser un puñado de documentos, hasta el límite máximo de 1000. Dado que el procesamiento de un gran número de coincidencias tardaría demasiado, solo los 50 primeros resultados pasan a la clasificación semántica.

   Sea cual sea el número de documentos, ya sea uno o 50, el conjunto de resultados inicial establece la primera iteración del corpus del documento para realizar la clasificación semántica.

1. A continuación, en el corpus, el contenido de cada campo en "searchFields" se extrae y se combina en una cadena larga.

1. Después de realizar la consolidación de la cadena, cualquier cadena que sea excesivamente larga se recorta para garantizar que la longitud total cumple los requisitos de entrada del paso de resumen.

   Este ejercicio de recorte es el motivo por el que es importante colocar en primer lugar campos concisos en "searchFields" para asegurarse de que se incluyen en la cadena. Si tiene documentos muy grandes con campos con mucho texto, se omite todo lo que se encuentra después del límite máximo.

Cada documento se representa ahora con una sola cadena larga.

La cadena se compone de tokens, no caracteres ni palabras. El número máximo de tokens es de 128 tokens únicos. Para realizar una estimación, puede suponer que 128 tokens equivalen aproximadamente a una cadena con una longitud de 128 palabras. 

> [!NOTE]
>La asignación del analizador determina la tokenización en los campos de búsqueda. Si usa un analizador especializado, como nGram o EdgeNGram, es posible que quiera excluir ese campo de searchFields. Para obtener información sobre cómo se tokenizan las cadenas, puede revisar la salida de tokens de un analizador mediante la [API de REST del analizador de pruebas](/rest/api/searchservice/test-analyzer).

## <a name="extraction"></a>Extracción

Después de realizar la reducción de la cadena, ahora es posible pasar las entradas reducidas a través de los modelos de comprensión de lectura del equipo y de representación del lenguaje para determinar qué oraciones y frases resumirán mejor el documento con respecto a la consulta. Esta fase extrae el contenido de la cadena que se moverá hacia el clasificador semántico.

Las entradas en el resumen son las cadenas largas que se obtienen para cada documento en la fase de preparación. En cada cadena, el modelo de resumen encuentra un pasaje que es el más representativo. Este pasaje también constituye un [subtítulo semántico](semantic-how-to-query-request.md) para el documento. Cada subtítulo está disponible en una versión de texto sin formato y en una versión de resaltado, y con frecuencia es menor que 200 palabras por documento.

También se devolverá una [respuesta semántica](semantic-answers.md) si especificó el parámetro "respuestas", si la consulta se escribió como una pregunta y si se puede encontrar un paso en la cadena larga que es probable que proporcione una respuesta a la pregunta.

## <a name="semantic-ranking"></a>Clasificación semántica

1. Los subtítulos se evalúan para la relevancia conceptual y semántica, en relación con la consulta proporcionada.

   En el diagrama siguiente se muestra una ilustración de lo que significa "relevancia semántica". Tenga en cuenta el término "capital", que podría usarse en el contexto de finanzas, ley, geografía o gramática. Si una consulta incluye términos del mismo espacio vectorial (por ejemplo, "capital" e "inversión"), un documento que también incluya tokens en el mismo clúster puntuará más que uno que no lo sea.

   :::image type="content" source="media/semantic-search-overview/semantic-vector-representation.png" alt-text="Representación vectorial para el contexto" border="true":::

1. Se asigna @search.rerankerScore a cada documento en función de la importancia semántica del subtítulo.

1. Una vez puntuados todos los documentos, se muestran en orden descendente en función de la puntuación y se incluyen en la carga de respuesta de la consulta. La carga útil incluye respuestas, texto sin formato y subtítulos resaltados, así como cualquier campo que se haya marcado como recuperable o especificado en una cláusula SELECT.

## <a name="next-steps"></a>Pasos siguientes

La clasificación semántica se ofrece en niveles estándar en regiones específicas. Para obtener más información sobre la disponibilidad y el registro, consulte [Disponibilidad y precios](semantic-search-overview.md#availability-and-pricing). Un nuevo tipo de consulta permite realizar la clasificación de relevancia y las estructuras de respuesta de la búsqueda semántica. Para comenzar, [cree una consulta semántica](semantic-how-to-query-request.md).

Como alternativa, revise los artículos siguientes sobre la clasificación predeterminada. La clasificación semántica depende del rango de similitud para devolver los resultados iniciales. Tener información acerca de la ejecución de las consultas y la clasificación le proporcionará una amplia comprensión de cómo funciona todo el proceso.

+ [Búsqueda de texto completo en Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Similitud y puntuación en Azure Cognitive Search](index-similarity-and-scoring.md)
+ [Analizadores para el procesamiento de texto en Azure Cognitive Search](search-analyzers.md)
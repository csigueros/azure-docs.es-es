---
title: Búsqueda semántica
titleSuffix: Azure Cognitive Search
description: Conozca cómo Cognitive Search usa modelos de búsqueda semántica de aprendizaje profundo de Bing para crear resultados de búsqueda más intuitivos.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/09/2021
ms.custom: references_regions
ms.openlocfilehash: 057afd588193a8fdfba020e25d086dc915bb9eaa
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128612840"
---
# <a name="semantic-search-in-azure-cognitive-search"></a>Búsqueda semántica en Azure Cognitive Search

> [!IMPORTANT]
> La búsqueda semántica está en versión preliminar pública en los [términos de uso complementarios](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Está disponible a través de Azure Portal, la API REST en versión preliminar y los SDK en versión beta. Estas características son facturables. Para más información, consulte [Disponibilidad y precios](semantic-search-overview.md#availability-and-pricing).

La búsqueda semántica es una colección de funcionalidades relacionadas con las consultas que aporta relevancia semántica y comprensión del lenguaje a los resultados de la búsqueda. Este artículo es una introducción general a la búsqueda semántica, con descripciones de cada característica y su funcionamiento colectivo. La tecnología se describe en el vídeo insertado y la disponibilidad y los precios aparece en la sección del final.

La búsqueda semántica es una característica Prémium. Si necesita información general, le recomendamos este artículo, pero si prefiere empezar a trabajar, siga estos pasos:

> [!div class="checklist"]
> * [Compruebe los requisitos regionales y de nivel de servicio](#availability-and-pricing).
> * [Regístrese para el programa de versión preliminar](https://aka.ms/SemanticSearchPreviewSignup). La solicitud puede tardar hasta dos días laborables en procesarse.
> * Tras la aceptación, cree o modifique las consultas para [devolver subtítulos y resaltados semánticos](semantic-how-to-query-request.md).
> * Agregue algunas propiedades más para devolver también [respuestas semánticas](semantic-answers.md).
> * También puede invocar la [corrección ortográfica](speller-how-to-add.md) para maximizar la precisión y la coincidencia.

## <a name="what-is-semantic-search"></a>¿Qué es la búsqueda semántica?

La búsqueda semántica es una colección de características que mejoran la calidad de los resultados de la búsqueda. Cuando se habilita en el servicio de búsqueda, extiende la canalización de ejecución de consultas de dos maneras. En primer lugar, agrega una clasificación secundaria a un conjunto de resultados inicial, lo que promociona los resultados más pertinentes semánticamente a la parte superior de la lista. En segundo lugar, extrae y devuelve subtítulos y respuestas en la respuesta, que puede representar en una página de búsqueda para mejorar la experiencia de búsqueda del usuario.

| Característica | Descripción |
|---------|-------------|
| [Cambio de clasificación semántico](semantic-ranking.md) | Usa el contexto o el significado semántico para calcular una nueva puntuación de relevancia en los resultados existentes. |
| [Títulos y resaltados semánticos](semantic-how-to-query-request.md) | Extrae las oraciones y frases de un documento que mejor resumen el contenido; se resaltan los pasajes clave para facilitar el análisis. Los títulos que resumen un resultado son útiles cuando los campos de contenido individuales son demasiado densos para la página de resultados. El texto resaltado eleva los términos y frases más relevantes para que los usuarios puedan determinar rápidamente por qué se consideró relevante una coincidencia. |
| [Respuestas semánticas](semantic-answers.md) | Es una subestructura opcional y adicional que se devuelve desde una consulta semántica. Proporciona una respuesta directa a una consulta que se parece a una pregunta. Requiere que un documento tenga texto con las características de una respuesta. |
| [Corrector ortográfico](speller-how-to-add.md) | Corrige los errores tipográficos antes de que los términos de consulta lleguen al motor de búsqueda. |

## <a name="how-semantic-ranking-works"></a>Funcionamiento de la clasificación semántica

La *clasificación semántica* busca el contexto y la relación entre los términos, elevando las coincidencias que tienen más sentido en función de la consulta. Language Understanding busca resúmenes o *títulos* y *respuestas* en el contenido, y los incluye en la respuesta, que luego se puede representar en una página de resultados de búsqueda para una experiencia de búsqueda más productiva.

Los modelos previamente entrenados de última generación se usan para el resumen y la clasificación. Para mantener el rápido rendimiento que los usuarios esperan de la búsqueda, el resumen semántico y la clasificación se aplican solo a los 50 resultados principales, en función de la puntuación del [algoritmo de puntuación de similitud predeterminada](index-similarity-and-scoring.md#similarity-ranking-algorithms). Al usar los resultados como un corpus de documento, la clasificación semántica vuelve a puntuar los resultados en función de la intensidad semántica de la coincidencia.

La tecnología subyacente es de Bing y Microsoft Research, y se integra con la infraestructura de Cognitive Search como una característica de complemento. Para más información sobre las inversiones en investigación e inteligencia artificial que respaldan la búsqueda semántica, consulte [Formas en que la inteligencia artificial de Bing Azure se usa en Cognitive Search (blog de Microsoft Research)](https://www.microsoft.com/research/blog/the-science-behind-semantic-search-how-ai-from-bing-is-powering-azure-cognitive-search/).

En el vídeo siguiente se proporciona información general de las funcionalidades.

> [!VIDEO https://www.youtube.com/embed/yOf0WfVd_V0]

### <a name="order-of-operations"></a>Orden de las operaciones

Los componentes de la búsqueda semántica amplían la canalización de ejecución de consultas existente en ambas direcciones. Si habilita la corrección ortográfica, el [corrector ortográfico](speller-how-to-add.md) corrige los errores tipográficos al principio de la consulta, antes de que los términos de la consulta lleguen al motor de búsqueda.

:::image type="content" source="media/semantic-search-overview/semantic-workflow.png" alt-text="Componentes semánticos de una ejecución de consulta" border="true":::

La ejecución de la consulta se realiza de la manera habitual, con el análisis de términos, varios exámenes y el análisis de los índices invertidos. El motor recupera los documentos mediante la coincidencia de tokens y puntúa los resultados mediante el [algoritmo de puntuación de similitud predeterminada](index-similarity-and-scoring.md#similarity-ranking-algorithms). Las puntuaciones se calculan en función del grado de similitud lingüística entre los términos de consulta y los términos coincidentes del índice. Si los ha definido, los perfiles de puntuación también se aplican en esta fase. A continuación, los resultados se pasan al subsistema de búsqueda semántica.

En el paso de preparación, el corpus del documento que se ha devuelto desde el conjunto de resultados inicial se analiza a nivel de oración y de párrafo para buscar pasajes que resuman cada documento. A diferencia de la búsqueda de palabras clave, este paso usa la lectura y la comprensión automáticas para evaluar el contenido. Con esta fase de procesamiento de contenido, una consulta semántica devuelve [descripciones](semantic-how-to-query-request.md) y [respuestas](semantic-answers.md). Para formular estos elementos, la búsqueda semántica usa la representación del lenguaje para extraer y resaltar los pasajes principales que mejor resuman un resultado. Si la consulta de búsqueda es una pregunta y se solicitan respuestas, la respuesta también incluirá el pasaje de texto que mejor responda a la pregunta, según se exprese en la consulta de búsqueda. 

En el caso de los títulos y las respuestas, se usa el texto existente en la formulación. Tenga en cuenta que los modelos semánticos no componen nuevas oraciones o frases a partir del contenido disponible, ni aplican la lógica para llegar a nuevas conclusiones. En resumen, el sistema nunca devolverá contenido que no exista.

A continuación, los resultados se vuelven a puntuar en función de la [similitud conceptual](semantic-ranking.md) de los términos de la consulta.

Para usar las funcionalidades semánticas en las consultas, deberá realizar pequeñas modificaciones en la [solicitud de búsqueda](semantic-how-to-query-request.md), pero no necesitará realizar más configuraciones ni nuevas indexaciones.

## <a name="semantic-capabilities-and-limitations"></a>Funcionalidades y limitaciones semánticas

La búsqueda semántica es una tecnología novedosa, por lo que es importante establecer expectativas sobre lo que puede y no puede hacer. Lo que puede hacer es mejorar la calidad de la búsqueda:

* Promoviendo las coincidencias que están semánticamente más cerca de la intención de la consulta original.

* Buscando cadenas en cada resultado que se puedan usar como subtítulos y, posiblemente, respuestas, que se puedan representar en una página de resultados de la búsqueda.

Lo que no puede hacer es volver a ejecutar la consulta en todo el corpus para buscar resultados semánticamente relevantes. La búsqueda semántica vuelve a clasificar el conjunto de resultados *existente*, que consta de los 50 primeros resultados puntuados por el algoritmo de clasificación predeterminado. Además, la búsqueda semántica no puede crear nuevas cadenas o información. Los subtítulos y las respuestas se extraen textualmente del contenido, por lo que si los resultados no incluyen texto parecido a una respuesta, los modelos de lenguaje no producirán uno.

Aunque la búsqueda semántica no es ventajosa en todos los escenarios, cierto contenido puede beneficiarse significativamente de sus funcionalidades. Los modelos de lenguaje de la búsqueda semántica funcionan mejor en contenido que permite búsquedas, que tiene gran cantidad de información y está estructurado como prosa. Una knowledge base, documentación en línea o los documentos con contenido descriptivo son los que más se benefician de las funcionalidades de la búsqueda semántica.

## <a name="availability-and-pricing"></a>Disponibilidad y precios

La búsqueda semántica está disponible a través del [registro de suscripción](https://aka.ms/SemanticSearchPreviewSignup). Hay una sola suscripción para las características semánticas y la revisión ortográfica.

| Característica | Nivel | Region (Región) | Suscripción | Precios |
|---------|------|--------|---------------------|-------------------|
| Búsqueda semántica (títulos, resaltados, respuestas) | Nivel estándar (S1, S2, S3) | Centro-norte de EE. UU., Oeste de EE. UU., Oeste de EE. UU. 2, Este de EE. UU. 2, Norte de Europa, Oeste de Europa | Obligatorio | [Página de precios de Cognitive Search](https://azure.microsoft.com/pricing/details/search/)  |
| Corrector ortográfico | Any | Centro-norte de EE. UU., Oeste de EE. UU., Oeste de EE. UU. 2, Este de EE. UU. 2, Norte de Europa, Oeste de Europa | Obligatorio | Ninguno (gratis) |

Puede usar la revisión ortográfica sin búsqueda semántica, de forma gratuita. Los cargos por la búsqueda semántica se aplican cuando las solicitudes de consulta incluyen `queryType=semantic` y la cadena de búsqueda no está vacía (por ejemplo, `search=pet friendly hotels in new york`. Las búsquedas vacías (consultas donde `search=*`) no se cobran, ni siquiera si queryType está establecido en `semantic`.

## <a name="disable-semantic-search"></a>Deshabilitación de la búsqueda semántica

Solo un servicio de búsqueda que tenga habilitada la característica puede incurrir en cargos. Sin embargo, si desea una protección completa contra el uso accidental, [deshabilite la opción](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#searchsemanticsearch).

* En la API REST de administración, versión 2021-04-01-Preview, se proporciona esta opción.

* Se requieren permisos de propietario o colaborador para deshabilitar las características.

```http
PUT https://management.azure.com/subscriptions/{{subscriptionId}}/resourcegroups/{{resource-group}}/providers/Microsoft.Search/searchServices/{{search-service-name}}?api-version=2021-04-01-Preview
    {
      "location": "{{region}}",
      "sku": {
        "name": "standard"
      },
      "properties": {
        "semanticSearch": "disabled"
      }
    }
```

Para volver a habilitar la búsqueda semántica, vuelva a ejecutar la solicitud anterior, estableciendo "semanticSearch" en "gratis" (valor predeterminado) o "estándar".

> [!TIP]
> Las llamadas a la API REST de administración se autentican mediante Azure Active Directory. Para obtener instrucciones sobre cómo configurar una solicitud y un principio de seguridad, consulte esta entrada de blog sobre [API REST de Azure con Postman (2021)](https://blog.jongallant.com/2021/02/azure-rest-apis-postman-2021/). El ejemplo anterior se probó con las instrucciones y la colección de Postman proporcionadas en esa entrada de blog.

## <a name="next-steps"></a>Pasos siguientes

[Regístrese](https://aka.ms/SemanticSearchPreviewSignup) para obtener la versión preliminar en un servicio de búsqueda que cumple con los requisitos regionales y de nivel indicados en la sección anterior.

La solicitud puede tardar hasta dos días laborables en procesarse. Una vez que el servicio esté listo, [cree una consulta semántica](semantic-how-to-query-request.md) para evaluar su rendimiento en el contenido.

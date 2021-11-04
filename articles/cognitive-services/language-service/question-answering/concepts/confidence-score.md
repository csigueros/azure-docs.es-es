---
title: 'Puntuación de confianza: respuesta a preguntas'
titleSuffix: Azure Cognitive Services
description: Cuando una consulta de usuario se compara con una base de conocimiento, la respuesta a preguntas devuelve respuestas pertinentes, junto con una puntuación de confianza.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 605f4ed4e4d146b6a03979496f171417330732ba
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093339"
---
# <a name="confidence-score"></a>Puntuación de confianza

Cuando una consulta de usuario se compara con un proyecto (también denominado base de conocimiento), la respuesta a preguntas devuelve respuestas pertinentes, junto con una puntuación de confianza. Esta puntuación indica el grado de confianza que se tiene en que la respuesta sea la coincidencia correcta para la consulta de usuario en cuestión.

La puntuación de confianza es un número entre 0 y 100. Una puntuación de 100 es probablemente una coincidencia exacta, mientras que una puntuación de 0 significa que no se ha encontrado ninguna respuesta coincidente. Cuanto mayor sea la puntuación, mayor será la confianza en la respuesta. Para una consulta determinada, podrían devolverse varias respuestas. En ese caso, las respuestas se devuelven en orden de mayor a menor puntuación de confianza.

En la tabla siguiente se indica la confianza típico asociada a un resultado determinado.

|Valor de la puntuación|Significado de la puntuación|Consulta de ejemplo|
|--|--|--|
|90-100|Coincidencia casi exacta de la consulta del usuario y una pregunta de KB.|
|>70|Confianza alta. Normalmente, una buena respuesta que responde por completo a la consulta del usuario.|
|50-70|Confianza media. Normalmente, una respuesta bastante buena que debería responder el propósito principal de la consulta del usuario.|
|30-50|Confianza baja. Normalmente, una respuesta relacionada que responde en parte a la intención del usuario.|
|<30|Confianza muy baja. Normalmente, no responde a la consulta del usuario, pero contiene algunas palabras o frases coincidentes. |
|0|Ninguna coincidencia, por lo que no se devuelve una respuesta.|

## <a name="choose-a-score-threshold"></a>Seleccionar un umbral de puntuación

En la tabla anterior se muestra el rango de puntuaciones que pueden producirse al realizar consultas con respuesta a preguntas. Pero como cada proyecto es diferente y contiene tipos de palabras, intenciones y objetivos distintos, se recomienda que pruebe y seleccione el umbral que mejor le funcione. De manera predeterminada, el umbral se establece en `0`, por lo que se devuelven todas las respuestas posibles. El umbral recomendado que debe funcionar para la mayoría de los proyectos es **50**.

Al elegir el umbral, tenga en cuenta el equilibrio entre los valores de **Precisión** y **Cobertura**, y ajuste el umbral en función de los requisitos.

- Si la **Precisión** es más importante en su caso, aumente el umbral. De este modo, cada vez que se devuelva una respuesta, el resultado será mucho más confiable y probablemente responderá a lo que buscan los usuarios. En este caso, podrían quedar más preguntas sin responder. 

- Si la **Cobertura** (o coincidencia) es más importante y quiere responder a tantas preguntas como sea posible, incluso aunque solo haya una relación parcial con la pregunta del usuario, reduzca el umbral. Esto significa que podría haber más casos en los que el resultado no responde a la consulta en sí del usuario, pero proporciona otra respuesta en cierto modo relacionada.

## <a name="set-threshold"></a>Establecimiento del umbral

Establezca la puntuación del umbral como una propiedad del [cuerpo JSON de la API REST](../quickstart/sdk.md). Esto significa que se configura para cada llamada a la API REST.

## <a name="improve-confidence-scores"></a>Mejorar las puntuaciones de confianza

Para mejorar la puntuación de confianza de una respuesta concreta a una consulta de usuario, puede agregar la consulta del usuario a la base de conocimiento como una pregunta alternativa en la respuesta. También puede usar [sinónimos](../tutorials/adding-synonyms.md) que no distinguen mayúsculas de minúsculas para agregar sinónimos a las palabras clave del proyecto.

## <a name="similar-confidence-scores"></a>Puntuaciones de confianza similares

Si varias respuestas tienen una puntuación de confianza similar, es probable que la consulta fuera demasiado genérica y, por tanto, coincidiera con la misma probabilidad con varias respuestas. Intente estructurar mejor sus preguntas y respuestas para que todas las entidades QnA tengan una intención distinta.

## <a name="confidence-score-differences-between-test-and-production"></a>Diferencias de puntuación de confianza entre prueba y producción

La puntuación de confianza de una respuesta puede cambiar de manera apenas perceptible entre la prueba y la versión implementada del proyecto, aunque el contenido sea el mismo. Esto se debe a que el contenido de la prueba y el proyecto implementado se encuentran en distintos índices de Azure Cognitive Search.

El índice de prueba contiene todos los pares de preguntas y respuestas del proyecto. Al consultar el índice de prueba, la consulta se aplica a todo el índice y los resultados se limitan a la partición de ese proyecto específico. Si los resultados de la consulta de prueba afectan negativamente a la capacidad de validar el proyecto, puede:
* Organizar el proyecto mediante una de las opciones siguientes:
    * Un recurso restringido a un proyecto: restrinja el único recurso de idioma (y el índice de prueba de Azure Cognitive Search resultante) a un proyecto o una base de conocimiento.
    * Dos recursos, uno para pruebas y otro para producción: puede tener dos recursos de idioma, y usar uno para las pruebas (con sus propios índices de prueba y producción) y el otro para producción (también con sus propios índices de prueba y producción).
* Use siempre los mismos parámetros al consultar los proyectos de prueba y producción.

Al implementar un proyecto, el contenido de preguntas y respuestas del proyecto pasa del índice de prueba a un índice de producción en Azure Search.

Si tiene un proyecto en regiones diferentes, en cada una se usa su propio índice de Azure Cognitive Search. Dado que se usan distintos índices, las puntuaciones no serán las mismas exactamente.

## <a name="no-match-found"></a>No se encontraron coincidencias

Si el clasificador no encuentra ninguna coincidencia correcta, se devuelven una puntuación de confianza de 0,0 o "None" (Ninguna), y la respuesta predeterminada. Puede cambiar la [respuesta predeterminada](../how-to/change-default-answer.md).

## <a name="next-steps"></a>Pasos siguientes
> [!div class="nextstepaction"]
> [procedimientos recomendados](./best-practices.md)

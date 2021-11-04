---
title: Proyectos multilingües
titleSuffix: Azure Cognitive Services
description: Obtenga información sobre cómo usar proyectos multilingües en la comprensión del lenguaje conversacional.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 4bbad9e51e26d643ff121e9b80b5da3fbafd89b9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091329"
---
# <a name="multilingual-projects"></a>Proyectos multilingües

La comprensión del lenguaje conversacional facilita la extensión del proyecto a varios idiomas a la vez. Al habilitar varios idiomas en los proyectos, podrá agregar expresiones y sinónimos específicos del idioma al proyecto y obtener predicciones multilingües para las intenciones y entidades. 

## <a name="multilingual-intent-and-learned-entity-components"></a>Intención multilingüe y componentes de entidades aprendidas

Al habilitar varios idiomas en un proyecto, puede entrenarlo con un idioma principal y obtener inmediatamente predicciones en otros. 

Por ejemplo, puede entrenar el proyecto completamente con expresiones en inglés y consultarlo en francés, alemán, mandarín, japonés, coreano y otros. La comprensión del lenguaje conversacional facilita el escalado de los proyectos a varios idiomas mediante el uso de tecnología multilingüe para entrenar los modelos.

Siempre que identifique que un idioma determinado no funciona tan bien como otros, puede agregar expresiones para ese idioma en el proyecto. En la página de [expresiones de etiqueta](../how-to/tag-utterances.md) de Language Studio puede seleccionar el idioma de la expresión que va a agregar. Al introducir ejemplos de ese idioma en el modelo, se introduce más sintaxis de ese idioma y aprende a predecirlo mejor.

No se espera que agregue la misma cantidad de expresiones para cada idioma. Debe compilar la mayor parte del proyecto en un idioma y agregar solo algunas expresiones en los idiomas que observe que no tienen un buen rendimiento. Si crea un proyecto que está principalmente en inglés y empieza a probarlo en francés, alemán y español, es posible que observe que en alemán no funciona tan bien como en los otros dos idiomas. En ese caso, considere la posibilidad de agregar el 5 % de los ejemplos originales en inglés en alemán, entrenar un modelo nuevo y volver a probarlo en alemán. Debería ver mejores resultados para las consultas en alemán. Cuantas más expresiones agregue, más probable es que mejoren los resultados. 

Al agregar datos en otro idioma, no debe esperar que se produzca un efecto negativo en los otros. 

## <a name="list-and-prebuilt-components-in-multiple-languages"></a>Componentes de lista y precompilados en varios idiomas

Los proyectos con varios idiomas habilitados le permitirán especificar sinónimos **por idioma** para cada clave de lista. En función del idioma con el que consulte el proyecto, solo obtendrá coincidencias para el componente de lista con sinónimos de ese idioma. Al consultar el proyecto, puede especificar el idioma en el cuerpo de la solicitud:

```json
"query": "{query}"
"language": "{language code}"
```

Si no proporciona un idioma, volverá al predeterminado del proyecto. Vea el artículo sobre [compatibilidad con idiomas](../language-support.md) para obtener una lista de códigos de idioma diferentes.

Los componentes precompilados son similares, donde debería esperar obtener predicciones para los componentes precompilados que están disponibles en idiomas específicos. El idioma de la solicitud determina de nuevo qué componentes se intentan predecir. Vea el artículo de referencia sobre [componentes precompilados](../prebuilt-component-reference.md) para obtener información sobre la compatibilidad con idiomas de cada componente precompilado.

## <a name="next-steps"></a>Pasos siguientes

* [Expresiones de etiqueta](../how-to/tag-utterances.md) 
* [Entrenamiento de un modelo](../how-to/train-model.md)

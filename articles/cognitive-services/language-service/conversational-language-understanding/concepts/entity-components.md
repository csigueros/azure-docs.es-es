---
title: Componentes de entidad de Conversational Language Understanding
titleSuffix: Azure Cognitive Services
description: Información sobre cómo Conversational Language Understanding extrae entidades de un texto
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-clu, ignite-fall-2021
ms.openlocfilehash: c42028b10a71a039a208f82cfacb58e5ba8e07cc
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132347734"
---
# <a name="entity-components"></a>Componentes de la entidad

En Conversational Language Understanding, las entidades son fragmentos de información relevantes que se extraen de las expresiones. Hay diferentes métodos para extraer una entidad: por aprendizaje a través del contexto, por coincidencia con respecto una lista o por detección mediante una entidad reconocida creada previamente. Cada entidad del proyecto se compone de uno o varios de estos métodos, que se definen como "componentes de la entidad". Cuando más de un componente define una entidad, sus predicciones pueden superponerse. Puede determinar el comportamiento de una predicción de entidad cuando sus componentes se superponen usando un conjunto fijo de opciones del **método de superposición**.

## <a name="component-types"></a>Tipos de componentes

Un componente de entidad determina una manera de extraer la entidad. Una entidad puede contener simplemente un componente, que determinaría el único método que se usaría para extraer la entidad, o varios componentes para ampliar las formas en que se define y extrae la entidad.

### <a name="learned-component"></a>Componente aprendido

El componente aprendido usa las etiquetas de entidad con las que etiqueta las expresiones para entrenar un modelo de aprendizaje automático. El modelo aprende a predecir dónde está la entidad, en función del contexto dentro de la expresión. Las etiquetas proporcionan ejemplos de dónde se espera que la entidad esté presente en una expresión, en función del significado de las palabras que la rodea y de las palabras etiquetadas. Este componente solo se define si agrega etiquetas mediante el etiquetado de expresiones para la entidad. Si no etiqueta ninguna expresión con la entidad, no tendrá un componente aprendido.

:::image type="content" source="../media/learned-component.png" alt-text="Captura de pantalla que muestra un ejemplo de componentes de aprendizaje para entidades." lightbox="../media/learned-component.png":::

### <a name="list-component"></a>Componente de lista

Un componente de lista representa un conjunto fijo y cerrado de palabras relacionadas y sus sinónimos. El componente realiza una búsqueda de coincidencia de texto exacta con respecto a la lista de valores que proporcionó como sinónimos. Cada sinónimo pertenece a una "clave de lista" que se puede usar como valor estándar normalizado para el sinónimo que se devolverá en la salida si el componente de lista encuentra una coincidencia. Las claves de lista **no** se usan para buscar coincidencias.


:::image type="content" source="../media/list-component.png" alt-text="Captura de pantalla que muestra un ejemplo de componentes de lista para entidades." lightbox="../media/list-component.png":::

### <a name="prebuilt-component"></a>Componente creado previamente

El componente creado previamente permite seleccionar valores de una biblioteca de tipos comunes, como números, fecha y hora, y nombres. Cuando se agrega, se detecta automáticamente un componente creado previamente. Puede tener hasta 5 componentes creados previamente por entidad. Para obtener más información, consulte [la lista de componentes creados previamente que se admiten](../prebuilt-component-reference.md).


:::image type="content" source="../media/prebuilt-component.png" alt-text="Captura de pantalla que muestra un ejemplo de componentes creados previamente para entidades." lightbox="../media/prebuilt-component.png":::


## <a name="overlap-methods"></a>Métodos de superposición

Cuando se definen varios componentes para una entidad, sus predicciones pueden superponerse. Cuando esto pasa, la predicción final de cada entidad se determina mediante una de las siguientes opciones.

### <a name="longest-overlap"></a>Superposición más larga

Cuando se encuentran dos o más componentes en el texto y se usa el método de superposición, se devuelve el componente con el **conjunto de caracteres más largo**.

Esta opción se recomienda cuando le interesa que los distintos componentes extraigan la predicción más larga posible. Este método garantiza que siempre que haya confusión (superposición), el componente devuelto será el más largo.

#### <a name="examples"></a>Ejemplos

Si el componente de lista encontró "Palm Beach" y el componente aprendido predijo "Extensión de Palm Beach", entonces se devuelve "**Extensión de Palm Beach**" porque es el conjunto de caracteres más largo en esta superposición.

:::image type="content" source="../media/return-longest-overlap-example-1.svg" alt-text="Captura de pantalla que muestra un ejemplo de resultados de superposición más larga de los componentes." lightbox="../media/return-longest-overlap-example-1.svg":::

Si el componente de lista encontró "Palm Beach" y el componente aprendido predijo "Extensión de Beach", entonces se devuelve "**Extensión de Beach**" porque es el componente con el conjunto de caracteres más largo en esta superposición.

:::image type="content" source="../media/return-longest-overlap-example-2.svg" alt-text="Captura de pantalla que muestra un segundo ejemplo de resultados de superposición más larga de los componentes." lightbox="../media/return-longest-overlap-example-2.svg":::

Si "Palm Beach" fue encontrado por el componente de lista y "Extensión" fue predicho por el componente aprendido, entonces se devuelven 2 instancias diferentes de las entidades, ya que no hay superposición entre ellas: una para "**Palm Beach**" y otra para "**Extensión**", ya que no se ha producido ningún solapamiento en esta instancia.

:::image type="content" source="../media/return-longest-overlap-example-3.svg" alt-text="Captura de pantalla que muestra un tercer ejemplo de resultados de superposición más larga de los componentes." lightbox="../media/return-longest-overlap-example-3.svg":::

### <a name="exact-overlap"></a>Superposición exacta

Todos los componentes deben superponerse exactamente con los **mismos caracteres** del texto para que se devuelva la entidad. Si uno de los componentes definidos no se encuentra ni se predice, la entidad no se devolverá.

Esta opción se recomienda cuando tiene una entidad estricta que debe tener varios componentes detectados al mismo tiempo para extraerse.

#### <a name="examples"></a>Ejemplos

Si el componente de lista encontró "Palm Beach" y el componente aprendido predijo "Palm Beach", y esos fueron los únicos 2 componentes definidos en la entidad, entonces se devuelve "**Palm Beach**" porque todos los componentes se superpusieron exactamente con los mismos caracteres.

:::image type="content" source="../media/require-exact-overlap-example-1.svg" alt-text="Captura de pantalla que muestra un ejemplo de resultados de superposición exacta de los componentes." lightbox="../media/require-exact-overlap-example-1.svg":::

Si el componente de lista encontró "Palm Beach" y el componente aprendido predijo "Extensión de Beach", entonces la entidad **no** se devuelve porque todos los componentes no se superpusieron exactamente con los mismos caracteres.

:::image type="content" source="../media/require-exact-overlap-example-2.svg" alt-text="Captura de pantalla que muestra un segundo ejemplo de resultados de superposición exacta de los componentes." lightbox="../media/require-exact-overlap-example-2.svg":::

Si el componente de lista encontró "Palm Beach" y el componente aprendido predijo "Extensión", entonces la entidad **no** se devuelve porque no se ha producido ninguna superposición en esta instancia.

:::image type="content" source="../media/require-exact-overlap-example-3.svg" alt-text="Captura de pantalla que muestra un tercer ejemplo de resultados de superposición exacta de los componentes." lightbox="../media/require-exact-overlap-example-3.svg":::

### <a name="union-overlap"></a>Superposición de unión

Cuando se encuentran dos o más componentes en el texto y se superponen, se devuelve la **unión** de los intervalos de los componentes.

Esta opción se recomienda cuando se optimiza la coincidencia y se intenta obtener la coincidencia más larga posible que se pueda combinar.

#### <a name="examples"></a>Ejemplos

Si el componente de lista encontró "Palm Beach" y el componente aprendido predijo "Extensión de Palm Beach", entonces se devuelve "**Extensión de Palm Beach**" porque el primer carácter al principio de la superposición es la "P" en "Palm", y la última letra al final de los componentes superpuestos es la "n" en "Extension".

:::image type="content" source="../media/return-union-example-1.svg" alt-text="Captura de pantalla que muestra un ejemplo de resultados de superposición de unión de los componentes." lightbox="../media/return-union-example-1.svg":::

Si el componente de lista encontró "Palm Beach" y el componente aprendido predijo "Extensión de Beach", entonces se devuelve "**Extensión de Palm Beach**" porque el primer carácter al principio de la superposición es la "P" en "Palm", y la última letra al final de los componentes superpuestos es la "n" en "Extension".

:::image type="content" source="../media/return-union-example-2.svg" alt-text="Captura de pantalla que muestra un segundo ejemplo de resultados de superposición de unión de los componentes." lightbox="../media/return-union-example-2.svg":::

Si el componente creado previamente predijo "Nueva York", el componente de lista encontró "York Beach" y el componente aprendido predijo "Extensión de Beach", entonces se devuelve "__**Extensión de Nueva York Beach**__" porque el primer carácter al principio de la superposición es la "N" en "Nueva" y la última letra al final de los componentes superpuestos es la "n" en "Extensión".

:::image type="content" source="../media/return-union-example-3.svg" alt-text="Captura de pantalla que muestra un tercer ejemplo de resultados de superposición de unión de los componentes." lightbox="../media/return-union-example-3.svg":::

### <a name="return-all-separately"></a>Devolver todo por separado

La coincidencia o predicción de cada componente se devuelve como una **instancia independiente** de la entidad.

Esta opción se recomienda cuando desea aplicar su propia lógica de superposición para la entidad después de la predicción.

#### <a name="examples"></a>Ejemplos

Si el componente de lista encontró "Palm Beach" y el componente aprendido predijo "Extensión de Palm Beach", entonces la entidad devuelve 2 instancias: una para "**Palm Beach**" y otra para "**Extensión de Palm Beach**".

:::image type="content" source="../media/return-all-overlaps-example-1.svg" alt-text="Captura de pantalla que muestra un ejemplo de devolución de todos los resultados de superposición de los componentes." lightbox="../media/return-all-overlaps-example-1.svg":::

Si el componente creado previamente predijo "Nueva York", el componente de lista encontró "York Beach" y el componente aprendido predijo "Extensión de Beach", entonces la entidad devuelve 3 instancias: una para "**Nueva York**", otra para "**York Beach**" y una última para "**Extensión de Beach**".

:::image type="content" source="../media/return-all-overlaps-example-2.svg" alt-text="Captura de pantalla que muestra un segundo ejemplo de devolución de todos los resultados de superposición de los componentes." lightbox="../media/return-all-overlaps-example-2.svg":::

## <a name="next-steps"></a>Pasos siguientes

[Componentes creados previamente que se admiten](../prebuilt-component-reference.md)

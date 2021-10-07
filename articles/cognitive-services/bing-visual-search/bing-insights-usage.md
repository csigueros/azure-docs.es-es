---
title: 'Ejemplos de información de Bing: Bing Visual Search'
titleSuffix: Azure Cognitive Services
description: Este artículo contiene ejemplos de cómo Bing Visual Search puede mostrar y usar información de imágenes en Bing.com.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: 51248cb5e37e6f109873340cca74c46e4af6a721
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128669671"
---
# <a name="examples-of-bing-insights-usage"></a>Ejemplos del uso de la información de Bing

> [!WARNING]
> Bing Search APIs se mueve de Cognitive Services a Bing Search Services. A partir del **30 de octubre de 2020**, las nuevas instancias de Bing Search deben aprovisionarse siguiendo el proceso documentado [aquí](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> El aprovisionamiento de Bing Search APIs con Cognitive Services será posible durante los próximos tres años o hasta que finalice el Contrato Enterprise, lo que suceda primero.
> Puede encontrar instrucciones sobre la migración en [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Este artículo contiene ejemplos de cómo Bing puede mostrar y usar información de imágenes en Bing.com.

## <a name="pagesincluding-insight-example"></a>Ejemplo de páginas que incluyen la información

En el ejemplo siguiente se muestra un vínculo a la primera página web y el usuario puede expandir y contraer la lista de otras páginas web que incluyen la imagen:

![Páginas expandidas con información](./media/pages-including.PNG)

## <a name="shoppingsources-insight-example"></a>Ejemplo orígenes de compra de la información

En la ilustración siguiente se indica cómo Bing podría mostrar orígenes de compras de productos que se muestran en la imagen:

![Orígenes de compra](./media/shopping-sources.PNG)

## <a name="visualsearch-insight-example"></a>Ejemplo de búsqueda visual de la información

Lo siguiente muestra cómo Bing podría mostrar imágenes visualmente similares (consulte las **imágenes relacionadas** en el ejemplo):

![Imágenes visualmente similares](./media/similar-images.PNG)

## <a name="recipes-insight-example"></a>Ejemplo de recetas de la información

Lo siguiente muestra cómo Bing podría mostrar recetas de los alimentos que se muestran en la imagen. El ejemplo permite al usuario saber qué recetas están disponibles:

![Recetas y páginas con información](./media/recipes-pages-including.PNG)

 Y proporciona el vínculo a las recetas cuando el usuario expande la lista:

![Páginas con recetas expandidas con información](./media/expanded-recipes-pages-including.PNG)

## <a name="relatedsearches-insight-example"></a>Ejemplo de búsquedas relacionadas con la información

Lo siguiente muestra cómo Bing puede mostrar búsquedas relacionadas de imágenes realizadas por otros. Si el usuario hace clic en la imagen, se le lleva a la página de resultados de búsqueda de imágenes de Bing.com/images para esa consulta relacionada.

![Búsquedas de imágenes relacionadas](./media/bordered-related-searches.PNG)

## <a name="entity-insight-example"></a>Ejemplo de entidades de información

Lo siguiente muestra cómo Bing puede mostrar información sobre la entidad (persona, lugar o cosa) mostrada en la imagen. Si el usuario hace clic en el vínculo de la entidad, el usuario accede a la página de resultados de búsqueda de Bing.com para la entidad:

![Entidad mostrada en la imagen](./media/entity.PNG)

## <a name="displaying-other-insights-that-the-user-might-explore"></a>Visualización de otra información que el usuario podría explorar

Lo siguiente explica cómo Bing puede mostrar otra información sobre la imagen que el usuario puede explorar.

![Exploración de otra información sobre la imagen](./media/apple-pie-more-tags.PNG)

## <a name="bounding-boxes-and-hot-spots"></a>Rectángulos delimitadores y zonas activas

Las etiquetas no predeterminadas incluyen el rectángulo delimitador que identifica el área de interés en la imagen a la que se aplica la etiqueta. Si el rectángulo delimitador no identifica toda la imagen, utilice este rectángulo para crear una zona activa en la imagen. El usuario puede hacer clic en la zona activa para obtener información relacionada con el contenido que se encuentra bajo dicha zona activa (o rectángulo). Por ejemplo, si la imagen es una imagen de moda, los resultados pueden contener etiquetas (y rectángulos delimitadores) para los accesorios mostrados en la imagen, como un bolso, joyas, bufandas, etc. El siguiente ejemplo muestra un rectángulo de zona activa para las gafas de sol que se muestran en la imagen:

![Rectángulo delimitador y zona activa](./media/click-to-search.PNG)

## <a name="next-steps"></a>Pasos siguientes

Para empezar a trabajar con la primera solicitud, consulte los artículos de inicio rápido:

* [C#](quickstarts/csharp.md)

* [Java](quickstarts/java.md)

* [node.js](quickstarts/nodejs.md)

* [Python](quickstarts/python.md)
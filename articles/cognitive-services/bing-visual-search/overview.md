---
title: ¿Qué es Bing Visual Search API?
titleSuffix: Azure Cognitive Services
description: Bing Visual Search muestra cómo obtener detalles o conclusiones sobre una imagen, como imágenes similares u orígenes de compras.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: overview
ms.date: 12/19/2019
ms.openlocfilehash: 8574a1272615cb72722e735373bc6609b2890d81
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128669519"
---
# <a name="what-is-the-bing-visual-search-api"></a>¿Qué es Bing Visual Search API?

> [!WARNING]
> Las Bing Search API se mueven de Cognitive Services a Bing Search Services. A partir del **30 de octubre de 2020**, las nuevas instancias de Bing Search deben aprovisionarse siguiendo el proceso documentado [aquí](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> El aprovisionamiento de Bing Search APIs con Cognitive Services será posible durante los próximos tres años o hasta que finalice el Contrato Enterprise, lo que suceda primero.
> Puede encontrar instrucciones sobre la migración en [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Bing Visual Search API devuelve información de una imagen. Puede cargar una imagen o especificar la dirección URL a una imagen. Entre la información devuelta puede haber imágenes visualmente similares, orígenes de compra, páginas web que incluyen la imagen y mucho más. La información devuelta por Bing Visual Search API es similar a la que se muestra en Bing.com/images. 

Si usa [Bing Image Search API](../bing-image-search/overview.md), puede utilizar tokens de información detallada de los resultados de búsqueda de la API para Bing Visual Search en lugar de cargar una imagen.

> [!IMPORTANT]
> Si obtiene información detallada de imágenes con Bing Image Search API, considere la posibilidad de cambiar a Bing Visual Search API, que proporciona información más completa.

## <a name="insights"></a>Información detallada

Mediante el uso de Bing Visual Search puede obtener la información siguiente:

| Conclusión                              | Descripción |
|--------------------------------------|-------------|
| Imágenes visualmente similares              | Lista de imágenes que son visualmente similares a la imagen de entrada. |
| Productos visualmente similares            | Productos que son visualmente similares al producto mostrado.            |
| Orígenes de compra                     | Lugares en los que puede comprar el artículo que se muestra en la imagen de entrada.            |
| Búsquedas relacionadas                     | Búsquedas relacionadas realizadas por otros usuarios o que se basan en el contenido de la imagen.            |
| Páginas web que incluyen la imagen     | Páginas web que incluyen la imagen de entrada.            |
| Recetas                              | Páginas web que incluyen recetas para hacer el plato que se muestra en la imagen de entrada.            |
| Entidades                             | Cosas, personas y lugares conocidos. |

Además de esta información, Bing Visual Search devuelve diversos términos (es decir, etiquetas) derivados de la imagen de entrada. Estas etiquetas permiten a los usuarios explorar los conceptos que se encuentran en la imagen. Por ejemplo, si la imagen de entrada es un deportista famoso, una de las etiquetas podría ser el nombre del deportista y otra, Deportes. O bien, si la imagen de entrada es un pastel de manzana, las etiquetas podrían ser Pastel de manzana, Pasteles y Postres.

Los resultados de Bing Visual Search también incluyen rectángulos de selección para las regiones de interés de la imagen. Por ejemplo, si la imagen contiene varias celebridades, los resultados pueden incluir rectángulos de selección para cada una de las celebridades reconocidas. O bien, si Bing reconoce un producto o prenda en la imagen, el resultado puede incluir un rectángulo de selección para el objeto que se ha reconocido.

## <a name="workflow"></a>Flujo de trabajo

Bing Visual Search API es un servicio web RESTful, por lo que resulta muy fácil llamarlo desde cualquier lenguaje de programación que pueda realizar solicitudes HTTP y analizar código JSON. Puede la API REST o el SDK para el servicio.

1. Cree una [cuenta de Cognitive Services](../cognitive-services-apis-create-account.md) para acceder a las API de Bing Search. Si no tiene una suscripción de Azure, puede [crear una cuenta gratuita](https://azure.microsoft.com/free/cognitive-services/).
2. Envíe una solicitud a la API con una consulta de búsqueda válida.
3. Analice el mensaje JSON devuelto para procesar la respuesta de API.

## <a name="next-steps"></a>Pasos siguientes

Primero, pruebe la [demostración interactiva](https://azure.microsoft.com/services/cognitive-services/bing-visual-search/) de Bing Visual Search API.
Esta demostración muestra cómo puede personalizar rápidamente una consulta de búsqueda y explorar la Web en busca de imágenes.

Para empezar a trabajar rápidamente con la primera solicitud, consulte los artículos de inicio rápido:

* [C#](quickstarts/csharp.md)

* [Java](quickstarts/java.md)

* [node.js](quickstarts/nodejs.md)

* [Python](quickstarts/python.md)

## <a name="see-also"></a>Consulte también

* La referencia de [Images - Visual Search](/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch) (Imágenes: búsqueda visual) describe definiciones e información sobre los puntos de conexión, los encabezados de solicitud, las respuestas y los parámetros de consulta que puede usar para solicitar resultados de búsqueda basados en imágenes.

* En los [requisitos de uso y visualización de Bing Search API](../bing-web-search/use-display-requirements.md) se especifican usos aceptables del contenido y la información adquirida mediante las API de Bing Search.

* Visite la [página central de Bing Search API](../bing-web-search/overview.md) para explorar las otras API disponibles.
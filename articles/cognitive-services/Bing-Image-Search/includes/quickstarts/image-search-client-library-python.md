---
title: Inicio rápido de la biblioteca cliente para Python de Bing Image Search
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/04/2020
ms.author: aahi
ms.openlocfilehash: c9dd8bdfb9af1d20433a083b48ee1b7e14658438
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131253674"
---
Use este inicio rápido para buscar su primera imagen mediante la biblioteca cliente de Bing Image Search, un contenedor de la API y que contiene las mismas características. Esta sencilla aplicación de Python envía una consulta de búsqueda de imagen, analiza la respuesta de JSON y muestra la dirección URL de la primera imagen devuelta.

El código fuente de este ejemplo está disponible en [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/image-search-quickstart.py) con anotaciones y control de errores adicionales.

## <a name="prerequisites"></a>Prerrequisitos

* [Python 2.7 o 3.6 y superiores](https://www.python.org/)

* La [biblioteca cliente de Azure Image Search](https://pypi.org/project/azure-cognitiveservices-search-imagesearch/) para Python
    * Instalación mediante `pip install azure-cognitiveservices-search-imagesearch`

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](~/includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Creación e inicialización de la aplicación

1. Cree un script de Python en su IDE o editor favorito y realice las siguientes importaciones:

    ```python
    from azure.cognitiveservices.search.imagesearch import ImageSearchClient
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Cree variables para el término de búsqueda y la clave de suscripción.

    ```python
    subscription_key = "Enter your key here"
    subscription_endpoint = "Enter your endpoint here"
    search_term = "canadian rockies"
    ```

## <a name="create-the-image-search-client"></a>Creación del cliente de Image Search

1. Cree una instancia de `CognitiveServicesCredentials` y úsela para crear una instancia del cliente:

    ```python
    client = ImageSearchClient(endpoint=subscription_endpoint, credentials=CognitiveServicesCredentials(subscription_key))
    ```

1. Envíe una consulta de búsqueda a Bing Image Search API:

    ```python
    image_results = client.images.search(query=search_term)
    ```

## <a name="process-and-view-the-results"></a>Procese y visualice los resultados

Analice los resultados de la imagen devueltos en la respuesta.

Si la respuesta contiene resultados de la búsqueda, almacene el primer resultado e imprima sus detalles, como la dirección URL de una miniatura, la dirección URL original, además del número total de imágenes devueltas.  

```python
if image_results.value:
    first_image_result = image_results.value[0]
    print("Total number of images returned: {}".format(len(image_results.value)))
    print("First image thumbnail url: {}".format(
        first_image_result.thumbnail_url))
    print("First image content url: {}".format(first_image_result.content_url))
else:
    print("No image results returned!")
```

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Tutorial de aplicación de una sola página de Bing Image Search](../../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Consulte también

* [¿Qué es Bing Image Search?](../../overview.md)  
* [Prueba de una demostración interactiva en línea](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Ejemplos de Python para el SDK de Azure Cognitive Services](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)  
* [Documentación de Azure Cognitive Services](../../../index.yml)
* [Referencia de Bing Image Search API](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
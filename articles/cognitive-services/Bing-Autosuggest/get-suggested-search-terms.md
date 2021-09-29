---
title: ¿Qué es Bing Autosuggest?
titleSuffix: Azure Cognitive Services
description: Bing Autosuggest API devuelve una lista de consultas sugeridas según la cadena de consulta parcial del cuadro de búsqueda.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: overview
ms.date: 12/18/2019
ms.openlocfilehash: f4df9d3c8cb17561cb822b2bd1ac43396470af52
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128618768"
---
# <a name="what-is-bing-autosuggest"></a>¿Qué es Bing Autosuggest?

> [!WARNING]
> Las Bing Search API se mueven de Cognitive Services a Bing Search Services. A partir del **30 de octubre de 2020**, las nuevas instancias de Bing Search deben aprovisionarse siguiendo el proceso documentado [aquí](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> El aprovisionamiento de Bing Search APIs con Cognitive Services será posible durante los próximos tres años o hasta que finalice el Contrato Enterprise, lo que suceda primero.
> Puede encontrar instrucciones sobre la migración en [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Si una aplicación envía consultas a cualquier Bing Search API, puede usar Bing Autosuggest API para mejorar su experiencia de búsqueda. Bing Autosuggest API devuelve una lista de consultas sugeridas según la cadena de consulta parcial del cuadro de búsqueda. Como los caracteres se escriben en el cuadro de búsqueda, puede mostrar sugerencias en una lista desplegable.

## <a name="bing-autosuggest-api-features"></a>Características de Bing Autosuggest API

| Característica                                                                                                                                                                                 | Descripción                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Sugerencia de términos de búsqueda en tiempo real](concepts/get-suggestions.md) | Mejore su experiencia con las aplicaciones mediante Autosuggest API para mostrar términos de búsqueda sugeridos a medida que se escriben. |

## <a name="workflow"></a>Flujo de trabajo

Bing Autosuggest API es un servicio web RESTful, fácil de llamar desde cualquier lenguaje de programación que pueda realizar solicitudes HTTP y analizar JSON.

1. Cree una [cuenta de API de Cognitive Services](../cognitive-services-apis-create-account.md) con acceso a Bing Search APIs. Si no tiene una suscripción de Azure, puede [crear una cuenta](https://azure.microsoft.com/free/cognitive-services/) gratuita.
2. Envíe una solicitud a esta API cada vez que un usuario escriba un carácter en el cuadro de búsqueda de la aplicación.
3. Analice el mensaje JSON devuelto para procesar la respuesta de API.

Generalmente, llamaría a esta API cada vez que un usuario escriba un carácter en el cuadro de búsqueda de la aplicación. A medida que se escriben más caracteres, la API devolverá las consultas de búsqueda sugeridas más relevantes. Por ejemplo, las sugerencias que la API podría devolver para un único `s` suelen ser menos pertinentes que para `sail`.

El ejemplo siguiente muestra un cuadro de búsqueda desplegable con los términos de consulta sugeridos de Bing Autosuggest API.

![Lista del cuadro de búsqueda desplegable de AutoSuggest](./media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

Cuando un usuario selecciona una sugerencia en la lista desplegable, puede usarla para comenzar la búsqueda con una de las Bing Search APIs o ir directamente a la página de resultados de búsqueda de Bing.

## <a name="next-steps"></a>Pasos siguientes

Para comenzar rápidamente con su primera solicitud, consulte [Making Your First Query](quickstarts/csharp.md) (Realizar su primera consulta).

Familiarícese con la referencia de [Bing Autosuggest API v7](/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference). La referencia contiene la lista de puntos de conexión, encabezados y parámetros de consulta que se usan para solicitar términos de consulta sugeridos y las definiciones de los objetos de la respuesta.

Visite la [página central de Bing Search API](../bing-web-search/overview.md) para explorar las otras API disponibles.


Aprenda a realizar búsquedas en la web mediante [Bing Web Search API](../bing-web-search/overview.md) y explore las demás [API de Bing Search](../bing-web-search/index.yml).

No olvide leer los [Requisitos de visualización y uso de Bing](../bing-web-search/use-display-requirements.md) para evitar infringir alguna de las reglas sobre el uso de los resultados de búsqueda.
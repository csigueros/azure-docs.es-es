---
title: Directivas de streaming en Azure Media Services
description: En este artículo se explica qué son las directivas de streaming y cómo las usa Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: inhenkel
ms.openlocfilehash: c6cd9c5c56f261bce4c04f6e441023c1b1457bbc
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/21/2021
ms.locfileid: "122634225"
---
# <a name="streaming-policies"></a>Directivas de streaming

En Azure Media Services v3, las [directivas de streaming](/rest/api/media/streamingpolicies) permiten definir los protocolos de streaming y las opciones de cifrado de los [localizadores de streaming](stream-streaming-locators-concept.md). Media Services v3 proporciona algunas directivas de streaming predefinidas para que se pueden usar directamente en la versión de prueba o en producción. 

Las directivas de streaming predefinidas que están disponibles actualmente son:<br/>
* 'Predefined_DownloadOnly'
* 'Predefined_ClearStreamingOnly'
* 'Predefined_DownloadAndClearStreaming'
* 'Predefined_ClearKey'
* 'Predefined_MultiDrmCencStreaming' 
* 'Predefined_MultiDrmStreaming'

El siguiente árbol de decisión le ayudará a elegir una directiva predefinida de streaming para su escenario.

> [!IMPORTANT]
> * Las propiedades de **directivas de streaming** que son del tipo Datetime siempre están en formato UTC.
> * Debería diseñar un conjunto limitado de directivas para su cuenta de Media Service y reutilizarlas con los objetos StreamingLocator siempre que se necesiten las mismas opciones. Para obtener más información, consulte [Cuotas y límites](limits-quotas-constraints-reference.md).

## <a name="decision-tree"></a>Árbol de decisión

Haga clic en la imagen para verla a tamaño completo.  

[![Diagrama que muestra un árbol de decisión diseñado para ayudarle a elegir una directiva predefinida de streaming para su escenario.](./media/streaming-policy/large.png)](./media/streaming-policy/large.png#lightbox)

Si cifra el contenido, deberá crear una [directiva de clave de contenido](drm-content-key-policy-concept.md); la **directiva de clave de contenido** no es necesaria para la descarga o el streaming sin cifrar. 

Si tiene requisitos especiales (por ejemplo, si desea especificar protocolos diferentes, tiene que usar un servicio de entrega de claves personalizadas o una pista de audio sin cifrar), puede [crear](/rest/api/media/streamingpolicies/create) una directiva personalizada de streaming. 

## <a name="get-a-streaming-policy-definition"></a>Obtención de la definición de una directiva de streaming  

Si desea ver la definición de una directiva de streaming, utilice [Get](/rest/api/media/streamingpolicies/get) y especifique el nombre de la directiva. Por ejemplo:

### <a name="rest"></a>REST

Solicitud:

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Media/mediaServices/contosomedia/streamingPolicies/clearStreamingPolicy?api-version=2018-07-01
```

Respuesta:

```
{
  "name": "clearStreamingPolicy",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Media/mediaservices/contosomedia/streamingPolicies/clearStreamingPolicy",
  "type": "Microsoft.Media/mediaservices/streamingPolicies",
  "properties": {
    "created": "2018-08-08T18:29:30.8501486Z",
    "noEncryption": {
      "enabledProtocols": {
        "download": true,
        "dash": true,
        "hls": true,
        "smoothStreaming": true
      }
    }
  }
}
```

## <a name="filtering-ordering-paging"></a>Filtrado, ordenación, paginación

Consulte [Filtrado, ordenación y paginación de entidades de Media Services](filter-order-page-entities-how-to.md).

## <a name="next-steps"></a>Pasos siguientes

* [Streaming de un archivo](stream-files-dotnet-quickstart.md)
* [Uso del cifrado dinámico AES-128 y el servicio de entrega de claves](drm-playready-license-template-concept.md)
* [Uso del cifrado dinámico de DRM y el servicio de entrega de licencias](drm-protect-with-drm-tutorial.md)

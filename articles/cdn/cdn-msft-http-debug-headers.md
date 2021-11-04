---
title: Encabezados HTTP de depuración para Azure CDN de Microsoft | Microsoft Docs
description: Los encabezados de solicitud de caché de depuración proporciona información adicional sobre la directiva de caché aplicada al recurso solicitado. Estos encabezados son específicos para Azure CDN de Microsoft.
services: cdn
documentationcenter: ''
author: duongau
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2019
ms.author: duau
ms.openlocfilehash: 5253d700f271ed18f6e1eae5488184da8ed6db7f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131450559"
---
# <a name="debug-http-header-for-azure-cdn-from-microsoft"></a>Encabezados HTTP de depuración para Azure CDN de Microsoft
El encabezado de respuesta de depuración, `X-Cache`, brinda detalles sobre la capa de la pila de CDN desde donde se sirvió el contenido. Este encabezado es específico para Azure CDN de Microsoft.

### <a name="response-header-format"></a>Formato del encabezado de respuesta

Header | Descripción
-------|------------
X-Cache: TCP_HIT | Este encabezado se devuelve cuando el contenido se sirve desde la caché perimetral de CDN. 
X-Cache: TCP_REMOTE_HIT | Este encabezado se devuelve cuando el contenido se sirve desde la caché regional de CDN (capa de escudo de origen).
X-Cache: TCP_MISS | Este encabezado se devuelve cuando se produce un error de caché y el contenido se sirve desde el origen.
X-Cache: PRIVATE_NOSTORE | Este encabezado se devuelve cuando la solicitud no se puede almacenar en caché, porque el encabezado de respuesta Cache-Control está establecido en privado o sin almacén.
X-Cache: CONFIG_NOCACHE | Este encabezado se devuelve cuando la solicitud está configurada para no almacenarse en caché en el perfil de CDN.
X-Cache: N/A | Este encabezado se devuelve cuando la solicitud ha sido denegada por la dirección URL firmada y el conjunto de reglas.

Para información adicional sobre los encabezados HTTP admitidos en Azure CDN, consulte [De Front Door a back-end](../frontdoor/front-door-http-headers-protocol.md#front-door-to-backend).

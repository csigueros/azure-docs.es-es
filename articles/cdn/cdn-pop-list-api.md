---
title: Recuperación de la lista de direcciones IP POP actual para Azure CDN| Microsoft Docs
description: Obtenga información sobre cómo obtener servidores POP mediante la API REST. Los servidores POP realizan solicitudes a los servidores de origen asociados a los puntos de conexión de Azure Content Delivery Network.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2021
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: 65bb0649f5fed3615887d750b95d2b4ce91216fb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128626064"
---
# <a name="retrieve-the-current-pop-ip-list-for-azure-cdn"></a>Recuperación de la lista de direcciones IP POP para Azure CDN

## <a name="retrieve-the-current-verizon-pop-ip-list-for-azure-cdn"></a>Recuperación de la lista de direcciones IP POP de Verizon para Azure CDN

Puede utilizar la API REST para recuperar el conjunto de direcciones IP de los servidores de punto de presencia (POP) de Verizon. Estos servidores de POP realizan solicitudes a los servidores de origen que están asociados a los puntos de conexión de Azure Content Delivery Network (CDN) en un perfil de Verizon (**Azure CDN de Verizon estándar** o **Azure CDN de Verizon premium**). Tenga en cuenta que este conjunto de direcciones IP es diferente de las direcciones IP que un cliente vería al realizar solicitudes a los POP. 

Para obtener la sintaxis de la operación de API REST para recuperar la lista de POP, consulte [Edge Nodes - List](/rest/api/cdn/edge-nodes/list) (Nodos de Edge: lista).

## <a name="retrieve-the-current-microsoft-pop-ip-list-for-azure-cdn"></a>Recuperación de la lista de direcciones IP POP actual de Microsoft para Azure CDN

Para bloquear la aplicación y que solo acepte el tráfico de Azure CDN de Microsoft, deberá configurar las ACL de IP para el back-end. También puede restringir el conjunto de valores aceptados para el encabezado "X-Forwarded-Host" enviado por Azure CDN de Microsoft. Estos pasos se detallan a continuación:

Configure la creación de listas de control de acceso de IP de los back-end para que acepten tráfico de Azure CDN solo del espacio de direcciones IP de back-end de Microsoft y de los servicios de infraestructura de Azure. 

Use [etiquetas de servicio](../virtual-network/service-tags-overview.md) de Azure Front Door con Azure CDN de Microsoft para configurar intervalos IP de back-end de Microsoft. Para ver una lista completa, consulte los [intervalos IP y las etiquetas de servicio](https://www.microsoft.com/en-us/download/details.aspx?id=56519) de los servicios de Microsoft.

## <a name="typical-use-case"></a>Caso de uso típico

Por motivos de seguridad, puede usar esta lista de direcciones IP para exigir que las solicitudes a su servidor de origen se realicen solo desde un POP de Verizon válido. Por ejemplo, si alguien detectase el nombre de host o la dirección IP de un servidor de origen del punto de conexión de CDN, se podrían realizar las solicitudes directamente al servidor de origen, por lo que se omitirían las funcionalidades de seguridad y escalado proporcionadas por Azure CDN. El establecimiento de las direcciones IP en la lista devuelta como las únicas permitidas en un servidor de origen puede evitar esta situación. Para garantizar que cuenta con la lista de POP más reciente, recupérela al menos una vez al día. 

## <a name="next-steps"></a>Pasos siguientes

Para más información acerca de la API REST, consulte [Azure CDN REST API](/rest/api/cdn/) (API REST de Azure CDN).

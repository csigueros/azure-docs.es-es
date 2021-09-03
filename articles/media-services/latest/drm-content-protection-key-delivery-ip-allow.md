---
title: Restricción del acceso a la entrega de licencias de DRM y claves AES mediante listas de direcciones IP permitidas
description: Obtenga información sobre cómo restringir el acceso a DRM y claves AES mediante listas de direcciones IP permitidas.
services: media-services
documentationcenter: ''
author: johndeu
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/11/2021
ms.author: johndeu
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: cc0a35c196956d1f6afaf3d3fb27b9f428bb9f6d
ms.sourcegitcommit: e1874bb73cb669ce1e5203ec0a3777024c23a486
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/16/2021
ms.locfileid: "112204858"
---
# <a name="restrict-access-to-drm-license-and-aes-key-delivery-using-ip-allowlists"></a>Restricción del acceso a la entrega de licencias de DRM y claves AES mediante listas de direcciones IP permitidas

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Al proteger los medios con las características de DRM y [protección de contenido](./drm-content-protection-concept.md) de Media Services, podría encontrar escenarios en los que necesita limitar la entrega de licencias o solicitudes de clave a un intervalo IP concreto de dispositivos cliente de la red. Para restringir la reproducción de contenido y la entrega de claves, puede usar la lista de direcciones IP permitidas para la entrega de claves.

Además, también puede usar esta lista para bloquear completamente todo el acceso público a Internet al tráfico de entrega de claves y solo permitir el que proceda de los puntos de conexión de red privados.

La lista de direcciones IP permitidas para la entrega de claves restringe la entrega de licencias DRM y claves AES-128 a los clientes dentro del intervalo de IP permitidas proporcionado.

## <a name="setting-the-allowlist-for-key-delivery"></a>Establecimiento de la lista de permitidos para la entrega de claves

La configuración de la lista de direcciones IP permitidas para la entrega de claves se encuentra en el recurso de cuenta de Media Services. Al crear una cuenta de Media Services, puede restringir los intervalos IP permitidos por medio de la propiedad **KeyDelivery** del [recurso de cuenta de Media Services.](/rest/api/media/mediaservices/create-or-update)

La propiedad **defaultAction** se puede establecer en "Permitir" o "Denegar" para controlar la entrega de licencias y claves a los clientes en el intervalo de lista de permitidos.

La propiedad **ipAllowList** es una matriz de direcciones IPv4 únicas o intervalos IPv4 en la que se usa la [notación CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#CIDR_notation).

## <a name="setting-the-allowlist-in-the-portal"></a>Establecimiento de la lista de permitidos en el portal

En Azure Portal se proporciona un método para configurar y actualizar la lista de direcciones IP permitidas para la entrega de claves.  Vaya a la cuenta de Media Services y acceda al menú **Entrega de claves** en **Configuración**.

## <a name="next-steps"></a>Pasos siguientes

- [Crear una cuenta](./account-create-how-to.md)
- [Introducción a DRM y la protección de contenido](./drm-content-protection-concept.md)

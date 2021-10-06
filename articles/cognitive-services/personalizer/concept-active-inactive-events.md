---
title: 'Eventos activos e inactivos: Personalizer'
description: En este artículo se describe el uso de los eventos activos e inactivos en el servicio de Personalizer.
author: jeffmend
ms.author: jeffme
ms.manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 948d375c0f580a71dbd27fa10660c0c7e0046a10
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129219161"
---
# <a name="defer-event-activation"></a>Aplazamiento de la activación de eventos

La activación aplazada de eventos le permite crear sitios web personalizados o campañas de correo electrónico, teniendo en cuenta que el usuario puede que nunca vea realmente la página o abra el correo electrónico. En estos escenarios, es posible que la aplicación tenga que llamar a Rank, incluso antes de saber si el resultado se va a usar o a mostrar al usuario. Si el contenido nunca se muestra al usuario, no se debe suponer ningún premio predeterminado (normalmente cero) para que aprenda.
La activación aplazada le permite usar los resultados de una llamada a Rank en un momento dado y decidir si el evento se debe aprender más adelante o en otra parte del código.

## <a name="typical-scenarios-for-deferred-activation"></a>Escenarios típicos de activación aplazada

Aplazar la activación de eventos es útil en los siguientes escenarios de ejemplo:

* Está representando previamente una página web personalizada para un usuario, pero es posible que el usuario nunca llegue a verla porque alguna lógica de negocios puede invalidar la opción de acción de Personalizer.
* Está personalizando el contenido "below the fold" (parte inferior) en una página web y es muy posible que el usuario nunca vea el contenido.
* Está personalizando los correos electrónicos de marketing y debe evitar el entrenamiento de correos electrónicos que nunca han abierto los usuarios.
* Ha personalizado un canal multimedia dinámico y los usuarios pueden dejar de reproducir el canal antes de que llegue a las canciones o vídeos seleccionados por Personalizer. 

En términos generales, estos escenarios se producen cuando:

* Está representando previamente la interfaz de usuario que el usuario podría ver o no debido a restricciones de tiempo o de la interfaz de usuario.
* La aplicación realiza la personalización predictiva en la que realiza llamadas Rank antes de saber si va a usar la salida.

## <a name="how-to-defer-activation-and-later-activate-events"></a>Aplazamiento de la activación de eventos y su posterior activación

Para aplazar la activación de un evento, llame a [Rank](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank) con `deferActivation = True` en el cuerpo de la solicitud.

En cuanto sepa que a los usuarios se les ha mostrado el contenido o los elementos multimedia personalizados, y que es razonable que esperen un premio, debe activar ese evento. Para ello, llame a la [API Activate](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Activate) con eventId.


La llamada a la [API Activate](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Activate) para esa llamada a EventID debe recibirse antes de que expire la ventana tiempo de espera de premio.

### <a name="behavior-with-deferred-activation"></a>Comportamiento con activación aplazada 

Personalizer aprenderá de los eventos y premios como se muestra a continuación:
* Si llama a Rank con `deferActivation = True` y *no llama* a la API `Activate` para ese eventId y llama a Reward, Personalizer no aprende del evento.
* Si llama a Rank con `deferActivation = True` y *llama* a la API `Activate` para ese eventId y llama a Reward, Personalizer aprenderá del evento con la puntuación de premio especificada.
* Si llama a Rank con `deferActivation = True` y *llama* a la API `Activate` para ese eventId, pero omite llamar a Reward, Personalizer aprenderá del evento con la puntuación de premio predeterminado establecida en la configuración.

## <a name="next-steps"></a>Pasos siguientes
* Configuración de [premios predeterminados](how-to-settings.md#configure-rewards-for-the-feedback-loop).
* Aprenda [cómo determinar la puntuación de recompensa y qué datos se deben tener en cuenta](concept-rewards.md).

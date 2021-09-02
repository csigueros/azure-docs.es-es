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
ms.openlocfilehash: ba95c22a773a382a3c03aab18f8f885e6a2791d8
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122831013"
---
# <a name="active-and-inactive-events"></a>Eventos activos e inactivos

Un evento **activo** es cualquier llamada al rango donde sepa que va a mostrar el resultado al cliente y determine la puntuación de recompensa. Este es el comportamiento predeterminado.

Un evento **inactivo** es una llamada al rango donde no está seguro de si el usuario verá la acción recomendada, debido a la lógica de negocios. Esto le permite descartar el evento para que Personalizer no esté entrenado con la recompensa predeterminada. Los eventos inactivos no deben llamar a la API Reward.

Es importante que el bucle de aprendizaje conozca el tipo de evento real. Un evento inactivo no tendrá una llamada de recompensa. Un evento activo debe tener una llamada de recompensa, pero si la llamada a la API no se realiza, se aplica la puntuación de recompensa predeterminada. Cambie el estado de un evento de inactivo a activo en cuanto sepa que va a influir en la experiencia del usuario.

## <a name="typical-active-events-scenario"></a>Escenario típico de eventos activos

Cuando la aplicación llama a API Rank, recibe la acción que la aplicación debe mostrar en el campo **rewardActionId**.  A partir de ese momento, Personalizer espera una llamada de recompensa con la misma puntuación de recompensa que tiene el mismo identificador de evento. La puntuación de recompensa se usará para entrenar el modelo para las llamadas futuras al rango. Si no se recibe ninguna llamada de recompensa para el eventId, se aplica una recompensa predeterminada. Las [recompensas predeterminadas](how-to-settings.md#configure-rewards-for-the-feedback-loop) se establecen en el recurso de Personalizer en Azure Portal.

## <a name="other-event-type-scenarios"></a>Otros escenarios de tipos de eventos

En algunos escenarios, es posible que la aplicación tenga que llamar a Rank, incluso antes de saber si el resultado se va a usar o a mostrar al usuario. Esto puede ocurrir en situaciones donde, por ejemplo, la representación de la página del contenido promocionado se sobrescribe con una campaña de marketing. Si el resultado de la llamada a Rank no se usó nunca y el usuario nunca lo vio, no envíe la llamada de recompensa correspondiente.

Normalmente, estos escenarios se producen cuando:

* Está preprocesando la interfaz de usuario que el usuario podría o no ver.
* La aplicación está haciendo una personalización predictiva en la que las llamadas de Rank se realizan con poco contexto en tiempo real y la aplicación puede utilizar o no la salida.

En estos casos, use Personalizer para llamar a Rank solicitando al evento que esté _inactivo_. Personalizer no esperará una recompensa para este evento y no aplicará ninguna predeterminada.

Después, en la lógica de negocios, si la aplicación usa la información de la llamada a Rank, lo único que debe hacer es _activar_ el evento. En cuanto el evento está activo, Personalizer espera una recompensa de evento. Si no se realiza ninguna llamada explícita a la API Rank, Personalizer aplica una recompensa predeterminada.

## <a name="inactive-events"></a>Eventos inactivos

Para deshabilitar el entrenamiento de un evento, llame a Rank mediante `learningEnabled = False`.

Para un evento inactivo, el aprendizaje se activa implícitamente si envía una recompensa para el eventId o si llama a la API `activate` para dicho eventId.

## <a name="next-steps"></a>Pasos siguientes

* Aprenda [cómo determinar la puntuación de recompensa y qué datos se deben tener en cuenta](concept-rewards.md).

---
title: Funcionamiento de Personalizer (Personalizer)
description: El _bucle_ de Personalizer utiliza el aprendizaje automático para compilar el modelo que predice la acción superior para su contenido. El modelo se entrena exclusivamente con los datos que usted le envía con las llamadas a Rank y Reward.
author: jeffmend
ms.author: jeffme
ms.manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 9c9d9f2b06a34feca2974bf97fb5d494c6105471
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829933"
---
# <a name="how-personalizer-works"></a>Funcionamiento de Personalizer

El recurso de Personalizer, su _bucle de aprendizaje_, utiliza el aprendizaje automático para compilar el modelo que predice la acción superior para su contenido. El modelo se entrena exclusivamente con los datos que usted le envía con las llamadas a **Rank** y **Reward**. Todos los bucles son completamente independientes entre sí.

## <a name="rank-and-reward-apis-impact-the-model"></a>Las API Rank y Reward afectan al modelo

Usted envía _acciones con características_ y _características de contexto_ a la API Rank. La API **Rank** decide usar:

* _Vulnerabilidad de seguridad_: el modelo actual para decidir la mejor acción según los datos anteriores.
* _Explorar_: seleccione una acción diferente, en lugar de la acción superior. Usted [configura este porcentaje](how-to-settings.md#configure-exploration-to-allow-the-learning-loop-to-adapt) para el recurso de Personalizer en Azure Portal.

Determina la puntuación de la recompensa y envía esa puntuación a la API Reward. La API **Reward**:

* Recopila datos para entrenar el modelo mediante la grabación de las características y las puntuaciones de recompensa de cada llamada a Rank.
* Utiliza esos datos para actualizar el modelo, en función de la configuración especificada en la _directiva de aprendizaje_.

## <a name="your-system-calling-personalizer"></a>Llamada del sistema a Personalizer

La siguiente imagen muestra el flujo arquitectónico de realizar las llamadas a Rank y Reward:

![texto alternativo](./media/how-personalizer-works/personalization-how-it-works.png "Funcionamiento de la personalización")

1. Usted envía _acciones con características_ y _características de contexto_ a la API Rank.

    * Personalizer decide si va a explorar el modelo actual o a explorar nuevas opciones para el modelo.
    * El resultado de la clasificación se envía a EventHub.
1. El rango superior se devuelve al sistema como _Id. de la acción de recompensa_.
    El sistema presenta ese contenido y determina una puntuación de recompensa en función de las reglas de negocio propias.
1. El sistema devuelve la puntuación de recompensa al bucle de aprendizaje.
    * Cuando Personalizer recibe la recompensa, esta se envía a EventHub.
    * La clasificación y la recompensa están correlacionadas.
    * El modelo de inteligencia artificial se actualiza en función de los resultados de la correlación.
    * El motor de inferencia se actualiza con el nuevo modelo.

## <a name="personalizer-retrains-your-model"></a>Personalizer vuelve a entrenar el modelo

Personalizer vuelve a entrenar el modelo en función del valor de **Frecuencia de actualización del modelo** del recurso de Personalizer de Azure Portal.

Personalizer usa todos los datos que se conservan actualmente, en función del valor de **Retención de datos** en el número de días en el recurso de Personalizer de Azure Portal.

## <a name="research-behind-personalizer"></a>La investigación que hay detrás de Personalizer

Personalizer se basa en una ciencia e investigación vanguardistas en el área del [aprendizaje de refuerzo](concepts-reinforcement-learning.md) que incluyen documentos, actividades de investigación o áreas de exploración en curso de Microsoft Research.

## <a name="next-steps"></a>Pasos siguientes

Más información sobre los [escenarios principales](where-can-you-use-personalizer.md) para el Personalizer
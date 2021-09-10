---
title: 'Puntuación de recompensa: Personalizer'
description: La puntuación de recompensa indica el resultado que tuvo la elección de personalización, RewardActionID, para el usuario. El valor de la puntuación de recompensa viene determinado por la lógica de negocios, según las observaciones del comportamiento del usuario. Personalizer entrena sus modelos de Machine Learning mediante la evaluación de las recompensas.
author: jeffmend
ms.author: jeffme
ms.manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.date: 02/20/2020
ms.topic: conceptual
ms.openlocfilehash: ec2c9ba7e700a79cffc70cfd4eab7ea780527cff
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122867044"
---
# <a name="reward-scores-indicate-success-of-personalization"></a>Las puntuaciones de recompensa reflejan el éxito de la personalización.

La puntuación de recompensa indica el resultado que tuvo la elección de personalización, [RewardActionID](/rest/api/personalizer/1.0/rank/rank#response), para el usuario. El valor de la puntuación de recompensa viene determinado por la lógica de negocios, según las observaciones del comportamiento del usuario.

Personalizer entrena sus modelos de Machine Learning mediante la evaluación de las recompensas.

Aprenda a [configurar](how-to-settings.md#configure-rewards-for-the-feedback-loop) la puntuación de recompensa predeterminada en Azure Portal para el recurso Personalizer.

## <a name="use-reward-api-to-send-reward-score-to-personalizer"></a>Uso de Reward API para enviar la puntuación de recompensa a Personalizer

Las recompensas se envían a Personalizer mediante [Reward API](/rest/api/personalizer/1.0/events/reward). Normalmente, una recompensa es un número comprendido entre 0 y 1. Una recompensa negativa, con el valor de -1, es posible en ciertos escenarios y solo debe usarse si tiene experiencia con el aprendizaje de refuerzo (RL). Personalizer entrena el modelo para lograr la suma más alta posible de recompensas a la largo del tiempo.

Las recompensas se envían después de que se haya producido el comportamiento del usuario, lo que podría ser días más tarde. El tiempo máximo que esperará Personalizer hasta que se considere que un evento no tiene ninguna recompensa o una recompensa predeterminada se configura con el [Tiempo de espera de las recompensas](#reward-wait-time) en Azure Portal.

Si no se ha recibido la puntuación de recompensa de un evento en el período del **Tiempo de espera de las recompensas**, se aplicará la **Recompensa predeterminada**. Normalmente, la **[Recompensa predeterminada](how-to-settings.md#configure-reward-settings-for-the-feedback-loop-based-on-use-case)** está configurada en cero.


## <a name="behaviors-and-data-to-consider-for-rewards"></a>Comportamientos y datos a considerar para las recompensas

Tenga en cuenta estas señales y estos comportamientos para el contexto de la puntuación de recompensa:

* Entradas directas del usuario para sugerencias cuando hay opciones involucradas ("¿Quiere decir X?").
* La duración de la sesión.
* El tiempo entre las sesiones.
* El análisis de sentimiento de las interacciones del usuario.
* Preguntas directas y pequeñas encuestas cuando el bot le pide al usuario sus comentarios sobre la utilidad y la precisión.
* La respuesta a las alertas o el retraso en responder a las alertas.

## <a name="composing-reward-scores"></a>Composición de las puntuaciones de recompensa

Una puntuación de recompensa debe calcularse en su lógica de negocios. La puntuación puede representarse como:

* Un único número que se envía una vez
* Una puntuación que se envía de inmediato (por ejemplo, 0,8) y una puntuación adicional enviada más tarde (normalmente, 0,2).

## <a name="default-rewards"></a>Recompensas predeterminadas

Si no se recibe ninguna recompensa dentro del [Tiempo de espera de las recompensas](#reward-wait-time), la duración desde la llamada a Rank, Personalizer aplica implícitamente la **Recompensa predeterminada** a ese evento de Rank.

## <a name="building-up-rewards-with-multiple-factors"></a>Creación de recompensas con varios factores

Para una personalización efectiva, puede compilar la puntuación de recompensa en función de varios factores.

Por ejemplo, podría aplicar estas reglas para personalizar una lista de contenido de vídeo:

|Comportamiento del usuario|Valor de puntuación parcial|
|--|--|
|El usuario hizo clic en el elemento superior.|Recompensa: +0,5|
|El usuario abrió el contenido real de ese elemento.|Recompensa: +0,3|
|El usuario vio 5 minutos del contenido o el 30 %, lo que sea más largo.|Recompensa: +0,2|
|||

A continuación, puede enviar la recompensa total a la API.

## <a name="calling-the-reward-api-multiple-times"></a>Llamada a Reward API varias veces

También puede llamar a Reward API con el mismo identificador de evento, enviando diferentes puntuaciones de recompensa. Cuando Personalizer obtiene esas recompensas, determina la recompensa final para ese evento sumándolas tal y como se especifica en la configuración de Personalizer.

Valores de agregación:

*  **Primero**: Toma la primera puntuación de recompensa recibida para el evento y descarta el resto.
* **Suma**: Toma todas las puntuaciones de recompensa recopiladas para el parámetro eventId y las suma.

Todas las recompensas de un evento, que se reciben después del **Tiempo de espera de las recompensas**, se descartan y no afectan al entrenamiento de los modelos.

Al sumar las puntuaciones de recompensa, su recompensa final puede estar fuera del intervalo de puntuación esperado. Esto no provocará un error del servicio.

## <a name="best-practices-for-calculating-reward-score"></a>Procedimientos recomendados para calcular la puntuación de recompensa

* **Considere los verdaderos indicadores de la personalización exitosa**: Es fácil pensar en términos de clics, pero una buena recompensa se basa en lo que quiere que los usuarios *logren* en lugar de lo que quiere que las personas *hagan*.  Por ejemplo, recompensar en función de los clics puede llevar a seleccionar contenido que genera más clics.

* **Usar una puntuación de recompensa en cuanto al funcionamiento de la personalización**: la personalización de una sugerencia de película esperaría esperar a que el usuario observara la película y le asignara una calificación alta. Puesto que la clasificación de película probablemente depende de muchos factores (la calidad de la actuación, el estado de ánimo del usuario), no es una buena señal de recompensa sobre lo bien que funcionó *la personalización*. Sin embargo, que el usuario vea los primeros minutos de la película puede ser una mejor señal de la efectividad de la personalización, y que envíe una recompensa de 1 después de 5 minutos será una mejor señal.

* **Las recompensas solo se aplican a RewardActionID**: Personalizer aplica a las recompensas para comprender la eficacia de la acción especificada en RewardActionID. Si decide mostrar otras acciones y el usuario hace clic en ellas, la recompensa debe ser cero.

* **Considere las consecuencias no previstas**: cree funciones de recompensa que conduzcan a resultados responsables con [ética y un uso responsable](ethics-responsible-use.md).

* **Usar las recompensas incrementales**: la adición de recompensas parciales para los comportamientos de usuario más pequeños ayuda a Personalizer a lograr mejores recompensas. Esta recompensa incremental permite que el algoritmo sepa que se está acercando a involucrar al usuario en el comportamiento final deseado.
    * Si se muestra una lista de películas, en caso de que el usuario pase el puntero por encima de la primera durante un tiempo para ver más información, puede determinar que ocurrió alguna interacción con el usuario. El comportamiento puede contar con una puntuación de recompensa de 0,1.
    * Si el usuario abrió la página y luego la cerró, la puntuación de recompensa puede ser de 0,2.

## <a name="reward-wait-time"></a>Tiempo de espera de las recompensas

Personalizer correlacionará la información de una llamada a Rank con las recompensas enviadas en las llamadas a Reward para entrenar el modelo. Esto pueden ocurrir en momentos diferentes. Personalizer espera durante un tiempo limitado, a partir del momento en que se produjo la llamada a Rank, incluso si la llamada de clasificación se realizó como un evento inactivo y se activó más tarde.

Si se agota el **Tiempo de espera de las recompensas** y no se ha producido ninguna información de recompensa, se aplica una recompensa predeterminada a ese evento con fines de entrenamiento. La duración de espera máxima es de 2 días. Si el escenario requiere un tiempo de espera de recompensa más prolongado (por ejemplo, para campañas de marketing por correo electrónico), ofrecemos una versión preliminar privada con tiempos de espera más dilatados. Abra una incidencia de soporte técnico en Azure Portal para ponerse en contacto con el equipo con objeto de ver si reúne los requisitos y puede optar a esta versión.

## <a name="best-practices-for-reward-wait-time"></a>Procedimientos recomendados para el tiempo de espera de la recompensa

Siga estas recomendaciones para mejorar los resultados.

* Configure un tiempo de espera de las recompensas lo más corto posible, dejando tiempo suficiente para la interactuación del usuario.

* No elija una duración que sea más corta que el tiempo necesario para obtener esta interactuación. Por ejemplo, si algunas de las recompensas llegan una vez que un usuario haya visto 1 minuto de un vídeo, la longitud del experimento debe ser al menos el doble de ese tiempo.

## <a name="next-steps"></a>Pasos siguientes

* [Aprendizaje de refuerzo](concepts-reinforcement-learning.md)
* [Pruebe la API de clasificación](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank/console)
* [Pruebe la API de recompensa](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward)

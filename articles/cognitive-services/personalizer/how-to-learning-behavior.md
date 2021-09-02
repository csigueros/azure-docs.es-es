---
title: Configuración del comportamiento de aprendizaje
description: El modo de aprendiz le proporciona confianza en el servicio Personalizer y sus capacidades de Machine Learning, y proporciona métricas conforme el servicio recibe información de la que se puede aprender, sin poner en peligro el tráfico en línea.
author: jeffmend
ms.author: jeffme
ms.manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 05/01/2020
ms.openlocfilehash: caa3a0d8d9619ee65dbd9d71ae2ff4f56a128c8d
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122831553"
---
# <a name="configure-the-personalizer-learning-behavior"></a>Configuración del comportamiento de aprendizaje de Personalizer

El [modo de aprendiz](concept-apprentice-mode.md) le proporciona confianza en el servicio Personalizer y sus capacidades de Machine Learning, y garantiza que el servicio recibe información de la que se puede aprender, sin poner en peligro el tráfico en línea.

[!INCLUDE [Important Blue Box - Apprentice mode pricing tier](./includes/important-apprentice-mode.md)]

## <a name="configure-apprentice-mode"></a>Configuración del modo de aprendiz

1. Inicie sesión en [Azure Portal](https://portal.azure.com) para buscar su recurso de Personalizer.

1. En la página **Configuración**, en la pestaña **Comportamiento de aprendizaje**, seleccione **Return baseline action, learn as an apprentice** (Volver a la acción de línea de base, aprender en modo de aprendiz) y, a continuación, seleccione **Guardar**.

> [!div class="mx-imgBorder"]
> ![Captura de pantalla de la configuración del comportamiento de aprendizaje del modo de aprendiz en Azure Portal](media/settings/configure-learning-behavior-azure-portal.png)

## <a name="changes-to-the-existing-application"></a>Cambios en la aplicación existente

La aplicación existente no debe cambiar el modo en que actualmente selecciona las acciones que se van a mostrar o cómo la aplicación determina el valor **recompensa** de esa acción. El único cambio en la aplicación podría ser el orden de las acciones enviadas a Rank API de Personalizer. La acción que se muestra actualmente en la aplicación se envía como la _primera acción_ en la lista de acciones. [Rank API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank) usa esta primera acción para entrenar el modelo de Personalizer.

### <a name="configure-your-application-to-call-the-rank-api"></a>Configuración de la aplicación para llamar a Rank API

Para agregar Personalizer a la aplicación, debe llamar a Rank API y Reward API.

1. Agregue la llamada a [Rank API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank) después del punto de la lógica de aplicación existente en el que determine la lista de acciones y sus características. La primera acción de la lista de acciones debe ser la acción seleccionada por la lógica existente.

1. Configure el código para mostrar la acción asociada con el **id. de la acción de recompensa** de Rank API.

### <a name="configure-your-application-to-call-reward-api"></a>Configuración de la aplicación para llamar a Reward API

1. Use la lógica de negocios existente para calcular la **recompensa** de la acción mostrada. El valor debe estar en un intervalo de 0 a 1. Envíe esta recompensa a Personalizer con [Reward API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward). El valor de recompensa no se espera inmediatamente y se puede retrasar cierto tiempo en función de la lógica de negocios.

1. Si no se devuelve la recompensa en el **Tiempo de espera de recompensa** configurado, se usará al recompensa predeterminada en su lugar.

## <a name="evaluate-apprentice-mode"></a>Evaluación del modo de aprendiz

En Azure Portal, en la página **Evaluaciones** del recurso de Personalizer, revise el **Rendimiento actual del comportamiento de aprendizaje**.

> [!div class="mx-imgBorder"]
> ![Captura de pantalla de revisión de la evaluación del comportamiento de aprendizaje del modo de aprendiz en Azure Portal](media/settings/evaluate-apprentice-mode.png)

El modo de aprendiz proporciona las siguientes **métricas de evaluación**:
* **Línea de base: promedio de recompensa**:  Promedio de recompensas del valor predeterminado de la aplicación (línea de base).
* **Personalizer: promedio de recompensa**. Promedio de recompensas totales que Personalizer puede haber alcanzado.
* **Proporción de logros de recompensas en los 1000 eventos más recientes**. Proporción de recompensas de línea de base y de Personalizer, normalizados con los últimos 1000 eventos.

## <a name="switch-behavior-to-online-mode"></a>Cambio del comportamiento al modo en línea

Cuando determine que Personalizer está entrenado con una media acumulada del 75-85 %, el modelo está listo para cambiar al modo en línea.

En Azure Portal para el recurso de Personalizer, en la página **Configuración**, en la pestaña **Comportamiento de aprendizaje**, seleccione **Devolver mejor acción** y, a continuación, seleccione **Guardar**.

No es necesario realizar ningún cambio en las llamadas a Rank API y Reward API.

## <a name="next-steps"></a>Pasos siguientes

* [Administración de la configuración de modelos y del aprendizaje](how-to-manage-model.md)

---
title: Configuración de Personalizer
description: La configuración del servicio incluye la forma en que el servicio trata las recompensas, la frecuencia con el servicio realiza las exploraciones, la frecuencia con que se vuelve a entrenar al modelo y la cantidad de datos que se almacenan.
author: jeffmend
ms.author: jeffme
ms.manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 04/29/2020
ms.openlocfilehash: c868004bcf8ec23eb6607d791f4e9762ad20eca0
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829942"
---
# <a name="configure-personalizer-learning-loop"></a>Configuración del bucle de aprendizaje de Personalizer

La configuración del servicio incluye la forma en que el servicio trata las recompensas, la frecuencia con el servicio realiza las exploraciones, la frecuencia con que se vuelve a entrenar al modelo y la cantidad de datos que se almacenan.

Configure el bucle de aprendizaje en la página **Configuración** de Azure Portal para ese recurso de Personalizer.

<a name="configure-service-settings-in-the-azure-portal"></a>
<a name="configure-reward-settings-for-the-feedback-loop-based-on-use-case"></a>

## <a name="planning-configuration-changes"></a>Planificación de los cambios de configuración

Dado que algunos cambios de configuración [restablecen el modelo](#settings-that-include-resetting-the-model), debe planear los cambios de configuración.

Si tiene previsto usar el [modo de aprendiz](concept-apprentice-mode.md), asegúrese de revisar la configuración de Personalizer antes de cambiar al modo de aprendiz.

<a name="clear-data-for-your-learning-loop"></a>

## <a name="settings-that-include-resetting-the-model"></a>Configuración que incluye el restablecimiento del modelo

Las siguientes acciones desencadenan un nuevo aprendizaje del modelo mediante los datos disponibles hasta los dos últimos días.

* Recompensa
* Exploración

Para [borrar](how-to-manage-model.md) todos los datos, use la página **Configuración de modelo y aprendizaje**.

## <a name="configure-rewards-for-the-feedback-loop"></a>Configuración de recompensas para el bucle de comentarios

Configure el servicio para el uso de recompensas del bucle de aprendizaje. Los cambios que se realicen en los valores siguientes restablecerán el modelo actual de Personalizer y volverán a entrenarlo con los datos de los dos últimos días.

> [!div class="mx-imgBorder"]
> ![Configuración de los valores de recompensas para el bucle de comentarios](media/settings/configure-model-reward-settings.png)

|Value|Propósito|
|--|--|
|Tiempo de espera de las recompensas|Establece el período durante el que Personalizer recopilará los valores de recompensa de una llamada a Rank, desde el momento en que se produce dicha llamada. Para establecer este valor se realiza la pregunta: "¿Cuánto tiempo debe Personalizer esperar llamadas de recompensas?" Todas las recompensas que lleguen después de este espacio temporal se registrarán, pero no se usarán para el aprendizaje.|
|Recompensa predeterminada|Si Personalizer no recibe ninguna llamada de recompensa por durante el período de tiempo de espera de las recompensas asociado a una llamada a Rank, Personalizer asignará la recompensa predeterminada. De forma predeterminada y en la mayoría de los escenarios, la recompensa predeterminada es cero (0).|
|Agregación de recompensas|Si se reciben varias recompensas para la misma llamada de la API Rank, se usa este método de agregación: **suma** o **más antiguo**. El primero elige la primera puntuación recibida y descarta el resto. Esto es útil si desea una única recompensa para las posibles llamadas duplicadas. |

Después de cambiar estos valores, asegúrese de seleccionar **Guardar**.

## <a name="configure-exploration-to-allow-the-learning-loop-to-adapt"></a>Configuración de la exploración para permitir la adaptación del bucle de aprendizaje

Personalization puede detectar nuevos patrones y adaptarlos a los cambios de comportamiento de los usuarios con el paso del tiempo mediante la exploración de alternativas, en lugar de usar la predicción del modelo entrenado. El valor **Exploration** determina qué porcentaje de las llamadas a Rank se responden con una exploración.

Los cambios que se realicen en este valor restablecerán el modelo actual de Personalizer y lo volverán a entrenar con los datos de los dos últimos días.

![El valor de exploración determina qué porcentaje de las llamadas a Rank se responden con una exploración](media/settings/configure-exploration-setting.png)

Después de cambiar este valor, asegúrese de seleccionar **Guardar**.

<a name="model-update-frequency"></a>

## <a name="configure-model-update-frequency-for-model-training"></a>Configuración de la frecuencia de actualización del modelo para el entrenamiento del modelo

La **Frecuencia de actualización del modelo** establece la frecuencia con qué se entrena el modelo.

|Valor de frecuencia|Propósito|
|--|--|
|1 minuto.|Las frecuencias de actualización de un minuto son útiles cuando **se depura** el código de una aplicación mediante Personalizer, se realizan demostraciones o se prueban de forma interactiva ciertos aspectos del aprendizaje automático.|
|15 minutos|Las frecuencias elevadas de actualización del modelo son útiles en aquellas situaciones en las que se desea hacer un **estrecho seguimiento de los cambios** en los comportamientos de los usuarios. Algunos ejemplos incluyen sitios que ofrecen noticias en vivo, contenido viral o pujas en vivo. En estos escenarios se puede usar una frecuencia de 15 minutos. |
|1 hora|Sin embargo, en la mayor parte de los casos una menor frecuencia de actualización resulta muy efectiva.|

![Frecuencia de actualización del modelo establece la frecuencia con la que se vuelve a entrenar un nuevo modelo de Personalizer.](media/settings/configure-model-update-frequency-settings-15-minutes.png)

Después de cambiar este valor, asegúrese de seleccionar **Guardar**.

## <a name="data-retention"></a>Retención de datos

**Período de retención de datos** establece el número de días que Personalizer conserva los registros de datos. Los registros de datos anteriores se necesitan para realizar [evaluaciones sin conexión](concepts-offline-evaluation.md), que se usan para medir la eficacia de Personalizer y optimizar la directiva de aprendizaje.

Después de cambiar este valor, asegúrese de seleccionar **Guardar**.



## <a name="next-steps"></a>Pasos siguientes

[Información sobre la administración del modelo](how-to-manage-model.md)

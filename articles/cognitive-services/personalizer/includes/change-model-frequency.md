---
title: Archivo de inclusión
description: Archivo de inclusión
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 08/25/2020
ms.openlocfilehash: 6473eeee350299ba98460f3f4f022e0514f90e69
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/16/2021
ms.locfileid: "112255424"
---
### <a name="change-the-model-update-frequency"></a>Cambio de la frecuencia de actualización del modelo

En la página **Configuración** del recurso Personalizer de Azure Portal, cambie el valor de **Frecuencia de actualización del modelo** a 30 segundos. Con esta duración breve, el modelo se entrenará rápidamente, lo que permite ver cómo cambia la acción principal en cada iteración.

![Cambiar la frecuencia de actualización del modelo](../media/settings/configure-model-update-frequency-settings.png)

La primera vez que se instancia un bucle de Personalizer, no hay ningún modelo porque no se ha producido ninguna llamada a API Reward a partir de la que realizar el entrenamiento. Las llamadas de Rank devolverán las mismas probabilidades para cada elemento. La aplicación siempre debe clasificar el contenido mediante la salida de RewardActionId.
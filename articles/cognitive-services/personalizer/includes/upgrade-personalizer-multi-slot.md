---
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 03/23/2021
ms.openlocfilehash: 0f181a28d1522fb433a274361c96e532c6a0b708
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/16/2021
ms.locfileid: "112255160"
---
### <a name="upgrade-personalizer-instance-to-multi-slot"></a>Actualización de instancias de Personalizer para que tenga varias ranuras

> [!NOTE]
> La personalización de varias ranuras (versión preliminar) afecta a otras funcionalidades del servicio Personalizer. Este cambio no se puede deshacer. Antes de habilitar la personalización de varias ranuras, consulte [Personalización de varias ranuras (versión preliminar)](../concept-multi-slot-personalization.md). 


1. Deshabilitación de la optimización automática En Azure Portal, en el recurso Personalizer, en la página **Configuración de modelo y aprendizaje** de **Administración de recursos**, desactive la optimización automática y guarde.

> [!NOTE]
> La personalización de varias ranuras no funcionará a menos que deshabilite la optimización automática. En el futuro se admitirá la optimización automática para la personalización de varias ranuras. 

2. Actualización de Personalizer para que tenga varias ranuras En Azure Portal, en el recurso Personalizer, en la página **Configuración de modelo y aprendizaje** de **Administración de recursos**, haga clic en **Exportar configuración de aprendizaje**. El campo **arguments** del archivo JSON descargado comenzará por **--cb_explore_adf**. Cambie esto por **--ccb_explore_adf** y guarde el archivo. CB (bandidos contextuales) y CCB (bandidos contextuales condicionales) son los algoritmos que usa Personalizer para la personalización de una y varias ranuras, respectivamente. ADF (características dependientes de la acción) significa que las acciones se expresan o identifican con características.

![Configuración de aprendizaje antes del cambio](../media/settings/learning-settings-pre-upgrade.png)

![Configuración de aprendizaje después del cambio](../media/settings/learning-settings-post-upgrade.png)

En la misma pestaña del portal, en **importar configuración de aprendizaje**, busque el archivo JSON modificado recientemente y ábralo. De este modo, su instancia de Personalizer se actualizará para ser de varias ranuras y permitir en lo sucesivo llamadas Rank y Reward de este tipo.



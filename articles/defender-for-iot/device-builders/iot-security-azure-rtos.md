---
title: Información general del microagente de Defender para IoT para Azure RTOS
description: Obtenga más información sobre la compatibilidad y la implementación del microagente de Defender para IoT para Azure RTOS como parte de Azure Defender para IoT.
ms.topic: conceptual
ms.date: 07/01/2021
ms.openlocfilehash: cf905eb9cff7840eeaac70e731421ba2252193e6
ms.sourcegitcommit: 025a2bacab2b41b6d211ea421262a4160ee1c760
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/06/2021
ms.locfileid: "113303659"
---
# <a name="overview-defender-for-iot-defender-iot-micro-agent-for-azure-rtos"></a>Información general: microagente de Defender para IoT de Defender para IoT para Azure RTOS

El micromódulo de Azure Defender para IoT proporciona una solución de seguridad completa para dispositivos que usan Azure RTOS. Proporciona cobertura para amenazas comunes y posibles actividades malintencionadas en dispositivos del sistema operativo en tiempo real (RTOS). Azure RTOS ahora se incluye con el microagente de Defender para IoT de Azure IoT integrado.

:::image type="content" source="./media/architecture/azure-rtos-security-monitoring.png" alt-text="Visualización de Defender para IoT para Azure RTOS.":::


El micromódulo para Azure RTOS ofrece las siguientes características:

- Detección de actividad de red malintencionada
- Línea de base del comportamiento de los dispositivos basada en alertas personalizadas
- Higiene mejorada de la seguridad de los dispositivos

## <a name="detect-malicious-network-activities"></a>Detección de actividades de red malintencionadas

Se supervisa la actividad de red entrante y saliente de cada dispositivo. Los protocolos admitidos son TCP, UDP e ICMP en IPv4 e IPv6. Defender para IoT inspecciona cada una de estas actividades de red en la fuente de inteligencia sobre amenazas de Microsoft. La fuente se actualiza en tiempo real con millones de indicadores de amenazas únicos recopilados en todo el mundo.

## <a name="device-behavior-baselining-based-on-custom-alerts"></a>Línea de base del comportamiento de los dispositivos basada en alertas personalizadas

La línea de base permite la agrupación en clústeres de dispositivos en grupos de seguridad y la definición del comportamiento esperado de cada grupo. Dado que los dispositivos IoT suelen estar diseñados para funcionar en escenarios bien definidos y limitados, es fácil crear una línea de base que defina su comportamiento esperado mediante un conjunto de parámetros. Cualquier desviación de la línea de base desencadena una alerta.

## <a name="improve-your-device-security-hygiene"></a>Mejora de la higiene de seguridad de los dispositivos

Al usar la infraestructura recomendada que proporciona Defender para IoT, puede adquirir conocimientos e información sobre los problemas de su entorno que afectan y dañan la postura de seguridad de los dispositivos. Una débil postura de seguridad de los dispositivos IoT puede permitir que los posibles ataques surtan efecto si se deja sin cambios. La seguridad siempre se mide por el eslabón más débil dentro de cualquier organización.

## <a name="get-started-protecting-azure-rtos-devices"></a>Introducción a la protección de dispositivos Azure RTOS

El microagente de Defender para IoT para Azure RTOS se proporciona como descarga gratuita para los dispositivos. El servicio en la nube Defender para IoT está disponible con una versión de prueba de 30 días por suscripción de Azure. Para empezar, descargue el [microagente de Defender para IoT para Azure RTOS](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/defender-for-iot/device-builders/iot-security-azure-rtos.md). 

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha obtenido información sobre el microagente de Defender para IoT para Azure RTOS. Para obtener más información sobre el microagente de Defender para IoT y empezar, consulte los artículos siguientes:

- [Conceptos del microagente de Defender para IoT para Azure RTOS](concept-rtos-security-module.md)
- [Inicio rápido: microagente de Defender para IoT para Azure RTOS](quickstart-azure-rtos-security-module.md)

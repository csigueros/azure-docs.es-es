---
title: Conexión de cámaras al servicio Azure Video Analyzer
description: En este artículo se abordan las formas de conectar las cámaras directamente al servicio Azure Video Analyzer.
ms.service: azure-video-analyzer
ms.topic: conceptual
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: f463fafb4f11e935d45c42326c23e1a0e4ffb28f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132297704"
---
# <a name="connect-cameras-to-the-cloud"></a>Conexión de las cámaras a la nube

[!INCLUDE [header](includes/cloud-env.md)]

El servicio Azure Video Analyzer permite a los usuarios conectar las cámaras RTSP directamente a la nube para capturar y grabar vídeo mediante [canalizaciones en directo](../pipeline.md). Esto reducirá la carga computacional en un dispositivo perimetral o eliminará completamente la necesidad de un dispositivo perimetral. El servicio Video Analyzer actualmente admite tres métodos diferentes para conectar cámaras a la nube: conexión a través de un adaptador de dispositivo remoto, conexión desde detrás de un firewall mediante un comando PnP de IoT y conexión a través de Internet sin un firewall.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/connect-cameras-to-cloud/connect-cameras-to-cloud.svg" alt-text="Tres métodos diferentes para conectar cámaras a la nube":::

## <a name="connect-via-a-remote-device-adapter"></a>Conexión a través de un adaptador de dispositivo remoto

Puede implementar el módulo perimetral de Video Analyzer en un dispositivo de IoT Edge en la misma red (privada) que las cámaras RTSP y conectar el dispositivo perimetral a Internet. El módulo perimetral ahora se puede configurar como un *adaptador* que permite al servicio Video Analyzer conectarse a los *dispositivos remotos* (cámaras). El módulo perimetral actúa como una [puerta de enlace transparente](../../../iot-edge/iot-edge-as-gateway.md) para el tráfico de vídeo entre las cámaras RTSP y el servicio Video Analyzer. Esta característica es útil en los escenarios siguientes:

* Cuando es necesario proteger las cámaras o los dispositivos de la exposición a Internet.
* Cuando las cámaras o dispositivos no tienen la funcionalidad para conectarse a IoT Hub independientemente.
* Cuando la energía, el espacio u otras consideraciones permiten implementar solo un dispositivo perimetral ligero en el entorno local.

El módulo perimetral de Video Analyzer no actúa como puerta de enlace transparente para la mensajería y telemetría desde la cámara a IoT Hub, sino que actúa solo como puerta de enlace transparente para vídeo.

## <a name="connect-behind-a-firewall-using-an-iot-pnp-device-implementation"></a>Conexión detrás de un firewall mediante una implementación de dispositivos PnP de IoT

Este método permite que los dispositivos o cámaras RTSP se conecten a Video Analyzer detrás de un firewall mediante una [interfaz de comandos de IoT Plug and Play](../../../iot-develop/overview-iot-plug-and-play.md) y elimina la necesidad de un dispositivo perimetral. Este método requiere que se instale y ejecute una implementación adecuada del dispositivo IoT PnP en cámaras o dispositivos. [Aquí](connect-devices.md) encontrará información sobre cómo conectar un dispositivo compatible desde cualquier fabricante.

## <a name="connect-over-the-internet-no-firewall"></a>Conexión a través de Internet (sin firewall)

Este método solo debe usarse para ejercicios de prueba de concepto supervisados, donde se puede admitir el permiso para que el servicio Video Analyzer acceda al dispositivo a través de Internet, sin un firewall. 

Un caso de uso relacionado es cuando se implementa en una máquina virtual de Azure para simular una cámara RTSP, tal como se describe en este [inicio rápido](get-started-livepipelines-portal.md).


## <a name="next-steps"></a>Pasos siguientes

- Siga esta [guía paso a paso](use-remote-device-adapter.md) para conectar cámaras a través de un adaptador de dispositivo remoto.
- Siga esta [guía paso a paso](connect-devices.md) para obtener información sobre cómo conectar dispositivos mediante una implementación de IoT PnP.
- Siga esta [guía paso a paso](get-started-livepipelines-portal.md) para conectar cámaras a través de Internet.

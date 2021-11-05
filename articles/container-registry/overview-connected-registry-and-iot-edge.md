---
title: Uso de un registro conectado con Azure IoT Edge
description: Introducción a Azure Container Registry conectado en escenarios de IoT Edge jerárquicos
author: toddysm
ms.author: memladen
ms.service: container-registry
ms.topic: overview
ms.date: 08/24/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 3f5be0a18c2185dffa685291121c8a730d60ef52
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091098"
---
# <a name="using-connected-registry-with-azure-iot-edge"></a>Uso de un registro conectado con Azure IoT Edge

En este artículo, ha aprendido a usar un [registro conectado](intro-connected-registry.md) de Azure en escenarios de [IoT Edge](../iot-edge/about-iot-edge.md) jerárquicos. El registro de contenedor conectado se puede implementar como un módulo de IoT Edge y desempeñar un rol esencial en el servicio de imágenes de contenedor que requieren los dispositivos de la jerarquía.

## <a name="what-is-a-hierarchical-iot-edge-deployment"></a>¿Qué es una implementación de IoT Edge jerárquica?

Azure IoT Edge permite implementar dispositivos con IoT Edge en redes organizadas en capas jerárquicas. Cada capa de una jerarquía es un [dispositivo de puerta de enlace](../iot-edge/iot-edge-as-gateway.md) que controla los mensajes y las solicitudes de los dispositivos de la capa que se encuentra debajo. Puede estructurar una jerarquía de dispositivos para que solo la capa superior tenga conectividad a la nube y las capas inferiores solo puedan comunicarse con las capas superior e inferior adyacentes. Estas capas de red constituyen la base de la mayoría de las redes industriales que siguen la [norma ISA-95](https://en.wikipedia.org/wiki/ANSI/ISA-95).

Para aprender a crear una jerarquía de dispositivos con IoT Edge, consulte [Tutorial: Creación de una jerarquía de dispositivos con IoT Edge][tutorial-nested-iot-edge]

## <a name="how-do-i-use-connected-registry-in-hierarchical-iot-edge-scenarios"></a>¿Cómo se usa un registro conectado en escenarios de IoT Edge jerárquicos?

En la imagen siguiente se muestra cómo se puede usar el registro conectado para admitir la implementación jerárquica de IoT Edge. Las líneas continuas grises muestran el flujo de red real, mientras que las discontinuas muestran la comunicación lógica entre los componentes y los registros conectados.

![Registro conectado e implementaciones de IoT Edge jerárquicas](media/overview-connected-registry-and-iot-edge/connected-registry-iot-edge-overview.svg)

### <a name="top-layer"></a>Nivel superior

La capa superior de la arquitectura de ejemplo, *Layer 5: Enterprise Network* (Capa 5: red de la empresa), se administra desde TI y puede acceder al registro de contenedor de Contoso en la nube de Azure. El registro conectado se implementa en forma de módulo de IoT Edge en la máquina virtual con IoT Edge y puede comunicarse directamente con el registro en la nube para extraer e insertar imágenes y artefactos. 

El registro conectado se muestra como en funcionamiento en el [modo readWrite predeterminado](intro-connected-registry.md#modes). Los clientes de este registro conectado pueden extraer e insertar imágenes y artefactos en él. Las imágenes insertadas se sincronizarán con el registro en la nube. Si no se requieren inserciones en esa capa, se puede cambiar el registro conectado para que funcione en [modo ReadOnly](intro-connected-registry.md#modes).

Para saber cuáles son los pasos necesarios para implementar el registro conectado como un módulo con IoT Edge en este nivel, consulte [Inicio rápido: Implementación de un registro conectado en un dispositivo con IoT Edge][quickstart-deploy-connected-registry-iot-edge-cli].

### <a name="nested-layers"></a>Capas anidadas

La siguiente capa inferior, *Layer 4: Site Business Planning and Logistics*, (Capa 4: planeamiento y logística empresarial) está configurada para comunicarse solo con la capa 5. Por consiguiente, al implementar la máquina con IoT Edge en la capa 4, es preciso extraer las imágenes del módulo del registro conectado en la capa 5 en su lugar. 

También puede implementar un registro conectado que funcione en modo ReadOnly para atender las capas siguientes. Esto se ilustra con la máquina virtual con IoT Edge en *Layer 3: Industrial Security Zone* (Capa 3: zona de seguridad industrial). Esa máquina virtual debe extraer las imágenes del módulo del registro conectado en la *capa 4*. Si es necesario servir a los clientes de capas inferiores, se puede implementar un registro conectado en modo ReadOnly en la capa 3, y así sucesivamente.

En esta arquitectura, los registros conectados implementados en cada capa se configuran para sincronizar las imágenes con el registro conectado en la capa anterior. Los registros conectados se implementan como módulos con IoT Edge y aprovechan los mecanismos con IoT Edge para la implementación y el enrutamiento de red.

Para ver los pasos para implementar el registro conectado en dispositivos con IoT Edge anidados, consulte [Inicio rápido: Implementación de un registro conectado en dispositivos con IoT Edge anidados][tutorial-deploy-connected-registry-nested-iot-edge-cli].

## <a name="next-steps"></a>Pasos siguientes

En esta información general, ha obtenido información sobre el uso del registro conectado en escenarios con IoT Edge jerárquicos. Continúe con los artículos siguientes para aprender a configurar e implementar un registro conectado en el dispositivo con IoT Edge.

> [!div class="nextstepaction"]
> [Inicio rápido: Creación de un registro conectado mediante la CLI][quickstart-connected-registry-cli]

> [!div class="nextstepaction"]
> [Inicio rápido: Creación de un registro conectado mediante Azure Portal][quickstart-connected-registry-portal]

> [!div class="nextstepaction"]
> [Inicio rápido: Implementación de un registro conectado en un dispositivo con IoT Edge][quickstart-deploy-connected-registry-iot-edge-cli]

> [!div class="nextstepaction"]
> [Tutorial: Implementación de un registro conectado en dispositivos con IoT Edge anidados][tutorial-deploy-connected-registry-nested-iot-edge-cli]

<!-- LINKS - internal -->
[quickstart-connected-registry-cli]:quickstart-connected-registry-cli.md
[quickstart-connected-registry-portal]:quickstart-connected-registry-portal.md
[quickstart-deploy-connected-registry-iot-edge-cli]:quickstart-deploy-connected-registry-iot-edge-cli.md
[tutorial-nested-iot-edge]:../iot-edge/tutorial-nested-iot-edge.md
[tutorial-deploy-connected-registry-nested-iot-edge-cli]: tutorial-deploy-connected-registry-nested-iot-edge-cli.md

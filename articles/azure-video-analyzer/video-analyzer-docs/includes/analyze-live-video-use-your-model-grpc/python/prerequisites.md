---
author: Juliako
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 04/07/2021
ms.author: juliako
ms.openlocfilehash: f82665a03cdc1f710afb05c58d9a457e5d604e15
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "113735043"
---
* Una cuenta de Azure que incluya una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), en caso de que aún no lo haya hecho.

    > [!NOTE]
    > Necesitará una suscripción a Azure con al menos un rol de colaborador. Si no tiene los permisos adecuados, póngase en contacto con el administrador de la cuenta para que se los conceda.
    * [Visual Studio Code](https://code.visualstudio.com/) con las siguientes extensiones:
        * [Herramientas de Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

        [!INCLUDE [install-docker-prompt](../../common-includes/install-docker-prompt.md)]
        * [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
    * [Python 3](https://www.python.org/downloads/) (3.6.9 o posterior), [Pip 3](https://pip.pypa.io/en/stable/installing/) y, opcionalmente, [venv](https://docs.python.org/3/library/venv.html).
 
## <a name="set-up-azure-resources"></a>Configuración de los recursos de Azure

[![Implementación en Azure](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)  
[!INCLUDE [resources](../../../includes/common-includes/azure-resources.md)]


## <a name="overview"></a>Información general

> [!div class="mx-imgBorder"]
> :::image type="content" source="./../../../media/analyze-live-video-use-your-model-grpc/overview.png" alt-text="Introducción a gRPC":::
 
En este diagrama se muestra cómo fluyen las señales en este inicio rápido. Un [módulo perimetral](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555) simula una cámara IP que hospeda un servidor de protocolo RTSP. Un [nodo de origen RTSP](./../../../pipeline.md#rtsp-source) extrae la fuente de vídeo de este servidor y envía fotogramas de vídeo al nodo del [procesador de detección del movimiento](./../../../pipeline.md#motion-detection-processor). Este procesador detectará el movimiento y, cuando se detecte, enviará fotogramas de vídeo al nodo del [procesador de extensión gRPC](./../../../pipeline.md#grpc-extension-processor).

El nodo de extensión gRPC desempeña el rol de un servidor proxy. Convierte los fotogramas de vídeo en el tipo de imagen especificado. Luego, retransmite la imagen sobre gRPC a otro módulo perimetral que ejecuta un modelo de IA detrás de un punto de conexión gRPC en una [memoria compartida](https://en.wikipedia.org/wiki/Shared_memory). En este ejemplo, el módulo perimetral se crea mediante el modelo [YOLOv3](https://github.com/Azure/video-analyzer/tree/main/edge-modules/extensions/yolo/yolov3), que puede detectar muchos tipos de objetos. El nodo del procesador de extensión gRPC recopila los resultados de la detección y publica los eventos en el nodo del [receptor de IoT Hub](./../../../pipeline.md#iot-hub-message-sink). que posteriormente los envía a [IoT Edge Hub](../../../../../iot-fundamentals/iot-glossary.md?view=iotedge-2020-11&preserve-view=true#iot-edge-hub).

En este inicio rápido realizará lo siguiente:

1. Creará e implementará la canalización.
1. Interpretará los resultados.
1. Limpieza de recursos.

## <a name="set-up-your-development-environment"></a>Configurado su entorno de desarrollo
[!INCLUDE [setup development environment](./../../../includes/set-up-dev-environment/python/python-set-up-dev-env.md)]
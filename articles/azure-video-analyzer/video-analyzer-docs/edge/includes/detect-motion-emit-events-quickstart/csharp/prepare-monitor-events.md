---
author: fvneerden
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 11/04/2021
ms.author: faneerde
ms.openlocfilehash: 2bb305673897daa264dafc9b766061a01717099a
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131860649"
---
El módulo perimetral de Video Analyzer se usará para detectar el movimiento en la secuencia de vídeo en directo entrante y enviar eventos a IoT Hub. Para ver estos eventos, siga estos pasos:

1. Abra el panel del explorador en Visual Studio Code y busque Azure IoT Hub en la esquina inferior izquierda.
1. Expanda el nodo **Devices** (Dispositivos).
1. Haga clic con el botón derecho en **avasample-iot-edge-device** y seleccione **Iniciar la supervisión del punto de conexión de eventos integrado**.

   ![Inicio de la supervisión de un punto de conexión de eventos integrado](../../../media/vscode-common-screenshots/start-monitoring.png)

    [!INCLUDE [provide-builtin-endpoint](../../common-includes/provide-builtin-endpoint.md)]

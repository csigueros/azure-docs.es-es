---
author: fvneerden
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 05/03/2021
ms.author: faneerde
ms.openlocfilehash: e98039dfe4c8c44be2fc375956339956a495da4f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131030856"
---
Va a utilizar el módulo de Azure Video Analyzer para detectar el movimiento en la secuencia de vídeo en directo entrante y enviar eventos a IoT Hub. Para ver estos eventos, siga estos pasos:

1. Abra el panel del explorador en Visual Studio Code y busque Azure IoT Hub en la esquina inferior izquierda.
1. Expanda el nodo **Devices** (Dispositivos).
1. Haga clic con el botón derecho en **avasample-iot-edge-device** y seleccione **Iniciar la supervisión del punto de conexión de eventos integrado**.

![Inicio de la supervisión de un punto de conexión de eventos integrado](../../../media/vscode-common-screenshots/start-monitoring.png)

[!INCLUDE [provide-builtin-endpoint](../../common-includes/provide-builtin-endpoint.md)]

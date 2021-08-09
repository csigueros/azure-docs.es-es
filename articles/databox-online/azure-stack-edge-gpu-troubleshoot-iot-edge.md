---
title: Solución de problemas de IoT Edge en el dispositivo Azure Stack Edge Pro con GPU| Microsoft Docs
description: En este artículo se describe cómo solucionar los problemas de un dispositivo Azure Stack Edge Pro con GPU.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 06/19/2021
ms.author: alkohli
ms.custom: contperf-fy21q4
ms.openlocfilehash: 405a9c62a551a011eb6d7b00025c6ae0a563e858
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/10/2021
ms.locfileid: "111987909"
---
# <a name="troubleshoot-iot-edge-issues-on-your-azure-stack-edge-pro-gpu-device"></a>Solución de problemas de IoT Edge en el dispositivo Azure Stack Edge Pro con GPU 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

En este artículo se describe cómo solucionar errores relacionados con el proceso en un dispositivo Azure Stack Edge Pro con GPU mediante la revisión de las respuestas en tiempo de ejecución del agente de IoT Edge y los errores del servicio IoT Edge instalado en el dispositivo.

## <a name="review-iot-edge-runtime-responses"></a>Revisión de respuestas en tiempo de ejecución de IoT Edge

[!INCLUDE [Troubleshoot IoT Edge runtime](../../includes/azure-stack-edge-iot-troubleshoot-compute.md)]

## <a name="troubleshoot-iot-edge-service-errors"></a>Solución de problemas de errores del servicio IoT Edge

El siguiente error está relacionado con el servicio IoT Edge en el dispositivo Azure Stack Edge Pro con GPU.

[!INCLUDE [Troubleshoot IoT Edge errors](../../includes/azure-stack-edge-iot-troubleshoot-compute-error-detail.md)]


## <a name="next-steps"></a>Pasos siguientes

- [Depuración de problemas de Kubernetes relacionados con IoT Edge](azure-stack-edge-gpu-connect-powershell-interface.md#debug-kubernetes-issues-related-to-iot-edge)
- [Solución de problemas de dispositivos](azure-stack-edge-gpu-troubleshoot.md)
---
title: 'Inicio rápido: Control de un dispositivo desde Azure IoT Hub | Microsoft Docs'
description: En este inicio rápido, se ejecutan dos aplicaciones de muestra. Una aplicación es una aplicación de servicio que puede controlar dispositivos conectados al centro de manera remota. La otra aplicación simula un dispositivo conectado al centro que se puede controlar de manera remota.
author: eross-msft
ms.author: lizross
ms.service: iot-hub
services: iot-hub
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
- 'Role: Cloud Development'
- devx-track-azurecli
ms.date: 07/26/2021
zone_pivot_groups: iot-hub-set1
ms.openlocfilehash: cae4b2c1b23acc56468d3c7ebe30cf8316842003
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132550256"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub"></a>Inicio rápido: Control de un dispositivo conectado a IoT Hub

En este inicio rápido, usa un método directo para controlar un dispositivo simulado conectado a IoT Hub. IoT Hub es un servicio de Azure que permite administrar dispositivos IoT desde la nube e ingerir grandes volúmenes de datos de telemetría de dispositivos en la nube para su almacenamiento o procesamiento. Puede usar métodos directos para cambiar el comportamiento de los dispositivos conectados a IoT Hub de forma remota.

:::zone pivot="programming-language-csharp"

[!INCLUDE [quickstart-control-device-dotnet](../../includes/quickstart-control-device-dotnet.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [quickstart-control-device-java](../../includes/quickstart-control-device-java.md)]

:::zone-end

:::zone pivot="programming-language-nodejs"

[!INCLUDE [quickstart-control-device-node](../../includes/quickstart-control-device-node.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [quickstart-control-device-python](../../includes/quickstart-control-device-python.md)]

:::zone-end

## <a name="clean-up-resources"></a>Limpieza de recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido ha llamado a un método directo en un dispositivo desde una aplicación de servicio y ha respondido a la llamada de método directo en una aplicación de dispositivo simulado.

Para obtener información sobre cómo redirigir mensajes del dispositivo a la nube a diferentes destinos en la nube, continúe con el siguiente tutorial.

> [!div class="nextstepaction"]
> [Tutorial: Enrutar datos de telemetría a distintos puntos de conexión para procesamiento](tutorial-routing.md)
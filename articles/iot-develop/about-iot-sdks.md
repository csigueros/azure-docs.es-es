---
title: Información general sobre las opciones del SDK de dispositivo IoT de Azure
description: Obtenga información sobre qué SDK de dispositivo IoT de Azure usar en función de su rol y tareas de desarrollo.
author: philmea
ms.author: philmea
ms.service: iot-develop
ms.topic: overview
ms.date: 02/11/2021
ms.openlocfilehash: e4f5d99caadaedf6389215fccb753aedd6620917
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "129983976"
---
# <a name="overview-of-azure-iot-device-sdks"></a>Información general sobre los SDK de dispositivo IoT de Azure

Los SDK de dispositivo IoT de Azure son un conjunto de bibliotecas cliente de dispositivo, ejemplos y documentación. Los SDK de dispositivo simplifican el proceso de conexión mediante programación de dispositivos a Azure IoT. Los SDK están disponibles en varios lenguajes de programación con compatibilidad con varios RTOS para dispositivos insertados.

## <a name="why-use-an-azure-iot-device-sdk"></a>¿Por qué usar un SDK de dispositivo IoT de Azure?

A continuación se describen las ventajas de usar un SDK de dispositivo IoT de Azure con respecto a la creación de una capa de conexión personalizada:

| | Capa de conexión personalizada | SDK de dispositivo IoT de Azure |
| :-- | :-- | :-- |
| **Soporte técnico** | Necesidad de respaldar y documentar la solución | Acceso al servicio de soporte técnico de Microsoft (GitHub, Microsoft Q&A, Microsoft Docs, equipos de soporte al cliente) |
| **Nuevas características** | Necesidad de agregar manualmente nuevas características de Azure | Posibilidad de aprovechar inmediatamente las nuevas características agregadas |
| **Inversión** | Inversión de cientos de horas de desarrollo insertado para diseñar, crear, probar y mantener una versión personalizada | Puede aprovechar las herramientas gratuitas de código abierto. El único costo asociado a los SDK es la curva de aprendizaje. |

## <a name="which-sdk-should-i-use"></a>¿Qué SDK debo usar?

El aspecto principal a la hora de elegir un SDK es el propio hardware del dispositivo. Los dispositivos informáticos generales, como los PC y teléfonos móviles, contienen unidades de microprocesador (MPU) y tienen recursos de proceso y memoria relativamente mayores. Una clase especializada de dispositivos, que se usan como sensores u otros roles de propósito especial, contienen unidades de microcontrolador (MCU) y tienen recursos de proceso y memoria relativamente limitados. Estos dispositivos con restricciones de recursos requieren herramientas de desarrollo y SDK especializados. En la tabla siguiente se resumen las distintas clases de dispositivos y los SDK que se usan para el desarrollo de dispositivos.

|Clase de dispositivo|Descripción|Ejemplos|SDK|
|-|-|-|-|
|Dispositivos insertados|Dispositivos basados en MCU de uso especial con limitaciones de proceso y memoria|Sensores|[SDK de dispositivos insertados](#embedded-device-sdks)|
|Otros|Se incluyen dispositivos basados en MPU de uso general con recursos de proceso y memoria mayores.|PC, smartphone, Raspberry Pi|[SDK de dispositivo](#device-sdks)|

> [!Note] 
> Para más información sobre las distintas categorías de dispositivos a fin de elegir el mejor SDK para el suyo, consulte [Tipos de dispositivos de Azure IoT](concepts-iot-device-types.md).

## <a name="device-sdks"></a>SDK de dispositivo

Estos SDK se pueden ejecutar en un dispositivo informático general basado en MPU, como un PC, una tablet, un smartphone o una Raspberry Pi. Los SDK admiten el desarrollo en C y en lenguajes administrados modernos, incluidos C#, Node.js, Python y Java.

Los SDK están disponibles en **varios idiomas**, lo que proporciona flexibilidad para elegir cuál se adapta mejor a su equipo y escenario.

| Idioma | Paquete | Source | Guías de inicio rápido | Ejemplos | Referencia |
| :-- | :-- | :-- | :-- | :-- | :-- |
| **.NET** | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [IoT Hub](quickstart-send-telemetry-iot-hub.md?pivots=programming-language-csharp) / [IoT Central](quickstart-send-telemetry-central.md?pivots=programming-language-csharp) | [Muestras](https://github.com/Azure-Samples/azure-iot-samples-csharp) | [Referencia](/dotnet/api/microsoft.azure.devices.client) |
| **Python** | [pip](https://pypi.org/project/azure-iot-device/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python) | [IoT Hub](quickstart-send-telemetry-iot-hub.md?pivots=programming-language-python) / [IoT Central](quickstart-send-telemetry-central.md?pivots=programming-language-python) | [Muestras](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples) | [Referencia](/python/api/azure-iot-device) |
| **Node.js** | [npm](https://www.npmjs.com/package/azure-iot-device) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [IoT Hub](quickstart-send-telemetry-iot-hub.md?pivots=programming-language-nodejs) / [IoT Central](quickstart-send-telemetry-central.md?pivots=programming-language-nodejs) | [Muestras](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples) | [Referencia](/javascript/api/azure-iot-device/) |
| **Java** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [IoT Hub](quickstart-send-telemetry-iot-hub.md?pivots=programming-language-java) / [IoT Central](quickstart-send-telemetry-central.md?pivots=programming-language-java) | [Muestras](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples) | [Referencia](/java/api/com.microsoft.azure.sdk.iot.device) |
| **C** | [packages](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#getting-the-sdk) | [GitHub](https://github.com/Azure/azure-iot-sdk-c) | [IoT Hub](quickstart-send-telemetry-iot-hub.md?pivots=programming-language-ansi-c) / [IoT Central](quickstart-send-telemetry-central.md?pivots=programming-language-ansi-c) | [Muestras](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples) | [Referencia](/azure/iot-hub/iot-c-sdk-ref/) |

> [!WARNING]
> El **SDK de C** mencionado anteriormente **no** es adecuado para las aplicaciones insertadas debido a su modelo de administración de memoria y subprocesos. Para los dispositivos insertados, consulte [SDK de dispositivos insertados](#embedded-device-sdks).

## <a name="embedded-device-sdks"></a>SDK de dispositivos insertados

Estos SDK se han diseñado y creado para ejecutarse en dispositivos con recursos limitados de proceso y memoria, y se implementan con el lenguaje C.

Los SDK de dispositivos insertados están disponibles para **varios sistemas operativos**, lo que proporciona flexibilidad al elegir cuál se adapta mejor a su equipo y escenario.

| RTOS | SDK | Source | Ejemplos | Referencia |
| :-- | :-- | :-- | :-- | :-- | 
| **Azure RTOS** | Middleware de Azure RTOS | [GitHub](https://github.com/azure-rtos/netxduo) | [Guías de inicio rápido](quickstart-devkit-mxchip-az3166.md) | [Referencia](https://github.com/azure-rtos/netxduo/tree/master/addons/azure_iot) | 
| **FreeRTOS** | FreeRTOS Middleware | [GitHub](https://github.com/Azure/azure-iot-middleware-freertos) | [Muestras](https://github.com/Azure-Samples/iot-middleware-freertos-samples) | [Referencia](https://azure.github.io/azure-iot-middleware-freertos) |
| **Bare Metal** | SDK de Azure para C insertado | [GitHub](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot) | [Muestras](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/README.md) | [Referencia](https://azure.github.io/azure-sdk-for-c) |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre el uso de SDK de dispositivo IoT de Azure para conectar dispositivos generales basados en MPU, consulte los siguientes artículos:

* [Inicio rápido: Envío de datos de telemetría a IoT Central](quickstart-send-telemetry-central.md)
* [Inicio rápido: Envío de datos de telemetría a IoT Hub](quickstart-send-telemetry-iot-hub.md)

Para más información sobre el uso de los SDK de dispositivo insertado IoT de Azure para conectar dispositivos basados en MCU con limitación de recursos, consulte el siguiente artículo:
* [Inicio rápido: Conexión de un kit de desarrollo MXCHIP AZ3166 a IoT Central](quickstart-devkit-mxchip-az3166.md)

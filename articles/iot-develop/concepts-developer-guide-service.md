---
title: 'Guía para desarrolladores de servicios: IoT Plug and Play | Microsoft Docs'
description: Descripción de IoT Plug and Play para desarrolladores de servicios de C
author: dominicbetts
ms.author: dobett
ms.date: 10/01/2020
ms.topic: conceptual
ms.service: iot-develop
services: iot-develop
zone_pivot_groups: programming-languages-set-ten
ms.openlocfilehash: 15a381f847082e0853db307cc60893eaeeb43e96
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/20/2021
ms.locfileid: "122604164"
---
# <a name="iot-plug-and-play-service-developer-guide"></a>Guía para desarrolladores de dispositivos IoT Plug and Play

IoT Plug and Play permite crear dispositivos IoT que anuncian sus funcionalidades en aplicaciones de Azure IoT. Los dispositivos IoT Plug and Play no requieren configuración manual cuando un cliente los conecta a aplicaciones habilitadas para IoT Plug and Play.

IoT Plug and Play le permite usar dispositivos que han anunciado su id. de modelo en su centro de IoT. Por ejemplo, puede acceder directamente a las propiedades y los comandos de un dispositivo.

Para usar un dispositivo IoT Plug and Play que esté conectado a su centro de IoT, utilice uno de los SDK de servicio de IoT:

## <a name="service-sdks"></a>SDK de servicio

Utilice los SDK de servicio de Azure IoT en su solución para interactuar con dispositivos y módulos. Por ejemplo, puede usar los SDK de servicio para leer y actualizar propiedades gemelas e invocar comandos. Los lenguajes admitidos son C#, Java, Node.js y Python.

Los SDK de servicio permiten obtener acceso a la información del dispositivo desde una solución, como una aplicación web o de escritorio. Los SDK de servicio incluyen dos espacios de nombres y modelos de objetos que puede utilizar para recuperar el id. de modelo:

- Cliente del servicio IOT Hub. Este servicio expone el id. de modelo como una propiedad gemela del dispositivo.

- Cliente de Digital Twins. La nueva API de Digital Twins funciona en construcciones de modelo del [lenguaje de definición de Digital Twins (DTDL)](concepts-digital-twin.md), como componentes, propiedades y comandos. Las API de gemelo digital facilitan la creación de soluciones de IoT Plug and Play.

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-pnp-service-devguide-csharp](../../includes/iot-pnp-service-devguide-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-pnp-service-devguide-java](../../includes/iot-pnp-service-devguide-java.md)]

:::zone-end

:::zone pivot="programming-language-javascript"

[!INCLUDE [iot-pnp-service-devguide-node](../../includes/iot-pnp-service-devguide-node.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-pnp-service-devguide-python](../../includes/iot-pnp-service-devguide-python.md)]

:::zone-end

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido acerca del modelado de dispositivos, estos son algunos recursos adicionales:

- [Lenguaje de definición de Digital Twins (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [SDK para dispositivos C](/azure/iot-hub/iot-c-sdk-ref/)
- [API REST de IoT](/rest/api/iothub/device)
- [Guía de modelado de IoT Plug and Play](concepts-modeling-guide.md)
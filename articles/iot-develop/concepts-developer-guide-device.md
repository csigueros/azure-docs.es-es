---
title: 'Guía para desarrolladores de dispositivos (C): IoT Plug and Play | Microsoft Docs'
description: Descripción de IoT Plug and Play para desarrolladores de dispositivos de C
author: rido-min
ms.author: rmpablos
ms.date: 11/19/2020
ms.topic: conceptual
ms.service: iot-develop
services: iot-develop
zone_pivot_groups: programming-languages-set-twenty-seven
ms.openlocfilehash: 528f1270be0065622ba7c79db33f8e4eed93bd74
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128580527"
---
# <a name="iot-plug-and-play-device-developer-guide"></a>Guía para desarrolladores de dispositivos IoT Plug and Play

IoT Plug and Play permite crear dispositivos IoT que anuncian sus funcionalidades en aplicaciones de Azure IoT. Los dispositivos IoT Plug and Play no requieren configuración manual cuando un cliente los conecta a aplicaciones habilitadas para IoT Plug and Play.

Un dispositivo IoT podría implementarse directamente, usar [módulos](../iot-hub/iot-hub-devguide-module-twins.md) o usar [módulos de IoT Edge](../iot-edge/about-iot-edge.md).

En esta guía se describen los pasos básicos necesarios para crear un dispositivo, un módulo o un módulo de IoT Edge que sigan las [convenciones de IoT Plug and Play](../iot-develop/concepts-convention.md).

Para compilar un dispositivo o un módulo IoT Plug and Play o un módulo de IoT Edge, siga estos pasos:

1. Asegúrese de que el dispositivo usa el protocolo MQTT o MQTT sobre WebSockets para conectarse a Azure IoT Hub.
1. Cree un modelo de [Lenguaje de definición de Digital Twins (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) para describir el dispositivo. Para más información, consulte [Descripción de los componentes de los modelos de IoT Plug and Play](concepts-modeling-guide.md).
1. Actualice el dispositivo o el módulo para anunciar el elemento `model-id` como parte de la conexión del dispositivo.
1. Implemente telemetría, propiedades y comandos mediante las [convenciones de IoT Plug and Play](concepts-convention.md).

Una vez que la implementación del dispositivo o del módulo esté lista, use [Azure IoT Explorer](../iot-fundamentals/howto-use-iot-explorer.md) para validar que el dispositivo o el módulo sigan las convenciones de IoT Plug and Play.

:::zone pivot="programming-language-ansi-c"

[!INCLUDE [iot-pnp-device-devguide-c](../../includes/iot-pnp-device-devguide-c.md)]

:::zone-end

:::zone pivot="programming-language-embedded-c"

[!INCLUDE [iot-pnp-device-devguide-embedded-c](../../includes/iot-pnp-device-devguide-embedded-c.md)]

:::zone-end

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-pnp-device-devguide-csharp](../../includes/iot-pnp-device-devguide-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-pnp-device-devguide-java](../../includes/iot-pnp-device-devguide-java.md)]

:::zone-end

:::zone pivot="programming-language-javascript"

[!INCLUDE [iot-pnp-device-devguide-node](../../includes/iot-pnp-device-devguide-node.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-pnp-device-devguide-python](../../includes/iot-pnp-device-devguide-python.md)]

:::zone-end

## <a name="next-steps"></a>Pasos siguientes

Ahora que aprendió sobre el desarrollo de dispositivos IoT Plug and Play, estos son algunos recursos adicionales:

- [Lenguaje de definición de Digital Twins (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [SDK para dispositivos C](/azure/iot-hub/iot-c-sdk-ref/)
- [API REST de IoT](/rest/api/iothub/device)
- [Descripción de los componentes de los modelos de IoT Plug and Play](concepts-modeling-guide.md)
- [Guía para desarrolladores de dispositivos IoT Plug and Play](concepts-developer-guide-service.md)

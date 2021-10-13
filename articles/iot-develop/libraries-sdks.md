---
title: SDK y bibliotecas de IoT Plug and Play
description: Información sobre las bibliotecas de dispositivos y servicios disponibles para el desarrollo de soluciones compatibles con IoT Plug and Play.
author: rido-min
ms.author: rmpablos
ms.date: 07/22/2020
ms.topic: reference
ms.service: iot-develop
services: iot-develop
ms.custom: mvc
ms.openlocfilehash: 089c82d2b7a682e5f9a655dbc2a4b34dc94c58ac
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2021
ms.locfileid: "129728710"
---
# <a name="microsoft-sdks-for-iot-plug-and-play"></a>SDK de Microsoft para IoT Plug and Play

Los SDK y las bibliotecas de IoT Plug and Play permiten a los desarrolladores crear soluciones de IoT con diversos lenguajes de programación en varias plataformas. En la tabla siguiente se incluyen vínculos a ejemplos y guías de inicio rápido para ayudarle a empezar:

## <a name="device-sdks"></a>SDK de dispositivo

| Idioma | Paquete | Repositorio de código | Ejemplos | Guía de inicio rápido | Referencia |
|---|---|---|---|---|---|
| C: dispositivo | [vcpkg 1.3.9](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md) | [GitHub](https://github.com/Azure/azure-iot-sdk-c) | [Muestras](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/pnp) | [Conexión a IoT Hub](tutorial-connect-device.md) | [Referencia](/azure/iot-hub/iot-c-sdk-ref/) |
| .NET: dispositivo | [NuGet 1.31.0](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/main/) | [Muestras](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/main/iot-hub/Samples/device/PnpDeviceSamples) | [Conexión a IoT Hub](tutorial-connect-device.md) | [Referencia](/dotnet/api/microsoft.azure.devices.client) |
| Java: dispositivo | [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/main/) | [Muestras](https://github.com/Azure/azure-iot-sdk-java/tree/main/device/iot-device-samples/pnp-device-sample) | [Conexión a IoT Hub](tutorial-connect-device.md) | [Referencia](/java/api/com.microsoft.azure.sdk.iot.device) |
| Python: dispositivo | [pip 2.3.0](https://pypi.org/project/azure-iot-device/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/master/) | [Muestras](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/pnp) | [Conexión a IoT Hub](tutorial-connect-device.md) | [Referencia](/python/api/azure-iot-device/azure.iot.device) |
| Node: dispositivo | [npm 1.17.2](https://www.npmjs.com/package/azure-iot-device)  | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/master/) | [Muestras](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples/javascript/pnp) | [Conexión a IoT Hub](tutorial-connect-device.md) | [Referencia](/javascript/api/azure-iot-device/) |
| C insertado: dispositivo | N/D | [GitHub](https://github.com/Azure/azure-sdk-for-c/)| [Muestras](tutorial-connect-device.md?pivots=programming-language-embedded-c#samples) | [Uso de C insertado](tutorial-connect-device.md?pivots=programming-language-embedded-c) | N/D

## <a name="service-sdks"></a>SDK de servicio

| Plataforma  | Paquete | Repositorio de código | Ejemplos | Guía de inicio rápido | Referencia |
|---|---|---|---|---|---|
| .NET: servicio IoT Hub | [NuGet 1.27.1](https://www.nuget.org/packages/Microsoft.Azure.Devices ) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [Muestras](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/main/iot-hub/Samples/service/PnpServiceSamples) | No aplicable | [Referencia](/dotnet/api/microsoft.azure.devices) |
| Java: servicio IoT Hub | [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client/1.26.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [Muestras](https://github.com/Azure/azure-iot-sdk-java/tree/main/service/iot-service-samples/pnp-service-sample) | No aplicable | [Referencia](/java/api/com.microsoft.azure.sdk.iot.service) |
| Node: servicio IoT Hub | [npm 1.13.0](https://www.npmjs.com/package/azure-iothub) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [Muestras](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples) | No aplicable | [Referencia](/javascript/api/azure-iothub/) |
| Python: servicio Digital Twins | [pip 2.2.3](https://pypi.org/project/azure-iot-hub) | [GitHub](https://github.com/Azure/azure-iot-sdk-python) | [Muestras](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub/samples) | [Interacción con la API de Digital Twins de IoT Hub](tutorial-service.md) | N/D |
| Node: servicio Digital Twins | [npm 1.13.0](https://www.npmjs.com/package/azure-iot-digitaltwins-service) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [Muestras](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples/javascript) | [Interacción con la API de Digital Twins de IoT Hub](tutorial-service.md) | N/D |

## <a name="next-steps"></a>Pasos siguientes

Para probar los SDK y las bibliotecas, vea la [guía para desarrolladores](concepts-developer-guide-device.md) y los [tutoriales sobre dispositivos](tutorial-connect-device.md) y [sobre servicios](tutorial-service.md).

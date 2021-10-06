---
title: SDK de IoT Hub de Azure | Microsoft Docs
description: Vínculos a los SDK de Azure IoT Hub que se pueden usar para compilar aplicaciones de dispositivo y aplicaciones de back-end.
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/01/2021
ms.custom:
- mqtt
- 'Role: IoT Device'
- 'Role: Cloud Development'
ms.openlocfilehash: e8bfc95d80aa58b9781258c1b87902c2e8d0c37f
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129458593"
---
# <a name="azure-iot-hub-sdks"></a>SDK de Azure IoT Hub

Hay dos categorías de kits de desarrollo de software (SDK) para trabajar con IoT Hub:

* Los [**SDK del servicio IoT Hub**](#azure-iot-hub-service-sdks) permiten compilar aplicaciones de back-end para administrar IoT Hub y, opcionalmente, enviar mensajes, programar trabajos, invocar métodos directos o enviar las actualizaciones de propiedades deseadas a los dispositivos o módulos de IoT.

* Los [**SDK de dispositivo de IoT Hub**](../iot-develop/about-iot-sdks.md) permiten compilar aplicaciones que se ejecutan en los dispositivos IoT mediante el cliente de dispositivo o el cliente de módulo. Estas aplicaciones envían datos de telemetría a IoT Hub y, de manera opcional, reciben mensajes, trabajos, métodos o actualizaciones gemelas de dicho servicio. Puede usar estos SDK para compilar aplicaciones de dispositivo que usen los modelos y las convenciones de [Azure IoT Plug and Play](../iot-develop/overview-iot-plug-and-play.md) para anunciar sus funcionalidades en aplicaciones habilitadas para IoT Plug and Play. También puede usar el cliente de módulo para crear [módulos](../iot-edge/iot-edge-modules.md) para el [entorno de ejecución de Azure IoT Edge](../iot-edge/about-iot-edge.md).

Además, también proporcionamos un conjunto de SDK para trabajar con el [servicio Device Provisioning](../iot-dps/about-iot-dps.md).

* Los **SDK de dispositivo de aprovisionamiento** permiten compilar aplicaciones que se ejecuten en los dispositivos IoT para la comunicación con el servicio Device Provisioning.

* Los **SDK del servicio de aprovisionamiento** permiten compilar aplicaciones de back-end para administrar las inscripciones en el servicio Device Provisioning.

Conozca las [ventajas de desarrollar con los SDK de Azure IoT](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/).

## <a name="azure-iot-hub-service-sdks"></a>SDK de servicios de Azure IoT Hub

Los SDK de servicios IoT de Azure contienen código que facilitan la creación de aplicaciones que interactúan directamente con IoT Hub para administrar dispositivos y seguridad.

| Plataforma  | Paquete | Repositorio de código | Ejemplos |  Referencia |
|---|---|---|---|---|
| .NET | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices ) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [Muestras](https://github.com/Azure-Samples/azure-iot-samples-csharp) | [Referencia](/dotnet/api/microsoft.azure.devices) |
| Java | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [Muestras](https://github.com/Azure/azure-iot-sdk-java/tree/main/service/iot-service-samples/pnp-service-sample) | [Referencia](/java/api/com.microsoft.azure.sdk.iot.service) |
| Nodo | [npm](https://www.npmjs.com/package/azure-iothub) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [Muestras](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples) | [Referencia](/javascript/api/azure-iothub/) |
| Python | [pip](https://pypi.org/project/azure-iot-hub) | [GitHub](https://github.com/Azure/azure-iot-sdk-python) | [Muestras](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub/samples) | [Referencia](/python/api/azure-iot-hub) |
| Node.js | [npm](https://www.npmjs.com/package/azure-iot-common) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [Muestras](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples/javascript) | [Referencia](/javascript/api/azure-iothub/) |

SDK de servicios de Azure IoT Hub para iOS:

* Se instala desde [CocoaPod](https://cocoapods.org/pods/AzureIoTHubServiceClient)
* [Muestras](https://github.com/Azure-Samples/azure-iot-samples-ios)

## <a name="microsoft-azure-provisioning-sdks"></a>SDK de aprovisionamiento de Microsoft Azure

Los **SDK de aprovisionamiento de Microsoft Azure** le permiten aprovisionar dispositivos en IoT Hub mediante el [servicio Device Provisioning](../iot-dps/about-iot-dps.md).

| Plataforma | Paquete | Código fuente | Referencia |
| -----|-----|-----|-----|
| .NET|[SDK de dispositivo](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Client/), [SDK de servicio](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) |[GitHub](https://github.com/Azure/azure-iot-sdk-csharp/)|[Referencia](/dotnet/api/microsoft.azure.devices.provisioning.client) |
| C|[apt-get, MBED, Arduino IDE o iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)|[GitHub](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning\_client)|[Referencia](/azure/iot-hub/iot-c-sdk-ref/) |
| Java|[Maven](https://github.com/Azure/azure-iot-sdk-java/blob/main/doc/java-devbox-setup.md#for-the-service-sdk)|[GitHub](https://github.com/Azure/azure-iot-sdk-java/blob/main/provisioning)|[Referencia](/java/api/com.microsoft.azure.sdk.iot.provisioning.device) |
| Node.js|[SDK de dispositivo](https://badge.fury.io/js/azure-iot-provisioning-device), [SDK de servicio](https://badge.fury.io/js/azure-iot-provisioning-service) |[GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning)|[Referencia](/javascript/api/overview/azure/iothubdeviceprovisioning) |
| Python|[SDK de dispositivo](https://pypi.org/project/azure-iot-device/), [SDK de servicio](https://pypi.org/project/azure-iothub-provisioningserviceclient/)|[GitHub](https://github.com/Azure/azure-iot-sdk-python)|[Referencia de dispositivo,](/python/api/azure-iot-device/azure.iot.device.provisioningdeviceclient) [Referencia de servicio](/python/api/azure-mgmt-iothubprovisioningservices) |

## <a name="azure-iot-hub-device-sdks"></a>SDK de dispositivos de Azure IoT Hub

Los SDK de dispositivos IoT de Microsoft Azure contienen código que facilita la compilación de aplicaciones que se conectan a servicios de Azure IoT Hub y que este administra.

Obtenga más información sobre los SDK de dispositivo de IoT Hub en [Información general sobre los SDK de dispositivo IoT de Azure](../iot-develop/about-iot-sdks.md).

## <a name="sdk-and-hardware-compatibility"></a>Compatibilidad de hardware y del SDK

Para obtener más información sobre cómo elegir un SDK de dispositivo, consulte [Información general de los SDK de dispositivo IoT de Azure](../iot-develop/about-iot-sdks.md).

Para más información sobre la compatibilidad del SDK con dispositivos de hardware concretos, consulte el [catálogo de dispositivos Azure Certified for IoT](https://devicecatalog.azure.com/) o el repositorio individual.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="next-steps"></a>Pasos siguientes

Documentos pertinentes relacionados con el desarrollo con los SDK de Azure IoT:

* Información acerca de la [administración de la conectividad y la mensajería confiable](iot-hub-reliability-features-in-sdks.md) mediante los SDK de IoT Hub.
* Aprenda cómo [desarrollar para plataformas móviles](iot-hub-how-to-develop-for-mobile-devices.md) como iOS y Android.
* [Documentación sobre el desarrollo de dispositivos IoT](../iot-develop/about-iot-sdks.md)

Otros temas de referencia en la Guía del desarrollador de IoT Hub son:

* [Puntos de conexión de IoT Hub](iot-hub-devguide-endpoints.md)
* [Lenguaje de consulta de IoT Hub para dispositivos gemelos, trabajos y enrutamiento de mensajes](iot-hub-devguide-query-language.md)
* [Cuotas y limitación](iot-hub-devguide-quotas-throttling.md)
* [Compatibilidad con MQTT de IoT Hub](iot-hub-mqtt-support.md)
* [Referencia de la API REST de IoT Hub](/rest/api/iothub/)

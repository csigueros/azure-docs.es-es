---
title: Determinación de la estrategia de actualización de Azure Percept DK
description: Ventajas y desventajas de las actualizaciones de Azure Percept DK mediante OTA o cable USB. Recomendación para elegir el mejor enfoque de actualización para distintos usuarios.
author: EthanChangAED
ms.author: amiyouss
ms.service: azure-percept
ms.topic: conceptual
ms.date: 08/23/2021
ms.custom: template-concept
ms.openlocfilehash: d5b3f793f6fdf7fe1182c700da8635df9b2ef9a0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124820537"
---
# <a name="determine-your-update-strategy-for-azure-percept-dk"></a>Determinación de la estrategia de actualización de Azure Percept DK

Para mantener actualizado el software Azure Percept DK, Microsoft ofrece dos métodos de actualización para el kit de desarrollo. **Actualización mediante un cable USB** o **actualización por vía inalámbrica (OTA)** .

La actualización mediante un cable USB realiza una instalación limpia en el kit de desarrollo. Las configuraciones existentes y todos los datos de usuario de cada partición se borrarán después de implementar la nueva imagen. Para ello, conecte el kit de desarrollo a un sistema host con un cable USB de tipo C. El sistema host puede ser una máquina Windows o Linux.  También puede usar este método de actualización como restablecimiento de fábrica. Para ello, vuelva a implementar la misma versión exacta en el kit de desarrollo. Vea [Actualización de Azure Percept DK por medio de una conexión por cable USB-C](./how-to-update-via-usb.md) para obtener más información sobre la actualización mediante un cable USB.

La actualización de tipo OTA se basa en el servicio de Azure [Device Update for IoT Hub](../iot-hub-device-update/device-update-resources.md). Para realizar este tipo de actualización, conecte el kit de desarrollo a Azure IoT Hub. Las configuraciones y los datos de usuario se conservarán después de la actualización de OTA. Consulte [Actualización de Azure Percept DK de forma inalámbrica (OTA)](./how-to-update-over-the-air.md) para obtener más información sobre cómo realizar la actualización de tipo OTA.

Compruebe las ventajas y desventajas de la actualización mediante cable USB y OTA y, después, siga las recomendaciones de Microsoft para distintos escenarios.

## <a name="usb-cable-update"></a>Actualización del cable USB

- Ventajas
  - No es necesario conectar el kit de desarrollo a Internet o Azure.
  - La imagen más reciente siempre es aplicable con independencia de la versión de software y firmware que estén cargadas actualmente en el kit de desarrollo.
- Desventajas
  - Vuelve a crear una imagen del dispositivo y quitará las configuraciones y los datos de usuario.
  - Debe volver a ejecutar OOBE y descargar cualquier contenedor no cargado previamente.
  - No se puede realizar de forma remota.

## <a name="ota-update"></a>Actualización de tipo OTA

- Ventajas
  - Conserva los datos de usuario, las configuraciones y los contenedores descargados. El kit de desarrollo seguirá funcionando tal y como estaba después de la OTA.
  - La actualización se puede realizar de forma remota.
  - Se pueden actualizar varios dispositivos similares al mismo tiempo. También se pueden programar las actualizaciones, por ejemplo, durante la noche.
- Desventajas
  - Es posible que haya versiones de detención inmediata que no se puedan omitir. Consulte [Versión de detención inmediata de OTA](./software-releases-over-the-air-updates.md#hard-stop-version-of-ota).
  - El dispositivo se debe conectar a una instancia de IoT Hub, que ha configurado correctamente la característica "Device Update for IoT Hub".
  - No funcionará bien para el cambio a una versión anterior.

> [!IMPORTANT]
> Device Update for IoT Hub no bloquea la implementación de la imagen con una versión anterior al sistema operativo que se ejecuta actualmente. Pero si lo hace en el kit de desarrollo, se pierden los datos y la funcionalidad.

## <a name="microsoft-recommendations"></a>Recomendaciones de Microsoft

|Tipo|Escenario|Update (método)|
|:---:|---|:---:|
|Producción|Mantenga actualizado el kit de desarrollo con la revisión de seguridad y corrección más reciente mientras ya ejecuta la solución o ha implementado en el campo.|OTA|
|Producción/desarrollo|Desempaquetado de un kit de desarrollo nuevo y actualizarlo al software más reciente.|USB|
|Producción/desarrollo|Quiere actualizar a la versión de software más reciente mientras ya se han omitido varias versiones mensuales.|USB|
|Producción/desarrollo|Restablecimiento de fábrica de un kit de desarrollo.|USB|
|Desarrollar|Durante el desarrollo de soluciones, quiere mantener actualizados el sistema operativo del kit de desarrollo y F/W.|USB/OTA|
|Desarrollar|Vaya a cualquier versión específica (anterior) para la investigación o depuración de problemas.|USB|

## <a name="next-steps"></a>Pasos siguientes

Después de decidir el método de actualización que prefiere, visite las páginas siguientes para prepararse para la actualización:

Actualización del cable USB

- [Actualización de Azure Percept DK por medio de una conexión por cable USB-C](./how-to-update-via-usb.md)
- [Versiones del software de Azure Percept DK para la actualización con un cable USB](./software-releases-usb-cable-updates.md)

OTA

- [Actualización de Azure Percept DK de forma inalámbrica (OTA)](./how-to-update-over-the-air.md)
- [Versiones del software de Azure Percept DK para la actualización mediante OTA](./software-releases-over-the-air-updates.md)
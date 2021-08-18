---
title: 'Traducción de protocolos modbus con puertas de enlace: Azure IoT Edge | Microsoft Docs'
description: Permitir dispositivos que usan Modbus TCP para comunicarse con Azure IoT Hub mediante la creación de un dispositivo de puerta de enlace con IoT Edge
author: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: kgremban
ms.openlocfilehash: 5b1c1884dd9b6a37219fece848ebab9e9369906b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121731292"
---
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway"></a>Conexión de dispositivos que usan Modbus TCP a través de una puerta de enlace de dispositivos IoT Edge

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Si desea conectar dispositivos de IoT que usan los protocolos Modbus TCP o RTU con Azure IoT Hub, puede usar un dispositivo de IoT Edge como puerta de enlace. Este dispositivo de puerta de enlace permite leer los datos de los dispositivos de Modbus y, posteriormente, comunica esos datos a la nube mediante un protocolo compatible.

![Los dispositivos de Modbus se conectan con IoT Hub mediante una puerta de enlace de IoT Edge](./media/deploy-modbus-gateway/diagram.png)

En este artículo se explica cómo crear su propia imagen de contenedor para un módulo Modbus (o puede usar un ejemplo creado previamente), y cómo implementarlo después en el dispositivo de IoT Edge que actuará como puerta de enlace.

En este artículo se da por supuesto que va a usar el protocolo Modbus TCP. Para más información acerca de cómo configurar el módulo para que admita la RTU de Modbus, consulte el proyecto [Azure IoT Edge Modbus module](https://github.com/Azure/iot-edge-modbus) (Módulo Modbus de Azure IoT Edge) en GitHub.

## <a name="prerequisites"></a>Requisitos previos

* Un dispositivo de Azure IoT Edge. Para ver un tutorial sobre cómo configurar uno, consulte [Implementación de Azure IoT Edge en Windows](quickstart.md) o [Linux](quickstart-linux.md).
* La cadena de conexión de clave principal para el dispositivo de IoT Edge.
* Un dispositivo de Modbus físico o simulado que admita Modbus TCP. Tendrá que conocer su dirección IPv4.

## <a name="prepare-a-modbus-container"></a>Preparación de un contenedor Modbus

Si desea probar la funcionalidad de puerta de enlace de Modbus, Microsoft tiene un módulo de ejemplo que puede usar. Puede acceder al módulo desde Azure Marketplace, [Modbus](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot.edge-modbus?tab=Overview), o con el URI de la imagen, `mcr.microsoft.com/azureiotedge/modbus:1.0`.

Si desea crear su propio módulo y personalizarlo para su entorno, hay un proyecto de código abierto llamado [Azure IoT Edge Modbus module](https://github.com/Azure/iot-edge-modbus) (Módulo Modbus de Azure IoT Edge) en GitHub. Siga las instrucciones de ese proyecto para crear su propia imagen de contenedor. Para crear su propia imagen de contenedor, consulte [Desarrollar módulos de C# en Visual Studio](./how-to-visual-studio-develop-module.md) o [Desarrollar módulos en Visual Studio Code](how-to-vs-code-develop-module.md). Esos artículos proporcionan instrucciones sobre cómo crear nuevos módulos y publicar imágenes de contenedor en un registro.

## <a name="try-the-solution"></a>Prueba de la solución

En esta sección se describe cómo implementar el módulo Modbus de ejemplo de Microsoft en el dispositivo IoT Edge.

1. En [Azure Portal](https://portal.azure.com/), vaya hasta la instancia de IoT Hub.

2. Vaya a **IoT Edge** y haga clic en el dispositivo IoT Edge.

3. Seleccione **Set modules** (Establecer módulos).

4. En la sección **Módulos de IoT Edge**, agregue el módulo de Modbus:

   1. Haga clic en la lista desplegable **Agregar** y seleccione **Módulo de Marketplace**.
   2. Busque `Modbus` y seleccione el **módulo Modbus TCP** de Microsoft.
   3. El módulo se configura automáticamente para IoT Hub y aparece en la lista de módulos de IoT Edge. Las rutas también se configuran automáticamente. Seleccione **Revisar + crear**.
   4. Revise el manifiesto de implementación y seleccione **Crear**.

5. Seleccione el módulo Modbus, `ModbusTCPModule`, en la lista y seleccione la pestaña **Configuración de módulos gemelos**. El código JSON necesario para las propiedades deseadas del módulo gemelo se rellena automáticamente.

6. Busque la propiedad **SlaveConnection** en el archivo JSON y establezca su valor en la dirección IPv4 del dispositivo Modbus.

7. Seleccione **Actualizar**.

8. Seleccione **Revisar y crear**, revise la implementación y, después, seleccione **Crear**.

9. Vuelva a la página de detalles del dispositivo y seleccione **Actualizar**. Verá que el nuevo módulo `ModbusTCPModule` se ejecuta junto con el entorno de ejecución de IoT Edge.

## <a name="view-data"></a>Visualización de datos

Vea los datos que entran a través del módulo Modbus:

```cmd/sh
iotedge logs modbus
```

Puede ver también la telemetría que el dispositivo envía mediante la [extensión Azure IoT Hub para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (anteriormente la extensión Azure IoT Toolkit).

## <a name="next-steps"></a>Pasos siguientes

* Para obtener más información sobre cómo pueden actuar los dispositivos IoT Edge como puertas de enlace, consulte [Creación de un dispositivo IoT Edge que actúa como una puerta de enlace transparente](./how-to-create-transparent-gateway.md).
* Para obtener más información sobre cómo funcionan los módulos de IoT Edge, consulte [Información de los módulos de Azure IoT Edge](iot-edge-modules.md).
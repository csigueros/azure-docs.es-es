---
title: Archivo de inclusión
description: archivo de inclusión
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 11/06/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 78ad23713d1fc58d55696502dc9ff780847a8357
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/21/2021
ms.locfileid: "112414885"
---
<!-- put the ## header in the file that includes this file -->

En esta sección, creará una identidad de dispositivo en el registro de identidades del centro de IoT. No se puede conectar un dispositivo a un centro a menos que tenga una entrada en el registro de identidades. Vea la [guía para desarrolladores de IoT Hub](../articles/iot-hub/iot-hub-devguide-identity-registry.md#identity-registry-operations) para más información.

1. En el menú de navegación del centro de IoT, abra **Dispositivos IoT** y, después, seleccione **Nuevo** para agregar un dispositivo nuevo en el centro de IoT.

    ![Creación de la identidad del dispositivo en el portal](./media/iot-hub-include-create-device/create-identity-portal-vs2019.png)

1. En **Crear un dispositivo**, proporcione un nombre para el dispositivo nuevo, como **IdDeMiDispositivo** y seleccione **Guardar**. Esta acción crea una nueva identidad de dispositivo para su centro de IoT. Deje activada la opción **Generar claves automáticamente** para que las claves principal y secundaria se generen de forma automática.

   ![Agregar un nuevo dispositivo](./media/iot-hub-include-create-device/create-a-device-vs2019.png)

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

1. Después de crear el dispositivo, ábralo desde la lista del panel **Dispositivos de IoT**. Copie la **Cadena de conexión principal**. El código del dispositivo usa esta cadena de conexión para comunicarse con el centro. 

    De forma predeterminada, las claves y las cadenas de conexión se enmascaran porque son información confidencial. Si hace clic en el icono de ojo, se hacen visibles, como se muestra en la imagen siguiente. No es necesario mostrarlas para copiarlas con el botón Copiar.

    ![Cadena de conexión de dispositivo](./media/iot-hub-include-create-device/device-details-vs2019.png)

> [!NOTE]
> El registro de identidades de IoT Hub solo almacena identidades de dispositivos para permitir el acceso seguro a IoT Hub. Almacena las claves y los identificadores de dispositivo para usarlos como credenciales de seguridad, y un indicador de habilitado o deshabilitado que permite deshabilitar el acceso a un dispositivo individual. Si la aplicación necesita almacenar otros metadatos específicos del dispositivo, debe usar un almacén específico de la aplicación. Consulte la [guía de desarrolladores de IoT Hub](../articles/iot-hub/iot-hub-devguide-identity-registry.md) para obtener más información.

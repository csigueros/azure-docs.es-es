---
title: 'Tutorial: Conexión de una aplicación cliente genérica a Azure IoT Central | Microsoft Docs'
description: En este tutorial se muestra cómo puede conectar un dispositivo que ejecuta una aplicación cliente de C, C#, Java, JavaScript o Python a la aplicación de Azure IoT Central. La plantilla de dispositivo generada automáticamente se modifica mediante la adición de vistas que permiten a un operador interactuar con un dispositivo conectado.
author: dominicbetts
ms.author: dobett
ms.date: 08/31/2021
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom:
- mqtt
- device-developer
zone_pivot_groups: programming-languages-set-twenty-six
ms.openlocfilehash: 7be12549d6e4afaa981a3d3dc83ad817ff5d9c9d
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2021
ms.locfileid: "123471325"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application"></a>Tutorial: Creación y conexión de un aplicación cliente a la aplicación de Azure IoT Central

En este tutorial se muestra cómo puede conectar una aplicación cliente a la aplicación de Azure IoT Central. La aplicación simula el comportamiento de un dispositivo de control de la temperatura. Cuando la aplicación se conecta a IoT Central, envía el identificador del modelo de dispositivo de control de temperatura. IoT Central usa el identificador de modelo para recuperar el modelo de dispositivo y crear una plantilla de dispositivo automáticamente. Agregue personalizaciones y vistas a la plantilla de dispositivo para permitir que un operador interactúe con un dispositivo.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear y ejecutar el código del dispositivo y ver si se conecta a la aplicación de IoT Central.
> * Ver los datos de telemetría simulados que envía el dispositivo.
> * Agregar vistas personalizadas a una plantilla de dispositivo.
> * Publicar la plantilla de dispositivo.
> * Usar una vista para administrar las propiedades del dispositivo.
> * Llamar a un comando para controlar el dispositivo.

:::zone pivot="programming-language-ansi-c"

[!INCLUDE [iot-central-connect-device-c](../../../includes/iot-central-connect-device-c.md)]

:::zone-end

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-central-connect-device-csharp](../../../includes/iot-central-connect-device-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-central-connect-device-java](../../../includes/iot-central-connect-device-java.md)]

:::zone-end

:::zone pivot="programming-language-javascript"

[!INCLUDE [iot-central-connect-device-nodejs](../../../includes/iot-central-connect-device-nodejs.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-central-connect-device-python](../../../includes/iot-central-connect-device-python.md)]

:::zone-end

## <a name="view-raw-data"></a>Visualización de datos sin procesar

Puede usar la vista **Datos sin procesar** para examinar los datos sin procesar que el dispositivo envía a IoT Central:

:::image type="content" source="media/tutorial-connect-device/raw-data.png" alt-text="Vista de datos sin procesar":::

En esta vista, puede seleccionar las columnas que se van a mostrar y establecer el intervalo de tiempo que desea ver. La columna **Datos no modelados** muestra datos del dispositivo que no coinciden con ninguna de las definiciones de telemetría o propiedades de la plantilla de dispositivo.

## <a name="clean-up-resources"></a>Limpieza de recursos

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Pasos siguientes

Si prefiere continuar con el conjunto de tutoriales de IoT Central y obtener más información sobre la creación de una solución de IoT Central, consulte:

> [!div class="nextstepaction"]
> [Creación de una plantilla de dispositivo de puerta de enlace](./tutorial-define-gateway-device-type.md)

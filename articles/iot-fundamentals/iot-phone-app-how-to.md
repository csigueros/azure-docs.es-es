---
title: Uso del smartphone como dispositivo de Azure IoT
description: Una guía paso a paso en la que se muestra cómo convertir el smartphone en un dispositivo IoT mediante la aplicación Azure IoT Plug and Play.
author: dominicbetts
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: how-to
ms.date: 05/27/2021
ms.author: dobett
ms.openlocfilehash: 0daffdab23d5da069598512f1934cfdc04bf01b3
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112306139"
---
# <a name="how-to-turn-your-smartphone-into-an-iot-device"></a>Procedimiento para convertir el smartphone en un dispositivo IoT

Una solución de Azure IoT le permite conectar los dispositivos IoT a un servicio IoT basado en la nube. Los dispositivos envían telemetría, como la temperatura y la humedad, y responden a comandos como el reinicio y el intervalo de entrega de cambios. Los dispositivos también pueden sincronizar su estado interno con el servicio, y compartir propiedades como el modelo y el sistema operativo del dispositivo.

La aplicación de teléfono IoT Plug and Play le permite empezar a explorar rápidamente las funcionalidades de Azure IoT sin necesidad de configurar un dispositivo IoT dedicado.

## <a name="azure-iot-plug-and-play-app"></a>Aplicación Azure IoT Plug and Play

Para empezar a trabajar rápidamente, en este artículo se usa una aplicación de smartphone como un dispositivo IoT. La aplicación envía la telemetría recopilada de los sensores del teléfono, responde a los comandos invocados desde el servicio y notifica los valores de las propiedades.

Puede usar esta aplicación de smartphone para lo siguiente:

- Explorar un escenario básico de IoT.
- Administrar e interactuar con el teléfono de forma remota.
- Probar la configuración.
- Como punto de partida para el desarrollo de dispositivos personalizados.

## <a name="install-the-app"></a>Instalación de la aplicación

[!INCLUDE [iot-phoneapp-install](../../includes/iot-phoneapp-install.md)]

## <a name="app-features"></a>Funciones de la aplicación

### <a name="connect"></a>Conectar

Puede conectarse a una aplicación de IoT Central si escanea un código QR en IoT Central.

Para obtener más información, vea [Conexión de la aplicación](#connect-the-app) más adelante en esta guía.

### <a name="telemetry"></a>Telemetría

La aplicación recopila datos de sensores en el teléfono para enviarlos como telemetría al servicio IoT que use. Los datos de los sensores se agregan cada cinco segundos de forma predeterminada, pero puede cambiarlo en la página de configuración de la aplicación:

:::image type="content" source="media/iot-phone-app-how-to/telemetry.png" alt-text="Captura de pantalla de la página de telemetría en la aplicación de smartphone.":::

En la captura de pantalla siguiente se muestra una vista de dispositivo en IoT Central con parte de la telemetría del dispositivo:

:::image type="content" source="media/iot-phone-app-how-to/central-telemetry.png" alt-text="Captura de pantalla de la telemetría del dispositivo en IoT Central.":::

### <a name="properties"></a>Propiedades

La aplicación notifica el estado del dispositivo, como su modelo y el fabricante. También hay una propiedad editable que puede modificar y ver la sincronización de cambios en la solución de Azure IoT:

:::image type="content" source="media/iot-phone-app-how-to/properties.png" alt-text="Captura de pantalla en la que se muestra la página de propiedades en la aplicación del dispositivo móvil.":::

En la captura de pantalla siguiente se muestra la propiedad que se puede escribir en IoT Central después de enviarla al dispositivo:

:::image type="content" source="media/iot-phone-app-how-to/central-writable-property.png" alt-text="Captura de pantalla en la que se muestra la propiedad que se puede escribir en IoT Central.":::

### <a name="image-upload"></a>Carga de imágenes

Tanto en IoT Central como en IoT Hub se permite la carga de archivos a Azure Storage desde un dispositivo. La aplicación de smartphone le permite cargar una imagen desde el dispositivo.

Para obtener más información sobre cómo configurar el servicio a fin de admitir cargas de archivos desde un dispositivo, vea:

- [Carga de archivos de un dispositivo a la nube con IoT Hub](../iot-hub/iot-hub-csharp-csharp-file-upload.md).
- [Carga de archivos de un dispositivo a la nube con IoT Central](../iot-central/core/howto-configure-file-uploads.md).

:::image type="content" source="media/iot-phone-app-how-to/image-upload.png" alt-text="Captura de pantalla en la que se muestra la página de carga de imágenes en la aplicación de smartphone.":::

### <a name="logs"></a>Registros

La aplicación de smartphone escribe eventos en un archivo de registro local que puede ver desde dentro de la aplicación. Use el archivo de registro para solucionar problemas y comprender mejor lo que hace la aplicación:

:::image type="content" source="media/iot-phone-app-how-to/logs.png" alt-text="Captura de pantalla en la que se muestra la página de registro en la aplicación de smartphone.":::

### <a name="settings"></a>Configuración

La página de configuración de la aplicación le permite:

- Conectar la aplicación a la solución de Azure IoT.
- Revisar la información de registro de dispositivos actual.
- Restablecer la aplicación mediante el borrado de los datos almacenados.
- Personalizar la apariencia de la aplicación.
- Establecer la frecuencia con la que la aplicación envía telemetría al servicio IoT.

:::image type="content" source="media/iot-phone-app-how-to/settings.png" alt-text="Captura de pantalla de la página de configuración en la aplicación de smartphone.":::

## <a name="connect-the-app"></a>Conexión de la aplicación

### <a name="prerequisites"></a>Requisitos previos

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

<!-- To do: does this need an app template? -->
Creación de una aplicación de IoT Central. Para más información, consulte [Creación de una aplicación de Azure IoT Central](../iot-central/core/howto-create-iot-central-application.md).

### <a name="register-a-device"></a>Registrar un dispositivo

Antes de conectar la aplicación de teléfono, debe registrar un dispositivo en la aplicación de IoT Central. Al crear un registro de dispositivo, IoT Central genera la información de conexión del dispositivo.

Para registrar el dispositivo en IoT Central:

1. Inicie sesión en la aplicación de IoT Central y vaya a la página **Dispositivos**.

1. Seleccione **Crear un dispositivo**.

1. En la página **Crear un dispositivo**, seleccione **Crear**:

    :::image type="content" source="media/iot-phone-app-how-to/iot-central-create-device.png" alt-text="Captura de pantalla en la que se muestra cómo crear un dispositivo en IoT Central.":::

1. En la lista de dispositivos, haga clic en el nombre del lista y luego seleccione **Conectar**. En la página  **Conexión del dispositivo** puede ver el código QR que escaneará en la aplicación de smartphone:

    :::image type="content" source="media/iot-phone-app-how-to/device-connection-qr-code.png" alt-text="Captura de pantalla en la que se muestra la página de conexión del dispositivo con el código QR.":::

### <a name="connect-the-device"></a>Conexión del dispositivo

Después de registrar el dispositivo en IoT Central, puede conectar la aplicación de smartphone si escanea el código QR. Para conectar la aplicación:

1. Abra la aplicación **IoT PnP** en el smartphone.

1. En la página principal, seleccione **Scan QR code** (Escanear código QR). Apunte la cámara del teléfono al código QR. A continuación, espere unos segundos mientras se establece la conexión.

1. En la página de telemetría de la aplicación, puede ver los datos que la aplicación envía a IoT Central. En la página de registros, puede ver el dispositivo que se conecta y varios mensajes de inicialización.

1. En la página **Configuración > Registro**, puede ver el ámbito de identificador y el identificador de dispositivo que la aplicación ha usado para conectarse a IoT Central.

Para obtener más información sobre cómo se conectan los dispositivos a IoT Central, vea [Conexión a Azure IoT Central](../iot-central/core/concepts-get-connected.md).

### <a name="verify-the-connection"></a>Comprobación de la conexión

Para ver los datos que el dispositivo envía en la aplicación de IoT Central:

1. Inicie sesión en la aplicación de IoT Central y vaya a la página **Dispositivos**. El dispositivo se ha asignado automáticamente a la plantilla de dispositivo **Smartphone**.

    > [!TIP]
    > Es posible que tenga que actualizar la página en el explorador web para ver cuándo se asigna el dispositivo a la plantilla dispositivo **Smartphone**.

1. En la lista de dispositivos, haga clic en el nombre del lista y luego seleccione **Información general**. En la página **Información general** se muestra la telemetría de los sensores del smartphone:

    :::image type="content" source="media/iot-phone-app-how-to/smartphone-overview.png" alt-text="Captura de pantalla de la página de información general del dispositivo en IoT Central en la que se muestra la telemetría de los sensores del smartphone.":::

1. Vea la página **Acerca de** para ver las propiedades que ha enviado el dispositivo.

1. En la página **Comandos** ejecute el comando **LightOn** para encender la linterna del teléfono.

> [!TIP]
> En la página **Datos sin procesar** se muestran todos los datos procedentes del dispositivo.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha conectado la aplicación de smartphone a IoT Central, un paso siguiente sugerido es obtener más información sobre [IoT Central](../iot-central/core/overview-iot-central.md).

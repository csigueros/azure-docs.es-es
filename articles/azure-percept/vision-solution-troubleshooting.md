---
title: Solución de problemas de Azure Percept Vision y de los módulos de visión
description: Vea sugerencias para solucionar algunos de los problemas más comunes que se encuentran en las experiencias de creación de prototipos de inteligencia artificial de visión.
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/29/2021
ms.custom: template-how-to
ms.openlocfilehash: 79a9147fde164a672d310a7dea6fb6166ed3a1f9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751374"
---
# <a name="vision-solution-troubleshooting"></a>Solución de problemas de las soluciones de visión

En este artículo se proporciona información sobre cómo solucionar problemas de las soluciones de visión sin código en Azure Percept Studio.

## <a name="delete-a-vision-project"></a>Eliminación de un proyecto de visión

1. Vaya a la página [Proyectos de Custom Vision](https://www.customvision.ai/projects).

1. Mantenga el mouse sobre el proyecto que quiere eliminar y seleccione el icono de la papelera para eliminar el proyecto.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-delete-project.png" alt-text="Captura de pantalla que muestra la página Proyectos de Custom Vision con el icono de eliminación resaltado.":::

## <a name="check-which-modules-are-on-a-device"></a>Comprobación de qué módulos hay en un dispositivo

1. Vaya a [Azure Portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home).

1. Seleccione el icono de **Iot Hub**.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-hub-2-inline.png" alt-text="Captura de pantalla que muestra la página principal de Azure Portal con el icono de Iot Hub resaltado." lightbox= "./media/vision-solution-troubleshooting/vision-iot-hub-2.png":::

1. Seleccione la instancia de Iot Hub al que está conectado el dispositivo de destino.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-hub.png" alt-text="Captura de pantalla que muestra una lista de centros de IoT.":::

1. Seleccione **IoT Edge** y elija su dispositivo en la pestaña **Id. de dispositivo**.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-edge.png" alt-text="Captura de pantalla que muestra la página principal de IoT Edge.":::

1. Los módulos de dispositivo aparecen en una lista en la pestaña **Módulos**.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-device-modules-inline.png" alt-text="Captura de pantalla que muestra la página de IoT Edge del dispositivo seleccionado con el contenido de la pestaña Módulos." lightbox= "./media/vision-solution-troubleshooting/vision-device-modules.png":::

## <a name="delete-a-device"></a>Eliminar un dispositivo

1. Vaya a [Azure Portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home).

1. Seleccione el icono de **Iot Hub**.

1. Seleccione la instancia de Iot Hub al que está conectado el dispositivo de destino.

1. Seleccione **IoT Edge** y la casilla situada junto al identificador del dispositivo de destino. Seleccione **Eliminar** para eliminar el dispositivo.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-delete-device.png" alt-text="Captura de pantalla que muestra el botón Eliminar resaltado en la página principal de IoT Edge.":::

## <a name="check-the-runtime-status-of-azureeyemodule"></a>Comprobar el estado de runtime de azureeyemodule

Si hay un problema con **WebStreamModule**, asegúrese de que **azureeyemodule**, que realiza la inferencia de modelos de visión, se esté ejecutando. Para comprobar el estado del entorno de ejecución:

1. Vaya a [Azure portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home) y a **Todos los recursos** >  *\<your IoT hub>*  > **IoT Edge** >  *\<your device ID>* . 
1. Seleccione la pestaña **Módulos** para ver el estado del entorno de ejecución de todos los módulos instalados.

   :::image type="content" source="./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page-inline.png" alt-text="Captura de pantalla que muestra la pantalla de estado del entorno de ejecución del módulo del dispositivo." lightbox= "./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page.png":::

1. Si el estado del entorno de ejecución de **azureeyemodule** no aparece como **En ejecución**, seleccione **Establecer módulos** > **azureeyemodule**. 
1. En la página **Configuración del módulo**, establezca **Estado deseado** como **En ejecución** y seleccione **Actualizar**.

    :::image type="content" source="./media/vision-solution-troubleshooting/firmware-desired-status-stopped.png" alt-text="Captura de pantalla que muestra la pantalla Configuración del módulo.":::

## <a name="change-how-often-messages-are-sent-from-the-azureeyemodule"></a>Cambio de la frecuencia con la que se envían los mensajes desde azureeyemodule

El nivel de suscripción puede afectar al número de mensajes que se pueden enviar desde el dispositivo a IoT Hub. Por ejemplo, el nivel Gratis limitará el número de mensajes a 8000 al día. Una vez alcanzado ese límite, azureeyemodule dejará de funcionar y es posible que reciba este error:

|Mensaje de error|
|------|
|*Total number of messages on IotHub 'xxxxxxxxx' exceeded the allocated quota. Max allowed message count: '8000', current message count: 'xxxx'. Send and Receive operations are blocked for this hub until the next UTC day. Consider increasing the units for this hub to increase the quota.* (El número total de mensajes en IotHub "xxxxxxxxx" superó la cuota asignada. Número máximo de mensajes permitido: "8000", recuento de mensajes actual: "xxxx". Las operaciones de envío y recepción se bloquean para este centro hasta el siguiente día UTC. Considere la posibilidad de aumentar las unidades de este centro para aumentar la cuota).|

Con el módulo gemelo azureeyemodule, es posible cambiar la velocidad del intervalo de la frecuencia con la que se envían los mensajes. El valor especificado para la velocidad del intervalo indica la frecuencia con la que se envía cada mensaje, en milisegundos. Cuanto mayor sea el número, más tiempo habrá entre cada mensaje. Por ejemplo, si establece la velocidad del intervalo en 12 000, significa que se enviará un mensaje cada 12 segundos. En el caso de un modelo que se ejecuta durante todo el día, esta velocidad genera 7200 mensajes al día, lo cual se encuentra en el límite del nivel Gratis. El valor que elija dependerá de la capacidad de respuesta que requiera que tenga el modelo de visión.

> [!NOTE]
> Cambiar la velocidad del intervalo de mensajes no afecta al tamaño de cada mensaje. El tamaño del mensaje depende de una serie de factores distintos, como el tipo de modelo y el número de objetos que se detectan en cada mensaje. Por lo tanto, es difícil determinar el tamaño del mensaje.

Siga estos pasos para actualizar el intervalo de mensajes:

1. Inicie sesión en [Azure Portal](https://ms.portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod#home) y abra **Todos los recursos**.

1. En la página **Todos los recursos**, seleccione el nombre del centro de IoT que se aprovisionó en el kit de desarrollo durante la configuración.

1. En el lado izquierdo de la página **IoT Hub**, en **Administración automática de dispositivos**, seleccione **IoT Edge**. En la página de dispositivos de IoT Edge, busque el identificador de dispositivo de su kit de desarrollo. Seleccione el identificador de dispositivo de su kit de desarrollo para abrir su página de dispositivo de IoT Edge.

1. Seleccione **azureeyemodule** en la pestaña **Módulos**.

1. En la página **azureeyemodule**, abra **Identidad de módulo gemela**.

    :::image type="content" source="./media/vision-solution-troubleshooting/module-page-inline.png" alt-text="Captura de pantalla de una página de módulos." lightbox= "./media/vision-solution-troubleshooting/module-page.png":::

1. Desplácese hasta **Propiedades**.
1. Busque **TelemetryInterval** y reemplácelo por **TelemetryIntervalNeuralNetworkMs**.

    :::image type="content" source="./media/vision-solution-troubleshooting/module-identity-twin-inline-02.png" alt-text="Captura de pantalla de las propiedades de Identidad de módulo gemela." lightbox= "./media/vision-solution-troubleshooting/module-identity-twin.png":::

1. Actualice el valor **TelemetryIntervalNeuralNetworkMs** (TelemetryIntervalNeuralNetworkMs) al valor necesario.

1. Seleccione el icono **Save** (Guardar).

## <a name="view-device-rtsp-video-stream"></a>Visualización de la secuencia de vídeo RTSP del dispositivo

Vea la secuencia de vídeo RTSP de su dispositivo en [Azure Percept Studio](./how-to-view-video-stream.md) o [VLC Media Player](https://www.videolan.org/vlc/index.html).

Para abrir la secuencia RTSP en VLC Media Player, vaya a **Media (Medios)**  > **Open network stream (Abrir secuencia de red)**  > **rtsp://[dirección IP del dispositivo]:8554/result**.

Si un cuadro gris bloquea parcialmente la secuencia RTSP, es posible que esté intentando verla con una conexión de red deficiente. Compruebe que la conexión tiene suficiente ancho de banda para las secuencias de vídeo.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre la solución de problemas de la instancia de Azure Percept DK, consulte la [guía general de solución de problemas](./troubleshoot-dev-kit.md).
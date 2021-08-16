---
title: Solución de problemas de Azure Percept Vision y de los módulos de visión
description: Vea sugerencias para solucionar algunos de los problemas más comunes que se encuentran en las experiencias de creación de prototipos de inteligencia artificial de visión.
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/29/2021
ms.custom: template-how-to
ms.openlocfilehash: 28ac7d0d7079d8ba8c9483e7da816430295941c9
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/11/2021
ms.locfileid: "112027864"
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

## <a name="eye-module-troubleshooting-tips"></a>Sugerencias para la solución de problemas del módulo de visión

Las sugerencias para solucionar problemas siguientes proporcionan ayuda para algunos de los problemas más comunes que se encuentran en las experiencias de creación de prototipos de inteligencia artificial de visión.

### <a name="check-the-runtime-status-of-azureeyemodule"></a>Comprobar el estado de runtime de azureeyemodule

Si hay un problema con **WebStreamModule**, asegúrese de que **azureeyemodule**, que realiza la inferencia de modelos de visión, se esté ejecutando. Para comprobar el estado del entorno de ejecución:

1. Vaya a [Azure portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home) y a **Todos los recursos** >  *\<your IoT hub>*  > **IoT Edge** >  *\<your device ID>* . 
1. Seleccione la pestaña **Módulos** para ver el estado del entorno de ejecución de todos los módulos instalados.

   :::image type="content" source="./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page-inline.png" alt-text="Captura de pantalla que muestra la pantalla de estado del entorno de ejecución del módulo del dispositivo." lightbox= "./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page.png":::

1. Si el estado del entorno de ejecución de **azureeyemodule** no aparece como **En ejecución**, seleccione **Establecer módulos** > **azureeyemodule**. 
1. En la página **Configuración del módulo**, establezca **Estado deseado** como **En ejecución** y seleccione **Actualizar**.

    :::image type="content" source="./media/vision-solution-troubleshooting/firmware-desired-status-stopped.png" alt-text="Captura de pantalla que muestra la pantalla Configuración del módulo.":::

### <a name="update-telemetryintervalneuralnetworkms"></a>Actualizar TelemetryIntervalNeuralNetworkMs

Si ve el siguiente error de limitación de recuento, deberá actualizar el valor de TelemetryIntervalNeuralNetworkMs en la configuración gemela del módulo azureeyemodule.

|Mensaje de error|
|------|
|El número total de mensajes en IotHub 'XXXXXXXXX' superó la cuota asignada. Número máximo de mensajes permitidos: ' 8000', número de mensajes actual: 'XXXX'. Las operaciones de envío y recepción se bloquean para este centro hasta el siguiente día UTC. Considere la posibilidad de aumentar las unidades de este centro para aumentar la cuota.|

TelemetryIntervalNeuralNetworkMs determina con qué frecuencia se envían los mensajes desde la red neuronal. Los mensajes se envían en milisegundos. Las suscripciones a Azure tienen un número limitado de mensajes por día.

La cantidad del mensajes se basa en el nivel de suscripción. Si se encuentra bloqueado debido a que ha enviado demasiados mensajes, aumente la cantidad a un número mayor. Una cantidad de 12 000 representa un mensaje cada 12 segundos. Esta cantidad proporciona 7200 mensajes al día, valor que está por debajo del límite de 8000 mensajes de la suscripción gratuita.

Para actualizar el valor TelemetryIntervalNeuralNetworkMs:

1. Inicie sesión en [Azure Portal](https://ms.portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod#home) y abra **Todos los recursos**.

1. En la página **Todos los recursos**, seleccione el nombre del centro de IoT que se aprovisionó en el kit de desarrollo durante la configuración.

1. En el lado izquierdo de la página **IoT Hub**, en **Administración automática de dispositivos**, seleccione **IoT Edge**. En la página de dispositivos de IoT Edge, busque el identificador de dispositivo de su kit de desarrollo. Seleccione el identificador de dispositivo de su kit de desarrollo para abrir su página de dispositivo de IoT Edge.

1. Seleccione **azureeyemodule** en la pestaña **Módulos**.

1. En la página **azureeyemodule**, abra **Identidad de módulo gemela**.

    :::image type="content" source="./media/vision-solution-troubleshooting/module-page-inline.png" alt-text="Captura de pantalla de una página de módulos." lightbox= "./media/vision-solution-troubleshooting/module-page.png":::

1. Desplácese hacia **propiedades**. Las propiedades **En ejecución** y **Registro** no están activas en este momento.

    :::image type="content" source="./media/vision-solution-troubleshooting/module-identity-twin-inline.png" alt-text="Captura de pantalla de las propiedades de Identidad de módulo gemela." lightbox= "./media/vision-solution-troubleshooting/module-identity-twin.png":::

1. Actualice el valor **telemetryIntervalNeuralNetworkMs** como quiera y seleccione el icono **Guardar**.

## <a name="view-device-rtsp-video-stream"></a>Visualización de la secuencia de vídeo RTSP del dispositivo

Vea la secuencia de vídeo RTSP de su dispositivo en [Azure Percept Studio](./how-to-view-video-stream.md) o [VLC Media Player](https://www.videolan.org/vlc/index.html).

Para abrir la secuencia RTSP en VLC Media Player, vaya a **Media (Medios)**  > **Open network stream (Abrir secuencia de red)**  > **rtsp://[dirección IP del dispositivo]:8554/result**.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre la solución de problemas de la instancia de Azure Percept DK, consulte la [guía general de solución de problemas](./troubleshoot-dev-kit.md).
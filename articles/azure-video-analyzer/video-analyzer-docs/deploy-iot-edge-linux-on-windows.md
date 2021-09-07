---
title: 'Implementación en IoT Edge para Linux en Windows: Azure'
description: En este artículo se proporcionan instrucciones sobre cómo realizar la implementación en un dispositivo de IoT Edge para Linux en Windows.
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: e80721375cf4b0c912fe47ec76c2cebe92359f90
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121743646"
---
# <a name="deploy-to-an-iot-edge-for-linux-on-windows-eflow-device"></a>Implementación en un dispositivo de IoT Edge para Linux en Windows (EFLOW)

En este artículo, aprenderá a implementar Azure Video Analyzer en un dispositivo perimetral que tiene [IoT Edge para Linux en Windows (EFLOW)](../../iot-edge/iot-edge-for-linux-on-windows.md). Una vez que haya terminado de realizar los pasos que se indican en este documento, podrá ejecutar una [canalización](pipeline.md) que detecte el movimiento en un vídeo y emita esos eventos a IoT Hub en la nube. Después, puede desactivar la canalización para escenarios avanzados y aportar la potencia de Azure Video Analyzer a su dispositivo de IoT Edge basado en Windows.

## <a name="prerequisites"></a>Requisitos previos 

* Una cuenta de Azure que tenga una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), en caso de que aún no lo haya hecho.

* [Visual Studio Code](https://code.visualstudio.com/) en la máquina de desarrollo. Asegúrese de tener la [extensión Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* Lea [¿Qué es EFLOW?](../../iot-edge/iot-edge-for-linux-on-windows.md)

## <a name="deployment-steps"></a>Pasos de implementación

A continuación se describe el flujo general del documento y en cinco pasos sencillos estará listo para ejecutar Azure Video Analyzer en un dispositivo Windows con EFLOW:

![Diagrama de IoT Edge para Linux en Windows (EFLOW).](./media/deploy-iot-edge-linux-on-windows/eflow.png)

1. [Instale EFLOW](../../iot-edge/how-to-install-iot-edge-on-windows.md) en el dispositivo Windows mediante PowerShell.


1. Una vez configurado EFLOW, escriba el comando `Connect-EflowVm` en PowerShell (con privilegios administrativos) para conectarse. Esto mostrará un terminal de Bash en PowerShell para controlar la VM de EFLOW, donde puede ejecutar comandos de Linux, incluidas utilidades como Top y Nano. 

    > [!TIP] 
    > Para salir de la VM de EFLOW, escriba `exit` en el terminal.

1. Inicie sesión en la VM de EFLOW a través de PowerShell y escriba el comando siguiente:

    `bash -c "$(curl -sL https://aka.ms/ava-edge/prep_device)"`

    El módulo de Live Video Analytics se ejecuta en el dispositivo perimetral con cuentas de usuario locales sin privilegios. Además, necesita ciertas carpetas locales para almacenar los datos de configuración de la aplicación. Por último, para esta guía paso a paso, se emplea un [simulador RTSP](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555) que retransmite una fuente de vídeo en tiempo real a un módulo de AVA para su análisis. Este simulador toma como entrada archivos de vídeo grabados previamente desde un directorio de entrada. 

    El script prep-device usado anteriormente automatiza estas tareas, por lo que puede ejecutar un comando y hacer que todas las carpetas de entrada y configuración pertinentes, archivos de entrada de vídeo y cuentas de usuario con privilegios se creen sin problemas. Una vez que el comando finalice correctamente, debería ver las siguientes carpetas creadas en el dispositivo perimetral. 

    * `/home/localedgeuser/samples`
    * `/home/localedgeuser/samples/input`
    * `/var/lib/videoanalyzer`
    * `/var/media`

    Observe los archivos de vídeo (*.mkv) de la carpeta /home/lvaedgeuser/samples/input, ya que le servirán como los archivos de entrada que se van a analizar. 
1. Ahora que tiene el dispositivo perimetral configurado, registrado en el centro de conectividad y ejecutándose correctamente con las estructuras de carpetas correctas creadas, el paso siguiente consiste en configurar los siguientes recursos adicionales de Azure e implementar el módulo de AVA. La siguiente plantilla de implementación se ocupará de la creación de recursos:

    [![Implementación en Azure](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)
    
    El proceso de implementación tardará unos 20 minutos. Al finalizar, tendrá determinados recursos de Azure implementados en la suscripción de Azure, entre los que se incluyen:

    * Cuenta de Video Analyzer: este servicio en la nube se usa para registrar el módulo perimetral Video Analyzer y para reproducir vídeo grabado y análisis de vídeo.
    * Cuenta de almacenamiento: para almacenar vídeo grabado y análisis de vídeo.
    * Identidad administrada: es la identidad administrada asignada por el usuario que se usa para administrar el acceso a la cuenta de almacenamiento anterior.
    * IoT Hub: funciona como un centro de mensajes común para la comunicación bidireccional entre la aplicación de IoT, los módulos de IoT Edge y los dispositivos que administra.

    En la plantilla, cuando se le pregunte si necesita un dispositivo perimetral, elija la opción "Use an existing edge device" (Usar un dispositivo perimetral existente), ya que creó el dispositivo y la instancia de IoT Hub anteriormente. También se le pedirán el nombre de la instancia de IoT Hub y el identificador del dispositivo de IoT Edge en los pasos siguientes.  
    
    ![Uso del dispositivo existente](./media/deploy-iot-edge-linux-on-windows/use-existing-device.png) 

    Una vez finalizado, puede volver a iniciar sesión en la VM de EFLOW y ejecutar el siguiente comando.

    **`sudo iotedge list`**

    Aparecerán los cuatro módulos siguientes implementados y en ejecución en el dispositivo perimetral. Tenga en cuenta que el script de creación de recursos implementa el módulo de AVA junto con los módulos de IoT Edge (edgeAgent y edgeHub) y un módulo de simulador RTSP para proporcionar la fuente de vídeo RTSP simulada.
    
    ![Módulos implementados](./media/vscode-common-screenshots/avaedge-module.png)
1. Con los módulos implementados y configurados, está listo para ejecutar la primera canalización de AVA en EFLOW. Realice los siguientes pasos para ejecutar una canalización de detección de movimiento simple como se muestra a continuación y visualizar los resultados:

    ![Video Analyzer basado en la detección de movimiento](./media/get-started-detect-motion-emit-events/motion-detection.svg)

    1. [Configure](get-started-detect-motion-emit-events.md#prepare-to-monitor-the-modules) la extensión Azure IoT Tools.
    1. Establezca el elemento pipelineTopology, cree instancias de un elemento livePipeline y actívelas mediante estas [llamadas de método directo](get-started-detect-motion-emit-events.md#use-direct-method-calls).
    1. [Observe los resultados](get-started-detect-motion-emit-events.md#observe-results) en el centro de conectividad.
    1. Invoque [métodos de limpieza](get-started-detect-motion-emit-events.md#deactivate-the-live-pipeline).
    1. Elimine los recursos si ya no los necesita.

        > [!IMPORTANT]
        > Los recursos sin eliminar pueden seguir activos e incurrir en costos de Azure. Asegúrese de eliminar los recursos que no piensa usar.
        
## <a name="next-steps"></a>Pasos siguientes

* Pruebe la detección de movimiento junto con la grabación de vídeos importantes en la nube. Siga los pasos que se indican en el inicio rápido [Detección de movimiento y grabación de vídeo](detect-motion-record-video-edge-devices.md).
* Ejecute [IA en Live Video](analyze-live-video-use-your-model-http.md#overview) (puede omitir la configuración de requisitos previos, ya que esta se ha hecho anteriormente)
* Use la [extensión VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.live-video-analytics-edge) para ver canalizaciones adicionales.
* Use una [cámara IP](https://en.wikipedia.org/wiki/IP_camera) compatible con RTSP en lugar de utilizar el simulador RTSP. Puede encontrar cámaras IP compatibles con RTSP en la página de [productos compatibles con ONVIF](https://www.onvif.org/conformant-products/). Busque dispositivos que cumplan con los perfiles G, S o T.

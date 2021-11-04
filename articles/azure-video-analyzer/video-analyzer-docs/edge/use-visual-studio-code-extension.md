---
title: Uso de la extensión de Visual Studio Code para Azure Video Analyzer
description: Este artículo le guiará por los pasos necesarios para empezar a usar la extensión Visual Studio Code de Azure Video Analyzer.
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0784f69e40597bef3a08512e3bcbd01cf1343390
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131552570"
---
# <a name="use-the-visual-studio-code-extension-for-azure-video-analyzer"></a>Uso de la extensión de Visual Studio Code para Azure Video Analyzer

[!INCLUDE [header](includes/edge-env.md)]

Este artículo le guiará por los pasos necesarios para empezar a usar la extensión Video Studio Code para Azure Video Analyzer. Conectará la extensión Visual Studio Code al módulo perimetral de Video Analyzer a través del IoT Hub e implementará una [topología de canalización de muestra](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/cvr-video-sink). A continuación, ejecutará una secuencia de vídeo en directo simulada a través de una canalización en directo que graba continuamente vídeo en un recurso de vídeo. En el diagrama a continuación se representa la canalización.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/use-continuous-video-recording/continuous-video-recording-overview.svg" alt-text="Grabación de vídeo continua":::
 
 ## <a name="prerequisites"></a>Prerrequisitos
 
* [Visual Studio Code](https://code.visualstudio.com/) con la [extensión Azure Video Analyzer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.azure-video-analyzer)
* [Inicio rápido: Introducción a Azure Video Analyzer](./get-started-detect-motion-emit-events.md) o el tutorial [Grabación continua de vídeo y reproducción](./use-continuous-video-recording.md)

 > [!NOTE]
 > Las imágenes de este artículo se basan en el tutorial [Grabación continua de vídeo y reproducción](./use-continuous-video-recording.md).    

## <a name="set-up-your-development-environment"></a>Configurado su entorno de desarrollo

### <a name="obtain-your-iot-hub-connection-string"></a>Obtención de la cadena de conexión de IoT Hub

A fin de realizar llamadas al módulo perimetral de Video Analyzer, primero se necesita una cadena de conexión para conectar la extensión Visual Studio Code al IoT Hub.

1. En Azure Portal, vaya a su cuenta de IoT Hub.
1. Busque **Directivas de acceso compartido** en el panel izquierdo y selecciónelo.
1. Seleccione la directiva llamada **iothubowner**.
1. Copie el valor de **Cadena de conexión principal**. Tendrá el siguiente aspecto: `HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX`.

### <a name="connect-the-visual-studio-code-extension-to-the-iot-hub"></a>Conexión de la extensión Visual Studio Code a IoT Hub

Con la cadena de conexión de IoT Hub, conecte la extensión Visual Studio Code al módulo de Video Analyzer. 

1.  En Visual Studio Code, seleccione el icono de **Azure Video Analyzer** en la barra de actividad del lado izquierdo.
1.  En el panel de extensiones de Video Analyzer, haga clic en el botón **Escribir la cadena de conexión**.
1.  En la parte superior, pegue la cadena de conexión de IoT Hub.
1.  Seleccione el dispositivo donde se implementa AVA. El valor predeterminado se llama `avasample-iot-edge-device`.
1.  Seleccione el módulo de Video Analyzer. El valor predeterminado se llama `avaedge`.

![GIF en el que se muestra cómo escribir la cadena de conexión.](./media/use-visual-studio-code-extension/enter-connection-string.gif)

En el panel de extensiones de Video Analyzer se debería mostrar el dispositivo conectado con todos sus módulos. Debajo de los módulos se enumeran las topologías de canalización. De forma predeterminada, no hay ninguna topología de canalización implementada.

## <a name="create-a-pipeline-topology"></a>Creación de una topología de canalización 

Una [topología de canalización](../pipeline.md) le permite describir cómo se debe procesar y analizar el vídeo en directo o los vídeos grabados según sus requisitos personalizados a través de un conjunto de nodos interconectados. 

1.  A la izquierda, en **Modules** (Módulos), haga clic con el botón derecho en **Pipeline topologies** (Topologías de canalización) y seleccione **Create pipeline topology** (Crear topología de canalización).
1.  En la parte superior, en **Try sample topologies** (Probar topologías de muestra), en **Continuous Video Recording** (Grabación continua de vídeo), seleccione **Record to Azure Video Analyzer video** (Grabar en vídeo de Azure Video Analyzer). Cuando se le solicite, haga clic en **Proceed** (Continuar).
1.  Haga clic en **Save** (Guardar) en la parte superior derecha.

![GIF en el que se muestra cómo agregar una topología.](./media/use-visual-studio-code-extension/add-topology.gif)

Observe que ahora hay una entrada en la lista **Pipeline topologies** (Topologías de canalización) de la izquierda con la etiqueta **CVRToVideoSink**. Se trata de una topología de canalización, donde algunos de los parámetros se definen como variables.

## <a name="create-a-live-pipeline"></a>Creación de una canalización en directo

Una canalización en directo es una instancia de una topología de canalización. Las variables de una topología de canalización se rellenan cuando se crea una canalización en directo.

1.  A la izquierda, en **Pipeline topologies** (Topologías de canalización), haga clic con el botón derecho en **CVRToVideoSink** y seleccione **Create live pipeline** (Crear canalización en directo).
1.  En **Instance name** (Nombre de instancia), escriba `livePipeline1`.
1. En la sección **Parameters** (Parámetros), en el parámetro **rtspUrl**, escriba `rtsp://rtspsim:554/media/camera-300s.mkv`.
1.  En la parte superior derecha, haga clic en **Save and activate** (Guardar y activar).

![GIF en el que se muestra cómo crear y activar una canalización en directo.](./media/use-visual-studio-code-extension/create-and-activate.gif)

Ahora que se ha activado una canalización en directo, los eventos operativos se pueden consultar haciendo clic en el botón **Iniciar la supervisión del punto de conexión de eventos integrado** en la extensión IoT Hub, como se muestra en el tutorial [Grabación continua de vídeo y reproducción](./use-continuous-video-recording.md#prepare-to-monitor-the-modules).

## <a name="next-steps"></a>Pasos siguientes

Cargue otra de las topologías de canalización de muestra a través de la extensión Visual Studio Code y vea las propiedades de cada nodo. Siga la referencia [extensión de Visual Studio Code para Azure Video Analyzer](../visual-studio-code-extension.md) a fin de obtener información sobre cómo parametrizar variables y cómo se conectan los módulos.

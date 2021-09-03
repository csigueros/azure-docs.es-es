---
title: Análisis de secuencias de vídeo en directo con varios modelos de IA mediante la composición de IA
description: En este artículo se proporcionan instrucciones sobre cómo analizar secuencias de vídeo en directo con varios modelos de IA mediante la característica de composición de IA de Azure Video Analyzer.
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 04/01/2021
ms.openlocfilehash: 03a2f0c597b3fe58b030ca2b03d9337b3322b444
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121781352"
---
# <a name="analyze-live-video-streams-with-multiple-ai-models-using-ai-composition"></a>Análisis de secuencias de vídeo en directo con varios modelos de IA mediante la composición de IA

Algunos escenarios de clientes requieren que el vídeo se analice con varios modelos de IA. Estos modelos pueden [aumentarse entre sí](ai-composition-overview.md#sequential-ai-composition) o [trabajar de forma independiente en paralelo](ai-composition-overview.md#parallel-ai-composition) en la [misma secuencia de vídeo o una combinación](ai-composition-overview.md#combined-ai-composition) de estos modelos aumentados e independientes en paralelo puede actuar en la misma secuencia de vídeo para obtener información práctica.

Azure Video Analyzer admite estos escenarios a través de una característica denominada [composición de IA](ai-composition-overview.md). En esta guía se muestra cómo puede aplicar varios modelos de forma aumentada en la misma secuencia de vídeo. Usa un YOLO Tiny(Light) y un modelo YOLO normal en paralelo, para detectar un objeto de interés. El modelo YOLO Tiny es computacionalmente más ligero pero menos preciso que el modelo YOLO y se le llama primero. Si el objeto detectado supera un umbral de confianza específico, no se invoca el modelo YOLO normal de almacenamiento en fase secuencial, con lo que se utilizan los recursos subyacentes de forma eficaz.

Después de completar los pasos de esta guía, podrá ejecutar una secuencia de vídeo en directo simulada a través de una canalización con capacidad de composición de IA y ampliarla a sus escenarios específicos. El siguiente diagrama representa gráficamente esa canalización.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/ai-composition/motion-with-object-detection-using-ai-composition.svg" alt-text="Introducción a la composición de IA":::
 
## <a name="prerequisites"></a>Requisitos previos

* Una cuenta de Azure que tenga una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), en caso de que aún no lo haya hecho.

    > [!NOTE]
    > Necesitará una suscripción de Azure con permisos para crear entidades de servicio (el rol de propietario permite esto). Si no tiene los permisos adecuados, póngase en contacto con el administrador de la cuenta para que se los conceda.
* [Visual Studio Code](https://code.visualstudio.com/) en la máquina de desarrollo. Asegúrese de tener la [extensión Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* Asegúrese de que la red a la que está conectada su máquina de desarrollo permita usar el protocolo Advanced Message Queuing Protocol (AMQP) a través del puerto 5671 para el tráfico saliente. Esta configuración permite a Azure IoT Tools comunicarse con Azure IoT Hub.
* Complete el inicio rápido de [Análisis de vídeo en directo con su propio modelo gRPC](analyze-live-video-use-your-model-grpc.md). No omita esto, ya que se trata de un requisito estricto para la guía de procedimientos.

> [!TIP]
> Al instalar la extensión de Azure IoT Tools, es posible que se le pida que instale Docker. Si lo desea, ignore esta petición.
>
> Si tiene problemas con los recursos de Azure que se crean, consulte la [guía de solución de problemas](troubleshoot.md#common-error-resolutions) para resolver algunos de los problemas más comunes que pueden surgir.

## <a name="review-the-video-sample"></a>Revisión de la muestra de vídeo

Puesto que ya ha completado el inicio rápido especificado en la sección de requisitos previos, tendrá un dispositivo perimetral ya creado. Este dispositivo perimetral tendrá la carpeta de entrada /home/localedgeuser/samples/input que incluye determinados archivos de vídeo. Inicie sesión en el dispositivo IoT Edge, cambie al directorio /home/localedgeuser/samples/input/ y ejecute el siguiente comando para obtener el archivo de entrada que se va a usar para esta guía de procedimientos.

wget https://lvamedia.blob.core.windows.net/public/co-final.mkv

Además, si lo desea, en el equipo que tiene el [reproductor multimedia VLC](https://www.videolan.org/vlc/), seleccione Ctrl+N y pegue un vínculo al [vídeo de ejemplo (.mkv)](https://lvamedia.blob.core.windows.net/public/co-final.mkv) para iniciar la reproducción. Verá las imágenes de los automóviles en una autovía.

## <a name="create-and-deploy-the-pipeline"></a>Creará e implementará la canalización.

De forma similar a los pasos del inicio rápido que completó en los requisitos previos, puede seguir los pasos aquí, pero con ajustes menores.

1. Siga las instrucciones de la sección [Creación e implementación de la canalización](analyze-live-video-use-your-model-grpc.md#create-and-deploy-the-pipeline) del inicio rápido que acaba de finalizar. Asegúrese de realizar los siguientes ajustes a medida que continúe con los pasos. Estos pasos ayudan a garantizar que se usa el cuerpo correcto para las llamadas al método directo.

Edite el archivo *operations.json*:

* Cambie el vínculo a la topología de la canalización: `"pipelineTopologyUrl" : "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/ai-composition/topology.json"`
* En `livePipelineSet`,  
   1. Asegure: `"topologyName" : "AIComposition"` y 
   2. Cambie el valor del parámetro `rtspUrl` a `"rtsp://rtspsim:554/media/co-final.mkv"`.
    
* En `pipelineTopologyDelete`, edite el nombre: `"name" : "AIComposition"`
    
2. Siga las instrucciones de la sección [Generación e implementación del manifiesto de implementación de IoT Edge](analyze-live-video-use-your-model-grpc.md#generate-and-deploy-the-iot-edge-deployment-manifest), pero use el siguiente manifiesto de implementación en su lugar: src/edge/deployment.composite.template.json

3. Siga las instrucciones de la sección [Ejecución del programa de ejemplo](analyze-live-video-use-your-model-grpc.md#run-the-sample-program).

4. Para más información sobre los resultados, consulte la sección [Interpretación de los resultados](analyze-live-video-use-your-model-grpc.md#interpret-results). Además de los eventos de análisis en el centro y los eventos de diagnóstico, la topología que ha usado también crea un clip de vídeo pertinente en la nube que se desencadena mediante la activación basada en señales de inteligencia artificial de la puerta de señal. Este clip también va acompañado de [eventos operativos](record-event-based-live-video.md#operational-events) en el centro para los flujos de trabajo descendentes que se van a realizar. Puede [examinar y reproducir](record-event-based-live-video.md#playing-back-the-recording) el clip de vídeo iniciando sesión en el Azure Portal.

## <a name="clean-up"></a>Limpieza

Si no va a seguir usando esta aplicación, elimine los recursos que ha creado en este inicio rápido.

## <a name="next-steps"></a>Pasos siguientes

Más información sobre los [mensajes de diagnóstico](monitor-log-edge.md).

---
title: 'Análisis de vídeo en directo con Computer Vision para análisis espacial: Azure'
description: En este tutorial se muestra cómo usar Azure Video Analyzer junto con la característica de IA de Computer Vision para análisis espacial, parte de Azure Cognitive Services, para analizar una fuente de vídeo en directo desde una cámara IP (simulada).
author: Juliako
ms.author: juliako
ms.service: azure-video-analyzer
ms.topic: tutorial
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: b3de27af4dab9dd8968f97487cb120dbcd0d08c8
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131554495"
---
# <a name="tutorial-live-video-with-computer-vision-for-spatial-analysis-preview"></a>Tutorial: Vídeo en directo con Computer Vision para análisis espacial (versión preliminar)

[!INCLUDE [header](includes/edge-env.md)]

En este tutorial se muestra cómo usar Azure Video Analyzer junto con el [servicio de IA de Computer Vision para el análisis espacial, parte de Azure Cognitive Services,](../../../cognitive-services/computer-vision/intro-to-spatial-analysis-public-preview.md) para analizar una fuente de vídeo en directo desde una cámara IP (simulada). Verá cómo este servidor de inferencia le permite analizar el vídeo en streaming para comprender las relaciones espaciales entre las personas y el movimiento en el espacio físico. Un subconjunto de los fotogramas de la fuente de vídeo se envía a este servidor de inferencia y los resultados se envían al centro de IoT Edge. Cuando se cumplen algunas condiciones, los clips de vídeo se graban y almacenan en la cuenta de Video Analyzer.

En este tutorial, aprenderá lo siguiente:

> [!div class="checklist"]
>
> - Configuración de recursos
> - Examen del código
> - Ejecución del código de ejemplo
> - Supervisión de eventos

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Sugerencias antes de la lectura

Consulte estos artículos antes de empezar:

- [Introducción a Video Analyzer](../overview.md)
- [Terminología de Video Analyzer](../terminology.md)
- [Conceptos de canalización](../pipeline.md)
- [Grabación de vídeo basada en eventos](record-event-based-live-video.md)
- [Contenedor de Computer Vision de Azure Cognitive Services](../../../cognitive-services/computer-vision/intro-to-spatial-analysis-public-preview.md) para análisis espacial.

## <a name="prerequisites"></a>Requisitos previos

Estos son los requisitos previos para conectar el módulo de análisis espacial a un módulo de Azure Video Analyzer.

[!INCLUDE [prerequisites](./includes/common-includes/csharp-prerequisites.md)]

   > [!Note]
   > Asegúrese de que la red a la que está conectada la máquina de desarrollo permita Advanced Message Queueing Protocol a través del puerto 5671. Esta configuración permite a Azure IoT Tools comunicarse con Azure IoT Hub.

## <a name="set-up-azure-resources"></a>Configuración de los recursos de Azure

1. **Elija un dispositivo de proceso**  

    Para ejecutar el contenedor de análisis espacial se necesita un dispositivo de proceso con una [GPU NVIDIA Tesla T4](https://www.nvidia.com/en-us/data-center/tesla-t4/). Se recomienda usar **[Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/)** con aceleración de GPU; sin embargo, el contenedor se ejecuta en cualquier otra **máquina de escritorio** o **VM de Azure** que tenga [Ubuntu Desktop 18.04 LTS](http://releases.ubuntu.com/18.04/) instalado en el equipo host.

   #### <a name="azure-stack-edge-device"></a>[Dispositivo Azure Stack Edge](#tab/azure-stack-edge)

   Azure Stack Edge es una solución de hardware como servicio y un dispositivo informático perimetral habilitado para inteligencia artificial que cuenta con funcionalidades de transferencia de datos de red. Para instrucciones detalladas sobre la preparación y configuración, consulte la [documentación de Azure Stack Edge](../../../databox-online/azure-stack-edge-deploy-prep.md).

   #### <a name="desktop-machine"></a>[Máquina de escritorio](#tab/desktop-machine)

   #### <a name="minimum-hardware-requirements"></a>Requisitos mínimos de hardware

   - 4 GB de RAM del sistema
   - 4 GB de RAM de GPU
   - CPU de 8 núcleos
   - 1 GPU NVIDIA Tesla T4
   - 20 GB de espacio en HDD

   #### <a name="recommended-hardware"></a>Hardware recomendado

   - 32 GB de RAM del sistema
   - 16 GB de RAM de GPU
   - CPU de 8 núcleos
   - 2 GPU NVIDIA Tesla T4
   - 50 GB de espacio en SSD

   #### <a name="azure-vm-with-gpu"></a>[Azure VM con GPU](#tab/virtual-machine)

   Puede usar una [máquina virtual de la serie NC](../../../virtual-machines/nc-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) con una GPU K80.
        
1. **Configuración del dispositivo perimetral**

    #### <a name="azure-stack-edge-device"></a>[Dispositivo Azure Stack Edge](#tab/azure-stack-edge)
    [Configuración del proceso en el portal de Azure Stack Edge](../../../cognitive-services/computer-vision/spatial-analysis-container.md#configure-compute-on-the-azure-stack-edge-portal)
    #### <a name="desktop-machine"></a>[Máquina de escritorio](#tab/desktop-machine)
    [Siga estas instrucciones si el equipo host no es un dispositivo Azure Stack Edge.](../../../cognitive-services/computer-vision/spatial-analysis-container.md#install-nvidia-cuda-toolkit-and-nvidia-graphics-drivers-on-the-host-computer)
    #### <a name="azure-vm-with-gpu"></a>[Azure VM con GPU](#tab/virtual-machine)
    1. [Creación de la VM](../../../cognitive-services/computer-vision/spatial-analysis-container.md?tabs=virtual-machine#create-the-vm) e instalación de los dockers necesarios en la VM

        > [!Important]
        > **Omita el paso del manifiesto de implementación de IoT** que se menciona en ese documento. Va a usar nuestro propio **[archivo de manifiesto de implementación](#configure-deployment-template)** para implementar los contenedores necesarios.

    1. Conéctese a la máquina virtual y, en el tipo de terminal, en el siguiente comando:
    ```bash
    bash -c "$(curl -sL https://aka.ms/ava-edge/prep_device)"
    ```
    El módulo de Live Video Analytics se ejecuta en el dispositivo perimetral con cuentas de usuario locales sin privilegios. Además, necesita ciertas carpetas locales para almacenar los datos de configuración de la aplicación. Por último, para esta guía paso a paso, se emplea un [simulador RTSP](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555) que retransmite una fuente de vídeo en tiempo real a un módulo de AVA para su análisis. Este simulador toma como entrada archivos de vídeo grabados previamente desde un directorio de entrada. 
    
    El script prep-device usado anteriormente automatiza estas tareas, por lo que puede ejecutar un comando y hacer que todas las carpetas de entrada y configuración pertinentes, archivos de entrada de vídeo y cuentas de usuario con privilegios se creen sin problemas. Una vez que el comando finalice correctamente, debería ver las siguientes carpetas creadas en el dispositivo perimetral. 
    
      * `/home/localedgeuser/samples`
      * `/home/localedgeuser/samples/input`
      * `/var/lib/videoanalyzer`
      * `/var/media`
    
     Observe los archivos de vídeo (*.mkv) de la carpeta /home/lvaedgeuser/samples/input, ya que le servirán como los archivos de entrada que se van a analizar.  

1. A continuación, implemente los demás recursos de Azure.

   [![Implementación en Azure](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

   > [!NOTE]
   > El botón anterior crea y usa la máquina virtual predeterminada que NO tiene la GPU NVIDIA. Use la opción "Use existing edge device" (Usar el dispositivo perimetral existente) cuando se le pida en la plantilla de Azure Resource Manager (ARM) y use IoT Hub y la información del dispositivo del paso anterior.
   > :::image type="content" source="./media/spatial-analysis/use-existing-device.png" alt-text="Uso del dispositivo existente":::

   [!INCLUDE [resources](./includes/common-includes/azure-resources.md)]

## <a name="overview"></a>Información general

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis/overview.png" alt-text="Introducción al análisis espacial":::

En este diagrama se muestra el flujo de las señales en este tutorial. Un [módulo perimetral](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555) simula una cámara IP que hospeda un servidor de protocolo RTSP. Un nodo de [origen RTSP](../pipeline.md#rtsp-source) extrae la fuente de vídeo de este servidor y envía fotogramas de vídeo al nodo del `CognitiveServicesVisionProcessor`.

El nodo de `CognitiveServicesVisionProcessor` desempeña el rol de un servidor proxy. Convierte los fotogramas de vídeo en el tipo de imagen especificado. Luego, utiliza la **memoria compartida** para retransmitir la imagen a otro módulo perimetral que ejecuta operaciones de IA detrás de un punto de conexión gRPC. En este ejemplo, ese módulo perimetral es el módulo de análisis espacial. El nodo `CognitiveServicesVisionProcessor` realiza dos acciones:

- Recopila los resultados y publica eventos en el nodo de [receptor de IoT Hub](../pipeline.md#iot-hub-message-sink). que posteriormente los envía a [IoT Edge Hub](../../../iot-fundamentals/iot-glossary.md#iot-edge-hub).
- También captura un clip de vídeo de 30 segundos del origen RTSP mediante un [procesador de puerta de señal](../pipeline.md#signal-gate-processor) y lo almacena como un receptor de vídeo.

## <a name="create-the-computer-vision-resource"></a>Creación del recurso de Computer Vision

Debe crear un recurso de Azure de tipo [Computer Vision](https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision) para el **nivel S1 estándar** en [Azure Portal](../../../iot-edge/how-to-deploy-modules-portal.md) o mediante la CLI de Azure. 

## <a name="set-up-your-development-environment"></a>Configurado su entorno de desarrollo

[!INCLUDE [setup development environment](./includes/set-up-dev-environment/csharp/csharp-set-up-dev-env.md)]

## <a name="configure-deployment-template"></a>Configuración de la plantilla de implementación
#### <a name="azure-stack-edge-device"></a>[Dispositivo Azure Stack Edge](#tab/azure-stack-edge)
Busque el archivo de implementación en **/src/edge/deployment.spatialAnalysis.ase.template.json**. En la plantilla, está el módulo `avaedge`, el módulo `rtspsim` y nuestro módulo `spatialanalysis`. 

En el archivo de plantilla de implementación:

1. El manifiesto de implementación usa el puerto 50051 para comunicarse entre el módulo `avaedge` y `spatialanalysis`. Si el puerto lo usa cualquier otra aplicación, establezca el enlace de puerto del módulo `spatialanalysis` en un puerto abierto.

   ```
   "PortBindings": {
       "50051/tcp": [
           {
               "HostPort": "50051"
           }
       ]
   }
   ```

1. `IpcMode` en createOptions de los módulos `avaedge` y `spatialanalysis` debe ser igual y estar establecido en **host**.
1. Para que el simulador RTSP funcione, asegúrese de que ha configurado los límites del volumen al usar un dispositivo de Azure Stack Edge.

   1. [Conéctese al recurso compartido de SMB](../../../databox-online/azure-stack-edge-deploy-add-shares.md#connect-to-an-smb-share) y copie el [archivo de vídeo con la tienda minorista de ejemplo](https://lvamedia.blob.core.windows.net/public/retailshop-15fps.mkv) en el recurso compartido local.

      > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWMIPP]

   1. Verá que el módulo rtspsim tiene la siguiente configuración:
      ```
      "createOptions": {
                          "HostConfig": {
                            "Mounts": [
                              {
                                "Target": "/live/mediaServer/media",
                                "Source": "<your Local Docker Volume Mount name>",
                                "Type": "volume"
                              }
                            ],
                            "PortBindings": {
                              "554/tcp": [
                                {
                                  "HostPort": "554"
                                }
                              ]
                            }
                          }
                        }
      ```
#### <a name="desktop-machine"></a>[Máquina de escritorio](#tab/desktop-machine)
Busque el archivo de implementación en **/src/edge/deployment.spatialAnalysis.generic.template.json**. En la plantilla, está el módulo `avaedge`, el módulo `rtspsim` y nuestro módulo `spatialanalysis`. 

#### <a name="azure-vm-with-gpu"></a>[Azure VM con GPU](#tab/virtual-machine)
Busque el archivo de implementación en **/src/edge/deployment.spatialAnalysis.generic.template.json**. En la plantilla, está el módulo `avaedge`, el módulo `rtspsim` y nuestro módulo `spatialanalysis`.

---

En la tabla siguiente se muestran las distintas variables de entorno que usa el módulo IoT Edge. También puede establecerlas en el manifiesto de implementación mencionado anteriormente, mediante el atributo `env` en `spatialanalysis`:

| Nombre de la opción de configuración | Valor | Descripción|
|---------|---------|---------|
| PANTALLA | :1 | Este valor debe ser igual que la salida de `echo $DISPLAY` en el equipo host. Los dispositivos Azure Stack Edge no tienen pantalla. Esta configuración no es aplicable|
| ARCHON_SHARED_BUFFER_LIMIT | 377487360 | **Sin modificar**|
| ARCHON_LOG_LEVEL | Info; Verbose (Información; Detallado) | Nivel de registro, seleccione uno de los dos valores|
| QT_X11_NO_MITSHM | 1 | **Sin modificar**|
| OMP_WAIT_POLICY | PASSIVE | **Sin modificar**|
| EULA | accept | Este valor se debe establecer en *accept* para que el contenedor se ejecute |
| ARCHON_TELEMETRY_IOTHUB | true | Establezca este valor en "true" para enviar los eventos de telemetría a IoT Hub |
| FACTURACIÓN | Su URI de punto de conexión| Recopile este valor en Azure Portal del recurso de Computer Vision. Puede encontrarlo en la hoja **Clave y punto de conexión** del recurso.|
| APIKEY | Su clave de API| Recopile este valor en Azure Portal del recurso de Computer Vision. Puede encontrarlo en la hoja **Clave y punto de conexión** del recurso. |
| LAUNCHER_TYPE | avaBackend | **Sin modificar** |
| ARCHON_GRAPH_READY_TIMEOUT | 600 | Agregue esta variable de entorno si la GPU **no** es T4 o NVIDIA 2080 Ti|

> [!IMPORTANT]
> Para poder ejecutar el contenedor, las opciones `Eula`, `Billing` y `ApiKey` deben estar especificadas; de lo contrario, el contenedor no se iniciará. 

### <a name="gathering-keys-and-endpoint-uri"></a>Recopilación de claves y URI de punto de conexión

Se usa una clave de API para iniciar el contenedor de análisis espacial, y dicha clave está disponible en la página `Keys and Endpoint` de Azure Portal de su recurso de Computer Vision. Vaya a esa página y busque la clave y el URI del punto de conexión que necesita el contenedor `spatialAnalysis`.  

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis/keys-endpoint.png" alt-text="URI de punto de conexión":::

## <a name="generate-and-deploy-the-deployment-manifest"></a>Generación e implementación del manifiesto de implementación

El manifiesto de implementación define los módulos que se implementan en un dispositivo perimetral. También define los valores de configuración de los módulos.

Siga estos pasos para generar el manifiesto a partir del archivo de plantilla y, después, impleméntelo en el dispositivo perimetral.

1. Abra Visual Studio Code.
1. Al lado del panel `AZURE IOT HUB`, seleccione el icono Más acciones para establecer la cadena de conexión de IoT Hub. La cadena se puede copiar del archivo `src/cloud-to-device-console-app/appsettings.json`.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/vscode-common-screenshots/set-connection-string.png" alt-text="Análisis espacial: cadena de conexión":::

1. En el Explorador de carpetas, haga clic con el botón derecho en el archivo de plantilla de implementación y seleccione Generar el manifiesto de implementación de IoT Edge.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/spatial-analysis/generate-deployment-manifest.png" alt-text="Análisis espacial: deployment amd64 json":::

   Esta acción debe crear un archivo de manifiesto en la carpeta **src/edge/config**.

1. Haga clic con el botón derecho en el archivo de manifiesto generado y seleccione **Crear implementación para un solo dispositivo**; a continuación, seleccione el nombre de su dispositivo perimetral.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/spatial-analysis/deployment-single-device.png" alt-text="Análisis espacial: implementación en un único dispositivo":::

1. En la parte superior de la página, se le pedirá que seleccione un dispositivo IoT Hub, elija el nombre del dispositivo perimetral en el menú desplegable.
1. Tras aproximadamente 30 segundos, en la esquina inferior izquierda de la ventana, actualice el panel **AZURE IOT HUB**. El dispositivo perimetral ahora muestra los siguientes módulos implementados:

   - Azure Video Analyzer (nombre del módulo **avaedge**).
   - Simulador del Protocolo de transmisión en tiempo real (RTSP) (nombre del módulo **rtspsim**).
   - Análisis espacial (nombre del módulo **spatialanalysis**).

Tras una implementación correcta, habrá un mensaje en la ventana SALIDA como el siguiente:

```
[Edge] Start deployment to device [<edge device name>]
[Edge] Deployment succeeded.
```

Después, puede encontrar los módulos `avaedge`, `spatialanalysis`y `rtspsim` en Dispositivos/Módulos, y su estado debe ser "**en ejecución**".

## <a name="prepare-to-monitor-events"></a>Preparación para supervisar eventos

Para ver estos eventos, siga estos pasos:

1. En Visual Studio Code, abra la pestaña **Extensiones** (o presione Ctrl + Mayús + X) y busque Azure IoT Hub.
1. Haga clic con el botón derecho y seleccione la opción **Configuración de la extensión**.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/vscode-common-screenshots/extension-settings.png" alt-text="Configuración de la extensión":::

1. Busque y habilite "Show Verbose Message" (Mostrar mensaje detallado).

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/vscode-common-screenshots/verbose-message.png" alt-text="Show Verbose Message"::: (Mostrar mensaje detallado)

1. Abra el panel Explorador y busque **AZURE IOT HUB** en la esquina inferior izquierda, haga clic con el botón derecho y seleccione Iniciar la supervisión del punto de conexión de eventos integrado.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/vscode-common-screenshots/start-monitoring.png" alt-text="Análisis espacial: iniciar supervisión":::

## <a name="run-the-program"></a>Ejecución del programa

Un archivo program.cs invocará los métodos directos en `src/cloud-to-device-console-app/operations.json`. Tendrá que editar el archivo `operations.json` y actualizar la dirección URL de la topología de canalización, el nombre de la topología, así como la dirección URL de RTSP.

En operations.json:

- Establezca la topología de canalización del modo siguiente:

  ```json
  {
      "opName": "pipelineTopologySet",
      "opParams": {
          "pipelineTopologyUrl": "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-zone-crossing-operation-topology.json"
      }
  },
  ```
* En `livePipelineSet`, edite el nombre de la topología para que coincida con el valor del vínculo anterior:
    * `"topologyName" : "PersonZoneCrossingTopology"`
* En `pipelineTopologyDelete`, edite el nombre:
    * `"name" : "PersonZoneCrossingTopology"`

> [!Important]
> La topología que usó anteriormente tiene un nombre codificado de forma definida para el recurso VideoSink `videoSink`. Si decide elegir otro origen de vídeo, no olvide cambiar este valor.

- Cree una canalización activa como esta, y establezca los parámetros de la topología de canalización aquí:

  ```json
  {
      "opName": "livePipelineSet",
      "opParams": {
          "name": "Sample-Pipeline-1",
          "properties": {
              "topologyName": "PersonZoneCrossingTopology",
              "description": "Sample pipeline description",
              "parameters": [
                  {
                      "name": "rtspUrl",
                      "value": "rtsp://rtspsim:554/media/retailshop-15fps.mkv"
                  },
                  {
                      "name": "rtspUserName",
                      "value": "testuser"
                  },
                  {
                      "name": "rtspPassword",
                      "value": "testpassword"
                  }
              ]
          }
      }
  },
  ```

Para ejecutar una sesión de depuración, seleccione F5 y siga las instrucciones de **TERMINAL**; se establecerá el elemento pipelineTopology, se establecerá y activará la topología activa y, por último, se eliminarán los recursos.

> [!Note]
> El programa se pausará en el paso para activar la canalización activa. Abra la pestaña Terminal y presione **Entrar** para continuar e iniciar los pasos de desactivación y eliminación de recursos.

## <a name="interpret-results"></a>Interpretación de los resultados

`spatialanalysis` es un contenedor grande y su tiempo de inicio puede ser de hasta 30 segundos. Una vez que el contenedor spatialanalysis esté en funcionamiento, comenzará a enviar los eventos de inferencias. Verá eventos como los siguientes:

```JSON
[IoTHubMonitor] [3:37:28 PM] Message received from [ase03-edge/avaedge]:
{
  "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1620671848135494 1 IN IP4 172.27.86.122\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/cafeteria.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.08.19\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.066\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/retailshop-15fps.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=640028;sprop-parameter-sets=Z2QAKKzZQHgCHoQAAAMABAAAAwDwPGDGWA==,aOvssiw=\r\na=control:track1\r\n"
}
[IoTHubMonitor] [3:37:30 PM] Message received from [ase03-edge/avaedge]:
{
  "type": "video",
  "location": "/videos/<your video name>",
  "startTime&quot;: &quot;2021-05-10T18:37:27.931Z"
}
[IoTHubMonitor] [3:37:40 PM] Message received from [ase03-edge/avaedge]:
{
  "state&quot;: &quot;initializing"
}
[IoTHubMonitor] [3:37:50 PM] Message received from [ase03-edge/avaedge]:
{
  "state&quot;: &quot;initializing"
}
[IoTHubMonitor] [3:38:18 PM] Message received from [ase03-edge/avaedge]:
{
  "type": "video",
  "location": "/videos/<your video name>",
  "startTime&quot;: &quot;2021-05-10T18:37:27.931Z"
}

```
> [!NOTE]
> Es posible que vea mensajes de tipo **"inicializando"** . Estos mensajes se muestran mientras se inicia el módulo spatialAnalysis y pueden tardar hasta 60 segundos en llegar a un estado en ejecución. Tenga paciencia y debería ver el flujo de eventos de inferencia.

Cuando se cree una instancia de topología de canalización, verá el evento "MediaSessionEstablished"; este es un [evento MediaSessionEstablished de ejemplo](detect-motion-emit-events-quickstart.md#mediasessionestablished-event).

El módulo spatialanalysis también enviará eventos de información de inteligencia artificial a Azure Video Analyzer y, luego, a IoTHub; también se mostrarán en **SALIDA**. Estas conclusiones de inteligencia artificial se graban junto con el vídeo a través del nodo receptor de vídeo. Puede usar Video Analyzer para verlos, tal como se describe a continuación.

## <a name="supported-spatial-analysis-operations"></a>Operaciones de análisis espacial admitidas

Estas son las operaciones que el módulo `spatialAnalysis` ofrece y que son compatibles con Azure Video Analyzer:

- **personZoneCrossing**
- **personCrossingLine**
- **personDistance**
- **personCount**
- **customOperation**


Lea nuestro documento de referencia de **[operaciones de análisis espacial](../spatial-analysis-operations.md)** **admitido** para obtener más información sobre las distintas operaciones y las propiedades admitidas en ellas.

## <a name="playing-back-the-recording"></a>Reproducción de la grabación

Para examinar el recurso de vídeo de Video Analyzer que se ha creado con la canalización en directo, inicie sesión en Azure Portal y vea el vídeo.

1. Abra el explorador web y vaya a [Azure Portal](https://portal.azure.com/). Introduzca sus credenciales para iniciar sesión en el portal. La vista predeterminada es el panel del servicio.
1. Busque la cuenta de Video Analyzer entre los recursos que tiene en la suscripción y abra el panel de cuentas.
1. Seleccione **Vídeos** en la lista **Instancias de Video Analyzer**.
1. Encontrará un vídeo con el nombre `personzonecrossing`. Este es el nombre elegido en el archivo de topología de la canalización.
1. Seleccione el vídeo.
1. En la página de detalles del vídeo, haga clic en el icono **Reproducir**

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/spatial-analysis/sa-video-playback.png" alt-text="Captura de pantalla de la reproducción de vídeo":::
   
1. Para ver los metadatos de inferencia en el video, haga clic en el icono **Representación de metadatos**.
   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/record-stream-inference-data-with-video/bounding-box.png" alt-text="Icono de representación de metadatos":::

    Encontrará tres opciones para ver como superposición en el vídeo:  
      - **Rectángulos delimitadores**: muestra un rectángulo delimitador alrededor de cada persona con un id. único.
      - **Atributos**: muestra atributos de personas como su velocidad (en pies/s) y la orientación (mediante una flecha), cuando está disponible.
      - **Ruta de acceso del objeto**: muestra un breve recorrido para el movimiento de cada persona, cuando esté disponible.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/spatial-analysis/sa-video-playback-bounding-boxes.png" alt-text="Captura de pantalla de la reproducción de vídeo con rectángulos de selección":::

[!INCLUDE [activate-deactivate-pipeline](./includes/common-includes/activate-deactivate-pipeline.md)]

## <a name="next-steps"></a>Pasos siguientes

Pruebe las distintas operaciones que ofrece el módulo `spatialAnalysis`; consulte las siguientes topologías pipelineTopologies:

- [personCount](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-count-operation-topology.json)
- [personDistance](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-distance-operation-topology.json)
- [personCrossingLine](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-line-crossing-operation-topology.json)
- [customOperation](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/custom-operation-topology.json)

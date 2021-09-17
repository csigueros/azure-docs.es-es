---
title: 'Introducción a Azure Video Analyzer mediante Azure Portal: Azure'
description: Este inicio rápido le guiará por los pasos necesarios para empezar a usar Azure Video Analyzer mediante Azure Portal.
ms.topic: quickstart
ms.date: 06/01/2021
ms.openlocfilehash: df63755aa66399a2f457673ba17391d5b42f31a8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734231"
---
# <a name="quickstart-get-started-with-azure-video-analyzer-in-the-azure-portal"></a>Inicio rápido: Introducción a Azure Video Analyzer en Azure Portal

Este inicio rápido le guiará por los pasos necesarios para empezar a usar Azure Video Analyzer. Creará una cuenta de Azure Video Analyzer y sus recursos complementarios mediante Azure Portal. A continuación, implementará el módulo perimetral de Video Analyzer y un módulo de simulador de cámara del Protocolo de streaming en tiempo real (RTSP) en el dispositivo Azure IoT Edge.

Una vez completados los pasos de la configuración, podrá ejecutar la secuencia de vídeo en directo simulada mediante una canalización que detecta e informa de todos los movimientos de esa secuencia. El siguiente diagrama representa gráficamente esa canalización.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/get-started-detect-motion-emit-events/motion-detection.svg" alt-text="Diagrama de una canalización que detecta el movimiento e informa de él.":::

## <a name="prerequisites"></a>Requisitos previos

- Una suscripción de Azure activa. En caso de no tener ninguna, [cree una cuenta gratuita](https://azure.microsoft.com/free/).

  [!INCLUDE [the video analyzer account and storage account must be in the same subscription and region](./includes/note-account-storage-same-subscription.md)]

- Un dispositivo IoT Edge en el que tenga privilegios de administrador:
  - [Implementación en un dispositivo de IoT Edge](deploy-iot-edge-device.md)
  - [Implementación en IoT Edge para Linux en Windows](deploy-iot-edge-linux-on-windows.md)
- [Visual Studio Code](https://code.visualstudio.com/) con la extensión [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

[!INCLUDE [install-docker-prompt](./includes/common-includes/install-docker-prompt.md)]

## <a name="prepare-your-iot-edge-device"></a>Preparación del dispositivo IoT Edge

El módulo de Azure Video Analyzer debe configurarse para ejecutarse en el dispositivo IoT Edge con una cuenta de usuario local sin privilegios. El módulo necesita ciertas carpetas locales para almacenar los datos de configuración de la aplicación. El módulo del simulador de cámara RTSP necesita archivos de vídeo con los que pueda sintetizar una fuente de vídeo en directo.

Ejecute el comando siguiente en el dispositivo de IoT Edge:

`bash -c "$(curl -sL https://aka.ms/ava-edge/prep_device)"`

El script prep-device de ese comando automatiza la tarea de crear carpetas de entrada y configuración, de descargar archivos de entrada de vídeo y de crear cuentas de usuario con los privilegios correctos. Una vez finalizado el comando correctamente, debería ver las siguientes carpetas creadas en el dispositivo perimetral:

- _/home/localedgeuser/samples_
- _/home/localedgeuser/samples/input_
- _/var/lib/videoanalyzer_
- _/var/media_

Los archivos de vídeo (_.mkv) de la carpeta _/home/localedgeuser/samples/input\* sirven para simular un vídeo en directo.

## <a name="create-azure-resources"></a>Creación de recursos de Azure

El siguiente paso es crear los recursos de Azure necesarios (cuenta de Video Analyzer, cuenta de almacenamiento e identidad administrada asignada por el usuario). A continuación puede crear un registro de contenedor opcional y registrar un módulo perimetral de Video Analyzer con la cuenta de Video Analyzer.

Al crear una cuenta de Azure Video Analyzer, tiene que asociar una cuenta de Azure Storage a ella. Si usa Video Analyzer para grabar vídeo en directo desde una cámara, los datos se almacenan como blobs en un contenedor de la cuenta de almacenamiento. Debe usar una identidad administrada para conceder a la cuenta de Video Analyzer el acceso adecuado a la cuenta de almacenamiento, tal como se muestra a continuación.

[!INCLUDE [the video analyzer account and storage account must be in the same subscription and region](./includes/note-account-storage-same-subscription.md)]

### <a name="create-a-video-analyzer-account-in-the-azure-portal"></a>Creación de una cuenta de Video Analyzer en Azure Portal

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
1. En la barra de búsqueda de la parte superior, escriba **Video Analyzer**.
1. Seleccione **Video Analyzers** (Instancias de Video Analyzer) en **Servicios**.
1. Seleccione **Agregar**.
1. En la sección **Create Video Analyzer account** (Crear una cuenta de Video Analyzer) escriba los valores necesarios:

   - **Suscripción**: elija la suscripción que esté usando para crear la cuenta de Video Analyzer.
   - **Grupo de recursos**: elija un grupo de recursos en el que crear la cuenta de Video Analyzer o seleccione **Crear** para crear un nuevo grupo de recursos.
   - **Video Analyzer account name** (Nombre de la cuenta de Video Analyzer): este es el nombre de la cuenta de Video Analyzer. El nombre debe estar compuesto totalmente de minúsculas o números, sin espacios y con una longitud de entre 3 y 24 caracteres.
   - **Ubicación**: elija una ubicación para implementar la cuenta de Video Analyzer, por ejemplo, **Oeste de EE. UU. 2**.
   - **Cuenta de almacenamiento**: cree una cuenta de almacenamiento. Se recomienda seleccionar una cuenta de almacenamiento [estándar de uso general v2](../../storage/common/storage-account-overview.md#types-of-storage-accounts).
   - **Identidad de usuario**: cree una nueva identidad administrada asignada por el usuario y asígnele un nombre.

1. En la parte inferior del formulario, seleccione **Revisar y crear**.

### <a name="create-a-container-registry"></a>Creación de un Registro de contenedor

1. Seleccione **Crear un recurso** > **Contenedores** > **Registro de contenedor**.
1. En la pestaña **Aspectos básicos**, escriba los valores para **Grupo de recursos** y **Nombre del Registro**. Use el mismo grupo de recursos de las secciones anteriores. El nombre del registro debe ser único dentro de Azure y contener entre 5 y 50 caracteres alfanuméricos.
1. Acepte los valores predeterminados para la configuración restante. Después, seleccione **Revisar y crear**. Revise la configuración y seleccione **Crear**.

## <a name="deploy-edge-modules"></a>Implementación de módulos perimetrales

### <a name="deploy-the-video-analyzer-edge-module"></a>Implementación del módulo perimetral de Video Analyzer

1. Vaya a su cuenta de Video Analyzer.
1. Seleccione **Edge Modules** (Módulos perimetrales) en el panel **Perimetral**.
1. Seleccione **Add edge modules**, (Agregar módulos perimetrales), escriba **avaedge** como nombre del nuevo módulo perimetral y seleccione **Agregar**.
1. La página **Copy the provisioning token** (Copiar el token de aprovisionamiento) aparecerá en el lado derecho de la pantalla. Copie el fragmento de código siguiente en **Recommended desired properties for IoT module deployment** (Propiedades deseadas recomendadas para la implementación del módulo IoT). La necesitará en otro paso más adelante.
   ```JSON
     {
         "applicationDataDirectory": "/var/lib/videoanalyzer",
         "ProvisioningToken": "XXXXXXX",
         "diagnosticsEventsOutputName": "diagnostics",
         "operationalEventsOutputName": "operational",
         "logLevel": "information",
         "LogCategories": "Application,Events",
         "allowUnsecuredEndpoints": true,
         "telemetryOptOut": false
     }
   ```
1. Vaya a la cuenta de Azure IoT Hub.
1. Seleccione **IoT Edge** en **Administración de dispositivos**.
1. Seleccione el valor de **ID del dispositivo** de su dispositivo IoT Edge.
1. Seleccione **Set modules** (Establecer módulos).
1. Seleccione **Agregar** y, a continuación, **Módulo IoT Edge** en el menú desplegable.
1. Escriba **avaedge** en **Nombre del módulo IoT Edge**.
1. Copie y pegue la siguiente línea en el campo **URI de la imagen**: `mcr.microsoft.com/media/video-analyzer:1`.
1. Seleccione **Variables de entorno**.
1. En **NAME**, escriba **LOCAL_USER_ID**. En **VALUE**, escriba **1010**.
1. En la segunda fila, en **NAME**, escriba **LOCAL_GROUP_ID**. En **VALUE**, escriba **1010**.
1. Seleccione **Opciones de creación del contenedor** y copie y pegue las líneas siguientes:
   ```json
   {
     "HostConfig": {
       "LogConfig": {
         "Type": "",
         "Config": {
           "max-size": "10m",
           "max-file": "10"
         }
       },
       "Binds": [
         "/var/media/:/var/media/",
         "/var/lib/videoanalyzer/:/var/lib/videoanalyzer"
       ],
       "IpcMode": "host",
       "ShmSize": 1536870912
     }
   }
   ```
1. Seleccione **Configuración de módulos gemelos** y pegue el fragmento de código que copió anteriormente de la página **Copy the provisioning token** (Copiar el token de aprovisionamiento) en la cuenta de Video Analyzer.
   ```JSON
     {
         "applicationDataDirectory": "/var/lib/videoanalyzer",
         "ProvisioningToken": "XXXXXXX",
         "diagnosticsEventsOutputName": "diagnostics",
         "operationalEventsOutputName": "operational",
         "logLevel": "information",
         "LogCategories": "Application,Events",
         "allowUnsecuredEndpoints": true,
         "telemetryOptOut": false
     }
   ```
1. Seleccione **Agregar** en la parte inferior de la pantalla.
1. Seleccione **Rutas**.
1. En **NOMBRE**, escriba **AVAToHub**. En **VALOR**, escriba `FROM /messages/modules/avaedge/outputs/* INTO $upstream`.
1. Seleccione **Revisar y crear** y **Crear** para implementar el módulo perimetral **avaedge**.

### <a name="deploy-the-edge-module-for-the-rtsp-camera-simulator"></a>Implementación del módulo perimetral para el simulador de cámara RTSP

1. Vaya a la cuenta de IoT Hub.
1. Seleccione **IoT Edge** en **Administración de dispositivos**.
1. Seleccione el valor de **ID del dispositivo** de su dispositivo IoT Edge.
1. Seleccione **Set modules** (Establecer módulos).
1. Seleccione **Agregar** y, a continuación, **Módulo IoT Edge** en el menú desplegable.
1. Escriba **rtspsim** en **Nombre del módulo IoT Edge**.
1. Copie y pegue la siguiente línea en el campo **URI de la imagen**: `mcr.microsoft.com/ava-utilities/rtspsim-live555:1.2`.
1. Seleccione **Opciones de creación del contenedor** y copie y pegue las líneas siguientes:
   ```json
   {
     "HostConfig": {
       "Binds": ["/home/localedgeuser/samples/input:/live/mediaServer/media"]
     }
   }
   ```
1. Seleccione **Agregar** en la parte inferior de la pantalla.
1. Seleccione **Revisar y crear** y, a continuación, seleccione **Crear** para implementar el módulo perimetral **rtspsim**.

### <a name="verify-your-deployment"></a>Comprobación de la implementación

En la página de detalles del dispositivo, compruebe que los módulos **avaedge** y **rtspsim** aparecen como **Especificado en la implementación** y **Notificado mediante el dispositivo**.

Los módulos pueden tardar unos minutos en iniciarse en el dispositivo y después notificarlo a IoT Hub. Actualice la página para ver el estado actualizado. Código de estado **200 -- Correcto** significa que el [entorno de ejecución de IoT Edge](../../iot-edge/iot-edge-runtime.md) es correcto y funciona bien.

![Captura de pantalla que muestra un valor de estado de un entorno de ejecución de IoT Edge.](./media/deploy-iot-edge-device/status.png)

## <a name="set-up-your-development-environment"></a>Configurado su entorno de desarrollo

### <a name="obtain-your-iot-hub-connection-string"></a>Obtención de la cadena de conexión de IoT Hub

1. En Azure Portal, vaya a su cuenta de IoT Hub.
1. Busque **Directivas de acceso compartido** en el panel izquierdo y selecciónelo.
1. Seleccione la directiva llamada **iothubowner**.
1. Copie el valor de **Cadena de conexión principal**. Tendrá el siguiente aspecto: `HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX`.

### <a name="connect-to-iot-hub"></a>Conexión a IoT Hub

1. Abra Visual Studio Code y seleccione **Ver** > **Explorador**. O bien, seleccione Ctrl+Mayús+E.
1. En la esquina inferior izquierda de la pestaña **Explorador**, seleccione **Azure IoT Hub**.
1. Seleccione el icono **Más opciones** para ver el menú contextual. Luego, seleccione **Set IoT Hub Connection String** (Establecer cadena de conexión de IoT Hub).
1. Cuando aparezca un cuadro de entrada, escriba la cadena de conexión de IoT Hub.
1. En unos 30 segundos, actualice Azure IoT Hub en la sección inferior izquierda. Debería mostrarse el Id. de dispositivo, que debe tener los siguientes módulos implementados:

   - Módulo perimetral de Video Analyzer (nombre del módulo **avaedge**)
   - Simulador RTSP (nombre de módulo **rtspsim**)

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/get-started-detect-motion-emit-events/modules-node.png" alt-text="Captura de pantalla que muestra el nodo Módulos expandido.":::

> [!TIP]
> Si ha [implementado manualmente Video Analyzer](deploy-iot-edge-device.md) en un dispositivo perimetral (por ejemplo, un dispositivo ARM64), verá que el módulo se muestra en ese dispositivo, bajo Azure IoT Hub. Puede seleccionar ese módulo y continuar con los pasos siguientes.

### <a name="prepare-to-monitor-the-modules"></a>Preparación para supervisar los módulos

Cuando use este inicio rápido, los eventos se enviarán a IoT Hub. Para ver estos eventos, siga estos pasos:

1. En Visual Studio Code, abra la pestaña **Extensiones** (o seleccione Ctrl + Mayús + X) y busque **Azure IoT Hub**.
1. Haga clic con el botón derecho en la extensión de IoT Hub y seleccione **Configuración de la extensión**.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/get-started-detect-motion-emit-events/extension-settings.png" alt-text="Captura de pantalla que muestra la selección de Configuración de la extensión.":::

1. Busque y habilite **Show Verbose Message** (Mostrar mensaje detallado).

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/get-started-detect-motion-emit-events/verbose-message.png" alt-text="Captura de pantalla de Show Verbose Message (Mostrar mensaje detallado) habilitado.":::

1. Abra el panel **Explorador** en Visual Studio Code y busque **Azure IoT Hub** en la esquina inferior izquierda.
1. Expanda el nodo **Devices** (Dispositivos).
1. Haga clic con el botón derecho en el identificador de dispositivo y seleccione **Start Monitoring Built-in Event Endpoint** (Iniciar la supervisión del punto de conexión de eventos integrado).

   > [!NOTE]
   > Es posible que se le pida que proporcione información del punto de conexión integrado de IoT Hub. Para obtener esa información, en Azure Portal, vaya a la cuenta de IoT Hub y busque **Puntos de conexión integrados** en el panel de navegación izquierdo. Selecciónelo y busque la sección **Extremo compatible con Event Hub**. Copie y use el texto del cuadro. El punto de conexión será similar a este:
   >
   > ```
   > Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
   > ```

## <a name="use-direct-method-calls"></a>Uso de llamadas de método directo

Ahora puede analizar secuencias de vídeo en directo mediante la invocación de métodos directos que expone el módulo perimetral de Video Analyzer. Consulte [Métodos directos de Video Analyzer](direct-methods.md) para examinar todos los métodos directos que proporciona el módulo. El esquema de los métodos directos se puede encontrar [aquí](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/videoanalyzer/data-plane/VideoAnalyzer.Edge/preview/1.0.0/AzureVideoAnalyzerSdkDefinitions.json).

### <a name="enumerate-pipeline-topologies"></a>Enumeración de topologías de canalización

En este paso se enumeran todas las [topologías de canalización](pipeline.md) del módulo.

1. Haga clic con el botón derecho en el módulo **avaedge** y seleccione **Invoke Module Direct Method** (Invocar método directo del módulo) en el menú contextual.
1. Escriba **pipelineTopologyList** en el cuadro de edición y presione la tecla Entrar.
1. Copie la siguiente carga JSON, péguela en el cuadro de edición y presione la tecla Entrar.

   ```json
   {
     "@apiVersion": "1.0"
   }
   ```

Tras unos segundos, en la ventana **SALIDA** se muestra la respuesta siguiente:

```
[DirectMethod] Invoking Direct Method [pipelineTopologyList] to [deviceId/avaedge] ...
[DirectMethod] Response from [deviceId/avaedge]:
{
  "status": 200,
  "payload": {
    "value": []
  }
}
```

Se espera esa respuesta porque no se ha creado ninguna topología de canalización.

### <a name="set-a-pipeline-topology"></a>Establecimiento de una topología de canalización

Con los mismos pasos de antes, puede invocar a `pipelineTopologySet` para establecer una topología de canalización con el siguiente JSON como carga. Se creará una topología de canalización llamada _MotionDetection_.

```json
{
  "@apiVersion": "1.0",
  "name": "MotionDetection",
  "properties": {
    "description": "Analyzing live video to detect motion and emit events",
    "parameters": [
      {
        "name": "rtspUrl",
        "type": "string",
        "description&quot;: &quot;rtspUrl"
      },
      {
        "name": "rtspUserName",
        "type": "string",
        "description": "rtspUserName",
        "default&quot;: &quot;dummyUserName"
      },
      {
        "name": "rtspPassword",
        "type": "string",
        "description": "rtspPassword",
        "default&quot;: &quot;dummypw"
      }
    ],
    "sources": [
      {
        "@type": "#Microsoft.VideoAnalyzer.RtspSource",
        "name": "rtspSource",
        "transport": "tcp",
        "endpoint": {
          "@type": "#Microsoft.VideoAnalyzer.UnsecuredEndpoint",
          "credentials": {
            "@type": "#Microsoft.VideoAnalyzer.UsernamePasswordCredentials",
            "username": "${rtspUserName}",
            "password&quot;: &quot;${rtspPassword}"
          },
          "url&quot;: &quot;${rtspUrl}"
        }
      }
    ],
    "processors": [
      {
        "@type": "#Microsoft.VideoAnalyzer.MotionDetectionProcessor",
        "sensitivity": "medium",
        "name": "motionDetection",
        "inputs": [
          {
            "nodeName": "rtspSource",
            "outputSelectors": []
          }
        ]
      }
    ],
    "sinks": [
      {
        "hubOutputName": "inferenceOutput",
        "@type": "#Microsoft.VideoAnalyzer.IotHubMessageSink",
        "name": "iotHubSink",
        "inputs": [
          {
            "nodeName&quot;: &quot;motionDetection"
          }
        ]
      }
    ]
  }
}
```

Esta carga de JSON crea una topología que define tres parámetros, dos de los cuales tienen valores predeterminados. La topología tiene un nodo de origen ([Origen RTSP](pipeline.md#rtsp-source)), un nodo de procesador ([Procesador de detección de movimiento](pipeline.md#motion-detection-processor)) y un nodo receptor ([Receptor de mensajes de IoT Hub](pipeline.md#iot-hub-message-sink)). La carga muestra la representación visual de la topología.

Tras unos segundos, en la ventana **SALIDA** se muestra la respuesta siguiente:

```json
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2021-03-21T18:16:46.491Z",
      "lastModifiedAt": "2021-03-21T18:16:46.491Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
      "parameters": [
        {
          "name": "rtspPassword",
          "type": "string",
          "description": "rtspPassword",
          "default": "dummypw"
        },
        {
          "name": "rtspUrl",
          "type": "string",
          "description": "rtspUrl"
        },
        {
          "name": "rtspUserName",
          "type": "string",
          "description": "rtspUserName",
          "default": "dummyUserName"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.VideoAnalyzer.RtspSource",
          "name": "rtspSource",
          "transport": "tcp",
          "endpoint": {
            "@type": "#Microsoft.VideoAnalyzer.UnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.VideoAnalyzer.UsernamePasswordCredentials",
              "username": "${rtspUserName}",
              "password": "${rtspPassword}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.VideoAnalyzer.MotionDetectionProcessor",
          "sensitivity": "medium",
          "eventAggregationWindow": "PT1S",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.VideoAnalyzer.IotHubMessageSink",
          "hubOutputName": "inferenceOutput",
          "name": "iotHubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        }
      ]
    }
  }
}
```

El estado que se devuelve es 201, que indica que se ha creado una topología.

Pruebe los siguientes pasos:

- Vuelva a invocar a `pipelineTopologySet`. El código de estado que se devuelve es 200. Este código indica que se ha actualizado correctamente una topología ya existente.
- Vuelva a invocar a `pipelineTopologySet`, pero cambie la cadena de descripción. El código de estado devuelto es 200 y la descripción se ha actualizado al nuevo valor.
- Invoque a `pipelineTopologyList` como se describió en la sección anterior. Ahora puede ver la topología _MotionDetection_ en la carga devuelta.

### <a name="read-the-pipeline-topology"></a>Lectura de la topología de canalización

Invoque a `pipelineTopologyGet` mediante el uso de la siguiente carga:

```json
{
  "@apiVersion": "1.0",
  "name": "MotionDetection"
}
```

Tras unos segundos, en la ventana **SALIDA** se muestra la respuesta siguiente:

```json
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2021-03-21T18:16:46.491Z",
      "lastModifiedAt": "2021-03-21T18:16:46.491Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
      "parameters": [
        {
          "name": "rtspPassword",
          "type": "string",
          "description": "rtspPassword",
          "default": "dummypw"
        },
        {
          "name": "rtspUrl",
          "type": "string",
          "description": "rtspUrl"
        },
        {
          "name": "rtspUserName",
          "type": "string",
          "description": "rtspUserName",
          "default": "dummyUserName"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.VideoAnalyzer.RtspSource",
          "name": "rtspSource",
          "transport": "tcp",
          "endpoint": {
            "@type": "#Microsoft.VideoAnalyzer.UnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.VideoAnalyzer.UsernamePasswordCredentials",
              "username": "${rtspUserName}",
              "password": "${rtspPassword}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.VideoAnalyzer.MotionDetectionProcessor",
          "sensitivity": "medium",
          "eventAggregationWindow": "PT1S",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.VideoAnalyzer.IotHubMessageSink",
          "hubOutputName": "inferenceOutput",
          "name": "iotHubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        }
      ]
    }
  }
}
```

En la carga de respuesta, observe estos detalles:

- El código de estado es 200, lo que indica que se ha realizado correctamente.
- La carga incluye las marcas de tiempo `createdAt` y `lastModifiedAt`.

### <a name="create-a-live-pipeline-by-using-the-topology"></a>Creación de una canalización en vivo mediante la topología

A continuación, cree una canalización en vivo que haga referencia a la topología de canalización anterior. Invoque el método directo `livePipelineSet` mediante la siguiente carga:

```json
{
  "@apiVersion": "1.0",
  "name": "mdpipeline1",
  "properties": {
    "topologyName": "MotionDetection",
    "description": "Sample pipeline description",
    "parameters": [
      {
        "name": "rtspUrl",
        "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
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
```

Tenga en cuenta que esta carga:

- Especifica la topología (_MotionDetection_)que usará la canalización en vivo.
- Contiene el valor del parámetro para `rtspUrl`, el cual no tenía un valor predeterminado en la carga de topología. Este valor es un vínculo al vídeo de ejemplo siguiente:

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4LTY4]

Tras unos segundos, en la ventana **SALIDA** se muestra la respuesta siguiente:

```json
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2021-03-21T18:27:41.639Z",
      "lastModifiedAt": "2021-03-21T18:27:41.639Z"
    },
    "name": "mdpipeline1",
    "properties": {
      "state": "Inactive",
      "description": "Sample pipeline description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspPassword",
          "value": "testpassword"
        },
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        },
        {
          "name": "rtspUserName",
          "value": "testuser"
        }
      ]
    }
  }
}
```

En la carga de respuesta, observe que:

- El código de estado es 201, lo que indica que se ha creado una canalización en vivo.
- El estado es `Inactive`, lo que indica que la canalización en vivo se creó, pero no se activó. Para más información, consulte [Estados de canalización](pipeline.md#pipeline-states).

Intente los siguientes métodos directos como se indica a continuación:

- Vuelva a invocar a `livePipelineSet` con la misma carga. Observe que el código de estado devuelto es 200.
- Vuelva a invocar a `livePipelineSet`, pero con otra descripción. Observe la descripción actualizada en la carga de respuesta, la cual indica que la canalización en vivo se actualizó correctamente.
- Invoque a `livePipelineSet`, pero cámbiele el nombre a `mdpipeline2` y `rtspUrl`, a `rtsp://rtspsim:554/media/lots_015.mkv`. En la carga de respuesta, observe la canalización en vivo que se acaba de crear (es decir, el código de estado 201).

  > [!NOTE]
  > Como se explica en [Topologías de canalización](pipeline.md#pipeline-topologies), puede crear varias canalizaciones en vivo para analizar secuencias de vídeo en directo de muchas cámaras con la misma topología de canalización. Si crea más canalizaciones en vivo, procure eliminarlas durante el paso de limpieza.

### <a name="activate-the-live-pipeline"></a>Activación de la canalización en vivo

Puede activar la canalización en vivo para iniciar el flujo de vídeo en directo (simulado) mediante la canalización. Invoque el método directo `livePipelineActivate` mediante la siguiente carga:

```json
{
  "@apiVersion": "1.0",
  "name": "mdpipeline1"
}
```

Tras unos segundos, en la ventana **SALIDA** se muestra la respuesta siguiente:

```json
{
  "status": 200,
  "payload": null
}
```

El código de estado 200 indica que la canalización en vivo se ha activado correctamente.

### <a name="check-the-state-of-the-live-pipeline"></a>Comprobación del estado de la canalización en vivo

Invoque el método directo `livePipelineGet` mediante la siguiente carga:

```json
{
  "@apiVersion": "1.0",
  "name": "mdpipeline1"
}
```

Tras unos segundos, en la ventana **SALIDA** se muestra la respuesta siguiente:

```json
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2021-03-21T18:27:41.639Z",
      "lastModifiedAt": "2021-03-21T18:27:41.639Z"
    },
    "name": "mdpipeline1",
    "properties": {
      "state": "Active",
      "description": "Sample pipeline description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspPassword",
          "value": "testpassword"
        },
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        },
        {
          "name": "rtspUserName",
          "value": "testuser"
        }
      ]
    }
  }
}
```

En la carga de respuesta, observe los siguientes detalles:

- El código de estado es 200, lo que indica que se ha realizado correctamente.
- El estado es `Active`, lo que indica que la canalización en vivo se encuentra activa en este momento.

## <a name="observe-results"></a>Observación de resultados

La canalización en vivo que ha creado y activado usa el nodo del procesador de detección de movimiento para detectar el movimiento en la secuencia de vídeo en directo entrante y envía eventos al receptor de IoT Hub. A continuación, estos eventos se retransmiten a IoT Hub como mensajes, como se puede observar ahora. Los mensajes de la ventana **SALIDA** tendrán el siguiente "cuerpo":

```json
{
  "timestamp": 145471641211899,
  "inferences": [
    {
      "type": "motion",
      "motion": {
        "box": {
          "l": 0.514644,
          "t": 0.574627,
          "w": 0.3375,
          "h": 0.096296
        }
      }
    }
  ]
}
```

La sección `inferences` indica que el tipo es movimiento. Proporciona más datos sobre el evento de movimiento. Además incluye un rectángulo de selección para la región del fotograma de vídeo (en la marca de tiempo especificada) donde se detectó el movimiento.

## <a name="invoke-more-direct-method-calls-to-clean-up"></a>Invocación de llamadas de método directo adicionales para la limpieza

A continuación, puede invocar métodos directos para desactivar y eliminar la canalización en vivo (en ese orden).

### <a name="deactivate-the-live-pipeline"></a>Desactivación de la canalización en vivo

Invoque el método directo `livePipelineDeactivate` mediante la siguiente carga:

```json
{
  "@apiVersion": "1.0",
  "name": "mdpipeline1"
}
```

Tras unos segundos, en la ventana **SALIDA** se muestra la respuesta siguiente:

```json
{
  "status": 200,
  "payload": null
}
```

El código de estado 200 indica que la canalización en vivo se ha desactivado correctamente.

A continuación, intente invocar `livePipelineGet` como se ha indicado en este mismo artículo. Observe el valor de estado.

### <a name="delete-the-live-pipeline"></a>Eliminación de la canalización en vivo

Invoque el método directo `livePipelineDelete` mediante la siguiente carga:

```json
{
  "@apiVersion": "1.0",
  "name": "mdpipeline1"
}
```

Tras unos segundos, en la ventana **SALIDA** se muestra la respuesta siguiente:

```json
{
  "status": 200,
  "payload": null
}
```

El código de estado 200 indica que la canalización en vivo se ha eliminado correctamente.

Si también creó la canalización denominada _mdpipeline2_, no podrá eliminar la topología de canalización sin eliminar también esta canalización adicional. Vuelva a invocar el método directo `livePipelineDelete` mediante la siguiente carga:

```
{
    "@apiVersion" : "1.0",
    "name" : "mdpipeline2"
}
```

Tras unos segundos, en la ventana **SALIDA** se muestra la respuesta siguiente:

```json
{
  "status": 200,
  "payload": null
}
```

El código de estado 200 indica que la canalización en vivo se ha eliminado correctamente.

Puede invocar a `livePipelineList` con la misma carga que `pipelineTopologyList`. Observe que no se enumera ninguna canalización en vivo.

### <a name="delete-the-pipeline-topology"></a>Eliminación de la topología de canalización

Una vez eliminadas todas las canalizaciones en vivo, puede invocar el método directo `pipelineTopologyDelete` con la siguiente carga:

```json
{
  "@apiVersion": "1.0",
  "name": "MotionDetection"
}
```

Tras unos segundos, en la ventana **SALIDA** se muestra la respuesta siguiente:

```json
{
  "status": 200,
  "payload": null
}
```

El código de estado 200 indica que la topología se ha eliminado correctamente.

Puede intentar invocar a `pipelineTopologyList` y observar que el módulo no contiene topologías.

## <a name="clean-up-resources"></a>Limpieza de recursos

[!INCLUDE [prerequisites](./includes/common-includes/clean-up-resources.md)]

## <a name="next-steps"></a>Pasos siguientes

- Pruebe el [inicio rápido para grabar vídeos en la nube al detectarse movimiento](detect-motion-record-video-clips-cloud.md).
- Pruebe el [inicio rápido para analizar vídeo en directo](analyze-live-video-use-your-model-http.md).
- Más información sobre los [mensajes de diagnóstico](monitor-log-edge.md).

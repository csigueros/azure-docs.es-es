---
title: Conexión de la cámara a la nube mediante un adaptador de dispositivo remoto
description: En este artículo se explica cómo conectar una cámara al servicio Azure Video Analyzer mediante un adaptador de dispositivo remoto.
ms.topic: how-to
ms.date: 11/01/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 71bcf82420d9777158cbb878c4943b350d51353b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091066"
---
# <a name="connect-cameras-to-the-cloud-using-a-remote-device-adapter"></a>Conexión de cámaras a la nube mediante un adaptador de dispositivo remoto

[!INCLUDE [header](includes/cloud-env.md)]

El servicio Azure Video Analyzer permite a los usuarios capturar y grabar vídeo desde cámaras RTSP conectadas a la nube. Esto requiere que estas cámaras sean accesibles a través de Internet, lo que puede no estar permitido en muchos casos. En su lugar, puede implementar el módulo perimetral de Video Analyzer en un dispositivo perimetral ligero de la misma red (privada) que las cámaras RTSP y conectar el dispositivo perimetral a Internet. El módulo perimetral ahora se puede configurar como un *adaptador* que permite al servicio Video Analyzer conectarse a los *dispositivos remotos* (cámaras). El módulo perimetral actúa como una [puerta de enlace transparente](../../../iot-edge/iot-edge-as-gateway.md) para el tráfico de vídeo entre las cámaras RTSP y el servicio Video Analyzer.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/use-remote-device-adapter/use-remote-device-adapter.svg" alt-text="Conexión de cámaras a la nube con un adaptador de dispositivo remoto":::

## <a name="pre-reading"></a>Lectura previa

* [Conexión de cámaras a la nube](connect-cameras-to-cloud.md)

## <a name="prerequisites"></a>Requisitos previos

* Una cuenta de Azure que tenga una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), en caso de que aún no lo haya hecho.
* IoT Hub
* [Cuenta de Video Analyzer](../create-video-analyzer-account.md) con lo siguiente:
  * Cuenta de almacenamiento
  * Identidad administrada asignada por el usuario (UAMI)
  * [IoT Hub debe estar asociado a la cuenta de Video Analyzer](../create-video-analyzer-account.md#post-deployment-steps)
* [Dispositivo IoT Edge con el módulo perimetral de Video Analyzer instalado y configurado](../edge/deploy-iot-edge-device.md)
* [Cámaras RTSP](../quotas-limitations.md#supported-cameras)
  * Asegúrese de que las cámaras están en la misma red que el dispositivo perimetral.
  * Asegúrese de que puede configurar la cámara para enviar vídeo con un ancho de banda máximo o por debajo de él (medido en kBps o kilobits/segundo).

## <a name="overview"></a>Información general
Para conectar una cámara al servicio Video Analyzer mediante un adaptador de dispositivo remoto, debe:

1. Crear un dispositivo IoT en la instancia de IoT Hub para representar la cámara RTSP
1. Crear un adaptador de dispositivo en el módulo perimetral de Video Analyzer para que actúe como puerta de enlace transparente para el dispositivo anterior
1. Usar el dispositivo IoT y el adaptador de dispositivo al crear una canalización activa en el servicio Video Analyzer para capturar y grabar vídeo desde la cámara



## <a name="create-an-iot-device"></a>Creación de un dispositivo IoT

Cree un dispositivo IoT para representar cada cámara RTSP que necesite conectar al servicio Video Analyzer. En Azure Portal:

1. Vaya a la instancia de IoT Hub.
1. Seleccione el panel **Dispositivos** en **Administración de dispositivos**.
1. Seleccione **+Agregar dispositivo**. 
1. Escriba un **id. de dispositivo** mediante una cadena única (por ejemplo: building404-camera1).
1. El **Tipo de autenticación** debe ser **Clave simétrica**.
1. Todas las demás propiedades se pueden establecer en los valores predeterminados.
1. Seleccione **Guardar** para crear el dispositivo IoT.
1. Seleccione el dispositivo IoT y registre la **Clave principal** o la **Clave secundaria**, ya que se necesitará más adelante.

## <a name="create-a-remote-device-adapter"></a>Creación de un adaptador de dispositivo remoto

Para permitir que el módulo perimetral de Video Analyzer actúe como puerta de enlace transparente para vídeo entre la cámara y el servicio Video Analyzer, debe crear un adaptador de dispositivo remoto para cada cámara. Invoque el [método directo **remoteDeviceAdapterSet**](../edge/direct-methods.md) que requiere los siguientes valores: 

* Id. de dispositivo del dispositivo IoT
* Clave principal del dispositivo IoT
* Dirección IP de la cámara

En Azure Portal:

1. Vaya a la instancia de IoT Hub.
1. Seleccione el panel **IoT Edge** en **Administración automática de dispositivos**.
1. Seleccione el dispositivo IoT Edge (por ejemplo, **ava-sample-device**) en el que se ha implementado el módulo perimetral de Video Analyzer.
1. En los módulos, seleccione el módulo perimetral de Video Analyzer (por ejemplo, **avaedge**).
1. Seleccione **</>Método directo**. 
1. Escriba **remoteDeviceAdapterSet** como nombre del método.
1. En **Carga**, escriba lo siguiente:

```
 {
   "@apiVersion" : "1.1",
   "name": "<name of remote device adapter such as remoteDeviceAdapterCamera1>",
   "properties": {
     "target": {
       "host": "<Camera's IP address>"
      },
     "iotHubDeviceConnection": {
      "deviceId": "<IoT Hub Device ID>",
      "credentials": {
        "@type": "#Microsoft.VideoAnalyzer.SymmetricKeyCredentials",
        "key": "<Primary or Secondary Key>"
       }
     }
   }
 }
 
```

Si se realiza correctamente, recibirá una respuesta con un código de estado 201.


## <a name="create-pipeline-topology-in-the-video-analyzer-service"></a>Creación de una topología de canalización en el servicio Video Analyzer

Al crear una topología de canalización en la nube para ingerir desde una cámara detrás de un firewall, la tunelización debe habilitarse en el nodo de origen de RTSP de la topología de canalización. Vea un ejemplo de este tipo de [topología de canalización](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/cloud-record-camera-behind-firewall).  

En este [inicio rápido](get-started-livepipelines-portal.md#deploy-a-live-pipeline) se describen los pasos para crear una topología de canalización y una canalización activa en Azure Portal. Use la topología de ejemplo `Live capture, record, and stream from RTSP camera behind firewall`.

Los valores siguientes, basados en el dispositivo IoT aprovisionado en las instrucciones anteriores, son necesarios para habilitar la tunelización en el nodo de origen de RTSP:

* Nombre de la instancia de IoT Hub
* Identificador de dispositivo IoT Hub

```
            {
                "@type": "#Microsoft.VideoAnalyzer.RtspSource",
                "name": "rtspSource",
                "transport": "tcp",
                "endpoint": {
                    "@type": "#Microsoft.VideoAnalyzer.UnsecuredEndpoint",
                    "url": "${rtspUrlParameter}",
                    "credentials": {
                        "@type": "#Microsoft.VideoAnalyzer.UsernamePasswordCredentials",
                        "username": "${rtspUsernameParameter}",
                        "password": "${rtspPasswordParameter}"
                    },
                    "tunnel": { 
                        "@type": "#Microsoft.VideoAnalyzer.SecureIotDeviceRemoteTunnel",
                        "iotHubName" : "<IoT Hub Name>",
                        "deviceId": "${ioTHubDeviceIdParameter}"
                    }
                }
            }
``` 

Asegúrese de que:
* `Transport` se establezca en `tcp`.
* `Endpoint` se establezca en `UnsecuredEndpoint`.
* `Tunnel` se establezca en `SecureIotDeviceRemoteTunnel`.

## <a name="create-and-activate-a-live-pipeline"></a>Creación y activación de una canalización activa

Al crear la canalización activa, se deben definir la dirección URL de RTSP, el nombre de usuario de RTSP, la contraseña de RTSP y el id. de dispositivo de IoT Hub. A continuación se muestra una carga de ejemplo.

```
   {
    "name": "record-from-building404-camera1",
    "properties": {
        "topologyName": "record-camera-behind-firewall",
        "description": "Capture, record and stream video from building404-camera1 via a remote device adapter",
        "bitrateKbps": 1500,
        "parameters": [
            {
                "name": "rtspUrlParameter",
                "value": "rtsp://localhost:554/<camera-specific-suffix>"
            },
            {
                "name": "rtspUsernameParameter",
                "value": "<User name for building404-camera1>"
            },
            {
                "name": "rtspPasswordParameter",
                "value": "<Password for building404-camera1>"
            },
            {
                "name": "ioTHubDeviceIdParameter",
                "value": "<IoT Hub Device ID such as building404-camera1>"
            },
            {
                "name": "videoName",
                "value": "video-from-building404-camera1"
            }
          ]
       }
   }
```
La dirección URL de RTSP debe ser **localhost**. Asegúrese de que el valor `bitrateKbps` coincide con la configuración de velocidad de bits máxima para el vídeo de la cámara RTSP.

Después de crear la canalización activa, la canalización se puede activar para iniciar la grabación en el recurso de vídeo de Video Analyzer. En el [inicio rápido](get-started-livepipelines-portal.md#deploy-a-live-pipeline) mencionado en el paso anterior también se describe cómo activar una canalización activa en Azure Portal.


### <a name="playback-recorded-video-in-the-azure-portal"></a>Reproducción del vídeo grabado en Azure Portal

1. Después de activar la canalización activa, el recurso de vídeo estará disponible en el panel **Vídeos** de la cuenta de Video Analyzer de Azure Portal. El estado indicará **En uso**, ya que la canalización está activa y grabando.
1. Seleccione el recurso de vídeo que se definió en la canalización activa para ver el vídeo.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/camera-1800s-mkv.png" alt-text="Captura de pantalla del vídeo en directo capturado por la canalización activa en la nube.":::

Si se producen errores al intentar reproducir el vídeo, siga los pasos de [esta guía de solución de problemas](troubleshoot.md#unable-to-play-video-after-activating-live-pipeline).

[!INCLUDE [activate-deactivate-pipeline](../edge/includes/common-includes/activate-deactivate-pipeline.md)]

## <a name="next-steps"></a>Pasos siguientes

Ahora que existe un vídeo en la cuenta de Video Analyzer, puede exportar un clip de este vídeo grabado al formato MP4 mediante [este tutorial](export-portion-of-video-as-mp4.md).

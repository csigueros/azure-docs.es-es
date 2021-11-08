---
title: Detección de cámaras compatibles con ONVIF en la subred local
description: En este procedimiento se muestra cómo puede usar el módulo perimetral de Video Analyzer para detectar cámaras compatibles con ONVIF en la subred local.
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8e0629294ba5d19315da096072fa804824649acc
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131564031"
---
# <a name="discovering-onvif-capable-cameras-in-the-local-subnet"></a>Detección de cámaras compatibles con ONVIF en la subred local

[!INCLUDE [header](includes/edge-env.md)]

Este procedimiento le guía a través del uso del módulo perimetral de Azure Video Analyzer para detectar cámaras compatibles con ONVIF en la misma subred que el dispositivo IoT Edge. Open Network Video Interface Forum (ONVIF) es un estándar abierto en el que los dispositivos físicos discretos basados en IP, como las cámaras de vigilancia, pueden comunicarse con otros dispositivos y software en red. Para más información sobre ONVIF, visite el sitio web de [ONVIF](https://www.onvif.org/about/mission/).

## <a name="prerequisites"></a>Prerrequisitos

Para completar los pasos de este artículo, necesitará lo siguiente:

- Tener una suscripción de Azure activa.
- Instalación de la [extensión de la CLI de Azure para IoT](https://github.com/Azure/azure-iot-cli-extension#installation)
- Elija entre lo siguiente:
  - [Inicio rápido: Introducción a Azure Video Analyzer](get-started-detect-motion-emit-events.md)
  - [Inicio rápido: Introducción a Azure Video Analyzer en Azure Portal](get-started-detect-motion-emit-events-portal.md)
- Implementación de la versión 1.1 (o posterior) del módulo perimetral de Video Analyzer en el dispositivo IoT Edge
- Si se usa una máquina virtual [Hyper-V](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/mt169373(v=ws.11)) o [EFLOW](../../../iot-edge/how-to-install-iot-edge-on-windows.md?view=iotedge-2018-06&tabs=windowsadmincenter&preserve-view=true), se requiere un [conmutador externo](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-switch-for-hyper-v-virtual-machines) para realizar la llamada de método directo onvifDeviceDiscover. Este requisito se debe a una llamada de [multidifusión](https://en.wikipedia.org/wiki/Multicast) usada para la detección de dispositivos ONVIF.

La característica ONVIF del módulo perimetral de Video Analyzer requiere opciones específicas de creación del contenedor, como se describe en [Habilitación de la característica de detección de ONVIF](#enable-onvif-discovery-feature). Esta característica de detección de ONVIF requiere que el puerto 3702 esté disponible.
  
> [!NOTE]
> Si tiene una nueva implementación de la versión 1.1 del módulo perimetral de Video Analyzer, puede ir directamente a la sección [Uso de llamadas de método directo](#use-direct-method-calls). De lo contrario, siga las secciones siguientes para actualizar el módulo perimetral de Video Analyzer existente para habilitar la característica de detección de ONVIF.  

## <a name="check-the-version"></a>Compruebe la versión.

Desde el símbolo del sistema, ejecute el siguiente comando:

```CLI
az account set --subscription <YOUR_SUBSCRIPTION_NAME>
az iot hub module-twin show -m <VIDEO_ANALYZER_IOT_EDGE_MODULE_NAME> -n <IOT_HUB_NAME> -d <IOT_EDGE_DEVICE_NAME> --query 'properties.reported.ProductInfo' -o tsv
```

Si el resultado del comando anterior es **Azure Video Analyzer:1.0.1**, ejecute los pasos siguientes para actualizar el módulo perimetral de Video Analyzer a la versión 1.1.

1. En Azure Portal, vaya a la instancia de IoT Hub (`<IOT_HUB_NAME>` más arriba) que se usa con la implementación del módulo perimetral de Video Analyzer.
1. Haga clic en **IoT Edge** en Administración de dispositivos automática y seleccione el dispositivo IoT Edge (`<IOT_EDGE_DEVICE_NAME>` más arriba) en el que se ha implementado el módulo perimetral de Video Analyzer.
1. Haga clic en **Establecer módulos** y, a continuación, en **Revisar y crear**.
1. Haga clic en **Crear**.
1. Después de unos minutos, el módulo perimetral de Video Analyzer se actualizará y puede volver a ejecutar el comando anterior para comprobarlo.

### <a name="enable-onvif-discovery-feature"></a>Habilitación de la característica de detección de ONVIF

A continuación, agregue opciones adicionales a las opciones de creación del contenedor para el módulo perimetral de Video Analyzer. Esto le permite comunicarse a través de la red host para detectar cámaras habilitadas para ONVIF en la misma subred.

1. En Azure Portal, vaya a IoT Hub.
1. Haga clic en **IoT Edge** en Administración de dispositivos automática y seleccione el dispositivo IoT Edge en el que se ha implementado el módulo perimetral de Video Analyzer.
1. Haga clic en **Establecer módulos** y seleccione el módulo perimetral de Video Analyzer.
1. Vaya a la pestaña **Opciones de creación del contenedor** y agregue las siguientes entradas JSON a las secciones correspondientes:

    - En el cuadro de entrada JSON, después del primer `{`, escriba lo siguiente:
  
        ```JSON
        "NetworkingConfig": {
        "EndpointsConfig": {
            "host": {}
            }
        },
        ```
    - Objeto JSON `HostConfig`:

        ```JSON
        "NetworkMode": "host",
        ```
1. Haga clic en **Actualizar** en la parte inferior.
1. Haga clic en **Revisar y crear**
1. Haga clic en **Crear**

## <a name="use-direct-method-calls"></a>Uso de llamadas de método directo

El módulo perimetral de Video Analyzer proporciona llamadas de método directo para la detección de ONVIF de las cámaras conectadas a la red de la misma subred. Los pasos siguientes se aplican a las secciones `onvifDeviceDiscover` y `onvifDeviceGet` siguientes:

1. En Azure Portal, vaya a IoT Hub.
1. Haga clic en **IoT Edge** en Administración de dispositivos automática y seleccione el dispositivo IoT Edge en el que se ha implementado el módulo perimetral de Video Analyzer.
1. Haga clic en el módulo perimetral de Video Analyzer y luego en **Método directo** en la barra de menús de la parte superior.

### <a name="onvifdevicediscover"></a>onvifDeviceDiscover

Este método directo enumera todos los dispositivos ONVIF detectables de la misma subred que el módulo perimetral de Video Analyzer.


1. En el nombre del método, escriba:

    ```JSON
    onvifDeviceDiscover
    ```
1. En la carga, escriba:

    ```JSON
    {
        "@apiVersion":"1.1",
        "discoveryDuration":"PT8S"
    }
    ```

   La duración de la detección es la cantidad de tiempo que el módulo perimetral de Video Analyzer espera para recibir respuestas de los dispositivos detectables de ONVIF. Puede que sea necesario ajustar este valor en un entorno grande.

1. En unos segundos debería ver un`result` como el siguiente:

    ```JSON
    {
        "status": 200,
        "payload": {
            "value": [
                {
                    "serviceIdentifier": "{urn:uuid}",
                    "remoteIPAddress": "{IP_ADDRESS}",
                    "scopes": [
                        "onvif://www.onvif.org/type/Network_Video_Transmitter",
                        "onvif://www.onvif.org/name/{CAMERA_MANUFACTURE}",
                        "onvif://www.onvif.org/location/",
                        "onvif://www.onvif.org/hardware/{CAMERA_MODEL}",
                        "onvif://www.onvif.org/Profile/Streaming",
                        "onvif://www.onvif.org/Profile/G",
                        "onvif://www.onvif.org/Profile/T"
                    ],
                    "endpoints": [
                        "http://<IP_ADDRESS>/onvif/device_service",
                        "https://<IP_ADDRESS>/onvif/device_service"
                    ],
                    
                }
            ]
        }
    }
    ```
    El estado devuelto de 200 indica que la llamada de método directo se realizó correctamente.

### <a name="onvifdeviceget"></a>onvifDeviceGet

La llamada onvifDeviceGet admite puntos de conexión no seguros y habilitados para TLS. Esta llamada de método directo recupera información detallada sobre un dispositivo ONVIF específico.

# <a name="unsecuredendpoint"></a>[UnsecuredEndpoint](#tab/unsecuredendpoint)

> [!NOTE]
> Cuando se realiza la llamada onvifDeviceGet a un punto de conexión no seguro de una cámara habilitada para ONVIF, debe establecer la configuración de la identidad de módulo gemelo de Video Edge `"allowUnsecuredEndpoints"` en `true`. Para más información vea el artículo [Propiedades módulos gemelos](./module-twin-configuration-schema.md).

1. En el nombre del método, escriba:

    ```JSON
    onvifDeviceGet
    ```
1. En la carga, escriba:

    ```JSON
    {
        "endpoint": {
            "@type": "#Microsoft.VideoAnalyzer.UnsecuredEndpoint",
            "credentials": {
                "username": "<USER_NAME>",
                "password": "<PASSWORD>",
                "@type": "#Microsoft.VideoAnalyzer.UsernamePasswordCredentials"
            },
            "url": "http://<IP_ADDRESS>/onvif/device_service"
        },
        "@apiVersion": "1.1"
    }
    ```

# <a name="tlsendpoint"></a>[TlsEndpoint](#tab/tlsendpoint)

1. En el nombre del método, escriba:

    ```JSON
    onvifDeviceGet
    ```
1. En la carga, escriba:

    ```JSON
    {
        "endpoint": {
            "@type": "#Microsoft.VideoAnalyzer.TlsEndpoint",
            "credentials": {
                "username": "<USER_NAME>",
                "password": "<PASSWORD>",
                "@type": "#Microsoft.VideoAnalyzer.UsernamePasswordCredentials"
            },
            "url": "https://<IP_ADDRESS>/onvif/device_service"
        },
        "@apiVersion": "1.1"
    }

---

In the above payload:

- `url` is the IP address of the ONVIF device from which you wish to get additional details
- `username` is the user name that is used to authenticate with the device, which has permissions to the ONVIF features. Some devices require specific accounts to be created for this purpose.
- `password` is the password for that user name

    Within a few seconds you should see a result such as:

    ```JSON
    {
        "status": 200,
        "payload": {
            "hostname": {
                "fromDhcp": true,
                "hostname": "{NAME_OF_THE_ONVIF_DEVICE}"
            },
            "systemDateTime": {
                "type": "ntp",
                "time": "2021-09-28T03:05:05.000Z",
                "timeZone": "GMT"
            },
            "dns": {
                "fromDhcp": true,
                "ipv4Address": [
                    "{IP_ADDRESS}"
                ],
                "ipv6Address": []
            },
            "mediaProfiles": [
                {
                    "name": "{Profile1}",
                    "mediaUri": {
                        "uri": "{RTSP_URI}"
                    },
                    "videoEncoderConfiguration": {
                        "encoding": "h264",
                        "resolution": {
                            "width": 3840,
                            "height": 2160
                        },
                        "rateControl": {
                            "bitRateLimit": 15600,
                            "encodingInterval": 1,
                            "frameRateLimit": 30,
                            "guaranteedFrameRate": false
                        },
                        "quality": 50,
                        "h264": {
                            "govLength": 255,
                            "profile": "main"
                        }
                    }
                },
                {
                    "name": "{Profile2}",
                    "mediaUri": {
                        "uri": "{RTSP_URI}"
                    },
                    "videoEncoderConfiguration": {
                        "encoding": "h264",
                        "resolution": {
                            "width": 1280,
                            "height": 720
                        },
                        "rateControl": {
                            "bitRateLimit": 1900,
                            "encodingInterval": 1,
                            "frameRateLimit": 30,
                            "guaranteedFrameRate": false
                        },
                        "quality": 50,
                        "h264": {
                            "govLength": 255,
                            "profile": "main"
                        }
                    }
                }
            ]
        }
    }
    ```

### <a name="return-status-of-onvifdeviceget"></a>Estado devuelto de onvifDeviceGet

| Estado | Código      | Significado/solución                                                                                                                                                                                                            |
| ------ | --------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 200    | Correcto   | La llamada de método directo se completó correctamente.                                                                                                                                                                                |
| 400    | Bad Request  | La solicitud tenía un formato incorrecto   |
| 403    | Prohibido | La llamada de método directo no pudo recuperar correctamente la información solicitada del dispositivo ONVIF debido a un error de autenticación. Compruebe que el nombre de usuario y/o la contraseña del cuerpo del mensaje son correctos. |
| 500 | Error | Se produjo un error desconocido.  |
| 502    | Puerta de enlace incorrecta | Recibió una respuesta no válida de un servicio ascendente.  |
| 504    | Tiempo de espera   | La llamada de método directo expiró antes de que se recibiera la respuesta del dispositivo ONVIF.  |

## <a name="troubleshooting"></a>Solución de problemas

En esta sección se tratan algunos pasos de solución de problemas:

- En Azure Portal, en la hoja de método directo del módulo IoT Edge, si recibe el error "An error prevented the operation from successfully completing (Un error impidió que la operación se completara correctamente). The request failed with status code 504 (Se ha producido un error en la solicitud con el código de estado 504)". (Vea la imagen siguiente):

    :::image type="content" source="./media/camera-discovery/five-zero-four-error.png" alt-text="Captura de pantalla que muestra el error 504.":::

    Compruebe que la configuración de `"discoveryDuration":"PT8S"` en la llamada de método directo anterior es más corta que los valores `Connection Timeout` o `Method Timeout`.

    :::image type="content" source="./media/camera-discovery/five-zero-four-error-fix.png" alt-text="Captura de pantalla que muestra la corrección del error 504.":::

- Si en el campo `Results` del método directo se muestra "{"status":200,"payload":{"value":[]}}, puede que necesite duraciones de detección más largas.

    :::image type="content" source="./media/camera-discovery/result-status-two-hundred-null.png" alt-text="La devolución del mensaje se muestra en el campo &quot;Resultados&quot; del método directo":::

    Ajuste el valor de tiempo (x) en `"discoveryDuration":"PTxS"` a un número mayor. Ajuste también los valores `Connection Timeout` y `Method Timeout` en consecuencia.
- La llamada de método directo `onvifDeviceGet` no mostrará ningún perfil multimedia para secuencias multimedia codificadas H.265.
- Puede que se devuelva el estado de retorno 403 en caso de que la cuenta de usuario usada para conectarse al dispositivo ONVIF no tenga permisos para las características de cámara de ONVIF. Algunas cámaras compatibles con ONVIF requieren que se agregue un usuario a la configuración de seguridad de ONVIF para recuperar la información del dispositivo ONVIF.
- Actualmente, el módulo perimetral de Video Analyzer devolverá hasta 200 cámaras habilitadas para ONVIF que son accesibles en la misma subred a través de multidifusión. Esto también requiere que el puerto 3702 esté disponible.
- Si se llama a onvifDeviceGet con un punto de conexión TLS, el nombre del dispositivo ONVIF y la dirección IP deben agregarse al archivo hosts (/etc/hosts) en el dispositivo perimetral.  Si se usa un certificado autofirmado para el cifrado TLS, el certificado también debe agregarse al almacén de certificados del dispositivo IoT Edge. 
- Para la llamada de método directo onvifDeviceDiscover desde una máquina virtual EFLOW, se deben realizar los pasos siguientes:
  - Conexión a la máquina virtual EFLOW y ejecución de `sudo iptables -I INPUT -p udp -j ACCEPT`

## <a name="next-steps"></a>Pasos siguientes

- Pruebe el [inicio rápido para analizar vídeo en directo](analyze-live-video-use-your-model-http.md) con el dispositivo ONVIF detectado.

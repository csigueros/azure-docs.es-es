---
title: Conexión de dispositivos a Azure Video Analyzer
description: En este artículo se describe cómo conectar dispositivos a Azure Video Analyzer.
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 72a83593134b52c652d8d31bb441ecc083d88fea
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091372"
---
# <a name="connect-devices-to-azure-video-analyzer"></a>Conexión de dispositivos a Azure Video Analyzer

[!INCLUDE [header](includes/cloud-env.md)]

Para capturar y grabar vídeo desde un dispositivo, el servicio Azure Video Analyzer debe establecer una conexión [RTSP](../terminology.md#rtsp) con él. Si el dispositivo está detrás de un firewall, estas conexiones se bloquean y es posible que no siempre pueda crear reglas para permitir conexiones entrantes desde Azure. Para admitir estos dispositivos, puede crear e instalar una implementación de dispositivo [Azure IoT Plug and Play](../../../iot-develop/overview-iot-plug-and-play.md), que escucha los comandos enviados a través de IoT Hub desde Video Analyzer y, a continuación, abre un túnel de WebSocket seguro al servicio. Una vez establecido este túnel, Video Analyzer puede conectarse al servidor RTSP.

## <a name="overview"></a>Información general 

En este artículo se brindan conceptos generales sobre la creación de una implementación de dispositivo Azure IoT PnP que puede permitir que Video Analyzer capture y grabe vídeo desde un dispositivo. 

La aplicación tendrá que: 

1. Ejecutarse como dispositivo IoT 
1. Implemente la interfaz de [IoT PnP](../../../iot-develop/overview-iot-plug-and-play.md) con un comando específico (`tunnelOpen`). 
1. Al recibir este comando: 
   * Valide los argumentos recibidos. 
   * Abra una conexión de WebSocket seguro a la dirección URL indicada mediante el token proporcionado.
   * Reenvíe los bytes de WebSocket a la conexión TCP del servidor RTSP de la cámara.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/connect-devices/connect-devices.svg" alt-text="Conexión de dispositivos a la nube":::

## <a name="run-as-an-iot-device"></a>Ejecutarse como dispositivo IoT 

La aplicación Video Analyzer se implementará como complemento PnP de Video Analyzer. Esto requiere el uso de uno de los [SDK de dispositivo IoT de Azure](../../../iot-develop/libraries-sdks.md#device-sdks) para crear la implementación del dispositivo IoT PnP. Registre el dispositivo IoT en su instancia de IoT Hub para obtener el identificador de dispositivo de IoT Hub y la cadena de conexión del dispositivo.

### <a name="iot-device-clientconfiguration"></a>Configuración del cliente de dispositivo IoT

* Establezca OPTION_MODEL_ID en `“dtmi:azure:videoanalyzer:WebSocketTunneling;1”` para admitir consultas PnP.  
* Asegúrese de que el dispositivo use el protocolo MQTT o MQTT sobre WebSockets para conectarse a Azure IoT Hub. 
    * Conéctese a IoT Hub a través de un proxy HTTPS si está configurado en el dispositivo IoT.  
* Registro de la devolución de llamada para el `tunnelOpen` método directo 

## <a name="implement-the-iot-pnp-interface-for-video-analyzer"></a>Implementación de la interfaz de IoT PnP para Video Analyzer

El siguiente modelo de [Lenguaje de definición de Digital Twins (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) describe un dispositivo que puede conectarse a Video Analyzer.

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:azure:videoanalyzer:WebSocketTunneling;1",
  "@type": "Interface",
  "displayName": "Azure Video Analyzer Web Socket Tunneling",
  "description": "This interface enables media publishing to Azure Video Analyzer service from a RTSP compatible device which is located behind a firewall or NAT device.",
  "contents": [
    {
      "@type": "Command",
      "displayName": "Tunnel Open",
      "name": "tunnelOpen",
      "request": {
        "@type": "CommandPayload",
        "displayName": "Parameters",
        "name": "parameters",
        "schema": {
          "@type": "Object",
          "fields": [
            {
              "displayName": "Remote Endpoint",
              "description": "The remote endpoint for the web socket tunnel.",
              "name": "remoteEndpoint",
              "schema": "string"
            },
            {
              "displayName": "Remote Authorization Token",
              "description": "The bearer token for the web socket authentication.",
              "name": "remoteAuthorizationToken",
              "schema": "string"
            },
            {
              "displayName": "Local Port",
              "description": "The local port where web socket data should be tunneled to.",
              "name": "localPort",
              "schema": "integer"
            }
          ]
        }
      }
    }
  ]
}
```

El dispositivo IoT registra un método directo  `tunnelOpen`, donde el cuerpo de la solicitud tendrá los parámetros `remoteEndpoint`, `remoteAuthorizationToken` y `localPort`, como se muestra anteriormente.

## <a name="implement-the-direct-method-tunnelopen"></a>Implementación del método directo `tunnelOpen`
Cuando el servicio Video Analyzer invoca el método directo `tunnelOpen`, la aplicación debe hacer lo siguiente:

1. Obtener los puertos RTSP disponibles del dispositivo.
1. Comparar el valor `localPort` especificado en la llamada al método directo con los puertos disponibles.
   * Devolver **BadRequest** si no se encuentra ninguna coincidencia (consulte la sección Respuestas de errores a continuación)
1. Abrir una conexión TCP a ("IP o nombre de host de la cámara):`localPort`"
   * Devolver **BadRequest** si hay un error de conexión.
   * NOTA: El nombre de host suele ser **localhost**.
1. Abra una conexión de WebSocket a `remoteEndpoint` (a través de un proxy si está configurado en el dispositivo).
   * Establezca el encabezado HTTP "Authorization" como "Bearer (remoteAuthorizationToken)".
   * Establezca el encabezado "TunnelConnectionSource" con el valor "PnpDevice".
   * Establezca User-Agent en un valor adecuado que le ayude a identificar la implementación. 
      * Por ejemplo, es posible que desee capturar la arquitectura de la CPU, el sistema operativo, el modelo o la marca del dispositivo.
   * Devolver 200 Correcto si la conexión de WebSocket se ha establecido correctamente; de lo contrario, devolver el código de error correspondiente.
1. Devolver la respuesta (no bloquear).
1. La implementación del dispositivo IoT PnP comienza a enviar datos de TCP bidireccionalmente entre el WebSocket y la conexión TCP del servidor RTSP.

El servicio Video Analyzer reintentará las solicitudes `tunnelOpen` en caso de error, por lo que no se necesitan reintentos en la aplicación.

### <a name="error-responses"></a>Respuestas de errores
Si se produce un error en la solicitud `tunnelOpen`, el cuerpo de la respuesta debe ser el siguiente:

```
{
    "code": "<errorCode>", // Use HTTP status error codes
    "target": "<uri>", // The target URI experiencing the issue
    "message": "<Error message>",  // Short error message describing issue. Do not include end user identifiable information.
}
```
Algunos ejemplos de estas respuestas de errores son:

* El puerto local no está disponible como puerto RTSP o RTSPS { "code": "400", "target": "(IP o nombre de host de la cámara):{localPort}", "message": "El puerto local no está disponible"}
* Se agotó el tiempo de espera o no se pudo conectar con el punto de conexión RTSP { "code": "400", "target": "(IP o nombre de host de la cámara):{localPort}", "message":"No se pudo conectar con el punto de conexión RTSP"}
*   Se agotó el tiempo de espera o respuesta con error del intento de conexión con WebSocket { "code": "{código de respuesta de WebSocket}", "target": "{remoteEndpoint}", "message": "{Mensaje de error de respuesta de WebSocket}"}


## <a name="ingestion-to-video-analyzer"></a>Ingesta en Video Analyzer
Para capturar y grabar vídeo en Video Analyzer, se debe crear una topología de canalización con la tunelización habilitada. A partir de esa topología, se debe crear y activar una canalización activa. [Las instrucciones para este proceso se describen aquí.](use-remote-device-adapter.md#create-pipeline-topology-in-the-video-analyzer-service)

 
## <a name="example-implementation"></a>Implementación de ejemplo
Póngase en contacto con videoanalyzerhelp@microsoft.com si desea implementar una aplicación en el dispositivo para conectarlo a Video Analyzer.

## <a name="see-also"></a>Vea también 

[¿Qué es IoT Plug and Play?](../../../iot-develop/overview-iot-plug-and-play.md)

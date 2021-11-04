---
title: 'Supervisión y registro: servicio Azure Video Analyzer'
description: En este artículo se proporciona información general sobre la supervisión y el registro de servicio de Azure Video Analyzer.
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 370904873a3f86ebee1cd530f349c55306588d08
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131564012"
---
# <a name="monitor-and-log"></a>Supervisión y registro

[!INCLUDE [header](includes/cloud-env.md)]

En este artículo, aprenderá sobre los eventos y registros generados por el servicio Azure Video Analyzer. También aprenderá a consumir los registros que genera el servicio y a supervisar los eventos del servicio.

## <a name="taxonomy-of-events"></a>Taxonomía de eventos

El diagrama siguiente representa la taxonomía común que se usa para los eventos o datos de telemetría emitidos por el servicio Video Analyzer:

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-taxonomy-cloud.png" alt-text="Diagrama que muestra la taxonomía de eventos.":::

## <a name="event-schema"></a>Esquema del evento

 Los eventos generados por el servicio Video Analyzer constan de propiedades del sistema, propiedades de la aplicación y un cuerpo.

### <a name="common-properties"></a>Propiedades comunes

Cada evento tiene un conjunto de propiedades comunes:

| Propiedad      | Tipo de propiedad       | Tipo de datos | Descripción                                                  |
| ------------- | ------------------- | --------- | ------------------------------------------------------------ |
| `trace-id`    | sistema              | guid      | Identificador de evento único                                             |
| `resourceId`  | applicationProperty | string    | Ruta de acceso de ARM para la cuenta de Azure Video Analyzer.               |
| `subject`     | applicationProperty | string    | Subruta de la entidad que emite el evento.                    |
| `time`        | applicationProperty | string    | Hora a la que se generó el evento                                |
| `category`    | sistema              | string    | Audit, Operational, Diagnostics.                              |
| `operationName`| applicationProperty| string    | Identificador de tipo de evento (consulte la sección siguiente).          |
| `level`        | sistema             | string    | Nivel de evento (informativo, advertencia, error, crítico).           |
| `body`        | body                | object    | Datos de evento concretos                                       |
| `operationVersion` | sistema         | string    | Versión de datos de eventos {Major}. {Minor}                           |

## <a name="event-types"></a>Tipos de eventos
El servicio Video Analyzer emite los siguientes tipos de datos de eventos:

**Operativo:** eventos generados por las acciones de un usuario o durante la ejecución de una [canalización](../pipeline.md).
* Volumen: Se espera que sea bajo (algunas veces un minuto o incluso menos). Ejemplos:

| Evento        | Nivel               | Descripción breve                                              |
| ------------- | ------------------- |  ------------------------------------------------------------ |
|RecordingStarted   |Informativo| Grabación iniciada|
|RecordingAvailable |Informativo| Grabación disponible|
|RecordingStopped   |Informativo| Grabación detenida|
|PipelineStateChanged   |Informativo| Estado de la canalización cambiado|

   *Evento operativo de ejemplo*
      
```json
{
  "time": "2021-10-06T21:19:36.0988630Z",
  "resourceId": "/SUBSCRIPTIONS/{SUBID}/RESOURCEGROUPS/{RGNAME}/PROVIDERS/MICROSOFT.MEDIA/VIDEOANALYZERS/{NAME}",
  "region": "westcentralus",
  "category": "Operational",
  "operationName": "Microsoft.VideoAnalyzer.Operational.RecordingStarted",
  "operationVersion": "1.0",
  "level": "Informational",
  "correlationId": "c7887efd-0043-4ada-aa3d-9a411e612497",
  "traceContext": "{\n  \"traceId\": \"e74a9d4c-c4b9-4024-9acf-06be76d978ad\"\n}",
  "properties": {
    "subject": "/livePipelines/livepipeline/sinks/videoSink",
    "body": {
      "type": "video",
      "location": "/videos/livetest1",
      "startTime": "2021-10-06T21:19:32.520Z"
    }
  }
}
```

**Diagnóstico:** eventos que ayudan a diagnosticar problemas y/o rendimiento.
   * Volumen: puede ser alto (varias veces al minuto), también podría afectar al costo de almacenamiento. Se recomienda habilitar solo estos eventos cuando se necesita el diagnóstico o para solucionar problemas. Ejemplos:
    
| Evento             | Nivel           | Descripción breve                                            |
| ----------------- | ----------------| ------------------------------------------------------------ |
|AuthenticationError|Error|Error de autenticación de cliente/servidor|
|AuthorizationError |   Error|  Error de autorización de servidor/cliente|
|FormatError        |   Error|  Incidencias de empaquetado, formato o codificación|
|MediaSessionEstablished|   Informativo|  SDP u otra información de sesión|
|NetworkError        |  Error|  DNS, error de red|
|ProtocolError        | Error|  RTSP o cualquier otro error de protocolo|
|StorageError        |  Error|  Error de almacenamiento/error de escritura|
|RtspPlaybackSessionEstablished|    Informativo| Se establece la sesión de reproducción de RTSP|
|RtspPlaybackSessionClosed| Informativo|  La sesión de reproducción de RTSP está cerrada|

*Evento de diagnóstico de ejemplo*
  
```json
{
  "time": "2021-10-06T21:19:34.1290000Z",
  "resourceId": "/SUBSCRIPTIONS/{SUBID}/RESOURCEGROUPS/{RGNAME}/PROVIDERS/MICROSOFT.MEDIA/VIDEOANALYZERS/{NAME}",
  "region": "westcentralus",
  "category": "Diagnostics",
  "operationName": "Microsoft.VideoAnalyzer.Diagnostics.MediaSessionEstablished",
  "operationVersion": "1.0",
  "level": "Informational",
  "traceContext": "{\n  \"traceId\": \"b03cabe2-b9d1-4be7-9770-4ac9e4fdc012\"\n}",
  "properties": {
    "subject": "/livePipelines/livepipesample/sources/rtspSource",
    "body": {
        "sdp": "SDP:\nv=0\r\no=- 0 0 IN IP4 127.0.0.1\r\ns=No Name\r\nt=0 0\r\na=tool:libavformat 58.76.100\r\nm=video 0 RTP/AVP 96\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1; sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX; profile-level-id=4D0029\r\na=control:streamid=0\r\n"
    }
  }
}

```
**Auditar:** El evento se usa para registrar el acceso a la API. 
  * Volumen: bajo, recomendado para habilitar solo estos eventos cuando se necesita auditoría.

*Evento de auditoría de muestras*
  
```json
{
  "time": "2021-10-07T23:53:31.6792370Z",
  "resourceId": "/SUBSCRIPTIONS/{SUBID}/RESOURCEGROUPS/{RGNAME}/PROVIDERS/MICROSOFT.MEDIA/VIDEOANALYZERS/{NAME}",
  "region": "westcentralus",
  "category": "Audit",
  "operationName": "Microsoft.VideoAnalyzer.Audit.ResourceGet",
  "level": "Warning",
  "uri": "https://{GUID}.api.{region}.videoanalyzer.azure.net/videos/batchjobsinknode",
  "resultType": "Failed",
  "resultSignature": "403",
  "identity": [
    {
      "alg": "RS256",
      "kid": "{KID}",
      "typ": "JWT"
    },
    {
      "sub": "livetest1",
      "aud": [ "https://{GUID}.streaming.{region}.videoanalyzer.azure.net/{GUID}", "wss://{GUID}.rtsp-tunnel.{region}.videoanalyzer.azure.net/{GUID}" ],
      "exp": 1633410145,
      "iss": "https://{region}.videoanalyzer.azure.net/"
    }
  ],
  "traceContext": "{\n  \"traceId\": \"4bb0dcf5-5c6d-4aa3-8c03-3f3d7e2c6210\"\n}",
  "properties": { "subject": "/videos/batchjobsinknodesample" }
}

```
## <a name="metrics"></a>Métricas

El servicio Video Analyzer genera métricas para las [canalizaciones](../pipeline.md). Puede acceder a las métricas mediante la Azure Portal, para lo que debe ir a la sección Supervisión desde el panel de administración de la cuenta de Video Analyzer.

| Nombre de métrica                           | Tipo    | Dimensión                                                                              | Descripción                                                  |
| ------------------------------------- | ------- | ----------------------------------------------------------------------------------- | ------------------------------------------------------------ |
| Bytes de entrada              | Contador   | Tipo de canalización, estado, nombre de topología                                 | Número total de bytes recibidos por una canalización. Solo se admite para orígenes RTSP.           |
| Canalizaciones                  | Contador   | Tipo de canalización, estado, nombre de topología                        | Proporciona las canalizaciones en distintos estados. Emitido a intervalos regulares. |

## <a name="monitoring-of-events"></a>Supervisión de eventos

Puede guardar los eventos generados por el servicio Video Analyzer en la cuenta de almacenamiento y consumirlos mediante Azure Monitor.

**Colección de eventos de Azure Monitor**

El servicio Video Analyzer admite actualmente la escritura de eventos de telemetría en una cuenta de almacenamiento y el uso de Azure Monitor para consumir esos eventos. Use [Azure Monitor](../../../azure-monitor/overview.md) para consumir eventos generados por el servicio, los clientes tienen una experiencia de supervisión integrada a través de Azure Monitor.

Siga los pasos para habilitar la recopilación de eventos y supervisión con Azure Monitor:
* Vaya a la sección de **Supervisión** de Azure Portal de la cuenta de Video Analyzer y seleccione **Configuración de diagnóstico**.
* Haga clic en **Agregar configuración de diagnóstico** para permitir la recopilación de los siguientes datos:
    *   Operativos
    *   Diagnóstico
    *   Auditoría
* Seleccione la categoría de registros que desea habilitar y el período de retención correspondiente.
* Para obtener detalles de destino, **seleccione Archivar en una cuenta de almacenamiento** y especifique la cuenta de almacenamiento donde se almacenarán estos registros de eventos.
    > [!IMPORTANT]
    > El servicio Video Analyzer actualmente no admite el envío de diagnósticos a destinos distintos de Azure Storage.

* Haga clic en **Guardar** después de configurar la configuración de diagnóstico
* Para acceder a los registros de diagnóstico, vaya al Explorador de almacenamiento y expanda la cuenta de almacenamiento y verá Contenedores de blobs. El contenedor "insights-logs-category" tendrá registros en formato de archivo JSON. 
* Descargue el archivo de registro deseado y el contenido del archivo de registro descargado sería similar al ejemplo siguiente:

```json
{
    "time": "2021-10-06T21:19:36.0988630Z",
    "resourceId": "/SUBSCRIPTIONS/{SUBID}/RESOURCEGROUPS/{RGNAME}/PROVIDERS/MICROSOFT.MEDIA/VIDEOANALYZERS/{NAME}",
    "region": "westcentralus",
    "category": "Operational",
    "operationName": "Microsoft.VideoAnalyzer.Operational.RecordingStarted",
    "operationVersion": "1.0",
    "level": "Informational",
    "correlationId": "c7887efd-0043-4ada-aa3d-9a411e612497",
    "traceContext": "{\n  \"traceId\": \"e74a9d4c-c4b9-4024-9acf-06be76d978ad\"\n}",
    "properties": {
        "subject": "/livePipelines/livepipesample/sinks/videoSink",
        "body": {
            "type": "video",
            "location": "/videos/livetest1",
            "startTime": "2021-10-06T21:19:32.520Z"
        }
    }
}
{
    "time": "2021-10-06T21:19:34.1290000Z",
    "resourceId": "/SUBSCRIPTIONS/{SUBID}/RESOURCEGROUPS/{RGNAME}/PROVIDERS/MICROSOFT.MEDIA/VIDEOANALYZERS/{NAME}",
    "region": "westcentralus",
    "category": "Diagnostics",
    "operationName": "Microsoft.VideoAnalyzer.Diagnostics.MediaSessionEstablished",
    "operationVersion": "1.0",
    "level": "Informational",
    "traceContext": "{\n  \"traceId\": \"b03cabe2-b9d1-4be7-9770-4ac9e4fdc012\"\n}",
    "properties": {
        "subject": "/livePipelines/livepipe/sources/rtspSource",
        "body": {
            "sdp": "SDP:\nv=0\r\no=- 0 0 IN IP4 127.0.0.1\r\ns=No Name\r\nt=0 0\r\na=tool:libavformat 58.76.100\r\nm=video 0 RTP/AVP 96\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1; sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX; profile-level-id=4D0029\r\na=control:streamid=0\r\n" 
        }
    }
```

**El registro de actividad** también se genera automáticamente para las operaciones de canalización y se puede acceder a él navegando a la sección "Registro de actividad" de la cuenta de Video Analyzer en Azure Portal. Puede ver el historial de las llamadas API de ARM realizadas a su cuenta y los detalles pertinentes.

![Muestra de registro de actividad](./media/activity-log.png)


## <a name="next-steps"></a>Pasos siguientes

[Servicio de Azure Video Analyzer para solucionar problemas](./troubleshoot.md)

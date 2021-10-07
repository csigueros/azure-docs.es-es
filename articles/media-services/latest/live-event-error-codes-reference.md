---
title: Códigos de error de eventos en directo de Azure Media Services
description: En este artículo se enumeran los códigos de error de eventos en directo.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: error-reference
ms.date: 03/26/2021
ms.author: inhenkel
ms.openlocfilehash: bd8a980d6f8cf7f71dbbfb5f099a12ca966be9ac
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128661647"
---
# <a name="media-services-live-event-error-codes"></a>Códigos de error de eventos en directo de Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

En las tablas siguientes se enumeran los códigos de error de [Evento en directo](live-event-outputs-concept.md).

## <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

Al suscribirse a los eventos de [Event Grid](../../event-grid/index.yml) para un evento en directo, es posible que vea uno de los siguientes errores del evento [LiveEventConnectionRejected](monitoring/media-services-event-schemas.md\#liveeventconnectionrejected).
> [!div class="mx-tdCol2BreakAll"]
>| Error | Información |
>|--|--|
>|**MPE_RTMP_APPID_AUTH_FAILURE** ||
>|Descripción | Dirección URL de introducción incorrecta. |
>|Solución propuesta| APPID es un token GUID en la dirección URL de ingesta RTMP. Asegúrese de que coincide con la dirección URL de ingesta de la API. |
>|**MPE_INGEST_ENCODER_CONNECTION_DENIED** ||
>| Descripción |La dirección IP del codificador no está presente en la lista de direcciones IP permitidas configurada. |
>| Solución propuesta| Asegúrese de que la dirección IP del codificador se encuentra en la lista de direcciones IP permitidas. Use una herramienta en línea como *whoismyip* o la *calculadora CIDR* para establecer el valor adecuado.  Asegúrese de que el codificador puede acceder al servidor antes del evento en directo real. |
>|**MPE_INGEST_RTMP_SETDATAFRAME_NOT_RECEIVED** ||
>| Descripción|El codificador RTMP no envió el comando `setDataFrame`. |
>| Solución propuesta|La mayoría de los codificadores comerciales envían metadatos del flujo. Para un codificador que envía una ingesta de velocidad de bits única, es posible que esto no sea un problema. El evento LiveEvent puede calcular la velocidad de bits entrante cuando faltan los metadatos del flujo.  En el caso de la ingesta de velocidad de bits múltiple para un canal PassThru o un escenario de inserciones dobles, puede intentar anexar la cadena de consulta con "videodatarate" y "audiodatarate" en la dirección URL de ingesta. Puede que el valor aproximado funcione. La unidad está en kilobits. Por ejemplo: `rtmp://hostname:1935/live/GUID_APPID/streamname?videodatarate=5000&audiodatarate=192` |
>|**MPE_INGEST_CODEC_NOT_SUPPORTED** ||
>| Descripción|El códec especificado no se admite.|
>| Solución propuesta| El evento LiveEvent recibió códec no compatible. Por ejemplo, en una ingesta RTMP, el evento LiveEvent recibió códec de vídeo no AVC.  Compruebe el valor preestablecido del codificador. |
>|**MPE_INGEST_DESCRIPTION_INFO_NOT_RECEIVED** ||
>| Descripción |No se recibió la información de descripción de los elementos multimedia antes de que se entregaran los datos multimedia reales. |
>| Solución propuesta|El evento LiveEvent no recibe la descripción del flujo (encabezado o etiqueta FLV) del codificador. Se trata de una infracción del protocolo. Póngase en contacto con el proveedor del codificador. |
>|**MPE_INGEST_MEDIA_QUALITIES_EXCEEDED** ||
>| Descripción| El recuento de niveles de calidad para el tipo de audio o vídeo superó el límite máximo permitido. El servicio omitió los niveles de calidad más allá del límite.|
>| Solución propuesta|Si el modo Evento en directo es Live Encoding, el codificador debe insertar una sola velocidad de bits de vídeo y audio.  Tenga en cuenta que se permite una inserción redundante de la misma velocidad de bits. Compruebe la configuración de salida o el valor preestablecido del codificador para asegurarse de que genera un flujo de velocidad de bits único. |
>|**MPE_INGEST_BITRATE_AGGREGATED_EXCEEDED** ||
>| Descripción|La velocidad de bits entrante total en un servicio de canal o evento en directo superó el límite máximo permitido. |
>| Solución propuesta|El codificador superó la velocidad de bits de entrada máxima. Este límite agrega todos los datos entrantes del codificador colaborador. Compruebe la configuración de salida o el valor preestablecido del codificador para reducir la velocidad de bits. |
>|**MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID** ||
>| Descripción|La marca de tiempo de la etiqueta FLVTag de audio o vídeo del codificador de RTMP no es válida. |
>| Solución propuesta|En desuso. |
>|**MPE_INGEST_FRAMERATE_EXCEEDED** ||
>| Descripción|Los flujos ingeridos del codificador entrante con velocidades de fotogramas superaron el máximo permitido de 30 fps para codificar canales o eventos en directo. |
>| Solución propuesta|Compruebe el valor preestablecido del codificador para reducir la velocidad de fotogramas a menos de 36 fps. |
>|**MPE_INGEST_VIDEO_RESOLUTION_NOT_SUPPORTED** ||
>| Descripción|Las transmisiones recibidas del codificador entrante superaron las siguientes resoluciones permitidas: 1920 x 1088 para la codificación de canales o eventos en directo y 4096 x 2160 para canales o eventos en directo de paso a través. |
>| Solución propuesta|Compruebe el valor preestablecido del codificador para reducir la resolución de vídeo de modo que no supere el límite. |
>|**MPE_INGEST_RTMP_TOO_LARGE_UNPROCESSED_FLV** |
>| Descripción|El evento en directo ha recibido una gran cantidad de datos de audio a la vez, o una gran cantidad de datos de vídeo sin ningún fotograma clave. Hemos desconectado el codificador para darle la oportunidad de volver a intentarlo con los datos correctos. |
>| Solución propuesta|Asegúrese de que el codificador envía un fotograma clave para cada intervalo de fotogramas clave (GOP).  Habilite opciones de configuración como "Constant bitrate(CBR)" [Velocidad de bits constante (CBR)] o "Align Key Frames" (Alinear fotogramas clave). A veces, puede ser útil restablecer el codificador colaborador. Si no es de ayuda, póngase en contacto con el proveedor del codificador. |

## <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

Es posible que vea uno de los siguientes errores del evento [LiveEventEncoderDisconnected](monitoring/media-services-event-schemas.md\#liveeventencoderdisconnected).

> [!div class="mx-tdCol2BreakAll"]
>| Error | Información |
>|--|--|
>|**MPE_RTMP_SESSION_IDLE_TIMEOUT** |
>| Descripción|La sesión de RTMP agotó el tiempo de espera después de estar inactiva durante el límite de tiempo permitido. |
>|Solución propuesta|Por lo general, esto sucede cuando un codificador deja de recibir la fuente de entrada y la sesión se inactiva porque no existen datos para extraer. Compruebe si el estado del codificador o la fuente de entrada es correcto. |
>|**MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID** |
>|Descripción| La marca de tiempo de la etiqueta FLVTag de audio o vídeo del codificador de RTMP no es válida. |
>| Solución propuesta| En desuso. |
>|**MPE_CAPACITY_LIMIT_REACHED** |
>| Descripción|El codificador envía datos demasiado rápido. |
>| Solución propuesta|Esto sucede cuando el codificador dispara el tamaño de un conjunto grande de fragmentos en un breve período.  En teoría, esto puede ocurrir cuando el codificador no puede insertar datos durante un tiempo debido a un problema de red y luego dispara los datos cuando la red está disponible. Busque el motivo en el registro del codificador o el registro del sistema. |
>|**Códigos de error desconocidos** |
>| Descripción| Estos códigos de error pueden variar desde un error de memoria hasta entradas duplicadas en el mapa de hash. Esto puede suceder cuando el codificador envía el tamaño de un conjunto grande de fragmentos en un período breve.  Esto también puede ocurrir cuando el codificador no puede insertar datos durante un tiempo debido a un problema de red y, a continuación, envía todos los fragmentos retrasados a la vez cuando la red está disponible. |
>|Solución propuesta| Compruebe los registros del codificador.|

## <a name="other-error-codes"></a>Otros códigos de error

> [!div class="mx-tdCol2BreakAll"]
>| Error | Información |Evento rechazado/desconectado|
>|--|--|--|
>|**ERROR_END_OF_MEDIA** ||Sí|
>| Descripción|Se trata de un error general. ||
>|Solución propuesta| Ninguno.||
>|**MPI_SYSTEM_MAINTENANCE** ||Sí|
>| Descripción|El codificador se desconectó debido a la actualización del servicio o al mantenimiento del sistema. ||
>|Solución propuesta|Asegúrese de que el codificador habilita "Conexión automática". Permite al codificador volver a conectarse al punto de conexión de evento en directo redundante que no está en mantenimiento. ||
>|**MPE_BAD_URL_SYNTAX** ||Sí|
>| Descripción|La dirección URL de ingesta tiene un formato incorrecto. ||
>|Solución propuesta|Asegúrese de que la dirección URL de ingesta tiene el formato correcto. Para RTMP, debe ser `rtmp[s]://hostname:port/live/GUID_APPID/streamname`. ||
>|**MPE_CLIENT_TERMINATED_SESSION** ||Sí|
>| Descripción|El codificador desconectó la sesión.  ||
>|Solución propuesta|Esto no es un error. El codificador inició la desconexión, incluida la desconexión estable. Si se trata de una desconexión inesperada, compruebe los registros del codificador. |
>|**MPE_INGEST_BITRATE_NOT_MATCH** ||No|
>| Descripción|La velocidad de datos de entrada no coincide con la velocidad de bits esperada. ||
>|Solución propuesta|Se trata de una advertencia que se produce cuando la velocidad de datos de entrada es demasiado lenta o demasiado rápida. Compruebe el registro del codificador o el registro del sistema.||
>|**MPE_INGEST_DISCONTINUITY** ||No|
>| Descripción| Existe discontinuidad en los datos de entrada.||
>|Solución propuesta| Se trata de una advertencia que indica que el codificador quita los datos debido a un problema de red o a un problema de recursos del sistema. Compruebe el registro del codificador o el registro del sistema. Supervise también el recurso del sistema (CPU, memoria o red). Si el consumo de la CPU del sistema es demasiado elevado, intente reducir la velocidad de bits o use la opción del codificador H/W de la tarjeta gráfica del sistema.||

## <a name="see-also"></a>Consulte también

[Códigos de error de puntos de conexión de streaming (origen)](stream-streaming-endpoint-error-codes-reference.md)

## <a name="next-steps"></a>Pasos siguientes

[Tutorial: Streaming en vivo con Media Services](stream-live-tutorial-with-api.md)

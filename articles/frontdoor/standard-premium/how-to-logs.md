---
title: Registro de Azure Front Door Estándar y Premium (versión preliminar)
description: En este artículo se explica cómo funciona el registro en Azure Front Door Estándar y Premium.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: article
ms.date: 08/26/2021
ms.author: duau
ms.openlocfilehash: 5446c240d1d07b5b99fe6f91a031617756a6018c
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/26/2021
ms.locfileid: "122967597"
---
# <a name="azure-front-door-standardpremium-preview-logging"></a>Registro de Azure Front Door Estándar y Premium (versión preliminar)

> [!Note]
> Esta documentación trata sobre Azure Front Door Estándar/Prémium (versión preliminar). ¿Busca información sobre Azure Front Door? Consulte [aquí](../front-door-overview.md).

Azure Front Door proporciona diferentes registros para ayudarle a realizar el seguimiento, la supervisión y la depuración de la instancia de Front Door. 

* Los registros de acceso tienen información detallada sobre cada solicitud que recibe AFD y ayudan a analizar y supervisar los patrones de acceso y a depurar los problemas. 
* Los registros de actividad proporcionan visibilidad sobre las operaciones llevadas a cabo en los recursos de Azure.  
* Los registros de sondeo de estado proporcionan los registros de cada sondeo con error al origen. 
* Los registros del firewall de aplicaciones web (WAF) proporcionan información detallada sobre las solicitudes que se registran con el modo de detección o prevención de un punto de conexión de Azure Front Door. Un dominio personalizado que se configura con WAF también se puede ver mediante estos registros.

> [!IMPORTANT]
> Azure Front Door Estándar/Prémium (versión preliminar) está disponible actualmente en versión preliminar pública.
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.
> Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Los registros de acceso, los registros de sondeo de estado y los registros del WAF no están habilitados de manera predeterminada. Siga los pasos que se indican a continuación para habilitar el registro. Las entradas del registro de actividades se recopilan de forma predeterminada y se pueden ver en Azure Portal. Los registros pueden tardar unos minutos en realizarse. 

Tiene tres opciones para almacenar los archivos de registro: 

* **Cuenta de almacenamiento**: las cuentas de almacenamiento resultan especialmente útiles para escenarios en los que los registros se almacenan durante mucho tiempo y se revisan cuando es necesario. 
* **Centros de eventos**: los centros de eventos son una buena opción para la integración con otras herramientas de administración de eventos e información de seguridad (SIEM) o con almacenes de datos externos. Por ejemplo: Splunk, DataDog o Sumo. 
* **Azure Log Analytics:** Azure Log Analytics en Azure Monitor es más útil para la supervisión general en tiempo real y el análisis de rendimiento de Azure Front Door.

## <a name="configure-logs"></a>Configuración de los registros

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. Busque Azure Front Door Estándar o Premium y seleccione el perfil de Azure Front Door.

1. En el perfil, vaya a **Supervisión** y seleccione **Configuración de diagnóstico**. Seleccione **Agregar configuración de diagnóstico**.

   :::image type="content" source="../media/how-to-logging/front-door-logging-1.png" alt-text="Captura de pantalla de la página de aterrizaje de la configuración de diagnóstico.":::

1. En **Configuración de diagnóstico**, escriba un nombre para  **Nombre de la configuración de diagnóstico**.

1. Seleccione el  **registro** entre **FrontDoorAccessLog**, **FrontDoorHealthProbeLog** y **FrontDoorWebApplicationFirewallLog**.

1. Seleccione  **Detalles del destino**. Las opciones de destino son: 

    * **Enviar a Log Analytics**
        * Seleccione la *Suscripción* y el *Área de trabajo de Log Analytics*.
    * **Archivar en una cuenta de almacenamiento**
        * Seleccione la *Suscripción* y la *Cuenta de almacenamiento*. y establezca **Retención (días)** .
    * **Transmisión a un centro de eventos**
        * Seleccione la  *suscripción, el espacio de nombres del centro de eventos, el nombre del centro de eventos (opcional)* y el  *nombre de la directiva del centro de eventos*. 

     :::image type="content" source="../media/how-to-logging/front-door-logging-2.png" alt-text="Captura de pantalla de página de configuración de diagnóstico.":::

1. Haga clic en **Guardar**.

## <a name="access-log"></a>Registro de acceso

Azure Front Door actualmente proporciona solicitudes de API individuales con cada entrada, las cuales tiene el siguiente esquema y se registran en formato JSON, tal como se muestra a continuación. 

| Propiedad | Descripción |
|----------|-------------| 
| TrackingReference | Cadena de referencia única que identifica una solicitud atendida por AFD, que también se envía como encabezado X-Azure-Ref al cliente. Se requiere para buscar los detalles en los registros de acceso para una solicitud específica. |
| Time | Fecha y hora en la que el perímetro de AFD entregó el contenido solicitado al cliente (en formato UTC). |
| HttpMethod | Método HTTP usado por la solicitud: DELETE, GET, HEAD, OPTIONS, PATCH, POST o PUT. |
| HttpVersion | Versión de HTTP que el visor especificó en la solicitud. |
| RequestUri | URI de la solicitud recibida. Este campo es un esquema completo: puerto, dominio, ruta de acceso y cadena de consulta. |
| HostName | Nombre de host de la solicitud del cliente. Si habilita dominios personalizados y tiene un dominio con caracteres comodín (*.contoso.com), el nombre de host será a.contoso.com. Si usa el dominio de Azure Front Door (contoso.azurefd.net), el nombre de host será contoso.azurefd.net. |
| RequestBytes | El tamaño del mensaje de solicitud HTTP en bytes, incluidos los encabezados de solicitud y el cuerpo de solicitud. Número de bytes de datos que el visor incluyó en la solicitud, contando con los encabezados. |
| ResponseBytes | Bytes enviados por el servidor back-end como respuesta. |
| UserAgent | Tipo de explorador utilizado por el cliente. |
| ClientIp | Dirección IP del cliente que realizó la solicitud original. Si hubiera un encabezado X-Forwarded-For en la solicitud, la dirección IP del cliente se seleccionaría del mismo. |
| SocketIp | Dirección IP de la conexión directa con el perímetro de AFD. Si el cliente usaba un proxy HTTP o un equilibrador de carga para enviar la solicitud, el valor de SocketIp es la dirección IP del proxy o del equilibrador de carga. |
| timeTaken | El período de tiempo desde que el servidor perimetral de AFD recibe la solicitud de un cliente hasta el momento en que AFD envía el último byte de la respuesta al cliente, en milisegundos. Este campo no tiene en cuenta la latencia de red y el almacenamiento en búfer de TCP. |
| RequestProtocol | Protocolo especificado por el cliente en la solicitud: HTTP, HTTPS. |
| SecurityProtocol | La versión del protocolo TLS/SSL utilizada por la solicitud o null si no hay cifrado. Entre los valores posibles se incluyen: SSLv3, TLSv1, TLSv1.1 y TLSv1.2 |
| SecurityCipher | Cuando el valor del protocolo de la solicitud es HTTPS, este campo indica el cifrado TLS/SSL negociado por el cliente y AFD para el cifrado. |
| Punto de conexión | Nombre de dominio del punto de conexión de AFD; por ejemplo, contoso.z01.azurefd.net |
| HttpStatusCode | Código de estado HTTP devuelto desde AFD. |
| PoP | El servidor PoP perimetral, que respondió a la solicitud del usuario.  |
| Estado de la caché | Proporciona el código de estado de cómo el servicio de la red CDN controla la solicitud cuando se almacena en caché. Los valores posibles son HIT: la solicitud HTTP se sirvió desde la memoria caché POP del perímetro de AFD. <br> **MISS**: la solicitud HTTP se sirvió desde el origen. <br/> **PARTIAL_HIT**: algunos de los bytes de una solicitud se obtuvieron desde la memoria caché POP del perímetro de AFD mientras que algunos de los bytes se sirvieron desde el origen para escenarios de fragmentación de objetos. <br> **CACHE_NOCONFIG**: reenvío de solicitudes sin configuración de almacenamiento en caché, incluido el escenario de omisión. <br/> **PRIVATE_NOSTORE**: no hay ninguna memoria caché configurada en la configuración de almacenamiento en caché por los clientes. <br> **REMOTE_HIT**: la memoria caché del nodo principal sirvió a la solicitud. <br/> **N/A**:** una solicitud que se denegó por la dirección URL firmada y el conjunto de reglas. |
| MatchedRulesSetName | Nombres de las reglas que se procesaron. |
| RouteName | Nombre de la ruta que coincidió con la solicitud. |
| ClientPort | Puerto IP del cliente que realizó la solicitud. |
| Referrer | Dirección URL del sitio que originó la solicitud. |
| TimetoFirstByte | Duración en milisegundos desde que AFD recibe la solicitud hasta el momento en que se envía el primer byte al cliente, según la medición de Azure Front Door. Esta propiedad no mide los datos del cliente. |
| ErrorInfo | Este campo proporciona información detallada del token de error de cada respuesta. <br> **NoError**: indica que no se encontró ningún error. <br> **CertificateError**: error genérico de certificado SSL. <br> **CertificateNameCheckFailed**: el nombre de host del certificado SSL no es válido o no coincide. <br> **ClientDisconnected**: error de solicitud debido a la conexión de red del cliente. <br> **ClientGeoBlocked**: el cliente se bloqueó debido a la ubicación geográfica de la dirección IP. <br> **UnspecifiedClientError**: error genérico de cliente. <br> **InvalidRequest**: solicitud no válida. Podría producirse debido a un encabezado, un cuerpo y una dirección URL con formato incorrecto. <br> **DNSFailure**: error de DNS. <br> **DNSTimeout**: se agotó el tiempo de espera de la consulta DNS para resolver el back-end. <br> **DNSNameNotResolved**: no se pudo resolver el nombre o la dirección del servidor. <br> **OriginConnectionAborted**: la conexión con el origen se desconectó de forma anómala. <br> **OriginConnectionError**: error genérico de conexión con el origen. <br> **OriginConnectionRefused**: no se pudo establecer la conexión con el origen. <br> **OriginError**: error genérico del origen. <br> **OriginInvalidRequest**: se envió una solicitud no válida al origen. <br> **ResponseHeaderTooBig**: el origen devolvió un encabezado de respuesta demasiado grande. <br> **OriginInvalidResponse**:** el origen devolvió una respuesta no válida o desconocida. <br> **OriginTimeout**: el período de tiempo de espera de la solicitud del origen expiró. <br> **ResponseHeaderTooBig**: el origen devolvió un encabezado de respuesta demasiado grande. <br> **RestrictedIP**: la solicitud se bloqueó debido a una IP restringida. <br> **SSLHandshakeError**: no se puede establecer la conexión con el origen debido a un error del protocolo de enlace SSL. <br> **SSLInvalidRootCA**: RootCA no es válido. <br> **SSLInvalidCipher**: el cifrado para el que se estableció la conexión HTTPS no era válido. <br> **OriginConnectionAborted**: la conexión con el origen se desconectó de forma anómala. <br> **OriginConnectionRefused**: no se pudo establecer la conexión con el origen. <br> **UnspecifiedError**: se produjo un error que no coincidía con ninguno de los errores de la tabla. |
| OriginURL | Dirección URL completa del origen desde el que se envían las solicitudes. Se compone de esquema, encabezado de host, puerto, ruta de acceso y cadena de consulta. <br> **Reescritura de dirección URL**: si hay una regla de reescritura de dirección URL en el conjunto de reglas, la ruta de acceso hace referencia a la ruta de acceso reescrita. <br> **Memoria caché en el POP perimetral** Si se trata de un acierto de caché en el POP perimetral, el origen es N/A. <br> **Solicitud grande** Si el contenido solicitado es grande, con varias solicitudes fragmentadas que vuelven al origen, este campo se corresponderá con la primera solicitud al origen. Para más información, consulte Fragmentación de objetos. |
| OriginIP | Dirección IP de origen que sirvió la solicitud. <br> **Acierto de caché en el POP perimetral** Si se trata de un acierto de caché en el POP perimetral, el origen es N/A. <br> **Solicitud grande** Si el contenido solicitado es grande, con varias solicitudes fragmentadas que vuelven al origen, este campo se corresponderá con la primera solicitud al origen. Para más información, consulte Fragmentación de objetos. |
| OriginName| Nombre DNS completo (nombre de host de la dirección URL de origen) al origen. <br> **Acierto de caché en el POP perimetral** Si se trata de un acierto de caché en el POP perimetral, el origen es N/A. <br> **Solicitud grande** Si el contenido solicitado es grande, con varias solicitudes fragmentadas que vuelven al origen, este campo se corresponderá con la primera solicitud al origen. Para más información, consulte Fragmentación de objetos. |

## <a name="health-probe-log"></a>Registro de sondeo de estado

Los registros de sondeo de estado proporcionan el registro de cada sondeo con errores para ayudarle a diagnosticar el origen. Los registros le proporcionarán información que puede usar para devolver el origen al servicio. Algunos escenarios en los que puede resultar útil este registro son:

* Ha observado que el tráfico de Azure Front Door se envió a algunos de los orígenes. Por ejemplo, solo tres de los cuatro orígenes reciben tráfico. Desea saber si los orígenes reciben sondeos y, de no ser así, el motivo del error.  

* Ha observado que el porcentaje de estado correcto del origen es menor de lo esperado y desea saber qué origen produjo el error y el motivo.

### <a name="health-probe-log-properties"></a>Propiedades del registro de sondeo de estado

Cada registro de sondeo de estado tiene el siguiente esquema.

| Propiedad | Descripción |
| --- | --- |
| HealthProbeId  | Identificador único para identificar la solicitud. |
| Time | Hora de finalización del sondeo |
| HttpMethod | Método HTTP utilizado por la solicitud del sondeo de estado. Los valores incluyen GET y HEAD, según las configuraciones del sondeo de estado. |
| Resultado | Estado del sondeo de estado del origen, el valor puede ser correcto u otro texto de error. |
| HttpStatusCode  | Código de estado HTTP devuelto desde el origen. |
| ProbeURL (destino) | Dirección URL completa del origen desde el que se envían las solicitudes. Se compone de esquema, encabezado de host, ruta de acceso y cadena de consulta. |
| OriginName  | Origen desde el que se envían las solicitudes. Este campo ayuda a localizar los orígenes de interés si el origen está configurado como FQDN. |
| POP | POP perimetral que envió la solicitud del sondeo. |
| IP de origen | Dirección IP de origen del destino. Este campo es útil para buscar los orígenes de interés si configura el origen como FQDN. |
| TotalLatency | Hora desde que el perímetro de AFDX envía la solicitud al origen hasta la hora en la que el origen envía la última respuesta al perímetro de AFDX. |
| ConnectionLatency| Tiempo invertido en la configuración de la conexión TCP para enviar la solicitud de sondeo HTTP al origen. | 
| DNSResolution Latency | Tiempo empleado en la resolución DNS si el origen está configurado como un FQDN en lugar de una dirección IP. N/A si el origen está configurado como dirección IP. |

### <a name="health-probe-log-sample-in-json"></a>Ejemplo de registro de sondeo de estado en formato JSON

`{ "records": [ { "time": "2021-02-02T07:15:37.3640748Z",
      "resourceId": "/SUBSCRIPTIONS/27CAFCA8-B9A4-4264-B399-45D0C9CCA1AB/RESOURCEGROUPS/AFDXPRIVATEPREVIEW/PROVIDERS/MICROSOFT.CDN/PROFILES/AFDXPRIVATEPREVIEW-JESSIE",
      "category": "FrontDoorHealthProbeLog",
      "operationName": "Microsoft.Cdn/Profiles/FrontDoorHealthProbeLog/Write",
      "properties": { "healthProbeId": "9642AEA07BA64675A0A7AD214ACF746E",
        "POP": "MAA",
        "httpVerb": "HEAD",
        "result": "OriginError",
        "httpStatusCode": "400",
        "probeURL": "http://afdxprivatepreview.blob.core.windows.net:80/",
        "originName": "afdxprivatepreview.blob.core.windows.net",
        "originIP": "52.239.224.228:80",
        "totalLatencyMilliseconds": "141",
        "connectionLatencyMilliseconds": "68",
        "DNSLatencyMicroseconds": "1814" } } ]
} `

## <a name="activity-logs"></a>Registros de actividad

Los registros de actividad proporcionan información sobre las operaciones realizadas en Azure Front Door Estándar y Premium. Los registros incluyen detalles sobre qué, quién y cuándo se realizó una operación de escritura en Azure Front Door. 

> [!NOTE]
> Los registros de actividad no incluyen operaciones GET. Tampoco incluyen operaciones realizadas con Azure Portal o la Management API original. 

Acceda a registros de actividad en Front Door o a los registros de todos los recursos de Azure en Azure Monitor.

Para ver los registros de actividad:

1. Seleccione el perfil de Front Door.

1. Seleccione **Registro de actividad**.

1. Elija un ámbito de filtrado y, a continuación, seleccione **Aplicar**.

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre [Informes de Azure Front Door Estándar y Premium (versión preliminar)](how-to-reports.md).
- Para más información, consulte [Supervisión en tiempo real en Azure Front Door Estándar y Premium](how-to-monitor-metrics.md).

---
title: Admisión de protocolos para encabezados HTTP en Azure Front Door | Microsoft Docs
description: En este artículo se describen los protocolos de encabezado HTTP que admite Front Door.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/10/2021
ms.author: duau
ms.openlocfilehash: 807138187e37deef6f23121ce085e62f520ad335
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121736667"
---
# <a name="protocol-support-for-http-headers-in-azure-front-door"></a>Admisión de protocolos para encabezados HTTP en Azure Front Door
Este artículo describe el protocolo que admite el servicio Front Door con partes de la ruta de acceso de llamada (consulte la imagen). Las secciones siguientes proporcionan más información acerca de los encabezados HTTP admitidos por el servicio Front Door.

:::image type="content" source="./media/front-door-http-headers-protocol/front-door-protocol-summary.png" alt-text="Protocolo de encabezados HTTP de Azure Front Door":::

>[!IMPORTANT]
>Front Door no certifica ningún encabezado HTTP que no se documente aquí.

## <a name="client-to-front-door"></a>Cliente para Front Door
Front Door acepta la mayoría de los encabezados de la solicitud entrante sin tener que modificarlos. Algunos encabezados reservados se quitan de la solicitud entrante si se envían, incluidos los encabezados con el prefijo X-FD-*.

## <a name="front-door-to-backend"></a>Front Door al back-end

Front Door incluye encabezados de una solicitud entrante a menos que se quite debido a las restricciones. Front Door también agrega los encabezados siguientes:

| Encabezado  | Ejemplo y descripción |
| ------------- | ------------- |
| Via |  *Via: 1.1 Azure* </br> Front Door agrega la versión del cliente HTTP seguida de *Azure* como valor del encabezado Via. Este encabezado indica la versión HTTP del cliente y que Front Door era un destinatario intermedio de la solicitud entre el cliente y el servidor back-end.  |
| X-Azure-ClientIP | *X-Azure-ClientIP: 127.0.0.1* </br> Representa la dirección IP de cliente asociada con la solicitud que se está procesando. Por ejemplo, una solicitud procedente de un servidor proxy puede agregar el encabezado X-Forwarded-For para indicar la dirección IP del autor de la llamada original. |
| X-Azure-SocketIP |  *X-Azure-SocketIP: 127.0.0.1* </br> Representa la dirección IP del socket asociada con la conexión TCP de la que se originó la solicitud actual. Una dirección IP del cliente de la solicitud podría no ser igual a su dirección IP de socket porque un usuario la puede sobrescribir de manera arbitraria.|
| X-Azure-Ref | *X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Una cadena de referencia única que identifica una solicitud atendida por Front Door. Se usa para buscar los registros de acceso y es esencial para la solución de problemas.|
| X-Azure-RequestChain | *X-Azure-RequestChain: hops=1* </br> Un encabezado que Front Door usa para detectar bucles de solicitudes, y los usuarios no deben generar ninguna dependencia de él. |
| X-Azure-FDID | *X-Azure-FDID: 55ce4ed1-4b06-4bf1-b40e-4638452104da* <br/> Una cadena de referencia que identifica la solicitud venía de un recurso de Front Door específico. El valor puede verse en Azure Portal o recuperarse mediante la API de administración. Puede usar este encabezado en combinación con ACL de IP para bloquear el punto de conexión y aceptar únicamente las solicitudes de un recurso de Front Door específico. Consulte las preguntas más frecuentes para obtener [información más detallada](front-door-faq.yml#how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door-) |
| X-Forwarded-For | *X-Forwarded-For: 127.0.0.1* </br> A menudo, el campo de encabezado X-Forwarded-For (XFF) identifica la dirección IP de origen de un cliente que se conecta a un servidor web a través de un equilibrador de carga o proxy HTTP. Si hay un encabezado XFF existente, Front Door le anexa la dirección IP de socket de cliente o agrega el encabezado XFF con la dirección IP de socket de cliente. |
| X-Forwarded-Host | *X-Forwarded-Host: contoso.azurefd.net* </br> El campo de encabezado HTTP X-Forwarded-Host es un método común utilizado para identificar el host original solicitado por el cliente en el encabezado de solicitud HTTP del host. Esto es porque el nombre de host de Front Door puede diferir del servidor back-end que controla la solicitud. Front Door reemplaza cualquier valor anterior. |
| X-Forwarded-Proto | *X-Forwarded-Proto: http* </br> El campo de encabezado HTTP X-Forwarded-Proto se usa a menudo para identificar el protocolo de origen de una solicitud HTTP. Según la configuración, Front Door puede comunicarse con el back-end mediante el uso de HTTPS. Esto es cierto incluso si la solicitud para el proxy inverso es HTTP. Front Door reemplaza cualquier valor anterior. |
| X-FD-HealthProbe | El campo de encabezado HTTP X-FD-HealthProbe se usa para identificar el sondeo de estado de Front Door. Si este encabezado se establece en 1, la solicitud procede del sondeo de estado. Se puede usar para restringir el acceso desde Front Door con un valor determinado en el campo de encabezado X-Forwarded-Host. |
| X-Azure-FDID | *Encabezado X-Azure-FDID: 437c82cd-360a-4a54-94c3-5ff707647783* </br> Este campo contiene frontdoorID, que se puede usar para identificar la instancia de Front Door de la que procede la solicitud entrante. Este campo lo rellena el servicio de Front Door. | 

## <a name="front-door-to-client"></a>Front Door al cliente

Los encabezados enviados a Front Door desde el back-end se pasan también al cliente. Los siguientes son los encabezados que se envían desde Front Door a los clientes.

| Encabezado  | Ejemplo y descripción |
| ------------- | ------------- |
| X-Azure-Ref |  *X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Se trata de una cadena de referencia única que identifica una solicitud atendida por Front Door, lo que es fundamental para la solución de problemas, ya que se usa para buscar registros de acceso.|
| X-Cache | *X-Cache*: este encabezado describe el estado de almacenamiento en caché de la solicitud. <br/> - *X-Cache: TCP_HIT*: el primer byte de la solicitud es un acierto de caché del perímetro de Front Door. <br/> - *X-Cache: TCP_REMOTE_HIT*: el primer byte de la solicitud es un acierto de caché de la caché regional (capa de escudo de origen), pero un error de la caché perimetral. <br/> - *X-Cache: TCP_MISS*: el primer byte de la solicitud es un error de caché y el contenido se sirve desde el origen. <br/> - *X-Cache: PRIVATE_NOSTORE*: la solicitud no se puede almacenar en caché, ya que el encabezado de respuesta Cache-Control está establecido en privado o sin almacén. <br/> - *X-Cache: CONFIG_NOCACHE*: la solicitud está configurada para no almacenarse en caché en el perfil de Front Door. |

Debe enviar el encabezado de solicitud "X-Azure-DebugInfo: 1" para habilitar los siguientes encabezados de respuesta opcionales.

| Encabezado  | Ejemplo y descripción |
| ------------- | ------------- |
| X-Azure-OriginStatusCode |  *X-Azure-OriginStatusCode: 503* </br> Este encabezado contiene el código de estado HTTP devuelto por el back-end. Mediante este encabezado puede identificar el código de estado HTTP devuelto por la aplicación que se ejecuta en el back-end sin examinar los registros de back-end. Este código de estado puede ser distinto al código de estado HTTP de la respuesta enviada por Front Door al cliente. Este encabezado permite determinar si el comportamiento del back-end no es correcto o si el problema tiene que ver con Front Door Service. |
| X-Azure-InternalError | Este encabezado contendrá el código de error que se encuentra Front Door al procesar la solicitud. Este error indica que se trata de un problema interno de la infraestructura o de Front Door Service. Informe del problema al servicio de soporte técnico.  |
| X-Azure-ExternalError | *X-Azure-ExternalError: 0x830c1011, La entidad de certificación no resulta familiar.* </br> Este encabezado muestra el código de error que se encuentran los servidores de Front Door al establecer la conectividad con el servidor back-end para procesar una solicitud. Este encabezado le ayudará a identificar problemas de conexión entre Front Door y la aplicación back-end. El encabezado incluirá un mensaje de error detallado que le ayudará a identificar los problemas de conectividad con el back-end (por ejemplo, la resolución DNS, un certificado no válido, etc.). |

## <a name="next-steps"></a>Pasos siguientes

- [Creación de una instancia de Front Door](quickstart-create-front-door.md)
- Información acerca de cómo [funciona Front Door](front-door-routing-architecture.md)

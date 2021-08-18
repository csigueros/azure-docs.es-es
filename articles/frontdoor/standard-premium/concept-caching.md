---
title: 'Azure Front Door: almacenamiento en caché'
description: Este artículo ayuda a entender el comportamiento de Azure Front Door Estándar/Prémium con reglas de enrutamiento que han habilitado el almacenamiento en caché.
services: front-door
author: duongau
manager: KumudD
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: 24a925b0d16dc1650398e6211aaff42cd47620eb
ms.sourcegitcommit: 4f185f97599da236cbed0b5daef27ec95a2bb85f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/19/2021
ms.locfileid: "112369418"
---
# <a name="caching-with-azure-front-door-standardpremium-preview"></a>Almacenamiento en caché con Azure Front Door Estándar/Prémium (versión preliminar)

> [!Note]
> Esta documentación trata sobre Azure Front Door Estándar/Prémium (versión preliminar). ¿Busca información sobre Azure Front Door? Consulte [aquí](../front-door-overview.md).

En este artículo, aprenderá cómo se comportan las rutas y el conjunto de reglas de Front Door Estándar/Prémium (versión preliminar) cuando tiene habilitado el almacenamiento en caché. Azure Front Door es una red de entrega de contenido (CDN) moderna con aceleración dinámica de sitios y equilibrio de carga.

> [!IMPORTANT]
> Azure Front Door Estándar/Prémium (versión preliminar) está disponible actualmente en versión preliminar pública.
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.
> Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="request-methods"></a>Métodos de solicitud

Solo el método de solicitud GET puede generar contenido almacenado en caché en Azure Front Door. Los demás métodos de solicitud siempre los procesa el proxy mediante la red.

## <a name="delivery-of-large-files"></a>Suministro de archivos grandes

Front Door Estándar/Prémium (versión preliminar) proporciona archivos grandes sin límite de tamaño. Front Door usa una técnica denominada fragmentación de objetos. Cuando se solicita un archivo grande, Front Door recupera partes más pequeñas del archivo del origen. Después de recibir una solicitud de un archivo completo o de intervalos de bytes, el entorno de Front Door solicita el archivo desde el origen en fragmentos de 8 MB.

Una vez que el fragmento llega al entorno de Front Door, se almacena en caché y se sirve inmediatamente al usuario. Después, Front Door realiza una captura previa del siguiente fragmento en paralelo. Este captura previa garantiza que el contenido sigue estando un fragmento por delante del usuario, lo que reduce la latencia. Este proceso continúa hasta que se descarga todo el archivo (si se ha solicitado) o el cliente termina la conexión.

Para más información sobre la solicitud de intervalo de bytes, vea [RFC 7233](https://www.rfc-editor.org/info/rfc7233).
Front Door almacena en caché los fragmentos cuando se reciben, por lo que no es necesario poner todo el archivo en la caché de Front Door. Las solicitudes subsiguientes del archivo o los intervalos de bytes se sirven desde la caché. Si no se almacenan en caché todos los fragmentos, se usa la captura previa para solicitar fragmentos del back-end. Esta optimización se basa en la capacidad del origen para admitir solicitudes de intervalos de bytes. Si el origen no admite solicitudes de intervalos de bytes, esta optimización no es efectiva.

## <a name="file-compression"></a>Compresión de archivos

Vea cómo mejorar el rendimiento con la compresión de archivos en Azure Front Door.

## <a name="query-string-behavior"></a>Comportamiento de las cadenas de consulta

Front Door permite controlar cómo se almacenan los archivos en caché para una solicitud web que contiene una cadena de consulta. En una solicitud web con una cadena de consulta, esta última es la parte de la solicitud que hay después del signo de interrogación (?). Una cadena de consulta puede contener uno o más pares clave-valor, en los cuales el nombre de campo y su valor están separados por un signo igual (=). Los pares clave-valor están separados entre ellos por una Y comercial (&). Por ejemplo, `http://www.contoso.com/content.mov?field1=value1&field2=value2`. Si hay más de un par clave-valor en una cadena de consulta de una solicitud, no importa el orden en el que se especifiquen.

* **Ignorar cadenas de consulta**: en este modo, Front Door pasa las cadenas de consulta del solicitante al origen en la primera solicitud y almacena en caché el recurso. Todas las solicitudes del recurso subsecuentes que se ofrecen desde el entorno de Front Door omiten las cadenas de consulta hasta que expira el recurso en caché.

* **Almacenar en caché todas las URL únicas**: en este modo, cada solicitud con un URL único, incluida la cadena de consulta, se trata como un recurso único con su propia memoria caché. Por ejemplo, la respuesta desde el origen de una solicitud a `www.example.ashx?q=test1` se almacena en caché en el entorno de Front Door y se devuelve en las cachés subsiguientes con la misma cadena de consulta. Se almacena en caché una solicitud de `www.example.ashx?q=test2` como un recurso independiente con su propia configuración de período de vida.
* También puede usar el conjunto de reglas para especificar el comportamiento de la **cadena de consulta de la clave de caché**, para incluir o excluir los parámetros especificados cuando se genere la clave de caché. Por ejemplo, la clave de caché predeterminada es /foo/Image/asset.html y la solicitud de ejemplo es `https://contoso.com//foo/image/asset.html?language=EN&userid=100&sessionid=200`. Hay una regla del conjunto de reglas para excluir la cadena de consulta "userid". Después, la clave de caché de la cadena de consulta sería `/foo/image/asset.html?language=EN&sessionid=200`.

## <a name="cache-purge"></a>Purga de la memoria caché

Consulte la purga de la caché.

## <a name="cache-expiration"></a>Expiración de la caché
El siguiente orden de encabezados se usa para determinar cuánto tiempo se almacenará un elemento en la memoria caché:</br>
1. Cache-Control: s-maxage=\<seconds>
2. Cache-Control: max-age=\<seconds>
3. Expira: \<http-date>

Los encabezados de respuesta Cache-Control que indican que la respuesta no se almacena en caché como Cache-Control: private, Cache-Control: no-cache y Cache-Control: no-store se respetan.  Si no hay ningún control de caché presente, el comportamiento predeterminado es que Front Door almacene en caché el recurso durante un tiempo X, donde X sería un valor aleatorio de entre 1 y 3 días.

## <a name="request-headers"></a>Encabezados de solicitud

Los siguientes encabezados de solicitud no se reenviarán a un origen cuando se use el almacenamiento en caché.
* Content-Length
* Transfer-Encoding

## <a name="cache-duration"></a>Duración de la caché

La duración de la caché puede configurarse en el conjunto de reglas. La duración de la caché establecida a través del conjunto de reglas es una invalidación de la caché verdadera. Esto significa que se usará el valor de invalidación con independencia de cuál sea el encabezado de respuesta de origen.

## <a name="next-steps"></a>Pasos siguientes

* Más información sobre las [condiciones de coincidencia del conjunto de reglas](concept-rule-set-match-conditions.md).
* Más información sobre las [acciones del conjunto de reglas](concept-rule-set-actions.md).

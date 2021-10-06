---
title: 'Azure Front Door: almacenamiento en caché | Microsoft Docs'
description: Este artículo ayuda a entender el comportamiento de Front Door con reglas de enrutamiento que han habilitado el almacenamiento en caché.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/13/2021
ms.author: duau
ms.openlocfilehash: 1fb1aafed996fa79177157f6c20e6727ee532e7d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128563122"
---
# <a name="caching-with-azure-front-door"></a>Almacenamiento en caché con Azure Front Door
En el documento siguiente se especifican los comportamientos de Front Door con reglas de enrutamiento que han habilitado el almacenamiento en caché. Front Door es una red Content Delivery Network (CDN) moderna con aceleración de sitios dinámicos y equilibrio de carga; también admite comportamientos de almacenamiento en caché como cualquier otra red CDN.

## <a name="delivery-of-large-files"></a>Suministro de archivos grandes
Azure Front Door proporciona archivos grandes sin un límite en el tamaño de los archivos. Front Door usa una técnica denominada fragmentación de objetos. Cuando se solicita un archivo grande, Front Door recupera partes más pequeñas del back-end. Después de recibir una solicitud de un archivo completo o de intervalos de bytes, el entorno de Front Door solicita el archivo desde el back-end en fragmentos de 8 MB.

Una vez que el fragmento llega al entorno de Front Door, se almacena en caché y se sirve inmediatamente al usuario. Después, Front Door realiza una captura previa del siguiente fragmento en paralelo. Este captura previa garantiza que el contenido sigue estando un fragmento por delante del usuario, lo que reduce la latencia. Este proceso continúa hasta que se descarga todo el archivo (si se ha solicitado) o el cliente termina la conexión.

Para más información sobre la solicitud de intervalo de bytes, vea [RFC 7233](https://www.rfc-editor.org/info/rfc7233).
Front Door almacena en caché los fragmentos cuando se reciben, por lo que no es necesario poner todo el archivo en la caché de Front Door. Las solicitudes subsiguientes del archivo o los intervalos de bytes se sirven desde la caché. Si no se almacenan en caché todos los fragmentos, se usa la captura previa para solicitar fragmentos del back-end. Esta optimización se basa en la capacidad del back-end de admitir solicitudes de intervalos de bytes. Si el back-end no admite solicitudes de intervalos de bytes, esta optimización no sirve.

## <a name="file-compression"></a>Compresión de archivos
Front Door comprime dinámicamente el contenido en el borde, lo que genera un tiempo de respuesta menor y más rápido para los clientes. Para que un archivo sea válido para la compresión, el almacenamiento en caché debe estar habilitado y el archivo debe ser de un tipo MIME para ser válido para la compresión. Actualmente, Front Door no permite cambiar esta lista. La lista actual es:
- "application/eot"
- "application/font"
- "application/font-sfnt"
- "application/javascript"
- "application/json"
- "application/opentype"
- "application/otf"
- "application/pkcs7-mime"
- "application/truetype"
- "application/ttf",
- "application/vnd.ms-fontobject"
- "application/xhtml+xml"
- "application/xml"
- "application/xml+rss"
- "application/x-font-opentype"
- "application/x-font-truetype"
- "application/x-font-THF"
- "application/x-httpd-cgi"
- "application/x-mpegurl"
- "application/x-opentype"
- "application/x-otf"
- "application/x-perl"
- "application/x-THF"
- "application/x-javascript"
- "font/eot"
- "font/ttf"
- "font/otf"
- "font/opentype"
- "image/svg+xml"
- "text/css"
- "text/csv"
- "text/html"
- "text/javascript"
- "text/js", "text/plain"
- "text/richtext"
- "text/tab-separated-values"
- "text/xml"
- "text/x-script"
- "text/x-component"
- "text/x-java-source"

Además, el tamaño del archivo debe estar entre 1 KB y 8 MB, ambos inclusive.

Estos perfiles admiten las codificaciones de compresión siguientes:
- [Gzip (GNU zip)](https://en.wikipedia.org/wiki/Gzip)
- [Brotli](https://en.wikipedia.org/wiki/Brotli)

Si una solicitud admite la compresión gzip y Brotli, la compresión Brotli tiene prioridad.</br>
Cuando una solicitud de un recurso especifica la compresión y la solicitud genera un error de caché, Front Door realiza la compresión del recurso directamente en el servidor POP. Después, el archivo comprimido se envía desde la caché. El elemento resultante se devuelve con transfer-encoding: chunked.

> [!NOTE]
> Las solicitudes de rango se pueden comprimir en tamaños diferentes. En Azure Front Door es necesario que los valores de longitud del contenido sean los mismos para cualquier solicitud HTTP GET. Si los clientes envían solicitudes de rango de bytes con el encabezado `accept-encoding` que provoca que el origen responda con diferentes longitudes de contenido, Azure Front Door devolverá un error 503. Puede deshabilitar la compresión en Origen/Azure Front Door, o bien crear una regla de conjunto de reglas para quitar `accept-encoding` de la solicitud para las solicitudes de rango de bytes.

## <a name="query-string-behavior"></a>Comportamiento de las cadenas de consulta
Front Door permite controlar cómo se almacenan los archivos en caché para una solicitud web que contiene una cadena de consulta. En una solicitud web con una cadena de consulta, esta última es la parte de la solicitud que hay después del signo de interrogación (?). Una cadena de consulta puede contener uno o más pares clave-valor, en los cuales el nombre de campo y su valor están separados por un signo igual (=). Los pares clave-valor están separados entre ellos por una Y comercial (&). Por ejemplo, `http://www.contoso.com/content.mov?field1=value1&field2=value2`. Si hay más de un par clave-valor en una cadena de consulta de una solicitud, no importa el orden en el que se especifiquen.
- **Pasar por alto las cadenas de consulta**: En este modo, Front Door pasa las cadenas de consulta del solicitante al back-end en la primera solicitud y almacena en la memoria caché el recurso. Todas las solicitudes del recurso subsecuentes que se ofrecen desde el entorno de Front Door omiten las cadenas de consulta hasta que expira el recurso en caché.

- **Almacenar en caché todas las URL únicas**: en este modo, cada solicitud con un URL único, incluida la cadena de consulta, se trata como un recurso único con su propia memoria caché. Por ejemplo, la respuesta desde el back-end a una solicitud de `www.example.ashx?q=test1` se almacena en caché en el entorno de Front Door y se devuelve en los almacenamientos en caché subsecuentes con la misma cadena de consulta. Se almacena en caché una solicitud de `www.example.ashx?q=test2` como un recurso independiente con su propia configuración de período de vida.

## <a name="cache-purge"></a>Purga de la memoria caché

Front Door almacena en caché los recursos hasta que el período de vida de dichos recursos (TTL) expira. Siempre que un cliente solicita un recurso con un período de vida expirado, el entorno de Front Door recupera una nueva copia actualizada de este para atender la solicitud y almacena en caché la versión actualizada.

El procedimiento recomendado para asegurarse de que los usuarios siempre obtengan la copia más reciente de los recursos es crear versiones correspondientes a cada actualización y publicarlos como nuevas URL. Front Door recuperará inmediatamente los nuevos recursos en las siguientes solicitudes de los clientes. A veces puede que quiera purgar contenido almacenado en caché de todos los nodos perimetrales y forzarlos todos para recuperar nuevos activos actualizados. Esto puede deberse a actualizaciones de la aplicación web o a actualizaciones rápidas de los recursos que contienen información incorrecta.

Seleccione los recursos que quiere purgar de los nodos perimetrales. Para borrar todos los recursos, seleccione **Purgar todo**. De lo contrario, en **Ruta de acceso**, escriba la ruta de acceso de cada recurso que quiera purgar.

Estos formatos se admiten en las listas de rutas de acceso que se van a purgar:

- **Purga de ruta de acceso única** Purgue recursos concretos mediante la especificación de la ruta de acceso completa del recurso (sin el protocolo y el dominio) con la extensión de archivo, por ejemplo, /pictures/strasbourg.png;
- **Purga con carácter comodín**: se puede usar el asterisco (\*) como carácter comodín. Purgue todas las carpetas, subcarpetas y archivos de un punto de conexión con /\* en la ruta de acceso o todas las subcarpetas y archivos de una carpeta concreta especificando la carpeta seguido de /\*; por ejemplo, /pictures/\*.
- **Purga de dominio raíz**: purgue la raíz del punto de conexión con "/" en la ruta de acceso.

> [!NOTE]
> **Depuración de dominios con caracteres comodín**: la especificación de rutas de acceso en caché para la purga como se describe en esta sección no se aplica a ningún dominio con caracteres comodín que esté asociado a Front Door. Actualmente, no se admite la purga directa de dominios con caracteres comodín. Puede purgar las rutas de acceso de subdominios específicos especificando ese subdominio concreto y la ruta de acceso de purga. Por ejemplo, si Front Door tiene `*.contoso.com`, puedo purgar los recursos de mi subdominio `foo.contoso.com` escribiendo `foo.contoso.com/path/*`. Actualmente, la especificación de los nombres de host en la ruta de acceso del contenido de purga se limita a los subdominios de dominios con caracteres comodín, si procede.
>

La purga de la memoria caché en Front Door distingue mayúsculas de minúsculas. Además, es independiente de la cadena de consulta, lo que significa que purgar una dirección URL purgará todas sus variaciones de la cadena de consulta. 

## <a name="cache-expiration"></a>Expiración de la caché
El siguiente orden de encabezados se usa para determinar cuánto tiempo se almacenará un elemento en la memoria caché:</br>
1. Cache-Control: s-maxage=\<seconds>
2. Cache-Control: max-age=\<seconds>
3. Expira: \<http-date>

Los encabezados de respuesta Cache-Control que indican que la respuesta no se almacena en caché como Cache-Control: private, Cache-Control: no-cache y Cache-Control: no-store se respetan.  Si no hay ningún control de caché presente, el comportamiento predeterminado es que Front Door almacene en caché el recurso durante un tiempo X, donde X se elige aleatoriamente entre 1 y 3 días.

## <a name="request-headers"></a>Encabezados de solicitud

Los siguientes encabezados de solicitud no se reenviarán a un back-end cuando se use el almacenamiento en caché.
- Content-Length
- Transfer-Encoding

## <a name="cache-behavior-and-duration"></a>Duración y comportamiento de la caché

La duración y el comportamiento de la caché se pueden configurar tanto en la regla de enrutamiento del diseñador de Front Door como en el motor de reglas. La configuración del almacenamiento en caché del motor de reglas siempre invalida a la de la regla de enrutamiento del diseñador de Front Door.

* Cuando el *almacenamiento en caché* está **deshabilitado**, Front Door no almacena en caché los contenidos de la respuesta, independientemente de las directivas de respuesta de origen.

* Cuando el *almacenamiento en caché* está **habilitado**, el comportamiento de la caché es diferente según los distintos valores de *Usar duración de caché predeterminada*.
    * Cuando *Usar duración de caché predeterminada* está establecido en **Sí**, Front Door siempre respeta la directiva de encabezado de respuesta de origen. Si falta la directiva de origen, Front Door almacena en caché el contenido entre uno y tres días.
    * Cuando *Usar duración de caché predeterminada* está establecido en **No**, Front Door siempre invalida con la *duración de la caché* (campos obligatorios), lo que significa que almacena en caché el contenido durante la duración de la caché y omite los valores de las directivas de respuesta de origen. 

> [!NOTE]
> * La *duración de la caché* establecida en la regla de enrutamiento del diseñador de Front Door es la **duración de la caché mínima**. Esta invalidación no funciona si el encabezado de control de la caché del origen tiene un TTL mayor que el valor de invalidación.
> * El contenido almacenado en caché se puede expulsar de Azure Front Door antes de que haya expirado si no se solicita con la frecuencia suficiente para acomodar el contenido solicitado con más frecuencia.
>

## <a name="next-steps"></a>Pasos siguientes

- Aprenda a [crear una instancia de Front Door](quickstart-create-front-door.md).
- Más información acerca de cómo [funciona Front Door](front-door-routing-architecture.md).

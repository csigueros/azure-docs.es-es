---
title: Características del motor de reglas de Azure CDN de Verizon Premium
description: Documentación de referencia sobre las características del motor de reglas de Azure CDN de Verizon Premium.
services: cdn
author: duongau
ms.service: azure-cdn
ms.topic: article
ms.date: 06/02/2020
ms.author: duau
ms.openlocfilehash: c1f77903378cdced27497893638b03a03279adfb
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131423344"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-features"></a>Características del motor de reglas de Azure CDN de Verizon Premium

En este tema se muestran descripciones detalladas de las características disponibles para el [motor de reglas](cdn-verizon-premium-rules-engine.md)de Azure Content Delivery Network (CDN).

La tercera parte de una regla es la característica. Una característica define el tipo de acción que se aplica al tipo de solicitud que se identifica con un conjunto de condiciones de coincidencia.

## <a name="azure-cdn-from-verizon-premium-rules-engine-features-reference"></a><a name="top"></a>Referencia de las características del motor de reglas de Azure CDN de Verizon Premium

Los tipos de características disponibles son:

* [Acceder](#access)
* [Almacenamiento en caché](#caching)
* [Comment](#comment)
* [Encabezados](#headers)
* [Registros](#logs)
* [Optimize](#optimize) (Optimizar)
* [Origen](#origin)
* [Especialidad](#specialty)
* [URL](#url)
* [Firewall de aplicaciones web](#waf)

### <a name="access"></a><a name="access"></a>Acceso

Estas características están diseñadas para controlar el acceso al contenido.

| Nombre       | Propósito                                                           |
|------------|-------------------------------------------------------------------|
| [Denegar acceso (403)](https://docs.vdms.com/cdn/Content/HRE/F/Deny-Access-403.htm) | Determina si todas las solicitudes se rechazan con una respuesta 403-Prohibido. |
| [Autenticación de token](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth.htm) | Determina si se aplicará a una solicitud de autenticación basada en tokens. |
| [Código de denegación de autenticación de tokens](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Denial-Code.htm) | Determina el tipo de respuesta que se devolverá al usuario cuando se deniega una solicitud debido a la autenticación basada en tokens. |
| [Ignorar mayúsculas y minúsculas en URL de autenticación de tokens](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Ignore-URL-Case.htm) | Determina si las comparaciones de URL realizadas por la autenticación basada en tokens distinguirán mayúsculas de minúsculas. |
| [Parámetro de autenticación de tokens](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Parameter.htm) | Determina si debe cambiarse el parámetro de cadena de consulta de autenticación basada en tokens. |

**[Volver al principio](#top)**

### <a name="caching"></a><a name="caching"></a>Almacenamiento en caché

Estas características están diseñadas para personalizar cuándo y cómo se almacena el contenido en caché.

| Nombre       | Propósito                                                           |
|------------|-------------------------------------------------------------------|
| [Parámetros de ancho de banda](https://docs.vdms.com/cdn/Content/HRE/F/Bandwidth-Parameters.htm) | Determina si se activarán los parámetros de limitación de ancho de banda (es decir, ec_rate y ec_prebuf). |
| [Limitación de ancho de banda](https://docs.vdms.com/cdn/Content/HRE/F/Bandwidth-Throttling.htm) | Limita el ancho de banda para la respuesta de nuestros servidores perimetrales. |
| [Omisión de la memoria caché](https://docs.vdms.com/cdn/Content/HRE/F/Bypass-Cache.htm) | Determina si la solicitud puede aprovechar nuestra tecnología de almacenamiento en caché. |
| [Tratamiento de encabezados Cache-Control](https://docs.vdms.com/cdn/Content/HRE/F/Cache-Control-Header-Treatment.htm) |  Controla la generación de encabezados Cache-Control mediante el servidor perimetral cuando está activa la característica Max-Age externa. |
| [Cadena de consulta de clave de caché](https://docs.vdms.com/cdn/Content/HRE/F/Cache-Key-Query-String.htm) | Determina si la **clave de caché** incluirá o excluirá los parámetros de cadena de consulta asociados a una solicitud. <br> *Una ruta de acceso relativa que identifica de forma única un recurso con fines de almacenamiento en caché.  Nuestros servidores perimetrales usan esta ruta de acceso relativa al comprobar el contenido almacenado en caché.  De forma predeterminada, una clave de caché no contendrá parámetros de cadena de consulta. |
| [Reescritura de clave de caché](https://docs.vdms.com/cdn/Content/HRE/F/Cache-Key-Rewrite.htm) | Reescribe la clave de caché asociada a una solicitud. |
| [Relleno de la memoria caché completa](https://docs.vdms.com/cdn/Content/HRE/F/Complete-Cache-Fill.htm) | Determina lo que ocurre cuando una solicitud tiene como resultado un error de caché parcial en un servidor perimetral. |
| [Comprimir tipos de archivo](https://docs.vdms.com/cdn/Content/HRE/F/Compress-File-Types.htm) | Define los formatos de archivo que se van a comprimir en el servidor. | 
| [Max-Age interna predeterminada](https://docs.vdms.com/cdn/Content/HRE/F/Default-Internal-Max-Age.htm) | Determina el intervalo predeterminado de max-age para el servidor perimetral en la revalidación de caché del servidor de origen. |
| [Tratamiento del encabezado Expires](https://docs.vdms.com/cdn/Content/HRE/F/Expires-Header-Treatment.htm) | Controla la generación de encabezados Expires mediante el servidor perimetral cuando está activa la característica Max-Age externa. |
| [Max-Age externa](https://docs.vdms.com/cdn/Content/HRE/F/External-Max-Age.htm) | Determina el intervalo de max-age para el explorador en la revalidación de caché del servidor de perimetral. |
| [Forzar Max-Age interna](https://docs.vdms.com/cdn/Content/HRE/F/Force-Internal-Max-Age.htm) | Determina el intervalo de max-age para el servidor perimetral en la revalidación de caché del servidor de origen. |
| [Compatibilidad de H.264 (descarga progresiva de HTTP)](https://docs.vdms.com/cdn/Content/HRE/F/H.264-Support-HPD.htm) | Determina los tipos de formatos de archivo H.264 que pueden usarse para transmitir contenido en streaming. |
| [Parámetros de búsqueda de vídeo compatibles con H. 264](https://docs.vdms.com/cdn/Content/HRE/F/H.264-Support-VSP-HPD.htm) | Invalida los nombres asignados a los parámetros que controlan la búsqueda a través de medios H.264 cuando se usa la descarga progresiva de HTTP. |
| [Respetar la solicitud de no almacenar en caché](https://docs.vdms.com/cdn/Content/HRE/F/Honor-No-Cache-Request.htm) | Determina si las solicitudes de no almacenar en caché de un cliente HTTP se reenviarán al servidor de origen. |
| [Ignorar la opción de no almacenar en caché de origen](https://docs.vdms.com/cdn/Content/HRE/F/Ignore-Origin-No-Cache.htm) | Determina si la CDN omitirá determinadas directivas procedentes de un servidor de origen. |
| [Ignorar intervalos que no se puedan satisfacer](https://docs.vdms.com/cdn/Content/HRE/F/Ignore-Unsatisfiable-Ranges.htm) | Determina la respuesta que se devolverá a los clientes cuando una solicitud genere un código de estado 416 - No se puede satisfacer el intervalo solicitado. |
| [Max-Stale interna](https://docs.vdms.com/cdn/Content/HRE/F/Internal-Max-Stale.htm) | Controla cuánto tiempo después de la hora de expiración normal puede atenderse un recurso almacenado en caché desde un servidor perimetral cuando el servidor perimetral no puede volver a validar el recurso almacenado en caché con el servidor de origen. |
| [Uso compartido de caché parcial](https://docs.vdms.com/cdn/Content/HRE/F/Partial-Cache-Sharing.htm) | Determina si una solicitud puede generar contenido almacenado parcialmente en caché. |
| [Prevalidar el contenido guardado en caché](https://docs.vdms.com/cdn/Content/HRE/F/Prevalidate-Cached-Content.htm) | Determina si el contenido almacenado en caché será apto para la revalidación temprana antes de que expire su período de vida. |
| [Actualizar archivos de caché de cero bytes](https://docs.vdms.com/cdn/Content/HRE/F/Refresh-Zero-Byte-Cache-Files.htm) | Determina cómo nuestros servidores perimetrales controlan la solicitud de un cliente HTTP para un recurso de la caché de 0 bytes. |
| [Establecer códigos de estado almacenables en caché](https://docs.vdms.com/cdn/Content/HRE/F/Set-Cacheable-Status-Codes.htm) | Define el conjunto de códigos de estado que puede dar lugar a contenido almacenado en caché. |
| [Entrega de contenido obsoleto en caso de error](https://docs.vdms.com/cdn/Content/HRE/F/Stale-Content-Delivery-on-Error.htm) | Determina si se entregará el contenido almacenado en caché cuando se produzca un error durante la revalidación de caché o al recuperar el contenido solicitado desde el servidor de origen del cliente. | 
| [Obsoleto durante revalidación](https://docs.vdms.com/cdn/Content/HRE/F/Stale-While-Revalidate.htm) | Mejora el rendimiento al permitir que nuestros servidores perimetrales sirvan un cliente obsoleto al solicitante mientras se lleva a cabo la revalidación. |

**[Volver al principio](#top)**

### <a name="comment"></a><a name="comment"></a>Comentario

La característica Comentario permite agregar una nota en una regla.

**[Volver al principio](#top)**

### <a name="headers"></a><a name="headers"></a>Encabezados

Estas características están diseñadas para agregar, modificar o eliminar encabezados de la solicitud o respuesta.

| Nombre       | Propósito                                                           |
|------------|-------------------------------------------------------------------|
| [Encabezado de respuesta Age](https://docs.vdms.com/cdn/Content/HRE/F/Age-Response-Header.htm) | Determina si se incluirá un encabezado de respuesta Age en la respuesta enviada al solicitante. |
| [Depurar encabezados de respuesta de la caché](https://docs.vdms.com/cdn/Content/HRE/F/Debug-Cache-Response-Headers.htm) | Determina si una respuesta puede incluir el [encabezado de respuesta X-EC-Debug](https://docs.vdms.com/cdn/Content/Knowledge_Base/X_EC_Debug.htm) que proporciona información sobre la directiva de caché del recurso solicitado. |
| [Modificar encabezado de solicitud de cliente](https://docs.vdms.com/cdn/Content/HRE/F/Modify-Client-Request-Header.htm) | Sobrescribe, agrega o elimina un encabezado en una solicitud. |
| [Modificar encabezado de respuesta de cliente](https://docs.vdms.com/cdn/Content/HRE/F/Modify-Client-Response-Header.htm) | Sobrescribe, agrega o elimina un encabezado en una respuesta. |
| [Establecer encabezado personalizado de IP de cliente](https://docs.vdms.com/cdn/Content/HRE/F/Set-Client-IP-Custom-Header.htm) | Permite que la dirección IP del cliente solicitante se agregue a la solicitud como encabezado de solicitud personalizado. |

**[Volver al principio](#top)**

### <a name="logs"></a><a name="logs"></a>Registros

Estas características están diseñadas para personalizar los datos almacenados en archivos de registro sin procesar.

| Nombre       | Propósito                                                           |
|------------|-------------------------------------------------------------------|
| [Campo de registro personalizado 1](https://docs.vdms.com/cdn/Content/HRE/F/Custom-Log-Field-1.htm) | Determina el formato y el contenido que se asignará al campo de registro personalizado en un archivo de registro sin procesar. |
| [Cadena de consulta del registro](https://docs.vdms.com/cdn/Content/HRE/F/Log-Query-String.htm) | Determina si una cadena de consulta se almacenará con la dirección URL en los registros de acceso. |

**[Volver al principio](#top)**

### <a name="optimize"></a><a name="optimize"></a>Optimización

Estas características determinan si una solicitud se someterá a las optimizaciones que proporciona Edge Optimizer.

| Nombre       | Propósito                                                           |
|------------|-------------------------------------------------------------------|
| [Edge Optimizer](https://docs.vdms.com/cdn/Content/HRE/F/Edge-Optimizer.htm) | Determina si Edge Optimizer puede aplicarse a una solicitud. |
| [Edge Optimizer: creación de instancias de configuración](https://docs.vdms.com/cdn/Content/HRE/F/Edge-Optimizer-Instantiate-Configuration.htm) | Crea o activa una instancia de configuración de Edge Optimizer asociada a un sitio. |

**[Volver al principio](#top)**

### <a name="origin"></a><a name="origin"></a>Origen

Estas características están diseñadas para controlar la forma en que la red CDN se comunica con un servidor de origen.

| Nombre       | Propósito                                                           |
|------------|-------------------------------------------------------------------|
| [Número máximo de solicitudes de conexión persistente](https://docs.vdms.com/cdn/Content/HRE/F/Maximum-Keep-Alive-Requests.htm) | Define el número máximo de solicitudes de conexión persistente antes de cerrarse. |
| [Encabezados de proxy especiales](https://docs.vdms.com/cdn/Content/HRE/F/Proxy-Special-Headers.htm) | Define el conjunto de [encabezados de solicitud específicos de la red CDN](https://docs.vdms.com/cdn/Content/Knowledge_Base/Request-Format.htm#RequestHeaders) que se reenviarán desde un servidor perimetral hasta un servidor de origen. |

**[Volver al principio](#top)**

### <a name="specialty"></a><a name="specialty"></a>Especialidad

Estas características ofrecen funcionalidad avanzada que solo deben utilizar los usuarios avanzados.

| Nombre       | Propósito                                                           |
|------------|-------------------------------------------------------------------|
| [Métodos HTTP almacenables en caché](https://docs.vdms.com/cdn/Content/HRE/F/Cacheable-HTTP-Methods.htm) | Determina el conjunto de métodos HTTP adicionales que pueden almacenarse en caché en nuestra red. |
| [Tamaño del cuerpo de solicitud almacenable en caché](https://docs.vdms.com/cdn/Content/HRE/F/Cacheable-Request-Body-Size.htm) | Define el umbral que determina si una respuesta POST se puede almacenar en caché. |
| [QUIC](https://docs.vdms.com/cdn/Content/HRE/F/QUIC.htm) | Determina si se informará al cliente de que nuestro servicio de CDN admite QUIC. |
| [Optimización de streaming](https://docs.vdms.com/cdn/Content/HRE/F/Streaming-Optimization.htm) | Ajusta la configuración del almacenamiento en caché para optimizar el rendimiento del streaming en vivo y reducir la carga en el servidor de origen. |
| [Variable USER](https://docs.vdms.com/cdn/Content/HRE/F/User-Variable.htm) | Asigna un valor a una variable definida por el usuario que se pasa a la solución de procesamiento del tráfico personalizada. |

**[Volver al principio](#top)**

### <a name="url"></a><a name="url"></a>URL

Estas características permiten reescribir una solicitud o redirigirla a una dirección URL diferente.

| Nombre       | Propósito                                                           |
|------------|-------------------------------------------------------------------|
| [Seguir redireccionamientos](https://docs.vdms.com/cdn/Content/HRE/F/Follow-Redirects.htm) | Determina si las solicitudes se pueden redirigir al nombre de host definido en el encabezado Ubicación que devuelve el servidor de origen de un cliente. |
| [Redirección de direcciones URL](https://docs.vdms.com/cdn/Content/HRE/F/URL-Redirect.htm) | Redirige las solicitudes a través del encabezado Ubicación. |
| [Reescritura de direcciones URL](https://docs.vdms.com/cdn/Content/HRE/F/URL-Rewrite.htm) | Reescribe la dirección URL de la solicitud. |

**[Volver al principio](#top)**

### <a name="web-application-firewall"></a><a name="waf"></a>Firewall de aplicaciones web

La característica [Firewall de aplicaciones web](https://docs.vdms.com/cdn/Content/HRE/F/Web_Application_Firewall.htm) determina si el firewall de aplicaciones web examinará la solicitud.

**[Volver al principio](#top)**

Para obtener las características más recientes, consulte la [documentación del motor de reglas de Verizon](https://docs.vdms.com/cdn/index.html#Quick_References/HRE_QR.htm#Actions).

## <a name="next-steps"></a>Pasos siguientes

- [Referencia del motor de reglas](cdn-verizon-premium-rules-engine-reference.md)
- [Expresiones condicionales del motor de reglas](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Condiciones de coincidencia del motor de reglas](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Invalidación del comportamiento HTTP mediante el motor de reglas](cdn-verizon-premium-rules-engine.md)
- [Información general de Azure CDN](cdn-overview.md)

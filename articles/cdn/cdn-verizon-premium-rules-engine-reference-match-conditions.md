---
title: Condiciones de coincidencia del motor de reglas de Azure CDN de Verizon Premium
description: Documentación de referencia sobre las condiciones de coincidencia del motor de reglas de Azure Content Delivery Network de Verizon Premium.
services: cdn
author: duongau
ms.service: azure-cdn
ms.topic: article
ms.date: 05/26/2020
ms.author: duau
ms.openlocfilehash: 61576e21bbf8fa260a5003bee62ac40b63d342c7
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131446740"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-match-conditions"></a>Condiciones de coincidencia del motor de reglas de Azure CDN de Verizon Premium

En este artículo se muestran descripciones detalladas de las condiciones de coincidencia disponibles para el [motor de reglas](cdn-verizon-premium-rules-engine.md) de Azure Content Delivery Network (CDN) de Verizon Premium.

La segunda parte de una regla es la condición de coincidencia. Una condición de coincidencia identifica tipos específicos de solicitudes para los que se ejecutará un conjunto de características.

Por ejemplo, puede usar una condición de coincidencia para:

- Filtrar las solicitudes de contenido de una ubicación concreta.
- Filtrar las solicitudes generadas a partir de una dirección IP o país o región específicos.
- Filtrar las solicitudes por la información de encabezado.

## <a name="match-conditions"></a><a name="top"></a>Condiciones de coincidencia

* [Siempre](#always)
* [Dispositivo](#device)
* [Ubicación](#location)
* [Origen](#origin)
* [Solicitud](#request)
* [URL](#url)

### <a name="always"></a><a name="always"></a>Siempre

[La condición de coincidencia Siempre ](https://docs.vdms.com/cdn/Content/HRE/M/Always.htm) está diseñada para aplicar un conjunto predeterminado de características a todas las solicitudes.

### <a name="device"></a><a name="device"></a>Dispositivo

Estas condiciones de coincidencia están diseñadas para identificar solicitudes en función del agente de usuario del cliente.

| Nombre       | Propósito                                                           |
|------------|-------------------------------------------------------------------|
| Nombre de la marca | Identifica las solicitudes en las que el nombre de marca del dispositivo coincide con un: <br> **-** Valor específico ([literal del nombre de marca](https://docs.vdms.com/cdn/Content/HRE/M/D-Brand-Name-Literal.htm)) <br> **-** Expresión regular ([expresión regular del nombre de marca](https://docs.vdms.com/cdn/Content/HRE/M/D-Brand-Name-Regex.htm)) <br> **-** Patrón específico ([carácter comodín del nombre de marca](https://docs.vdms.com/cdn/Content/HRE/M/D-Brand-Name-Wildcard.htm)) |
| Sistema operativo del dispositivo | Identifica las solicitudes en las que el SO del dispositivo coincide con un: <br> **-** Valor específico ([literal del SO del dispositivo](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Literal.htm)) <br> **-** Expresión regular ([expresión regular del SO del dispositivo](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Regex.htm)) <br> **-** Patrón específico ([carácter comodín del SO del dispositivo](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Wildcard.htm)) |
| Versión del sistema operativo del dispositivo | Identifica las solicitudes en las que la versión del SO del dispositivo coincide con un: <br> **-** Valor específico ([literal de la versión del SO del dispositivo](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Version-Literal.htm)) <br> **-** Expresión regular ([expresión regular de la versión del SO del dispositivo](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Version-Regex.htm)) <br> **-** Patrón específico ([carácter comodín de la versión del SO del dispositivo](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Version-Wildcard.htm)) |
| [¿Orientación dual?](https://docs.vdms.com/cdn/Content/HRE/M/D-Dual-Orientation.htm) | Identifica las solicitudes en las que el dispositivo admite la orientación dual. |
| DTD preferido de HTML | Identifica las solicitudes en las que el DTD preferido de HTML del dispositivo coincide con un: <br> **-** Valor específico ([literal del DTD preferido de HTML](https://docs.vdms.com/cdn/Content/HRE/M/D-HTML-Preferred-DTD-Literal.htm)) <br> **-** Expresión regular ([expresión regular del DTD preferido de HTML](https://docs.vdms.com/cdn/Content/HRE/M/D-HTML-Preferred-DTD-Regex.htm)) <br> **-** Patrón específico ([carácter comodín del DTD preferido de HTML](https://docs.vdms.com/cdn/Content/HRE/M/D-HTML-Preferred-DTD-Wildcard.htm)) |
| [¿Imagen de inclusión?](https://docs.vdms.com/cdn/Content/HRE/M/D-Image-Inlining.htm) | Identifica las solicitudes en las que el dispositivo admite imágenes codificadas en Base64. |
| [¿Es Android?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Android.htm) | Identifica las solicitudes en las que el dispositivo usa el SO Android. |
| [¿Es la aplicación?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-App.htm) | Identifica las solicitudes en las que una aplicación nativa ha solicitado contenido. |
| [¿Está completo el escritorio?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Full-Desktop.htm) | Identifica las solicitudes en las que el dispositivo proporciona una experiencia de escritorio completo. |
| [¿Es iOS?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-iOS.htm) | Identifica las solicitudes en las que el dispositivo usa iOS. |
| [¿Es un robot?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Robot.htm) | Identifica las solicitudes en las que se considera que el dispositivo es un cliente HTTP automatizado (por ejemplo, un rastreador de robot). |
| [¿Es un Smart TV?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Smart-TV.htm) | Identifica las solicitudes en las que el dispositivo es un smart TV. |
| [¿Es un smartphone?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Smartphone.htm) | Identifica las solicitudes en las que el dispositivo es un smartphone.
| [¿Es una tablet?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Tablet.htm) | Identifica las solicitudes en las que el dispositivo es una tablet. |
| [¿Tiene pantalla táctil?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Touchscreen.htm) | Identifica las solicitudes en las que el dispositivo señalador principal del dispositivo es una pantalla táctil. |
| [¿Es un Windows Phone?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Windows-Phone.htm) | Identifica solicitudes en las que el dispositivo es un Windows Mobile 6.5/Windows Phone 7 o posterior. |
| [¿Es un dispositivo inalámbrico?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Wireless-Device.htm) | Identifica las solicitudes en las que el dispositivo es inalámbrico. 
| Nombre de marca | Identifica las solicitudes en las que el nombre de marca del dispositivo coincide con un: <br> **-** Valor específico ([literal de nombre de marketing](https://docs.vdms.com/cdn/Content/HRE/M/D-Marketing-Name-Literal.htm)) <br> **-** Expresión regular ([expresión regular del nombre de marca](https://docs.vdms.com/cdn/Content/HRE/M/D-Marketing-Name-Regex.htm)) <br> **-** Patrón específico ([carácter comodín del nombre de marca](https://docs.vdms.com/cdn/Content/HRE/M/D-Marketing-Name-Wildcard.htm)) |
| Explorador móvil | Identifica las solicitudes en las que el explorador del dispositivo coincide con un: <br> **-** Valor específico ([literal de explorador móvil](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Literal.htm)) <br> **-** Expresión regular ([expresión regular del explorador móvil](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Regex.htm)) <br> **-** Patrón específico ([carácter comodín del explorador móvil](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Wildcard.htm)) |
| Versión del explorador móvil | Identifica las solicitudes en las que la versión del explorador del dispositivo coincide con un: <br> **-** Valor específico ([literal de la versión de explorador móvil](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Version-Literal.htm)) <br> **-** Expresión regular ([Expresión regular de la versión del explorador móvil](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Version-Regex.htm)) <br> **-** Patrón específico ([carácter comodín de la versión del explorador móvil](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Version-Wildcard.htm)) |
| Nombre del modelo | Identifica las solicitudes en las que el nombre del modelo del dispositivo coincide con un: <br> **-** Valor específico ([literal del nombre de modelo](https://docs.vdms.com/cdn/Content/HRE/M/D-Model-Name-Literal.htm)) <br> **-** Expresión regular ([expresión regular del nombre del modelo](https://docs.vdms.com/cdn/Content/HRE/M/D-Model-Name-Regex.htm)) <br> **-** Patrón específico ([carácter comodín del nombre del modelo](https://docs.vdms.com/cdn/Content/HRE/M/D-Model-Name-Wildcard.htm)) |
| [¿Descarga progresiva?](https://docs.vdms.com/cdn/Content/HRE/M/D-Progressive-Download.htm) | Identifica solicitudes en las que el dispositivo admite la descarga progresiva. |
| Fecha de la versión | Identifica las solicitudes en las que la fecha de lanzamiento del dispositivo coincide con un: <br> **-** Valor específico ([literal de la fecha de lanzamiento](https://docs.vdms.com/cdn/Content/HRE/M/D-Release-Date-Literal.htm)) <br> **-** Expresión regular ([expresión regular de la fecha de lanzamiento](https://docs.vdms.com/cdn/Content/HRE/M/D-Release-Date-Regex.htm)) <br> **-** Patrón específico ([carácter comodín de la fecha de lanzamiento](https://docs.vdms.com/cdn/Content/HRE/M/D-Release-Date-Wildcard.htm)) |
| [Altura de resolución](https://docs.vdms.com/cdn/Content/HRE/M/D-Resolution-Height.htm) | Identifica solicitudes por la altura del dispositivo. |
| [Anchura de resolución](https://docs.vdms.com/cdn/Content/HRE/M/D-Resolution-Width.htm) | Identifica solicitudes por la anchura del dispositivo. |

**[Volver al principio](#top)**

### <a name="location"></a><a name="location"></a>Ubicación

Estas condiciones de coincidencia están diseñadas para identificar solicitudes en función de la ubicación del solicitante.

| Nombre       | Propósito                                                           |
|------------|-------------------------------------------------------------------|
| [Número de sistema autónomo (AS)](https://docs.vdms.com/cdn/Content/HRE/M/AS-Number.htm) | Identifica solicitudes que se originan en una red determinada. |
| Nombre de la ciudad | Identifica las solicitudes que se originan en una ciudad cuyo nombre coincide con un: <br> **-** Valor específico ([literal del nombre de la ciudad](https://docs.vdms.com/cdn/Content/HRE/M/City-Name-Literal.htm)) <br> **-** Expresión regular ([expresión regular del nombre de la ciudad](https://docs.vdms.com/cdn/Content/HRE/M/City-Name-Regex.htm)) |
| [Continente](https://docs.vdms.com/cdn/Content/HRE/M/Continent.htm) | Identifica solicitudes que se originan en continentes determinados. |
| [País](https://docs.vdms.com/cdn/Content/HRE/M/Country.htm) | Identifica solicitudes que se originan en países determinados. |
| [Código DMA](https://docs.vdms.com/cdn/Content/HRE/M/DMA-Code.htm) | Identifica solicitudes que se originan en áreas metropolitanas especificadas (áreas comerciales designadas). |
| [Latitud](https://docs.vdms.com/cdn/Content/HRE/M/Latitude.htm) | Identifica solicitudes que se originan en latitudes determinadas. |
| [Longitud](https://docs.vdms.com/cdn/Content/HRE/M/Longitude.htm) | Identifica solicitudes que se originan en longitudes especificadas. |
| [Código metro](https://docs.vdms.com/cdn/Content/HRE/M/Metro-Code.htm) | Identifica solicitudes que se originan en áreas metropolitanas especificadas (áreas comerciales designadas). |
| [Código postal](https://docs.vdms.com/cdn/Content/HRE/M/Postal-Code.htm) | Identifica solicitudes que se originan en códigos postales especificados. |
| [Código de región](https://docs.vdms.com/cdn/Content/HRE/M/Region-Code.htm) | Identifica solicitudes que se originan en regiones especificadas. |

> [!NOTE]
> **¿Debo usar código metro o código DMA?** <br>
Ambas condiciones de coincidencia proporcionan la misma capacidad. Sin embargo, se recomienda el uso de la condición de coincidencia de código metro para identificar las solicitudes por DMA.

**[Volver al principio](#top)**

### <a name="origin"></a><a name="origin"></a>Origen

Estas condiciones de coincidencia están diseñadas para identificar solicitudes que señalan al servidor de origen de un cliente o de almacenamiento de CDN.

| Nombre       | Propósito                                                           |
|------------|-------------------------------------------------------------------|
| [Origen de red CDN](https://docs.vdms.com/cdn/Content/HRE/M/CDN-Origin.htm) | Identifica solicitudes de contenido almacenado en el almacenamiento de CDN. |
| [Origen de cliente](https://docs.vdms.com/cdn/Content/HRE/M/Customer-Origin.htm) | Identifica solicitudes de contenido almacenado en el servidor de origen de un cliente específico. |

**[Volver al principio](#top)**

### <a name="request"></a><a name="request"></a>Solicitud

Estas condiciones de coincidencia están diseñadas para identificar solicitudes en función de sus propiedades.

| Nombre              | Propósito                                                                |
|-------------------|------------------------------------------------------------------------|
| [Dirección IP de cliente](https://docs.vdms.com/cdn/Content/HRE/M/Client-IP-Address.htm) | Identifica solicitudes que se originan en una dirección IP determinada. |
| Parámetro de cookie  | Identifica una solicitud si contiene una cookie que coincide con un: <br> **-** Valor específico ([literal de parámetro de cookie](https://docs.vdms.com/cdn/Content/HRE/M/Cookie-Parameter-Literal.htm)) <br> **-** Expresión regular ([expresión regular de parámetro de cookie](https://docs.vdms.com/cdn/Content/HRE/M/Cookie-Parameter-Regex.htm) <br> **-** Patrón específico ([carácter comodín de parámetro de cookie](https://docs.vdms.com/cdn/Content/HRE/M/Cookie-Parameter-Wildcard.htm)) |
| [Cname perimetral](https://docs.vdms.com/cdn/Content/HRE/M/Edge-CNAME.htm) | Identifica solicitudes que señalan a un CNAME perimetral específico . |
| Dominio de referencia | Identifica una solicitud en función de si se hacía referencia a ella mediante un nombre de host que coincide con un: <br> **-** Valor específico ([literal de dominio de referencia](https://docs.vdms.com/cdn/Content/HRE/M/Referring-Domain-Literal.htm)) <br> **-** Patrón específico ([carácter comodín de dominio de referencia](https://docs.vdms.com/cdn/Content/HRE/M/Referring-Domain-Wildcard.htm)) |
| Encabezado de solicitud | Identifica una solicitud en función de si contiene un encabezado que coincide con un: <br> **-** Valor específico ([literal de encabezado de solicitud](https://docs.vdms.com/cdn/Content/HRE/M/Request-Header-Literal.htm)) <br> **-** Expresión regular ([expresión regular de encabezado de solicitud](https://docs.vdms.com/cdn/Content/HRE/M/Request-Header-Regex.htm)) <br> **-** Patrón específico ([carácter comodín de encabezado de solicitud](https://docs.vdms.com/cdn/Content/HRE/M/Request-Header-Wildcard.htm)) |
| [Método de solicitud](https://docs.vdms.com/cdn/Content/HRE/M/Request-Method.htm) | Identifica solicitudes por su método HTTP. |
| [Esquema de solicitud](https://docs.vdms.com/cdn/Content/HRE/M/Request-Scheme.htm) | Identifica solicitudes por su protocolo HTTP. |

**[Volver al principio](#top)**

### <a name="url"></a><a name="url"></a>URL

| Nombre              | Propósito                                                                |
|-------------------|------------------------------------------------------------------------|
| Ruta de acceso URL | Identifica las solicitudes en las que su ruta de acceso relativa, incluido FILENAME, coincide con un: <br> **-** Valor específico ([literal de ruta de acceso URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Literal.htm)) <br> **-** Expresión regular ([expresión regular de ruta de acceso URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Regex.htm)) <br> **-** Patrón específico ([carácter comodín de ruta de acceso URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Wildcard.htm)) |
| Directorio de ruta de acceso URL | Identifica las solicitudes en las que su ruta de acceso relativa coincide con un: <br> **-** Valor específico ([literal de directorio de ruta de acceso URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Directory-Literal.htm)) <br> **-** Patrón específico ([comodín de directorio de ruta de acceso URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Directory-Wildcard.htm)) |
| Extensión de ruta de acceso URL | Identifica las solicitudes en las que su extensión de archivo coincide con un: <br> **-** Valor específico ([literal de extensión de ruta de acceso URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Extension-Literal.htm)) <br> **-** Patrón específico ([carácter comodín de directorio de extensión de ruta de acceso URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Extension-Wildcard.htm)) |
| Nombre de archivo de ruta de acceso URL | Identifica las solicitudes en las que su nombre de archivo coincide con un: <br> **-** Valor específico ([literal de nombre de archivo de ruta de acceso URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Filename-Literal.htm)) <br> **-** Patrón específico ([carácter comodín de nombre de archivo de ruta de acceso URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Filename-Wildcard.htm)) |
| Consulta URL | Identifica las solicitudes en las que su cadena de consulta coincide con un: <br> **-** Valor específico ([literal de consulta URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Literal.htm)) <br> **-** Expresión regular ([expresión regular de consulta URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Regex.htm)) <br> **-** Patrón específico ([carácter comodín de consulta URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Wildcard.htm)) |
| Parámetro de consulta de dirección URL | Identifica las solicitudes en las que su parámetro de cadena de consulta se ha establecido en un valor que coincide con un: <br> **-** Valor específico ([literal de parámetro de consulta URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Parameter-Literal.htm)) <br> **-** Patrón específico ([carácter comodín de parámetro de consulta URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Parameter-Wildcard.htm)) |

**[Volver al principio](#top)**

Para ver las condiciones de coincidencia más recientes, consulte la [documentación del motor de reglas de Verizon](https://docs.vdms.com/cdn/index.html#Quick_References/HRE_QR.htm#Conditio).

## <a name="next-steps"></a>Pasos siguientes

- [Introducción a Azure Content Delivery Network](cdn-overview.md)
- [Referencia del motor de reglas](cdn-verizon-premium-rules-engine-reference.md)
- [Expresiones condicionales del motor de reglas](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Características del motor de reglas](cdn-verizon-premium-rules-engine-reference-features.md)
- [Invalidación del comportamiento HTTP predeterminado mediante el motor de reglas](cdn-verizon-premium-rules-engine.md)

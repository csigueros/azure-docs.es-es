---
title: Condiciones de coincidencia en el motor de reglas estándar de Azure CDN | Microsoft Docs
description: Documentación de referencia para las condiciones de coincidencia del motor de reglas estándar de Azure Content Delivery Network (Azure CDN).
services: cdn
author: duongau
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: duau
ms.openlocfilehash: bd0c6b0578fc301c38e96b654a281eb025c54dc5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131469927"
---
# <a name="match-conditions-in-the-standard-rules-engine-for-azure-cdn"></a>Condiciones de coincidencia en el motor de reglas estándar de Azure CDN

En el [motor de reglas estándar](cdn-standard-rules-engine.md) de Azure Content Delivery Network (Azure CDN), una regla consta de una o varias condiciones de coincidencia y una acción. En este artículo se muestran descripciones detalladas de las condiciones de coincidencia que puede usar en el motor de reglas estándar de Azure CDN.

La primera parte de una regla es una condición de coincidencia o un conjunto de condiciones de coincidencia. En el motor de reglas estándar de Azure CDN, cada regla puede tener hasta cuatro condiciones de coincidencia. Una condición de coincidencia identifica tipos específicos de solicitudes para los que se realizan las acciones definidas. Si usa varias condiciones de coincidencia, estas se agruparán mediante la lógica AND.

Por ejemplo, puede usar una condición de coincidencia para:

- Filtrar las solicitudes en función de una dirección IP específica, un país o una región.
- Filtrar las solicitudes por la información de encabezado.
- Filtrar las solicitudes de los dispositivos móviles o de escritorio.

## <a name="match-conditions"></a>Condiciones de coincidencia

Las siguientes condiciones de coincidencia están disponibles para su uso en el motor de reglas estándar de Azure CDN. 

### <a name="device-type"></a>Tipo de dispositivo 

Identifica las solicitudes realizadas desde un dispositivo móvil o un dispositivo de escritorio.  

#### <a name="required-fields"></a>Campos obligatorios

Operator | Valores admitidos
---------|----------------
Es igual a, No es igual a | Móvil, Escritorio

### <a name="http-version"></a>Versión de HTTP

Identifica las solicitudes en función de la versión de HTTP de la solicitud.

#### <a name="required-fields"></a>Campos obligatorios

Operator | Valores admitidos
---------|----------------
Es igual a, No es igual a | 2.0, 1.1, 1.0, 0.9, Todas

### <a name="request-cookies"></a>Cookies de solicitud

Identifica las solicitudes en función de la información de las cookies de la solicitud entrante.

#### <a name="required-fields"></a>Campos obligatorios

Nombre de la cookie | Operator | Valor de la cookie | Transformación de mayúsculas y minúsculas
------------|----------|--------------|---------------
String | [Lista de operadores estándar](#standard-operator-list) | Cadena, Int | Sin transformación, en mayúsculas, en minúsculas

#### <a name="key-information"></a>Información importante

- No se pueden usar valores de carácter comodín (incluidos los asteriscos [\*]) cuando se especifica un nombre de cookie; debe usar un nombre de cookie exacto.
- Solo se puede especificar un nombre de cookie por instancia de esta condición de coincidencia.
- Las comparaciones de nombres de cookie distinguen entre mayúsculas y minúsculas.
- Para especificar varios valores de cookies, utilice un único espacio entre cada valor de cookie. 
- Los valores de cookie pueden aprovechar los valores de carácter comodín.
- Si no se ha especificado un valor de carácter comodín, solo una coincidencia exacta cumplirá esta condición de coincidencia. Por ejemplo, "Value" coincidirá con "Value", pero no con "Value1". 

### <a name="post-argument"></a>Argumento POST

Identifica las solicitudes en función de los argumentos definidos para el método POST que se usa en la solicitud. 

#### <a name="required-fields"></a>Campos obligatorios

Nombre de argumento | Operator | Valor de argumento | Transformación de mayúsculas y minúsculas
--------------|----------|----------------|---------------
String | [Lista de operadores estándar](#standard-operator-list) | Cadena, Int | Sin transformación, en mayúsculas, en minúsculas

### <a name="query-string"></a>Cadena de consulta

Identifica las solicitudes que contienen el parámetro de cadena de consulta especificado. Este parámetro se establece en un valor que coincide con un patrón específico. Los parámetros de cadena de consulta (por ejemplo, **parameter=value**) de la dirección URL de la solicitud determinan si se cumple esta condición. Esta condición de coincidencia identifica un parámetro de cadena de consulta por su nombre y acepta uno o varios valores para el valor del parámetro.

#### <a name="required-fields"></a>Campos obligatorios

Operator | Cadena de consulta | Transformación de caso
---------|--------------|---------------
[Lista de operadores estándar](#standard-operator-list) | Cadena, Int | Sin transformación, en mayúsculas, en minúsculas

### <a name="remote-address"></a>Dirección remota

Identifica las solicitudes en función de la ubicación o la dirección IP del solicitante.

#### <a name="required-fields"></a>Campos obligatorios

Operator | Valores admitidos
---------|-----------------
Any | N/D
Coincidencia geográfica | Código de país
Coincidencia de IP | Dirección IP (separada por espacios)
Ninguna | N/D
No tiene ninguna coincidencia geográfica | Código de país
No tiene ninguna coincidencia de IP | Dirección IP (separada por espacios)

#### <a name="key-information"></a>Información importante

- Utilice la notación CIDR.
- Para especificar varias direcciones IP y bloques de direcciones IP, utilice un solo espacio entre los valores:
  - **Ejemplo IPv4**: *1.2.3.4 10.20.30.40* coincide con las solicitudes que llegan desde la dirección 1.2.3.4 o la 10.20.30.40.
  - **Ejemplo IPv6**: *1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80* coincide con las solicitudes que llegan desde la dirección 1:2:3:4:5:6:7:8 o la 10:20:30:40:50:60:70:80.
- La sintaxis de un bloque de direcciones IP es la dirección IP de base seguida por una barra diagonal y el tamaño del prefijo. Por ejemplo:
  - **Ejemplo IPv4**: *5.5.5.64/26* coincide con las solicitudes que llegan desde la dirección 5.5.5.64 hasta la 5.5.5.127.
  - **Ejemplo IPv6**: *1:2:3:/48* coincide con las solicitudes que llegan desde la dirección 1:2:3:0:0:0:0:0 hasta la 1:2:3:ffff:ffff:ffff:ffff:ffff.
- Dirección remota representa la dirección IP del cliente original que es de la conexión de red o normalmente el encabezado de solicitud X-Forwarded-For si el usuario está detrás de un proxy.

### <a name="request-body"></a>Cuerpo de la solicitud

Identifica las solicitudes en función del texto específico que aparece en el cuerpo de la solicitud.

#### <a name="required-fields"></a>Campos obligatorios

Operator | Cuerpo de la solicitud | Transformación de mayúsculas y minúsculas
---------|--------------|---------------
[Lista de operadores estándar](#standard-operator-list) | Cadena, Int | Sin transformación, en mayúsculas, en minúsculas

### <a name="request-header"></a>Encabezado de solicitud

Identifica las solicitudes que usan un encabezado específico en la solicitud.

#### <a name="required-fields"></a>Campos obligatorios

Nombre de encabezado | Operator | Valor de encabezado | Transformación de mayúsculas y minúsculas
------------|----------|--------------|---------------
String | [Lista de operadores estándar](#standard-operator-list) | Cadena, Int | Sin transformación, en mayúsculas, en minúsculas

### <a name="request-method"></a>Método de solicitud

Identifica las solicitudes que usan el método de solicitud especificado.

#### <a name="required-fields"></a>Campos obligatorios

Operator | Valores admitidos
---------|----------------
Es igual a, No es igual a | GET, POST, PUT, DELETE, HEAD, OPTIONS, TRACE

#### <a name="key-information"></a>Información importante

- Solo el método de solicitud GET puede generar contenido almacenado en caché en Azure CDN. Los demás métodos de solicitud los procesa el proxy mediante la red. 

### <a name="request-protocol"></a>Protocolo de solicitud

Identifica las solicitudes que usan el protocolo especificado.

#### <a name="required-fields"></a>Campos obligatorios

Operator | Valores admitidos
---------|----------------
Es igual a, No es igual a | HTTP, HTTPS

### <a name="request-url"></a>URL de la solicitud

Identifica las solicitudes que coinciden con la dirección URL especificada.

#### <a name="required-fields"></a>Campos obligatorios

Operator | URL de la solicitud | Transformación de mayúsculas y minúsculas
---------|-------------|---------------
[Lista de operadores estándar](#standard-operator-list) | Cadena, Int | Sin transformación, en mayúsculas, en minúsculas

#### <a name="key-information"></a>Información importante

- Cuando use esta condición de regla, asegúrese de incluir la información del protocolo. Por ejemplo, *https://www.\<yourdomain\>.com*.

### <a name="url-file-extension"></a>Extensión de archivo de la URL

Identifica las solicitudes que incluyen la extensión de archivo especificada en el nombre de archivo de la dirección URL de la solicitud.

#### <a name="required-fields"></a>Campos obligatorios

Operator | Extensión | Transformación de mayúsculas y minúsculas
---------|-----------|---------------
[Lista de operadores estándar](#standard-operator-list) | Cadena, Int | Sin transformación, en mayúsculas, en minúsculas

#### <a name="key-information"></a>Información importante

- En el caso de la extensión, no incluya un punto inicial; por ejemplo, utilice *html* en lugar de *.html*.

### <a name="url-file-name"></a>Nombre de archivo de la URL

Identifica las solicitudes que incluyen el nombre de archivo especificado en la dirección URL de la solicitud.

#### <a name="required-fields"></a>Campos obligatorios

Operator | Nombre de archivo | Transformación de mayúsculas y minúsculas
---------|-----------|---------------
[Lista de operadores estándar](#standard-operator-list) | Cadena, Int | Sin transformación, en mayúsculas, en minúsculas

#### <a name="key-information"></a>Información importante

- Puede especificar varios nombres de archivo delimitando cada nombre de archivo con un único espacio. 

### <a name="url-path"></a>Ruta de acceso URL

Identifica las solicitudes que incluyen la ruta de acceso especificada en la dirección URL de la solicitud.

#### <a name="required-fields"></a>Campos obligatorios

Operator | Value | Transformación de caso
---------|-------|---------------
[Lista de operadores estándar](#standard-operator-list) | Cadena, Int | Sin transformación, en mayúsculas, en minúsculas

#### <a name="key-information"></a>Información importante

- Un valor de nombre de archivo puede aprovechar los valores de carácter comodín. Por ejemplo, cada patrón de nombre de archivo puede contener uno o varios asteriscos (*), donde cada asterisco coincide con una secuencia de uno o más caracteres.

## <a name="reference-for-rules-engine-match-conditions"></a>Referencia de las condiciones de coincidencia del motor de reglas

### <a name="standard-operator-list"></a>Lista de operadores estándar

En el caso de las reglas que aceptan valores de la lista de operadores estándar, son válidos los siguientes operadores:

- Any
- Equals 
- Contains 
- Empieza por 
- Termina por 
- Menor que
- Menor o igual que
- Mayor que
- Mayor o igual que
- Ninguno
- No contiene
- No empieza por 
- No termina con 
- No es menor que
- No es menor o igual que
- No es mayor que
- No es mayor o igual que

En el caso de los operadores numéricos, como *Menor que* o *Mayor o igual que*, la comparación usada se basa en la longitud. En este caso, el valor de la condición de coincidencia debe ser un entero, igual a la longitud que quiere comparar. 

## <a name="next-steps"></a>Pasos siguientes

- [Información general de Azure CDN](cdn-overview.md)
- [Referencia del motor de reglas estándar](cdn-standard-rules-engine-reference.md)
- [Acciones en el motor de reglas estándar](cdn-standard-rules-engine-actions.md)
- [Aplicación de HTTPS mediante el motor de reglas estándar](cdn-standard-rules-engine.md)

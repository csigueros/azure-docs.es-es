---
title: Método Transliterate de Translator
titleSuffix: Azure Cognitive Services
description: Convierta texto en un idioma de un script a otro con el método Transliterate de Translator.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: lajanuar
ms.openlocfilehash: 11574542116fe90629f84fc572f404a1b42b078b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121730194"
---
# <a name="translator-30-transliterate"></a>Translator 3.0: Transliterar

Convierte el texto en un idioma de un script a otro.

## <a name="request-url"></a>URL de la solicitud

Envíe una solicitud `POST` a:

```HTTP
https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0
```

## <a name="request-parameters"></a>Parámetros de solicitud

Los parámetros de solicitud que se pasaron en la cadena de consulta son:

| Parámetro de consulta | Descripción |
| --- | --- |
| api-version | *Parámetro obligatorio*.<br/>Versión de la API que el cliente solicitó. El valor debe ser `3.0`. |
| language | *Parámetro obligatorio*.<br/>Especifica el idioma del texto que se va a convertir de un script a otro. Los idiomas posibles se enumeran en el ámbito `transliteration` obtenido al consultar el servicio de sus [idiomas admitidos](./v3-0-languages.md). |
| fromScript | *Parámetro obligatorio*.<br/>Especifica el script que usa el texto de entrada. Busque los [idiomas admitidos](./v3-0-languages.md) con el ámbito `transliteration`, para ver los scripts de entrada disponibles para el idioma seleccionado. |
| toScript | *Parámetro obligatorio*.<br/>Especifica el nombre del script de salida. Busque los [idiomas admitidos](./v3-0-languages.md) con el ámbito `transliteration`, para ver los scripts de salida disponibles para la combinación de idiomas y el script de entrada seleccionados. |

Los encabezados de solicitud incluyen lo siguiente:

| encabezados | Descripción |
| --- | --- |
| Encabezados de autenticación | <em>Encabezado de solicitud obligatorio</em>.<br/>Consulte las [opciones disponibles para la autenticación](./v3-0-reference.md#authentication). |
| Content-Type | *Encabezado de solicitud obligatorio*.<br/>Especifica el tipo de contenido de la carga. Los valores posibles son: `application/json` |
| Content-Length | *Encabezado de solicitud obligatorio*.<br/>Longitud del cuerpo de la solicitud. |
| X-ClientTraceId | *Opcional*.<br/>GUID generado por el cliente para identificar de forma única la solicitud. Puede omitir este encabezado si incluye el id. de seguimiento en la cadena de la consulta mediante un parámetro de consulta denominado `ClientTraceId`. |

## <a name="request-body"></a>Cuerpo de la solicitud

El cuerpo de la solicitud es una matriz JSON. Cada elemento de la matriz es un objeto JSON con una propiedad de cadena denominada `Text`, que representa la cadena que se va a convertir.

```json
[
    {"Text":"こんにちは"},
    {"Text":"さようなら"}
]
```

Se aplican las siguientes limitaciones:

* La matriz puede tener como máximo 10 elementos.
* El valor de texto de un elemento de la matriz no puede superar los 1 000 caracteres, incluyendo los espacios.
* El texto completo incluido en la solicitud no puede superar los 5000 caracteres, incluidos los espacios.

## <a name="response-body"></a>Response body

Una respuesta correcta es una matriz JSON, que cuenta con un resultado para cada elemento en la matriz de entrada. Un objeto del resultado incluye las siguientes propiedades:

  * `text`: cadena que es el resultado de convertir la cadena de entrada en el script de salida.
  
  * `script`: cadena que especifica el script que se usa en la salida.

Un ejemplo de respuesta JSON es:

```json
[
    {"text":"konnnichiha","script":"Latn"},
    {"text":"sayounara","script":"Latn"}
]
```

## <a name="response-headers"></a>Encabezados de respuesta

| encabezados | Descripción |
| --- | --- |
| X-RequestId | Valor generado por el servicio para identificar la solicitud. Se usa para solucionar problemas. |

## <a name="response-status-codes"></a>Códigos de estado de respuesta

A continuación se indican los códigos de estado HTTP posibles que devuelve una solicitud. 

| Código de estado | Descripción |
| --- | --- |
| 200 | Correcto. |
| 400 | Uno de los parámetros de consulta falta o no es válido. Corrija los parámetros de la solicitud antes de volver a intentarlo. |
| 401 | No pudo autenticarse la solicitud. Compruebe que las credenciales que se especificaron sean correctas. |
| 403 | La solicitud no está autenticada. Compruebe los detalles del mensaje de error. Esto a menudo indica que todas las traducciones gratuitas que proporciona la suscripción de prueba se han agotado. |
| 429 | El servidor rechazó la solicitud porque el cliente superó los límites de solicitudes. |
| 500 | Se ha producido un error inesperado. Si el error continúa, notifíquelo con: fecha y hora del error, identificador de la solicitud del encabezado de respuesta `X-RequestId` e identificador de cliente del encabezado de solicitud `X-ClientTraceId`. |
| 503 | Servidor no disponible temporalmente. Vuelva a intentarlo. Si el error continúa, notifíquelo con: fecha y hora del error, identificador de la solicitud del encabezado de respuesta `X-RequestId` e identificador de cliente del encabezado de solicitud `X-ClientTraceId`. |

Si se produce un error, la solicitud también devuelve una respuesta de error JSON. El código de error es un número de 6 dígitos que combina el código de estado HTTP de 3 dígitos y otro número de 3 dígitos que ayuda a categorizar aún más el error. En la [página de referencia de Traductor v3](./v3-0-reference.md#errors) pueden encontrarse los códigos de error comunes. 

## <a name="examples"></a>Ejemplos

En el ejemplo siguiente se muestra cómo convertir dos cadenas escritas en japonés a japonés escrito con el alfabeto latino.

Esta es la carga de JSON para la solicitud en este ejemplo:

```json
[{"text":"こんにちは","script":"jpan"},{"text":"さようなら","script":"jpan"}]
```

Si está usando cURL en una ventana de línea de comandos que no admite caracteres Unicode, use la siguiente carga JSON y guárdela en un archivo denominado `request.txt`. Asegúrese de guardar el archivo con una codificación `UTF-8`.

```
curl -X POST "https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0&language=ja&fromScript=Jpan&toScript=Latn" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d @request.txt
```

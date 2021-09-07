---
title: Método Detect de Translator
titleSuffix: Azure Cognitive Services
description: Identifique el idioma de un fragmento de texto con el método Detect de Translator de Azure Cognitive Services.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: lajanuar
ms.openlocfilehash: 36691229ebe59a6b3b5dd59776b6af41e326a3c2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121749999"
---
# <a name="translator-30-detect"></a>Translator 3.0: Detect

Identifica el idioma de un fragmento de texto.

## <a name="request-url"></a>URL de la solicitud

Envíe una solicitud `POST` a:

```HTTP
https://api.cognitive.microsofttranslator.com/detect?api-version=3.0
```

## <a name="request-parameters"></a>Parámetros de solicitud

Los parámetros de solicitud que se pasaron en la cadena de consulta son:

| Parámetro de consulta | Descripción |
| --- | --- |
| api-version | *Parámetro obligatorio*.<br/>Versión de la API que el cliente solicitó. El valor debe ser `3.0`. |

Los encabezados de solicitud incluyen lo siguiente:

| encabezados | Descripción |
| --- | --- |
| Encabezados de autenticación | <em>Encabezado de solicitud obligatorio</em>.<br/>Consulte las [opciones disponibles para la autenticación](./v3-0-reference.md#authentication)</a>. |
| Content-Type | *Encabezado de solicitud obligatorio*.<br/>Especifica el tipo de contenido de la carga. Los valores posibles son: `application/json`. |
| Content-Length | *Encabezado de solicitud obligatorio*.<br/>Longitud del cuerpo de la solicitud. |
| X-ClientTraceId | *Opcional*.<br/>GUID generado por el cliente para identificar de forma única la solicitud. Puede omitir este encabezado si incluye el id. de seguimiento en la cadena de la consulta mediante un parámetro de consulta denominado `ClientTraceId`. |

## <a name="request-body"></a>Cuerpo de la solicitud

El cuerpo de la solicitud es una matriz JSON. Cada elemento de la matriz es un objeto JSON con una propiedad de cadena denominada `Text`. La detección de idioma se aplica al valor de la propiedad `Text`. La detección automática de idioma funciona mejor con texto de entrada más largo. Un cuerpo de solicitud de muestra tiene el siguiente aspecto:

```json
[
    { "Text": "Ich würde wirklich gerne Ihr Auto ein paar Mal um den Block fahren." }
]
```

Se aplican las siguientes limitaciones:

* La matriz puede tener como máximo 100 elementos.
* El texto completo incluido en la solicitud no puede superar los 50 000 caracteres, incluidos los espacios.

## <a name="response-body"></a>Response body

Una respuesta correcta es una matriz JSON con un resultado para cada cadena en la matriz de entrada. Un objeto del resultado incluye las siguientes propiedades:

  * `language`: Código del idioma detectado.

  * `score`: valor flotante que indica la confianza en el resultado. La puntuación varía entre cero y uno, y una puntuación baja indica una confianza baja.

  * `isTranslationSupported`: Valor booleano que tiene el valor "true" si el idioma detectado es uno de los idiomas admitidos para la traducción de texto.

  * `isTransliterationSupported`: Valor booleano que tiene el valor "true" si el idioma detectado es uno de los idiomas admitidos para la transliteración.
  
  * `alternatives`: Matriz de otros idiomas posibles. Cada elemento de la matriz es otro objeto con las mismas propiedades enumeradas anteriormente: `language`, `score`, `isTranslationSupported` y `isTransliterationSupported`.

Un ejemplo de respuesta JSON es:

```json
[

    {

        "language": "de",

        "score": 1.0,

        "isTranslationSupported": true,

        "isTransliterationSupported": false

    }

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

Si se produce un error, la solicitud también devolverá una respuesta de error JSON. El código de error es un número de 6 dígitos que combina el código de estado HTTP de 3 dígitos y otro número de 3 dígitos que ayuda a categorizar aún más el error. En la [página de referencia de Traductor v3](./v3-0-reference.md#errors) pueden encontrarse los códigos de error comunes. 

## <a name="examples"></a>Ejemplos

En el ejemplo siguiente se muestra cómo recuperar los idiomas admitidos para la traducción de texto.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/detect?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'What language is this text written in?'}]"
```

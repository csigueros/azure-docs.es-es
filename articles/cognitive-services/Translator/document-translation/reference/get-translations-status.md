---
title: Obtención del estado de las traducciones
titleSuffix: Azure Cognitive Services
description: El método de obtención del estado de las traducciones devuelve una lista de solicitudes por lotes enviadas y el estado de cada solicitud.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 06/22/2021
ms.author: v-jansk
ms.openlocfilehash: 5ed4c565ad784bb50ebbc464d4229bfcabb7a5d7
ms.sourcegitcommit: 5fabdc2ee2eb0bd5b588411f922ec58bc0d45962
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/23/2021
ms.locfileid: "112540706"
---
# <a name="get-translations-status"></a>Obtención del estado de las traducciones

El método de obtención del estado de las traducciones devuelve una lista de solicitudes por lotes enviadas y el estado de cada solicitud. Esta lista solo contiene las solicitudes por lotes enviadas por el usuario (en función del recurso).

Si el número de solicitudes supera el límite de paginación, se usa la paginación en el servidor. Las respuestas paginadas indican un resultado parcial e incluyen un token de continuación en la respuesta. La ausencia de un token de continuación significa que no hay otras páginas disponibles.

Los parámetros de consulta $top, $skip y $maxpagesize se pueden usar para especificar el número de resultados que se van a devolver y el desplazamiento en la colección.

$top indica el número total de registros que el usuario desea que se devuelvan entre todas las páginas. $skip indica el número de registros que se omitirán de la lista de lotes según el método de ordenación especificado. De manera predeterminada, se ordena por hora de inicio descendente. $maxpagesize es el número máximo de elementos que se devuelven en una página. Si se solicitan más elementos mediante $top (o no se especifica $top y hay más elementos que devolver), @nextLink contendrá el vínculo a la página siguiente.

El parámetro de consulta $orderBy se puede usar para ordenar la lista devuelta (por ejemplo, "$orderBy=createdDateTimeUtc asc" o "$orderBy=createdDateTimeUtc desc"). La ordenación predeterminada es por createdDateTimeUtc descendente. Se pueden usar algunos parámetros de consulta para filtrar la lista devuelta (por ejemplo, "status=Succeeded,Cancelled") solo devolverá operaciones correctas y canceladas. createdDateTimeUtcStart y createdDateTimeUtcEnd se pueden usar combinados o por separado para especificar un intervalo de fecha y hora por el que filtrar la lista devuelta. Los parámetros de consulta de filtrado admitidos son (status, ID, createdDateTimeUtcStart y createdDateTimeUtcEnd).

El servidor respeta los valores especificados por el cliente. Sin embargo, los clientes deben estar preparados para controlar las respuestas que contienen un tamaño de página diferente o que contienen un token de continuación.

Cuando se incluyen $top y $skip, el servidor debe aplicar primero $skip y, a continuación, $top en la colección. 

> [!NOTE]
> Si el servidor no puede aceptar $top o $skip, el servidor debe devolver un error al cliente que informa sobre él en lugar de simplemente omitir las opciones de consulta. Esto reduce el riesgo de que el cliente realice suposiciones sobre los datos devueltos.

## <a name="request-url"></a>URL de la solicitud

Envíe una solicitud `GET` a:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0/batches
```

Aprenda a encontrar su [nombre de dominio personalizado](../get-started-with-document-translation.md#find-your-custom-domain-name).

> [!IMPORTANT]
>
> * **Todas las solicitudes de API al servicio de traducción de documentos requieren un punto de conexión de dominio personalizado**.
> * No usará el punto de conexión que se encuentra en la página _Claves y punto de conexión_ del recurso en Azure Portal, ni el punto de conexión global del traductor (`api.cognitive.microsofttranslator.com`) para realizar solicitudes HTTP de traducción de documentos.

## <a name="request-parameters"></a>Parámetros de solicitud

Los parámetros de solicitud que se pasaron en la cadena de consulta son:

|Parámetro de consulta|En|Obligatorio|Tipo|Descripción|
|--- |--- |--- |---|---|
|$maxpagesize|Query|Falso|integer int32|$maxpagesize es el número máximo de elementos que se devuelven en una página. Si se solicitan más elementos mediante $top (o no se especifica $top y hay más elementos que devolver), @nextLink contendrá el vínculo a la página siguiente. Los clientes PUEDEN solicitar la paginación controlada por el servidor con un tamaño de página específico especificando su preferencia para $maxpagesize. El servidor DEBE respetar esta preferencia si el tamaño de página especificado es menor que el tamaño de página predeterminado del servidor.|
|$orderBy|Query|Falso|array|Consulta de ordenación de la colección (por ejemplo, "CreatedDateTimeUtc asc", "CreatedDateTimeUtc desc")|
|$skip|Query|Falso|integer int32|$skip indica el número de registros que se omitirán de la lista de registros mantenida por el servidor según el método de ordenación especificado. De manera predeterminada, se ordena por hora de inicio descendente. Los clientes pueden usar los parámetros de consulta $top y $skip para especificar el número de resultados que se van a devolver y el desplazamiento en la colección. Cuando el cliente proporciona $top y $skip, el servidor DEBE aplicar primero $skip y, a continuación, $top en la colección. Nota: Si el servidor no puede respetar $top o $skip, el servidor DEBE devolver un error al cliente que informe al respecto en lugar de simplemente omitir las opciones de consulta.|
|$top|Query|Falso|integer int32|$top indica el número total de registros que el usuario desea que se devuelvan entre todas las páginas. Los clientes pueden usar los parámetros de consulta $top y $skip para especificar el número de resultados que se van a devolver y el desplazamiento en la colección. Cuando el cliente proporciona $top y $skip, el servidor DEBE aplicar primero $skip y, a continuación, $top en la colección. Nota: Si el servidor no puede respetar $top o $skip, el servidor DEBE devolver un error al cliente que informe al respecto en lugar de simplemente omitir las opciones de consulta.|
|createdDateTimeUtcEnd|Query|Falso|string date-time|Fecha y hora de finalización para obtener los elementos anteriores.|
|createdDateTimeUtcStart|Query|Falso|string date-time|Fecha y hora de inicio después de la cual se obtienen los elementos.|
|ids|Query|Falso|array|Los ID que se usarán en el filtrado.|
|statuses|Query|Falso|array|Estados que se usarán en el filtrado.|

## <a name="request-headers"></a>Encabezados de solicitud

Los encabezados de solicitud son:

|encabezados|Descripción|
|--- |--- |
|Ocp-Apim-Subscription-Key|Encabezado de solicitud obligatorio|

## <a name="response-status-codes"></a>Códigos de estado de respuesta

A continuación se indican los códigos de estado HTTP posibles que devuelve una solicitud.

|Código de estado|Descripción|
|--- |--- |
|200|Aceptar. Solicitud correcta y devuelve el estado de todas las operaciones. HeadersRetry-After: integerETag: string|
|400|Solicitud incorrecta. Solicitud no válida. Compruebe los parámetros de entrada.|
|401|No autorizado. Compruebe sus credenciales.|
|500|Error interno del servidor.|
|Otros códigos de estado|<ul><li>Demasiadas solicitudes</li><li>Servidor temporalmente no disponible</li></ul>|

## <a name="get-translations-status-response"></a>Respuesta de obtención del estado de las traducciones

### <a name="successful-get-translations-status-response"></a>Respuesta correcta de obtención del estado de las traducciones

En una respuesta correcta se devuelve la información siguiente.

|Nombre|Tipo|Descripción|
|--- |--- |--- |
|@nextLink|string|Dirección URL de la página siguiente Valor NULL si no hay más páginas disponibles|
|value|TranslationStatus[]|Matriz TranslationStatus[] que se muestra a continuación|
|value.id|string|Identificador de la operación.|
|value.createdDateTimeUtc|string|Fecha y hora de creación de la operación.|
|value.lastActionDateTimeUtc|string|Fecha y hora en que se actualizó el estado de la operación.|
|value.status|String|Lista de posibles estados del trabajo o documento: <ul><li>Canceled</li><li>Cancelling</li><li>Con error</li><li>NotStarted</li><li>En ejecución</li><li>Correcto</li><li>ValidationFailed</li></ul>|
|value.summary|StatusSummary[]|Resumen que contiene los detalles que se muestran a continuación.|
|value.summary.total|integer|Recuento total de documentos.|
|value.summary.failed|integer|Recuento de documentos con errores.|
|value.summary.success|integer|Recuento de documentos traducidos correctamente.|
|value.summary.inProgress|integer|Recuento de documentos en curso.|
|value.summary.notYetStarted|integer|Recuento de documentos que aún no se han empezado a procesar.|
|value.summary.cancelled|integer|Recuento de documentos cancelados.|
|value.summary.totalCharacterCharged|integer|Recuento total de caracteres cargados.|

### <a name="error-response"></a>Respuesta de error

|Nombre|Tipo|Descripción|
|--- |--- |--- |
|código|string|Enumeraciones que contiene códigos de error de alto nivel. Valores posibles:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>No autorizado</li></ul>|
|message|string|Obtiene un mensaje de error de alto nivel.|
|Destino|string|Obtiene el origen del error. Por ejemplo, sería "documentos" o "id. de documento" si se trata de un documento no válido.|
|innerError|InnerTranslationError|Nuevo formato de error interno, que cumple las directrices de Cognitive Services API. Contiene las propiedades necesarias ErrorCode, message y las propiedades opcionales, target, details (par clave-valor), innerError (se puede anidar).|
|innerError.code|string|Obtiene la cadena de error de código.|
|innerError.message|string|Obtiene un mensaje de error de alto nivel.|
|innerError.target|string|Obtiene el origen del error. Por ejemplo, sería "documentos" o "id. de documento" si se trata de un documento no válido.|

## <a name="examples"></a>Ejemplos

### <a name="example-successful-response"></a>Ejemplo de respuesta correcta

Aquí se muestra un ejemplo de respuesta correcta.

```JSON
{
    "value": [
        {
            "id": "36724748-f7a0-4db7-b7fd-f041ddc75033",
            "createdDateTimeUtc": "2021-06-18T03:35:30.153374Z",
            "lastActionDateTimeUtc": "2021-06-18T03:36:44.6155316Z",
            "status": "Succeeded",
            "summary": {
                "total": 3,
                "failed": 2,
                "success": 1,
                "inProgress": 0,
                "notYetStarted": 0,
                "cancelled": 0,
                "totalCharacterCharged": 0
            }
        },
        {
            "id": "1c7399a7-6913-4f20-bb43-e2fe2ba1a67d",
            "createdDateTimeUtc": "2021-05-24T17:57:43.8356624Z",
            "lastActionDateTimeUtc": "2021-05-24T17:57:47.128391Z",
            "status": "Failed",
            "summary": {
                "total": 1,
                "failed": 1,
                "success": 0,
                "inProgress": 0,
                "notYetStarted": 0,
                "cancelled": 0,
                "totalCharacterCharged": 0
            }
        },
        {
            "id": "daa2a646-4237-4f5f-9a48-d515c2d9af3c",
            "createdDateTimeUtc": "2021-04-14T19:49:26.988272Z",
            "lastActionDateTimeUtc": "2021-04-14T19:49:43.9818634Z",
            "status": "Succeeded",
            "summary": {
                "total": 2,
                "failed": 0,
                "success": 2,
                "inProgress": 0,
                "notYetStarted": 0,
                "cancelled": 0,
                "totalCharacterCharged": 21899
            }
        }
    ],
    ""@nextLink": "https://westus.cognitiveservices.azure.com/translator/text/batch/v1.0/operations/727BF148-F327-47A0-9481-ABAE6362F11E/documents?$top=5&$skip=15"
}

```

### <a name="example-error-response"></a>Ejemplo de respuesta con error

A continuación se presenta un ejemplo de una respuesta con error. El esquema de otros códigos de error es el mismo.

Código de estado: 500

```JSON
{
  "error": {
    "code": "InternalServerError",
    "message": "Internal Server Error",
    "target": "Operation",
    "innerError": {
      "code": "InternalServerError",
      "message": "Unexpected internal server error has occurred"
    }
  }
}
```

## <a name="next-steps"></a>Pasos siguientes

Siga nuestro inicio rápido para obtener más información sobre el uso de Traducción de documentos y la biblioteca cliente.

> [!div class="nextstepaction"]
> [Introducción a la traducción de documentos](../get-started-with-document-translation.md)

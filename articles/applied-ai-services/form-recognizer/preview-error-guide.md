---
title: 'Referencia: Errores de Form Recognizer (2021-09-30-preview)'
titleSuffix: Azure Applied AI Services
description: Obtenga información sobre cómo se representan los errores en Form Recognizer y consulte una lista de los posibles errores que devuelve el servicio.
author: paulhsu
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: reference
ms.date: 09/30/2021
ms.author: paulhsu
ms.custom: cog-serv-seo-aug-2020
keywords: procesamiento de documentos
ms.openlocfilehash: a67c98189198cf8df0f8f7e6fa88c9a552812bfa
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2021
ms.locfileid: "129730572"
---
# <a name="form-recognizer-error-guide-v30-preview"></a>Guía de errores de Form Recognizer v3.0 (versión preliminar)

Form Recognizer usa un diseño unificado para representar todos los errores detectados en API de REST.  Cada vez que una operación de API devuelve un código de estado 4xx o 5xx, se devuelve información adicional sobre el error en el cuerpo JSON de la respuesta, como se indica a continuación:

```json
{
  "error": {
    "code": "InvalidRequest",
    "message": "Invalid request.",
    "innererror": {
      "code": "InvalidContent",
      "message": "The file format is unsupported or corrupted. Refer to documentation for the list of supported formats."
    }
  }
}
```

En el caso de operaciones de larga ejecución en las que se puedan detectar varios errores, el código de error de nivel superior se establece en el error más grave, y cada error se enumera con la propiedad *error.details*.  En estos escenarios, la propiedad *target* de cada error especifica el desencadenador.

```json
{
    "status": "failed",
    "createdDateTime": "2021-07-14T10:17:51Z",
    "lastUpdatedDateTime": "2021-07-14T10:17:51Z",
    "error": {
        "code": "InternalServerError",
        "message": "An unexpected error occurred.",
        "details": [
            {
                "code": "InternalServerError",
                "message": "An unexpected error occurred."
            },
            {
                "code": "InvalidContentDimensions",
                "message": "The input image dimensions are out of range. Refer to documentation for supported image dimensions.",
                "target": "2"
            }
        ]
    }
}
```

La propiedad *error.code* de nivel superior puede ser uno de los siguientes mensajes de código de error:

| Código de error           | Mensaje                                                | Estado HTTP |
| -------------------- | ------------------------------------------------------ | ----------- |
| InvalidRequest       | Solicitud no válida.                                       | 400         |
| InvalidArgument      | Argumento no válido.                                      | 400         |
| Prohibido            | Acceso prohibido debido a una directiva u otra configuración. | 403         |
| NotFound             | Recurso no encontrado.                                    | 404         |
| MethodNotAllowed     | No se admite el método HTTP solicitado.              | 405         |
| Conflicto             | La solicitud no se ha podido completar debido a un conflicto.  | 409         |
| UnsupportedMediaType | No se admite el tipo de contenido de la solicitud.                 | 415         |
| InternalServerError  | Se ha producido un error inesperado.                          | 500         |
| ServiceUnavailable   | Se ha producido un error transitorio. Inténtelo de nuevo.      | 503         |

Siempre que es posible, se especifican más detalles en la propiedad *inner error*.

| Código de error superior | Código de error interno | Mensaje |
| -------------- | ---------------- | ------- |
| Conflicto | ModelExists | Ya existe un modelo con el nombre proporcionado. |
| Prohibido | AuthorizationFailed | Error de autorización: {details} |
| Prohibido | InvalidDataProtectionKey | La clave de protección de datos no es válida: {details} |
| Prohibido | OutboundAccessForbidden | La solicitud contiene un nombre de dominio que no está permitido por la directiva de control de acceso actual. |
| InternalServerError | Unknown | Error desconocido. |
| InvalidArgument | InvalidContentSourceFormat | Origen de contenido no válido: {details} |
| InvalidArgument | InvalidParameter | El parámetro {parameterName} no es válido: {details} |
| InvalidArgument | InvalidParameterLength | El parámetro {parameterName} no puede tener una longitud superior a {maxChars} caracteres. |
| InvalidArgument | InvalidSasToken | La firma de acceso compartido (SAS) no es válida: {details} |
| InvalidArgument | ParameterMissing | El parámetro {parameterName} no es necesario. |
| InvalidRequest | ContentSourceNotAccessible | No se puede acceder al contenido: {details} |
| InvalidRequest | ContentSourceTimeout | Se ha agotado el tiempo de espera al recibir el archivo del cliente. |
| InvalidRequest | DocumentModelLimit | La cuenta no puede crear más de {maximumModels} modelos. |
| InvalidRequest | DocumentModelLimitComposed | La cuenta no puede crear un modelo con más de {details} modelos de componentes. |
| InvalidRequest | InvalidContent | El archivo está dañado o tiene un formato no admitido. Consulte la lista de formatos admitidos en la documentación. |
| InvalidRequest | InvalidContentDimensions | Las dimensiones de la imagen de entrada están fuera del intervalo. Consulte las dimensiones de imagen admitidas en la documentación. |
| InvalidRequest | InvalidContentLength | La imagen de entrada es demasiado grande. Consulte el tamaño de archivo máximo en la documentación. |
| InvalidRequest | InvalidFieldsDefinition | Campos no válidos: {details} |
| InvalidRequest | InvalidTrainingContentLength | El contenido de entrenamiento tiene {bytes} bytes. El entrenamiento está limitado a {maxBytes} bytes. |
| InvalidRequest | InvalidTrainingContentPageCount | El contenido de entrenamiento tiene {pages} páginas. El entrenamiento está limitado a {pages} páginas. |
| InvalidRequest | ModelAnalyzeError | No se ha podido realizar el análisis con un modelo personalizado: {details} |
| InvalidRequest | ModelBuildError | No se ha podido compilar el modelo: {details} |
| InvalidRequest | ModelComposeError | No se ha podido crear el modelo: {details} |
| InvalidRequest | ModelNotReady | El modelo no está listo para la operación solicitada. Espere a que se complete el entrenamiento o compruebe si hay errores de operación. |
| InvalidRequest | ModelReadOnly | El modelo solicitado es de solo lectura. |
| InvalidRequest | NotSupportedApiVersion | La operación solicitada requiere {minimumApiVersion} o posterior. |
| InvalidRequest | OperationNotCancellable | La operación ya no se puede cancelar. |
| InvalidRequest | TrainingContentMissing | Faltan datos de entrenamiento: {details} |
| InvalidRequest | UnsupportedContent | No se admite el contenido: {details} |
| NotFound | ModelNotFound | No se ha encontrado la dirección URL solicitada. Es posible que se haya eliminado o que se siga compilando. |
| NotFound | OperationNotFound | No se ha encontrado la operación solicitada. Es posible que el identificador no sea válido o que la operación haya expirado. |
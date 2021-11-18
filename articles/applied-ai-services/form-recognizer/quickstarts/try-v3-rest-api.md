---
title: 'Inicio rápido: API REST de Form Recognizer | Versión preliminar'
titleSuffix: Azure Applied AI Services
description: Procesamiento de formularios y documentos, extracción y análisis de datos mediante API REST de Form Recognizer Studio v3.0 (versión preliminar)
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 11/02/2021
ms.author: lajanuar
ms.custom: ignite-fall-2021
ms.openlocfilehash: e7d532392a2ede60370812535f9946f608f969e9
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132710652"
---
# <a name="quickstart-rest-api---preview"></a>Inicio rápido: API REST | Versión preliminar

>[!NOTE]
> Form Recognizer v3.0 está actualmente en versión preliminar pública. Es posible que algunas características no se admitan o que tengan funcionalidades limitadas.

| [API REST de Form Recognizer](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument) | [Referencia de la API REST de Azure](/rest/api/azure/) |

Comience a usar Azure Form Recognizer mediante el lenguaje de programación C#. Azure Form Recognizer es un componente de Azure Applied AI Services en la nube que usa el aprendizaje automático para extraer y analizar campos de formulario, texto y tablas de los documentos. Puede llamar fácilmente a los modelos de Form Recognizer mediante la integración de los SDK de biblioteca cliente en los flujos de trabajo y aplicaciones. Se recomienda usar el servicio gratuito cuando se está aprendiendo la tecnología. Recuerde que el número de páginas gratuitas se limita a 500 al mes.

Para más información sobre las características y las opciones de desarrollo de Form Recognizer, visite nuestra página de [información general](../overview.md#form-recognizer-features-and-development-options).
## <a name="form-recognizer-models"></a>Modelos de Form Recognizer

 API REST admite los siguientes modelos y funcionalidades:

* 🆕Documento general: analizar y extraer texto, tablas, estructura, pares clave-valor y entidades con nombre.|
* Diseño: analizar y extraer tablas, líneas, palabras y marcas de selección, como botones de radio y casillas en documentos de formularios, sin necesidad de entrenar un modelo.
* Personalizado: analizar y extraer campos de formulario y otro contenido de los formularios personalizados mediante modelos entrenados con sus propios tipos de formulario.
* Facturas: analizar y extraer los campos comunes de las facturas mediante un modelo de facturas entrenado previamente.
* Recibos: analizar y extraer los campos comunes de los recibos mediante un modelo de recibos entrenado previamente.
* Documentos de identificación: analizar y extraer campos comunes de documentos de identificación, como pasaportes o permisos de conducir, mediante un modelo de documentos de identificación entrenado previamente.
* Tarjetas de presentación: analizar y extraer campos comunes de tarjetas de presentación mediante un modelo de tarjetas de presentación previamente entrenado.

## <a name="analyze-document"></a>Análisis de documentos

Form Recognizer v3.0 consolida las operaciones analyze document y get analyze result (GET) para el diseño, los modelos precompilados y los modelos personalizados en un único par de operaciones mediante la asignación de  `modelIds` a las operaciones POST y GET:

```http
POST /documentModels/{modelId}:analyze

GET /documentModels/{modelId}/analyzeResults/{resultId}
```

En la tabla siguiente se muestran las actualizaciones de las llamadas a la API REST.

|Característica| v2.1 | v3.0|
|-----|-----|----|
|Documento general | N/D |`/documentModels/prebuilt-document:analyze` |
|Layout |`/layout/analyze` | ``/documentModels/prebuilt-layout:analyze``|
|Factura | `/prebuilt/invoice/analyze` | `/documentModels/prebuilt-invoice:analyze` |
|Recibo | `/prebuilt/receipt/analyze` | `/documentModels/prebuilt-receipt:analyze` |
|Documento de identificación| `/prebuilt/idDocument/analyze` | `/documentModels/prebuilt-idDocument:analyze`|
|Tarjeta de presentación| `/prebuilt/businessCard/analyze`  | `/documentModels/prebuilt-businessCard:analyze` |
|Personalizado| `/custom/{modelId}/analyze` |`/documentModels/{modelId}:analyze`|

En este inicio rápido, usará las siguientes características para analizar y extraer datos y valores de formularios y documentos:

* [🆕 **Documento general**](#try-it-general-document-model): análisis y extracción de texto, tablas, estructuras, pares clave-valor y entidades con nombre.

* [**Diseño**](#try-it-layout-model): análisis y extracción de tablas, líneas, palabras y marcas de selección, como botones de radio y casillas en documentos de formularios, sin necesidad de entrenar un modelo.

* [**Modelo precompilado**](#try-it-prebuilt-model):analice y extraiga datos de tipos de documentos comunes mediante un modelo entrenado previamente.

## <a name="prerequisites"></a>Prerrequisitos

* Una suscripción a Azure: [cree una cuenta gratuita](https://azure.microsoft.com/free/cognitive-services)

* [cURL](https://curl.haxx.se/windows/) instalado.

* [PowerShell versión 6.0, o superior,](/powershell/scripting/install/installing-powershell-core-on-windows) o una aplicación de la línea de comandos similar.

* Un recurso de Cognitive Services o Form Recognizer. Una vez que tenga la suscripción de Azure, cree un recurso de Form Recognizer de [servicio único](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) o de [varios servicios](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) en Azure Portal para obtener la clave y el punto de conexión. Puede usar el plan de tarifa gratis (`F0`) para probar el servicio y actualizarlo más adelante a un plan de pago para producción.

> [!TIP]
> Cree un recurso de Cognitive Services si tiene previsto acceder a varios servicios de Cognitive Services en un único punto de conexión o clave. Para acceder únicamente a Form Recognizer, cree un recurso de Form Recognizer. Tenga en cuenta que necesitará un recurso de servicio único si tiene previsto usar la [autenticación de Azure Active Directory](../../../active-directory/authentication/overview-authentication.md).

* Una vez implementado el recurso, seleccione **Ir al recurso**. Necesitará la clave y el punto de conexión del recurso que ha creado para conectar la aplicación a la API de Form Recognizer. En una sección posterior de este mismo inicio rápido, pegará la clave y el punto de conexión en el código siguiente:

  :::image type="content" source="../media/containers/keys-and-endpoint.png" alt-text="Captura de pantalla: claves y ubicación del punto de conexión en Azure Portal.":::

### <a name="select-a-code-sample-to-copy-and-paste-into-your-application"></a>Seleccione un ejemplo de código para copiar y pegar en la aplicación:

* [**Documento general**](#try-it-general-document-model)

* [**Layout**](#try-it-layout-model)

* [**Modelo precompilado**](#try-it-prebuilt-model)

> [!IMPORTANT]
>
> Recuerde quitar la clave del código cuando haya terminado y no hacerla nunca pública. En producción, use métodos seguros para almacenar y acceder a sus credenciales. Para más información, consulte el artículo sobre la [seguridad](../../../cognitive-services/cognitive-services-security.md) de Cognitive Services.

## <a name="try-it-general-document-model"></a>**Pruébelo**: modelo de documento general

> [!div class="checklist"]
>
> * Para este ejemplo, necesitará un **archivo de documento de formulario en un identificador URI**. Puede usar nuestro [documento de formulario de ejemplo](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf) para este inicio rápido. Antes de ejecutar el comando, realice estos cambios:

1. Reemplace `{endpoint}` por el punto de conexión que obtuvo con la suscripción de Form Recognizer.
1. Reemplace `{subscription key}` con la clave de suscripción que copió en el paso anterior.
1. Reemplace `{your-document-url}` por la dirección URL del documento del formulario de ejemplo.

#### <a name="request"></a>Solicitud

```bash
curl -v -i POST "https://{endpoint}/formrecognizer/documentModels/prebuilt-document:analyze?api-version=2021-09-30-preview" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{'source': '{your-document-url}'}"
```

#### <a name="operation-location"></a>Operation-Location

Recibirá una respuesta `202 (Success)` que incluye un encabezado **Operation-Location**. El valor de este encabezado contiene un identificador de resultado que puede usar para consultar el estado de la operación asincrónica y obtener los resultados:

https://{host}/formrecognizer/documentModels/{modelId}/analyzeResults/ **{resultId}** ?api-version=2021-07-30-preview

### <a name="get-general-document-results"></a>Obtención de resultados generales del documento

Tras la llamada a **[Analyze document](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)** API, llame a **[Get analyze result](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/GetAnalyzeDocumentResult)** API para obtener el estado de la operación y los datos extraídos. Antes de ejecutar el comando, realice estos cambios:

1. Reemplace `{endpoint}` por el punto de conexión que obtuvo con la suscripción de Form Recognizer.
1. Reemplace `{subscription key}` con la clave de suscripción que copió en el paso anterior.
1. Reemplace `{resultId}` por el identificador de resultado del paso anterior.
<!-- markdownlint-disable MD024 -->

#### <a name="request"></a>Solicitud

```bash
curl -v -X GET "https://{endpoint}/formrecognizer/documentModels/prebuilt-document/analyzeResults/{resultId}?api-version=2021-09-30-preview"
```

### <a name="examine-the-response"></a>Examen de la respuesta

Recibirá una respuesta `200 (Success)` con la salida JSON. El primer campo, `"status"`, indica el estado de la operación. Si la operación no está completa, el valor de `"status"` será `"running"` o `"notStarted"`, y debe llamar a la API de nuevo, ya sea manualmente o a través de un script. Se recomienda un intervalo de uno o varios segundos entre llamadas.

El nodo `"analyzeResults"` contiene todo el texto reconocido. El texto se organiza por página, líneas, tablas, pares clave-valor y entidades.

#### <a name="sample-response"></a>Respuesta de muestra

```json
{
    "status": "succeeded",
    "createdDateTime": "2021-09-28T16:52:51Z",
    "lastUpdatedDateTime": "2021-09-28T16:53:08Z",
    "analyzeResult": {
        "apiVersion": "2021-09-30-preview",
        "modelId": "prebuilt-document",
        "stringIndexType": "textElements",
        "content": "content extracted",
        "pages": [
            {
                "pageNumber": 1,
                "angle": 0,
                "width": 8.4722,
                "height": 11,
                "unit": "inch",
                "words": [
                    {
                        "content": "Case",
                        "boundingBox": [
                            1.3578,
                            0.2244,
                            1.7328,
                            0.2244,
                            1.7328,
                            0.3502,
                            1.3578,
                            0.3502
                        ],
                        "confidence": 1,
                        "span": {
                            "offset": 0,
                            "length": 4
                        }
                    }

                ],
                "lines": [
                    {
                        "content": "Case",
                        "boundingBox": [
                            1.3578,
                            0.2244,
                            3.2879,
                            0.2244,
                            3.2879,
                            0.3502,
                            1.3578,
                            0.3502
                        ],
                        "spans": [
                            {
                                "offset": 0,
                                "length": 22
                            }
                        ]
                    }
                ]
            }
        ],
        "tables": [
            {
                "rowCount": 8,
                "columnCount": 3,
                "cells": [
                    {
                        "kind": "columnHeader",
                        "rowIndex": 0,
                        "columnIndex": 0,
                        "rowSpan": 1,
                        "columnSpan": 1,
                        "content": "Applicant's Name:",
                        "boundingRegions": [
                            {
                                "pageNumber": 1,
                                "boundingBox": [
                                    1.9198,
                                    4.277,
                                    3.3621,
                                    4.2715,
                                    3.3621,
                                    4.5034,
                                    1.9198,
                                    4.5089
                                ]
                            }
                        ],
                        "spans": [
                            {
                                "offset": 578,
                                "length": 17
                            }
                        ]
                    }
                ],
                "spans": [
                    {
                        "offset": 578,
                        "length": 300
                    },
                    {
                        "offset": 1358,
                        "length": 10
                    }
                ]
            }
        ],
        "keyValuePairs": [
            {
                "key": {
                    "content": "Case",
                    "boundingRegions": [
                        {
                            "pageNumber": 1,
                            "boundingBox": [
                                1.3578,
                                0.2244,
                                1.7328,
                                0.2244,
                                1.7328,
                                0.3502,
                                1.3578,
                                0.3502
                            ]
                        }
                    ],
                    "spans": [
                        {
                            "offset": 0,
                            "length": 4
                        }
                    ]
                },
                "value": {
                    "content": "A Case",
                    "boundingRegions": [
                        {
                            "pageNumber": 1,
                            "boundingBox": [
                                1.8026,
                                0.2276,
                                3.2879,
                                0.2276,
                                3.2879,
                                0.3502,
                                1.8026,
                                0.3502
                            ]
                        }
                    ],
                    "spans": [
                        {
                            "offset": 5,
                            "length": 17
                        }
                    ]
                },
                "confidence": 0.867
            }
        ],
        "entities": [
            {
                "category": "Person",
                "content": "Jim Smith",
                "boundingRegions": [
                    {
                        "pageNumber": 1,
                        "boundingBox": [
                            3.4672,
                            4.3255,
                            5.7118,
                            4.3255,
                            5.7118,
                            4.4783,
                            3.4672,
                            4.4783
                        ]
                    }
                ],
                "confidence": 0.93,
                "spans": [
                    {
                        "offset": 596,
                        "length": 21
                    }
                ]
            }
        ],
        "styles": [
            {
                "isHandwritten": true,
                "confidence": 0.95,
                "spans": [
                    {
                        "offset": 565,
                        "length": 12
                    },
                    {
                        "offset": 3493,
                        "length": 1
                    }
                ]
            }
        ]
    }
}

```

## <a name="try-it-layout-model"></a>**Pruébelo**: modelo de diseño

> [!div class="checklist"]
>
> * Para este ejemplo, necesitará un **archivo de documento de formulario en un identificador URI**. Puede usar nuestro [documento de formulario de ejemplo](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf) para este inicio rápido.

 Antes de ejecutar el comando, realice estos cambios:

1. Reemplace `{endpoint}` por el punto de conexión que obtuvo con la suscripción de Form Recognizer.
1. Reemplace `{subscription key}` con la clave de suscripción que copió en el paso anterior.
1. Reemplace `"{your-document-url}` por una de las direcciones URL de ejemplo.

#### <a name="request"></a>Solicitud

```bash
curl -v -i POST "https://{endpoint}/formrecognizer/documentModels/prebuilt-layout:analyze?api-version=2021-09-30-preview" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{'source': '{your-document-url}'}"

```

#### <a name="operation-location"></a>Operation-Location

Recibirá una respuesta `202 (Success)` que incluye un encabezado **Operation-Location**. El valor de este encabezado contiene un identificador de resultado que puede usar para consultar el estado de la operación asincrónica y obtener los resultados:

`https://{host}/formrecognizer/documentModels/{modelId}/analyzeResults/**{resultId}**?api-version=2021-07-30-preview`

### <a name="get-layout-results"></a>Obtención de los resultados del diseño

Tras la llamada a **[Analyze document](https://westus.api.cognitive.microsoft.com/formrecognizer/documentModels/prebuilt-layout:analyze?api-version=2021-09-30-preview&stringIndexType=textElements)** API, llame a **[Get analyze result](https://westus.api.cognitive.microsoft.com/formrecognizer/documentModels/prebuilt-layout/analyzeResults/{resultId}?api-version=2021-09-30-preview)** API para obtener el estado de la operación y los datos extraídos. Antes de ejecutar el comando, realice estos cambios:

1. Reemplace `{endpoint}` por el punto de conexión que obtuvo con la suscripción de Form Recognizer.
1. Reemplace `{subscription key}` con la clave de suscripción que copió en el paso anterior.
1. Reemplace `{resultId}` por el identificador de resultado del paso anterior.
<!-- markdownlint-disable MD024 -->

#### <a name="request"></a>Solicitud

```bash
curl -v -X GET "https://{endpoint}/formrecognizer/documentModels/prebuilt-layout/analyzeResults/{resultId}?api-version=2021-09-30-preview"
```

### <a name="examine-the-response"></a>Examen de la respuesta

Recibirá una respuesta `200 (Success)` con la salida JSON. El primer campo, `"status"`, indica el estado de la operación. Si la operación no está completa, el valor de `"status"` será `"running"` o `"notStarted"`, y debe llamar a la API de nuevo, ya sea manualmente o a través de un script. Se recomienda un intervalo de uno o varios segundos entre llamadas.

## <a name="try-it-prebuilt-model"></a>**Probar**: modelo precompilado

En este ejemplo se muestra cómo analizar datos procedentes de algunos tipos de documentos comunes con un modelo previamente entrenado. Por ejemplo, utilizando una factura.

> [!div class="checklist"]
>
> * En este ejemplo, analizaremos un documento de factura mediante un modelo precompilado. Puede usar nuestro [documento de factura de ejemplo](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf) para este inicio rápido.

### <a name="choose-the-invoice-prebuilt-model-id"></a>Elección del identificador de modelo precompilado de factura

No está limitado a las facturas: hay varios modelos precompilados entre los que elegir, cada uno de los cuales tiene su propio conjunto de campos admitidos. El modelo que se va a usar para la operación de análisis depende del tipo de documento que se va a analizar. Estos son los identificadores de modelo de los modelos precompilados admitidos actualmente por el servicio Form Recognizer:

* **prebuilt-invoice**: extrae texto, marcas de selección, tablas, pares clave-valor y la información principal de las facturas.
* **prebuilt-businessCard**: extrae el texto y la información principal de las tarjetas de presentación.
* **prebuilt-idDocument**: extrae el texto y la información principal de permisos de conducir y pasaportes internacionales.
* **prebuilt-receipt**: extrae el texto y la información principal de los recibos.

Antes de ejecutar el comando, realice estos cambios:

1. Reemplace `{endpoint}` por el punto de conexión que obtuvo con la suscripción de Form Recognizer.
1. Reemplace `{subscription key}` con la clave de suscripción que copió en el paso anterior.
1. Reemplace `\"{your-document-url}` por una dirección URL de factura de ejemplo:

    ```http
    https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf
    ```

#### <a name="request"></a>Solicitud

```bash
curl -v -i POST "https://{endpoint}/formrecognizer/documentModels/prebuilt-invoice:analyze?api-version=2021-09-30-preview" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{'source': '{your-document-url}'}"
```

#### <a name="operation-location"></a>Operation-Location

Recibirá una respuesta `202 (Success)` que incluye un encabezado **Operation-Location**. El valor de este encabezado contiene un identificador de resultado que puede usar para consultar el estado de la operación asincrónica y obtener los resultados:

https://{host}/formrecognizer/documentModels/{modelId}/analyzeResults/ **{resultId}** ?api-version=2021-07-30-preview

### <a name="get-invoice-results"></a>Obtención de los resultados de la factura

Tras la llamada a **[Analyze document](https://westus.api.cognitive.microsoft.com/formrecognizer/documentModels/prebuilt-invoice:analyze?api-version=2021-09-30-preview&stringIndexType=textElements)** API, llame a **[Get analyze result](https://westus.api.cognitive.microsoft.com/formrecognizer/documentModels/prebuilt-invoice/analyzeResults/{resultId}?api-version=2021-09-30-preview)** API para obtener el estado de la operación y los datos extraídos. Antes de ejecutar el comando, realice estos cambios:

1. Reemplace `{endpoint}` por el punto de conexión que obtuvo con la suscripción de Form Recognizer.
1. Reemplace `{subscription key}` con la clave de suscripción que copió en el paso anterior.
1. Reemplace `{resultId}` por el identificador de resultado del paso anterior.
<!-- markdownlint-disable MD024 -->

#### <a name="request"></a>Solicitud

```bash
curl -v -X GET "https://{endpoint}/formrecognizer/documentModels/prebuilt-invoice/analyzeResults/{resultId}?api-version=2021-09-30-preview"
```

### <a name="examine-the-response"></a>Examen de la respuesta

Recibirá una respuesta `200 (Success)` con la salida JSON. El primer campo, `"status"`, indica el estado de la operación. Si la operación no está completa, el valor de `"status"` será `"running"` o `"notStarted"`, y debe llamar a la API de nuevo, ya sea manualmente o a través de un script. Se recomienda un intervalo de uno o varios segundos entre llamadas.

### <a name="improve-results"></a>Mejora de los resultados

[!INCLUDE [improve results](../includes/improve-results-unlabeled.md)]

## <a name="manage-custom-models"></a>Administración de modelos personalizados

### <a name="get-a-list-of-models"></a>Obtención de una lista de modelos

La versión preliminar v3.0 de la solicitud   [List models](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/GetModels) devuelve una lista paginada de modelos precompilados, además de modelos personalizados. Solo se incluyen los modelos con el estado correcto. Los modelos en curso o con errores se pueden enumerar a través de la solicitud [List Operations](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/GetOperations). Use la propiedad nextLink para acceder a la página siguiente de modelos, si hay. Para más información sobre cada modelo devuelto, incluida la lista de documentos admitidos y sus campos, pase modelId a la solicitud  [Get Model](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/GetOperations).

```bash
curl -v -X GET "https://{endpoint}/formrecognizer/documentModels?api-version=2021-07-30-preview"
```

### <a name="get-a-specific-model"></a>Obtención de un modelo específico

La versión preliminar v3.0 de [Get Model](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/GetModel) recupera información sobre un modelo concreto con el estado de correcto. En el caso de los modelos con errores y en curso, use [Get Operation](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/GetOperation) para realizar un seguimiento del estado de las operaciones de creación de modelos y de los errores resultantes.

```bash
curl -v -X GET "https://{endpoint}/formrecognizer/documentModels/{modelId}?api-version=2021-07-30-preview"
```

### <a name="delete-a-model"></a>Eliminar un modelo

La versión preliminar v3.0 de la solicitud [Delete model](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/DeleteModel) quita el modelo personalizado, por lo que las operaciones futuras ya no pueden acceder a modelId.  Los nuevos modelos se pueden crear con el mismo modelId sin conflictos.

```bash
curl -v -X DELETE "https://{endpoint}/formrecognizer/documentModels/{modelId}?api-version=2021-07-30-preview"
```

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, ha usado la API REST de Form Recognizer, versión preliminar (v3.0) analizar formularios de maneras diferentes. A continuación, explore la documentación de referencia para encontrar más información sobre la API de Form Recognizer.

> [!div class="nextstepaction"]
> [Documentación de referencia de la versión preliminar v3.0 de la API REST ](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)

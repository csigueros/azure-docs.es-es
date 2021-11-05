---
title: 'Introducción: API REST v2.1 de Azure Form Recognizer'
description: Use la API REST v2.1 de Form Recognizer para crear una aplicación de procesamiento de formularios que extraiga pares clave-valor y datos de tabla de los documentos personalizados.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 11/02/2021
ms.author: lajanuar
ms.custom: ignite-fall-2021
ms.openlocfilehash: 7d53287f5ed83f87d21880605e80206527264dd3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131030886"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->

> [!IMPORTANT]
>
> Este inicio rápido va dirigido a la API REST de Azure Form Recognizer, versión **2.1** que usa cURL para ejecutar llamadas API REST.

| [API REST de Form Recognizer](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm) | [Referencia de la API REST de Azure](/rest/api/azure/) |

En este inicio rápido, usará las siguientes API para extraer datos estructurados de formularios y documentos:

* [Diseño](#try-it-layout-model)

* [Factura](#try-it-prebuilt-model)

## <a name="prerequisites"></a>Prerrequisitos

* Una suscripción a Azure: [cree una cuenta gratuita](https://azure.microsoft.com/free/cognitive-services)

* [cURL](https://curl.haxx.se/windows/) instalado.

* [PowerShell versión 6.0, o superior,](/powershell/scripting/install/installing-powershell-core-on-windows) o una aplicación de la línea de comandos similar.

* Un recurso de Cognitive Services o Form Recognizer. Una vez que tenga la suscripción de Azure, cree un recurso de Form Recognizer de [servicio único](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) o de [varios servicios](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) en Azure Portal para obtener la clave y el punto de conexión. Puede usar el plan de tarifa gratis (`F0`) para probar el servicio y actualizarlo más adelante a un plan de pago para producción.

  > [!TIP]
  > Cree un recurso de Cognitive Services si tiene previsto acceder a varios servicios de Cognitive Services en un único punto de conexión o clave. Para acceder únicamente a Form Recognizer, cree un recurso de Form Recognizer. Tenga en cuenta que necesitará un recurso de servicio único si tiene previsto usar la [autenticación de Azure Active Directory](/azure/active-directory/authentication/overview-authentication).

* Una vez implementado el recurso, seleccione **Ir al recurso**. Necesitará la clave y el punto de conexión del recurso que ha creado para conectar la aplicación a la API de Form Recognizer. En una sección posterior de este mismo inicio rápido, pegará la clave y el punto de conexión en el código siguiente:

  :::image type="content" source="../../media/containers/keys-and-endpoint.png" alt-text="Captura de pantalla: claves y ubicación del punto de conexión en Azure Portal.":::

### <a name="select-a-code-sample-to-copy-and-paste-into-your-application"></a>Seleccione un ejemplo de código para copiar y pegar en la aplicación:

* [**Layout**](#try-it-layout-model)

* [**Factura precompilada**](#try-it-prebuilt-model)

> [!IMPORTANT]
>
> Recuerde quitar la clave del código cuando haya terminado y no hacerla nunca pública. En producción, use métodos seguros para almacenar y acceder a sus credenciales. Para más información, consulte el artículo sobre la [seguridad](/azure/cognitive-services/cognitive-services-security.md) de Cognitive Services.

## <a name="try-it-layout-model"></a>**Pruébelo**: modelo de diseño

> [!div class="checklist"]
>
> * Para este ejemplo, necesitará un **archivo de documento de formulario en un identificador URI**. Puede usar nuestro [documento de formulario de ejemplo](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf) para este inicio rápido.

1. Reemplace `{endpoint}` por el punto de conexión que obtuvo con la suscripción de Form Recognizer.
1. Reemplace `{subscription key}` con la clave de suscripción que copió en el paso anterior.
1. Reemplace `\"{your-document-url}` por la dirección URL del formulario de ejemplo:

```http
https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf
```

#### <a name="request"></a>Solicitud

```bash
curl -v -i POST "https://{endpoint}/formrecognizer/v2.1/layout/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{'source': '{your-document-url}'}"
```

#### <a name="operation-location"></a>Operation-Location

Recibirá una respuesta `202 (Success)` que incluye un encabezado **Operation-Location**. El valor de este encabezado contiene un identificador de resultado que puede usar para consultar el estado de la operación asincrónica y obtener los resultados:

https://<span></span>cognitiveservice/formrecognizer/v2.1/layout/analyzeResults/ **{resultId}** .

En el ejemplo siguiente, como parte de la dirección URL, la cadena después de `analyzeResults/` es el identificador de resultado.

```console
https://cognitiveservice/formrecognizer/v2/layout/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-layout-results"></a>Obtención de los resultados del diseño

Tras la llamada a la API **[Analyze Layout](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeLayoutAsync)** , llame a la API **[Get Analyze Layout Result](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetAnalyzeLayoutResult)** para obtener el estado de la operación y los datos extraídos. Antes de ejecutar el comando, realice estos cambios:

1. Reemplace `{endpoint}` por el punto de conexión que obtuvo con la suscripción de Form Recognizer.
1. Reemplace `{subscription key}` con la clave de suscripción que copió en el paso anterior.
1. Reemplace `{resultId}` por el identificador de resultado del paso anterior.
<!-- markdownlint-disable MD024 -->

#### <a name="request"></a>Solicitud

```bash
curl -v -X GET "https://{endpoint}/formrecognizer/v2.1/layout/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-results"></a>Examen de los resultados

Recibirá una respuesta `200 (success)` con contenido JSON.

Consulte la siguiente imagen de una factura y su correspondiente salida JSON.

* El nodo `"readResults"` contiene cada línea de texto con su correspondiente posición de cuadro de límite en la página.
* El nodo `selectionMarks` muestra todas las marcas de selección (casilla, botón de opción) y si su estado es "activado" o "no activado".
* En la sección `"pageResults"` se incluyen las tablas extraídas. Para cada tabla, se extraen el texto, el índice de filas y columnas, la expansión de filas y columnas, el rectángulo de selección, etc.

:::image type="content" source="../../media/contoso-invoice.png" alt-text="Documento de instrucciones de proyecto de Contoso con una tabla.":::

#### <a name="response-body"></a>Response body

Puede ver la [salida de ejemplo completa en GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-layout-output.json).

## <a name="try-it-prebuilt-model"></a>**Probar**: modelo precompilado

> [!div class="checklist"]
>
> * En este ejemplo, analizaremos un documento de factura mediante un modelo precompilado. Puede usar nuestro [documento de factura de ejemplo](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf) para este inicio rápido.

### <a name="choose-a-prebuilt-model"></a>Elección de un modelo precompilado

No está limitado a las facturas: hay varios modelos precompilados entre los que elegir, cada uno de los cuales tiene su propio conjunto de campos admitidos. El modelo que se va a usar para la operación de análisis depende del tipo de documento que se va a analizar. Estos son los modelos precompilados que actualmente admite el servicio Form Recognizer:

* [**Factura**](../../concept-invoice.md): extrae texto, marcas de selección, tablas, campos e información clave de las facturas.
* [**Recibo**](../../concept-receipt.md): extrae texto e información clave de los recibos.
* [**Documento de identificación**](../../concept-id-document.md): extrae texto e información clave de permisos de conducir y pasaportes internacionales.
* [**Tarjeta de presentación**](../../concept-business-card.md): extrae texto e información clave de las tarjetas de presentación.

Antes de ejecutar el comando, realice estos cambios:

1. Reemplace `{endpoint}` por el punto de conexión que obtuvo con la suscripción de Form Recognizer.
1. Reemplace `{subscription key}` con la clave de suscripción que copió en el paso anterior.
1. Reemplace `\"{your-document-url}` por una dirección URL de factura de ejemplo:

    ```http
    https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf
    ```

#### <a name="request"></a>Solicitud

```bash
curl -v -i POST https://{endpoint}/formrecognizer/v2.1/prebuilt/invoice/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key:  {subscription key}" --data-ascii "{'source': '{your invoice URL}'}"
```

#### <a name="operation-location"></a>Operation-Location

Recibirá una respuesta `202 (Success)` que incluye un encabezado **Operation-Location**. El valor de este encabezado contiene un identificador de resultado que puede usar para consultar el estado de la operación asincrónica y obtener los resultados:

 _https://<span></span>cognitiveservice/formrecognizer/v2.1/prebuilt/receipt/analyzeResults/ **{resultId}**_

En el ejemplo siguiente, como parte de la dirección URL, la cadena después de `analyzeResults/` es el identificador de resultado:

```console
https://cognitiveservice/formrecognizer/v2.1/prebuilt/invoice/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-invoice-results"></a>Obtención de los resultados de la factura

Tras la llamada a la API **Analyze Invoice**, llame a la API **[Get Analyze Invoice Result](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9acb78c40a2533aee83)** para obtener el estado de la operación y los datos extraídos. Antes de ejecutar el comando, realice estos cambios:

1. Reemplace `{endpoint}` por el punto de conexión que obtuvo con la clave de suscripción de Form Recognizer. Lo encontrará en la pestaña **Información general** del recurso Form Recognizer.
1. Reemplace `{resultId}` por el identificador de resultado del paso anterior.
1. Reemplace `{subscription key}` por la clave de suscripción.

#### <a name="request"></a>Solicitud

```bash
curl -v -X GET "https://{endpoint}/formrecognizer/v2.1/prebuilt/invoice/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>Examen de la respuesta

Recibirá una respuesta `200 (Success)` con la salida JSON.

* El campo `"readResults"` contiene todas las líneas de texto que se extrajeron de la factura.
* El campo `"pageResults"` incluye las marcas de tablas y selecciones extraídas de la factura.
* El campo `"documentResults"` contiene información de pares clave-valor para las partes más importantes de la factura.

Consulte el documento de [factura de ejemplo](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-invoice.pdf).

#### <a name="response-body"></a>Response body

Consulte la [salida de ejemplo completa en GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-invoice-output.json).

## <a name="next-steps"></a>Pasos siguientes

Felicidades. En este inicio rápido, ha usado la API REST de Form Recognizer para analizar formularios de maneras diferentes. A continuación, explore la documentación de referencia para encontrar más información sobre la API de Form Recognizer v3.0.

> [!div class="nextstepaction"]
> [Documentación de referencia de la API REST v2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9843c2794cbb1a96291)

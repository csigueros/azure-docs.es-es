---
title: Migración de la aplicación de Form Recognizer v2.1 a v3.0
titleSuffix: Azure Applied AI Services
description: En esta guía paso a paso, aprenderá las diferencias entre la API de Form Recognizer v2.1 y v3.0. También aprenderá los cambios que necesita para pasar a la versión más reciente de la API.
author: vkurpad
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 10/07/2021
ms.author: vikurpad
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: fd0c9c1ebaf177f6f10698631b96e2c27825603f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131020832"
---
# <a name="form-recognizer-v30-migration--preview"></a>Migración de Form Recognizer v3.0 | Versión preliminar

> [!IMPORTANT]
>
> La API REST v3.0 de Form Recognizer presenta cambios importantes en la solicitud de API REST y analiza el código JSON de respuesta.

Form Recognizer v3.0 (versión preliminar) presenta varias características y funcionalidades nuevas:

* [API REST de Form Recognizer](quickstarts/try-v3-rest-api.md) se ha rediseñado para que sea más fácil de usar.
* El modelo de [**documento general (v3.0)**](concept-general-document.md) es una nueva API que extrae texto, tablas, estructura, pares clave-valor y entidades con nombre de formularios y documentos.
* El modelo de [**recibo (v3.0)**](concept-receipt.md) admite el procesamiento de recibos de hotel de una sola página.
* El modelo de [**documento de identificación (v3.0)**](concept-id-document.md) admite aprobaciones, restricciones y la extracción de clasificación de vehículos de permisos de conducir de EE. UU.
* La [**API del modelo personalizado (v3.0)**](concept-custom.md) admite la detección de firmas de formularios personalizados.

En este artículo, aprenderá las diferencias entre Form Recognizer v2.1 y v3.0 y cómo pasar a la versión más reciente de la API.

## <a name="changes-to-the-rest-api-endpoints"></a>Cambios en los puntos de conexión de la API REST

 La API REST v3.0 combina las operaciones de análisis de los análisis de diseño, los modelos precompilados y los modelos personalizados en un único par de operaciones mediante la asignación de **`documentModels` y** **`modelId`** a análisis de diseño (diseño precompilado) y modelos precompilados.

### <a name="post-request"></a>Solicitud POST

```http
https://{your-form-recognizer-endpoint}/formrecognizer/documentModels/{modelId}?api-version=2021-07-30-preview

```

### <a name="get-request"></a>Solicitud GET

```http
https://{your-form-recognizer-endpoint}/formrecognizer/documentModels/{modelId}/AnalyzeResult/{resultId}?api-version=2021-07-30-preview
```

### <a name="analyze-operation"></a>Operación de análisis

* La carga de la solicitud y el patrón de llamada permanecen sin cambios.
* La operación de análisis especifica el documento de entrada y las configuraciones específicas del contenido, y devuelve la dirección URL del resultado de análisis a través del encabezado Operation-Location en la respuesta.
* Sondee esta dirección URL de resultado de análisis a través de una solicitud GET para comprobar el estado de la operación de análisis (el intervalo mínimo recomendado entre las solicitudes es de 1 segundo).
* Si es correcto, el estado se establece como correcto y en el cuerpo de la respuesta se devuelve [analyzeResult](#changes-to-analyze-result). Si se producen errores, el estado se establecerá en error y se devolverá un error.

| Modelo | v2.1 | v3.0 |
|:--| :--| :--|
| **Prefijo de dirección URL de solicitud**| **https://{your-form-recognizer-endpoint}/formrecognizer/v2.1**  | **https://{your-form-recognizer-endpoint}/formrecognizer** |
|🆕 **Documento general**|N/D|/documentModels/prebuilt-document:analyze |
| **Diseño**| /layout/analyze |/documentModels/prebuilt-layout:analyze|
|**Personalizada**| /custom/{modelId}/analyze    |/documentModels/{modelId}:analyze |
| **Factura** | /prebuilt/invoice/analyze    | /documentModels/prebuilt-invoice:analyze |
| **Recibo** | /prebuilt/receipt/analyze    | /documentModels/prebuilt-receipt:analyze |
| **Documento de identificación** | /prebuilt/idDocument/analyze |  /documentModels/prebuilt-idDocument:analyze |
|**Tarjeta de presentación**| /prebuilt/businessCard/analyze| /documentModels/prebuilt-businessCard:analyze|

### <a name="analyze-request-body"></a>Análisis del cuerpo de la solicitud

El contenido que se va a analizar se proporciona a través del cuerpo de la solicitud. La dirección URL o los datos codificados en Base64 se pueden usar para construir la solicitud.

  Para especificar una dirección URL web de acceso público, establezca Content-Type en  **application/json** y envíe el siguiente cuerpo JSON:

  ```json
  {
    "urlSource": "{urlPath}"
  }
  ```

La codificación Base64 también se admite en Form Recognizer v3.0:

```json
{
  "base64Source": "{base64EncodedContent}"
}
```

### <a name="additional-parameters"></a>Parámetros adicionales

Parámetros que siguen siendo compatibles:

* páginas
* locale

Parámetros que ya no se admiten: 

* includeTextDetails

El nuevo formato de respuesta es más compacto y siempre se devuelve la salida completa.

## <a name="changes-to-analyze-result"></a>Cambios en el resultado de análisis

La respuesta de análisis se ha refactorizado a los siguientes resultados de nivel superior para admitir elementos de varias páginas.

* páginas
* Tablas
* keyValuePairs
* entities
* estilos
* Documentos

> [!NOTE]
>
> Los cambios en la respuesta analyzeResult son varios, como pasar de una propiedad de páginas a una propiedad de nivel superior dentro de analyzeResult.

```json

{
// Basic analyze result metadata
"apiVersion": "2021-07-30-preview", // REST API version used
"modelId": "prebuilt-invoice", // ModelId used
"stringIndexType": "textElements", // Character unit used for string offsets and lengths:
// textElements, unicodeCodePoint, utf16CodeUnit // Concatenated content in global reading order across pages.
// Words are generally delimited by space, except CJK (Chinese, Japanese, Korean) characters.
// Lines and selection marks are generally delimited by newline character.
// Selection marks are represented in Markdown emoji syntax (:selected:, :unselected:).
"content": "CONTOSO LTD.\nINVOICE\nContoso Headquarters...", "pages": [ // List of pages analyzed
{
// Basic page metadata
"pageNumber": 1, // 1-indexed page number
"angle": 0, // Orientation of content in clockwise direction (degree)
"width": 0, // Page width
"height": 0, // Page height
"unit": "pixel", // Unit for width, height, and bounding box coordinates
"spans": [ // Parts of top-level content covered by page
{
"offset": 0, // Offset in content
"length": 7 // Length in content
}
], // List of words in page
"words": [
{
"text": "CONTOSO", // Equivalent to $.content.Substring(span.offset, span.length)
"boundingBox": [ ... ], // Position in page
"confidence": 0.99, // Extraction confidence
"span": { ... } // Part of top-level content covered by word
}, ...
], // List of selectionMarks in page
"selectionMarks": [
{
"state": "selected", // Selection state: selected, unselected
"boundingBox": [ ... ], // Position in page
"confidence": 0.95, // Extraction confidence
"span": { ... } // Part of top-level content covered by selection mark
}, ...
], // List of lines in page
"lines": [
{
"content": "CONTOSO LTD.", // Concatenated content of line (may contain both words and selectionMarks)
"boundingBox": [ ... ], // Position in page
"spans": [ ... ], // Parts of top-level content covered by line
}, ...
]
}, ...
], // List of extracted tables
"tables": [
{
"rowCount": 1, // Number of rows in table
"columnCount": 1, // Number of columns in table
"boundingRegions": [ // Bounding boxes potentially across pages covered by table
{
"pageNumber": 1, // 1-indexed page number
"boundingBox": [ ... ], // Bounding box
}
],
"spans": [ ... ], // Parts of top-level content covered by table // List of cells in table
"cells": [
{
"kind": "stub", // Cell kind: content (default), rowHeader, columnHeader, stub, description
"rowIndex": 0, // 0-indexed row position of cell
"columnIndex": 0, // 0-indexed column position of cell
"rowSpan": 1, // Number of rows spanned by cell (default=1)
"columnSpan": 1, // Number of columns spanned by cell (default=1)
"content": "SALESPERSON", // Concatenated content of cell
"boundingRegions": [ ... ], // Bounding regions covered by cell
"spans": [ ... ] // Parts of top-level content covered by cell
}, ...
]
}, ...
], // List of extracted key-value pairs
"keyValuePairs": [
{
"key": { // Extracted key
"content": "INVOICE:", // Key content
"boundingRegions": [ ... ], // Key bounding regions
"spans": [ ... ] // Key spans
},
"value": { // Extracted value corresponding to key, if any
"content": "INV-100", // Value content
"boundingRegions": [ ... ], // Value bounding regions
"spans": [ ... ] // Value spans
},
"confidence": 0.95 // Extraction confidence
}, ...
], // List of extracted entities
"entities": [
{
"category": "DateTime", // Primary entity category
"subCategory": "Date", // Secondary entity category
"content": "11/15/2019", // Entity content
"boundingRegions": [ ... ], // Entity bounding regions
"spans": [ ... ], // Entity spans
"confidence": 0.99 // Extraction confidence
}, ...
], // List of extracted styles
"styles": [
{
"isHandwritten": true, // Is content in this style handwritten?
"spans": [ ... ], // Spans covered by this style
"confidence": 0.95 // Detection confidence
}, ...
], // List of extracted documents
"documents": [
{
"docType": "prebuilt-invoice", // Classified document type (model dependent)
"boundingRegions": [ ... ], // Document bounding regions
"spans": [ ... ], // Document spans
"confidence": 0.99, // Document splitting/classification confidence // List of extracted fields
"fields": {
"VendorName": { // Field name (docType dependent)
"type": "string", // Field value type: string, number, array, object, ...
"valueString": "CONTOSO LTD.",// Normalized field value
"content": "CONTOSO LTD.", // Raw extracted field content
"boundingRegions": [ ... ], // Field bounding regions
"spans": [ ... ], // Field spans
"confidence": 0.99 // Extraction confidence
}, ...
}
}, ...
]
}



```

## <a name="build-or-train-model"></a>Compilación o entrenamiento del modelo

El objeto de modelo tiene dos actualizaciones en la nueva API

* ```modelId``` es ahora una propiedad que se puede establecer en un nombre legible para el usuario en un modelo.
* El nombre de ```modelName``` ha cambiado a ```description```

La operación ```build``` se invoca para entrenar un modelo. La carga de la solicitud y el patrón de llamada permanecen sin cambios. La operación de compilación especifica el modelo y el conjunto de datos de entrenamiento, y devuelve el resultado a través del encabezado Operation-Location de la respuesta. Sondee esta dirección URL de la operación de modelo a través de una solicitud GET para comprobar el estado de la operación de compilación (el intervalo mínimo recomendado entre las solicitudes es de 1 segundo). A diferencia de la versión 2.1, esta dirección URL no es la ubicación de recursos del modelo. En su lugar, la dirección URL del modelo se puede construir a partir del valor de modelId especificado, que también se recupera de la propiedad resourceLocation en la respuesta. Si la operación es correcta, el estado se establece en ```succeeded``` y el resultado contiene la información del modelo personalizado. Si se producen errores, el estado se establece en ```failed``` y se devuelve el error.

El código siguiente es una solicitud de compilación de ejemplo mediante un token de SAS. Tenga en cuenta la barra diagonal final al establecer la ruta de acceso del prefijo o de la carpeta.

```json
POST https://{your-form-recognizer-endpoint}/formrecognizer/documentModels:build?api-version=2021-09-30-preview

{
  "modelId": {modelId},
  "description": "Sample model",
  "azureBlobSource": {
    "containerUrl": "https://{storageAccount}.blob.core.windows.net/{containerName}?{sasToken}",
    "prefix": "{folderName/}"
  }
}
```

## <a name="changes-to-compose-model"></a>Cambios en la composición de modelos

La composición de modelos ahora está limitada a un único nivel de anidamiento. Los modelos compuestos ahora son coherentes con los modelos personalizados gracias a la adición de las propiedades ```modelId``` y ```description```.

```json
POST https://{your-form-recognizer-endpoint}/formrecognizer/documentModels:compose?api-version=2021-09-30-preview
{
  "modelId": "{composedModelId}",
  "description": "{composedModelDescription}",
  "componentModels": [
    { "modelId": "{modelId1}" },
    { "modelId": "{modelId2}" },
  ]
}

```

## <a name="changes-to-copy-model"></a>Cambios en el modelo de copia

El patrón de llamada para el modelo de copia permanece sin cambios:

* Autorización de la operación de copia con el recurso de destino llamando a ```authorizeCopy```. Ahora, una solicitud POST.
* Envío de la autorización al recurso de origen para copiar el modelo llamando a ```copy-to```.
* Sondeo de la operación devuelta para validar que la operación se completó correctamente.

Los únicos cambios en la función del modelo de copia son:

* La acción HTTP en ```authorizeCopy``` ahora es una solicitud POST.
* La carga de autorización contiene toda la información necesaria para enviar la solicitud de copia.

***Autorizar la copia***

```json
POST https://{targetHost}/formrecognizer/documentModels:authorizeCopy?api-version=2021-09-30-preview
{
  "modelId": "{targetModelId}",
  "description": "{targetModelDescription}",
}
```

Use el cuerpo de respuesta de la acción de autorización para construir la solicitud de la copia.

```json
POST https://{sourceHost}/formrecognizer/documentModels/{sourceModelId}:copy-to?api-version=2021-09-30-preview
{
  "targetResourceId": "{targetResourceId}",
  "targetResourceRegion": "{targetResourceRegion}",
  "targetModelId": "{targetModelId}",
  "targetModelLocation": "https://{targetHost}/formrecognizer/documentModels/{targetModelId}",
  "accessToken": "{accessToken}",
  "expirationDateTime": "2021-08-02T03:56:11Z"
}
```

## <a name="changes-to-list-models"></a>Cambios en los modelos de lista

Los modelos de lista se han ampliado para devolver ahora modelos precompilados y personalizados. Todos los nombres de modelos precompilados comienzan por ```prebuilt-```. Solo se devuelven los modelos con el estado correcto. Para enumerar los modelos con errores o en curso, consulte [Operaciones de enumeración](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/GetModels).

***Solicitud de enumeración de modelos de ejemplo***

```json
GET https://{your-form-recognizer-endpoint}/formrecognizer/documentModels?api-version=2021-09-30-preview
```

## <a name="change-to-get-model"></a>Cambio en el modelo de obtención

Como el modelo de obtención incluye ahora modelos precompilados, la operación de obtención devuelve un diccionario ```docTypes```. Cada tipo de documento se describe por su nombre, descripción opcional, esquema de campo y confianza de campo opcional. El esquema de campo describe la lista de campos posiblemente devueltos con el tipo de documento.

```json
GET https://{your-form-recognizer-endpoint}/formrecognizer/documentModels/{modelId}?api-version=2021-09-30-preview
```

## <a name="new-get-info-operation"></a>Nueva operación de obtención de información

La operación ```info``` en el servicio devuelve el recuento y el límite de modelos personalizados.

```json
GET https://{your-form-recognizer-endpoint}/formrecognizer/info? api-version=2021-09-30-preview
```

***Respuesta de muestra***

```json
{
  "customDocumentModels": {
    "count": 5,
    "limit": 100
  }
}
```

## <a name="next-steps"></a>Pasos siguientes

En esta guía de migración, ha aprendido a actualizar la aplicación Form Recognizer existente para usar las API v3.0. Siga usando la API 2.1 con todas las características disponibles con carácter general y la API 3.0 si quiere usar cualquiera de las características en versión preliminar.

* [Revisión de la nueva API REST](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)
* [¿Qué es Form Recognizer?](overview.md)
* [Inicio rápido: Uso de la biblioteca cliente de Form Recognizer](./quickstarts/try-sdk-rest-api.md)

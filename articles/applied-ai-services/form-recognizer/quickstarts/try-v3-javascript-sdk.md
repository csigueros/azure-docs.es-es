---
title: 'Inicio rápido: SDK de JavaScript de Form Recognizer v3.0 | Versión preliminar'
titleSuffix: Azure Applied AI Services
description: Procesamiento de formularios y documentos, extracción de datos y análisis mediante los SDK de la biblioteca cliente de JavaScript de Form Recognizer v3.0 (versión preliminar)
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 11/02/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: d3af8169bf482acbbc7122c55094231e834b3aa9
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132709830"
---
# <a name="quickstart-form-recognizer-javascript-client-library-sdks-v30--preview"></a>Inicio rápido: SDK de la biblioteca cliente de JavaScript de Form Recognizer v3.0 | Versión preliminar

>[!NOTE]
> Form Recognizer v3.0 está actualmente en versión preliminar pública. Es posible que algunas características no se admitan o que tengan funcionalidades limitadas.

[Documentación de referencia](https://azuresdkdocs.blob.core.windows.net/$web/javascript/azure-ai-form-recognizer/4.0.0-beta.1/index.html) | [Código fuente de la biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/formrecognizer/ai-form-recognizer/src) | [Paquete (NuGet)](https://www.nuget.org/packages/Azure.AI.FormRecognizer) | [Ejemplos](https://github.com/Azure/azure-sdk-for-js/blob/%40azure/ai-form-recognizer_4.0.0-beta.1/sdk/formrecognizer/ai-form-recognizer/README.md)

Empiece a usar Azure Form Recognizer con el lenguaje de programación JavaScript. Azure Form Recognizer es un componente de Azure Applied AI Services en la nube que usa el aprendizaje automático para extraer y analizar campos de formulario, texto y tablas de los documentos. Puede llamar fácilmente a los modelos de Form Recognizer mediante la integración de los SDK de biblioteca cliente en los flujos de trabajo y aplicaciones. Se recomienda usar el servicio gratuito cuando se está aprendiendo la tecnología. Recuerde que el número de páginas gratuitas se limita a 500 al mes.

Para más información sobre las características y las opciones de desarrollo de Form Recognizer, visite nuestra página de [información general](../overview.md#form-recognizer-features-and-development-options).

En este inicio rápido, usará las siguientes características para analizar y extraer datos y valores de formularios y documentos:

* [🆕 **Documento general**](#try-it-general-document-model): análisis y extracción de texto, tablas, estructuras, pares clave-valor y entidades con nombre.

* [**Diseño**](#try-it-layout-model): análisis y extracción de tablas, líneas, palabras y marcas de selección, como botones de radio y casillas en documentos de formularios, sin necesidad de entrenar un modelo.

* [**Factura precompilada**](#try-it-prebuilt-model): análisis y extracción de campos comunes de facturas mediante un modelo de facturas entrenado previamente.

## <a name="prerequisites"></a>Prerrequisitos

* Una suscripción a Azure: [cree una cuenta gratuita](https://azure.microsoft.com/free/cognitive-services/).

* La versión más reciente de [Visual Studio Code](https://code.visualstudio.com/) o el IDE que prefiera. 

* La versión de LTS más reciente para [Node.js](https://nodejs.org/about/releases/).

* Un recurso de Cognitive Services o Form Recognizer. Una vez que tenga la suscripción de Azure, cree un recurso de Form Recognizer de [servicio único](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) o de [varios servicios](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) en Azure Portal para obtener la clave y el punto de conexión. Puede usar el plan de tarifa gratis (`F0`) para probar el servicio y actualizarlo más adelante a un plan de pago para producción.

    > [!TIP]
    > Cree un recurso de Cognitive Services si tiene previsto acceder a varios servicios de Cognitive Services en un único punto de conexión o clave. Para acceder únicamente a Form Recognizer, cree un recurso de Form Recognizer. Tenga en cuenta que necesitará un recurso de servicio único si tiene previsto usar la [autenticación de Azure Active Directory](../../../active-directory/authentication/overview-authentication.md).

* Una vez implementado el recurso, seleccione **Ir al recurso**. Necesitará la clave y el punto de conexión del recurso que ha creado para conectar la aplicación a la API de Form Recognizer. En una sección posterior de este mismo inicio rápido, pegará la clave y el punto de conexión en el código siguiente:

  :::image type="content" source="../media/containers/keys-and-endpoint.png" alt-text="Captura de pantalla: claves y ubicación del punto de conexión en Azure Portal.":::

## <a name="set-up"></a>Configurar

1. Cree una nueva aplicación Node.js. En una ventana de la consola (como cmd, PowerShell o Bash), cree un directorio para la aplicación y vaya a él.

    ```console
    mkdir form-recognizer-app && cd form-recognizer-app
    ```

1. Ejecute el comando `npm init` para crear una aplicación de nodo con un archivo `package.json`.

    ```console
    npm init
    ```

1. Instale el paquete npm de la biblioteca cliente `ai-form-recognizer`:

    ```console
    npm install @azure/ai-form-recognizer@4.0.0-beta.1 @azure/identity
    ```

    * el archivo `package.json` de la aplicación se actualizará con las dependencias.

1. Cree un archivo llamado `index.js`, ábralo y agregue las bibliotecas siguientes:

    ```javascript
   const { AzureKeyCredential, DocumentAnalysisClient } = require("@azure/ai-form-recognizer");
    ```

1. Cree las variables para el punto de conexión y la clave de Azure del recurso.

    ```javascript
    const apiKey = "PASTE_YOUR_FORM_RECOGNIZER_SUBSCRIPTION_KEY_HERE";
    const endpoint = "PASTE_YOUR_FORM_RECOGNIZER_ENDPOINT_HERE";
    ```

En este punto, la aplicación JavaScript debería contener las siguientes líneas de código:

```javascript
const { AzureKeyCredential, DocumentAnalysisClient } = require("@azure/ai-form-recognizer");

const endpoint = "PASTE_YOUR_FORM_RECOGNIZER_ENDPOINT_HERE";
const apiKey = "PASTE_YOUR_FORM_RECOGNIZER_SUBSCRIPTION_KEY_HERE";
```

### <a name="select-a-code-sample-to-copy-and-paste-into-your-application"></a>Seleccione un ejemplo de código para copiar y pegar en la aplicación:

* [**Documento general**](#try-it-general-document-model)

* [**Layout**](#try-it-layout-model)

* [**Factura precompilada**](#try-it-prebuilt-model)

> [!IMPORTANT]
>
> Recuerde quitar la clave del código cuando haya terminado y no hacerla nunca pública. En producción, use métodos seguros para almacenar y acceder a sus credenciales. Para más información, consulte el artículo sobre [seguridad](../../../cognitive-services/cognitive-services-security.md) de Cognitive Services.

## <a name="try-it-general-document-model"></a>**Pruébelo**: modelo de documento general

> [!div class="checklist"]
>
> * Para este ejemplo, necesitará un **archivo de documento de formulario en un identificador URI**. Puede usar nuestro [documento de formulario de ejemplo](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf) para este inicio rápido.
> * Para analizar un archivo determinado en un identificador URI, utilizará el método `beginExtractGenericDocument`.
> * Se ha agregado el valor del URI del archivo a la variable `formUrl` cerca de la parte superior del archivo.
> * Para ver la lista de todos los campos admitidos y los tipos correspondientes, consulte nuestra página de concepto del [documento general](../concept-general-document.md#named-entity-recognition-ner-categories).

### <a name="add-the-following-code-to-your-general-document-application-on-the-line-below-the-apikey-variable"></a>Agregue el código siguiente a la aplicación de documento general en la línea debajo de la variable `apiKey`

```javascript

const formUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf"

async function main() {
    const client = new DocumentAnalysisClient(endpoint, new DefaultAzureCredential(apiKey));

    const poller = await client.beginExtractGenericDocument(formUrl);

    const {
        keyValuePairs,
        entities
    } = await poller.pollUntilDone();

    if (keyValuePairs.length <= 0) {
        console.log("No key-value pairs were extracted from the document.");
    } else {
        console.log("Key-Value Pairs:");
        for (const {
                key,
                value,
                confidence
            } of keyValuePairs) {
            console.log("- Key  :", `"${key.content}"`);
            console.log("  Value:", `"${value?.content ?? "<undefined>"}" (${confidence})`);
        }
    }

    if (entities.length <= 0) {
        console.log("No entities were extracted from the document.");
    } else {
        console.log("Entities:");
        for (const entity of entities) {
            console.log(
                `- "${entity.content}" ${entity.category} - ${entity.subCategory ?? "<none>"} (${
          entity.confidence
        })`
            );
        }
    }
}

main().catch((error) => {
    console.error("An error occurred:", error);
    process.exit(1);
});
```

## <a name="try-it-layout-model"></a>**Pruébelo**: modelo de diseño

> [!div class="checklist"]
>
> * Para este ejemplo, necesitará un **archivo de documento de formulario en un identificador URI**. Puede usar nuestro [documento de formulario de ejemplo](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf) para este inicio rápido.
> * Se ha agregado el valor del URI del archivo a la variable `formUrl` cerca de la parte superior del archivo.
> * Para analizar un archivo determinado en un identificador URI, utilizará el método `beginExtractLayout`.

### <a name="add-the-following-code-to-your-layout-application-on-the-line-below-the-apikey-variable"></a>Agregue el código siguiente a la aplicación de formato en la línea debajo de la variable `apiKey`

```javascript

const formUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf"

async function main() {
    const client = new DocumentAnalysisClient(endpoint, new AzureKeyCredential(apiKey));

    const poller = await client.beginExtractLayout(formUrl);

    const {
        pages,
        tables
    } = await poller.pollUntilDone();

    if (pages.length <= 0) {
        console.log("No pages were extracted from the document.");
    } else {
        console.log("Pages:");
        for (const page of pages) {
            console.log("- Page", page.pageNumber, `(unit: ${page.unit})`);
            console.log(`  ${page.width}x${page.height}, angle: ${page.angle}`);
            console.log(`  ${page.lines.length} lines, ${page.words.length} words`);
        }
    }

    if (tables.length <= 0) {
        console.log("No tables were extracted from the document.");
    } else {
        console.log("Tables:");
        for (const table of tables) {
            console.log(
                `- Extracted table: ${table.columnCount} columns, ${table.rowCount} rows (${table.cells.length} cells)`
            );
        }
    }
}

main().catch((error) => {
    console.error("An error occurred:", error);
    process.exit(1);
});

```

## <a name="try-it-prebuilt-model"></a>**Probar**: modelo precompilado

En este ejemplo se muestra cómo analizar datos procedentes de algunos tipos de documentos comunes con un modelo previamente entrenado. Por ejemplo, utilizando una factura.

> [!div class="checklist"]
>
> * En este ejemplo, analizaremos un documento de factura mediante un modelo precompilado. Puede usar nuestro [documento de factura de ejemplo](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf) para este inicio rápido.
> * Se ha agregado el valor del URI del archivo a la variable `invoiceUrl` de la parte superior del archivo.
> * Para analizar un archivo determinado de un URI, usará el método `beginAnalyzeDocuments` y transferirá `PrebuiltModels.Invoice` como identificador del modelo. El valor devuelto es un objeto `result` que contiene datos sobre el documento enviado.
> * Por motivos de simplicidad, aquí no se muestran todos los pares clave-valor que devuelve el servicio. Para ver la lista de todos los campos admitidos y los tipos correspondientes, consulte nuestra página de concepto de [factura](../concept-invoice.md#field-extraction).

### <a name="choose-the-invoice-prebuilt-model-id"></a>Elección del identificador de modelo precompilado de factura

No está limitado a las facturas: hay varios modelos precompilados entre los que elegir, cada uno de los cuales tiene su propio conjunto de campos admitidos. El modelo que se va a usar para la operación de análisis depende del tipo de documento que se va a analizar. Estos son los identificadores de modelo de los modelos precompilados admitidos actualmente por el servicio Form Recognizer:

* [**prebuilt-invoice**](../concept-invoice.md): extrae texto, marcas de selección, tablas, pares clave-valor e información clave de las facturas.
* [**prebuilt-receipt**](../concept-receipt.md): extrae texto e información clave de los recibos.
* [**prebuilt-idDocument**](../concept-id-document.md): extrae texto e información clave de permisos de conducir y pasaportes internacionales.
* [**prebuilt-businessCard**](../concept-business-card.md): extrae texto e información clave de las tarjetas de presentación.

### <a name="add-the-following-code-to-your-prebuilt-invoice-application-below-the-apikey-variable"></a>Agregue el código siguiente a la aplicación de factura precompilada debajo de la variable `apiKey`

```javascript

const {PreBuiltModels} = require("@azure/ai-form-recognizer");

// Use of PrebuiltModels.Receipt above (rather than the raw model ID), adds strong typing of the model's output

const invoiceUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf";

async function main() {

    const client = new DocumentAnalysisClient(endpoint, new AzureKeyCredential(apiKey));

    const poller = await client.beginAnalyzeDocuments(PrebuiltModels.Invoice, invoiceUrl);

    const {
        documents: [result]
    } = await poller.pollUntilDone();

    if (result) {
        const invoice = result.fields;

        console.log("Vendor Name:", invoice.vendorName?.value);
        console.log("Customer Name:", invoice.customerName?.value);
        console.log("Invoice Date:", invoice.invoiceDate?.value);
        console.log("Due Date:", invoice.dueDate?.value);

        console.log("Items:");
        for (const {
                properties: item
            } of invoice.items?.values ?? []) {
            console.log("-", item.productCode?.value ?? "<no product code>");
            console.log("  Description:", item.description?.value);
            console.log("  Quantity:", item.quantity?.value);
            console.log("  Date:", item.date?.value);
            console.log("  Unit:", item.unit?.value);
            console.log("  Unit Price:", item.unitPrice?.value);
            console.log("  Tax:", item.tax?.value);
            console.log("  Amount:", item.amount?.value);
        }

        console.log("Subtotal:", invoice.subTotal?.value);
        console.log("Previous Unpaid Balance:", invoice.previousUnpaidBalance?.value);
        console.log("Tax:", invoice.totalTax?.value);
        console.log("Amount Due:", invoice.amountDue?.value);
    } else {
        throw new Error("Expected at least one receipt in the result.");
    }
}

main().catch((error) => {
    console.error("An error occurred:", error);
    process.exit(1);
});

```

## <a name="run-your-application"></a>Ejecución de la aplicación

1. Acceda a la carpeta en la que tiene la aplicación Form Recognizer (form-recognizer-app).

1. Escriba el siguiente comando en el terminal:

```console
node index.js
```

¡Enhorabuena! En este inicio rápido, ha usado Form Recognizer JavaScript SDK para analizar varios formularios de maneras diferentes. A continuación, explore la documentación de referencia para encontrar más información sobre la API de Form Recognizer v3.0.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Documentación de referencia de la versión v3.0 de la API REST](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)

> [!div class="nextstepaction"]
> [Biblioteca de referencia de JavaScript de Form Recognizer](https://azuresdkdocs.blob.core.windows.net/$web/javascript/azure-ai-form-recognizer/4.0.0-beta.1/index.html)
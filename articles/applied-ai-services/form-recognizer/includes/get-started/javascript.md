---
title: 'Inicio rápido: SDK de JavaScript de Form Recognizer v2.1'
titleSuffix: Azure Applied AI Services
description: Procesamiento de formularios y documentos, extracción de datos y análisis mediante la biblioteca cliente de JavaScript de Form Recognizer v2.1
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 11/02/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: f978333bdb10433f19831b5255010dacef7c81aa
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131030880"
---
> [!IMPORTANT]
>
> Este inicio rápido va dirigido a la API REST de Azure Form Recognizer **v2.1**.

[Documentación de referencia](/javascript/api/overview/azure/ai-form-recognizer-readme?view=azure-node-latest&preserve-view=true) | [Código fuente de la biblioteca](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/formrecognizer/ai-form-recognizer/) | [Paquete (npm)](https://www.npmjs.com/package/@azure/ai-form-recognizer) | [Ejemplos](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples)

En este inicio rápido, usará las siguientes API para extraer datos estructurados de formularios y documentos:

* [Diseño](#try-it-layout-model)

* [Factura](#try-it-prebuilt-model)

## <a name="prerequisites"></a>Prerrequisitos

* Una suscripción a Azure: [cree una cuenta gratuita](https://azure.microsoft.com/free/cognitive-services/).

* La versión más reciente de [Visual Studio Code](https://code.visualstudio.com/) o el IDE que prefiera.

* La versión de LTS más reciente para [Node.js](https://nodejs.org/about/releases/).

* Un recurso de Cognitive Services o Form Recognizer. Una vez que tenga la suscripción de Azure, cree un recurso de Form Recognizer de [servicio único](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) o de [varios servicios](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) en Azure Portal para obtener la clave y el punto de conexión. Puede usar el plan de tarifa gratis (`F0`) para probar el servicio y actualizarlo más adelante a un plan de pago para producción.

    > [!TIP]
    > Cree un recurso de Cognitive Services si tiene previsto acceder a varios servicios de Cognitive Services en un único punto de conexión o clave. Para acceder únicamente a Form Recognizer, cree un recurso de Form Recognizer. Tenga en cuenta que necesitará un recurso de servicio único si tiene previsto usar la [autenticación de Azure Active Directory](/azure/active-directory/authentication/overview-authentication).

* Una vez implementado el recurso, seleccione **Ir al recurso**. Necesitará la clave y el punto de conexión del recurso que ha creado para conectar la aplicación a la API de Form Recognizer. En una sección posterior de este mismo inicio rápido, pegará la clave y el punto de conexión en el código siguiente:

  :::image type="content" source="../../media/containers/keys-and-endpoint.png" alt-text="Captura de pantalla: claves y ubicación del punto de conexión en Azure Portal.":::

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
    npm install @azure/ai-form-recognizer
    ```

    el archivo `package.json` de la aplicación se actualizará con las dependencias.

1. Cree un archivo llamado `index.js`, ábralo e importe las bibliotecas siguientes:

    ```javascript
    const { FormRecognizerClient, AzureKeyCredential } = require("@azure/ai-form-recognizer");
    ```

1. Cree variables para el punto de conexión y la clave de Azure del recurso:

    ```javascript
    const apiKey = "PASTE_YOUR_FORM_RECOGNIZER_SUBSCRIPTION_KEY_HERE";
    const endpoint = "PASTE_YOUR_FORM_RECOGNIZER_ENDPOINT_HERE";
    ```

1. En este punto, la aplicación JavaScript debería contener las siguientes líneas de código:

    ```javascript

    const { FormRecognizerClient, AzureKeyCredential } = require("@azure/ai-form-recognizer");

    const endpoint = "PASTE_YOUR_FORM_RECOGNIZER_ENDPOINT_HERE";
    const apiKey = "PASTE_YOUR_FORM_RECOGNIZER_SUBSCRIPTION_KEY_HERE";
    ```

### <a name="select-a-code-sample-to-copy-and-paste-into-your-application"></a>Seleccione un ejemplo de código para copiar y pegar en la aplicación:

* [**Layout**](#try-it-layout-model)

* [**Factura precompilada**](#try-it-prebuilt-model)

> [!IMPORTANT]
>
> Recuerde quitar la clave del código cuando haya terminado y no hacerla nunca pública. En producción, use métodos seguros para almacenar y acceder a sus credenciales. Para más información, consulte el artículo Cognitive Services [security](.(/azure/cognitive-services/cognitive-services-security.md).

## <a name="try-it-layout-model"></a>**Pruébelo**: modelo de diseño

> [!div class="checklist"]
>
> * Para este ejemplo, necesitará un **archivo de documento de formulario en un identificador URI**. Puede usar nuestro [documento de formulario de ejemplo](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf) para este inicio rápido.
> * Se ha agregado el valor del URI del archivo a la variable `formUrl` cerca de la parte superior del archivo.
> * Para analizar un archivo determinado en un identificador URI, utilizará el método `beginRecognizeContent`.

### <a name="add-the-following-code-to-your-layout-application-on-the-line-below-the-apikey-variable"></a>Agregue el código siguiente a la aplicación de formato en la línea debajo de la variable `apiKey`

```javascript
const formUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf";

const formUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf";

async function recognizeContent() {
    const client = new FormRecognizerClient(endpoint, new AzureKeyCredential(apiKey));
    const poller = await client.beginRecognizeContentFromUrl(formUrl);
    const pages = await poller.pollUntilDone();

    if (!pages || pages.length === 0) {
        throw new Error("Expecting non-empty list of pages!");
    }

    for (const page of pages) {
        console.log(
            `Page ${page.pageNumber}: width ${page.width} and height ${page.height} with unit ${page.unit}`
        );
        for (const table of page.tables) {
            for (const cell of table.cells) {
                console.log(`cell [${cell.rowIndex},${cell.columnIndex}] has text ${cell.text}`);
            }
        }
    }
}

recognizeContent().catch((err) => {
    console.error("The sample encountered an error:", err);
});

```

## <a name="try-it-prebuilt-model"></a>**Probar**: modelo precompilado

En este ejemplo se muestra cómo analizar datos de determinados tipos de documentos comunes con modelos entrenados previamente, utilizando una factura como ejemplo. *Consulte* nuestra página de conceptos precompilados para ver una lista completa de [**campos de factura**](../../concept-invoice.md#field-extraction).

> [!div class="checklist"]
>
> * En este ejemplo, analizaremos un documento de factura mediante un modelo precompilado. Puede usar nuestro [documento de factura de ejemplo](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf) para este inicio rápido.
> * Se ha agregado el valor del URI del archivo a la variable `invoiceUrl` de la parte superior del archivo.
> * Para analizar un archivo determinado en un identificador URI, utilizará el método `beginRecognizeInvoices`.
> * Por motivos de simplicidad, aquí no se muestran todos los campos que devuelve el servicio. Para ver la lista de todos los campos admitidos y los tipos correspondientes, consulte nuestra página de concepto de [factura](../../concept-invoice.md#field-extraction).

### <a name="choose-a-prebuilt-model"></a>Elección de un modelo precompilado

No está limitado a las facturas: hay varios modelos precompilados entre los que elegir, cada uno de los cuales tiene su propio conjunto de campos admitidos. El modelo que se va a usar para la operación de análisis depende del tipo de documento que se va a analizar. Estos son los modelos precompilados que actualmente admite el servicio Form Recognizer:

* [**Factura**](../../concept-invoice.md): extrae texto, marcas de selección, tablas, campos e información clave de las facturas.
* [**Recibo**](../../concept-receipt.md): extrae texto e información clave de los recibos.
* [**Documento de identificación**](../../concept-id-document.md): extrae texto e información clave de permisos de conducir y pasaportes internacionales.
* [**Tarjeta de presentación**](../../concept-business-card.md): extrae texto e información clave de las tarjetas de presentación.

### <a name="add-the-following-code-to-your-prebuilt-invoice-application-below-the-apikey-variable"></a>Agregue el código siguiente a la aplicación de factura precompilada debajo de la variable `apiKey`

```javascript

const invoiceUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf";

async function recognizeInvoices() {

    const client = new FormRecognizerClient(endpoint, new AzureKeyCredential(apiKey));

    const poller = await client.beginRecognizeInvoicesFromUrl(invoiceUrl);
    const [invoice] = await poller.pollUntilDone();

    if (invoice === undefined) {
        throw new Error("Failed to extract data from at least one invoice.");
    }

    /**
     * This is a helper function for printing a simple field with an elemental type.
     */
    function fieldToString(field) {
        const {
            name,
            valueType,
            value,
            confidence
        } = field;
        return `${name} (${valueType}): '${value}' with confidence ${confidence}'`;
    }

    console.log("Invoice fields:");

    /**
     * Invoices contain a lot of optional fields, but they are all of elemental types
     * such as strings, numbers, and dates, so we will just enumerate them all.
     */
    for (const [name, field] of Object.entries(invoice.fields)) {
        if (field.valueType !== "array" && field.valueType !== "object") {
            console.log(`- ${name} ${fieldToString(field)}`);
        }
    }

    // Invoices also support nested line items, so we can iterate over them.
    let idx = 0;

    console.log("- Items:");

    const items = invoice.fields["Items"]?.value;
    for (const item of items ?? []) {
        const value = item.value;

        // Each item has several subfields that are nested within the item. We'll
        // map over this list of the subfields and filter out any fields that
        // weren't found. Not all fields will be returned every time, only those
        // that the service identified for the particular document in question.

        const subFields = [
                "Description",
                "Quantity",
                "Unit",
                "UnitPrice",
                "ProductCode",
                "Date",
                "Tax",
                "Amount"
            ]
            .map((fieldName) => value[fieldName])
            .filter((field) => field !== undefined);

        console.log(
            [
                `  - Item #${idx}`,
                // Now we will convert those fields into strings to display
                ...subFields.map((field) => `    - ${fieldToString(field)}`)
            ].join("\n")
        );
    }
}

recognizeInvoices().catch((err) => {
    console.error("The sample encountered an error:", err);
});

```

¡Enhorabuena! En este inicio rápido, ha usado Form Recognizer JavaScript SDK para analizar varios formularios de maneras diferentes. A continuación, explore la documentación de referencia para encontrar más información sobre la API de Form Recognizer v3.0.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Documentación de referencia de la API REST v2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9843c2794cbb1a96291)

> [!div class="nextstepaction"]
> [Biblioteca de referencia de JavaScript de Form Recognizer](/javascript/api/overview/azure/ai-form-recognizer-readme?view=azure-node-latest&preserve-view=true)

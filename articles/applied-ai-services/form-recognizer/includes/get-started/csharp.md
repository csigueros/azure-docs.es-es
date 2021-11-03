---
title: 'Introducción: Biblioteca cliente de Form Recognizer para .NET v2.1'
description: Use el SDK de Form Recognizer para .NET para crear una aplicación de procesamiento de formularios que extraiga pares clave-valor y datos de tabla de los documentos personalizados.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 11/02/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: bface4c7df450e3123f8e4be73e5c942ff95a8cc
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131030889"
---
<!-- markdownlint-disable MD024 -->

<!-- markdownlint-disable MD033 -->
> [!IMPORTANT]
>
> Este inicio rápido va dirigido a la API REST de Azure Form Recognizer, versión **2.1** que usa cURL para ejecutar llamadas API REST.
>
>* Por simplicidad, en el código de este artículo se usan métodos sincrónicos y almacenamiento de credenciales no protegidas.

[Documentación de referencia](/dotnet/api/overview/azure/ai.formrecognizer-readme) | [Código fuente de la biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/src) | [Paquete (NuGet)](https://www.nuget.org/packages/Azure.AI.FormRecognizer) | [Ejemplos](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md)

En este inicio rápido, usará las siguientes API para extraer datos estructurados de formularios y documentos:

* [Diseño](#try-it-layout-model)

* [Factura](#try-it-prebuilt-model)

## <a name="prerequisites"></a>Prerrequisitos

* Una suscripción a Azure: [cree una cuenta gratuita](https://azure.microsoft.com/free/cognitive-services/).

* Versión actual del [IDE de Visual Studio](https://visualstudio.microsoft.com/vs/). <!-- or [.NET Core](https://dotnet.microsoft.com/download). -->

* Un recurso de Cognitive Services o Form Recognizer. Una vez que tenga la suscripción de Azure, cree un recurso de Form Recognizer de [servicio único](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) o de [varios servicios](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) en Azure Portal para obtener la clave y el punto de conexión. Puede usar el plan de tarifa gratis (`F0`) para probar el servicio y actualizarlo más adelante a un plan de pago para producción.

    > [!TIP]
    > Cree un recurso de Cognitive Services si tiene previsto acceder a varios servicios de Cognitive Services en un único punto de conexión o clave. Para acceder únicamente a Form Recognizer, cree un recurso de Form Recognizer. Tenga en cuenta que necesitará un recurso de servicio único si tiene previsto usar la [autenticación de Azure Active Directory](/azure/active-directory/authentication/overview-authentication).

* Una vez que se implemente el recurso, haga clic en **Ir al recurso**. Necesitará la clave y el punto de conexión del recurso que ha creado para conectar la aplicación a la API de Form Recognizer. En una sección posterior de este mismo inicio rápido, pegará la clave y el punto de conexión en el código siguiente:

  :::image type="content" source="../../media/containers/keys-and-endpoint.png" alt-text="Captura de pantalla: claves y ubicación del punto de conexión en Azure Portal.":::

## <a name="set-up"></a>Configurar

1. Inicie Visual Studio 2019.

1. En la página de inicio, elija Crear un proyecto nuevo.

    :::image type="content" source="../../media/quickstarts/start-window.png" alt-text="Captura de pantalla: ventana de inicio de Visual Studio.":::

1. En la página **Crear un proyecto**, escriba **consola** en el cuadro de búsqueda. Elija la plantilla **Aplicación de consola** y, a continuación, seleccione **Siguiente**.

    :::image type="content" source="../../media/quickstarts/create-new-project.png" alt-text="Captura de pantalla: página Crear un proyecto de Visual Studio.":::

1. En la ventana de diálogo **Configure su nuevo proyecto**, escriba `formRecognizer_quickstart` en el cuadro Nombre de proyecto. Después, haga clic en Siguiente.

    :::image type="content" source="../../media/quickstarts/configure-new-project.png" alt-text="Captura de pantalla: ventana de diálogo Configure su nuevo proyecto de Visual Studio.":::

1. En la ventana de diálogo **Información adicional**, seleccione **.NET 5.0 (Actual)** y, a continuación, seleccione **Crear**.

    :::image type="content" source="../../media/quickstarts/additional-information.png" alt-text="Captura de pantalla: ventana de diálogo de información adicional de Visual Studio.":::

### <a name="install-the-client-library-with-nuget"></a>Instalación de la biblioteca cliente con NuGet

 1. Haga clic con el botón derecho en el proyecto **formRecognizer_quickstart** y seleccione **Administrar paquetes NuGet...** .

    :::image type="content" source="../../media/quickstarts/select-nuget-package.png" alt-text="Captura de pantalla: select-nuget-package.png":::

 1. Seleccione la pestaña Examinar y escriba Azure.AI.FormRecognizer.

     :::image type="content" source="../../media/quickstarts/azure-nuget-package.png" alt-text="Captura de pantalla: select-form-recognizer-package.png":::

 1. Seleccione la versión **3.1.1** del menú desplegable y elija **Instalar**.

## <a name="build-your-application"></a>Compilación de la aplicación

Para interactuar con el servicio Form Recognizer, tiene que crear una instancia de la clase `FormRecognizerClient`. Para ello, creará un elemento `AzureKeyCredential` con el elemento apiKey y una instancia de `FormRecognizerClient` con el elemento `AzureKeyCredential` y el elemento `endpoint` de Form Recognizer.

1. Abra el archivo **Program.cs**.

1. Agregue las siguientes directivas using:

```csharp
using System;
using Azure;
using Azure.AI.FormRecognizer;
using Azure.AI.FormRecognizer.Models;
using System.Threading.Tasks;
```

1. Establezca las variables de entorno `endpoint` y `apiKey`, y cree la instancia de `AzureKeyCredential` y `FormRecognizerClient`:

```csharp
private static readonly string endpoint = "your-form-recognizer-endpoint";
private static readonly string apiKey = "your-api-key";
private static readonly AzureKeyCredential credential = new AzureKeyCredential(apiKey);
```

1. Elimine la línea `Console.Writeline("Hello World!");` y agregue uno de los ejemplos de código **Probar** al método **Main** en el archivo **Program.cs**:

    :::image type="content" source="../../media/quickstarts/add-code-here.png" alt-text="Captura de pantalla: adición del código de ejemplo al método Main.":::

1. Seleccione un ejemplo de código para copiar y pegar en el método main de la aplicación:

    * [**Layout**](#try-it-layout-model)

    * [**Factura precompilada**](#try-it-prebuilt-model)

> [!IMPORTANT]
>
> Recuerde quitar la clave del código cuando haya terminado y no hacerla nunca pública. En producción, use métodos seguros para almacenar y acceder a sus credenciales. Para más información, consulte el artículo Cognitive Services [security](.(/azure/cognitive-services/cognitive-services-security.md).

## <a name="try-it-layout-model"></a>**Pruébelo**: modelo de diseño

Extraiga de los documentos texto, marcas de selección, estilos de texto y estructuras de tablas, junto con las coordenadas de su región delimitadora.

> [!div class="checklist"]
>
> * Para este ejemplo, necesitará un **archivo de documento de formulario en un identificador URI**. Puede usar nuestro [documento de formulario de ejemplo](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf) para este inicio rápido.
> * Se ha agregado el valor del URI del archivo a la variable `formUri`.
> * Para extraer el diseño de un archivo determinado en un identificador URI, use el método `StartRecognizeContentFromUriAsync`.

### <a name="add-the-following-code-to-your-layout-application-main-method"></a>Agregue el código siguiente al método **Main** de la aplicación de diseño:

```csharp

FormRecognizerClient recognizerClient = AuthenticateClient();

Task recognizeContent = RecognizeContent(recognizerClient);
Task.WaitAll(recognizeContent);
```

### <a name="add-the-following-code-below-the-main-method"></a>Agregue el siguiente código debajo del método **main**:

```csharp

private static FormRecognizerClient AuthenticateClient()
            {
                var credential = new AzureKeyCredential(apiKey);
                var client = new FormRecognizerClient(new Uri(endpoint), credential);
                return client;
            }

            private static async Task RecognizeContent(FormRecognizerClient recognizerClient)
        {
            string formUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf";
            FormPageCollection formPages = await recognizerClient
        .StartRecognizeContentFromUri(new Uri(formUrl))
        .WaitForCompletionAsync();

            foreach (FormPage page in formPages)
            {
                Console.WriteLine($"Form Page {page.PageNumber} has {page.Lines.Count} lines.");

                for (int i = 0; i < page.Lines.Count; i++)
                {
                    FormLine line = page.Lines[i];
                    Console.WriteLine($"    Line {i} has {line.Words.Count} word{(line.Words.Count > 1 ? "s" : "")}, and text: '{line.Text}'.");
                }

                for (int i = 0; i < page.Tables.Count; i++)
                {
                    FormTable table = page.Tables[i];
                    Console.WriteLine($"Table {i} has {table.RowCount} rows and {table.ColumnCount} columns.");
                    foreach (FormTableCell cell in table.Cells)
                    {
                        Console.WriteLine($"    Cell ({cell.RowIndex}, {cell.ColumnIndex}) contains text: '{cell.Text}'.");
                    }
                }
            }
        }
    }
}

```

## <a name="try-it-prebuilt-model"></a>**Probar**: modelo precompilado

En este ejemplo se muestra cómo analizar datos de determinados tipos de documentos comunes con modelos entrenados previamente, utilizando una factura como ejemplo.

> [!div class="checklist"]
>
> * En este ejemplo, analizaremos un documento de factura mediante un modelo precompilado. Puede usar nuestro [documento de factura de ejemplo](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf) para este inicio rápido.
> * Se ha agregado el valor del URI del archivo a la variable `invoiceUri` en la parte superior del método main.
> * Para analizar un archivo determinado en un URI, use el método `StartRecognizeInvoicesFromUriAsync`.
> * Por motivos de simplicidad, aquí no se muestran todos los campos que devuelve el servicio. Para ver la lista de todos los campos admitidos y los tipos correspondientes, consulte nuestra página de concepto de [factura](../../concept-invoice.md#field-extraction).

### <a name="choose-a-prebuilt-model"></a>Elección de un modelo precompilado

No está limitado a las facturas: hay varios modelos precompilados entre los que elegir, cada uno de los cuales tiene su propio conjunto de campos admitidos. El modelo que se va a usar para la operación de análisis depende del tipo de documento que se va a analizar. Estos son los modelos precompilados que actualmente admite el servicio Form Recognizer:

* [**Factura**](../../concept-invoice.md): extrae texto, marcas de selección, tablas, campos e información clave de las facturas.
* [**Recibo**](../../concept-receipt.md): extrae texto e información clave de los recibos.
* [**Documento de identificación**](../../concept-id-document.md): extrae texto e información clave de permisos de conducir y pasaportes internacionales.
* [**Tarjeta de presentación**](../../concept-business-card.md): extrae texto e información clave de las tarjetas de presentación.

### <a name="add-the-following-code-to-your-prebuilt-invoice-application-main-method"></a>Agregue el código siguiente al método **Main** de la aplicación de factura pregenerada.

```csharp
FormRecognizerClient recognizerClient = AuthenticateClient();

            Task analyzeinvoice = AnalyzeInvoice(recognizerClient, invoiceUrl);
            Task.WaitAll(analyzeinvoice);
```

### <a name="add-the-following-code-below-the-main-method"></a>Agregue el siguiente código debajo del método **main**:

```csharp
   private static FormRecognizerClient AuthenticateClient() {
     var credential = new AzureKeyCredential(apiKey);
     var client = new FormRecognizerClient(new Uri(endpoint), credential);
     return client;
   }

   static string invoiceUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf";

   private static async Task AnalyzeInvoice(FormRecognizerClient recognizerClient, string invoiceUrl) {
     var options = new RecognizeInvoicesOptions() {
       Locale = "en-US"
     };
     RecognizedFormCollection invoices = await recognizerClient.StartRecognizeInvoicesFromUriAsync(new Uri(invoiceUrl), options).WaitForCompletionAsync();

     RecognizedForm invoice = invoices[0];

     FormField invoiceIdField;
     if (invoice.Fields.TryGetValue("InvoiceId", out invoiceIdField)) {
       if (invoiceIdField.Value.ValueType == FieldValueType.String) {
         string invoiceId = invoiceIdField.Value.AsString();
         Console.WriteLine($ "    Invoice Id: '{invoiceId}', with confidence {invoiceIdField.Confidence}");
       }
     }

     FormField invoiceDateField;
     if (invoice.Fields.TryGetValue("InvoiceDate", out invoiceDateField)) {
       if (invoiceDateField.Value.ValueType == FieldValueType.Date) {
         DateTime invoiceDate = invoiceDateField.Value.AsDate();
         Console.WriteLine($ "    Invoice Date: '{invoiceDate}', with confidence {invoiceDateField.Confidence}");
       }
     }

     FormField dueDateField;
     if (invoice.Fields.TryGetValue("DueDate", out dueDateField)) {
       if (dueDateField.Value.ValueType == FieldValueType.Date) {
         DateTime dueDate = dueDateField.Value.AsDate();
         Console.WriteLine($ "    Due Date: '{dueDate}', with confidence {dueDateField.Confidence}");
       }
     }

     FormField vendorNameField;
     if (invoice.Fields.TryGetValue("VendorName", out vendorNameField)) {
       if (vendorNameField.Value.ValueType == FieldValueType.String) {
         string vendorName = vendorNameField.Value.AsString();
         Console.WriteLine($ "    Vendor Name: '{vendorName}', with confidence {vendorNameField.Confidence}");
       }
     }

     FormField vendorAddressField;
     if (invoice.Fields.TryGetValue("VendorAddress", out vendorAddressField)) {
       if (vendorAddressField.Value.ValueType == FieldValueType.String) {
         string vendorAddress = vendorAddressField.Value.AsString();
         Console.WriteLine($ "    Vendor Address: '{vendorAddress}', with confidence {vendorAddressField.Confidence}");
       }
     }

     FormField customerNameField;
     if (invoice.Fields.TryGetValue("CustomerName", out customerNameField)) {
       if (customerNameField.Value.ValueType == FieldValueType.String) {
         string customerName = customerNameField.Value.AsString();
         Console.WriteLine($ "    Customer Name: '{customerName}', with confidence {customerNameField.Confidence}");
       }
     }

     FormField customerAddressField;
     if (invoice.Fields.TryGetValue("CustomerAddress", out customerAddressField)) {
       if (customerAddressField.Value.ValueType == FieldValueType.String) {
         string customerAddress = customerAddressField.Value.AsString();
         Console.WriteLine($ "    Customer Address: '{customerAddress}', with confidence {customerAddressField.Confidence}");
       }
     }

     FormField customerAddressRecipientField;
     if (invoice.Fields.TryGetValue("CustomerAddressRecipient", out customerAddressRecipientField)) {
       if (customerAddressRecipientField.Value.ValueType == FieldValueType.String) {
         string customerAddressRecipient = customerAddressRecipientField.Value.AsString();
         Console.WriteLine($ "    Customer address recipient: '{customerAddressRecipient}', with confidence {customerAddressRecipientField.Confidence}");
       }
     }

     FormField invoiceTotalField;
     if (invoice.Fields.TryGetValue("InvoiceTotal", out invoiceTotalField)) {
       if (invoiceTotalField.Value.ValueType == FieldValueType.Float) {
         float invoiceTotal = invoiceTotalField.Value.AsFloat();
         Console.WriteLine($ "    Invoice Total: '{invoiceTotal}', with confidence {invoiceTotalField.Confidence}");
       }
     }
   }
 }
}
```

## <a name="run-your-application"></a>Ejecución de la aplicación

Elija el botón verde **Inicio** situado junto a formRecognizer_quickstart para compilar y ejecutar el programa, o pulse **F5**.

  :::image type="content" source="../../media/quickstarts/run-visual-studio.png" alt-text="Captura de pantalla: ejecución del programa en Visual Studio.":::

<!-- --- -->

¡Enhorabuena! En este inicio rápido, ha usado el SDK de Form Recognizer para C# para analizar varios formularios y documentos de maneras diferentes. A continuación, explore la documentación de referencia para encontrar más información sobre la API de Form Recognizer.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Documentación de referencia de la API REST v2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9843c2794cbb1a96291)

> [!div class="nextstepaction"]
> [Biblioteca de referencia de Form Recognizer](/dotnet/api/overview/azure/AI.FormRecognizer-readme)

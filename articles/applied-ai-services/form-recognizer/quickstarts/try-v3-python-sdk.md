---
title: 'Inicio rápido: SDK de Form Recognizer para Python v3.0 | versión preliminar'
titleSuffix: Azure Applied AI Services
description: Procesamiento de formularios y documentos, extracción de datos y análisis mediante el SDK de biblioteca cliente de Form Recognizer para Python v3.0 (versión preliminar)
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 11/02/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: b2383b52184d4cdd66aaeebe2ec34c1ed4b62a1d
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2021
ms.locfileid: "132371702"
---
# <a name="quickstart-python-client-library-sdk-v30--preview"></a>Inicio rápido: SDK v3.0 de la biblioteca cliente de Python | Versión preliminar

>[!NOTE]
> Form Recognizer v3.0 está actualmente en versión preliminar pública. Es posible que algunas características no se admitan o que tengan funcionalidades limitadas. 

[Documentación de referencia](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-formrecognizer/latest/azure.ai.formrecognizer.html) | [Código fuente de la biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/formrecognizer/azure-ai-formrecognizer/azure/ai/formrecognizer) | [Paquete (PyPi)](https://pypi.org/project/azure-ai-formrecognizer/) | [Ejemplos](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/formrecognizer/azure-ai-formrecognizer/samples)

 Comience a usar Azure Form Recognizer mediante el lenguaje de programación Python. Azure Form Recognizer es un componente de Azure Applied AI Services en la nube que usa el aprendizaje automático para extraer y analizar campos de formulario, texto y tablas de los documentos. Puede llamar fácilmente a los modelos de Form Recognizer mediante la integración de los SDK de biblioteca cliente en los flujos de trabajo y aplicaciones. Se recomienda usar el servicio gratuito cuando se está aprendiendo la tecnología. Recuerde que el número de páginas gratuitas se limita a 500 al mes.

Para más información sobre las características y las opciones de desarrollo de Form Recognizer, visite nuestra página de [información general](../overview.md#form-recognizer-features-and-development-options).

En este inicio rápido, usará las siguientes características para analizar y extraer datos y valores de formularios y documentos:

* [🆕 **Documento general**](#try-it-general-document-model): análisis y extracción de texto, tablas, estructuras, pares clave-valor y entidades con nombre.

* [**Diseño**](#try-it-layout-model): análisis y extracción de tablas, líneas, palabras y marcas de selección, como botones de radio y casillas en documentos de formularios, sin necesidad de entrenar un modelo.

* [**Factura precompilada**](#try-it-prebuilt-model): análisis y extracción de campos comunes de facturas mediante un modelo de facturas entrenado previamente.

## <a name="prerequisites"></a>Prerrequisitos

* Una suscripción a Azure: [cree una cuenta gratuita](https://azure.microsoft.com/free/cognitive-services)

* [Python 3.x](https://www.python.org/)

  * La instalación de Python debe incluir [pip](https://pip.pypa.io/en/stable/). Puede comprobar si tiene pip instalado mediante la ejecución de `pip --version` en la línea de comandos. Para obtener pip, instale la versión más reciente de Python.

* Un recurso de Cognitive Services o Form Recognizer. Una vez que tenga la suscripción de Azure, cree un recurso de Form Recognizer de [servicio único](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) o de [varios servicios](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) en Azure Portal para obtener la clave y el punto de conexión. Puede usar el plan de tarifa gratis (`F0`) para probar el servicio y actualizarlo más adelante a un plan de pago para producción.

> [!TIP]
> Cree un recurso de Cognitive Services si tiene previsto acceder a varios servicios de Cognitive Services en un único punto de conexión o clave. Para acceder únicamente a Form Recognizer, cree un recurso de Form Recognizer. Tenga en cuenta que necesitará un recurso de servicio único si tiene previsto usar la [autenticación de Azure Active Directory](../../../active-directory/authentication/overview-authentication.md).

* Una vez implementado el recurso, seleccione **Ir al recurso**. Necesitará la clave y el punto de conexión del recurso que ha creado para conectar la aplicación a la API de Form Recognizer. En una sección posterior de este inicio rápido, pegará la clave y el punto de conexión en el código siguiente:

  :::image type="content" source="../media/containers/keys-and-endpoint.png" alt-text="Captura de pantalla: claves y ubicación del punto de conexión en Azure Portal.":::

## <a name="set-up"></a>Configurar

Abra una ventana de terminal en el entorno local e instale la biblioteca cliente de Azure Form Recognizer para Python con pip:

```console
pip install azure-ai-formrecognizer --pre

```

### <a name="create-a-new-python-application"></a>Creación de una nueva aplicación de Python

Cree una aplicación de Python y asígnele el nombre **form_recognizer_quickstart.py** en el editor o IDE que prefiera. A continuación, importe las bibliotecas siguientes:

```python
import os
from azure.core.exceptions import ResourceNotFoundError
from azure.ai.formrecognizer import DocumentAnalysisClient
from azure.core.credentials import AzureKeyCredential
```

### <a name="create-variables-for-your-azure-resource-endpoint-and-key"></a>Cree variables para el punto de conexión y la clave del recurso de Azure

```python
endpoint = "YOUR_FORM_RECOGNIZER_ENDPOINT"
key = "YOUR_FORM_RECOGNIZER_SUBSCRIPTION_KEY"
```

En este momento, la aplicación Python debe contener las siguientes líneas de código:

```python
import os
from azure.core.exceptions import ResourceNotFoundError
from azure.ai.formrecognizer import DocumentAnalysisClient
from azure.core.credentials import AzureKeyCredential

endpoint = "YOUR_FORM_RECOGNIZER_ENDPOINT"
key = "YOUR_FORM_RECOGNIZER_SUBSCRIPTION_KEY"

```

### <a name="select-a-code-sample-to-copy-and-paste-into-your-application"></a>Seleccione un ejemplo de código para copiar y pegar en la aplicación:

* [**Documento general**](#try-it-general-document-model)

* [**Layout**](#try-it-layout-model)

* [**Factura precompilada**](#try-it-prebuilt-model)

> [!IMPORTANT]
>
> Recuerde quitar la clave del código cuando haya terminado y no hacerla nunca pública. En producción, use métodos seguros para almacenar y acceder a sus credenciales. Para más información, consulte el artículo sobre la [seguridad](../../../cognitive-services/cognitive-services-security.md) de Cognitive Services.

## <a name="try-it-general-document-model"></a>**Pruébelo**: modelo de documento general

> [!div class="checklist"]
>
> * Para este ejemplo, necesitará un **archivo de documento de formulario en un identificador URI**. Puede usar nuestro [documento de formulario de ejemplo](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf) para este inicio rápido.
> * Para analizar un archivo determinado de un URI, usará el método `begin_analyze_document` y transferirá `prebuilt-document` como identificador del modelo. El valor devuelto es un objeto `result` que contiene datos sobre el documento enviado.
> * Se ha agregado el valor del URI del archivo a la variable `formUrl` cerca de la parte superior del archivo.
> * Por motivos de simplicidad, aquí no se muestran todos los campos de entidad que devuelve el servicio. Para ver la lista de todos los campos admitidos y los tipos correspondientes, consulte nuestra página de concepto del [documento general](../concept-general-document.md#named-entity-recognition-ner-categories).

### <a name="add-the-following-code-to-your-general-document-application-on-the-line-below-the-key-variable"></a>Agregue el código siguiente a la aplicación de documento general en la línea debajo de la variable `key`

```python

def format_bounding_region(bounding_regions):
    if not bounding_regions:
        return "N/A"
    return ", ".join("Page #{}: {}".format(region.page_number, format_bounding_box(region.bounding_box)) for region in bounding_regions)

def format_bounding_box(bounding_box):
    if not bounding_box:
        return "N/A"
    return ", ".join(["[{}, {}]".format(p.x, p.y) for p in bounding_box])


def analyze_general_documents():

    formUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf"

    document_analysis_client = DocumentAnalysisClient(
        endpoint=endpoint, credential=AzureKeyCredential(key)
    )

    poller = document_analysis_client.begin_analyze_document_from_url(
            "prebuilt-document", formUrl)
    result = poller.result()

    for style in result.styles:
        if style.is_handwritten:
            print("Document contains handwritten content: ")
            print(",".join([result.content[span.offset:span.offset + span.length] for span in style.spans]))

    print("----Key-value pairs found in document----")
    for kv_pair in result.key_value_pairs:
        if kv_pair.key:
            print(
                    "Key '{}' found within '{}' bounding regions".format(
                        kv_pair.key.content,
                        format_bounding_region(kv_pair.key.bounding_regions),
                    )
                )
        if kv_pair.value:
            print(
                    "Value '{}' found within '{}' bounding regions\n".format(
                        kv_pair.value.content,
                        format_bounding_region(kv_pair.value.bounding_regions),
                    )
                )

    print("----Entities found in document----")
    for entity in result.entities:
        print("Entity of category '{}' with sub-category '{}'".format(entity.category, entity.sub_category))
        print("...has content '{}'".format(entity.content))
        print("...within '{}' bounding regions".format(format_bounding_region(entity.bounding_regions)))
        print("...with confidence {}\n".format(entity.confidence))

    for page in result.pages:
        print("----Analyzing document from page #{}----".format(page.page_number))
        print(
            "Page has width: {} and height: {}, measured with unit: {}".format(
                page.width, page.height, page.unit
            )
        )

        for line_idx, line in enumerate(page.lines):
            print(
                "...Line # {} has text content '{}' within bounding box '{}'".format(
                    line_idx,
                    line.content,
                    format_bounding_box(line.bounding_box),
                )
            )

        for word in page.words:
            print(
                "...Word '{}' has a confidence of {}".format(
                    word.content, word.confidence
                )
            )

        for selection_mark in page.selection_marks:
            print(
                "...Selection mark is '{}' within bounding box '{}' and has a confidence of {}".format(
                    selection_mark.state,
                    format_bounding_box(selection_mark.bounding_box),
                    selection_mark.confidence,
                )
            )

    for table_idx, table in enumerate(result.tables):
        print(
            "Table # {} has {} rows and {} columns".format(
                table_idx, table.row_count, table.column_count
            )
        )
        for region in table.bounding_regions:
            print(
                "Table # {} location on page: {} is {}".format(
                    table_idx,
                    region.page_number,
                    format_bounding_box(region.bounding_box),
                )
            )
        for cell in table.cells:
            print(
                "...Cell[{}][{}] has content '{}'".format(
                    cell.row_index,
                    cell.column_index,
                    cell.content,
                )
            )
            for region in cell.bounding_regions:
                print(
                    "...content on page {} is within bounding box '{}'\n".format(
                        region.page_number,
                        format_bounding_box(region.bounding_box),
                    )
                )
    print("----------------------------------------")


if __name__ == "__main__":
    analyze_general_documents()
```

## <a name="try-it-layout-model"></a>**Pruébelo**: modelo de diseño

> [!div class="checklist"]
>
> * Para este ejemplo, necesitará un **archivo de documento de formulario en un identificador URI**. Puede usar nuestro [documento de formulario de ejemplo](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf) para este inicio rápido.
> * Se ha agregado el valor del URI del archivo a la variable `formUrl` cerca de la parte superior del archivo.
> * Para analizar un archivo determinado de un URI, usará el método `begin_analyze_document` y transferirá `prebuilt-layout` como identificador del modelo. El valor devuelto es un objeto `result` que contiene datos sobre el documento enviado.

### <a name="add-the-following-code-to-your-layout-application-on-the-line-below-the-key-variable"></a>Agregue el código siguiente a la aplicación de formato en la línea debajo de la variable `key`

```python

def format_bounding_box(bounding_box):
    if not bounding_box:
        return "N/A"
    return ", ".join(["[{}, {}]".format(p.x, p.y) for p in bounding_box])


def analyze_layout():
    # sample form document
    formUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf"

    document_analysis_client = DocumentAnalysisClient(
        endpoint=endpoint, credential=AzureKeyCredential(key)
    )

    poller = document_analysis_client.begin_analyze_document_from_url(
            "prebuilt-layout", formUrl)
    result = poller.result()

    for idx, style in enumerate(result.styles):
        print(
            "Document contains {} content".format(
                "handwritten" if style.is_handwritten else "no handwritten"
            )
        )

```

## <a name="try-it-prebuilt-model"></a>**Probar**: modelo precompilado

En este ejemplo se muestra cómo analizar datos procedentes de algunos tipos de documentos comunes con un modelo previamente entrenado. Por ejemplo, utilizando una factura.

> [!div class="checklist"]
>
> * En este ejemplo, analizaremos un documento de factura mediante un modelo precompilado. Puede usar nuestro [documento de factura de ejemplo](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf) para este inicio rápido.
> * Se ha agregado el valor del URI del archivo a la variable `string fileUri` de la parte superior del archivo.
> * Para analizar un archivo determinado de un URI, usará el método `begin_analyze_document` y transferirá `prebuilt-invoice` como identificador del modelo. El valor devuelto es un objeto `result` que contiene datos sobre el documento enviado.
> * Por motivos de simplicidad, aquí no se muestran todos los pares clave-valor que devuelve el servicio. Para ver la lista de todos los campos admitidos y los tipos correspondientes, consulte nuestra página de concepto de [factura](../concept-invoice.md#field-extraction).

### <a name="choose-the-invoice-prebuilt-model-id"></a>Elección del identificador de modelo precompilado de factura

No está limitado a las facturas: hay varios modelos precompilados entre los que elegir, cada uno de los cuales tiene su propio conjunto de campos admitidos. El modelo que se va a usar para la operación de análisis depende del tipo de documento que se va a analizar. Estos son los identificadores de modelo de los modelos precompilados admitidos actualmente por el servicio Form Recognizer:

* [**prebuilt-invoice**](../concept-invoice.md): extrae texto, marcas de selección, tablas, pares clave-valor e información clave de las facturas.
* [**prebuilt-receipt**](../concept-receipt.md): extrae texto e información clave de los recibos.
* [**prebuilt-idDocument**](../concept-id-document.md): extrae texto e información clave de permisos de conducir y pasaportes internacionales.
* [**prebuilt-businessCard**](../concept-business-card.md): extrae texto e información clave de las tarjetas de presentación.

### <a name="add-the-following-code-to-your-prebuilt-invoice-application-below-the-key-variable"></a>Agregue el código siguiente a la aplicación de factura precompilada debajo de la variable `key`

```python

def format_bounding_region(bounding_regions):
    if not bounding_regions:
        return "N/A"
    return ", ".join("Page #{}: {}".format(region.page_number, format_bounding_box(region.bounding_box)) for region in bounding_regions)

def format_bounding_box(bounding_box):
    if not bounding_box:
        return "N/A"
    return ", ".join(["[{}, {}]".format(p.x, p.y) for p in bounding_box])


def analyze_invoice():

    formUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf"

    document_analysis_client = DocumentAnalysisClient(
        endpoint=endpoint, credential=AzureKeyCredential(key)
    )

    poller = document_analysis_client.begin_analyze_document_from_url(
            "prebuilt-invoice", formUrl)
    invoices = poller.result()

    for idx, invoice in enumerate(invoices.documents):
        print("--------Recognizing invoice #{}--------".format(idx + 1))
        vendor_name = invoice.fields.get("VendorName")
        if vendor_name:
            print(
                "Vendor Name: {} has confidence: {}".format(
                    vendor_name.value, vendor_name.confidence
                )
            )
        vendor_address = invoice.fields.get("VendorAddress")
        if vendor_address:
            print(
                "Vendor Address: {} has confidence: {}".format(
                    vendor_address.value, vendor_address.confidence
                )
            )
        vendor_address_recipient = invoice.fields.get("VendorAddressRecipient")
        if vendor_address_recipient:
            print(
                "Vendor Address Recipient: {} has confidence: {}".format(
                    vendor_address_recipient.value, vendor_address_recipient.confidence
                )
            )
        customer_name = invoice.fields.get("CustomerName")
        if customer_name:
            print(
                "Customer Name: {} has confidence: {}".format(
                    customer_name.value, customer_name.confidence
                )
            )
        customer_id = invoice.fields.get("CustomerId")
        if customer_id:
            print(
                "Customer Id: {} has confidence: {}".format(
                    customer_id.value, customer_id.confidence
                )
            )
        customer_address = invoice.fields.get("CustomerAddress")
        if customer_address:
            print(
                "Customer Address: {} has confidence: {}".format(
                    customer_address.value, customer_address.confidence
                )
            )
        customer_address_recipient = invoice.fields.get("CustomerAddressRecipient")
        if customer_address_recipient:
            print(
                "Customer Address Recipient: {} has confidence: {}".format(
                    customer_address_recipient.value,
                    customer_address_recipient.confidence,
                )
            )
        invoice_id = invoice.fields.get("InvoiceId")
        if invoice_id:
            print(
                "Invoice Id: {} has confidence: {}".format(
                    invoice_id.value, invoice_id.confidence
                )
            )
        invoice_date = invoice.fields.get("InvoiceDate")
        if invoice_date:
            print(
                "Invoice Date: {} has confidence: {}".format(
                    invoice_date.value, invoice_date.confidence
                )
            )
        invoice_total = invoice.fields.get("InvoiceTotal")
        if invoice_total:
            print(
                "Invoice Total: {} has confidence: {}".format(
                    invoice_total.value, invoice_total.confidence
                )
            )
        due_date = invoice.fields.get("DueDate")
        if due_date:
            print(
                "Due Date: {} has confidence: {}".format(
                    due_date.value, due_date.confidence
                )
            )
        purchase_order = invoice.fields.get("PurchaseOrder")
        if purchase_order:
            print(
                "Purchase Order: {} has confidence: {}".format(
                    purchase_order.value, purchase_order.confidence
                )
            )
        billing_address = invoice.fields.get("BillingAddress")
        if billing_address:
            print(
                "Billing Address: {} has confidence: {}".format(
                    billing_address.value, billing_address.confidence
                )
            )
        billing_address_recipient = invoice.fields.get("BillingAddressRecipient")
        if billing_address_recipient:
            print(
                "Billing Address Recipient: {} has confidence: {}".format(
                    billing_address_recipient.value,
                    billing_address_recipient.confidence,
                )
            )
        shipping_address = invoice.fields.get("ShippingAddress")
        if shipping_address:
            print(
                "Shipping Address: {} has confidence: {}".format(
                    shipping_address.value, shipping_address.confidence
                )
            )
        shipping_address_recipient = invoice.fields.get("ShippingAddressRecipient")
        if shipping_address_recipient:
            print(
                "Shipping Address Recipient: {} has confidence: {}".format(
                    shipping_address_recipient.value,
                    shipping_address_recipient.confidence,
                )
            )
        print("Invoice items:")
        for idx, item in enumerate(invoice.fields.get("Items").value):
            print("...Item #{}".format(idx + 1))
            item_description = item.value.get("Description")
            if item_description:
                print(
                    "......Description: {} has confidence: {}".format(
                        item_description.value, item_description.confidence
                    )
                )
            item_quantity = item.value.get("Quantity")
            if item_quantity:
                print(
                    "......Quantity: {} has confidence: {}".format(
                        item_quantity.value, item_quantity.confidence
                    )
                )
            unit = item.value.get("Unit")
            if unit:
                print(
                    "......Unit: {} has confidence: {}".format(
                        unit.value, unit.confidence
                    )
                )
            unit_price = item.value.get("UnitPrice")
            if unit_price:
                print(
                    "......Unit Price: {} has confidence: {}".format(
                        unit_price.value, unit_price.confidence
                    )
                )
            product_code = item.value.get("ProductCode")
            if product_code:
                print(
                    "......Product Code: {} has confidence: {}".format(
                        product_code.value, product_code.confidence
                    )
                )
            item_date = item.value.get("Date")
            if item_date:
                print(
                    "......Date: {} has confidence: {}".format(
                        item_date.value, item_date.confidence
                    )
                )
            tax = item.value.get("Tax")
            if tax:
                print(
                    "......Tax: {} has confidence: {}".format(tax.value, tax.confidence)
                )
            amount = item.value.get("Amount")
            if amount:
                print(
                    "......Amount: {} has confidence: {}".format(
                        amount.value, amount.confidence
                    )
                )
        subtotal = invoice.fields.get("SubTotal")
        if subtotal:
            print(
                "Subtotal: {} has confidence: {}".format(
                    subtotal.value, subtotal.confidence
                )
            )
        total_tax = invoice.fields.get("TotalTax")
        if total_tax:
            print(
                "Total Tax: {} has confidence: {}".format(
                    total_tax.value, total_tax.confidence
                )
            )
        previous_unpaid_balance = invoice.fields.get("PreviousUnpaidBalance")
        if previous_unpaid_balance:
            print(
                "Previous Unpaid Balance: {} has confidence: {}".format(
                    previous_unpaid_balance.value, previous_unpaid_balance.confidence
                )
            )
        amount_due = invoice.fields.get("AmountDue")
        if amount_due:
            print(
                "Amount Due: {} has confidence: {}".format(
                    amount_due.value, amount_due.confidence
                )
            )
        service_start_date = invoice.fields.get("ServiceStartDate")
        if service_start_date:
            print(
                "Service Start Date: {} has confidence: {}".format(
                    service_start_date.value, service_start_date.confidence
                )
            )
        service_end_date = invoice.fields.get("ServiceEndDate")
        if service_end_date:
            print(
                "Service End Date: {} has confidence: {}".format(
                    service_end_date.value, service_end_date.confidence
                )
            )
        service_address = invoice.fields.get("ServiceAddress")
        if service_address:
            print(
                "Service Address: {} has confidence: {}".format(
                    service_address.value, service_address.confidence
                )
            )
        service_address_recipient = invoice.fields.get("ServiceAddressRecipient")
        if service_address_recipient:
            print(
                "Service Address Recipient: {} has confidence: {}".format(
                    service_address_recipient.value,
                    service_address_recipient.confidence,
                )
            )
        remittance_address = invoice.fields.get("RemittanceAddress")
        if remittance_address:
            print(
                "Remittance Address: {} has confidence: {}".format(
                    remittance_address.value, remittance_address.confidence
                )
            )
        remittance_address_recipient = invoice.fields.get("RemittanceAddressRecipient")
        if remittance_address_recipient:
            print(
                "Remittance Address Recipient: {} has confidence: {}".format(
                    remittance_address_recipient.value,
                    remittance_address_recipient.confidence,
                )
            )

if __name__ == "__main__":
    analyze_invoice()
```

## <a name="run-your-application"></a>Ejecución de la aplicación

1. Vaya a la carpeta donde tiene el archivo **form_recognizer_quickstart.py**.

1. Escriba el siguiente comando en el terminal:

```console
python form_recognizer_quickstart.py
```

¡Enhorabuena! En este inicio rápido, ha usado el SDK de Form Recognizer para Python para analizar varios formularios de maneras diferentes. A continuación, explore la documentación de referencia para encontrar más información sobre la API de Form Recognizer v3.0.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Documentación de referencia de la versión v3.0 de la API REST](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)

> [!div class="nextstepaction"]
> [Biblioteca de referencia de Form Recognizer](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-formrecognizer/3.2.0b1/index.html)

---
title: Modelo de factura de Form Recognizer
titleSuffix: Azure Applied AI Services
description: Conceptos que comprenden la extracción y el análisis de datos mediante el modelo de factura precompilado
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0f9c2f1603a87e30b0db32041f7d7aeff259600e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131027368"
---
<!-- markdownlint-disable MD033 -->

# <a name="form-recognizer-invoice-model"></a>Modelo de factura de Form Recognizer

 El modelo de factura combina funcionalidades de reconocimiento óptico de caracteres (OCR) eficaces con modelos de aprendizaje profundo para analizar y extraer campos clave y artículos de línea de recibos de ventas.  Las facturas pueden ser de distintos formatos y tener diferentes grados de calidad, lo que incluye imágenes capturadas por un teléfono, documentos digitalizados y archivos PDF digitales. La API analiza el texto de las facturas; extrae información clave, como el nombre del cliente, la dirección de facturación, la fecha de vencimiento y el importe a pagar; y devuelve una representación de datos JSON estructurada.

**Factura de ejemplo procesada con [Form Recognizer Studio](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=invoice)** :

:::image type="content" source="media/studio/overview-invoices.png" alt-text="Factura de ejemplo" lightbox="media/overview-invoices-big.jpg":::

## <a name="development-options"></a>Opciones de desarrollo

Los siguientes recursos son compatibles con Form Recognizer v2.1:

| Característica | Recursos |
|----------|-------------------------|
|**Modelo de factura**| <ul><li>[**Herramienta de etiquetado Form Recognizer**](https://fott-2-1.azurewebsites.net/prebuilts-analyze)</li><li>[**API DE REST**](quickstarts/try-sdk-rest-api.md?pivots=programming-language-rest-api#analyze-invoices)</li><li>[**SDK de biblioteca cliente**](quickstarts/try-sdk-rest-api.md)</li><li>[**Contenedor de Docker de Form Recognizer**](containers/form-recognizer-container-install-run.md?tabs=invoice#run-the-container-with-the-docker-compose-up-command)</li></ul>|

Los siguientes recursos son compatibles con Form Recognizer v3.0:

| Característica | Recursos | Id. de modelo |
|----------|-------------|-----------|
|**Modelo de factura** | <ul><li>[**Form Recognizer Studio**](https://formrecognizer.appliedai.azure.com)</li><li>[**API DE REST**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)</li><li>[**SDK DE C#**](quickstarts/try-v3-csharp-sdk.md)</li><li>[**SDK de Python**](quickstarts/try-v3-python-sdk.md)</li><li>[**SDK de Java**](quickstarts/try-v3-java-sdk.md)</li><li>[**SDK de JavaScript**](quickstarts/try-v3-javascript-sdk.md)</li></ul>|**prebuilt-invoice**|

### <a name="try-form-recognizer"></a>Probar Form Recognizer

Vea cómo se extraen los datos, incluida la información del cliente, los detalles del proveedor y los elementos de línea, de las facturas mediante Form Recognizer Studio o nuestra herramienta de etiquetado de ejemplo. Necesitará lo siguiente:

* Una suscripción a Azure: puede [crear una cuenta gratuita](https://azure.microsoft.com/free/cognitive-services/)

* Una [instancia de Form Recognizer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) en Azure Portal. Puede usar el plan de tarifa gratuito (`F0`) para probar el servicio. Después de implementar el recurso, seleccione **Ir al recurso** para obtener la clave de API y el punto de conexión.

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="Captura de pantalla: claves y ubicación del punto de conexión en Azure Portal.":::

#### <a name="form-recognizer-studio-preview"></a>Form Recognizer Studio (versión preliminar)

1. En la página principal de Form Recognizer Studio, seleccione **Facturas**.

1. Puede analizar la factura de ejemplo o seleccionar el botón **+ Agregar** para cargar un ejemplo propio.

1. Seleccione el botón **Analizar**:

    :::image type="content" source="media/studio/invoice-analyze.png" alt-text="Captura de pantalla: menú analizar factura.":::

> [!div class="nextstepaction"]
> [Prueba de Form Recognizer Studio](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=invoice)

#### <a name="sample-labeling-tool"></a>Herramienta de etiquetado de muestras

Necesitará un documento de factura. Puede usar nuestro [documento de factura de ejemplo](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf).

1. En la página principal de la herramienta de etiquetado de ejemplo, seleccione **Use prebuilt model to get data** (Usar modelo precompilado para obtener datos).

1. Seleccione **Factura** en el menú desplegable **Tipo de formulario**:

    :::image type="content" source="media/try-invoice.png" alt-text="Captura de pantalla: menú desplegable de selección del modelo precompilado de la herramienta de etiquetado de ejemplo.":::

    > [!div class="nextstepaction"]
    > [Pruebe la herramienta de etiquetado de ejemplo](https://fott-2-1.azurewebsites.net/prebuilts-analyze)

## <a name="input-requirements"></a>Requisitos de entrada

* Para obtener unos resultados óptimos, proporcione una foto clara o una digitalización de alta calidad por documento.
* Formatos de archivo admitidos: JPEG, PNG, BMP, TIFF y PDF (texto insertado o digitalizado). Los PDF insertados de texto son mejores para eliminar la posibilidad de error en la extracción de caracteres y en la ubicación.
* En el caso de PDF y TIFF, se pueden procesar hasta 2000 páginas (con una suscripción de nivel gratuito, solo se procesan las dos primeras páginas).
* El tamaño del archivo debe ser inferior a 50 MB.
* Las imágenes deben tener unas dimensiones entre 50 x 50 píxeles y 10 000 x 10 000 píxeles.
* Los archivos PDF tienen unas dimensiones de hasta 17 x 17 pulgadas, lo que corresponde a los tamaños de papel Legal o A3, o más pequeños.
* El tamaño total de los datos de entrenamiento es de 500 páginas o menos.
* Si los archivos PDF están bloqueados con contraseña, debe desbloquearlos antes de enviarlos.
* Para un aprendizaje sin supervisión (sin datos etiquetados):
  * Los datos deben contener claves y valores.
  * Las claves deben aparecer por encima o a la izquierda de los valores; no pueden aparecer por debajo ni a la derecha.

> [!NOTE]
> La [herramienta de etiquetado de ejemplo](https://fott-2-1.azurewebsites.net/) no admite el formato de archivo BMP. Se trata de una limitación de la herramienta, no del servicio Form Recognizer.

## <a name="supported-languages-and-locales"></a>Idiomas y configuraciones regionales compatibles

| Modelo | Idioma: código de configuración local | Valor predeterminado |
|--------|:----------------------|:---------|
|Factura| <ul><li>Inglés (Estados Unidos): en-US</li></ul>| Inglés (Estados Unidos): en-US|

## <a name="field-extraction"></a>Extracción de campos

|Nombre| Tipo | Descripción | Salida estandarizada |
|:-----|:----|:----|:---:|
| CustomerName | String | Cliente facturado| |
| CustomerId | String | Identificador de referencia del cliente | |
| PurchaseOrder | String | Número de referencia del pedido de compra | |
| InvoiceId | String | Identificador de esta factura concreta (a menudo "Número de factura") | |
| FechaFactura | Fecha | Fecha en que se generó la factura | aaaa-mm-dd|
| DueDate | Fecha | Fecha de vencimiento de esta factura | aaaa-mm-dd|
| VendorName | String | Nombre del proveedor |  |
| VendorAddress | String |  Dirección de correo postal del proveedor|  |
| VendorAddressRecipient | String | Nombre asociado a VendorAddress |  |
| CustomerAddress | String | Dirección de correo del cliente | |
| CustomerAddressRecipient | String | Nombre asociado a CustomerAddress | |
| BillingAddress | String | Dirección facturación del cliente |  |
| BillingAddressRecipient | String | Nombre asociado a BillingAddress | |
| ShippingAddress | String | Dirección de envío explícita del cliente | |
| ShippingAddressRecipient | String | Nombre asociado a ShippingAddress |  |
| SubTotal | Número | Campo de subtotal identificado en esta factura | Entero |
| TotalTax | Número | Campo de total de impuestos identificado en esta factura | Entero |
| InvoiceTotal | Número (USD) | Total de cargos nuevos asociados a esta factura | Entero |
| AmountDue |  Número (USD) | Importe total debido al proveedor | Entero |
| ServiceAddress | String | Dirección de servicio o dirección de propiedad explícitas para el cliente | |
| ServiceAddressRecipient | String | Nombre asociado a ServiceAddress |  |
| RemittanceAddress | String | Dirección de remite o de pago explícitas del cliente |   |
| RemittanceAddressRecipient | String | Nombre asociado a RemittanceAddress |  |
| ServiceStartDate | Fecha | Primera fecha del período de servicio (por ejemplo, un período de servicio de factura de la utilidad) | aaaa-mm-dd |
| ServiceEndDate | Fecha | Fecha de finalización del período de servicio (por ejemplo, un período de servicio de factura de la utilidad) | aaaa-mm-dd|
| PreviousUnpaidBalance | Número | Saldo explícito no pagado previamente | Entero |

### <a name="line-items"></a>Elementos de línea

A continuación se muestran los elementos de línea extraídos de una factura en la respuesta de salida JSON (la siguiente salida usa esta [factura de ejemplo](media/sample-invoice.jpg))

|Nombre| Tipo | Descripción | Texto (elemento de línea n.º 1) | Valor (salida estándar) |
|:-----|:----|:----|:----| :----|
| Elementos | String | Línea de texto de cadena completa del elemento de línea | 3/4/2021 A123 Consulting Services 2 hours $30.00 10% $60.00 | |
| Amount | Número | La cantidad del elemento de línea | $60.00 | 100 |
| Descripción | String | La descripción de texto para el elemento de la línea de factura | Servicios de consultoría | Servicios de consultoría |
| Cantidad | Number | La cantidad para este elemento de línea de factura | 2 | 2 |
| UnitPrice | Número | El precio neto o bruto (según la configuración de factura bruta de la factura) de una unidad de este elemento | $30.00 | 30 |
| ProductCode | String| Código de producto, número de producto o SKU asociado al elemento de línea específico | A123 | |
| Unidad | String| Unidad del elemento de línea, por ejemplo, kg, lb, etc. | Horas | |
| Date | Date| Fecha correspondiente a cada elemento de línea. Suele ser la fecha en la que se ha enviado el elemento de línea | 3/4/2021| 2021-03-04 |
| Impuesto | Número | Impuestos asociados a cada elemento de línea. Los valores posibles incluyen importe de impuestos, porcentaje de impuestos e impuesto S/N | 10 % | |

Los pares de clave/valor y los elementos de línea de la factura extraídos se encuentran en la sección `documentResults` de la salida JSON. 

## <a name="form-recognizer-preview-v30"></a>Versión preliminar de Form Recognizer v3.0

 La versión preliminar de Form Recognizer presenta varias características y funcionalidades nuevas.

* Siga nuestra [**guía de migración de Form Recognizer v3.0**](v3-migration-guide.md) para obtener información sobre cómo usar la versión preliminar en las aplicaciones y flujos de trabajo.

* Explore nuestra [**API de REST (versión preliminar)**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument) para obtener más información sobre la versión preliminar y las nuevas funcionalidades.

## <a name="next-steps"></a>Pasos siguientes

* Completar un inicio rápido de Form Recognizer:

  > [!div class="nextstepaction"]
  > [Inicio rápido: Uso de la biblioteca cliente de Form Recognizer](quickstarts/try-sdk-rest-api.md)

* Explorar nuestra API de REST:

    > [!div class="nextstepaction"]
    > [API v2.1 de Form Recognizer](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9843c2794cbb1a96291)

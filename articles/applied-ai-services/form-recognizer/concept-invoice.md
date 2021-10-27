---
title: Modelo de factura de Form Recognizer
titleSuffix: Azure Applied AI Services
description: Conceptos que comprenden la extracción y el análisis de datos mediante el modelo de factura precompilado
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: 458e6d9b9de91dcf9f9214f2021fb780841ff7b4
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130164340"
---
<!-- markdownlint-disable MD033 -->

# <a name="form-recognizer-invoice-model"></a>Modelo de factura de Form Recognizer

 El modelo de factura combina funcionalidades de reconocimiento óptico de caracteres (OCR) eficaces con modelos de aprendizaje profundo para analizar y extraer campos clave y artículos de línea de recibos de ventas.  Las facturas pueden ser de distintos formatos y tener diferentes grados de calidad, lo que incluye imágenes capturadas por un teléfono, documentos digitalizados y archivos PDF digitales. La API analiza el texto de las facturas; extrae información clave, como el nombre del cliente, la dirección de facturación, la fecha de vencimiento y el importe a pagar; y devuelve una representación de datos JSON estructurada.

##### <a name="sample-invoice-processed-with-form-recognizer-sample-labeling-tool"></a>Factura de ejemplo procesada con la [herramienta de etiquetado de ejemplo de Form Recognizer](https://fott-2-1.azurewebsites.net/):

:::image type="content" source="media/overview-invoices.jpg" alt-text="Factura de ejemplo" lightbox="media/overview-invoices-big.jpg":::

## <a name="try-form-recognizer-studio-preview"></a>Probar Form Recognizer Studio (versión preliminar)

* Form Recognizer Studio está disponible con la API de versión preliminar (v3.0).

* Extraiga los detalles del cliente y del proveedor, los artículos de línea y mucho más con nuestra característica Factura de Form Recognizer Studio:

> [!div class="nextstepaction"]
> [Prueba de Form Recognizer Studio](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=invoice)

## <a name="try-it-sample-labeling-tool"></a>Probarlo: herramienta de etiquetado de ejemplo

Para ver cómo se extraen los datos de factura, pruebe nuestra herramienta de etiquetado de ejemplo. Necesitará lo siguiente:

* Una suscripción a Azure: puede [crear una cuenta gratuita](https://azure.microsoft.com/free/cognitive-services/)

* Una [instancia de Form Recognizer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) en Azure Portal. Puede usar el plan de tarifa gratuito (`F0`) para probar el servicio. Después de implementar el recurso, haga clic en **Ir al recurso** para obtener la clave de API y el punto de conexión.

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="Captura de pantalla: claves y ubicación del punto de conexión en Azure Portal.":::

* Una factura. Puede usar nuestro [documento de factura de ejemplo](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf).

> [!div class="nextstepaction"]
  > [Probarlo](https://fott-2-1.azurewebsites.net/prebuilts-analyze)

  En la interfaz de usuario de Form Recognizer:

  1. Seleccione **Use prebuilt model to get data** (Usar un modelo precompilado para obtener datos).
  1. Seleccione **Factura** en el menú desplegable **Tipo de formulario**:

  :::image type="content" source="media/try-invoice.png" alt-text="Captura de pantalla: menú de selección de modelos precompilados desplegable de la herramienta de etiquetado de ejemplo.":::

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
> La [herramienta de etiquetado de ejemplo](https://fott-2-1.azurewebsites.net/) no admite el formato de archivo BMP. Se trata de una limitación de la herramienta, no del servicio Form Recognizer.

## <a name="supported-languages-and-locales"></a>Idiomas y configuraciones regionales compatibles

| Modelo | Idioma: código de configuración local | Valor predeterminado |
|--------|:----------------------|:---------|
|Factura| <ul><li>Inglés (Estados Unidos): en-US</li></ul>| Inglés (Estados Unidos): en-US|

## <a name="key-value-pair-extraction"></a>Extracción de pares clave-valor

|Nombre| Tipo | Descripción | Salida estandarizada |
|:-----|:----|:----|:---:|
| CustomerName | string | Cliente facturado| |
| CustomerId | string | Identificador de referencia del cliente | |
| PurchaseOrder | string | Número de referencia del pedido de compra | |
| InvoiceId | string | Identificador de esta factura concreta (a menudo "Número de factura") | |
| FechaFactura | date | Fecha en que se generó la factura | aaaa-mm-dd|
| DueDate | date | Fecha de vencimiento de esta factura | aaaa-mm-dd|
| VendorName | string | Nombre del proveedor |  |
| VendorAddress | string |  Dirección de correo postal del proveedor|  |
| VendorAddressRecipient | string | Nombre asociado a VendorAddress |  |
| CustomerAddress | string | Dirección de correo del cliente | |
| CustomerAddressRecipient | string | Nombre asociado a CustomerAddress | |
| BillingAddress | string | Dirección facturación del cliente |  |
| BillingAddressRecipient | string | Nombre asociado a BillingAddress | |
| ShippingAddress | string | Dirección de envío explícita del cliente | |
| ShippingAddressRecipient | string | Nombre asociado a ShippingAddress |  |
| SubTotal | number | Campo de subtotal identificado en esta factura | integer |
| TotalTax | number | Campo de total de impuestos identificado en esta factura | integer |
| InvoiceTotal | número (USD) | Total de cargos nuevos asociados a esta factura | integer |
| AmountDue |  número (USD) | Importe total debido al proveedor | integer |
| ServiceAddress | string | Dirección de servicio o dirección de propiedad explícitas para el cliente | |
| ServiceAddressRecipient | string | Nombre asociado a ServiceAddress |  |
| RemittanceAddress | string | Dirección de remite o de pago explícitas del cliente |   |
| RemittanceAddressRecipient | string | Nombre asociado a RemittanceAddress |  |
| ServiceStartDate | date | Primera fecha del período de servicio (por ejemplo, un período de servicio de factura de la utilidad) | aaaa-mm-dd |
| ServiceEndDate | date | Fecha de finalización del período de servicio (por ejemplo, un período de servicio de factura de la utilidad) | aaaa-mm-dd|
| PreviousUnpaidBalance | number | Saldo explícito no pagado previamente | integer |

### <a name="line-items"></a>Elementos de línea

A continuación se muestran los elementos de línea extraídos de una factura en la respuesta de salida JSON (la siguiente salida usa esta [factura de ejemplo](media/sample-invoice.jpg))

|Nombre| Tipo | Descripción | Texto (elemento de línea n.º 1) | Valor (salida estándar) |
|:-----|:----|:----|:----| :----|
| Elementos | string | Línea de texto de cadena completa del elemento de línea | 3/4/2021 A123 Consulting Services 2 hours $30.00 10% $60.00 | |
| Amount | number | La cantidad del elemento de línea | $60.00 | 100 |
| Descripción | string | La descripción de texto para el elemento de la línea de factura | Servicios de consultoría | Servicios de consultoría |
| Cantidad | number | La cantidad para este elemento de línea de factura | 2 | 2 |
| UnitPrice | number | El precio neto o bruto (según la configuración de factura bruta de la factura) de una unidad de este elemento | $30.00 | 30 |
| ProductCode | string| Código de producto, número de producto o SKU asociado al elemento de línea específico | A123 | |
| Unidad | string| Unidad del elemento de línea, por ejemplo, kg, lb, etc. | horas | |
| Fecha | fecha| Fecha correspondiente a cada elemento de línea. Suele ser la fecha en la que se ha enviado el elemento de línea | 3/4/2021| 2021-03-04 |
| Impuesto | number | Impuestos asociados a cada elemento de línea. Los valores posibles incluyen importe de impuestos, porcentaje de impuestos e impuesto S/N | 10 % | |

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

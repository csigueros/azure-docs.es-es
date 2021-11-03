---
title: Modelo de recibo de Form Recognizer
titleSuffix: Azure Applied AI Services
description: Conceptos que abarcan la extracción y el análisis de datos mediante el modelo de recibo precompilado
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: 111dcc2ab07c83e164e054395b0804f46c07d748
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131027079"
---
<!-- markdownlint-disable MD033 -->

# <a name="form-recognizer-receipt-model"></a>Modelo de recibo de Form Recognizer

El modelo de recibo combina funcionalidades de reconocimiento óptico de caracteres (OCR) eficaces con modelos de aprendizaje profundo para analizar y extraer información clave de recibos de ventas. Los recibos pueden tener varios formatos y calidad, incluidos los recibos impresos y manuscritos. La API extrae información clave, como el nombre del comerciante, el número de teléfono del comerciante, la fecha de transacción, los impuestos y el total de la transacción, y devuelve una representación de datos JSON estructurada.

***Recibo de ejemplo procesado con [la herramienta de etiquetado de ejemplo de Form Recognizer](https://fott-2-1.azurewebsites.net/)***:

:::image type="content" source="media/studio/overview-receipt.png" alt-text="recibo de ejemplo" lightbox="media/overview-receipt.jpg":::

## <a name="development-options"></a>Opciones de desarrollo

Los siguientes recursos son compatibles con Form Recognizer v2.1:

| Característica | Recursos |
|----------|-------------------------|
|**Modelo de recibo**| <ul><li>[**Herramienta de etiquetado Form Recognizer**](https://fott-2-1.azurewebsites.net/prebuilts-analyze)</li><li>[**API DE REST**](quickstarts/try-sdk-rest-api.md?pivots=programming-language-rest-api#analyze-receipts)</li><li>[**SDK de biblioteca cliente**](quickstarts/try-sdk-rest-api.md)</li><li>[**Contenedor de Docker de Form Recognizer**](containers/form-recognizer-container-install-run.md?tabs=receipt#run-the-container-with-the-docker-compose-up-command)</li></ul>|

Los siguientes recursos son compatibles con Form Recognizer v3.0:

| Característica | Recursos | Id. de modelo |
|----------|-------------|-----------|
|**Modelo de recibo**| <ul><li>[**Form Recognizer Studio**](https://formrecognizer.appliedai.azure.com)</li><li>[**API DE REST**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)</li><li>[**SDK DE C#**](quickstarts/try-v3-csharp-sdk.md)</li><li>[**SDK de Python**](quickstarts/try-v3-python-sdk.md)</li></ul>|**Recibo precompilado**|

### <a name="try-form-recognizer"></a>Probar Form Recognizer

Vea cómo se extraen de los recibos los datos, incluida la hora y la fecha de las transacciones, la información del comerciante y los totales de importe, mediante Form Recognizer Studio o nuestra herramienta de etiquetado de ejemplo. Necesitará lo siguiente:

* Una suscripción a Azure: puede [crear una cuenta gratuita](https://azure.microsoft.com/free/cognitive-services/)

* Una [instancia de Form Recognizer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) en Azure Portal. Puede usar el plan de tarifa gratuito (`F0`) para probar el servicio. Después de implementar el recurso, seleccione **Ir al recurso** para obtener la clave de API y el punto de conexión.

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="Captura de pantalla: claves y ubicación del punto de conexión en Azure Portal.":::

#### <a name="form-recognizer-studio-preview"></a>Form Recognizer Studio (versión preliminar)

> [!NOTE]
> Form Recognizer Studio está disponible con la API de versión preliminar (v3.0).

1. En la página principal de Form Recognizer Studio, seleccione **Recibos**

1. Puede analizar el recibo de ejemplo o seleccionar el botón **+ Agregar** para cargar un ejemplo propio.

1. Seleccione el botón **Analizar**:

    :::image type="content" source="media/studio/receipt-analyze.png" alt-text="Captura de pantalla: menú Analizar recibo.":::

    > [!div class="nextstepaction"]
    > [Prueba de Form Recognizer Studio](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=receipt)

#### <a name="sample-labeling-tool"></a>Herramienta de etiquetado de muestras

Necesitará un documento de recibo. Puede usar nuestro [documento de recibo de ejemplo](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/contoso-receipt.png).

1. En la página principal de la herramienta de etiquetado de ejemplo, seleccione **Use prebuilt model to get data** (Usar modelo precompilado para obtener datos).

1. Seleccione **Recibo** en el menú desplegable **Tipo de formulario**:

      :::image type="content" source="media/try-receipt.png" alt-text="Captura de pantalla: menú desplegable de selección del modelo precompilado de la herramienta de etiquetado de ejemplo.":::

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

## <a name="supported-languages-and-locales-v21"></a>Idiomas y configuraciones regionales compatibles v2.1

>[!NOTE]
 > No es necesario especificar una configuración regional. Se trata de un parámetro opcional. La tecnología de aprendizaje profundo de Form Recognizer detectará automáticamente el idioma del texto de la imagen.

| Modelo | Idioma: código de configuración local | Valor predeterminado |
|--------|:----------------------|:---------|
|Recibo| <ul><li>Inglés (Estados Unidos): en-US</li><li> Inglés (Australia): en-AU</li><li>Inglés (Canadá): en-CA</li><li>Inglés (Reino Unido): en-GB</li><li>Inglés (India): en-IN</li></ul>  | Detección automática |

## <a name="field-extraction"></a>Extracción de campos

|Nombre| Tipo | Descripción | Salida estandarizada |
|:-----|:----|:----|:----|
| ReceiptType | String | Tipo de recibo de venta |  Detallados |
| MerchantName | String | Nombre del comerciante que emite el recibo |  |
| MerchantPhoneNumber | phoneNumber | Número de teléfono mostrado del comerciante | +1 xxx xxx xxxx |
| MerchantAddress | String | Dirección mostrada del comerciante |   |
| Fecha de transacción | Fecha | Fecha de emisión del recibo | aaaa-mm-dd |
| TransactionTime | Time | Hora de emisión del recibo | hh-mm-ss (24 horas)  |
| Total | Número (USD)| Número total de transacciones de recibos | Float de dos decimales|
| Subtotal | Número (USD) | Subtotal del recibo, a menudo antes de aplicar impuestos | Float de dos decimales|
| Impuesto | Número (USD) | Impuesto sobre el recibo (impuesto sobre ventas o equivalente) | Float de dos decimales |
| Sugerencia | Número (USD) | Propina incluida por el comprador | Float de dos decimales|
| Elementos | Matriz de objetos | Líneas del artículo extraídas, con el nombre, la cantidad, el precio por unidad y el precio total extraídos | |
| Nombre | String | Nombre del elemento | |
| Cantidad | Number | Cantidad de cada artículo | Entero |
| Precio | Número | Precio individual de cada unidad del artículo| Float de dos decimales |
| Precio total | Número | Precio total del artículo | Float de dos decimales |

## <a name="form-recognizer-preview-v30"></a>Versión preliminar de Form Recognizer v3.0

 La versión preliminar de Form Recognizer presenta varias características y funcionalidades nuevas. El modelo de **Recibo** admite el procesamiento de recibos de hotel de una sola página.

### <a name="hotel-receipt-field-extraction"></a>Extracción de campos de recibos de hotel

|Nombre| Tipo | Descripción | Salida estandarizada |
|:-----|:----|:----|:----|
| ArrivalDate | Fecha | Fecha de llegada | aaaa-mm-dd |
| Moneda | Moneda | Unidad de moneda de importes de recibo. Por ejemplo, USD, EUR o MIXTA si se encuentran varios valores ||
| DepartureDate | Fecha | Fecha de salida | aaaa-mm-dd |
| Elementos | Array | | |
| Items.*.Category | String | Categoría de elemento, por ejemplo, Habitación, Impuestos, etc. |  |
| Items.*.Date | Fecha | Fecha del artículo | aaaa-mm-dd |
| Items.*.Description | String | Descripción del artículo | |
| Items.*.TotalPrice |  Número | Precio total del artículo | Entero |
| Configuración regional | String | Configuración regional del recibo, por ejemplo, en-US. | Código ISO de idioma-país   |
| MerchantAddress | String | Dirección mostrada del comerciante | |
| MerchantAliases | Array| | |
| MerchantAliases.* | String | Nombre alternativo del comerciante |  |
| MerchantName | String | Nombre del comerciante que emite el recibo | |
| MerchantPhoneNumber | phoneNumber | Número de teléfono mostrado del comerciante | +1 xxx xxx xxxx|
| ReceiptType | String | Tipo de recibo, por ejemplo, Hotel, Detallado | |
| Total | Número | Número total de transacciones de recibos | Float de dos decimales |

### <a name="hotel-receipt-supported-languages-and-locales"></a>Idiomas y configuraciones regionales compatibles con el recibo de hotel

| Modelo | Idioma: código de configuración local | Valor predeterminado |
|--------|:----------------------|:---------|
|Recibo (hotel) | <ul><li>Inglés (Estados Unidos): en-US</li></ul>| Inglés (Estados Unidos): en-US|

### <a name="migration-guide-and-rest-api-v30"></a>Guía de migración y API REST v3.0

* Siga nuestra [**guía de migración de Form Recognizer v3.0**](v3-migration-guide.md) para obtener información sobre cómo usar la versión preliminar en las aplicaciones y flujos de trabajo.

* Explore nuestra [**API de REST (versión preliminar)**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument) para obtener más información sobre la versión preliminar y las nuevas funcionalidades.

## <a name="next-steps"></a>Pasos siguientes

* Completar un inicio rápido de Form Recognizer:

  > [!div class="nextstepaction"]
  > [Inicio rápido: Uso de la biblioteca cliente de Form Recognizer](quickstarts/try-sdk-rest-api.md)

* Explorar nuestra API de REST:

    > [!div class="nextstepaction"]
    > [API v2.1 de Form Recognizer](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeReceiptAsync)

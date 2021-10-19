---
title: Modelo de recibo de Form Recognizer
titleSuffix: Azure Applied AI Services
description: Conceptos que abarcan la extracción y el análisis de datos mediante el modelo de recibo precompilado
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 10/05/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: 48ebbb4975e57db34fe080db2a61328cef65ca80
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/09/2021
ms.locfileid: "129716340"
---
<!-- markdownlint-disable MD033 -->

# <a name="form-recognizer-receipt-model"></a>Modelo de recibo de Form Recognizer

El modelo de recibo combina funcionalidades de reconocimiento óptico de caracteres (OCR) eficaces con modelos de aprendizaje profundo para analizar y extraer información clave de recibos de ventas. Los recibos pueden tener varios formatos y calidad, incluidos los recibos impresos y manuscritos. La API extrae información clave, como el nombre del comerciante, el número de teléfono del comerciante, la fecha de transacción, los impuestos y el total de la transacción, y devuelve una representación de datos JSON estructurada.

##### <a name="sample-receipt-processed-with-form-recognizer-sample-labeling-tool"></a>Recibo de ejemplo procesado con la [herramienta de etiquetado de ejemplo de Form Recognizer](https://fott-2-1.azurewebsites.net/):

:::image type="content" source="./media/overview-receipt.jpg" alt-text="recibo de ejemplo" lightbox="./media/overview-receipt.jpg":::

## <a name="try-form-recognizer-studio-preview"></a>Probar Form Recognizer Studio (versión preliminar)

* Form Recognizer Studio está disponible con la API de versión preliminar (v3.0).

* Extraiga la hora y la fecha de las transacciones, la información del comerciante, los totales de importe y mucho más con nuestra característica Recibo de Form Recognizer Studio:

> [!div class="nextstepaction"]
> [Prueba de Form Recognizer Studio](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=receipt)

## <a name="try-it-sample-labeling-tool"></a>Probarlo: herramienta de etiquetado de ejemplo

Para ver cómo se extraen los datos de recibo, pruebe nuestra herramienta de etiquetado de ejemplo. Necesitará lo siguiente:

* Una suscripción a Azure: puede [crear una cuenta gratuita](https://azure.microsoft.com/free/cognitive-services/)

* Una [instancia de Form Recognizer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) en Azure Portal. Puede usar el plan de tarifa gratuito (`F0`) para probar el servicio. Después de implementar el recurso, haga clic en **Ir al recurso** para obtener la clave de API y el punto de conexión.

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="Captura de pantalla: claves y ubicación del punto de conexión en Azure Portal.":::

* Un documento de recibo. Puede usar nuestro [documento de recibo de ejemplo](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/contoso-receipt.png).

> [!div class="nextstepaction"]
  > [Probarlo](https://fott-2-1.azurewebsites.net/prebuilts-analyze)

En la interfaz de usuario de Form Recognizer:

  1. Seleccione **Use prebuilt model to get data** (Usar un modelo precompilado para obtener datos).
  1. Seleccione **Recibo** en el menú desplegable **Tipo de formulario**:

  :::image type="content" source="media/try-receipt.png" alt-text="Captura de pantalla: menú de selección de modelos precompilados desplegable de la herramienta de etiquetado de ejemplo.":::

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

## <a name="key-value-pair-extraction"></a>Extracción de pares clave-valor

|Nombre| Tipo | Descripción | Salida estandarizada |
|:-----|:----|:----|:----|
| ReceiptType | string | Tipo de recibo de venta |  Detallados |
| MerchantName | string | Nombre del comerciante que emite el recibo |  |
| MerchantPhoneNumber | phoneNumber | Número de teléfono mostrado del comerciante | +1 xxx xxx xxxx |
| MerchantAddress | string | Dirección mostrada del comerciante |   |
| Fecha de transacción | date | Fecha de emisión del recibo | aaaa-mm-dd |
| TransactionTime | time | Hora de emisión del recibo | hh-mm-ss (24 horas)  |
| Total | número (USD)| Número total de transacciones de recibos | Float de dos decimales|
| Subtotal | número (USD) | Subtotal del recibo, a menudo antes de aplicar impuestos | Float de dos decimales|
| Impuesto | número (USD) | Impuesto sobre el recibo (impuesto sobre ventas o equivalente) | Float de dos decimales |
| Sugerencia | número (USD) | Propina incluida por el comprador | Float de dos decimales|
| Elementos | matriz de objetos | Líneas del artículo extraídas, con el nombre, la cantidad, el precio por unidad y el precio total extraídos | |
| Nombre | string | Nombre del elemento | |
| Cantidad | number | Cantidad de cada artículo | integer |
| Precio | number | Precio individual de cada unidad del artículo| Float de dos decimales |
| Precio total | number | Precio total del artículo | Float de dos decimales |

## <a name="form-recognizer-preview-v30"></a>Versión preliminar de Form Recognizer v3.0

 La versión preliminar de Form Recognizer presenta varias características y funcionalidades nuevas:

* El modelo de **Recibo** admite el procesamiento de recibos de hotel de una sola página.

    ### <a name="hotel-receipt-key-value-pair-extraction"></a>Extracción de pares clave-valor de recibo de hotel

    |Nombre| Tipo | Descripción | Salida estandarizada |
    |:-----|:----|:----|:----|
    | ArrivalDate | date | Fecha de llegada | aaaa-mm-dd |
    | Moneda | currency | Unidad de moneda de importes de recibo. Por ejemplo, USD, EUR o MIXTA si se encuentran varios valores ||
    | DepartureDate | date | Fecha de salida | aaaa-mm-dd |
    | Elementos | array | | |
    | Items.*.Category | string | Categoría del artículo, por ejemplo, Habitación, Impuestos, etc. |  |
    | Items.*.Date | date | Fecha del artículo | aaaa-mm-dd |
    | Items.*.Description | string | Descripción del artículo | |
    | Items.*.TotalPrice |  number | Precio total del artículo | integer |
    | Locale | string | Configuración regional del recibo, por ejemplo, en-US. | Código ISO de idioma-país   |
    | MerchantAddress | string | Dirección mostrada del comerciante | |
    | MerchantAliases | array| | |
    | MerchantAliases.* | string | Nombre alternativo del comerciante |  |
    | MerchantName | string | Nombre del comerciante que emite el recibo | |
    | MerchantPhoneNumber | phoneNumber | Número de teléfono mostrado del comerciante | +1 xxx xxx xxxx|
    | ReceiptType | string | Tipo de recibo, por ejemplo, Hotel, Detallado | |
    | Total | number | Número total de transacciones de recibos | Float de dos decimales |

    ### <a name="hotel-receipt-supported-languages-and-locales"></a>Idiomas y configuraciones regionales compatibles con el recibo de hotel

    | Modelo | Idioma: código de configuración local | Valor predeterminado |
    |--------|:----------------------|:---------|
    |Recibo (hotel) | <ul><li>Inglés (Estados Unidos): en-US</li></ul>| Inglés (Estados Unidos): en-US|

* Siga nuestra [**guía de migración de Form Recognizer v3.0**](v3-migration-guide.md) para obtener información sobre cómo usar la versión preliminar en las aplicaciones y flujos de trabajo.

* Explore nuestra [**API de REST (versión preliminar)** ](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument) para obtener más información sobre la versión preliminar y las nuevas funcionalidades.

## <a name="next-steps"></a>Pasos siguientes

* Completar un inicio rápido de Form Recognizer:

  > [!div class="nextstepaction"]
  > [Inicio rápido: Uso de la biblioteca cliente de Form Recognizer](quickstarts/try-sdk-rest-api.md)

* Explorar nuestra API de REST:

    > [!div class="nextstepaction"]
    > [API v2.1 de Form Recognizer](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeReceiptAsync)

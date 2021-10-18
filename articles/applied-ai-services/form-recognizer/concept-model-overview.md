---
title: Modelos de Form Recognizer
titleSuffix: Azure Applied AI Services
description: Conceptos que comprenden la extracción y el análisis de datos mediante modelos precompilados
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: f2e2ef59d4c3608065edab4ffd1d1ec55122f2ad
ms.sourcegitcommit: af303268d0396c0887a21ec34c9f49106bb0c9c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2021
ms.locfileid: "129754622"
---
<!-- markdownlint-disable MD033 -->

# <a name="form-recognizer-models"></a>Modelos de Form Recognizer

 Los modelos precompilados de Azure Form Recognizer permiten agregar procesamiento de formularios inteligente a las aplicaciones y los flujos sin necesidad de entrenar ni compilar modelos propios. Los modelos precompilados usan el reconocimiento óptico de caracteres (OCR) combinado con modelos de aprendizaje profundo para identificar y extraer texto y campos de datos predefinidos comunes a tipos de documentos y formularios específicos. Form Recognizer extrae y analiza los datos de formularios y documentos y luego devuelve una respuesta JSON estructurada y organizada. Form Recognizer v2.1 admite modelos de facturas, recibos, documentos de identificación y tarjetas de presentación.

## <a name="model-overview"></a>Introducción al modelo

| **Modelo**   | **Descripción**   |
| --- | --- |
| 🆕[Documento general (versión preliminar)](#general-document-preview) | Extrae texto, tablas, estructuras, pares clave-valor y entidades con nombre.  |
| [Diseño](#layout)  | Extrae texto e información de diseño de los documentos.  |
| [Factura](#invoice)  | Extrae información clave de facturas en inglés.  |
| [Recibo](#receipt)  | Extrae información clave de recibos en inglés.  |
| [Documento de identificación](#id-document)  | Extrae información clave de permisos de conducir de EE. UU. y pasaportes internacionales.  |
| [Tarjeta de presentación](#business-card)  | Extrae información clave de tarjetas de presentación en inglés.  |
| [Personalizada](#custom) |  Extrae datos de formularios y documentos específicos de la empresa. Los modelos personalizados se entrenan para sus distintos datos y casos de uso. |

### <a name="general-document-preview"></a>Documento general (versión preliminar)

* La API de documento general admite la mayoría de tipos de formulario y analiza los documentos y asocia valores a las claves y entradas de las tablas que detecta. Es ideal para extraer pares clave-valor comunes de documentos. Puede usar el modelo de documento general como alternativa al [entrenamiento de un modelo personalizado sin etiquetas](compose-custom-models.md#train-without-labels).

* El documento general es un modelo entrenado previamente y se puede invocar directamente mediante la API REST.

* El modelo de documento general admite el reconocimiento de entidades con nombre (NER) de varias categorías de entidad. NER es la capacidad de identificar diferentes entidades en el texto y de clasificarlas en clases o tipos predefinidos como: persona, ubicación, evento, producto y organización. La extracción de entidades puede ser útil en escenarios en los que se quieren validar los valores extraídos. Las entidades se extraen de todo el contenido.

##### <a name="sample-document-processed-in-the-form-recognizer-studio"></a>Documento de ejemplo procesado en [Form Recognizer Studio](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=document):

:::image type="content" source="media/general-document-analyze.png" alt-text="Captura de pantalla: análisis de documento general en Form Recognizer Studio.":::

> [!div class="nextstepaction"]
> [Más información: modelo de documento general](concept-general-document.md)

### <a name="layout"></a>Layout

La API de diseño analiza y extrae texto, tablas y encabezados, marcas de selección e información de estructura de documentos y devuelve una representación de datos JSON estructurada.

##### <a name="sample-form-processed-with-form-recognizer-sample-labeling-tool--layout-feature"></a>Formulario de ejemplo procesado con la característica de diseño de la [herramienta de etiquetado de ejemplo de Form Recognizer](https://fott-2-1.azurewebsites.net/):

:::image type="content" source="media/overview-layout.png" alt-text="{alt-text}":::

> [!div class="nextstepaction"]
> [Más información: modelo de diseño](concept-layout.md)

### <a name="invoice"></a>Factura

El modelo de factura analiza y extrae información clave de facturas de ventas. La API analiza las facturas en varios formatos; extrae información clave, como el nombre del cliente, la dirección de facturación, la fecha de vencimiento y el importe a pagar; y devuelve una representación de datos JSON estructurada.

##### <a name="sample-invoice-processed-with-form-recognizer-sample-labeling-tool"></a>Factura de ejemplo procesada con la [herramienta de etiquetado de ejemplo de Form Recognizer](https://fott-2-1.azurewebsites.net/):

:::image type="content" source="./media/overview-invoices.jpg" alt-text="Factura de ejemplo" lightbox="./media/overview-invoices.jpg":::

> [!div class="nextstepaction"]
> [Más información: modelo de factura](concept-invoice.md)

### <a name="receipt"></a>Recibo

El modelo de recibo analiza y extrae información clave de recibos de ventas. La API analiza recibos impresos y manuscritos; extrae información clave, como el nombre del comerciante, el número de teléfono del comerciante, la fecha de transacción, los impuestos y el total de la transacción; y devuelve una representación de datos JSON estructurada.

##### <a name="sample-receipt-processed-with-form-recognizer-sample-labeling-tool"></a>Recibo de ejemplo procesado con la [herramienta de etiquetado de ejemplo de Form Recognizer](https://fott-2-1.azurewebsites.net/):

:::image type="content" source="./media/overview-receipt.jpg" alt-text="recibo de ejemplo" lightbox="./media/overview-receipt.jpg":::

> [!div class="nextstepaction"]
> [Más información: modelo de recibo](concept-receipt.md)

### <a name="id-document"></a>Documento de identificación

El modelo de documento de identificación analiza y extrae información clave de los permisos de conducir de EE. UU. (los 50 estados y el Distrito de Columbia) y de las páginas biográficas de los pasaportes internacionales (excepto los visados y otros documentos de viaje). La API analiza los documentos de identidad; extrae información clave, como nombre, apellidos, dirección y fecha de nacimiento; y devuelve una representación de datos JSON estructurada.

##### <a name="sample-us-drivers-license-processed-with-form-recognizer-sample-labeling-tool"></a>Permiso de conducir de EE. UU. de ejemplo procesado con la [herramienta de etiquetado de ejemplo de Form Recognizer](https://fott-2-1.azurewebsites.net/):

:::image type="content" source="./media/id-example-drivers-license.jpg" alt-text="tarjeta de identidad de ejemplo" lightbox="./media/overview-id.jpg":::

> [!div class="nextstepaction"]
> [Más información: modelo de documento de identidad](concept-id-document.md)

### <a name="business-card"></a>Tarjeta de presentación

El modelo de tarjeta de presentación analiza y extrae información clave de las imágenes de las tarjetas de presentación. La API analiza tarjetas de presentación impresas; extrae información clave, como el nombre, los apellidos, el nombre de la empresa, la dirección de correo electrónico y el número de teléfono; y devuelve una representación de datos JSON estructurada.

##### <a name="sample-business-card-processed-with-form-recognizer-sample-labeling-tool"></a>Tarjeta de presentación de ejemplo procesada con la [herramienta de etiquetado de ejemplo de Form Recognizer](https://fott-2-1.azurewebsites.net/):

:::image type="content" source="./media/overview-business-card.jpg" alt-text="tarjeta de presentación de ejemplo" lightbox="./media/overview-business-card.jpg":::

> [!div class="nextstepaction"]
> [Más información: modelo de tarjeta de presentación](concept-business-card.md)

### <a name="custom"></a>Personalizado

El modelo personalizado analiza y extrae datos de formularios y documentos específicos de la empresa. La API es un programa de aprendizaje automático entrenado para reconocer campos de formulario dentro del contenido distintivo y extraer pares clave-valor y datos de tablas. Solo necesita cinco ejemplos del mismo tipo de formulario para empezar y el modelo personalizado se puede entrenar con o sin conjuntos de datos etiquetados.

##### <a name="sample-custom-form-processed-with-form-recognizer-sample-labeling-tool"></a>Formulario personalizado de ejemplo procesado con la [herramienta de etiquetado de ejemplo de Form Recognizer](https://fott-2-1.azurewebsites.net/):

:::image type="content" source="media/analyze.png" alt-text="Captura de pantalla: ventana de análisis de un formulario personalizado de la herramienta Form Recognizer.":::

> [!div class="nextstepaction"]
> [Más información: modelo personalizado](concept-custom.md)

## <a name="data-extraction"></a>Extracción de datos

 | **Modelo**   | **Extracción de texto** |**Pares clave-valor** |**Marcas de selección**   | **Tablas**   |**Entidades** |
  | --- | :---: |:---:| :---: | :---: |:---: |
  |🆕Documento general  | ✓  |  ✓ | ✓  | ✓  | ✓  |
  | Layout  | ✓  |   | ✓  | ✓  |   |
  | Factura  | ✓ | ✓  | ✓  | ✓ ||
  |Recibo  | ✓  |   ✓ |   |  ||
  | Documento de identificación | ✓  |   ✓  |   |   ||
  | Tarjeta de presentación    | ✓  |   ✓ |   |   ||
  | Personalizado             |✓  |  ✓ | ✓  | ✓  | ✓  |

## <a name="input-requirements"></a>Requisitos de entrada

* Para obtener unos resultados óptimos, proporcione una foto clara o un escaneo de alta calidad por documento.
* Formatos de archivo admitidos: JPEG, PNG, BMP, TIFF y PDF (texto insertado o digitalizado). Los PDF insertados de texto son mejores para eliminar la posibilidad de error en la extracción de caracteres y en la ubicación.
* En el caso de PDF y TIFF, se pueden procesar hasta 2000 páginas (con una suscripción de nivel gratuito, solo se procesan las dos primeras páginas).
* El tamaño del archivo debe ser inferior a 50 MB.
* Las imágenes deben tener unas dimensiones entre 50 x 50 píxeles y 10 000 x 10 000 píxeles.
* Los archivos PDF tienen unas dimensiones de hasta 17 x 17 pulgadas, lo que corresponde a los tamaños de papel Legal o A3, o más pequeños.
* El tamaño total de los datos de entrenamiento es de 500 páginas o menos.
* Si los archivos PDF están bloqueados con contraseña, debe desbloquearlos antes de enviarlos.
* Para un aprendizaje sin supervisión (sin datos etiquetados):
  * Los datos deben contener claves y valores.
  * Las claves deben aparecer por encima o a la izquierda de los valores; no pueden aparecer por debajo ni a la derecha.

> [!NOTE]
> La [herramienta de etiquetado de ejemplo](https://fott-2-1.azurewebsites.net/) no admite el formato de archivo BMP. Se trata de una limitación de la herramienta, no del servicio Form Recognizer.

## <a name="form-recognizer-preview-v30"></a>Versión preliminar de Form Recognizer v3.0

  Form Recognizer v3.0 (versión preliminar) presenta varias características y capacidades nuevas:

* El modelo de [**documento general (versión preliminar)** ](concept-general-document.md) es una nueva API que usa un modelo entrenado previamente para extraer texto, tablas, estructura, pares clave-valor y entidades con nombre de formularios y documentos.
* El modelo de [**recibo (versión preliminar)** ](concept-receipt.md) admite el procesamiento de recibos de hotel de una página.
* El modelo de [**documento de identificación (versión preliminar)** ](concept-id-document.md) admite aprobaciones, restricciones y extracción de clasificación de vehículos de permisos de conducir de EE. UU.
* La [**API del modelo personalizado (versión preliminar)** ](concept-custom.md) admite la detección de firmas de formularios personalizados.

### <a name="version-migration"></a>Migración de versión

Aprenda a usar Form Recognizer v3.0 en las aplicaciones con la [**guía de migración de Form Recognizer v3.0**](v3-migration-guide.md)

## <a name="next-steps"></a>Pasos siguientes

* [Aprenda a procesar formularios y documentos propios](quickstarts/try-sample-label-tool.md) con la [herramienta de ejemplo de Form Recognizer](https://fott-2-1.azurewebsites.net/)

* Realice un [inicio rápido de Form Recognizer](quickstarts/try-sdk-rest-api.md) y empiece a crear una aplicación de procesamiento de formularios en el lenguaje de desarrollo que prefiera.

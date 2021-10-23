---
title: 'Diseños: Form Recognizer'
titleSuffix: Azure Applied AI Services
description: 'Conozca los conceptos relacionados con el análisis de diseños mediante API Form Recognizer: uso y límites.'
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: 425a5cb9ab332076ddd0d745bba7b914e5601917
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/09/2021
ms.locfileid: "129707167"
---
# <a name="form-recognizer-layout-model"></a>Modelo de diseño de Form Recognizer

La API Layout de Azure Form Recognizer extrae texto, tablas, marcas de selección e información de estructura de documentos (PDF, TIFF) e imágenes (JPG, PNG, BMP). Permite a los clientes tomar documentos en varios formatos y devolver sus representaciones de datos estructurados. Combina una versión mejorada de nuestras poderosas funcionalidades de [reconocimiento óptico de caracteres (OCR)](../../cognitive-services/computer-vision/overview-ocr.md) con modelos de aprendizaje profundo para extraer texto, tablas, marcas de selección y la estructura de los documentos.

##### <a name="sample-form-processed-with-form-recognizer-sample-labeling-tool--layout-feature"></a>Formulario de ejemplo procesado con la característica de diseño de la [herramienta de etiquetado de ejemplo de Form Recognizer](https://fott-2-1.azurewebsites.net/):

:::image type="content" source="media/layout-demo.gif" alt-text="{alt-text}":::

**Características de extracción de datos**

| **Modelo de diseño**   | **Extracción de texto**   | **Marcas de selección**   | **Tablas**  |
| --- | --- | --- | --- |
| Layout  | ✓  | ✓  | ✓  |

## <a name="try-form-recognizer-studio-preview"></a>Probar Form Recognizer Studio (versión preliminar)

* Form Recognizer Studio está disponible con la API de versión preliminar (v3.0).

* Extraiga tablas, casillas y texto de formularios y documentos con nuestra característica Diseño de Form Recognizer Studio:

> [!div class="nextstepaction"]
> [Prueba de Form Recognizer Studio](https://formrecognizer.appliedai.azure.com/studio/layout)

## <a name="try-form-recognizer-sample-labeling-tool"></a>Pruebe la herramienta de etiquetado de ejemplo de Form Recognizer

Para ver cómo se extraen los datos de diseño, pruebe nuestra herramienta de etiquetado de ejemplo. Necesitará lo siguiente:

* Una suscripción a Azure: puede [crear una cuenta gratuita](https://azure.microsoft.com/free/cognitive-services/)

* Una [instancia de Form Recognizer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) en Azure Portal. Puede usar el plan de tarifa gratuito (`F0`) para probar el servicio. Después de implementar el recurso, haga clic en **Ir al recurso** para obtener la clave de API y el punto de conexión.

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="Captura de pantalla: claves y ubicación del punto de conexión en Azure Portal.":::

* Un formulario. Puede usar nuestro [documento de formulario de ejemplo](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf).

> [!div class="nextstepaction"]
  > [Probarlo](https://fott-2-1.azurewebsites.net/prebuilts-analyze)

  En la interfaz de usuario de Form Recognizer:

  1. Seleccione **Uso de Diseño para obtener texto, tablas y marcas de selección**.
  1. Seleccione **Archivo local** en el menú desplegable.
  1. Upload el archivo y seleccione **Ejecutar diseño**

  :::image type="content" source="media/try-layout.png" alt-text="Captura de pantalla: menú de selección de origen del archivo de diseño desplegable de la herramienta de etiquetado de ejemplo.":::

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

 La versión preliminar de Form Recognizer presenta compatibilidad de idioma adicional para el modelo de diseño. *Consulte* nuestra [compatibilidad de idioma](language-support.md#layout-and-custom-model) para obtener una lista completa de textos manuscritos e impresos compatibles.

## <a name="features"></a>Características

### <a name="tables-and-table-headers"></a>Tablas y encabezados de tabla

La API Layout extrae tablas de la sección `pageResults` de la salida JSON. Los documentos se pueden digitalizar o fotografiar. Las tablas pueden ser complejas con celdas o columnas combinadas, con o sin bordes, y con ángulos impares. La información de las tablas extraídas incluyen el número de columnas y filas, el intervalo de filas y el intervalo de columnas. Cada celda con su rectángulo de selección se genera con información sobre si se reconoce como parte de un encabezado o no. Las celdas de encabezado predichos del modelo pueden abarcar varias filas y no son necesariamente las primeras filas de una tabla. También funcionan con tablas giradas. Cada celda de la tabla también incluye el texto completo con referencias a las palabras individuales de la sección `readResults`.

:::image type="content" source="./media/layout-table-headers-example.png" alt-text="Salida de encabezados de tabla de diseño":::

### <a name="selection-marks"></a>Marcas de selección

La API Layout también extrae marcas de selección de los documentos. Entre las marcas de selección extraídas se incluyen el rectángulo delimitador, la confianza y el estado (seleccionado o no seleccionado). La información de la marca de selección se extrae en la sección `readResults` de la salida JSON.

:::image type="content" source="./media/layout-selection-marks.png" alt-text="Salida de marcas de selección de diseño":::

### <a name="text-lines-and-words"></a>Palabras y líneas del texto

La API Layout extrae texto de documentos e imágenes con varios ángulos y colores de texto. Es compatible con fotografías de documentos, faxes, texto impreso o manuscrito (solo en inglés) y modos mixtos. El texto se extrae con información sobre las líneas, palabras, rectángulos delimitadores, puntuaciones de confianza y estilo (manuscrito u otro). Toda la información del texto se incluye en la sección `readResults` de la salida JSON.

:::image type="content" source="./media/layout-text-extraction.png" alt-text="Salida de extracción del texto de diseño":::

### <a name="natural-reading-order-for-text-lines-latin-only"></a>Orden de lectura natural para las líneas de texto (solo idiomas derivados del latín)

Puede especificar el orden en que se generan las líneas de texto con el parámetro de consulta `readingOrder`. Use `natural` si quiere obtener una salida de orden de lectura más natural, como se muestra en el ejemplo siguiente. Esta característica solo es compatible con los idiomas procedentes del latín.

:::image type="content" source="./media/layout-reading-order-example.png" alt-text="Ejemplo de orden del diseño de lectura" lightbox="../../cognitive-services/Computer-vision/Images/ocr-reading-order-example.png":::

### <a name="handwritten-classification-for-text-lines-latin-only"></a>Clasificación manuscrita de líneas de texto (solo para idiomas derivados del latín)

La respuesta incluye la clasificación de si cada línea de texto es de estilo manuscrito o no, junto con una puntuación de confianza. Esta característica solo es compatible con los idiomas procedentes del latín. En el ejemplo siguiente se muestra la clasificación manuscrita del texto de la imagen.

:::image type="content" source="./media/layout-handwriting-classification.png" alt-text="Ejemplo de clasificación de escritura a mano":::

### <a name="select-page-numbers-or-ranges-for-text-extraction"></a>Seleccione los intervalos o los números de páginas para la extracción de texto

En el caso de documentos de varias páginas de gran tamaño, use el parámetro de consulta `pages`para indicar números de página o intervalos de páginas específicos para la extracción de texto. En el ejemplo siguiente se muestra un documento con 10 páginas, con texto extraído para ambos casos: todas las páginas (1-10) y las páginas seleccionadas (3-6).

:::image type="content" source="./media/layout-select-pages-for-text.png" alt-text="Salida de páginas seleccionadas de diseño":::

## <a name="form-recognizer-preview-v30"></a>Versión preliminar de Form Recognizer v3.0

 La versión preliminar de Form Recognizer presenta varias características y funcionalidades nuevas.

* Siga nuestra [**guía de migración de Form Recognizer v3.0**](v3-migration-guide.md) para obtener información sobre cómo usar la versión preliminar en las aplicaciones y flujos de trabajo.

* Explore nuestra [**API de REST (versión preliminar)** ](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument) para obtener más información sobre la versión preliminar y las nuevas funcionalidades.

## <a name="next-steps"></a>Pasos siguientes

* Completar un inicio rápido de Form Recognizer:

  > [!div class="nextstepaction"]
  > [Inicio rápido: Uso de la biblioteca cliente de Form Recognizer](quickstarts/try-sdk-rest-api.md)

* Explorar nuestra API REST:

    > [!div class="nextstepaction"]
    > [API v2.1 de Form Recognizer](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeLayoutAsync)
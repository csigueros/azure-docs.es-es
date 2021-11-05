---
title: Modelo de documento general de Form Recognizer | Versión preliminar
titleSuffix: Azure Applied AI Services
description: Conceptos que abarcan la extracción y el análisis de datos mediante el modelo de vista previa de documento general precompilado
author: vkurpad
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: 63d381f96a9781f2f3ab1abfd45d03c968d6dad8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131027534"
---
<!-- markdownlint-disable MD033 -->

# <a name="form-recognizer-general-document-model-preview"></a>Modelo de documento general de Form Recognizer (versión preliminar)

El modelo de vista previa de documento general combina eficaces funcionalidades de reconocimiento óptico de caracteres (OCR) con modelos de aprendizaje profundo para extraer pares clave-valor y entidades de documentos. El documento general solo está disponible con la API de versión preliminar (v3.0).  Para más información sobre el uso de la API de versión preliminar (v3.0), consulte nuestra [guía de migración](v3-migration-guide.md).

La API de documento general admite la mayoría de tipos de formulario y analiza los documentos y asocia valores a las claves y entradas de las tablas que detecta. Es ideal para extraer pares clave-valor comunes de documentos. Puede usar el modelo de documento general como alternativa al [entrenamiento de un modelo personalizado sin etiquetas](compose-custom-models.md#train-without-labels).

## <a name="general-document-features"></a>Características del documento general

* No es necesario entrenar un modelo personalizado para extraer pares clave-valor.

* Se usa una única API para extraer pares clave-valor, entidades, texto, tablas y estructura de los documentos.

* Se trata de un modelo previamente entrenado que se entrenará periódicamente con nuevos datos para mejorar la cobertura y la precisión.

* El modelo de documento general admite datos estructurados, semiestructurados y no estructurados.

***Documento de ejemplo procesado en Form Recognizer Studio***

:::image type="content" source="media/studio/general-document-analyze.png" alt-text="Captura de pantalla: análisis de documento general en Form Recognizer Studio.":::

## <a name="development-options"></a>Opciones de desarrollo

Los siguientes recursos son compatibles con Form Recognizer v3.0:

| Característica | Recursos |
|----------|-------------------------|
|🆕 **Modelo de documento general**|<ul ><li>[**Form Recognizer Studio**](https://formrecognizer.appliedai.azure.com)</li><li>[**API DE REST**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)</li><li>[**SDK DE C#**](quickstarts/try-v3-csharp-sdk.md)</li><li>[**SDK de Python**](quickstarts/try-v3-python-sdk.md)</li><li>[**SDK de Java**](quickstarts/try-v3-java-sdk.md)</li><li>[**SDK de JavaScript**](quickstarts/try-v3-javascript-sdk.md)</li></ul>|

### <a name="try-form-recognizer"></a>Probar Form Recognizer

Vea cómo los datos, incluidas las tablas, los valores y las entidades, se extraen de formularios y documentos mediante Form Recognizer Studio o nuestra herramienta de etiquetado de ejemplo. Necesitará lo siguiente:

* Una suscripción a Azure: puede [crear una cuenta gratuita](https://azure.microsoft.com/free/cognitive-services/)

* Una [instancia de Form Recognizer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) en Azure Portal. Puede usar el plan de tarifa gratuito (`F0`) para probar el servicio. Después de implementar el recurso, seleccione **Ir al recurso** para obtener la clave de API y el punto de conexión.

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="Captura de pantalla: claves y ubicación del punto de conexión en Azure Portal.":::

#### <a name="form-recognizer-studio-preview"></a>Form Recognizer Studio (versión preliminar)

> [!NOTE]
> Form Recognizer Studio y el modelo de documento general están disponibles con la API de versión preliminar (v3.0).

1. En la página principal de Form Recognizer Studio, seleccione **Documentos generales**

1. Puede analizar el documento de ejemplo o seleccionar el botón **+ Agregar** para cargar su propio ejemplo.

1. Seleccione el botón **Analizar**:

    :::image type="content" source="media/studio/general-document-analyze-1.png" alt-text="Captura de pantalla: menú de análisis de documentos generales.":::

    > [!div class="nextstepaction"]
    > [Prueba de Form Recognizer Studio](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=document)

## <a name="key-value-pairs"></a>Pares clave-valor

Los pares clave-valor son intervalos específicos dentro del documento que identifican una etiqueta o clave y su respuesta o valor asociados. En un formato estructurado, esto podría ser la etiqueta y el valor especificado por el usuario para ese campo; en un documento no estructurado, podría ser la fecha en la que se ejecutó un contrato en función del texto de un párrafo.  El modelo de IA está entrenado para extraer claves y valores identificables basados en una amplia variedad de tipos de documentos, formatos y estructuras.

Las claves también pueden existir de forma aislada cuando el modelo detecta que existe una clave, sin ningún valor asociado, o cuando se procesan campos opcionales. Por ejemplo, un campo de segundo nombre se puede dejar en blanco en un formulario en algunos casos. Los pares clave-valor son siempre intervalos de texto contenidos en el documento y, si tiene documentos donde el mismo valor se describe de maneras diferentes, por ejemplo, un cliente o un usuario, la clave asociada será cliente o usuario en función de lo que contenga el documento. 

## <a name="entities"></a>Entidades

Los modelos de procesamiento de lenguaje natural pueden identificar partes de la voz y clasificar cada token o palabra. El modelo de reconocimiento de entidades con nombre es capaz de identificar entidades como personas, ubicaciones y fechas para proporcionar una experiencia más enriquecedora. La identificación de entidades le permite distinguir entre los tipos de cliente, por ejemplo, un individuo o una organización.
El modelo de extracción de pares clave-valor y el modelo de identificación de entidades se ejecutan en paralelo en todo el documento y no solo en los valores de los pares clave-valor extraídos. Esto garantiza que las estructuras complejas en las que no se puede identificar una clave siguen enriquecidas mediante la identificación de las entidades a las que se hace referencia. Todavía puede hacer coincidir claves o valores con entidades en función de los desplazamientos de los intervalos identificados.

* El documento general es un modelo entrenado previamente y se puede invocar directamente mediante la API REST. 

* El modelo de documento general admite el reconocimiento de entidades con nombre (NER) de varias categorías de entidad. NER es la capacidad de identificar diferentes entidades en el texto y de clasificarlas en clases o tipos predefinidos como: persona, ubicación, evento, producto y organización. La extracción de entidades puede ser útil en escenarios en los que se quieren validar los valores extraídos. Las entidades se extraen de todo el contenido y no solo de los valores extraídos.

## <a name="general-document-model-data-extraction"></a>Extracción de datos del modelo de documento general

| **Modelo**   | **Extracción de texto** |**Pares clave-valor** |**Marcas de selección**   | **Tablas**   |**Entidades** |
| --- | :---: |:---:| :---: | :---: |:---: |
|Documento general  | ✓  |  ✓ | ✓  | ✓  | ✓  |

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

## <a name="supported-languages-and-locales"></a>Idiomas y configuraciones regionales compatibles

| Modelo | Idioma: código de configuración local | Valor predeterminado |
|--------|:----------------------|:---------|
|Documento general| <ul><li>Inglés (Estados Unidos): en-US</li></ul>| Inglés (Estados Unidos): en-US|

### <a name="named-entity-recognition-ner-categories"></a>Categorías de Reconocimiento de entidades con nombre (NER)

| Category | Tipo | Descripción |
|-----------|-------|--------------------|
| Person | String | Nombre parcial o completo de una persona. |
| PersonType | String | Tipo de trabajo o rol de una persona.  |
| Location | String | Puntos de referencia naturales y humanos, estructuras, características geográficas y entidades geopolíticas. |
| Organización | String | Empresas, grupos políticos, bandas musicales, clubs deportivos, organismos gubernamentales y organizaciones públicas. |
| Evento | String | Eventos históricos, sociales y naturales. |
| Producto | String |Objetos físicos de varias categorías. |
| Habilidad | String | Capacidad, aptitud o experiencia. |
| Dirección | String | Dirección de correo postal completa. |
| Número de teléfono | String| Números de teléfono. | 
| Email | String | Dirección de correo electrónico. |
| URL | String | Direcciones URL y vínculos del sitio web. |
| Dirección IP | String | Direcciones IP de red. |
| DateTime | String | Fechas y horas del día. |
| Cantidad | String | Unidades y medidas numéricas. |

## <a name="considerations"></a>Consideraciones

* La extracción de entidades puede ser útil en escenarios en los que se quieren validar los valores extraídos. Las entidades se extraen en todo el contenido de los documentos y no solo en los valores extraídos.

* Las claves son intervalos de texto extraídos del documento; en el caso de los documentos semiestructurados, es posible que las claves deban asignarse a un diccionario de claves existente.

* Espere ver los pares clave-valor con una clave, pero ningún valor. Por ejemplo, si un usuario decide no proporcionar una dirección de correo electrónico en el formulario.

## <a name="next-steps"></a>Pasos siguientes

* Siga nuestra [**guía de migración de Form Recognizer v3.0**](v3-migration-guide.md) para obtener información sobre cómo usar la versión preliminar en las aplicaciones y flujos de trabajo.

* Explore nuestra [**API de REST (versión preliminar)**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument) para obtener más información sobre la versión preliminar y las nuevas funcionalidades.

> [!div class="nextstepaction"]
> [Prueba de Form Recognizer Studio](https://formrecognizer.appliedai.azure.com/studio)

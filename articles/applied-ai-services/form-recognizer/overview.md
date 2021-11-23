---
title: ¿Qué es Azure Form Recognizer? (actualizado)
titleSuffix: Azure Applied AI Services
description: El servicio Azure Form Recognizer le permite identificar y extraer pares clave-valor y datos de tabla de los documentos de formulario, así como información importante de los recibos de ventas y las tarjetas de presentación.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 11/16/2021
ms.author: lajanuar
recommendations: false
keywords: automated data processing, document processing, automated data entry, forms processing
ms.custom: ignite-fall-2021
ms.openlocfilehash: af81466257839563cd09917335e8eedca107ec0b
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132708789"
---
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD024 -->
# <a name="what-is-azure-form-recognizer"></a>¿Qué es Azure Form Recognizer?

:::image type="content" source="media/form-recognizer-icon.png" alt-text="Icono de Form Recognizer en Azure Portal.":::

Azure Form Recognizer es un [servicio de Azure Applied AI Services](../../applied-ai-services/index.yml) basado en la nube que usa modelos de aprendizaje automático para extraer y analizar campos de formulario, texto y tablas de los documentos. Form Recognizer analiza los formularios y documentos, extrae texto y datos, asigna relaciones de campo como pares clave-valor y devuelve una salida JSON estructurada. Obtendrá rápidamente resultados precisos a la medida de su contenido específico sin necesidad de una intervención manual excesiva o una amplia experiencia en ciencia de datos. Use Form Recognizer para automatizar el procesamiento de datos en aplicaciones y flujos de trabajo, mejorar las estrategias basadas en datos y enriquecer las funcionalidades de búsqueda de documentos.

Form Recognizer identifica, extrae y analiza fácilmente los siguientes datos de los documentos:

* Estructura y contenido de la tabla.
* Elementos de formulario y valores de campo.
* Texto alfanumérico escrito y escrito a mano.
* Relaciones entre elementos.
* Pares clave-valor.
* Ubicación de elementos con coordenadas de rectángulo de selección.

## <a name="form-recognizer-features-and-development-options"></a>Características y opciones de desarrollo de Form Recognizer

### <a name="form-recognizer-ga-v21"></a>[Disponibilidad general de Form Recognizer (v2.1)](#tab/v2-1)

Las siguientes características son compatibles con Form Recognizer v2.1. Use los vínculos de la tabla para obtener más información sobre cada característica y examinar las referencias de API.

| Característica | Descripción | Opciones de desarrollo |
|----------|--------------|-------------------------|
|[**API de diseño**](concept-layout.md) | Extracción y análisis de texto, marcas de selección y estructuras de tabla, junto con sus coordenadas de rectángulo de selección, de formularios y documentos. | <ul><li>[**Herramienta de etiquetado Form Recognizer**](quickstarts/try-sample-label-tool.md#analyze-layout)</li><li>[**API DE REST**](quickstarts/get-started-sdk-rest-api.md#try-it-layout-model)</li><li>[**SDK de biblioteca cliente**](quickstarts/try-sdk-rest-api.md)</li><li>[**Contenedor de Docker de Form Recognizer**](containers/form-recognizer-container-install-run.md?branch=main&tabs=layout#run-the-container-with-the-docker-compose-up-command)</li></ul>|
|[**Modelo personalizado**](concept-custom.md) | Extracción y análisis de datos de formularios y documentos específicos de distintos datos empresariales y casos de uso.| <ul><li>[**Herramienta de etiquetado Form Recognizer**](quickstarts/try-sample-label-tool.md#train-a-custom-form-model)</li><li>[**API DE REST**](quickstarts/try-sdk-rest-api.md)</li><li>[**SDK de biblioteca cliente**](how-to-guides/try-sdk-rest-api.md)</li><li>[**Contenedor de Docker de Form Recognizer**](containers/form-recognizer-container-install-run.md?tabs=custom#run-the-container-with-the-docker-compose-up-command)</li></ul>|
|[**Modelo de factura**](concept-invoice.md) | Procesamiento automatizado de datos y extracción de información clave de facturas. | <ul><li>[**Herramienta de etiquetado Form Recognizer**](quickstarts/try-sample-label-tool.md#analyze-using-a-prebuilt-model)</li><li>[**API DE REST**](quickstarts/get-started-sdk-rest-api.md#try-it-prebuilt-model)</li><li>[**SDK de biblioteca cliente**](quickstarts/try-sdk-rest-api.md)</li><li>[**Contenedor de Docker de Form Recognizer**](containers/form-recognizer-container-install-run.md?tabs=invoice#run-the-container-with-the-docker-compose-up-command)</li></ul>|
|[**Modelo de recibo**](concept-receipt.md) | Procesamiento automatizado de datos y extracción de información clave de recibos de compra.| <ul><li>[**Herramienta de etiquetado Form Recognizer**](quickstarts/try-sample-label-tool.md#analyze-using-a-prebuilt-model)</li><li>[**API DE REST**](quickstarts/get-started-sdk-rest-api.md#try-it-prebuilt-model)</li><li>[**SDK de biblioteca cliente**](how-to-guides/try-sdk-rest-api.md)</li><li>[**Contenedor de Docker de Form Recognizer**](containers/form-recognizer-container-install-run.md?tabs=receipt#run-the-container-with-the-docker-compose-up-command)</li></ul>|
|[**Modelo de documentación de id.**](concept-id-document.md) | Procesamiento automatizado de datos y extracción de información clave de permisos de conducir de EE. UU. y pasaportes internacionales.| <ul><li>[**Herramienta de etiquetado Form Recognizer**](quickstarts/try-sample-label-tool.md#analyze-using-a-prebuilt-model)</li><li>[**API DE REST**](quickstarts/get-started-sdk-rest-api.md#try-it-prebuilt-model)</li><li>[**SDK de biblioteca cliente**](how-to-guides/try-sdk-rest-api.md)</li><li>[**Contenedor de Docker de Form Recognizer**](containers/form-recognizer-container-install-run.md?tabs=id-document#run-the-container-with-the-docker-compose-up-command)</li></ul>|
|[**Modelo de tarjeta de presentación**](concept-business-card.md) | Procesamiento automatizado de datos y extracción de información clave de tarjetas de presentación.| <ul><li>[**Herramienta de etiquetado Form Recognizer**](quickstarts/try-sample-label-tool.md#analyze-using-a-prebuilt-model)</li><li>[**API DE REST**](quickstarts/get-started-sdk-rest-api.md#try-it-prebuilt-model)</li><li>[**SDK de biblioteca cliente**](how-to-guides/try-sdk-rest-api.md)</li><li>[**Contenedor de Docker de Form Recognizer**](containers/form-recognizer-container-install-run.md?tabs=business-card#run-the-container-with-the-docker-compose-up-command)</li></ul>|

### <a name="form-recognizer-preview-v30"></a>[Versión preliminar de Form Recognizer (v3.0)](#tab/v3-0)

El servicio Form Recognizer v3.0 admite las siguientes características y opciones de desarrollo. Use los vínculos de la tabla para obtener más información sobre cada característica y examinar las referencias de API.

| Característica | Descripción | Opciones de desarrollo |
|----------|--------------|-------------------------|
|[🆕 **Modelo de documento general**](concept-general-document.md)|Extraiga texto, tablas, estructuras, pares clave-valor y entidades con nombre.|<ul ><li>[**Form Recognizer Studio**](quickstarts/try-v3-form-recognizer-studio.md#prebuilt-models)</li><li>[**API DE REST**](quickstarts/try-v3-rest-api.md#try-it-general-document-model)</li><li>[**SDK DE C#**](quickstarts/try-v3-csharp-sdk.md#try-it-general-document-model)</li><li>[**SDK de Python**](quickstarts/try-v3-python-sdk.md#try-it-general-document-model)</li><li>[**SDK de Java**](quickstarts/try-v3-java-sdk.md#try-it-general-document-model)</li><li>[**JavaScript**](quickstarts/try-v3-javascript-sdk.md#try-it-general-document-model)</li></ul> |
|[**Modelo de diseño**](concept-layout.md) | Extraiga de los documentos y formularios texto, marcas de selección y estructuras de tablas, junto con las coordenadas de sus rectángulos de selección.</br></br> La API de diseño se ha actualizado a un modelo precompilado. | <ul><li>[**Form Recognizer Studio**](quickstarts/try-v3-form-recognizer-studio.md#layout)</li><li>[**API DE REST**](quickstarts/try-v3-rest-api.md#try-it-layout-model)</li><li>[**SDK DE C#**](quickstarts/try-v3-csharp-sdk.md#try-it-layout-model)</li><li>[**SDK de Python**](quickstarts/try-v3-python-sdk.md#try-it-layout-model)</li><li>[**SDK de Java**](quickstarts/try-v3-java-sdk.md#try-it-layout-model)</li><li>[**JavaScript**](quickstarts/try-v3-javascript-sdk.md#try-it-layout-model)</li></ul>|
|[**Modelo personalizado (actualizado)**](concept-custom.md) | Extracción y análisis de datos de formularios y documentos específicos de distintos datos empresariales y casos de uso.</br></br>La API del modelo personalizado (v3.0) admite la **detección de firmas de formularios personalizados**.</li></ul>| <ul><li>[**Form Recognizer Studio**](quickstarts/try-v3-form-recognizer-studio.md#custom-model-basics)</li><li>[**API DE REST**](quickstarts/try-v3-rest-api.md)</li><li>[**SDK DE C#**](quickstarts/try-v3-csharp-sdk.md)</li><li>[**SDK de Python**](quickstarts/try-v3-python-sdk.md)</li><li>[**SDK de Java**](quickstarts/try-v3-java-sdk.md)</li><li>[**JavaScript**](quickstarts/try-v3-javascript-sdk.md)</li></ul>|
|[**Modelo de factura**](concept-invoice.md) | Procesamiento automatizado de datos y extracción de información clave de facturas. | <ul><li>[**Form Recognizer Studio**](quickstarts/try-v3-form-recognizer-studio.md#prebuilt-models)</li><li>[**API DE REST**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)</li><li>[**SDK DE C#**](quickstarts/try-v3-csharp-sdk.md#try-it-prebuilt-model)</li><li>[**SDK de Python**](quickstarts/try-v3-python-sdk.md#try-it-prebuilt-model)</li></ul>|
|[**Modelo de recibo (actualizado)**](concept-receipt.md) | Procesamiento automatizado de datos y extracción de información clave de recibos de compra.</br></br>El modelo de recibo v3.0 admite el procesamiento de **recibos de hotel de una sola página**.| <ul><li>[**Form Recognizer Studio**](quickstarts/try-v3-form-recognizer-studio.md#prebuilt-models)</li><li>[**API DE REST**](quickstarts/try-v3-rest-api.md)</li><li>[**SDK DE C#**](quickstarts/try-v3-csharp-sdk.md#try-it-prebuilt-model)</li><li>[**SDK de Python**](quickstarts/try-v3-python-sdk.md#try-it-prebuilt-model)</li><li>[**SDK de Java**](quickstarts/try-v3-java-sdk.md#try-it-prebuilt-model)</li><li>[**JavaScript**](quickstarts/try-v3-javascript-sdk.md#try-it-prebuilt-model)</li></ul>|
|[**Modelo de documento de identificación (actualizado)**](concept-id-document.md) |Procesamiento automatizado de datos y extracción de información clave de permisos de conducir de EE. UU. y pasaportes internacionales.</br></br>La API de documentos de identificación precompilada admite la extracción de **aprobaciones, restricciones y clasificación de vehículos de permisos de conducir de EE. UU.** |<ul><li> [**Form Recognizer Studio**](quickstarts/try-v3-form-recognizer-studio.md#prebuilt-models)</li><li>[**API DE REST**](quickstarts/try-v3-rest-api.md)</li><li>[**SDK DE C#**](quickstarts/try-v3-csharp-sdk.md#try-it-prebuilt-model)</li><li>[**SDK de Python**](quickstarts/try-v3-python-sdk.md#try-it-prebuilt-model)</li><li>[**SDK de Java**](quickstarts/try-v3-java-sdk.md#try-it-prebuilt-model)</li><li>[**JavaScript**](quickstarts/try-v3-javascript-sdk.md#try-it-prebuilt-model)</li></ul>|
|[**Modelo de tarjeta de presentación**](concept-business-card.md) |Procesamiento automatizado de datos y extracción de información clave de tarjetas de presentación.| <ul><li>[**Form Recognizer Studio**](quickstarts/try-v3-form-recognizer-studio.md#prebuilt-models)</li><li>[**API DE REST**](quickstarts/try-v3-rest-api.md)</li><li>[**SDK DE C#**](quickstarts/try-v3-csharp-sdk.md#try-it-prebuilt-model)</li><li>[**SDK de Python**](quickstarts/try-v3-python-sdk.md#try-it-prebuilt-model)</li><li>[**SDK de Java**](quickstarts/try-v3-java-sdk.md#try-it-prebuilt-model)</li><li>[**JavaScript**](quickstarts/try-v3-javascript-sdk.md#try-it-prebuilt-model)</li></ul>|

---

## <a name="which-form-recognizer-feature-should-i-use"></a>¿Qué característica de Form Recognizer debo usar?

Esta sección le ayuda a decidir la característica de Form Recognizer que debe usar para la aplicación.

| ¿Qué tipo de documento desea analizar?| ¿Qué formato tiene el documento? | Su mejor solución |
| -----------------|-------------------| ----------|
|<ul><li>**Factura**</li><li>**Recibo**</li><li>**Tarjeta de presentación**</li></ul>| ¿El documento de factura, recibo o tarjeta de presentación está redactado en inglés? | <ul><li>Si la respuesta es **Sí**, use el modelo [**Factura**](concept-invoice.md), [**Recibo**](concept-receipt.md) o [**Tarjeta de presentación**](concept-business-card.md).</li><li>Si la respuesta es **No**, use el modelo [**Diseño**](concept-layout.md) o [**Documento general (versión preliminar)**](concept-general-document.md).</li></ul>|
|<ul><li>**Documento de identificación**</li></ul>| ¿El documento de identificación es un permiso de conducir de EE. UU. o un pasaporte internacional?| <ul><li>Si la respuesta es **Sí**, use el modelo de [**Documento de identificación**](concept-id-document.md).</li><li>Si la respuesta es **No**, use el modelo [**Diseño**](concept-layout.md) o [**Documento general (versión preliminar)**](concept-general-document.md).</li></ul>|
 |<ul><li>**Formulario** o **documento**</li></ul>| ¿Su formulario o documento tiene un formato estándar del sector que se usa habitualmente en su empresa o sector?| <ul><li>Si la respuesta es **Sí**, use [**Diseño**](concept-layout.md) o [**Documento general (versión preliminar)**](concept-general-document.md).</li><li>Si la respuesta es **No**, puede [**Entrenar y crear un modelo personalizado**](quickstarts/try-sample-label-tool.md#train-a-custom-form-model).

## <a name="how-to-use-form-recognizer-documentation"></a>Uso de la documentación de Form Recognizer

Esta documentación contiene los siguientes tipos de artículos:

* Los [**conceptos**](concept-layout.md) proporcionan explicaciones detalladas sobre la funcionalidad y las características del servicio.
* Los [**inicios rápidos**](quickstarts/try-sdk-rest-api.md) son instrucciones de inicio que le guiarán a la hora de hacer solicitudes al servicio.
* Las [**guías de procedimientos**](how-to-guides/try-sdk-rest-api.md) contienen instrucciones para usar el servicio de una manera más específica o personalizada.
* Los [**tutoriales**](tutorial-ai-builder.md) son guías más largas que muestran cómo usar el servicio como un componente en soluciones empresariales más amplias.

## <a name="data-privacy-and-security"></a>Seguridad y privacidad de datos

 Al igual que sucede con todas las instancias de Cognitive Services, los desarrolladores que usan el servicio Form Recognizer deben estar al tanto de las directivas de Microsoft sobre los datos de los clientes. Consulte la página [Datos, privacidad y seguridad de Form Recognizer](/legal/cognitive-services/form-recognizer/fr-data-privacy-security).

## <a name="next-steps"></a>Pasos siguientes

### <a name="form-recognizer-v21"></a>[Form Recognizer v2.1](#tab/v2-1)

> [!div class="checklist"]
>
> * Pruebe nuestra [**herramienta en línea de etiquetado de ejemplo**](https://aka.ms/fott-2.1-ga/)
> * Siga el [**inicio rápido de la API REST y la biblioteca de cliente**](./quickstarts/try-sdk-rest-api.md) para empezar a extraer datos de los documentos. Se recomienda usar el servicio gratuito cuando se está aprendiendo la tecnología. Recuerde que el número de páginas gratuitas se limita a 500 al mes.
> * Explore la [**documentación de referencia de API REST**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm) para más información.
> * Si está familiarizado con una versión anterior de la API, consulte el artículo sobre [**novedades**](./whats-new.md) para más información sobre los cambios recientes.

### <a name="form-recognizer-v30"></a>[Form Recognizer v3.0](#tab/v3-0)

> [!div class="checklist"]
>
> * Pruebe [**Form Recognizer Studio**](https://formrecognizer.appliedai.azure.com)
> * Explore la [**documentación de referencia de API REST**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument) para más información.
> * Si está familiarizado con una versión anterior de la API, consulte el artículo sobre [**novedades**](./whats-new.md) para más información sobre los cambios recientes.

---

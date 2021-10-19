---
title: ¿Qué es Azure Form Recognizer? (actualizado)
titleSuffix: Azure Applied AI Services
description: El servicio Azure Form Recognizer le permite identificar y extraer pares clave-valor y datos de tabla de los documentos de formulario, así como información importante de los recibos de ventas y las tarjetas de presentación.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 10/07/2021
ms.author: lajanuar
recommendations: false
keywords: automated data processing, document processing, automated data entry, forms processing
ms.openlocfilehash: 272104444ca3389f69c639fba0984552b93c5893
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/09/2021
ms.locfileid: "129712108"
---
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD024 -->
# <a name="what-is-azure-form-recognizer"></a>¿Qué es Azure Form Recognizer?

Azure Form Recognizer es uno de los servicios de [Azure Applied AI Services](../../applied-ai-services/index.yml) y permite crear software de procesamiento de documentos automatizado mediante la tecnología de aprendizaje automático. Form Recognizer analiza los formularios y documentos, extrae texto y datos, asigna relaciones de campo como pares clave-valor y devuelve una salida JSON estructurada. Obtendrá rápidamente resultados precisos a la medida de su contenido específico sin necesidad de una intervención manual excesiva o una amplia experiencia en ciencia de datos. Use Form Recognizer para automatizar el procesamiento de datos en aplicaciones y flujos de trabajo, mejorar las estrategias basadas en datos y enriquecer las funcionalidades de búsqueda de documentos.

Form Recognizer identifica, extrae y analiza fácilmente los siguientes datos de los documentos:

* Estructura y contenido de la tabla.
* Elementos de formulario y valores de campo.
* Texto alfanumérico escrito y escrito a mano.
* Relaciones entre elementos.
* Pares clave-valor.
* Ubicación de elementos con coordenadas de rectángulo de selección.

Esta documentación contiene los siguientes tipos de artículos:

* Los [**conceptos**](concept-layout.md) proporcionan explicaciones detalladas sobre la funcionalidad y las características del servicio.
* Los [**inicios rápidos**](quickstarts/try-sdk-rest-api.md) son instrucciones de inicio que le guiarán a la hora de hacer solicitudes al servicio.
* Las [**guías de procedimientos**](build-training-data-set.md) contienen instrucciones para usar el servicio de una manera más específica o personalizada.
* Los [**tutoriales**](tutorial-ai-builder.md) son guías más largas que muestran cómo usar el servicio como un componente en soluciones empresariales más amplias.

## <a name="form-recognizer-features-and-development-options"></a>Características y opciones de desarrollo de Form Recognizer

### <a name="form-recognizer-ga-v21"></a>[Disponibilidad general de Form Recognizer (v2.1)](#tab/v2-1)

El servicio Form Recognizer v2.1 admite las siguientes características. Use los vínculos de la tabla para obtener más información sobre cada característica y examinar las referencias de API.

| Característica | Descripción | Opciones de desarrollo |
|----------|--------------|-------------------------|
|[**API de diseño**](concept-layout.md) | Extracción y análisis de texto, marcas de selección y estructuras de tabla, junto con sus coordenadas de rectángulo de selección, de formularios y documentos. | <ul><li>[**Herramienta de etiquetado Form Recognizer**](https://fott-2-1.azurewebsites.net/layout-analyze)</li><li>[**API DE REST**](quickstarts/try-sdk-rest-api.md?pivots=programming-language-rest-api#analyze-layout)</li><li>[**SDK de biblioteca cliente**](quickstarts/try-sdk-rest-api.md)</li><li>[**Contenedor de Docker de Form Recognizer**](containers/form-recognizer-container-install-run.md?branch=main&tabs=layout#run-the-container-with-the-docker-compose-up-command)</li></ul>|
|[**Modelo de tarjeta de presentación**](concept-business-card.md) | Procesamiento automatizado de datos y extracción de información clave de tarjetas de presentación.| <ul><li>[**Herramienta de etiquetado Form Recognizer**](https://fott-2-1.azurewebsites.net/prebuilts-analyze)</li><li>[**API DE REST**](quickstarts/try-sdk-rest-api.md?pivots=programming-language-rest-api#analyze-business-cards)</li><li>[**SDK de biblioteca cliente**](quickstarts/try-sdk-rest-api.md)</li><li>[**Contenedor de Docker de Form Recognizer**](containers/form-recognizer-container-install-run.md?tabs=business-card#run-the-container-with-the-docker-compose-up-command)</li></ul>|
|[**Modelo de documentación de id.**](concept-id-document.md) | Procesamiento automatizado de datos y extracción de información clave de permisos de conducir de EE. UU. y pasaportes internacionales.| <ul><li>[**Herramienta de etiquetado Form Recognizer**](https://fott-2-1.azurewebsites.net/prebuilts-analyze)</li><li>[**API DE REST**](quickstarts/try-sdk-rest-api.md?pivots=programming-language-rest-api#analyze-identity-id-documents)</li><li>[**SDK de biblioteca cliente**](quickstarts/try-sdk-rest-api.md)</li><li>[**Contenedor de Docker de Form Recognizer**](containers/form-recognizer-container-install-run.md?tabs=id-document#run-the-container-with-the-docker-compose-up-command)</li></ul>|
|[**Modelo de factura**](concept-invoice.md) | Procesamiento automatizado de datos y extracción de información clave de facturas. | <ul><li>[**Herramienta de etiquetado Form Recognizer**](https://fott-2-1.azurewebsites.net/prebuilts-analyze)</li><li>[**API DE REST**](quickstarts/try-sdk-rest-api.md?pivots=programming-language-rest-api#analyze-invoices)</li><li>[**SDK de biblioteca cliente**](quickstarts/try-sdk-rest-api.md)</li><li>[**Contenedor de Docker de Form Recognizer**](containers/form-recognizer-container-install-run.md?tabs=invoice#run-the-container-with-the-docker-compose-up-command)</li></ul>|
|[**Modelo de recibo**](concept-receipt.md) | Procesamiento automatizado de datos y extracción de información clave de recibos de compra.| <ul><li>[**Herramienta de etiquetado Form Recognizer**](https://fott-2-1.azurewebsites.net/prebuilts-analyze)</li><li>[**API DE REST**](quickstarts/try-sdk-rest-api.md?pivots=programming-language-rest-api#analyze-receipts)</li><li>[**SDK de biblioteca cliente**](quickstarts/try-sdk-rest-api.md)</li><li>[**Contenedor de Docker de Form Recognizer**](containers/form-recognizer-container-install-run.md?tabs=receipt#run-the-container-with-the-docker-compose-up-command)</li></ul>|
|[**Modelo personalizado**](concept-business-card.md) | Extracción y análisis de datos de formularios y documentos específicos de distintos datos empresariales y casos de uso.| <ul><li>[**Herramienta de etiquetado Form Recognizer**](https://fott-2-1.azurewebsites.net)</li><li>[**API DE REST**](quickstarts/try-sdk-rest-api.md?pivots=programming-language-rest-api#analyze-forms-with-a-custom-model)</li><li>[**SDK de biblioteca cliente**](quickstarts/try-sdk-rest-api.md)</li><li>[**Contenedor de Docker de Form Recognizer**](containers/form-recognizer-container-install-run.md?tabs=custom#run-the-container-with-the-docker-compose-up-command)</li></ul>|

### <a name="form-recognizer-preview-v30"></a>[Versión preliminar de Form Recognizer (v3.0)](#tab/v3-0)

>[!NOTE]
> Form Recognizer Studio está actualmente en versión preliminar pública. Es posible que algunas características no se admitan o que tengan funcionalidades limitadas.

El servicio Form Recognizer v3.0 admite las siguientes características y opciones de desarrollo. Use los vínculos de la tabla para obtener más información sobre cada característica y examinar las referencias de API.

| Característica | Descripción | Opciones de desarrollo |
|----------|--------------|-------------------------|
|[🆕 **Modelo de documento general**](concept-general-document.md)|Extraiga texto, tablas, estructuras, pares clave-valor y entidades con nombre.|<ul ><li>[**Form Recognizer Studio**](https://formrecognizer.appliedai.azure.com)</li><li>[**API DE REST**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)</li><li>[**SDK DE C#**](quickstarts/try-v3-csharp-sdk.md)</li><li>[**SDK de Python**](quickstarts/try-v3-python-sdk.md)</li></ul> |
|[**Modelo de diseño**](concept-layout.md) | Extraiga de los documentos y formularios texto, marcas de selección y estructuras de tablas, junto con las coordenadas de sus rectángulos de selección.</br></br> La API de diseño se ha actualizado a un modelo precompilado. | <ul><li>[**Form Recognizer Studio**](https://formrecognizer.appliedai.azure.com)</li><li>[**API DE REST**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)</li><li>[**SDK DE C#**](quickstarts/try-v3-csharp-sdk.md)</li><li>[**SDK de Python**](quickstarts/try-v3-python-sdk.md)</li></ul>|
|[**Modelo personalizado (actualizado)** ](concept-custom.md) | Extracción y análisis de datos de formularios y documentos específicos de distintos datos empresariales y casos de uso.</br></br>La API del modelo personalizado (v3.0) admite la **detección de firmas de formularios personalizados**.</li></ul>| <ul><li>[**Form Recognizer Studio**](https://fott-2-1.azurewebsites.net)</li><li>[**API DE REST**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)</li><li>[**SDK DE C#**](quickstarts/try-v3-csharp-sdk.md)</li><li>[**SDK de Python**](quickstarts/try-v3-python-sdk.md)</li></ul>|
|[**Modelo de recibo (actualizado)** ](concept-receipt.md) | Procesamiento automatizado de datos y extracción de información clave de recibos de compra.</br></br>El modelo de recibo v3.0 admite el procesamiento de **recibos de hotel de una sola página**.| <ul><li>[**Form Recognizer Studio**](https://formrecognizer.appliedai.azure.com)</li><li>[**API DE REST**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)</li><li>[**SDK DE C#**](quickstarts/try-v3-csharp-sdk.md)</li><li>[**SDK de Python**](quickstarts/try-v3-python-sdk.md)</li></ul>|
|[**Modelo de documento de identificación (actualizado)** ](concept-id-document.md) |Procesamiento automatizado de datos y extracción de información clave de permisos de conducir de EE. UU. y pasaportes internacionales.</br></br>La API de documentos de identificación precompilada admite la extracción de **aprobaciones, restricciones y clasificación de vehículos de permisos de conducir de EE. UU.** |<ul><li> [**Form Recognizer Studio**](https://formrecognizer.appliedai.azure.com)</li><li>[**API DE REST**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)</li><li>[**SDK DE C#**](quickstarts/try-v3-csharp-sdk.md)</li><li>[**SDK de Python**](quickstarts/try-v3-python-sdk.md)</li></ul>|
|[**Modelo de factura**](concept-invoice.md) | Procesamiento automatizado de datos y extracción de información clave de facturas. | <ul><li>[**Form Recognizer Studio**](https://formrecognizer.appliedai.azure.com)</li><li>[**API DE REST**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)</li><li>[**SDK DE C#**](quickstarts/try-v3-csharp-sdk.md)</li><li>[**SDK de Python**](quickstarts/try-v3-python-sdk.md)</li></ul>|
|[**Modelo de tarjeta de presentación**](concept-business-card.md) |Procesamiento automatizado de datos y extracción de información clave de tarjetas de presentación.| <ul><li>[**Form Recognizer Studio**](https://formrecognizer.appliedai.azure.com)</li><li>[**API DE REST**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)</li><li>[**SDK DE C#**](quickstarts/try-v3-csharp-sdk.md)</li><li>[**SDK de Python**](quickstarts/try-v3-python-sdk.md)</li></ul>|

---

## <a name="prerequisites"></a>Prerrequisitos

* Necesitará una suscripción a Azure: [**cree una gratuita**](https://azure.microsoft.com/free/cognitive-services).

* Cuando tenga la suscripción, cree un [**recurso de Form Recognizer**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) en Azure Portal. Puede usar el plan de tarifa gratis (F0) para probar el servicio y actualizarlo más adelante a un plan de pago para producción.

* Por último, deberá recuperar la **dirección URL del punto de conexión** y la **clave de API** del recurso en Azure Portal para probar el servicio Form Recognizer:

  :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="Captura de pantalla: claves y ventana del punto de conexión en Azure Portal.":::

 ## <a name="form-recognizer-workflows"></a>Flujos de trabajo de Form Recognizer

| Tipo de documento | Consideraciones | Solución |
| -----------------|-------------------| ----------|
|<ul><li>**Factura**</li><li>**Recibo**</li><li>**Tarjeta de presentación**</li></ul>| ¿El documento de factura, recibo o tarjeta de presentación está redactado en inglés? | <ul><li>Sí → Modelo de [**factura**](concept-invoice.md), [**recibo**](concept-receipt.md) o [**tarjeta de presentación**](concept-business-card.md)</li><li>No → Modelo de [**diseño**](concept-layout.md) o [**documento general (versión preliminar)** ](concept-general-document.md)</li></ul>|
|<ul><li>**Documento de identificación**</li></ul>| ¿El documento de identificación es un permiso de conducir de EE. UU. o un pasaporte internacional?| <ul><li>Sí → Modelo de [**documento de identificación**](concept-id-document.md)</li><li>No → Modelo de [**diseño**](concept-layout.md) o [**documento general (versión preliminar)** ](concept-general-document.md)</li></ul>|
|<ul><li>**Formulario** o **documento**</li></ul>| ¿Su formulario o documento tiene un formato estándar del sector que se usa habitualmente en su empresa o sector?| <ul><li>Sí → Modelo de [**diseño**](concept-id-document.md) o [**documento general (versión preliminar)** ](concept-general-document.md)</li><li>No → [**Entrene y cree un modelo personalizado**](concept-layout.md) 

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

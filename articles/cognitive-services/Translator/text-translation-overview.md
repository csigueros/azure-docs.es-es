---
title: ¿Qué es Text Translation de Microsoft Azure Cognitive Services?
titlesuffix: Azure Cognitive Services
description: Integre Text Translation API en sus aplicaciones, sitios web, herramientas u otras soluciones para proporcionar experiencias de usuario en varios idiomas.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.topic: overview
ms.subservice: translator-text
ms.date: 08/09/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-aug-2020
keywords: translator, text translation, machine translation, translation service, custom translator
ms.openlocfilehash: 68354706935d0dcfb29055a1eb5b0e273022d306
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121787966"
---
# <a name="what-is-text-translation"></a>¿Qué es Text Translation?

 Text Translation es una característica de API REST basada en la nube del servicio Translator que usa la tecnología de traducción automática neuronal para permitir la traducción de texto de origen a destino rápida y precisa en tiempo real en todos los [idiomas admitidos](language-support.md). En esta introducción, aprenderá cómo Text Translation API REST le permite crear soluciones inteligentes para sus aplicaciones y flujos de trabajo.

La documentación de Text Translation contiene los siguientes tipos de artículos:

* [**Guías de inicio rápido**](quickstart-translator.md). Instrucciones de inicio rápido que le guían a la hora de hacer solicitudes al servicio.
* [**Guías paso a paso**](translator-how-to-signup.md) Instrucciones de acceso y uso del servicio de una manera más específica o personalizada.
* [**Artículos de referencia**](reference/v3-0-reference.md). Documentación de la API REST y contenido basado en lenguaje de programación.

## <a name="text-translation-features"></a>Características de Text Translation

 La característica Text Translation admite los métodos siguientes:

* [**Idiomas**](reference/v3-0-languages.md). Devuelve una lista de los idiomas admitidos por las operaciones **Translate**, **Transliterate** y **Dictionary Lookup**. Esta solicitud no requiere autenticación; simplemente copie y pegue la siguiente solicitud GET en Postman o en su explorador o herramienta de API favoritos:

    ```http
    https://api.cognitive.microsofttranslator.com/languages?api-version=3.0
    ```

* [**Translate**](reference/v3-0-translate.md#translate-to-multiple-languages). Traduce texto de un solo idioma de origen en textos en varios idiomas de destino con una sola solicitud.

* [**Transliterate**](reference/v3-0-transliterate.md). Convierte caracteres o letras de un idioma de origen en los caracteres o letras correspondientes de un idioma de destino.

* [**Detect**](reference/v3-0-detect.md). Devuelve el código fuente del idioma y una variable booleana que indica si el idioma detectado es compatible con la traducción y la transliteración de texto.

    > [!NOTE]
    > Puede **traducir, transliterar y detectar** texto con [una sola llamada API REST](reference/v3-0-translate.md#translate-a-single-input-with-language-autodetection).

* [**Dictionary lookup**](reference/v3-0-dictionary-lookup.md). Devuelve palabras equivalentes del término de origen en el idioma de destino.
* [**Dictionary example**](reference/v3-0-dictionary-examples.md): devuelve ejemplos de estructura gramatical y contexto para el par formado por el término de origen y el término de destino.

## <a name="text-translation-deployment-options"></a>Opciones de implementación de Text Translation

Agregue Text Translation a los proyectos y aplicaciones mediante los siguientes recursos:

* Acceda al servicio Translator basado en la nube a través de la [**API REST**](reference/rest-api-guide.md), disponible en Azure.

* Use la [solicitud de traducción](containers/translator-container-supported-parameters.md) de la API REST con el [**contenedor Docker de Text Translation**](containers/translator-how-to-install-container.md).

    > [!IMPORTANT]
    >
    > * El contenedor de Translator está en versión preliminar "validada". Para usarlo, debe completar y enviar el formulario de solicitud en línea [**Solicitud de Azure Cognitive Services de servicios controlados**](https://aka.ms/csgate-translator) y obtener su aprobación para conseguir acceso al contenedor.
    >
    > * La [**imagen de contenedor de Translator**](https://hub.docker.com/_/microsoft-azure-cognitive-services-translator-text-translation) admite características limitadas en comparación con las ofertas en la nube.
    >

## <a name="get-started-with-text-translation"></a>Introducción a Text Translation

¿Está listo para comenzar?

* [**Creación de un recurso de Translator** ](translator-how-to-signup.md "Vaya a Azure Portal.") en Azure Portal.

* [**Obtenga las claves de acceso y el punto de conexión de API**](translator-how-to-signup.md#authentication-keys-and-endpoint-url). Se requieren una dirección URL de punto de conexión y una clave de solo lectura para la autenticación.

* Explore nuestro [**inicio rápido**](quickstart-translator.md "Aprenda a usar Translator a través de REST y un lenguaje de programación preferido.") y vea los casos de uso y ejemplos de código para los siguientes lenguajes de programación: 
  * [**C#/.NET**](quickstart-translator.md?tabs=csharp)
  * [**Go**](quickstart-translator.md?tabs=go)
  * [**Java**](quickstart-translator.md?tabs=java)
  * [**JavaScript/Node.js**](quickstart-translator.md?tabs=nodejs)
  * [**Python**](quickstart-translator.md?tabs=python)

## <a name="next-steps"></a>Pasos siguientes

Profundice en Text Translation REST API:

> [!div class="nextstepaction"]
> [Consultar la referencia de la API REST](./reference/v3-0-reference.md)

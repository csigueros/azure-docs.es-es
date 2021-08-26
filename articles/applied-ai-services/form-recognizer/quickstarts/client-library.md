---
title: 'Inicio rápido: Biblioteca cliente o API REST de Form Recognizer'
titleSuffix: Azure Applied AI Services
description: Use la biblioteca cliente o API REST de Form Recognizer con el fin de crear una aplicación de procesamiento de formularios que extraiga pares clave-valor y datos de tabla de los documentos personalizados.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 04/14/2021
ms.author: lajanuar
zone_pivot_groups: programming-languages-set-formre
ms.custom: devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
keywords: forms processing, automated data processing
ms.openlocfilehash: 898d7628ea96da82095ce5ff9e6fbd0a1fd6227a
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122327875"
---
# <a name="quickstart-get-started-with-the-client-library-sdks-or-rest-api"></a>Inicio rápido: Introducción a los SDK o API REST de la biblioteca cliente

Comience a usar Azure Form Recognizer mediante el lenguaje de programación que prefiera. Azure Form Recognizer es uno de los servicios de [Azure Applied AI Services](../../../applied-ai-services/index.yml) y permite crear software de procesamiento de datos automatizado mediante la tecnología de aprendizaje automático. Identifique y extraiga texto, pares clave-valor, marcas de selección, datos de tabla, etc., de los documentos de formulario (el servicio genera datos estructurados que incluyen las relaciones en el archivo original). Puede usar Form Recognizer a través de la API REST o el SDK. Se recomienda usar el servicio gratuito cuando se está aprendiendo la tecnología. Recuerde que el número de páginas gratuitas se limita a 500 al mes.

Va a utilizar las siguientes API para extraer datos estructurados de formularios y documentos:

* [Autenticar el cliente](#authenticate-the-client)
* [Análisis de diseño](#analyze-layout)
* [Análisis de las confirmaciones de recepción](#analyze-receipts)
* [Análisis de tarjetas de presentación](#analyze-business-cards)
* [Análisis de facturas](#analyze-invoices)
* [Análisis de documentos de identidad](#analyze-identity-documents)
* [Entrenar un modelo personalizado](#train-a-custom-model)
* [Analizar formularios con un modelo personalizado](#analyze-forms-with-a-custom-model)
* [Administración de modelos personalizados](#manage-custom-models)

::: zone pivot="programming-language-csharp"

[!INCLUDE [C# SDK quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [Java SDK quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [NodeJS SDK quickstart](../includes/quickstarts/javascript-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [Python SDK quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end

::: zone pivot="programming-language-rest-api"

[!INCLUDE [REST API quickstart](../includes/quickstarts/rest-api.md)]

::: zone-end
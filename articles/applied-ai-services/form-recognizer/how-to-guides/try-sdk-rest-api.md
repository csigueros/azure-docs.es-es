---
title: Uso de los SDK de la biblioteca cliente o de la API de REST de Form Recognizer
titleSuffix: Azure Applied AI Services
description: Use un SDK de la biblioteca cliente o de la API de REST de Form Recognizer con el fin de crear una aplicación de procesamiento de formularios que extraiga pares clave-valor y datos de tabla de los documentos personalizados.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 11/02/2021
ms.author: lajanuar
zone_pivot_groups: programming-languages-set-formre
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0402041121e34902d9801307dad8a759f8fe9516
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131090933"
---
# <a name="use-form-recognizer-sdks-or-rest-api"></a>Uso de los SDK o de la API de REST de Form Recognizer

 En esta guía paso a paso, aprenderá a agregar Form Recognizer a las aplicaciones y los flujos de trabajo mediante un SDK en un lenguaje de programación de su elección o la API de REST. Azure Form Recognizer es un componente de Azure Applied AI Services en la nube que usa el aprendizaje automático para extraer y analizar campos de formulario, texto y tablas de los documentos. Se recomienda usar el servicio gratuito cuando se está aprendiendo la tecnología. Recuerde que el número de páginas gratuitas se limita a 500 al mes.

Va a utilizar las siguientes API para extraer datos estructurados de formularios y documentos:

* [Autenticar el cliente](#authenticate-the-client)
* [Análisis de diseño](#analyze-layout)
* [Análisis de las confirmaciones de recepción](#analyze-receipts)
* [Análisis de tarjetas de presentación](#analyze-business-cards)
* [Análisis de facturas](#analyze-invoices)
* [Análisis de documentos de identificación](#analyze-id-documents)
* [Entrenar un modelo personalizado](#train-a-custom-model)
* [Analizar formularios con un modelo personalizado](#analyze-forms-with-a-custom-model)
* [Administración de modelos personalizados](#manage-custom-models)

::: zone pivot="programming-language-csharp"

[!INCLUDE [C# SDK quickstart](../includes/how-to-guides/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [Java SDK quickstart](../includes/how-to-guides/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [NodeJS SDK quickstart](../includes/how-to-guides/javascript-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [Python SDK quickstart](../includes/how-to-guides/python-sdk.md)]

::: zone-end

::: zone pivot="programming-language-rest-api"

[!INCLUDE [REST API quickstart](../includes/how-to-guides/rest-api.md)]

::: zone-end

---
title: 'Inicio rápido: Minería de datos mediante la biblioteca cliente de Text Analytics'
titleSuffix: Azure Cognitive Services
description: Use este inicio rápido para realizar un análisis de sentimiento, entre otras funciones, mediante la API Text Analytics desde Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 09/23/2021
ms.author: aahi
keywords: text mining, sentiment analysis, text analytics
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-text-analytics
ms.openlocfilehash: 169aa30b28dfc0ad207df3719e2769e2f75b1664
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128672464"
---
# <a name="quickstart-use-the-text-analytics-client-library-and-rest-api"></a>Inicio rápido: Uso de la biblioteca cliente y la API REST de Text Analytics

Use este artículo para empezar a trabajar con la biblioteca cliente y la API REST de Text Analytics. Siga estos pasos para probar código de ejemplo para realizar tareas de minería de texto.


::: zone pivot="programming-language-csharp"

> [!IMPORTANT]
> * En este inicio rápido, solo se tratan las siguientes versiones de la API: v3.1 y v3.2-preview.

[!INCLUDE [C# quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

> [!IMPORTANT]
> * La versión estable más reciente de Text Analytics API es `3.1`.
> * Por motivos de simplicidad, en el código de este artículo se usan métodos sincrónicos y almacenamiento de credenciales no protegidas. En escenarios de producción, se recomienda usar los métodos asincrónicos por lotes para mayor rendimiento y escalabilidad. Para más información, consulte la siguiente documentación de referencia.
> * También puede usar la versión preliminar más reciente de la biblioteca cliente para usar el resumen extractivo. Consulte los siguientes ejemplos [en GitHub](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics/lro/AnalyzeExtractiveSummarization.java).


[!INCLUDE [Java quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

> [!IMPORTANT]
> * La versión estable más reciente de Text Analytics API es `3.1`.
>    * Asegúrese de seguir únicamente las instrucciones de la versión que está usando.
> * Por motivos de simplicidad, en el código de este artículo se usan métodos sincrónicos y almacenamiento de credenciales no protegidas. En escenarios de producción, se recomienda usar los métodos asincrónicos por lotes para mayor rendimiento y escalabilidad. Para más información, consulte la siguiente documentación de referencia.
> * Esta versión de la biblioteca cliente de Text Analytics también se puede ejecutar en el [explorador](https://github.com/Azure/azure-sdk-for-js/blob/master/documentation/Bundling.md).

[!INCLUDE [NodeJS quickstart](../includes/quickstarts/nodejs-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

> [!IMPORTANT]
> * En este inicio rápido, solo se tratan las siguientes versiones de la API: v3.1 y v3.2-preview.

[!INCLUDE [Python quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end

::: zone pivot="rest-api"

> [!IMPORTANT]
> * La versión estable más reciente de Text Analytics API es `3.1`.
>    * Asegúrese de seguir únicamente las instrucciones de la versión que está usando.

[!INCLUDE [REST API quickstart](../includes/quickstarts/rest-api.md)]

::: zone-end

## <a name="clean-up-resources"></a>Limpieza de recursos

Si quiere limpiar y eliminar una suscripción a Cognitive Services, puede eliminar el recurso o grupo de recursos. Al eliminar el grupo de recursos, también se elimina cualquier otro recurso que esté asociado a él.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI de Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Exploración de una solución](../text-analytics-user-scenarios.md#analyze-recorded-inbound-customer-calls)

* [Información general de Text Analytics](../overview.md)
* [Análisis de opiniones](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Reconocimiento de entidades](../how-tos/text-analytics-how-to-entity-linking.md)
* [Detección de idioma](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Reconocimiento de idioma](../how-tos/text-analytics-how-to-language-detection.md)

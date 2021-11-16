---
title: 'Limitaciones de los contenedores: LUIS'
titleSuffix: Azure Cognitive Services
description: Idiomas de los contenedores LUIS que se admiten.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/28/2021
ms.author: aahi
ms.openlocfilehash: cd29ef48f2d1042091a1379570e8ea710c718ff9
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131426229"
---
# <a name="language-understanding-luis-container-limitations"></a>Limitaciones de los contenedores de Language Understanding (LUIS)

Los contenedores LUIS tienen pocas limitaciones notables. Desde las dependencias no admitidas hasta un subconjunto de idiomas admitidos, en este artículo se detallan estas restricciones.

## <a name="supported-dependencies-for-latest-container"></a>Dependencias admitidas del contenedor `latest`

El último contenedor de LUIS admite:

* [Nuevos dominios creados previamente](luis-reference-prebuilt-domains.md): estos dominios empresariales incluyen entidades, expresiones de ejemplo y patrones. Amplíe estos dominios para su propio uso.

## <a name="unsupported-dependencies-for-latest-container"></a>Dependencias no admitidas del contenedor `latest`

Para [exportar para contenedor](luis-container-howto.md#export-packaged-app-from-luis), debe quitar las dependencias no admitidas de la aplicación de LUIS. Si intenta exportar el contenedor, el portal de LUIS informa de estas características no admitidas que debe quitar.

Puede usar una aplicación de LUIS si **no incluye** ninguna de las siguientes dependencias:

Configuraciones de aplicaciones no admitidas|Detalles|
|--|--|
|Referencias culturales de contenedor no admitidas| Los idiomas neerlandés (`nl-NL`), japonés (`ja-JP`) y alemán (`de-DE`) solo se admiten con la versión [1.0.2 del tokenizador](luis-language-support.md#custom-tokenizer-versions).|
|Entidades no compatibles con ninguna referencia cultural|Entidad [KeyPhrase](luis-reference-prebuilt-keyphrase.md) pregenerada para todas las referencias culturales|
|Entidades no compatibles con la referencia cultural Inglés (`en-US`)|Entidades [GeographyV2](luis-reference-prebuilt-geographyV2.md) pregeneradas|
|Preparación para la voz|El contenedor no admite dependencias externas.|
|análisis de opiniones|El contenedor no admite dependencias externas.|
|Bing Spell Check|El contenedor no admite dependencias externas.|

## <a name="languages-supported"></a>Idiomas admitidos

Los contenedores LUIS admiten un subconjunto de los [idiomas admitidos](luis-language-support.md#languages-supported) por LUIS. Los contenedores LUIS son capaces de comprender expresiones en los siguientes idiomas:

| Idioma | Configuración regional | Dominio creado previamente | Entidad creada previamente | Recomendaciones de la lista de frases | **[Análisis de sentimiento](../language-service/sentiment-opinion-mining/language-support.md) y [extracción de frases clave](../language-service/key-phrase-extraction/language-support.md)|
|--|--|:--:|:--:|:--:|:--:|
| Spanish (Traditional Sort) - Spain | `en-US` | ✔️ | ✔️ | ✔️ | ✔️ |
| Árabe (versión preliminar: Árabe estándar moderno) |`ar-AR`|❌|❌|❌|❌|
| *[Chino](#chinese-support-notes) |`zh-CN` | ✔️ | ✔️ | ✔️ | ❌ |
| Francés (Francia) |`fr-FR` | ✔️ | ✔️ | ✔️ | ✔️ |
| Francés (Canadá) |`fr-CA` | ❌ | ❌ | ❌ | ✔️ |
| Alemán |`de-DE` | ✔️ | ✔️ | ✔️ | ✔️ |
| Hindi | `hi-IN`| ❌ | ❌ | ❌ | ❌ |
| Italiano |`it-IT` | ✔️ | ✔️ | ✔️ | ✔️ |
| Coreano |`ko-KR` | ✔️ | ❌ | ❌ | Solo la *frase clave* |
| Maratí | `mr-IN`|❌|❌|❌|❌|
| Portugués (Brasil) |`pt-BR` | ✔️ | ✔️ | ✔️ | No todas las referencias culturales secundarias |
| Español (España) |`es-ES` | ✔️ | ✔️ |✔️|✔️|
| Español (México)|`es-MX` | ❌ | ❌ |✔️|✔️|
| Tamil | `ta-IN`|❌|❌|❌|❌|
| Telugu | `te-IN`|❌|❌|❌|❌|
| Turco | `tr-TR` |✔️| ❌ | ❌ | Solo *opiniones* |

[!INCLUDE [Chinese language support notes](includes/chinese-language-support-notes.md)]

[!INCLUDE [Language service support notes](includes/text-analytics-support-notes.md)]

---
title: Compatibilidad de lenguaje con Análisis de sentimiento y Minería de opiniones
titleSuffix: Azure Cognitive Services
description: En este artículo se explica qué lenguajes admiten las características de Análisis de sentimiento y Minería de opiniones de Azure Cognitive Service para lenguaje.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/08/2021
ms.author: aahi
ms.custom: language-service-sentiment-opinion-mining, ignite-fall-2021
ms.openlocfilehash: a0cbc5bab15e80bb18ca3d4a13efa2d05fe5aeff
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2021
ms.locfileid: "132027154"
---
# <a name="sentiment-analysis-and-opinion-mining-language-support"></a>Compatibilidad de lenguaje con Análisis de sentimiento y Minería de opiniones 

Use este artículo para obtener información sobre qué lenguajes naturales son compatibles con el Análisis de sentimiento y la Minería de opiniones.

> [!NOTE]
> Los idiomas se agregan a medida que se lanzan nuevas versiones del modelo. La versión actual del modelo Disponible con carácter general de Análisis de sentimiento es la `2020-04-01`. Para usar la versión preliminar más reciente del modelo (`2021-10-01-preview`), especifíquela en la llamada API. Para más información, consulte [cómo llamar a la API](how-to/call-api.md#specify-the-sentiment-analysis-model).

## <a name="sentiment-analysis-language-support"></a>Compatibilidad de lenguaje con Análisis de sentimiento

| Idioma              | Código de lenguaje | A partir de la versión del modelo: |              Notas |
|:----------------------|:-------------:|:--------------------------:|-------------------:|
| Chino simplificado    |   `zh-hans`         |         2019-10-01         | También se acepta `zh` |
| Chino (tradicional)   |   `zh-hant`         |         2019-10-01         |                    |
| Neerlandés                 |     `nl`            |         2019-10-01        |                    |
| Inglés               |     `en`            |         2019-10-01         |                    |
| Francés                |     `fr`            |         2019-10-01         |                    |
| Alemán                |     `de`            |         2019-10-01         |                    |
| Hindi                 |    `hi`             |         2020-04-01         |                    |
| Italiano               |     `it`            |         2019-10-01         |                    |
| Japonés              |     `ja`            |         2019-10-01         |                    |
| Coreano                |     `ko`            |         2019-10-01         |                    |
| Noruego (Bokmål)   |     `no`            |         2020-04-01         |                    |
| Portugués (Brasil)   |    `pt-BR`          |         2020-04-01         |                    |
| Portugués (Portugal) |    `pt-PT`          |         2019-10-01         | También se acepta `pt` |
| Español               |     `es`            |         2019-10-01         |                    |
| Turco               |     `tr`             |         2020-04-01        |                    |

### <a name="opinion-mining-language-support"></a>Compatibilidad de lenguaje con Minería de opiniones

| Idioma              | Código de lenguaje | A partir de la versión del modelo: |              Notas |
|:----------------------|:-------------:|:------------------------------------:|-------------------:|
| Inglés               |     `en`      |  2020-04-01              |                    |
| Francés                |     `fr`      |         2021-10-01-preview         |                    |
| Alemán                |     `de`      |         2021-10-01-preview         |                    |
| Italiano               |     `it`      |         2021-10-01-preview         |                    |
| Portugués (Brasil)   |    `pt-BR`    |         2021-10-01-preview         |                    |
| Portugués (Portugal) |    `pt-PT`    |         2021-10-01-preview         | También se acepta `pt` |
| Español               |     `es`      |         2021-10-01-preview         |                    |

## <a name="next-steps"></a>Pasos siguientes

[Introducción a Análisis de sentimiento y Minería de opiniones](overview.md)

---
title: ¿Qué es el análisis de sentimientos y la minería de opiniones en Azure Cognitive Service for Language?
titleSuffix: Azure Cognitive Services
description: Información general de la característica de análisis de opiniones de Azure Cognitive Services, que le ayuda a averiguar qué piensa la gente de un tema mediante la minería de texto para obtener pistas.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-sentiment-opinion-mining, ignite-fall-2021
ms.openlocfilehash: 8807b12f9920fd62901aebed862ecba483e77bb0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093096"
---
# <a name="what-is-sentiment-analysis-and-opinion-mining-in-azure-cognitive-service-for-language"></a>¿Qué es el análisis de sentimientos y la minería de opiniones en Azure Cognitive Service for Language?

El análisis de sentimiento y la minería de opiniones son características que ofrece [Azure Cognitive Service for Language](../overview.md), una colección de algoritmos de aprendizaje automático e inteligencia artificial en la nube para el desarrollo de aplicaciones inteligentes en las que interviene lenguaje escrito. Estas características le ayudan a averiguar qué piensa la gente de su marca o tema mediante la minería de texto para obtener pistas sobre opiniones positivas o negativas, y pueden asociarlas a aspectos específicos del texto. 

* Los [**inicios rápidos**](quickstart.md) son instrucciones de inicio que le guiarán a la hora de hacer solicitudes al servicio.
* Las [**guías de procedimientos**](how-to/call-api.md) contienen instrucciones para usar el servicio de una manera más específica o personalizada.

## <a name="sentiment-analysis"></a>análisis de opiniones 

La característica de análisis de sentimiento proporciona etiquetas de opinión (como "negative", "neutral" y "positive") basadas en la mayor puntuación de confianza que ha encontrado el servicio tanto en el nivel de oración como en el de documento. Esta característica también devuelve puntuaciones de confianza entre 0 y 1 para todos los documentos y que contiene, con el fin de indicar una opinión positiva, neutra y negativa. 

### <a name="deploy-on-premises-using-docker-containers"></a>Implementación local mediante contenedores de Docker

Use el contenedor de Docker disponible para [implementar el análisis de sentimiento en el entorno local](how-to/use-containers.md). Estos contenedores de Docker permiten acercar el servicio a los datos para mejorar el cumplimiento, la seguridad o por otras razones operativas.

## <a name="opinion-mining"></a>Minería de opiniones

La minería de opiniones es una característica de análisis de sentimiento. Esta característica, también conocida como Análisis de sentimiento basada en aspectos en el procesamiento de lenguaje natural (NLP), proporciona información más detallada sobre las opiniones relacionadas con palabras (como los atributos de los productos o servicios) en el texto.

[!INCLUDE [Typical workflow for pre-configured language features](../includes/overview-typical-workflow.md)]

## <a name="responsible-ai"></a>Inteligencia artificial responsable 

Los sistemas de inteligencia artificial incluyen no solo la tecnología, sino también las personas que la usarán, las personas que se verán afectadas por ella y los entornos en los que se implementan. Lea la [nota de transparencia sobre el análisis de sentimiento](/legal/cognitive-services/language-service/transparency-note-sentiment-analysis?context=/azure/cognitive-services/language-service/context/context) para obtener información sobre el uso responsable de la inteligencia artificial y la implementación en los sistemas. También puede ver los siguientes artículos para obtener más información:

[!INCLUDE [Responsible AI links](../includes/overview-responsible-ai-links.md)]

## <a name="next-steps"></a>Pasos siguientes

Hay dos maneras de empezar a usar la característica de vinculación de entidades:
* [Language Studio](../language-studio.md), que es una plataforma basada en web que permite probar varias características de Azure Cognitive Service for Language sin necesidad de escribir código.
* El [artículo de inicio rápido](quickstart.md) para obtener instrucciones sobre cómo realizar solicitudes al servicio mediante la API de REST y el SDK de la biblioteca cliente.  

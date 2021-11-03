---
title: ¿Qué es Text Analytics for health de Azure Cognitive Service for Language?
titleSuffix: Azure Cognitive Services
description: Información general sobre Text Analytics for health de Azure Cognitive Services, que le ayuda a extraer información médica de texto no estructurado, como documentos clínicos.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-health, ignite-fall-2021
ms.openlocfilehash: 609d60d2094372cd5c1e476afd3abde1df7f68af
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091104"
---
# <a name="what-is-text-analytics-for-health-in-azure-cognitive-service-for-language"></a>¿Qué es Text Analytics for health de Azure Cognitive Service for Language?

Text Analytics for health es una de las características que ofrece [Azure Cognitive Service for Language](../overview.md), una colección de algoritmos de aprendizaje automático e inteligencia artificial en la nube para el desarrollo de aplicaciones inteligentes en las que interviene lenguaje escrito. 

Text Analytics for health extrae y etiqueta información médica pertinente de textos no estructurados, como notas del médico, resúmenes de altas, documentos clínicos e historiales sanitarios electrónicos.

Esta documentación contiene los siguientes tipos de artículos:

* Los [**inicios rápidos**](quickstart.md) son instrucciones de inicio que le guiarán a la hora de hacer solicitudes al servicio.
* Las [**guías de procedimientos**](how-to/call-api.md) contienen instrucciones para usar el servicio de una manera más específica o personalizada.
* En los [**artículos conceptuales**](concepts/health-entity-categories.md) se proporcionan explicaciones detalladas de la funcionalidad y las características del servicio.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Introducing-Text-Analytics-for-Health/player]

## <a name="features"></a>Características

[!INCLUDE [Text Analytics for health](includes/features.md)]

[!INCLUDE [Typical workflow for pre-configured language features](../includes/overview-typical-workflow.md)]

## <a name="deploy-on-premises-using-docker-containers"></a>Implementación local mediante contenedores de Docker

Use el contenedor de Docker disponible para [implementar esta característica en el entorno local](how-to/use-containers.md). Estos contenedores de Docker permiten acercar el servicio a los datos para mejorar el cumplimiento, la seguridad o por otras razones operativas.

## <a name="responsible-ai"></a>Inteligencia artificial responsable 

Un sistema de inteligencia artificial incluye no solo la tecnología, sino también las personas que la usarán, las personas que se verán afectadas por ella y el entorno en el que se implementa. Lea la [nota de transparencia de Text Analytics for health](/legal/cognitive-services/language-service/transparency-note-health?context=/azure/cognitive-services/language-service/context/context) para información sobre el uso y la implementación responsables de la inteligencia artificial en los sistemas. También puede consultar los siguientes artículos para obtener más información:

[!INCLUDE [Responsible AI links](../includes/overview-responsible-ai-links.md)]

## <a name="next-steps"></a>Pasos siguientes

Hay dos maneras de empezar a usar la característica de vinculación de entidades:
* [Language Studio](../language-studio.md), que es una plataforma basada en web que permite probar varias características de Azure Cognitive Service for Language sin necesidad de escribir código.
* El [artículo de inicio rápido](quickstart.md) para obtener instrucciones sobre cómo realizar solicitudes al servicio mediante la API de REST y el SDK de la biblioteca cliente.  

---
title: ¿Qué es la vinculación de entidad en Azure Cognitive Service for Language?
titleSuffix: Azure Cognitive Services
description: Información general sobre la vinculación de entidad en Azure Cognitive Services, que ayuda a extraer entidades del texto y proporciona vínculos a una knowledge base en línea.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-entity-linking, ignite-fall-2021
ms.openlocfilehash: bbd4483702055a4bebb05251606fb705b1e0deb2
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2021
ms.locfileid: "132487456"
---
# <a name="what-is-entity-linking-in-azure-cognitive-service-for-language"></a>¿Qué es la vinculación de entidad en Azure Cognitive Service for Language?

La vinculación de entidad es una de las características que ofrece [Azure Cognitive Service for Language](../overview.md), una colección de algoritmos de aprendizaje automático e inteligencia artificial en la nube para el desarrollo de aplicaciones inteligentes en las que interviene lenguaje escrito. La vinculación de entidad identifica y elimina la ambigüedad de la identidad de las entidades que se encuentran en el texto. Por ejemplo, en la frase "*Fuimos a Seattle la semana pasada"* , se identificaría la palabra "*Seattle*", con un vínculo a más información en Wikipedia.

Esta documentación contiene los siguientes tipos de artículos:

* Los [**inicios rápidos**](quickstart.md) son instrucciones de inicio que le guiarán a la hora de hacer solicitudes al servicio.
* Las [**guías paso a paso**](how-to/call-api.md) contienen instrucciones para usar el servicio de una manera más específica.

[!INCLUDE [Typical workflow for pre-configured language features](../includes/overview-typical-workflow.md)]

El resultado será una colección de entidades reconocidas en el texto, con direcciones URL a Wikipedia como una knowledge base en línea. 

## <a name="responsible-ai"></a>Inteligencia artificial responsable 

Los sistemas de inteligencia artificial incluyen no solo la tecnología, sino también las personas que la usarán, las personas que se verán afectadas por ella y los entornos en los que se implementan. Lea la [nota de transparencia de vinculación de entidad](/legal/cognitive-services/language-service/transparency-note?context=/azure/cognitive-services/language-service/context/context) para obtener información sobre el uso responsable de la inteligencia artificial y la implementación en los sistemas. Para más información, también puede ver los siguientes artículos:

[!INCLUDE [Responsible AI links](../includes/overview-responsible-ai-links.md)]

## <a name="next-steps"></a>Pasos siguientes

Hay dos maneras de empezar a usar la característica de vinculación de entidades:
* [Language Studio](../language-studio.md), que es una plataforma basada en web que permite probar varias características de Azure Cognitive Service for Language sin necesidad de escribir código.
* El [artículo de inicio rápido](quickstart.md) para obtener instrucciones sobre cómo realizar solicitudes al servicio mediante la API de REST y el SDK de la biblioteca cliente.  

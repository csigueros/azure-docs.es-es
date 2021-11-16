---
title: ¿Qué es Azure Cognitive Service for Language?
titleSuffix: Azure Cognitive Services
description: Aprenda a integrar la inteligencia artificial en las aplicaciones que pueden extraer información y comprender el lenguaje escrito.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 1b4a23b3c59050b99db2938b33091d359f3c34ea
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131502946"
---
# <a name="what-is-azure-cognitive-service-for-language"></a>¿Qué es Azure Cognitive Service for Language?

Azure Cognitive Service for Language es un servicio en la nube que proporciona características de procesamiento del lenguaje natural (NLP) para comprender y analizar texto. Este servicio se usa para ayudar a compilar aplicaciones inteligentes mediante Language Studio basado en web, las API REST y las bibliotecas cliente.  

Este servicio de lenguaje unifica Text Analytics, QnA Maker y LUIS, además de proporcionar varias características nuevas. Estas características pueden ser:
* Preconfiguradas, lo que significa que los modelos de IA que usa la característica no son se pueden personalizar. Solo tiene que enviar los datos y usar la salida de la característica en las aplicaciones.
* Personalizables, lo que significa que entrenará un modelo de IA mediante nuestras herramientas para que se ajuste específicamente a sus datos.

## <a name="migrate-from-text-analytics-qna-maker-or-language-understanding"></a>¿Migración desde Text Analytics, QnA Maker o Language Understanding?

Azure Cognitive Services for Language unifica tres servicios de lenguaje individuales en Cognitive Services: Text Analytics, QnA Maker y Language Understanding (LUIS). Si ha usado estos tres servicios, puede migrar fácilmente a la nueva Azure Cognitive Services para Language. Para obtener las instrucciones necesarias para hacerlo, vea [Migración a Azure Cognitive Services for Language](concepts/migrate.md).  

## <a name="available-features"></a>Características disponibles

Azure Cognitive Service for Language proporciona las siguientes características:

> [!div class="mx-tdCol2BreakAll"]
> |Característica  |Descripción  | Opciones de implementación| 
> |---------|---------|---------|
> | [Reconocimiento de entidades con nombre (NER)](named-entity-recognition/overview.md)     | Esta característica preconfigurado identifica entidades en el texto en varias categorías predefinidas.        | • [Language Studio](language-studio.md) <br> • [API REST y biblioteca cliente](named-entity-recognition/quickstart.md) |
> | [Detección de información de identificación personal](personally-identifiable-information/overview.md)     | Esta característica preconfigurado identifica entidades en texto en varias categorías predefinidas de información confidencial, como la información de la cuenta.        | • [Language Studio](language-studio.md) <br> • [API REST y biblioteca cliente](named-entity-recognition/quickstart.md) |
> | [Extracción de frases clave](key-phrase-extraction/overview.md)     | Esta característica preconfigurada evalúa el texto no estructurado y devuelve una lista de frases clave y puntos principales del texto de cada documento de entrada. | • [Language Studio](language-studio.md) <br> • [API REST y biblioteca cliente](key-phrase-extraction/quickstart.md) <br> • [Contenedor de Docker](key-phrase-extraction/how-to/use-containers.md)  |
> |[Vinculación de entidad](entity-linking/overview.md)    | Esta característica preconfigurada elimina la ambigüedad de la identidad de una entidad que se encuentra en el texto y proporciona vínculos a la entidad en Wikipedia.        | • [Language Studio](language-studio.md) <br> • [API REST y biblioteca cliente](entity-linking/quickstart.md) |
> | [Text Analytics for Health](text-analytics-for-health/overview.md)    | Esta característica preconfigurada extrae información de textos médicos no estructurados, como apuntes clínicos y notas del médico.  | • [Language Studio](language-studio.md) <br> • [API REST y biblioteca cliente](text-analytics-for-health/quickstart.md) <br> • [Contenedor de Docker](text-analytics-for-health/how-to/use-containers.md) |
> | [NER personalizado](custom-named-entity-recognition/overview.md)    | Cree un modelo de inteligencia artificial para extraer categorías de entidades personalizadas mediante el texto no estructurado que proporcione. |  • [Language Studio](custom-named-entity-recognition/quickstart.md?pivots=language-studio) <br> • [API REST](custom-named-entity-recognition/quickstart.md?pivots=rest-api) |
> | [Análisis del sentimiento y las opiniones](sentiment-opinion-mining/overview.md)     | Esta característica preconfigurada proporciona etiquetas de opinión (como "*negative*", "*neutral*" y "*positive*") tanto para oraciones como para documentos. Esta característica puede proporcionar información detallada sobre las opiniones relacionadas con las palabras que aparecen en el texto, como los atributos de productos o servicios. |  • [Language Studio](language-studio.md) <br> • [API REST y biblioteca cliente](sentiment-opinion-mining/quickstart.md) <br> • [Contenedor de Docker](sentiment-opinion-mining/how-to/use-containers.md)
> |[Detección de idioma](language-detection/overview.md)    | Esta característica preconfigurada evalúa el texto y determina el idioma en que se escribió. Devuelve un identificador de idioma y una puntuación que indica la intensidad del análisis.        | • [Language Studio](language-studio.md) <br> • [API REST y biblioteca cliente](language-detection/quickstart.md) <br> • [Contenedor de Docker](language-detection/how-to/use-containers.md) |
> |[Clasificación de texto personalizada (versión preliminar)](custom-classification/overview.md)    | Cree un modelo de IA para clasificar texto no estructurado en las clases personalizadas que defina.         | • [Language Studio](custom-classification/quickstart.md?pivots=language-studio)<br> • [API REST](language-detection/quickstart.md?pivots=rest-api) |
> | [Resumen de texto (versión preliminar)](text-summarization/overview.md)     | Esta característica preconfigurada extrae frases clave que transmiten, en conjunto, la esencia de un documento. | • [Language Studio](language-studio.md) <br> • [API REST y biblioteca cliente](text-summarization/quickstart.md) |
> | [Reconocimiento del lenguaje conversacional (versión preliminar)](conversational-language-understanding/overview.md)   | Cree un modelo de IA para aportar la capacidad de comprender el lenguaje natural en aplicaciones, bots y dispositivos IoT. | • [Language Studio](conversational-language-understanding/quickstart.md)
> | [Respuesta a preguntas](question-answering/overview.md)     | Esta característica preconfigurada proporciona respuestas a las preguntas extraídas de la entrada de texto, para lo que usa contenido semiestructurado, como preguntas más frecuentes, manuales y documentos. | • [Language Studio](language-studio.md) <br> • [API REST y biblioteca cliente](question-answering/quickstart/sdk.md) |

## <a name="tutorials"></a>Tutoriales

Una vez que haya tenido la oportunidad de usar el servicio de lenguaje, pruebe nuestros tutoriales, que le enseñarán a resolver distintos escenarios.

* [Extracción de frases clave del texto almacenado en Power BI](key-phrase-extraction/tutorials/integrate-power-bi.md)
* [Uso de Power Automate para ordenar la información en Microsoft Excel](named-entity-recognition/tutorials/extract-excel-information.md) 
* [Uso de Flask para traducir texto, analizar opiniones y sintetizar voz](/translator/tutorial-build-flask-app-translation-synthesis?context=/azure/cognitive-services/language-service/context/context)
* [Uso de Cognitive Services en aplicaciones de lienzo](/powerapps/maker/canvas-apps/cognitive-services-api?context=/azure/cognitive-services/language-service/context/context)
* [Creación de un bot de preguntas frecuentes](question-answering/tutorials/bot-service.md)

## <a name="additional-code-samples"></a>Ejemplos de código adicionales

En GitHub puede encontrar más ejemplos de código para los siguientes lenguajes:

* [C#](https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/textanalytics/Azure.AI.TextAnalytics/samples)
* [Java](https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/textanalytics/azure-ai-textanalytics/src/samples)
* [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/textanalytics/ai-text-analytics/samples)
* [Python](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/textanalytics/azure-ai-textanalytics/samples)

## <a name="deploy-on-premises-using-docker-containers"></a>Implementación local mediante contenedores de Docker 
Use los contenedores del servicio de lenguaje para implementar características de API de forma local. Estos contenedores de Docker permiten acercar el servicio a los datos para mejorar el cumplimiento, la seguridad o por otras razones operativas. El servicio de lenguaje ofrece los siguientes contenedores:

* [Análisis de opiniones](sentiment-opinion-mining/how-to/use-containers.md)
* [Detección de idioma](language-detection/how-to/use-containers.md)
* [Extracción de frases clave](key-phrase-extraction/how-to/use-containers.md) 
* [Text Analytics for Health](text-analytics-for-health/how-to/use-containers.md)


## <a name="responsible-ai"></a>Inteligencia artificial responsable 

Los sistemas de inteligencia artificial incluyen no solo la tecnología, sino también las personas que la usarán, las personas que se verán afectadas por ella y el entorno en el que se implementa. Lea los siguientes artículos para obtener información sobre el uso responsable de la inteligencia artificial y la implementación en los sistemas:

* [Nota de transparencia para el servicio de lenguaje](/legal/cognitive-services/text-analytics/transparency-note)
* [Integración y uso responsable](/legal/cognitive-services/text-analytics/guidance-integration-responsible-use)
* [Datos, privacidad y seguridad](/legal/cognitive-services/text-analytics/data-privacy)

---
title: Procesamiento integrado de texto e imagen durante la indexación
titleSuffix: Azure Cognitive Search
description: Las aptitudes de extracción de datos, lenguaje natural y procesamiento de imágenes agregan semántica y estructura al contenido sin procesar de una canalización de enriquecimiento de Azure Cognitive Search.
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: 161813b7da42a5697fdb595e6a7050923e28fca2
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123038898"
---
# <a name="built-in-skills-for-text-and-image-processing-during-indexing-azure-cognitive-search"></a>Aptitudes integradas para el procesamiento de texto e imagen durante la indexación (Azure Cognitive Search)

En este artículo se describen las aptitudes proporcionadas con Azure Cognitive Search que puede incluir en un [conjunto de aptitudes](cognitive-search-working-with-skillsets.md) para extraer contenido y estructura de archivos de texto e imágenes sin procesar y sin estructura. Una *aptitud* es una operación atómica que transforma el contenido de alguna manera. A menudo, se trata de una operación que reconoce o extrae texto, pero también puede ser una aptitud de utilidad que cambia la forma de los enriquecimientos que ya se han creado. Normalmente, la salida se basa en texto para que se pueda usar en consultas de texto completo. 

## <a name="built-in-skills"></a>Aptitudes integradas

Las aptitudes integradas se basan en modelos de Microsoft entrenados previamente, lo que significa que no puede entrenar el modelo con sus propios datos de entrenamiento. Las aptitudes que llaman a las API de recursos cognitivos tienen una dependencia de esos servicios y se facturan según el precio de pago por uso de Cognitive Services al [adjuntar un recurso](cognitive-search-attach-cognitive-services.md). Hay otras aptitudes que las mide Azure Cognitive Search, o bien son aptitudes de utilidad que están disponibles sin cargo.

En la tabla siguiente se enumeran y se describen las habilidades integradas.

| Tipo de OData  | Descripción | Medida por |
|-------|-------------|-------------|
|[Microsoft.Skills.Text.CustomEntityLookupSkill](cognitive-search-skill-custom-entity-lookup.md) | Busca texto de una lista de palabras y frases personalizada definida por el usuario.| [Precios](https://azure.microsoft.com/pricing/details/search/) de Azure Cognitive Search |
| [Microsoft.Skills.Text.KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md) | Esta habilidad usa un modelo aprendido previamente para detectar frases importantes en función de la colocación de términos, las reglas lingüísticas, la proximidad a otros términos y cómo de inusual es el término en los datos de origen. | [Precios](https://azure.microsoft.com/pricing/details/cognitive-services/) de Cognitive Services | 
| [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)  | Esta habilidad usa un modelo aprendido previamente para detectar qué idioma se usa (un identificador de idioma por documento). Si se usan varios idiomas en los mismos segmentos de texto, la salida es el LCID del idioma más usado. | [Precios](https://azure.microsoft.com/pricing/details/cognitive-services/) de Cognitive Services | 
| [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md) | Consolida el texto de una colección de campos en un solo campo.  | No aplicable |
| [Microsoft.Skills.Text.V3.EntityLinkingSkill](cognitive-search-skill-entity-linking-v3.md) | Esta aptitud usa un modelo entrenado previamente para generar vínculos para entidades reconocidas a artículos de Wikipedia. | [Precios](https://azure.microsoft.com/pricing/details/cognitive-services/) de Cognitive Services | 
| [Microsoft.Skills.Text.V3.EntityRecognitionSkill](cognitive-search-skill-entity-recognition-v3.md) | Esta aptitud usa un modelo entrenado previamente para establecer las entidades de un conjunto fijo de categorías: los campos `"Person"`, `"Location"`, `"Organization"`, `"Quantity"`, `"DateTime"`, `"URL"`, `"Email"`, `"PersonType"`, `"Event"`, `"Product"`, `"Skill"`, `"Address"`, `"Phone Number"` y `"IP Address"`. | [Precios](https://azure.microsoft.com/pricing/details/cognitive-services/) de Cognitive Services | 
| [Microsoft.Skills.Text.PIIDetectionSkill](cognitive-search-skill-pii-detection.md)  | Esta aptitud usa un modelo previamente entrenado para extraer información personal de un texto dado. También ofrece varias opciones para enmascarar las entidades de información personal que se detecten en el texto.  | [Precios](https://azure.microsoft.com/pricing/details/cognitive-services/) de Cognitive Services | 
| [Microsoft.Skills.Text.V3.SentimentSkill](cognitive-search-skill-sentiment-v3.md)  | Esta aptitud usa un modelo entrenado previamente para asignar etiquetas de opinión (como "negative", "neutral" y "positive") basadas en la mayor puntuación de confianza que haya encontrado el servicio, tanto en el nivel de oración como en el de documento, registro a registro. | [Precios](https://azure.microsoft.com/pricing/details/cognitive-services/) de Cognitive Services | 
| [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md) | Divide el texto en páginas para que se pueda enriquecer o aumentar el contenido de forma incremental. | No aplicable |
| [Microsoft.Skills.Text.TranslationSkill](cognitive-search-skill-text-translation.md) | Esta aptitud usa un modelo entrenado previamente para traducir el texto de entrada a una variedad de idiomas para los casos de uso de normalización o localización. | [Precios](https://azure.microsoft.com/pricing/details/cognitive-services/) de Cognitive Services | 
| [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md) | Esta habilidad usa un algoritmo de detección de imágenes para identificar el contenido de una imagen y generar una descripción de texto. | [Precios](https://azure.microsoft.com/pricing/details/cognitive-services/) de Cognitive Services | 
| [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md) | Reconocimiento óptico de caracteres. | [Precios](https://azure.microsoft.com/pricing/details/cognitive-services/) de Cognitive Services |
| [Microsoft.Skills.Util.ConditionalSkill](cognitive-search-skill-conditional.md) | Permite el filtrado, la asignación de un valor predeterminado y la combinación de datos según una condición. | No aplicable |
| [Microsoft.Skills.Util.DocumentExtractionSkill](cognitive-search-skill-document-extraction.md) | Extrae contenido de un archivo dentro de la canalización de enriquecimiento. | [Precios](https://azure.microsoft.com/pricing/details/search/) de Azure Cognitive Search
| [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md) | Asigna la salida a un tipo complejo (un tipo de datos de varias partes que se podría usar para un nombre completo, una dirección de varias líneas o una combinación de apellido e identificador personal). | No aplicable |

## <a name="custom-skills"></a>Aptitudes personalizadas

[Las aptitudes personalizadas](cognitive-search-custom-skill-web-api.md) son módulos que diseña, desarrolla e implementa en la web. A continuación, puede llamar al módulo desde un conjunto de aptitudes como una aptitud personalizada.

| Tipo  | Descripción | Medida por |
|-------|-------------|-------------|
| [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md) | Permite la extensibilidad de la canalización de enriquecimiento con inteligencia artificial mediante la realización de una llamada HTTP a una API web personalizada | Ninguno a menos que la solución use un servicio de Azure medido |
| [Microsoft.Skills.Custom.AmlSkill](cognitive-search-aml-skill.md) | Permite la extensibilidad de una canalización de enriquecimiento con IA con un modelo de Azure Machine Learning | Ninguno a menos que la solución use un servicio de Azure medido |

Para obtener las instrucciones necesarias para crear una aptitud personalizada, vea [Cómo definir una interfaz personalizada](cognitive-search-custom-skill-interface.md) y [Ejemplo: crear una aptitud personalizada para enriquecimiento con IA](cognitive-search-create-custom-skill-example.md).

## <a name="see-also"></a>Consulte también

+ [Definición de un conjunto de aptitudes](cognitive-search-defining-skillset.md)
+ [Custom Skills interface definition](cognitive-search-custom-skill-interface.md) (Definición de interfaz de habilidades personalizadas)
+ [Tutorial: Enriched indexing with AI](cognitive-search-tutorial-blob.md) (Tutorial: Indexación enriquecida con inteligencia artificial)

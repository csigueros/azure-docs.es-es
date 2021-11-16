---
title: Cognitive Services en Azure Synapse Analytics
description: Enriquezca los datos con inteligencia artificial en Azure Synapse Analytics mediante modelos previamente entrenados de Azure Cognitive Services.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: overview
ms.reviewer: jrasnick, garye, negust, ruxu
ms.date: 06/30/2021
author: garyericson
ms.author: garye
ms.custom: ignite-fall-2021
ms.openlocfilehash: e69730f486303fa508fed4cd53f8df3ee76433ba
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132318472"
---
# <a name="cognitive-services-in-azure-synapse-analytics"></a>Cognitive Services en Azure Synapse Analytics

Con los modelos previamente entrenados de Azure Cognitive Services, puede enriquecer los datos con inteligencia artificial en Azure Synapse Analytics.

[Azure Cognitive Services](../../cognitive-services/what-are-cognitive-services.md) son servicios basados en la nube que agregan inteligencia cognitiva a los datos, incluso si no tiene aptitudes para la inteligencia artificial o ciencia de datos. Existen varias maneras de utilizar estos servicios con los datos en Synapse Analytics:

- El asistente para Cognitive Services de Synapse Analytics genera código PySpark en un cuaderno de Synapse que se conecta a un servicio cognitivo mediante los datos de una tabla de Spark. A continuación, mediante modelos de aprendizaje automático previamente entrenados, el servicio realiza el trabajo para agregar inteligencia artificial a los datos. Consulte el [Asistente para análisis de sentimiento](tutorial-cognitive-services-sentiment.md) y el [Asistente para detección de anomalías](tutorial-cognitive-services-anomaly.md) para más información.

- Synapse Machine Learning ([SynapseML](https://github.com/microsoft/SynapseML)) le permite crear modelos predictivos y analíticos eficaces y altamente escalables a partir de varios orígenes de datos de Spark. Synapse Spark proporciona bibliotecas de SynapseML integradas, entre las que se incluye [Cognitive Services en Spark](https://github.com/microsoft/SynapseML/blob/master/docs/cogsvc.md). Consulte [Tutoriales](#tutorials) para más información.

- A partir del código de PySpark generado por el asistente o el código SynapseML de ejemplo proporcionado en el tutorial, puede escribir su propio código para utilizar otros servicios cognitivos con sus datos. Consulte [¿Qué es Azure Cognitive Services?](../../cognitive-services/what-are-cognitive-services.md) para más información sobre los servicios disponibles.




## <a name="get-started"></a>Introducción

[Tutorial: Requisitos previos para el uso de Cognitive Services en Azure Synapse](tutorial-configure-cognitive-services-synapse.md) le puede servir de guía siguiendo un par de pasos que debe realizar antes de utilizar Cognitive Services en Synapse Analytics.

## <a name="tutorials"></a>Tutoriales

En los tutoriales siguientes se proporcionan ejemplos completos del uso de Cognitive Services en Synapse Analytics.

- [Tutorial: Análisis de sentimiento con Cognitive Services (versión preliminar)](tutorial-cognitive-services-sentiment.md): con un conjunto de datos de ejemplo de comentarios de clientes, cree una tabla de Spark con una columna que indique el sentimiento de los comentarios de cada fila.

- [Tutorial: Detección de anomalías con Cognitive Services (versión preliminar)](tutorial-cognitive-services-anomaly.md): con un conjunto de datos de ejemplo de datos de serie temporal, cree una tabla de Spark con una columna que indique si los datos de cada fila presentan una anomalía.

- [Tutorial: Creación de aplicaciones de aprendizaje automático con Microsoft Machine Learning para Apache Spark](tutorial-build-applications-use-mmlspark.md): en este tutorial se muestra cómo se utiliza SynapseML para acceder a varios modelos desde Cognitive Services.

- En el tutorial [Form Recognizer con Applied AI Services](tutorial-form-recognizer-use-mmlspark.md) se indica cómo usar [Form Recognizer](../../applied-ai-services/form-recognizer/index.yml) para analizar los formularios y documentos, extraer texto y datos en Azure Synapse Analytics. 

- En el tutorial, [Text Analytics con Cognitive Services](tutorial-text-analytics-use-mmlspark.md) se muestra el uso de [Text Analytics](../../cognitive-services/text-analytics/index.yml) para analizar texto no estructurado en Azure Synapse Analytics.

- En el tutorial [Translator con Cognitive Services](tutorial-translator-use-mmlspark.md) se indica cómo usar [Translator](../../cognitive-services/Translator/index.yml) para crear soluciones inteligentes en varios idiomas en Azure Synapse Analytics.

- En [Computer Vision con Cognitive Services](tutorial-computer-vision-use-mmlspark.md) se muestra cómo usar [Computer Vision](../../cognitive-services/computer-vision/index.yml) para analizar imágenes en Azure Synapse Analytics.

## <a name="available-cognitive-services-apis"></a>Cognitive Services APIs disponibles
### <a name="bing-image-search"></a>Bing Image Search

| Tipo de API                                   | API de SynapseML                  | Cognitive Services APIs (versiones)                                                                                               | DEP VNet Support |
| ------------------------------------------ | ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------- | ---------------- |
|Bing Image Search|BingImageSearch|Images - Visual Search V7.0| No compatible|

### <a name="anomaly-detector"></a>Anomaly Detector

| Tipo de API                                   | API de SynapseML                  | Cognitive Services APIs (versiones)                                                                                               | DEP VNet Support |
| ------------------------------------------ | ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------- | ---------------- |
| Detección de la anomalía más reciente                        | DetectLastAnomaly              | Detect Last Point V1.0                                                                                                          | Compatible        |
| Detección de anomalías                           | DetectAnomalies                | Detect Entire Series V1.0                                                                                                       | Compatible        |
| Simple DetectAnomalies                     | SimpleDetectAnomalies          | Detect Entire Series V1.0                                                                                                       | Compatible        |

### <a name="computer-vision"></a>Visión del equipo

| Tipo de API                                   | API de SynapseML                  | Cognitive Services APIs (versiones)                                                                                               | DEP VNet Support |
| ------------------------------------------ | ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------- | ---------------- |
| OCR                                        | OCR                            | Recognize Printed Text V2.0                                                                                                     | Compatible        |
| Reconocer texto                             | RecognizeText                  | Recognize Text V2.0                                                                                                             | Compatible        |
| Lectura de imágenes                                 | ReadImage                      | Read V3.1                                                                                                                       | Compatible        |
| Generación de miniaturas                        | GenerateThumbnails             | Generate Thumbnail V2.0                                                                                                         | Compatible        |
| Analyze Image                              | AnalyzeImage                   | Analyze Image V2.0                                                                                                              | Compatible        |
| Reconocimiento del contenido específico del dominio          | RecognizeDomainSpecificContent | Analyze Image By Domain V2.0                                                                                                    | Compatible        |
| Etiquetado de imágenes                                  | TagImage                       | Tag Image V2.0                                                                                                                  | Compatible        |
| Describe Image                             | DescribeImage                  | Describe Image V2.0                                                                                                             | Compatible        |


### <a name="translator"></a>Traductor

| Tipo de API                                   | API de SynapseML                  | Cognitive Services APIs (versiones)                                                                                               | DEP VNet Support |
| ------------------------------------------ | ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------- | ---------------- |
| Traducción de texto                             | Translate                      | Translate V3.0                                                                                                                  | No compatible    |
| Transliteración de texto                         | Transliterar                  | Transliterate V3.0                                                                                                              | No compatible    |
| Detección de idioma                            | Detect                         | Detect V3.0                                                                                                                     | No compatible    |
| División de frases                             | BreakSentence                  | Break Sentence V3.0                                                                                                             | No compatible    |
| Búsqueda en el diccionario (traducciones alternativas) | DictionaryLookup               | Dictionary Lookup V3.0                                                                                                          | No compatible    |
| Traducción de documentos                       | DocumentTranslator             | Document Translation V1.0                                                                                                       | No compatible    |


### <a name="face"></a>Caras

| Tipo de API                                   | API de SynapseML                  | Cognitive Services APIs (versiones)                                                                                               | DEP VNet Support |
| ------------------------------------------ | ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------- | ---------------- |
| Detección de caras                                | DetectFace                     | Detect With Url V1.0                                                                                                            | Compatible        |
| Búsqueda de caras similares                          | FindSimilarFace                | Find Similar V1.0                                                                                                               | Compatible        |
| Agrupación de caras                                | GroupFaces                     | Group V1.0                                                                                                                      | Compatible        |
| Identificación de caras                             | IdentifyFaces                  | Identify V1.0                                                                                                                   | Compatible        |
| Comprobación de caras                               | VerifyFaces                    | Verify Face To Face V1.0                                                                                                        | Compatible        |

### <a name="form-recognizer"></a>Form Recognizer
| Tipo de API                                   | API de SynapseML                  | Cognitive Services APIs (versiones)                                                                                               | DEP VNet Support |
| ------------------------------------------ | ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------- | ---------------- |
| Análisis de diseño                             | AnalyzeLayout                  | Analyze Layout Async V2.1                                                                                                       | Compatible        |
| Análisis de recibos                           | AnalyzeReceipts                | Analyze Receipt Async V2.1                                                                                                      | Compatible        |
| Análisis de tarjetas de presentación                     | AnalyzeBusinessCards           | Analyze Business Card Async V2.1                                                                                                | Compatible        |
| Análisis de facturas                           | AnalyzeInvoices                | Analyze Invoice Async V2.1                                                                                                      | Compatible        |
| Análisis de documentos de identificación                       | AnalyzeIDDocuments             | identification (ID) document model V2.1                                                                                         | Compatible        |
| Lista de modelos personalizados                         | ListCustomModels               | List Custom Models V2.1                                                                                                         | Compatible        |
| Obtención de modelos personalizados                           | GetCustomModel                 | Get Custom Models V2.1                                                                                                          | Compatible        |
| Análisis de modelos personalizados                       | AnalyzeCustomModel             | Analyze With Custom Model V2.1                                                                                                  | Compatible        |

### <a name="speech-to-text"></a>Voz a texto
| Tipo de API                                   | API de SynapseML                  | Cognitive Services APIs (versiones)                                                                                               | DEP VNet Support |
| ------------------------------------------ | ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------- | ---------------- |
| Speech To Text                             | SpeechToText                   | SpeechToText V1.0 |  No compatible    |
| Voz a texto (SDK)                         | SpeechToTextSDK                | Uso de Speech SDK Version 1.14.0                                                                                                 | No compatible    |


### <a name="text-analytics"></a>Text Analytics

| Tipo de API                                   | API de SynapseML                  | Cognitive Services APIs (versiones)                                                                                               | DEP VNet Support |
| ------------------------------------------ | ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------- | ---------------- |
| Text Sentiment V2                          | TextSentimentV2                | Sentiment V2.0                                                                                                                  | Compatible        |
| Language Detector V2                       | LanguageDetectorV2             | Languages V2.0                                                                                                                  | Compatible        |
| Entity Detector V2                         | EntityDetectorV2               | Entities Linking V2.0                                                                                                           | Compatible        |
| NER V2                                     | NERV2                          | Entities Recognition General V2.0                                                                                               | Compatible        |
| Key Phrase Extractor V2                    | KeyPhraseExtractorV2           | Key Phrases V2.0                                                                                                                | Compatible        |
| Text Sentiment                             | TextSentiment                  | Sentiment V3.1                                                                                                                  | Compatible        |
| Key Phrase Extractor                       | KeyPhraseExtractor             | Key Phrases V3.1                                                                                                                | Compatible        |
| PII                                        | PII                            | Entities Recognition Pii V3.1                                                                                                   | Compatible        |
| NER                                        | NER                            | Entities Recognition General V3.1                                                                                               | Compatible        |
| Detector de idioma                          | LanguageDetector               | Languages V3.1                                                                                                                  | Compatible        |
| Detector de entidad                            | EntityDetector                 | Entities Linking V3.1                                                                                                           | Compatible        |


## <a name="next-steps"></a>Pasos siguientes

- [Funcionalidades de aprendizaje automático en Azure Synapse Analytics](what-is-machine-learning.md)
- [¿Qué es Cognitive Services?](../../cognitive-services/what-are-cognitive-services.md)
- [Uso de un cuaderno de ejemplo de la galería de Synapse Analytics](quickstart-gallery-sample-notebook.md)

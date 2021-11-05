---
title: Adjuntar Cognitive Services a un conjunto de aptitudes
titleSuffix: Azure Cognitive Search
description: Aprenda a asociar un recurso multiservicio de Cognitive Services a una canalización de enriquecimiento de inteligencia artificial en Azure Cognitive Search.
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/22/2021
ms.openlocfilehash: ce369b60fa1b12823acdb1f5045e403bcaa3f5dd
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131037670"
---
# <a name="attach-a-cognitive-services-resource-to-a-skillset-in-azure-cognitive-search"></a>Asociación de un recurso de Cognitive Services con un conjunto de aptitudes en Azure Cognitive Search

Al configurar una [canalización de enriquecimiento con IA](cognitive-search-concept-intro.md) opcional en Azure Cognitive Search, se puede enriquecer un número limitado de documentos de forma gratuita. En el caso de cargas de trabajo mayores y más frecuentes, hay que asociar un [**recurso multiservicio facturable de Cognitive Services**](../cognitive-services/cognitive-services-apis-create-account.md). Un recurso multservicio hace referencia a "Cognitive Services" como oferta, en lugar de a servicios individuales, y el acceso se concede a través de una clave de API única.

Una clave de recurso multiservicio se especifica en un conjunto de aptitudes y permite a Microsoft cobrarle por el uso de estas API:

+ [Computer Vision](https://azure.microsoft.com/services/cognitive-services/computer-vision/) para el análisis de imágenes y el reconocimiento óptico de caracteres (OCR)
+ [Text Analytics](https://azure.microsoft.com/services/cognitive-services/text-analytics/) para la detección de idioma, el reconocimiento de entidades, el análisis de sentimiento y la extracción de frases clave
+ [Traducción de texto](https://azure.microsoft.com/services/cognitive-services/translator-text-api/)

> [!NOTE]
> El enriquecimiento con IA ofrece una pequeña cantidad de procesamiento gratuito para que pueda completar ejercicios breves sin tener que asociar un recurso de Cognitive Services. Los enriquecimientos gratuitos son 20 documentos al día por cada indizador. Puede [restablecer el indizador](search-howto-run-reset-indexers.md) para restablecer el contador si desea repetir un ejercicio.

## <a name="azure-portal"></a>[**Azure Portal**](#tab/cogkey-portal)

1. Cree un [recurso multiservicio de Cognitive Services](../cognitive-services/cognitive-services-apis-create-account.md) en la [misma región](#same-region-requirement) que el servicio de búsqueda.

1. Agregue la clave a la definición de un conjunto de aptitudes:

   + Si utiliza el [Asistente para la importación de datos](search-import-data-portal.md), escriba la clave en el segundo paso, "Agregar enriquecimientos con IA".

   + Si agrega la clave a un conjunto de aptitudes nuevo o existente, proporciónela en la pestaña **Cognitive Services**.

   :::image type="content" source="media/cognitive-search-attach-cognitive-services/attach-existing2.png" alt-text="Captura de pantalla de la página de claves" border="true":::

## <a name="rest"></a>[**REST**](#tab/cogkey-rest)

1. Cree un [recurso multiservicio de Cognitive Services](../cognitive-services/cognitive-services-apis-create-account.md) en la [misma región](#same-region-requirement) que el servicio de búsqueda.

1. Cree o actualice un conjunto de aptitudes. Para ello, especifique la sección `cognitiveServices` en el cuerpo de la [solicitud del conjunto de aptitudes](/rest/api/searchservice/create-skillset):

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2020-06-30
api-key: [admin key]
Content-Type: application/json
{
    "name": "skillset name",
    "skills": 
    [
      {
        "@odata.type": "#Microsoft.Skills.Text.V3.EntityRecognitionSkill",
        "categories": [ "Organization" ],
        "defaultLanguageCode": "en",
        "inputs": [
          {
            "name": "text", "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "organizations", "targetName": "organizations"
          }
        ]
      }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "mycogsvcs",
        "key": "<your key goes here>"
    }
}
```

## <a name="net-sdk"></a>[**SDK de .NET**](#tab/cogkey-dotnet)

El fragmento de código siguiente proviene de [azure-search-dotnet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/tutorial-ai-enrichment/v11/Program.cs) y se recortó para mayor brevedad.

```csharp
IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
IConfigurationRoot configuration = builder.Build();

string searchServiceUri = configuration["SearchServiceUri"];
string adminApiKey = configuration["SearchServiceAdminApiKey"];
string cognitiveServicesKey = configuration["CognitiveServicesKey"];

SearchIndexerClient indexerClient = new SearchIndexerClient(new Uri(searchServiceUri), new AzureKeyCredential(adminApiKey));

// Create the skills
Console.WriteLine("Creating the skills...");
OcrSkill ocrSkill = CreateOcrSkill();
MergeSkill mergeSkill = CreateMergeSkill();

// Create the skillset
Console.WriteLine("Creating or updating the skillset...");
List<SearchIndexerSkill> skills = new List<SearchIndexerSkill>();
skills.Add(ocrSkill);
skills.Add(mergeSkill);

SearchIndexerSkillset skillset = CreateOrUpdateDemoSkillSet(indexerClient, skills, cognitiveServicesKey);
```

---

## <a name="how-the-key-is-used"></a>Uso de la clave

La clave se usa para la facturación, pero no para las conexiones. En el caso de las conexiones, un servicio de búsqueda utiliza la red interna para conectarse a un recurso de Cognitive Services que se encuentra en la [misma región física](https://azure.microsoft.com/global-infrastructure/services/?products=search). 

Se aplica la facturación basada cuando las llamadas API a los recursos de Cognitive Services superan las 20 llamadas API al dia por cada indizador. Puede restablecer el indexador después de cada invocación del indexador para restablecer el contador de API, pero el número máximo de llamadas que se pueden realizar libremente se limita a 20.

 Durante el enriquecimiento con IA, Cognitive Search llama a las Cognitive Services APIs para las [aptitudes integradas](cognitive-search-predefined-skills.md) que se basan en Computer Vision, Text Analytics y Text Analytics. Las aptitudes que hacen llamadas de back-end a Cognitive Services incluyen [Vinculación de entidad](cognitive-search-skill-entity-linking-v3.md), [Reconocimiento de entidades](cognitive-search-skill-entity-recognition-v3.md), [Análisis de imágenes](cognitive-search-skill-image-analysis.md), [Extracción de frases clave](cognitive-search-skill-keyphrases.md), [Detección de idioma](cognitive-search-skill-language-detection.md), [OCR](cognitive-search-skill-ocr.md), [Detección de DCP](cognitive-search-skill-pii-detection.md), [Opinión](cognitive-search-skill-sentiment-v3.md) y [Traducción de texto](cognitive-search-skill-text-translation.md).

Puede omitir la clave y la sección de Cognitive Services para conjuntos de aptitudes que constan únicamente de aptitudes personalizadas o aptitudes de utilidad. También puede dejar la propiedad sin especificar si el uso de aptitudes facturables es inferior a 20 transacciones al día por indizador.

### <a name="exceptions-and-special-cases"></a>Excepciones y casos especiales

+ Las aptitudes de utilidad que no llaman a Cognitive Services (concretamente, las aptitudes [Conditional](cognitive-search-skill-conditional.md), [Extracción de documentos](cognitive-search-skill-document-extraction.md), [Conformador](cognitive-search-skill-shaper.md), [Combinación de texto](cognitive-search-skill-textmerger.md) y [División de texto](cognitive-search-skill-textsplit.md)) no son facturables. 

+ La [búsqueda de entidades personalizada](cognitive-search-skill-custom-entity-lookup.md) se mide en Azure Cognitive Search, no en Cognitive Services, pero requiere una clave de recurso de Cognitive Services para desbloquear transacciones más allá de 20 por indizador al día. Solo para esta aptitud, la clave de recurso desbloquea el número de transacciones, pero no está relacionada con la facturación.

### <a name="other-costs-of-ai-enrichment"></a>Otros costos del enriquecimiento con IA

La extracción de imágenes es una operación de Azure Cognitive Search que se produce al descifrar documentos antes del enriquecimiento. La extracción de imágenes se factura en todos los niveles, a excepción de 20 extracciones diarias gratuitas en el nivel gratuito. Los costes de extracción de imágenes se aplican a los archivos de imagen dentro de blobs, a las imágenes insertadas en otros archivos (archivos PDF y otros archivos de aplicación) y a las imágenes extraídas mediante [Extracción de documentos](cognitive-search-skill-document-extraction.md). Para ver los precios de la extracción de imágenes, consulte la [página de precios de Azure Cognitive Search](https://azure.microsoft.com/pricing/details/search/).

La extracción de texto también se produce durante la fase de [descifrado de documentos](search-indexer-overview.md#document-cracking). No es facturable.

> [!TIP]
> Para reducir el coste del procesamiento del conjunto de habilidades, habilite [el enriquecimiento incremental (versión preliminar)](cognitive-search-incremental-indexing-conceptual.md) para almacenar en caché y reutilizar cualquier enriquecimiento que no se vea afectado por los cambios realizados en un conjunto de habilidades. El almacenamiento en caché requiere Azure Storage (consulte los [precios](https://azure.microsoft.com/pricing/details/storage/blobs/)), pero el costo acumulado de la ejecución del conjunto de aptitudes es menor si se pueden reutilizar los enriquecimientos existentes, especialmente en los que usan extracción de imágenes y análisis.

## <a name="same-region-requirement"></a>Requisito de la misma región

Tanto Cognitive Search como Cognitive Services deben existir en la misma región física, como se indica en la página de [disponibilidad del producto](https://azure.microsoft.com/global-infrastructure/services/?products=search). La mayoría de las regiones que ofrecen Cognitive Search también ofrecen Cognitive Services.

Si intenta usar el enriquecimiento de inteligencia artificial en una región que no tenga ambos servicios, verá este mensaje: "La clave proporcionada no es una clave de tipo CognitiveServices válida para la región del servicio de búsqueda".

> [!NOTE]
> Algunas habilidades integradas se basan en una instancia de Cognitive Services no regional (por ejemplo, la [habilidad de traducción de texto](cognitive-search-skill-text-translation.md)). El uso de una habilidad no regional implica que la solicitud podría ser atendida en una región distinta de la región de Azure Cognitive Search. Para obtener más información sobre los servicios no regionales, consulte la página de [productos de Cognitive Services por región](https://aka.ms/allinoneregioninfo).

## <a name="example-estimate-costs"></a>Ejemplo: estimación de costos

Para calcular los costos asociados con la indexación de Cognitive Search, empiece con una idea de cómo debe verse un documento promedio para que pueda ejecutar algunos números. Un cálculo aproximado, por ejemplo, sería:

+ 1000 archivos PDF.
+ Seis páginas cada uno.
+ Una imagen por página (6000 imágenes).
+ 3000 caracteres por página.

Supongamos que hay una canalización que consta del descifrado de cada documento PDF con extracción de imágenes y texto, reconocimiento óptico de caracteres (OCR) de las imágenes y reconocimiento de entidad de las organizaciones.

Los precios mostrados en este artículo son hipotéticos. Se usan para ilustrar el proceso de estimación. Sus costos podrían ser más bajos. Para ver los precios reales de las transacciones, consulte [Precios de Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services).

1. Para el descifrado de documentos con contenido de texto e imagen, la extracción de texto actualmente es gratis. Para 6000 imágenes, suponga 1 USD por cada 1000 imágenes extraídas. Eso es un costo de 6 USD para este paso.

2. Para realizar el reconocimiento óptico de caracteres (OCR) de 6000 imágenes en inglés, la aptitud de reconocimiento de OCR usa el mejor algoritmo (DescribeText). Suponiendo un precio de 2,50 USD por cada 1000 imágenes que se analizan, tendríamos que pagar 15,00 USD en este paso.

3. Para la extracción de entidades, tendríamos un total de tres registros de texto por página. Cada registro tiene 1000 caracteres. Tres registros de texto por página multiplicados por 6000 páginas equivale a 18 000 registros de texto. Suponiendo un costo de 2,00 USD por cada 1,000 registros de texto, en este paso tendríamos un costo de 36,00 USD.

En resumen, pagaría unos 57 USD por la ingesta de 1000 documentos PDF de este tipo con el conjunto de aptitudes descrito.

## <a name="next-steps"></a>Pasos siguientes

+ [Página de precios de Azure Cognitive Search](https://azure.microsoft.com/pricing/details/search/)
+ [Definición de un conjunto de aptitudes](cognitive-search-defining-skillset.md)
+ [Crear un conjunto de aptitudes (REST)](/rest/api/searchservice/create-skillset)
+ [Cómo asignar campos enriquecidos](cognitive-search-output-field-mapping.md)

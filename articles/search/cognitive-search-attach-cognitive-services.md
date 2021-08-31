---
title: Adjuntar Cognitive Services a un conjunto de aptitudes
titleSuffix: Azure Cognitive Search
description: Aprenda a asociar un recurso multiservicio de Cognitive Services a una canalización de enriquecimiento de inteligencia artificial en Azure Cognitive Search.
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: 0fe9a87e82ab391fc0e1ccfca95ad48a0ef5dc61
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2021
ms.locfileid: "122772470"
---
# <a name="attach-a-cognitive-services-resource-to-a-skillset-in-azure-cognitive-search"></a>Asociación de un recurso de Cognitive Services con un conjunto de aptitudes en Azure Cognitive Search

Al configurar una [canalización de enriquecimiento de inteligencia artificial](cognitive-search-concept-intro.md) en Azure Cognitive Search, se puede enriquecer un número limitado de documentos de forma gratuita. En el caso de cargas de trabajo mayores y más frecuentes, hay que asociar un [recurso multiservicio facturable de Cognitive Services](../cognitive-services/cognitive-services-apis-create-account.md). Un recurso multservicio hace referencia a "Cognitive Services" como oferta, en lugar de a servicios individuales, y el acceso se concede a través de una clave de API única.

Una clave de recurso se especifica en un conjunto de aptitudes y permite a Microsoft cobrarle por el uso de estas API:

+ [Computer Vision](https://azure.microsoft.com/services/cognitive-services/computer-vision/) para el análisis de imágenes y el reconocimiento óptico de caracteres (OCR)
+ [Text Analytics](https://azure.microsoft.com/services/cognitive-services/text-analytics/) para la detección de idioma, el reconocimiento de entidades, el análisis de sentimiento y la extracción de frases clave
+ [Traducción de texto](https://azure.microsoft.com/services/cognitive-services/translator-text-api/)

La clave se usa para la facturación, pero no para las conexiones. Internamente, un servicio de búsqueda se conecta a Cognitive Services recurso que se encuentra en la [misma región física](https://azure.microsoft.com/global-infrastructure/services/?products=search).

## <a name="key-requirements"></a>Requisitos principales

Se requiere una clave de recurso para las habilidades facturables [incorporadas](cognitive-search-predefined-skills.md) que se utilizan más de 20 veces al día en el mismo indizador. Las aptitudes que hacen llamadas de back-end a Cognitive Services incluyen Vinculación de entidad, Reconocimiento de entidad, Análisis de imagen, Extracción de frases clave, Detección de idioma, OCR, Detección PII, Opinión o Traducción de texto.

La [búsqueda de entidades personalizada](cognitive-search-skill-custom-entity-lookup.md) se mide en Azure Cognitive Search, no en Cognitive Services, pero requiere una clave de recurso de Cognitive Services para desbloquear transacciones más allá de 20 por indizador al día. Solo para esta aptitud, la clave de recurso desbloquea el número de transacciones, pero no está relacionada con la facturación.

Puede omitir la clave y la sección Cognitive Services para conjuntos de aptitudes que consten únicamente de aptitudes personalizadas o aptitudes de utilidad (Condicional, Extracción de documentos, Conformador, Combinación de texto, División de texto). También puede omitir la sección si el uso de aptitudes facturables es inferior a 20 transacciones por indizador al día.

## <a name="how-billing-works"></a>Cómo funciona la facturación

+ Azure Cognitive Search usa la clave de recurso de Cognitive Services proporcionada por el usuario en un conjunto de aptitudes con objeto de facturar el enriquecimiento de imágenes y texto. La ejecución de aptitudes facturables tiene lugar al [precio de pago por uso de Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/).

+ La extracción de imágenes es una operación de Azure Cognitive Search que se produce al descifrar documentos antes del enriquecimiento. La extracción de imágenes se factura en todos los niveles, a excepción de 20 extracciones diarias gratuitas en el nivel gratuito. Los costes de extracción de imágenes se aplican a los archivos de imagen dentro de blobs, a las imágenes insertadas en otros archivos (archivos PDF y otros archivos de aplicación) y a las imágenes extraídas mediante [Extracción de documentos](cognitive-search-skill-document-extraction.md). Para ver los precios de la extracción de imágenes, consulte la [página de precios de Azure Cognitive Search](https://azure.microsoft.com/pricing/details/search/).

+ La extracción de texto también se produce durante la fase de [descifrado de documentos](search-indexer-overview.md#document-cracking). No es facturable.

+ Las aptitudes que no llaman a Cognitive Services, como las aptitudes Condicional, Conformador, Combinación de texto y División de texto, no son facturables. 

  Como se ha señalado, [la búsqueda de entidades personalizadas](cognitive-search-skill-custom-entity-lookup.md) es un caso especial, ya que requiere una clave, pero es [medida por la Cognitive Search](https://azure.microsoft.com/pricing/details/search/#pricing).

> [!TIP]
> Para reducir el coste del procesamiento del conjunto de habilidades, habilite [el enriquecimiento incremental (versión preliminar)](cognitive-search-incremental-indexing-conceptual.md) para almacenar en caché y reutilizar cualquier enriquecimiento que no se vea afectado por los cambios realizados en un conjunto de habilidades. El almacenamiento en caché requiere Azure Storage (consulte los [precios](https://azure.microsoft.com/pricing/details/storage/blobs/)), pero el costo acumulado de la ejecución del conjunto de aptitudes es menor si se pueden reutilizar los enriquecimientos existentes, especialmente en los que usan extracción de imágenes y análisis.

## <a name="same-region-requirement"></a>Requisito de la misma región

Tanto Cognitive Search como Cognitive Services deben existir en la misma región física, como se indica en la página de [disponibilidad del producto](https://azure.microsoft.com/global-infrastructure/services/?products=search). La mayoría de las regiones que ofrecen Cognitive Search también ofrecen Cognitive Services.

Si intenta usar el enriquecimiento de inteligencia artificial en una región que no tenga ambos servicios, verá este mensaje: "La clave proporcionada no es una clave de tipo CognitiveServices válida para la región del servicio de búsqueda".

> [!NOTE]
> Algunas habilidades integradas se basan en una instancia de Cognitive Services no regional (por ejemplo, la [habilidad de traducción de texto](cognitive-search-skill-text-translation.md)). El uso de una habilidad no regional implica que la solicitud podría ser atendida en una región distinta de la región de Azure Cognitive Search. Para obtener más información sobre los servicios no regionales, consulte la página de [productos de Cognitive Services por región](https://aka.ms/allinoneregioninfo).

## <a name="use-free-resources"></a>Uso de recursos gratis

Puede usar una opción de procesamiento limitada y gratuita para completar los ejercicios del tutorial y la guía de inicio rápido de enriquecimiento con inteligencia artificial.

Los recursos gratuitos (enriquecimientos limitados) se restringen a 20 documentos al día por indexador. Puede [restablecer el indizador](search-howto-run-reset-indexers.md) para restablecer el contador.

Si usa el Asistente para la **importación de datos** para el enriquecimiento de la inteligencia artificial, encontrará las opciones de "Adjuntar Cognitive Services" en la página **Agregar enriquecimiento de AI (opcional)** .

![Sección Adjuntar Cognitive Services expandida](./media/cognitive-search-attach-cognitive-services/attach1.png "Sección Adjuntar Cognitive Services expandida")

## <a name="use-billable-resources"></a>Uso de recursos facturables

Para cargas de trabajo que creen más de 20 enriquecimientos facturables al día, asegúrese de asociar un recurso de Cognitive Services. 

Si utiliza el Asistente para la **importación de datos**, puede configurar un recurso facturable en la página **Agregar enriquecimiento de AI (opcional)** .

1. Expanda **Adjuntar Cognitive Services** y, a continuación, seleccione **Crear nuevo recurso de Cognitive Services**. Se abre una nueva pestaña para poder crear el recurso:

   ![Cree un recurso de Cognitive Services](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "Creación de un recurso de Cognitive Services")

1. En la lista **Ubicación**, seleccione la misma región que tiene el servicio de búsqueda.

1. En la lista **Plan de tarifa**, seleccione **S0** para obtener la colección todo en uno de características de Cognitive Services, incluidas las características Visión e Idioma que respaldan las aptitudes integradas que proporciona Azure Cognitive Search.

   Para el nivel S0, encontrará tarifas para cargas de trabajo específicas en la [página de precios de Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/).
  
   + En la lista **Select Offer** (Seleccionar oferta), asegúrese de que **Cognitive Services** está seleccionado.
   + En las características **Idioma**, las tarifas para **Text Analytics Standard** se aplican a la indexación de inteligencia artificial.
   + En las características **Visión**, se aplican las tarifas para **Computer Vision S1**.

1. Seleccione **Crear** para aprovisionar el nuevo recurso de Cognitive Services.

1. Vuelva a la pestaña anterior. Seleccione **Actualizar** para mostrar el recurso de Cognitive Services y, luego, seleccione el recurso:

   ![Seleccione el recurso de Cognitive Services](./media/cognitive-search-attach-cognitive-services/attach2.png "Seleccione el recurso de Cognitive Services")

1. Expanda la sección **Agregar aptitudes cognitivas** para seleccionar las aptitudes cognitivas específicas que desea ejecutar en los datos. Realice los pasos restantes del asistente.

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>Asociación de un conjunto de aptitudes existentes con un recurso de Cognitive Services

Si ya tiene un conjunto de aptitudes, puede asociarlo a un recurso de Cognitive Services nuevo o distinto.

1. En la página de información general del servicio de búsqueda, seleccione **Conjunto de aptitudes**:

   ![Pestaña Conjuntos de aptitudes](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "Pestaña Conjuntos de aptitudes")

1. Seleccione el nombre del conjunto de aptitudes y un recurso existente o cree uno nuevo. Seleccione **Aceptar** para confirmar los cambios.

   ![Lista de recursos del conjunto de aptitudes](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "Lista de recursos del conjunto de aptitudes")

   Recuerde que **Gratis (enriquecimientos limitados)** se limita a 20 documentos diarios y que puede usar **Crear nuevo recurso de Cognitive Services** para aprovisionar un recurso facturable nuevo. Si crea un nuevo recurso, seleccione **Actualizar** para actualizar la lista de recursos de Cognitive Services y, a continuación, seleccione el recurso.

## <a name="attach-cognitive-services-programmatically"></a>Asociación de Cognitive Services mediante programación

Cuando defina un conjunto de aptitudes mediante programación, agregue una sección `cognitiveServices` al conjunto de aptitudes. En la sección, incluya la clave del recurso de Cognitive Services que desea asociar con el conjunto de aptitudes. Recuerde que el recurso debe estar en la misma región que su recurso de Azure Cognitive Search. Además incluya `@odata.type` y establézcalo en `#Microsoft.Azure.Search.CognitiveServicesByKey`.

En el siguiente ejemplo se muestra este patrón. Observe la sección `cognitiveServices` al final de la definición.

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

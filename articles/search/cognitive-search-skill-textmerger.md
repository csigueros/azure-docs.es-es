---
title: Aptitud cognitiva Combinación de texto
titleSuffix: Azure Cognitive Search
description: Combine el texto de una colección de campos en un solo campo consolidado. Use esta aptitud cognitiva en una canalización de enriquecimiento con inteligencia artificial de Búsqueda cognitiva de Azure.
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: 4ea7681a28cf8f17c53e42e9ad05ddf12b5d2f9b
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121860549"
---
#   <a name="text-merge-cognitive-skill"></a>Aptitud cognitiva Combinación de texto

La aptitud **Combinación de texto** consolida el texto de una colección de campos en un solo campo. 

> [!NOTE]
> Esta aptitud no está enlazada a Cognitive Services. No es facturable y no tiene ningún requisito de clave de Cognitive Services.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.MergeSkill

## <a name="skill-parameters"></a>Parámetros de la aptitud

Los parámetros distinguen mayúsculas de minúsculas.

| Nombre de parámetro     | Descripción |
|--------------------|-------------|
| `insertPreTag`    | Cadena que se incluirá antes de cada inserción. El valor predeterminado es `" "`. Para omitir el espacio, establezca el valor en `""`.  |
| `insertPostTag`   | Cadena que se incluirá después de cada inserción. El valor predeterminado es `" "`. Para omitir el espacio, establezca el valor en `""`.  |


##  <a name="sample-input"></a>Entrada de ejemplo
Un documento JSON con una entrada útil para esta aptitud podría ser:

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
      {
        "text": "The brown fox jumps over the dog",
        "itemsToInsert": ["quick", "lazy"],
        "offsets": [3, 28]
      }
    }
  ]
}
```

##  <a name="sample-output"></a>Salida de ejemplo
Este ejemplo muestra la salida de la entrada anterior, suponiendo que *insertPreTag* esté establecido en `" "` y *insertPostTag* esté establecido en `""`. 

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
      {
        "mergedText": "The quick brown fox jumps over the lazy dog"
      }
    }
  ]
}
```

## <a name="extended-sample-skillset-definition"></a>Definición del conjunto de aptitudes de ejemplo extendido

Un escenario común a la hora de utilizar Combinación de texto es combinar la representación textual de imágenes (el texto de una aptitud de OCR o la leyenda de una imagen) en el campo de contenido de un documento. 

El siguiente conjunto de aptitudes de ejemplo utiliza la aptitud OCR para extraer el texto de las imágenes insertadas en el documento. A continuación, crea un campo *merged_text* para que contenga el texto original y el texto de OCR de cada imagen. Puede aprender más sobre la habilidad de OCR [aquí](./cognitive-search-skill-ocr.md).

```json
{
  "description": "Extract text from images and merge with content text to produce merged_text",
  "skills":
  [
    {
      "description": "Extract text (plain and structured) from image.",
      "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
      "context": "/document/normalized_images/*",
      "defaultLanguageCode": "en",
      "detectOrientation": true,
      "inputs": [
        {
          "name": "image",
          "source": "/document/normalized_images/*"
        }
      ],
      "outputs": [
        {
          "name": "text"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
      "description": "Create merged_text, which includes all the textual representation of each image inserted at the right location in the content field.",
      "context": "/document",
      "insertPreTag": " ",
      "insertPostTag": " ",
      "inputs": [
        {
          "name":"text", 
          "source": "/document/content"
        },
        {
          "name": "itemsToInsert", 
          "source": "/document/normalized_images/*/text"
        },
        {
          "name":"offsets", 
          "source": "/document/normalized_images/*/contentOffset" 
        }
      ],
      "outputs": [
        {
          "name": "mergedText", 
          "targetName" : "merged_text"
        }
      ]
    }
  ]
}
```
En el ejemplo anterior se asume que existe un campo de imágenes normalizadas. Para obtener este campo de imágenes normalizadas, establezca la configuración *imageAction* en la definición del indexador en *generateNormalizedImages*, tal como se muestra a continuación:

```json
{
  //...rest of your indexer definition goes here ...
  "parameters":{
    "configuration":{
        "dataToExtract":"contentAndMetadata",
        "imageAction":"generateNormalizedImages"
    }
  }
}
```

## <a name="see-also"></a>Consulte también

+ [Aptitudes integradas](cognitive-search-predefined-skills.md)
+ [Definición de un conjunto de aptitudes](cognitive-search-defining-skillset.md)
+ [Create Indexer (REST)](/rest/api/searchservice/create-indexer)
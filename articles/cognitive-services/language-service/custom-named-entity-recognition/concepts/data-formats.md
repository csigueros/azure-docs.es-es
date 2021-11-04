---
title: Formatos de datos NER personalizados
titleSuffix: Azure Cognitive Services
description: Obtenga información sobre los formatos de datos aceptados por la característica Reconocimiento de entidades con nombre (NER) personalizado.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: de37751b3d814110b5d37f1b7c84d41278b2bea9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093653"
---
# <a name="data-formats-accepted-by-custom-ner"></a>Formatos de datos aceptados por NER personalizado

Cuando el modelo usa los datos para el aprendizaje, espera que los datos tengan un formato específico. Al etiquetar los datos en Language Studio, se convierten al formato JSON descrito en este artículo. También puede etiquetar manualmente los archivos.


## <a name="json-file-format"></a>Formato de archivo JSON

Al etiquetar entidades, las etiquetas se guardan como en el siguiente formato JSON. Si carga un archivo de etiquetas, este debe seguir el mismo formato.

```json
{
    //List of entity names. Their index within this array is used as an ID. 
    "entityNames": [
        "entity_name1",
        "entity_name2"
    ],
    "documents": "path_to_document", //Relative file path to get the text.
    "culture": "en-US", //Standard culture strings supported by CultureInfo.
    "entities": [
        {
            "regionStart": 0,
            "regionLength": 69,
            "labels": [
                {
                    "entity": 0, // Index of the entity in the "entityNames" array. Positions are relative to the original text (not bounding box)
                    "start": 4,
                    "length": 10
                },
                {
                    "entity": 1,
                    "start": 18,
                    "length": 11
                }
            ]
        }
    ]    
}
```

En la lista siguiente se describen las distintas propiedades JSON del ejemplo anterior.

* `entityNames`: matriz de nombres de entidad. El índice de la entidad en la matriz se usa como identificador.
* `documents`: matriz de documentos etiquetados.
  * `location`: ruta de acceso del documento relativa al archivo JSON. Por ejemplo, documentos en el mismo nivel que el archivo de etiquetas `file.txt`, para documentos en un nivel de directorio `dir1/file.txt`.
  * `culture`: referencia cultural o idioma del documento. <!-- See [language support](../language-support.md) for more information. -->
  * `entities`: especifica las etiquetas de reconocimiento de entidades.
    * `regionStart`: posición del carácter inclusivo del inicio del texto.
    * `regionLength`: longitud del rectángulo delimitador en términos de caracteres UTF16. El entrenamiento solo tiene en cuenta los datos de esta región, por lo que si se trata de un archivo etiquetado, establezca `regionStart` en 0 y `regionLength` en el último índice del último carácter del archivo. También puede establecer esta región si desea introducir un ejemplo negativo en el entrenamiento mediante la definición de la región como una parte del archivo sin etiquetas.

    * `labels`: todas las etiquetas se producen dentro del cuadro de límite.
      * `entity`: índice de la entidad de la matriz `entityNames`.
      * `start`: posición del carácter inclusivo del inicio de la etiqueta en el texto del documento. No es relativa al rectángulo delimitador.
      * `length`: longitud de la etiqueta en términos de caracteres UTF16.

## <a name="next-steps"></a>Pasos siguientes

Consulte el [artículo de cómo obtener](../how-to/tag-data.md) más información sobre el etiquetado de los datos. Cuando haya terminado de etiquetar los datos, puede [entrenar el modelo](../how-to/train-model.md).  

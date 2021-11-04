---
title: Formatos de datos de clasificación de texto personalizados
titleSuffix: Azure Cognitive Services
description: Obtenga información sobre los formatos de datos aceptados por la extracción de entidades personalizadas.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: f99dd2b0d540cad6fad605e264df4b6054240e35
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093372"
---
# <a name="accepted-data-formats"></a>Formatos de datos aceptados

Cuando el modelo usa los datos para el aprendizaje, espera que los datos tengan un formato específico. Al etiquetar los datos en Language Studio, se convierten al formato JSON descrito en este artículo. También puede etiquetar manualmente los archivos.


## <a name="json-file-format"></a>Formato de archivo JSON

El archivo de etiquetas debe tener el siguiente formato `json`.

```json
{
    "classifiers": [
        {
            "name": "Class1"
        },
        {
            "name": "Class2"
        }
    ],
    "documents": [
        {
            "location": "doc1.txt",
            "language": "en-us",
            "classifiers": [
                {
                    "classifierName": "Class2"
                },
                {
                    "classifierName": "Class1"
                }
            ]
        }
    ]
}
```

### <a name="data-description"></a>Descripción de los datos

* `classifiers`: matriz de clasificadores para los datos. Cada clasificador representa una de las clases con las que desea etiquetar los datos.
* `documents`: matriz de documentos etiquetados. Por ejemplo:
  * `location`: ruta de acceso del archivo JSON que contiene etiquetas. El archivo de etiquetas debe estar en la raíz del contenedor de almacenamiento.
  * `language`: idioma del documento. Use una de las [configuraciones regionales de referencia cultural admitidas](../language-support.md).
  * `classifiers`: matriz de objetos clasificadores asignados al documento. Si está trabajando en un único proyecto de clasificación, solo debe haber un clasificador.

## <a name="next-steps"></a>Pasos siguientes

Consulte el [artículo de cómo obtener](../how-to/tag-data.md) más información sobre el etiquetado de los datos. Cuando haya terminado de etiquetar los datos, puede [entrenar el modelo](../how-to/train-model.md).  

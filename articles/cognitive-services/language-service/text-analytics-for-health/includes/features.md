---
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: bf17375352b7c5ac4751ec14e75beb4f38b5472d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091108"
---
# <a name="named-entity-recognition"></a>[Reconocimiento de entidades con nombre](#tab/ner)

El reconocimiento de entidades con nombre detecta palabras y frases mencionadas en texto no estructurado que se pueden asociar a uno o varios tipos semánticos, como el diagnóstico, el nombre de los medicamentos, el síntoma, el signo o la edad.

> [!div class="mx-imgBorder"]
> ![Text Analytics para el mantenimiento de NER](../media/call-api/health-named-entity-recognition.png)

# <a name="relation-extraction"></a>[Extracción de relaciones](#tab/relation-extraction)

La extracción de relaciones identifica conexiones significativas entre los conceptos mencionados en el texto. Por ejemplo, una relación de "hora de condición" se encuentra al asociar un nombre de condición con una hora o entre una abreviatura y la descripción completa.  

> [!div class="mx-imgBorder"]
> ![Text Analytics para la extracción de relación de mantenimiento](../media/call-api/health-relation-extraction.png)


# <a name="entity-linking"></a>[Entity Linking](#tab/entity-linking)

La vinculación de entidad anula las distintas entidades al asociar las entidades con nombre mencionadas en el texto a los conceptos que se encuentran en una base de datos de conceptos predefinida, incluido el Sistema de Lenguaje Médico Unificado (UMLS). Los conceptos médicos también tienen asignada una denominación preferida, como forma adicional de normalización.

> [!div class="mx-imgBorder"]
> ![Text Analytics para la vinculación de entidades de mantenimiento](../media/call-api/health-entity-linking.png)

Text Analytics para el estado admite la vinculación a los vocabularios biomédicos y de salud que se encuentran en el origen de la información del metadiccionario de sinónimos del Sistema unificado de lenguaje médico ([UMLS](https://www.nlm.nih.gov/research/umls/sourcereleasedocs/index.html)).

# <a name="assertion-detection"></a>[Detección de aserciones](#tab/assertion-detection) 

El significado del contenido médico se ve muy afectado por los modificadores, como las aserciones negativas o condicionales, que pueden tener implicaciones críticas si se interpretan de manera errónea. Text Analytics for Health admite tres categorías de detección de aserciones para entidades del texto: 

* Certeza
* Condicional
* Asociación

> [!div class="mx-imgBorder"]
> ![Text Analytics para la negación de mantenimiento](../media/call-api/assertions.png)

---

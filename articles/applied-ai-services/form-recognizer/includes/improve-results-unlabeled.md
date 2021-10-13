---
author: laujan
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: lajanuar
ms.openlocfilehash: 27f002172646b7d50d505e3911df966aec133ba2
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/09/2021
ms.locfileid: "129714487"
---
Examine los valores de `"confidence"` de cada resultado de clave-valor en el nodo `"pageResults"`. También debe examinar las puntuaciones de confianza del nodo `"readResults"`, que se corresponden a la operación de lectura de texto. La confianza de los resultados de la lectura no afecta a la confianza de los resultados de la extracción de los pares clave-valor, por lo que debe comprobar ambos.
* Si las puntuaciones de confianza de la operación de lectura son bajas, intente mejorar la calidad de los documentos de entrada (consulte [Requisitos de entrada](../concept-model-overview.md#input-requirements)).
* Si las puntuaciones de confianza de la operación de extracción de pares clave-valor son bajas, asegúrese de que los documentos que se están analizando sean del mismo tipo que los documentos usados en el conjunto de entrenamiento. Si los documentos del conjunto de entrenamiento tienen variantes de apariencia, considere la posibilidad de dividirlos en carpetas diferentes y entrenar un modelo para cada variante.

El objetivo de puntuación de confianza dependerá de su caso de uso pero, por lo general, se recomienda establecer una puntuación del 80 % o superior. Para casos más confidenciales, como la lectura de registros médicos o instrucciones de facturación, se recomienda una puntuación del 100 %.
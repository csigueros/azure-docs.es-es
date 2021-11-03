---
title: Detección de aserciones en Text Analytics para el estado
titleSuffix: Azure Cognitive Services
description: Obtenga información sobre la detección de aserciones.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-health, ignite-fall-2021
ms.openlocfilehash: 0f9d24e43bd4525a703027cff6a5ee4bec7bd431
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131030533"
---
# <a name="assertion-detection"></a>Detección de aserciones

El significado del contenido médico se ve muy afectado por los modificadores, como las aserciones negativas o condicionales, que pueden tener implicaciones críticas si se interpretan de manera errónea. Text Analytics for Health admite tres categorías de detección de aserciones para entidades del texto: 

* Certeza
* Condicional
* Asociación

## <a name="assertion-output"></a>Salida de aserciones

Text Analytics for Health devuelve modificadores de aserciones, que son atributos informativos asignados a conceptos médicos que proporcionan una comprensión más profunda del contexto de los conceptos en el texto. Estos modificadores se dividen en tres categorías, cada uno de las cuales se centra en un aspecto diferente y contiene un conjunto de valores mutuamente excluyentes. Solo se asigna un valor por categoría a cada entidad. El valor más común de cada categoría es el valor predeterminado. La respuesta de salida del servicio solo contiene modificadores de aserciones que son diferentes del valor predeterminado.

**CERTAINTY**: proporciona información sobre la presencia (presencia frente a ausencia) del concepto y la certeza del texto en relación con su presencia (cierta frente a posible).
*   **Positive** [valor predeterminado]: el concepto existe o se ha producido.
* **Negative**: el concepto no existe o no se ha producido nunca.
* **Positive_Possible**: probablemente el concepto existe, pero existe alguna incertidumbre.
* **Negative_Possible**: la existencia del concepto es improbable pero existe alguna incertidumbre.
* **Neutral_Possible**: el concepto puede existir o no, sin tener a ninguna de estas opciones.

**CONDITIONALITY**: proporciona información sobre si la existencia de un concepto depende de determinadas condiciones. 
*   **None** [valor predeterminado]: el concepto es un hecho, no hipotético y no depende de determinadas condiciones.
*   **Hypothetical**: el concepto puede desarrollarse o producirse en el futuro.
*   **Conditional**: el concepto existe o solo se produce en determinadas condiciones.

**ASSOCIATION**: describe si el concepto está asociado al sujeto del texto o a otra persona.
*   **Subject** [valor predeterminado]: el concepto está asociado al sujeto del texto, normalmente el paciente.
*   **Someone_Else**: el concepto está asociado a alguien que no es el sujeto del texto.


La detección de aserciones representa las entidades negadas como un valor negativo para la categoría de certeza, por ejemplo:

```json
{
    "offset": 381,
    "length": 3,
    "text": "SOB",
    "category": "SymptomOrSign",
    "confidenceScore": 0.98,
    "assertion": {
        "certainty&quot;: &quot;negative"
    },
    "name": "Dyspnea",
    "links": [
        {
            "dataSource": "UMLS",
            "id&quot;: &quot;C0013404"
        },
        {
            "dataSource": "AOD",
            "id&quot;: &quot;0000005442"
        },
    ...
}
```

## <a name="next-steps"></a>Pasos siguientes

[Cómo llamar a Text Analytics para el estado](../how-to/call-api.md)

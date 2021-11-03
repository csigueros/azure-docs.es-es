---
title: Extracción de relaciones en Text Analytics for health
titleSuffix: Azure Cognitive Services
description: Aprenda sobre la extracción de relaciones.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-health, ignite-fall-2021
ms.openlocfilehash: 6fd838471387d7ef1b54beb9ead7b802f6e041e2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131030529"
---
# <a name="relation-extraction"></a>Extracción de relaciones

La extracción de relaciones identifica conexiones significativas entre los conceptos mencionados en el texto. Por ejemplo, una relación de "hora de condición" se encuentra al asociar un nombre de condición con una hora o entre una abreviatura y la descripción completa.  


## <a name="relation-extraction-output"></a>Salida de la extracción de relaciones

Text Analytics for health reconoce las relaciones entre distintos conceptos, incluidas las relaciones entre el atributo y la entidad (por ejemplo, la dirección de la estructura corporal, la dosis de medicación) y entre las entidades (por ejemplo, la detección de abreviaturas).

> [!NOTE]
> * Las relaciones que hacen referencia a CONDITION pueden referirse al tipo de entidad DIAGNOSIS o al tipo de entidad SYMPTOM_OR_SIGN.
> * Las relaciones que hacen referencia a MEDICATION pueden referirse al tipo de entidad MEDICATION_NAME o al tipo de entidad MEDICATION_CLASS.
> * Las relaciones que hacen referencia a TIME pueden referirse al tipo de entidad TIME o al tipo de entidad DATE.

La salida de la extracción de relaciones contiene referencias de URI y roles asignados de las entidades del tipo de relación. Por ejemplo, en el siguiente JSON:

```json
                "relations": [
                    {
                        "relationType": "DosageOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/0",
                                "role": "Dosage"
                            },
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            }
                        ]
                    },
                    {
                        "relationType": "RouteOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            },
                            {
                                "ref": "#/results/documents/0/entities/2",
                                "role": "Route"
                            }
                        ]
...
]
```

## <a name="recognized-relations"></a>Relaciones reconocidas

La API puede devolver las siguientes relaciones. 

**ABBREVIATION**

**BODY_SITE_OF_CONDITION**

**BODY_SITE_OF_TREATMENT**

**COURSE_OF_CONDITION**

**COURSE_OF_EXAMINATION**

**COURSE_OF_MEDICATION**

**COURSE_OF_TREATMENT**

**DIRECTION_OF_BODY_STRUCTURE**

**DIRECTION_OF_CONDITION**

**DIRECTION_OF_EXAMINATION**

**DIRECTION_OF_TREATMENT**

**DOSAGE_OF_MEDICATION**

**EXAMINATION_FINDS_CONDITION**

**EXPRESSION_OF_GENE**

**EXPRESSION_OF_VARIANT**

**FORM_OF_MEDICATION**

**FREQUENCY_OF_CONDITION**

**FREQUENCY_OF_MEDICATION**

**FREQUENCY_OF_TREATMENT**

**MUTATION_TYPE_OF_GENE**

**MUTATION_TYPE_OF_VARIANT**

**QUALIFIER_OF_CONDITION**

**RELATION_OF_EXAMINATION**

**ROUTE_OF_MEDICATION** 

**SCALE_OF_CONDITION**

**TIME_OF_CONDITION**

**TIME_OF_EVENT**

**TIME_OF_EXAMINATION**

**TIME_OF_MEDICATION**

**TIME_OF_TREATMENT**

**UNIT_OF_CONDITION**

**UNIT_OF_EXAMINATION**

**VALUE_OF_CONDITION**  

**VALUE_OF_EXAMINATION**

**VARIANT_OF_GENE**

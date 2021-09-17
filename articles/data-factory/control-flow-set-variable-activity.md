---
title: Establecimiento de la actividad de variable
titleSuffix: Azure Data Factory & Azure Synapse
description: Aprenda a usar la actividad Establecer variable para establecer el valor de una variable existente definida en una canalización de Azure Data Factory o Azure Synapse Analytics.
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
ms.date: 08/24/2021
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.openlocfilehash: c9c489ab15f7b6a44e4aadeef8cd98b0d935e1ba
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122821649"
---
# <a name="set-variable-activity-in-azure-data-factory-and-azure-synapse-analytics"></a>Actividad Establecer variable de Azure Data Factory y Azure Synapse Analytics
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Use la actividad Establecer variable para establecer el valor de una variable existente de tipo cadena, booleano o matriz definida en una canalización de Data Factory o Synapse.

## <a name="type-properties"></a>Propiedades de tipo

Propiedad | Descripción | Obligatorio
-------- | ----------- | --------
name | Nombre de la actividad en la canalización | sí
description | Texto que describe para qué se usa la actividad | no
type | Debe establecerse en **SetVariable** | sí
value | Literal de cadena o valor de objeto de expresión al que se asigna la variable | sí
variableName | Nombre de la variable que esta actividad establece | sí

## <a name="incrementing-a-variable"></a>Incremento de una variable

Un escenario común que incluye variables es el uso de una variable como iterador dentro de una actividad until o foreach. En una actividad de establecimiento de variables no se puede hacer referencia a la variable que se establece en el campo `value`. Para solucionar esta limitación, establezca una variable temporal y, a continuación, cree una segunda actividad de establecimiento de variables. La segunda actividad de establecimiento de variables establece el valor del iterador en la variable temporal. 

A continuación se muestra un ejemplo de este patrón:

![Incremento de variable](media/control-flow-set-variable-activity/increment-variable.png "Incrementar variable")

``` json
{
    "name": "pipeline3",
    "properties": {
        "activities": [
            {
                "name": "Set I",
                "type": "SetVariable",
                "dependsOn": [
                    {
                        "activity": "Increment J",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ],
                "userProperties": [],
                "typeProperties": {
                    "variableName": "i",
                    "value": {
                        "value": "@variables('j')",
                        "type": "Expression"
                    }
                }
            },
            {
                "name": "Increment J",
                "type": "SetVariable",
                "dependsOn": [],
                "userProperties": [],
                "typeProperties": {
                    "variableName": "j",
                    "value": {
                        "value": "@string(add(int(variables('i')), 1))",
                        "type": "Expression"
                    }
                }
            }
        ],
        "variables": {
            "i": {
                "type": "String",
                "defaultValue": "0"
            },
            "j": {
                "type": "String",
                "defaultValue": "0"
            }
        },
        "annotations": []
    }
}
```

Actualmente, el ámbito de las variables es el nivel de canalización. Esto significa que no son seguras para subprocesos y que pueden provocar un comportamiento inesperado y no deseado si se accede a ellas desde dentro de una actividad de iteración paralela, como un bucle foreach, especialmente si el valor también se modifica dentro de esa actividad foreach.

## <a name="next-steps"></a>Pasos siguientes
Obtenga información sobre otra actividad de flujo de control relacionada: 

- [Actividad de anexión de variables](control-flow-append-variable-activity.md)

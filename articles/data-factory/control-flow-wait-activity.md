---
title: Actividad de espera
titleSuffix: Azure Data Factory & Azure Synapse
description: La actividad Wait en Azure Data Factory y Synapse Analytics pausa la ejecución de una canalización durante un período especificado.
author: chez-charlie
ms.author: chez
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: dfc74e9ada7715f9c954123892a93611df7eca9d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124798928"
---
# <a name="execute-wait-activity-in-azure-data-factory-and-synapse-analytics"></a>Ejecución de la actividad de copia en Azure Data Factory y Azure Synapse Analytics
Cuando use una actividad Wait en una canalización, esta espera durante el período de tiempo especificado antes de continuar con la ejecución de actividades sucesivas. 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


## <a name="syntax"></a>Sintaxis

```json
{
    "name": "MyWaitActivity",
    "type": "Wait",
    "typeProperties": {
        "waitTimeInSeconds": 1
    }
}

```

## <a name="type-properties"></a>Propiedades de tipo

Propiedad | Descripción | Valores permitidos | Obligatorio
-------- | ----------- | -------------- | --------
name | Nombre de la actividad `Wait`. | String | Sí
type | Debe establecerse en **Wait**. | String | Sí
waitTimeInSeconds | El número de segundos que esperará la canalización antes de seguir con el procesamiento. | Entero | Sí

## <a name="example"></a>Ejemplo

> [!NOTE]
> En esta sección se proporcionan definiciones JSON y comandos de PowerShell de ejemplo para ejecutarlos en la canalización. Para ver una guía con instrucciones paso a paso para crear una canalización con definiciones de JSON y Azure PowerShell, consulte [Tutorial: Creación de una factoría de datos mediante Azure PowerShell](quickstart-create-data-factory-powershell.md).

### <a name="pipeline-with-wait-activity"></a>Canalización con actividad Wait
En este ejemplo, la canalización tiene dos actividades: **Until** y **Wait**. La actividad Wait está configurada para que espere durante un segundo. La canalización ejecuta la actividad Web en un bucle con un tiempo de espera de un segundo entre cada ejecución. 

```json
{
    "name": "DoUntilPipeline",
    "properties": {
        "activities": [
            {
                "type": "Until",
                "typeProperties": {
                    "expression": {
                        "value": "@equals('Failed', coalesce(body('MyUnauthenticatedActivity')?.status, actions('MyUnauthenticatedActivity')?.status, 'null'))",
                        "type": "Expression"
                    },
                    "timeout": "00:00:01",
                    "activities": [
                        {
                            "name": "MyUnauthenticatedActivity",
                            "type": "WebActivity",
                            "typeProperties": {
                                "method": "get",
                                "url": "https://www.fake.com/",
                                "headers": {
                                    "Content-Type": "application/json"
                                }
                            },
                            "dependsOn": [
                                {
                                    "activity": "MyWaitActivity",
                                    "dependencyConditions": [ "Succeeded" ]
                                }
                            ]
                        },
                        {
                            "type": "Wait",
                            "typeProperties": {
                                "waitTimeInSeconds": 1
                            },
                            "name": "MyWaitActivity"
                        }
                    ]
                },
                "name": "MyUntilActivity"
            }
        ]
    }
}

```

## <a name="next-steps"></a>Pasos siguientes
Vea otras actividades de flujo de control admitidas: 

- [Actividad If Condition](control-flow-if-condition-activity.md)
- [Actividad de ejecución de canalización](control-flow-execute-pipeline-activity.md)
- [Para cada actividad](control-flow-for-each-activity.md)
- [Actividad GetMetadata](control-flow-get-metadata-activity.md)
- [Actividad Lookup](control-flow-lookup-activity.md)
- [Actividad web](control-flow-web-activity.md)
- [Actividad Until](control-flow-until-activity.md)

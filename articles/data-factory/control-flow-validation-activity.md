---
title: Actividad de validación
titleSuffix: Azure Data Factory & Azure Synapse
description: La actividad de validación en Azure Data Factory y Synapse Analytics retrasa la ejecución de la canalización hasta que se valida un conjunto de datos con criterios definidos por el usuario.
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: fa98ef27b5dbcc7949f37bf548c414d015224e4c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124750612"
---
# <a name="validation-activity-in-azure-data-factory-and-synapse-analytics-pipelines"></a>Actividad de validación en canalizaciones de Azure Data Factory y Synapse Analytics
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Puede usar una validación en una canalización para asegurarse de que la canalización solo continúe la ejecución una vez que haya validado la existencia de la referencia del conjunto de datos adjunto, que cumple con los criterios especificados o que se haya alcanzado el tiempo de expiración.


## <a name="syntax"></a>Sintaxis

```json

{
    "name": "Validation_Activity",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_File",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "minimumSize": 20
    }
},
{
    "name": "Validation_Activity_Folder",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_Folder",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "childItems": true
    }
}

```


## <a name="type-properties"></a>Propiedades de tipo

Propiedad | Descripción | Valores permitidos | Obligatorio
-------- | ----------- | -------------- | --------
name | Nombre de la actividad de validación | String | Sí |
type | Debe establecerse en **Validación**. | String | Sí |
dataset | La actividad bloqueará la ejecución hasta que haya validado la existencia de esta referencia del conjunto de datos y que cumple con los criterios especificados, o se haya alcanzado el tiempo de expiración. El conjunto de datos proporcionado debe admitir la propiedad "MinimumSize" o "ChildItems". | Referencia del conjunto de datos | Sí |
timeout | Especifica el tiempo de espera para que se ejecute la actividad. Si no se especifica ningún valor, el valor predeterminado es 7 días ("7.00:00:00"). El formato es d.hh:mm:ss | String | No |
en reposo | Un retardo en segundos entre los intentos de validación. Si no se especifica ningún valor, el valor predeterminado es 10 segundos. | Entero | No |
childItems | Comprueba si la carpeta tiene elementos secundarios. Se puede establecer en-true: valide que la carpeta existe y que tiene elementos. Se bloquea hasta que haya al menos un elemento en la carpeta o se alcance el valor de tiempo de espera.-false: valide que la carpeta existe y que está vacía. Se bloquea hasta que la carpeta está vacía o hasta que se alcanza el valor del tiempo de expiración. Si no se especifica ningún valor, la actividad se bloqueará hasta que exista la carpeta o hasta que se alcance el tiempo de espera. | Boolean | No |
minimumSize | Tamaño mínimo de un archivo en bytes. Si no se especifica ningún valor, el valor predeterminado es 0 bytes. | Entero | No |


## <a name="next-steps"></a>Pasos siguientes
Vea otras actividades de flujo de control admitidas:

- [Actividad If Condition](control-flow-if-condition-activity.md)
- [Actividad de ejecución de canalización](control-flow-execute-pipeline-activity.md)
- [Para cada actividad](control-flow-for-each-activity.md)
- [Actividad GetMetadata](control-flow-get-metadata-activity.md)
- [Actividad Lookup](control-flow-lookup-activity.md)
- [Actividad web](control-flow-web-activity.md)
- [Actividad Until](control-flow-until-activity.md)

---
title: Ejecución de la actividad Fail en Azure Data Factory y Synapse Analytics (versión preliminar)
titleSuffix: Azure Data Factory & Azure Synapse
description: En este artículo se analiza la manera en que una actividad Fail en Azure Data Factory y Synapse Analytics devuelve intencionalmente un error en una canalización.
author: chez-charlie
ms.author: chez
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/22/2021
ms.openlocfilehash: 2b8b012617633a7569c951e842d35b05a3b2740e
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129535859"
---
# <a name="execute-a-fail-activity-in-azure-data-factory-and-synapse-analytics-preview"></a>Ejecución de la actividad Fail en Azure Data Factory y Synapse Analytics (versión preliminar)

En ocasiones, es posible que quiera producir un error en una canalización intencionadamente. Por ello, es posible que una [actividad de búsqueda](control-flow-lookup-activity.md) no devuelva datos coincidentes, o que una [actividad personalizada](transform-data-using-dotnet-custom-activity.md) termine con un error interno. Sea cual sea el motivo, ahora puede usar una actividad Fail en una canalización y personalizar el código y el mensaje de error.

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


## <a name="syntax"></a>Sintaxis

```json
{
    "name": "MyFailActivity",
    "type": "Fail",
    "typeProperties": {
        "message": "500",
        "errorCode": "My Custom Error Message"
    }
}

```

## <a name="type-properties"></a>Propiedades de tipo

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| --- | --- | --- | --- |
| name | Nombre de la actividad Fail. | String | Sí |
| type | Debe establecerse en **Fail**. | String | Sí |
| message | Mensaje de error que aparece en la actividad de error. Puede ser contenido dinámico evaluado en tiempo de ejecución. | String | Sí |
| errorCode | Código de error que clasifica el tipo de error de la actividad Fail. Puede ser contenido dinámico evaluado en tiempo de ejecución. | String | Sí |
| | |

## <a name="understand-the-fail-activity-error-code"></a>Descripción del código de error la actividad Fail

Normalmente, los usuarios establecen el mensaje y el código de error de una actividad Fail. Póngase en contacto con el desarrollador de las canalizaciones para comprender los significados específicos de los códigos de error. Sin embargo, en los siguientes casos perimetrales, es la instancia de Azure Data Factory la que establece el mensaje o el código de error.

| Descripción de la situación | Mensaje de error | Código de error |
| --- | --- | --- |
El contenido (dinámico) en `message` y `errorCode` se ha interpretado correctamente. | Mensaje de error establecido por el usuario | Código de error establecido por el usuario |
El contenido dinámico de `message` y `errorCode` no se puede interpretar. | "No se puede interpretar el código o el mensaje de error de _<activity_name>_ " | `ErrorCodeNotString` |
| El contenido dinámico de `message` no se puede interpretar como una cadena. | "El parámetro del mensaje de error de _<activity_name>_ no se puede interpretar como una cadena" | Código de error establecido por el usuario |
| El contenido dinámico de `message` se resuelve en NULL, una cadena vacía o espacios en blanco. | "No se puede interpretar el código o el mensaje de error de _<activity_name>_ " | Código de error establecido por el usuario |
| El contenido dinámico de `errorCode` no se puede interpretar como una cadena. | Mensaje de error establecido por el usuario | `ErrorCodeNotString` |
| El contenido dinámico de `errorCode` se resuelve en NULL, una cadena vacía o espacios en blanco. | Mensaje de error establecido por el usuario | `ErrorCodeNotString` |
| El valor de `message` o `errorCode` proporcionado por el usuario no se puede convertir en cadena.* | Se _produce un error_ en la canalización con: "Valor no válido para la propiedad <`errorCode`/`message`>" | |
| Falta el campo `message`.* | "No se proporcionó el mensaje de error" | Código de error establecido por el usuario |
| Falta el campo `errorCode`.* | Mensaje de error establecido por el usuario | `ErrorCodeNotString` |
| | |

\* Esta situación no debería producirse si la canalización se desarrolla con la interfaz de usuario web (UI) de Data Factory.

## <a name="next-steps"></a>Pasos siguientes

Consulte otras actividades de flujo de control admitidas, incluidas las siguientes:

- [Actividad If Condition](control-flow-if-condition-activity.md)
- [Actividad de ejecución de canalización](control-flow-execute-pipeline-activity.md)
- [Actividad ForEach](control-flow-for-each-activity.md)
- [Actividad de obtención de metadatos](control-flow-get-metadata-activity.md)
- [Actividad de búsqueda](control-flow-lookup-activity.md)
- [Actividad web](control-flow-web-activity.md)
- [Actividad Until](control-flow-until-activity.md)

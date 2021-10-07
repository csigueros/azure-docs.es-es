---
title: Actividad de error
titleSuffix: Azure Data Factory & Azure Synapse
description: La actividad de error en Azure Data Factory y Synapse Analytics produce intencionadamente un error en una canalización
author: chez-charlie
ms.author: chez
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/22/2021
ms.openlocfilehash: 95c4a7245a39ad717ad47ac9e8a7c72b2b5365fd
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/26/2021
ms.locfileid: "129061958"
---
# <a name="execute-fail-activity-in-azure-data-factory-and-synapse-analytics-preview"></a>Ejecución de la actividad de error en Azure Data Factory y Synapse Analytics (versión preliminar)

En ocasiones, es posible que desee producir un error intencionadamente en una canalización: quizás la [actividad de búsqueda](control-flow-lookup-activity.md) no devuelve ningún dato coincidente o la [actividad personalizada](transform-data-using-dotnet-custom-activity.md), a pesar de devolver 200, produce un error interno. Sea cual sea el motivo, ahora puede usar una actividad de error en una canalización y personalizar el código y el mensaje de error.

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

Propiedad | Descripción | Valores permitidos | Obligatorio
-------- | ----------- | -------------- | --------
name | Nombre de la actividad `Fail`. | String | Sí
type | Debe establecerse en **Fail**. | String | Sí
message | Mensaje de error que aparece en la actividad. Puede ser contenido dinámico evaluado en tiempo de ejecución. | String | Sí
errorCode | Código que categoriza el tipo de error de la actividad. Puede ser contenido dinámico evaluado en tiempo de ejecución. | String | sí

## <a name="understand-fail-activity-error-code"></a>Descripción del código de la actividad de error

Normalmente los clientes establecen el código y el mensaje de una actividad de error. Póngase en contacto con el desarrollador de las canalizaciones para comprender los significados específicos de los códigos de error. Sin embargo, en los siguientes casos perimetrales, ADF establecerá el código o los mensajes de error.

Descripción de la situación | Mensaje de error | Código de error
-------- | ----------- | --------------
Contenido (dinámico) en `message` y `errorCode` interpretado correctamente | Mensaje de error establecido por el usuario | Código de error establecido por el usuario
El contenido dinámico de `message` y `errorCode` no se puede interpretar | "Non se puede interpretar el código o el mensaje de error de _<activity_name>_ " | `ErrorCodeNotString`
El contenido dinámico de `message` no se puede interpretar como una cadena | "El parámetro del mensaje de error de _<activity_name>_ no se puede interpretar como una cadena" | Código de error establecido por el usuario
El contenido dinámico de `message` se resuelve en NULL, una cadena vacía o espacios en blanco | "Non se puede interpretar el código o el mensaje de error de _<activity_name>_ " | Código de error establecido por el usuario
El contenido dinámico de `errorCode` no se puede interpretar como una cadena | Mensaje de error establecido por el usuario | `ErrorCodeNotString`
El contenido dinámico de `errorCode` se resuelve en NULL, una cadena vacía o espacios en blanco | Mensaje de error establecido por el usuario | `ErrorCodeNotString`
El valor de `message` y `errorCode` proporcionado por el usuario no se puede convertir en cadena. * | Se __produce un error__ en la canalización con: "Valor no válido para la propiedad <`errorCode`/`message`>"
Falta el campo `message` * | "No se proporcionó el mensaje de error" | Código de error establecido por el usuario
Falta el campo `errorCode` * | Mensaje de error establecido por el usuario | `ErrorCodeNotString`

Las situaciones marcadas con * no deberían producirse si la canalización se desarrolla con la interfaz de usuario web (UI) de Data Factory.

## <a name="next-steps"></a>Pasos siguientes

Consulte otras actividades de flujo de control admitidas, incluidas las siguientes:

- [Actividad If Condition](control-flow-if-condition-activity.md)
- [Actividad de ejecución de canalización](control-flow-execute-pipeline-activity.md)
- [Para cada actividad](control-flow-for-each-activity.md)
- [Actividad GetMetadata](control-flow-get-metadata-activity.md)
- [Actividad Lookup](control-flow-lookup-activity.md)
- [Actividad web](control-flow-web-activity.md)
- [Actividad Until](control-flow-until-activity.md)

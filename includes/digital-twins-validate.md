---
author: baanders
description: Archivo de inclusión que describe cómo validar los modelos de Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: 064f8e322d75e8c92f8ae9d0bd976ebbfb1bdb49
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114438587"
---
> [!TIP]
> Después de crear un modelo, se recomienda validar los modelos sin conexión antes de cargarlos en la instancia de Azure Digital Twins.

Hay un [ejemplo de validador de DTDL](/samples/azure-samples/dtdl-validator/dtdl-validator) disponible para todos los lenguajes que sirve para validar los documentos del modelo para garantizar que DTDL sea correcto antes de cargarlo en la instancia.

El ejemplo de validador de DTDL se basa en una biblioteca de analizador de DTDL de .NET, que está disponible en NuGet como una biblioteca de cliente: [Microsoft.Azure.DigitalTwins.Parser](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/). También puede usar la biblioteca directamente para diseñar su propia solución de validación. Al usar la biblioteca del analizador, asegúrese de usar una versión que sea compatible con la versión de Azure Digital Twins que se está ejecutando. Actualmente, se trata de la versión *3.12.4*.

Puede obtener más información sobre el ejemplo del validador y la biblioteca del analizador, incluidos ejemplos de uso, en [Análisis y validación de modelos](../articles/digital-twins/how-to-parse-models.md).
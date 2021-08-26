---
title: Transformación Unión en el flujo de datos de asignación
titleSuffix: Azure Data Factory & Azure Synapse
description: Transformación Nueva rama de flujo de datos de asignación de Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 04/27/2020
ms.openlocfilehash: 2a81ffa5d73509a5bb47cda8bc2933894b3139f9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "122638074"
---
# <a name="union-transformation-in-mapping-data-flow"></a>Transformación Unión en el flujo de datos de asignación

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Unión combina varias secuencias de datos en una, y la unión SQL de esas secuencias será la salida de la transformación Unión. Todo el esquema de cada secuencia de entrada se combinará en su flujo de datos, sin necesidad de tener una clave de combinación.

Puede combinar un número n de secuencias en la tabla de configuración. Para ello, seleccione el icono "+" junto a cada fila configurada, incluidos los datos de origen y las secuencias de transformaciones existentes en el flujo de datos.

A continuación se muestra un breve vídeo paso a paso sobre la transformación Unión en el flujo de datos de asignación de ADF:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4vngz]

![Transformación Unión](media/data-flow/union.png "Union")

En este caso, puede combinar los metadatos dispares de varios orígenes (en este ejemplo, tres archivos de origen diferentes) y combinarlos en una única secuencia:

![Información general de la transformación Unión](media/data-flow/union111.png "Unión 1")

Para lograrlo, agregue filas adicionales en la configuración de Unión mediante la inclusión de todos los orígenes que quiere agregar. No se quiere ninguna clave común de búsqueda o unión:

![Configuración de la transformación Unión](media/data-flow/unionsettings.png "Configuración de unión")

Si establece una transformación Selección después de Unión, podrá cambiar el nombre de los campos superpuestos o los campos cuyos nombres no se han asignado desde orígenes sin encabezado. Haga clic en "Inspeccionar" para ver los metadatos combinados con un total de 132 columnas en este ejemplo de tres orígenes diferentes:

![Transformación Unión final](media/data-flow/union333.png "Unión 3")

## <a name="name-and-position"></a>Nombre y posición

Al elegir la "unión por nombre", cada valor de columna se colocará en la columna correspondiente de cada origen, con un nuevo esquema de metadatos concatenados.

Si elige la "unión por posición", cada valor de columna se colocará en la posición original de cada origen correspondiente, lo que dará lugar a un nuevo flujo de datos combinado, donde los datos de cada origen se agregan a la misma secuencia:

![Salida de unión](media/data-flow/unionoutput.png "Salida de unión")

## <a name="next-steps"></a>Pasos siguientes

Explorar transformaciones similares, como [Combinación](data-flow-join.md) y [Existe](data-flow-exists.md).
